---
title: 캐싱 작동 방식 | Microsoft Docs
description: 캐싱은 데이터에 대한 향후 요청에 신속하게 액세스할 수 있도록 데이터를 로컬에 저장하는 프로세스입니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: 563c073e781e2a2bee88b4ecdcdc82541c21ec4f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092394"
---
# <a name="how-caching-works"></a>캐싱 작동 방식

이 문서는 일반적인 캐싱 개념과 [Azure CDN(Content Delivery Network)](cdn-overview.md)이 캐싱을 사용하여 성능을 향상시키는 방법에 대한 개요를 제공합니다. CDN 엔드포인트에서 캐싱 동작을 사용자 지정하는 방법에 대해 알아보려면 [캐싱 규칙을 사용하여 Azure CDN 캐싱 동작 제어](cdn-caching-rules.md)와 [쿼리 문자열로 Azure CDN 캐싱 동작 제어](cdn-query-string.md)를 참조하세요.

## <a name="introduction-to-caching"></a>캐싱에 대한 소개

캐싱은 데이터에 대한 향후 요청에 신속하게 액세스할 수 있도록 데이터를 로컬에 저장하는 프로세스입니다. 가장 일반적인 캐싱 유형인 웹 브라우저 캐싱의 경우 웹 브라우저는 로컬 하드 드라이브에 정적 데이터의 복사본을 로컬로 저장합니다. 캐싱을 사용함으로써, 웹 브라우저는 서버에 여러 번 왕복하는 대신 로컬에서 동일한 데이터를 액세스할 수 있기 때문에 시간과 리소스를 절약할 수 있습니다. 캐싱은 정적 이미지, CSS 파일 및 JavaScript 파일과 같은 작고 정적인 데이터를 로컬에서 관리하는 데 적합합니다.

마찬가지로, 캐싱은 원본 서버로 다시 돌아가는 요청을 피하여 최종 사용자 대기 시간을 줄이기 위해서 사용자와 가까운 에지 서버의 콘텐츠 배달 네트워크에서 사용됩니다. 단일 사용자에 대해서만 사용되는 웹 브라우저 캐시와 달리 CDN에는 공유 캐시가 있습니다. CDN 공유 캐시에서는 한 사용자가 요청한 파일을 나중에 다른 사용자가 액세스할 수 있기 때문에 원본 서버에 대한 요청 수가 크게 줄어듭니다.

자주 변경되거나 개별 사용자의 고유한 동적 리소스는 캐시할 수 없습니다. 단, 이러한 유형의 리소스는 성능 향상을 위해 Azure Content Delivery Network에서 DSA(동적 사이트 가속) 최적화를 이용할 수 있습니다.

캐싱은 원본 서버와 최종 사용자 사이에 여러 수준에서 발생할 수 있습니다.

- 웹 서버: 공유 캐시(여러 사용자용)를 사용합니다.
- 콘텐츠 배달 네트워크: 공유 캐시(여러 사용자용)를 사용합니다.
- ISP(인터넷 서비스 공급자): 공유 캐시(여러 사용자용)를 사용합니다.
- 웹 브라우저: 개인 캐시(단일 사용자용)를 사용합니다.

