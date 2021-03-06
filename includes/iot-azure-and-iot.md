---
title: 포함 파일
description: 포함 파일
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/25/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f27dbf20eab5866ac3061d888c17e970a1d197f7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666758"
---
# <a name="azure-and-the-internet-of-things"></a>Azure 및 사물 인터넷

Microsoft Azure 및 IoT(사물 인터넷)를 시작합니다. 이 문서에서는 클라우드에 있는 IoT 솔루션의 일반적인 특징에 대해 설명합니다. IoT 솔루션은 수백만 대에 이르는 장치 및 솔루션 백 엔드 사이의 안전한, 양방향 통신을 필요로 합니다. 예를 들어 장치-클라우드 이벤트 스트림에서 새 정보를 발견하기 위하여 솔루션에서 자동화된 예측 분석을 사용할 수 있습니다.

## <a name="iot-solution-architecture"></a>IoT 솔루션 아키텍처

다음 다이어그램에서는 일반적인 IoT 솔루션 아키텍처의 주요 요소를 보여 줍니다. 이 다이어그램은 특정 구현 세부 정보(예: 사용되는 Azure 서비스 및 장치 운영 체제)와 관계가 없습니다. 이 아키텍처에서 IoT 장치는 클라우드 게이트웨이로 보내는 데이터를 수집합니다. 클라우드 게이트웨이를 통해 다른 백 엔드 서비스에 의한 처리에 데이터를 사용할 수 있습니다. 이러한 백 엔드 서비스에서 데이터를 제공할 수 있는 대상은 다음과 같습니다.

* 다른 기간 업무 응용 프로그램
* 대시보드 또는 다른 프레젠테이션 장치를 통한 인간 조작자

![IoT 솔루션 아키텍처][img-solution-architecture]

> [!NOTE]
> IoT 아키텍처에 대한 자세한 설명은 [Microsoft Azure IoT 참조 아키텍처][lnk-refarch]를 참조하세요.

### <a name="device-connectivity"></a>장치 연결

IoT 솔루션 아키텍처에서 장치는 일반적으로 저장 및 처리를 위해 클라우드에 원격 분석을 보냅니다. 예를 들어 예측 유지 관리 시나리오에서 솔루션 백 엔드는 센서 데이터 스트림을 사용하여 특정 펌프에 유지 관리가 필요한 시기를 결정할 수 있습니다. 또한 장치는 클라우드 끝점에서 메시지를 읽어 수신하고 클라우드-장치 메시지에 응답할 수 있습니다. 동일한 예에서 솔루션 백 엔드는 메시지를 펌핑 스테이션의 다른 펌프로 보내서 유지 관리가 시작되기 직전에 라우팅 흐름을 다시 시작할 수 있습니다. 이 절차를 통해 유지 관리 엔지니어가 도착하는 즉시 작업을 시작할 수 있는지 확인합니다.

장치를 안전하고 안정적으로 연결하는 것은 종종 IoT 솔루션의 가장 중요한 과제입니다. 이는 브라우저 및 모바일 앱과 같은 다른 클라이언트에 비해 다른 특징이 IoT 장치에 있기 때문입니다. IoT 장치의 이러한 특징은 구체적으로 다음과 같습니다.

* 인간 조작자가 없는 내장 시스템인 경우가 많습니다(전화기와 다름).
* 물리적 액세스에 많은 비용이 드는 원격 위치에 배포될 수 있습니다.
* 솔루션 백 엔드를 통해서만 연결 가능합니다. 장치와 상호 작용하는 다른 방법이 없습니다.
* 전원 및 리소스 처리 제한이 있을 수 있습니다.
* 일시적이거나 느리거나 비용이 많이 드는 네트워크 연결이 있을 수 있습니다.
* 독점, 사용자 지정 또는 업계 특정 응용 프로그램 프로토콜을 사용해야 할 수 있습니다.
* 널리 사용되는 다양한 하드웨어 및 소프트웨어 플랫폼을 사용하여 만들 수 있습니다.

