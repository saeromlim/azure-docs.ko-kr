---
title: 사용자 지정 개발 응용 프로그램에 대한 토큰 수명 기본값을 변경하는 방법 | Microsoft Docs
description: Azure AD에서 개발 중인 응용 프로그램에 대한 토큰 수명 정책을 업데이트하는 방법
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 059920c710b202e22a22f8431c536c5dfa19f2b9
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723942"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>사용자 지정 개발 응용 프로그램에 대한 토큰 수명 기본값을 변경하는 방법

Azure AD Premium을 사용하면 앱 개발자 및 테넌트 관리자가 기밀이 아닌 클라이언트에 대해 발급된 토큰의 수명을 구성할 수 있습니다. 토큰 수명 정책은 테넌트 전체 또는 액세스 중인 리소스에 설정됩니다.

 * 토큰 수명 정책을 설정하려면 [Azure AD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureADPreview)을 다운로드해야 합니다.

 * **Connect-AzureAD -Confirm** 명령을 실행합니다.

 * 다음은 최대 사용 기간 단일 요소 새로 고침 토큰을 설정하는 예제 정책입니다. 다음 정책을 만듭니다. ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * [토큰 수명 구성](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) 문서를 확인하여 기타 사용자 지정을 만드는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계
[토큰 수명 구성](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Azure AD 토큰 참조](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

