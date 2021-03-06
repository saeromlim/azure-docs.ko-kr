---
title: Azure 유지 관리 일정(미리 보기) | Microsoft Docs
description: 고객은 유지 관리 예약 기능을 사용하여 Azure SQL 데이터 웨어하우스 서비스에서 새 기능, 업그레이드 및 패치를 출시하는 데 사용하는 필요한 예약 유지 관리 이벤트를 계획할 수 있습니다.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228218"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>유지 관리 일정을 사용하여 서비스 유지 관리 및 업데이트 관리

Azure SQL Data Warehouse 유지 관리 예약 기능은 미리 보기로 제공됩니다. 새롭게 제공되는 이 기능은 서비스 상태 계획된 유지 관리 알림, 리소스 상태 검사 모니터 및 Azure SQL Data Warehouse 유지 관리 예약 서비스와 원활하게 통합됩니다.

유지 관리 예약 기능을 사용하면 새 기능, 업그레이드 및 패치를 받기에 편리한 기간을 예약할 수 있습니다. 고객은 7일 이내의 기본 유지 관리 기간과 보조 유지 관리 기간을 선택합니다. 예를 들어 토요일 오후 10시~일요일 오전 1시(기본) 및 수요일 오후 7시~오후 10시(보조)를 선택할 수 있습니다. 기본 유지 관리 기간 중에 유지 관리를 수행할 수 없는 경우 보조 유지 관리 기간 중에 유지 관리를 다시 시도합니다.

새로 작성되는 모든 Azure SQL Data Warehouse 인스턴스의 경우 배포 중에 시스템 정의 유지 관리 일정이 적용됩니다. 배포가 완료되는 즉시 일정을 편집할 수 있습니다.

각 유지 관리 기간은 3~8시간 사이로 지정할 수 있으며 현재 사용 가능한 가장 짧은 기간 옵션은 3시간입니다. 유지 관리 기간 내에 언제든지 유지 관리를 수행할 수 있으며, 이 기간 동안에는 서비스가 데이터 웨어하우스에 새 코드를 배포하므로 연결이 잠깐 동안 끊깁니다. 

기능 미리 보기 기간 동안에는 고객이 각기 별도의 기간 범위(일) 내에 기본 기간과 보조 기간을 지정해야 합니다.   
모든 유지 관리 작업은 예약된 유지 관리 기간 내에 완료해야 하며, 사전 알림 없이는 지정된 유지 관리 기간이 아닐 때 유지 관리가 수행되지 않습니다. 데이터 웨어하우스는 예약된 유지 관리 중에 일시 중지되는 경우 다시 시작 작업 중에 업데이트됩니다.  


## <a name="alerts-and-monitoring"></a>경고 및 모니터링

유지 관리 예약 기능은 서비스 상태 알림 및 리소스 상태 검사 모니터와 원활하게 통합되므로, 고객은 예정된 유지 관리 활동의 정보를 지속적으로 파악할 수 있습니다. 새롭게 제공되는 이 자동화 기능은 Azure Monitor를 활용합니다. 고객은 이 기능을 통해 예정된 유지 관리 이벤트 알림을 받을 방법과, 작업에 대한 영향을 최소화하고 가동 중지 시간을 관리하기 위해 트리거해야 하는 자동화된 흐름을 결정할 수 있습니다.

모든 유지 관리 이벤트의 24시간 전에는 사전 알림이 전송됩니다. 인스턴스 가동 중지 시간을 최소화하려면 선택한 유지 관리 기간이 시작되기 전에 데이터 웨어하우스에서 장기 실행 트랜잭션이 없는지 확인해야 합니다. 유지 관리가 시작되면 모든 활성 세션은 취소되고 커밋되지 않은 트랜잭션은 롤백되며 데이터 웨어하우스의 연결이 잠시 끊깁니다. 데이터 웨어하우스에서 유지 관리가 완료된 직후에 알림이 전송됩니다. 

유지 관리가 수행된다는 사전 알림을 받았는데 해당 시간 중에 유지 관리를 수행할 수 없는 경우에는 취소 알림이 전송됩니다. 그러면 유지 관리는 예약된 다음 유지 관리 기간에 다시 시작됩니다.
 
모든 활성 유지 관리 이벤트는 ‘서비스 상태 - 계획된 유지 관리’ 섹션에 표시됩니다. 이전 이벤트의 전체 레코드는 서비스 상태 기록의 일부분으로 보존됩니다. 활성 이벤트가 진행되는 중에 Azure Service Health 검사 포털 대시보드를 통해 유지 관리를 모니터링할 수 있습니다.

### <a name="maintenance-schedule-availability"></a>유지 관리 일정 사용 가능성

선택한 지역에서 유지 관리 예약 기능이 아직 제공되지 않더라도 언제든지 유지 관리 일정을 확인하고 편집할 수 있습니다. 유지 관리 예약 기능을 해당 지역에서 사용할 수 있게 되면 확인된 일정이 데이터 웨어하우스에서 즉시 활성화됩니다.


## <a name="next-steps"></a>다음 단계

- 유지 관리 일정 확인 방법 [자세히 알아보기](viewing-maintenance-schedule.md) 
- 유지 관리 일정 변경 방법 [자세히 알아보기](changing-maintenance-schedule.md)
- Azure Monitor를 사용하여 경고를 작성/확인/관리하는 방법 [자세히 알아보기](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)
- 로그 경고 규칙용 웹후크 작업에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)
- Azure Service Health에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/service-health/service-health-overview)