이전의 제약 조건 외에도 모든 IoT 솔루션이 확장 가능하고, 안전하며, 신뢰할 수 있어야 합니다.

통신 프로토콜 및 네트워크 가용성에 따라 장치는 클라우드와 직접 통신하거나 중간 게이트웨이를 통해 통신할 수 있습니다. IoT 아키텍처에는 이러한 두 가지 통신 패턴이 혼합되어 있는 경우가 많습니다.

### <a name="data-processing-and-analytics"></a>데이터 처리 및 분석

최신 IoT 솔루션에서 데이터 처리는 클라우드 또는 장치 쪽에서 발생할 수 있습니다. 장치 쪽 처리를 *에지 컴퓨팅*이라고 합니다. 데이터를 처리할 위치는 다음과 같은 요인에 따라 다릅니다.

* 네트워크 제약 조건 - 장치와 클라우드 간의 대역폭이 제한된 경우에는 더 많은 에지 처리를 수행할 동기가 있습니다.
* 응답 시간 - 거의 실시간으로 장치를 작동해야 한다는 요구 사항이 있는 경우 장치 자체에서 응답을 처리하는 것이 더 나을 수 있습니다. 예를 들어 비상시에 중지해야 하는 로봇 팔이 있습니다.
* 규정 환경 - 일부 데이터는 클라우드로 보낼 수 없습니다.

일반적으로 에지 및 클라우드 모두의 데이터 처리는 다음 기능을 결합한 것입니다.

* 장치로부터 원격 분석을 대규모로 받고 해당 데이터를 처리하고 저장하는 방법을 결정합니다.
* 실시간이든 사후이든 상관없이 원격 분석을 분석하여 통찰력을 제공합니다.
* 클라우드 또는 게이트웨이 장치에서 특정 장치로 명령을 보냅니다.

또한 IoT 클라우드 백 엔드는 다음을 제공해야 합니다.

* 다음을 수행할 수 있는 장치 등록 기능
    * 장치를 프로비전합니다.
    * 인프라에 연결할 수 있는 장치를 제어합니다.
* 장치 상태를 제어하고 해당 작업을 모니터링하는 장치 관리

예를 들어 예측 유지 관리 시나리오에서 솔루션 백 엔드는 원격 분석 기록 데이터를 저장합니다. 솔루션에서 이 데이터를 사용하여 실제 문제가 발생하기 전에 특정 펌프의 잠재적인 비정상 동작을 식별합니다. 데이터 분석을 사용하면 예방 솔루션에서 정정 작업을 수행하도록 명령을 장치로 다시 보내는 것을 식별할 수 있습니다. 이 프로세스는 장치와 클라우드 간에 자동화된 피드백 루프를 생성하여 솔루션의 효율성을 크게 높입니다.

### <a name="presentation-and-business-connectivity"></a>프레젠테이션 및 Business Connectivity

프레젠테이션 및 Business Connectivity 계층을 통해 최종 사용자가 IoT 솔루션 및 장치를 조작할 수 있습니다. 이를 통해 사용자가 해당 장치에서 수집된 데이터를 보고 분석할 수 있습니다. 이러한 보기는 기록 데이터와 거의 실시간 데이터를 모두 표시할 수 있는 대시보드 또는 BI 보고서 형식일 수 있습니다. 예를 들어 운영자는 특정 펌핑 스테이션의 상태를 검사하고 시스템에서 발생한 경고를 확인할 수 있습니다. 또한 이 계층을 통해 IoT 솔루션 백 엔드와 기존 기간 업무 응용 프로그램을 통합하여 엔터프라이즈 비즈니스 프로세스 또는 워크플로와 연결할 수 있습니다. 예를 들어 예측 유지 관리 솔루션 가속기는 유지 관리가 필요한 펌프를 발견하면 예약 시스템과 통합되어 엔지니어의 펌핑 스테이션 방문 일정을 예약할 수 있습니다.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT solution accelerators]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
