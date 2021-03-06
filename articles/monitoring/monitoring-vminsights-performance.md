---
title: VM용 Azure Monitor를 사용하여 성능을 차트로 표시하는 방법 | Microsoft Docs
description: 성능은 Windows 및 Linux 시스템의 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑하는 VM의 Azure Monitor 기능입니다. 이 문서에서는 다양한 시나리오에서 이 기능을 사용하는 방법에 대해 자세하게 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: magoedte
ms.openlocfilehash: 06073197254245727cfa41020f060d904a4e50f9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957566"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>VM용 Azure Monitor를 사용하여 성능을 차트로 표시하는 방법
VM용 Azure Monitor는 가상 머신이 얼마나 잘 실행되고 있는지 확인하기 위한 여러 가지 KPI(핵심 성과 지표)를 대상으로 하는 성능 차트 집합을 포함하고 있습니다. 이러한 차트는 시간에 따른 리소스 사용률을 표시하므로 병목 상태 및 이상 현상을 식별하거나 각 머신을 나열하는 큐브 뷰로 전환하여 선택한 메트릭을 기반으로 리소스 사용률을 볼 수 있습니다. 성능을 다룰 때 고려해야 하는 여러 요소가 있지만, VM용 Azure Monitor는 프로세서, 메모리, 네트워크 어댑터 및 디스크를 통해 표시되는 운영 체제에 집중합니다. 성능은 상태 모니터링 기능을 보완하며 시스템 구성 요소 오류를 나타내는 문제를 공개하고, 효율성 향상을 위한 튜닝 및 최적화를 지원하고, 용량 계획을 지원하는 데 도움이 됩니다.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Azure Monitor의 다중 VM 관점
Azure Monitor의 성능 기능은 구독 또는 환경의 리소스 그룹에 배포된 모든 모니터링되는 다중 가상 머신 보기를 제공합니다.  Azure Monitor에서 액세스하려면 다음을 수행합니다. 

1. Azure Portal에서 **모니터**를 선택합니다. 
2. **솔루션** 섹션에서 **Virtual Machines(미리 보기)** 를 선택합니다.
3. **성능** 탭을 선택합니다.

![VM 인사이트 성능 상위 N개 목록 보기](./media/monitoring-vminsights-performance/vminsights-performance-aggview-01.png)

**상위 N개 차트** 탭에서, Log Analytics 작업 영역이 둘 이상인 경우 페이지 맨 위에 있는 **작업 영역** 선택기에서 솔루션과 통합된 작업 영역을 선택합니다.  그런 다음, **그룹** 선택기에서 지정된 시간에 대한 구독, 리소스 그룹 또는 특정 머신을 선택합니다.  기본적으로 차트는 지난 24시간을 보여줍니다.  **TimeRange** 선택기로 최대 30일의 과거 시간 범위를 쿼리하여 과거의 성능을 살펴볼 수 있습니다.   

다음과 같은 5가지 용량 사용률 차트가 페이지에 표시됩니다.

* CPU 사용률(%) - 평균 프로세서 사용률이 가장 높은 상위 5개 머신을 보여줍니다. 
* 사용 가능한 메모리 - 사용 가능한 평균 메모리가 가장 적은 상위 5개 머신을 보여줍니다. 
* 논리 디스크 공간 사용률(%) - 모든 디스크 볼륨에서 평균 디스크 공간 사용률(%)이 가장 높은 상위 5개 머신을 보여줍니다. 
* 바이트 전송 속도 - 전송된 평균 바이트 수가 가장 높은 상위 5개 머신을 보여줍니다. 
* 바이트 수신 속도 - 수신한 평균 바이트 수가 가장 높은 상위 5개 머신을 보여줍니다. 

5개 차트 중 아무 차트의 오른쪽 상단 모서리를 클릭하면 **상위 N개 목록** 보기가 열립니다.  여기서 해당 성능 메트릭의 리소스 사용률을 목록 보기의 개별 VM별로 확인하고 어떤 머신이 가장 높은지 볼 수 있습니다.  