각 캐시는 일반적으로 자체 리소스의 최신 여부를 관리하고 파일이 오래되면 유효성 검사를 수행합니다. 이러한 동작은 [RFC 7234](https://tools.ietf.org/html/rfc7234) HTTP 캐싱 사양에 정의되어 있습니다.

### <a name="resource-freshness"></a>리소스 최신 여부

캐시된 리소스는 원본 서버의 해당 리소스에 비해 오래되거나 유효하지 않을 가능성이 있기 때문에 새로 고칠 시기를 제어하는 것은 어느 캐싱 메커니즘에나 중요합니다. 시간과 대역폭을 절약하기 위하여, 캐시된 리소스를 액세스할 때마다 원본 서버의 버전과 비교하지는 않습니다. 대신 캐시된 리소스가 최신으로 간주되기만 하면 가장 최신 버전이라고 가정하고 클라이언트에 바로 전송됩니다. 캐시된 리소스는 해당 기간이 캐시 설정에 정의된 기간보다 짧으면 최신으로 간주됩니다. 예를 들어 브라우저가 웹 페이지를 다시 로드할 때는 하드 드라이브에 있는 각각의 캐시된 리소스가 최신 상태인지 확인하고 로드합니다. 리소스가 최신이 아니면(유효하지 않으면) 최신 복사본이 서버에서 로드됩니다.

### <a name="validation"></a>유효성 검사

리소스가 유효하지 않은 것으로 간주되면 유효성을 검사하도록 즉, 캐시의 데이터가 원본 서버에 있는 데이터와 여전히 일치하는지 확인하도록 원본 서버에 요청합니다. 파일이 원본 서버에서 수정되었으면 캐시는 리소스의 버전을 업데이트합니다. 그렇지 않고 리소스가 최신이면 캐시의 데이터가 유효성 검사를 거치지 않고 바로 전송됩니다.

### <a name="cdn-caching"></a>CDN 캐싱

캐싱은 이미지, 글꼴 및 비디오와 같은 정적 자산에 대한 원본 로드를 줄이고 배달 속도를 높이기 위해 CDN이 작동하는 방식에 필수적입니다. CDN 캐싱에서 정적 리소스는 사용자의 로컬에 가깝게 전략적으로 배치된 서버에 선별적으로 저장되며 다음과 같은 이점을 제공합니다.

- 대부분의 웹 트래픽은 정적이기 때문에(예: 이미지, 글꼴 및 비디오) CDN 캐싱은 콘텐츠를 사용자 가까이로 이동하여 데이터가 이동하는 거리를 줄여서 네트워크 대기 시간을 감소시킵니다.

- 캐싱은 작업을 CDN으로 오프로드하여 네트워크 트래픽과 원본 서버의 로드를 줄일 수 있습니다. 이렇게 하면 사용자 수가 많은 경우에도 응용 프로그램의 리소스 요구 사항과 비용을 줄일 수 있습니다.

웹 브라우저에서 캐싱이 구현되는 방식과 유사하게, 캐시 지시문 헤더를 보내서 CDN에서 캐시가 구현되는 방식을 제어할 수 있습니다. 캐시 지시문 헤더는 HTTP 헤더이며 일반적으로 원본 서버에서 추가됩니다. 이러한 헤더의 대부분은 원래 클라이언트 브라우저의 캐싱을 처리하도록 설계되었지만 현재는 CDN과 같은 모든 중간 캐시에서도 사용됩니다. 

`Cache-Control` 및 `Expires`라는 두 가지 헤더를 사용하여 캐시의 최신 여부를 정의할 수 있습니다. `Cache-Control`이 더 최신이고, 둘 다 있는 경우 `Expires`보다 우선합니다. 유효성 검사에 사용되는 두 가지 유형의 헤더(유효성 검사기라고 함)는 `ETag` 및 `Last-Modified`입니다. `ETag`가 더 최신이고, 둘 다 정의된 경우 `Last-Modified`보다 우선합니다.  

## <a name="cache-directive-headers"></a>캐시 지시문 헤더

> [!IMPORTANT]
> 기본적으로 DSA에 최적화된 Azure CDN 엔드포인트는 캐시 지시문 헤더를 무시하고 캐싱을 바이패스합니다. **Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필의 경우 캐싱을 사용하도록 설정하는 [CDN 캐싱 규칙](cdn-caching-rules.md)을 사용하여 Azure CDN 엔드포인트가 이러한 헤더를 처리하는 방식을 조정할 수 있습니다. **Verizon의 Azure CDN 프리미엄** 프로필의 경우 [규칙 엔진](cdn-rules-engine.md)을 사용하여 캐싱을 사용하도록 설정합니다.

Azure CDN은 캐시 기간과 캐시 공유를 정의하는 다음과 같은 HTTP 캐시 지시문 헤더를 지원합니다.

**Cache-Control:**
- 웹 게시자에게 콘텐츠에 대한 제어 권한을 더 많이 부여하고 `Expires` 헤더의 제한 사항을 해결하기 위해 HTTP 1.1에 도입되었습니다.
- `Expires` 헤더와 `Cache-Control`이 모두 정의 된 경우 전자의 헤더를 대체합니다.
- 클라이언트에서 CDN POP으로의 HTTP 요청에 사용되는 경우 `Cache-Control`은 기본적으로 모든 Azure CDN 프로필에서 무시됩니다.
- 클라이언트에서 CDN POP으로의 HTTP 응답에 사용되는 경우:
     - **Verizon의 Azure CDN 표준/프리미엄** 및 **Microsoft의 Azure CDN 표준**은 둘 다 `Cache-Control` 지시문을 지원합니다.
     - **Akamai의 Azure CDN 표준**은 다음과 같은 `Cache-Control` 지시문만 지원하고 나머지는 무시됩니다.
         - `max-age`: 캐시는 지정된 시간(초)동안 콘텐츠를 저장할 수 있습니다. 예: `Cache-Control: max-age=5`. 이 지시문은 콘텐츠가 최신으로 간주되는 최대 시간을 지정합니다.
         - `no-cache`: 콘텐츠를 캐시하지만 캐시에서 전송할 때마다 그 전에 콘텐츠의 유효성을 검사합니다. `Cache-Control: max-age=0`과 동일합니다.
         - `no-store`: 콘텐츠를 캐시하지 않습니다. 이전에 저장된 콘텐츠가 있으면 제거합니다.

**만료:**
- 이전 버전과의 호환성을 위해 지원되는 HTTP 1.0에서 도입된 레거시 헤더입니다.
- 초 단위 정밀도로 날짜 기반 만료 시간을 사용합니다. 
- `Cache-Control: max-age`와 유사합니다.
- `Cache-Control`이 없을 때 사용됩니다.

**Pragma:**
   - 기본적으로 Azure CDN에서는 적용되지 않습니다.
   - 이전 버전과의 호환성을 위해 지원되는 HTTP 1.0에서 도입된 레거시 헤더입니다.
   - `no-cache` 지시문과 함께 클라이언트 요청 헤더로 사용됩니다. 이 지시문은 새로운 버전의 리소스를 제공하도록 서버에 지시합니다.
   - `Pragma: no-cache`은 `Cache-Control: no-cache`와 동등합니다.

## <a name="validators"></a>유효성 검사기

캐시가 유효하지 않으면 HTTP 캐시 유효성 검사기를 사용하여 캐시된 버전의 파일을 원본 서버의 버전과 비교합니다. **Verizon의 Azure CDN 표준/프리미엄**은 기본적으로 `ETag` 및 `Last-Modified` 유효성 검사기를 지원하는 반면, **Microsoft의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준**은 기본적으로 `Last-Modified`만 지원합니다.

**ETag:**
- **Verizon의 Azure CDN 표준/프리미엄**은 기본적으로 `ETag`를 지원하는 반면, **Microsoft의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준**은 지원하지 않습니다.
- `ETag` 모든 파일 및 파일 버전에 대해 고유한 문자열을 정의합니다. 예: `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- HTTP 1.1에서 도입되었으며 `Last-Modified`보다 최신입니다. 마지막 수정 날짜를 확인하기 어려운 경우에 유용합니다.
- 강력한 유효성 검사와 약한 유효성 검사 모두를 지원하지만 Azure CDN은 강력한 유효성 검사만 지원합니다. 강력한 유효성 검사의 경우 두 리소스 표현이 바이트 단위로 동일해야 합니다. 
- 캐시는 요청에 하나 이상의 `ETag` 유효성 검사기가 있는 `If-None-Match` 헤더를 전송하여 `ETag`를 사용하는 파일의 유효성을 검사합니다. 예: `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. 서버의 버전이 목록의 `ETag` 유효성 검사기와 일치하면 응답에 상태 코드 304(수정되지 않음)가 전송됩니다. 버전이 다른 경우 서버는 상태 코드 200(정상)과 업데이트된 리소스로 응답합니다.

**마지막 수정:**
- **Verizon의 Azure CDN 표준/프리미엄**의 경우에만 `ETag`가 HTTP 응답에 속하지 않으면 `Last-Modified`가 사용됩니다. 
- 리소스가 마지막으로 수정된 것으로 원본 서버에서 확인된 날짜와 시간을 지정합니다. 예: `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- 캐시는 요청에 날짜와 시간이 있는 `If-Modified-Since` 헤더를 보내어 `Last-Modified`를 사용하여 파일의 유효성을 검사합니다. 원본 서버는 해당 날짜를 최신 리소스의 `Last-Modified` 헤더와 비교합니다. 리소스가 지정된 시간 이후로 수정되지 않은 경우 서버는 응답에 상태 코드 304(수정되지 않음)를 반환합니다. 리소스가 수정된 경우에는 서버가 상태 코드 200(정상)과 업데이트된 리소스를 반환합니다.

## <a name="determining-which-files-can-be-cached"></a>캐시할 수 있는 파일 결정

모든 리소스를 캐시할 수 있는 것은 아닙니다. 다음 테이블은 HTTP 응답 유형을 기반으로 어떤 리소스를 캐시할 수 있는지 보여줍니다. 이러한 조건을 모두 충족하지 않는 HTTP 응답과 함께 전송되는 리소스는 캐시할 수 없습니다. **Verizon의 Azure CDN 프리미엄**의 경우에만 규칙 엔진을 사용하여 이러한 조건 중 일부를 사용자 지정할 수 있습니다.

|                   | Microsoft의 Azure CDN          | Verizon의 Azure CDN | Akamai의 Azure CDN        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP 상태 코드 | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| HTTP 메서드      | GET, HEAD                         | GET                    | GET                          |
| 파일 크기 제한  | 300GB                            | 300GB                 | - 일반 웹 배달 최적화: 1.8GB<br />- 미디어 스트리밍 최적화: 1.8GB<br />- 대용량 파일 최적화: 150GB |

**Microsoft의 Azure CDN 표준** 캐싱이 리소스에 작동하려면 원본 서버가 모든 HEAD 및 GET HTTP 요청을 지원해야 하고, content-length 값이 자산에 대한 모든 HEAD 및 GET HTTP 응답에 대해 동일해야 합니다. HEAD 요청의 경우, 원본 서버는 HEAD 요청에서 지원해야 하고, GET 요청을 수신한 경우처럼 동일한 헤더로 응답해야 합니다.

## <a name="default-caching-behavior"></a>기본 캐싱 동작

다음 테이블은 Azure CDN 제품의 기본 캐싱 동작과 최적화에 대해 설명합니다.

|    | Microsoft: 일반 웹 배달 | Verizon: 일반 웹 배달 | Verizon: DSA | Akamai: 일반 웹 배달 | Akamai: DSA | Akamai: 대용량 파일 다운로드 | Akamai: 일반 또는 VOD 미디어 스트리밍 |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **원본 사용**       | yes    | yes   | 아니요   | yes    | 아니요   | 예   | yes    |
| **CDN 캐시 기간** | 2일 |7 일 | 없음 | 7 일 | 없음 | 1일 | 1년 |

**원본 사용**: [지원되는 캐시 지시문 헤더](#http-cache-directive-headers)가 원본 서버의 HTTP 응답에 존재하는 경우 사용할지 여부를 지정합니다.

**CDN 캐시 기간**: Azure CDN에 리소스가 캐시되는 시간의 길이를 지정합니다. 하지만 **원본 사용**이 예이고 원본 서버의 HTTP 응답에 캐시 지시문 헤더 `Expires` 또는 `Cache-Control: max-age`가 포함되어 있으면 Azure CDN은 헤더에 지정된 기간 값을 대신 사용합니다. 

## <a name="next-steps"></a>다음 단계

- 캐싱 규칙을 통해 CDN의 기본 캐싱 동작을 사용자 지정하고 재정의하는 방법을 알아보려면 [캐싱 규칙을 사용하여 Azure CDN 캐싱 동작 제어](cdn-caching-rules.md)를 참조하세요. 
- 쿼리 문자열을 사용하여 캐싱 동작을 제어하는 방법을 알아보려면 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](cdn-query-string.md)를 참조하세요.



