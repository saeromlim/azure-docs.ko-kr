---
title: Azure 유지 관리 일정(미리 보기) | Microsoft Docs
description: 고객은 유지 관리 예약 기능을 사용하여 Azure SQL 데이터 웨어하우스 서비스에서 새 기능, 업그레이드 및 패치를 출시하는 데 사용하는 필요한 예약 유지 관리 이벤트를 계획할 수 있습니다.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228166"
---
# <a name="viewing-a-maintenance-schedule"></a>유지 관리 일정 확인 

## <a name="portal"></a>포털

기본적으로 새로 만드는 모든 Azure SQL Data Warehouse 인스턴스에는 배포 중에 시스템 정의 기본 및 보조 유지 관리 기간으로 8시간이 설정됩니다. 배포가 완료되는 즉시 이 기간을 편집할 수 있습니다. 사전 알림 없이는 지정된 유지 관리 기간이 아닐 때 유지 관리가 수행되지 않습니다.
포털에서 데이터 웨어하우스에 적용된 유지 관리 일정을 확인하려면 다음 단계를 완료합니다.

포털에서 데이터 웨어하우스에 적용된 유지 관리 일정을 확인하려면 다음 단계를 완료합니다.
1.  [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2.  확인하려는 데이터 웨어하우스를 선택합니다. 
3.  선택한 Azure SQL Data Warehouse가 개요 블레이드에서 열립니다. 선택한 데이터 웨어하우스에 적용되어 있는 유지 관리 일정이 유지 관리 일정(미리 보기) 아래에 표시됩니다.

![개요 블레이드](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>다음 단계
Azure Monitor를 사용하여 경고를 작성/확인/관리하는 방법에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage).
로그 경고 규칙용 웹후크 작업에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
Azure Service Health에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/service-health/service-health-overview).


