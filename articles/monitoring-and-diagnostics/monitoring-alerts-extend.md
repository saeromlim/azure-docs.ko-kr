---
title: Azure Alerts로 Log Analytics 경고 확장(복사) - 개요
description: 일반적인 고객 문제에 대응하는 자세한 정보를 포함한 OMS 포털의 Log Analytics에서 Azure Alerts로 경고 복사 프로세스의 개요입니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: eb3489c24bd5aa328620c5a6c14ee71882a6a6f2
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249573"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Log Analytics 경고를 Azure Alerts로 확장
최근까지 Azure Log Analytics에는 Log Analytics 데이터에 기반한 조건을 사전에 알려줄 수 있는 자체 경고 기능이 포함됩니다. Microsoft Operations Management Suite 포털에서 경고 규칙을 관리했습니다. 새 경고 환경은 이제 Microsoft Azure의 다양한 서비스에서 경고를 통합했습니다. 이 환경은 Azure Portal에서 Azure Monitor 아래의 **경고**로 사용 가능하며 Log Analytics 및 Azure Application Insights에서 활동 로그, 메트릭 및 로그의 경고를 지원합니다. 

## <a name="benefits-of-extending-your-alerts"></a>경고 확장의 이점
다음과 같이 Azure Portal에서 경고를 만들고 관리하는 여러 이점이 있습니다.

- 250개의 경고를 생성 및 볼 수 있는 Operations Management Suite 포털에서와 달리 Azure Alerts에서는 이러한 제한 사항이 없습니다.
- Azure Alerts에서 모든 경고 유형을 관리하고 열거하며 볼 수 있습니다. 전에는 Log Analytics 경고에 대해서만 그렇게 할 수 있었습니다.
- [Azure Monitor 역할](monitoring-roles-permissions-security.md)을 사용하여 모니터링 및 경고에 대해 사용자 액세스를 제한할 수 있습니다.
- Azure Alerts에서 [작업 그룹](monitoring-action-groups.md)을 사용할 수 있습니다. 이 옵션을 사용하면 각 경고에 대해 1 초과 작업이 있을 수 있습니다. SMS를 보내고 음성 통화를 하고 Azure Automation Runbook을 호출하고 웹후크를 호출하고 ITSM(IT 서비스 관리) 커넥터를 구성할 수 있습니다. 

## <a name="process-of-extending-your-alerts"></a>경고 확장의 프로세스
Log Analytics에서 Azure Alerts로 경고를 이동하는 프로세스는 어떠한 방법으로도 경고 정의, 쿼리 또는 구성의 변경을 포함하지 않습니다. 필요한 유일한 변경은 Azure에서 작업 그룹을 사용하여 모든 작업을 수행하는 것입니다. 작업 그룹이 이미 경고와 연결되어 있는 경우 작업 그룹은 Azure로 확장될 때 포함됩니다.

> [!NOTE]
> Microsoft는 완료될 때까지 시리즈를 반복하면서 2018년 5월 14일부터 Log Analytics의 퍼블릭 클라우드 인스턴스에서 만든 경고를 자동으로 Azure Alerts로 확장합니다. [작업 그룹](monitoring-action-groups.md)을 만드는 데 문제가 있는 경우 [이러한 재구성 단계](monitoring-alerts-extend-tool.md#troubleshooting)를 사용하여 만든 작업 그룹을 자동으로 가져옵니다. 2018년 7월 5일까지는 이러한 단계를 사용할 수 있습니다. *Log Analytics의 Azure Goverment 및 Soveriegn 클라우드 사용자에게는 적용할 수 없습니다*. 
> 

Log Analytics 작업 영역의 경고가 Azure로 확장되도록 일정을 예약하면 계속해서 작동하고 어떤 방식으로든 구성을 손상하지 않습니다. 예약된 경우 경고는 일시적으로 수정이 가능하지 않을 수 있지만 이 시간 동안 새 Azure 경고를 계속 만들 수는 있습니다. Operations Management Suite 포털에서 경고를 만들거나 편집하려는 경우 Log Analytics 작업 영역에서 경고를 계속 만들 수 있는 옵션이 있습니다. 또한 Azure Portal의 Azure Alerts에서 경고 만들기를 선택할 수 있습니다.

 ![Log Analytics 또는 Azure Alerts에서 경고를 만들 수 있는 옵션 스크린샷](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Log Analytics에서 Azure로 경고를 확장하면 계정에 요금을 발생하지 않습니다. 쿼리 기반 Log Analytics 경고에 대한 Azure Alerts 사용은 [Azure Monitor 가격 책정 정책](https://azure.microsoft.com/pricing/details/monitor/)에 명시된 제한 및 조건 내에서 사용되는 경우 청구되지 않습니다.  


### <a name="how-to-extend-your-alerts-voluntarily"></a>자발적으로 경고를 확장하는 방법
Azure Alerts에 경고를 확장하려면 Operations Management Suite 포털에서 사용 가능한 마법사를 사용하거나 API를 사용하여 프로그래밍 방식으로 마법사를 사용할 수 있습니다. 자세한 내용은 [Operations Management Suite 포털 및 API를 사용하여 Azure로 경고 확장](monitoring-alerts-extend-tool.md)을 참조하세요.

## <a name="experience-after-extending-your-alerts"></a>경고 확장 후의 환경
경고가 Azure Alerts로 확장된 후 이전과 다르지 않게 Operations Management Suite 포털에서 관리를 위해 경고를 계속 사용할 수 있습니다.

![경고가 나열되어 있는 Operations Management Suite 포털의 스크린샷](./media/monitor-alerts-extend/PostExtendList.png)

Operations Management Suite 포털에서 새 경고를 만들거나 기존 경고를 편집하려고 하면 자동으로 Azure Alerts로 리디렉션됩니다.  

> [!NOTE]
> 경고를 추가하거나 편집해야 하는 개인에게 할당된 사용 권한이 Azure에서 제대로 할당됐는지 확인합니다. 부여해야 하는 사용 권한을 이해하려면 [Azure Monitor 및 Alerts를 사용한 사용 권한](monitoring-roles-permissions-security.md)을 참조합니다.  
> 

[Log Analytics API](../log-analytics/log-analytics-api-alerts.md) 및 [Log Analytics 리소스 템플릿](../monitoring/monitoring-solutions-resources-searches-alerts.md)에서 계속 경고를 만들 수 있습니다. 이렇게 하면 작업 그룹을 포함해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Log Analytics에서 Azure로 경고 확장을 시작](monitoring-alerts-extend-tool.md)하는 도구를 알아봅니다.
* [Azure Alerts 환경](monitoring-overview-unified-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Alerts에서 로그 경고](monitor-alerts-unified-log.md)를 만드는 방법을 알아봅니다.