![선택한 성능 메트릭에 대한 상위 N개 목록 보기](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

가상 머신을 클릭하면 오른쪽에 **속성** 창이 확장되고 운영 체제에서 보고한 시스템 정보, Azure VM 속성 등 선택된 항목의 속성이 표시됩니다. **빠른 링크** 섹션 아래에서 옵션 중 하나를 클릭하면 선택한 VM에서 해당 기능으로 직접 리디렉션됩니다.  

![[가상 머신 속성] 창](./media/monitoring-vminsights-performance/vminsights-properties-pane-01.png)

**집계 차트** 탭으로 전환하면 평균 또는 백분위 측정값으로 필터링된 성능 메트릭을 볼 수 있습니다.  

![VM 인사이트 성능 집계 보기](./media/monitoring-vminsights-performance/vminsights-performance-aggview-02.png)

다음 용량 사용률 차트가 제공됩니다.

* CPU 사용률(%) - 기본적으로 평균 및 상위 95번째 백분위수를 표시 
* 사용 가능한 메모리 - 기본적으로 평균, 상위 5번째 및 10번째 백분위수를 표시 
* 논리 디스크 공간 사용률(%) - 기본적으로 평균 및 95번째 백분위수를 표시 
* 바이트 전송 속도 - 기본적으로 전송된 평균 바이트 수를 표시 
* 바이트 수신 속도 - 기본적으로 수신된 평균 바이트 수를 표시

백분위 선택기에서 **평균**, **최소**, **최대**, **50번째**, **90번째** 및 **95번째**를 선택하여 시간 범위 내에서 차트 세분성을 변경할 수도 있습니다.   

목록 보기의 개별 VM별로 리소스 사용률을 살펴보고 어떤 머신의 사용률이 가장 높은지 확인하려면 **상위 N개 목록** 탭을 선택합니다.  **상위 N개 목록** 페이지에는 *CPU 사용률(%)* 에 대한 95번째 백분위수에서 가장 많이 사용된 상위 20개 머신이 표시됩니다.  **추가 로드**를 선택하면 상위 500개 머신을 표시하도록 결과가 확장되므로 더 많은 머신을 볼 수 있습니다. 

>[!NOTE]
>목록에 한 번에 표시할 수 있는 머신 수는 최대 500개입니다.  
>

![상위 N개 목록 페이지 예제](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

목록의 특정 가상 머신에 대한 결과를 필터링하려면 **이름으로 검색** 텍스트 상자에 해당 컴퓨터 이름을 입력합니다.  

다른 성능 메트릭에서 사용률을 보려면 **메트릭** 드롭다운 목록에서 **사용 가능한 메모리**, **논리 디스크 공간 사용률(%)**, **네트워크 수신 바이트 수/초** 또는 **네트워크 전송 바이트 수/초**를 선택합니다. 그러면 해당 메트릭 범위에 속하는 사용률을 표시하도록 목록이 업데이트됩니다.  

목록에서 가상 머신을 선택하면 페이지 오른쪽에 **속성** 패널이 열리는데, 여기서 **성능 세부 정보**를 선택할 수 있습니다.  Azure VM에서 VM 인사이트 성능에 직접 액세스할 때의 환경과 마찬가지로, **가상 머신 세부 정보** 페이지가 열리고 범위가 해당 VM으로 지정됩니다.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Azure VM에서 직접 성능 보기
가상 머신에서 직접 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 **Virtual Machines**를 선택합니다. 
2. 목록에서 VM을 선택하고 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.  
3. **성능** 탭을 선택합니다. 

이 페이지에는 성능 사용률 차트뿐 아니라 검색된 각 논리 디스크, 용량, 사용률, 측정별 총 평균 사용률에 대한 표가 포함되어 있습니다.  

다음 용량 사용률 차트가 제공됩니다.

* CPU 사용률(%) - 기본적으로 평균 및 상위 95번째 백분위수를 표시 
* 사용 가능한 메모리 - 기본적으로 평균, 상위 5번째 및 10번째 백분위수를 표시 
* 논리 디스크 공간 사용률(%) - 기본적으로 평균 및 95번째 백분위수를 표시 
* 논리 디스크 IOPS - 기본적으로 평균 및 95번째 백분위수를 표시
* 논리 디스크 MB/초 - 기본적으로 평균 및 95번째 백분위수를 표시
* 최대 논리 디스크 사용률(%) - 기본적으로 평균 및 95번째 백분위수를 표시
* 바이트 전송 속도 - 기본적으로 전송된 평균 바이트 수를 표시 
* 바이트 수신 속도 - 기본적으로 수신된 평균 바이트 수를 표시

![VM 보기에서 직접 VM 인사이트 성능 보기](./media/monitoring-vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="next-steps"></a>다음 단계
상태 기능을 사용하는 방법을 알아보려면 [VM용 Azure Monitor 상태 보기](monitoring-vminsights-health.md)를 참조하고, 검색된 응용 프로그램 종속성을 보려면 [VM 맵에 대한 Azure Monitor 보기](monitoring-vminsights-maps.md)를 참조하세요. 