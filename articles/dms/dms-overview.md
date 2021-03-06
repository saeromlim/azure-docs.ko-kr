---
title: Azure Database Migration Service 개요 | Microsoft Docs
description: 많은 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션을 제공하는 Azure Database Migration Service 개요입니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 09/01/2018
ms.openlocfilehash: d59850b0234912b02b003f4fc8089d76130151ba
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666232"
---
# <a name="what-is-the-azure-database-migration-service"></a>Azure Database Migration Service란 무엇인가요?
Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다.

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>익숙한 도구로 Azure에 데이터베이스 마이그레이션
Azure Database Migration Service는 기존 도구 및 서비스의 일부 기능을 통합합니다. 고객에게 포괄적이고 항상 사용 가능한 솔루션을 제공합니다. 서비스는 [Data Migration Assistant](http://aka.ms/dma)를 사용하여 마이그레이션을 수행하기 전에 필요한 변경 사항을 안내하는 권장 사항을 제공하는 평가 보고서를 생성합니다. 필요한 모든 수정 작업을 수행하는 것은 사용자의 몫입니다. 마이그레이션 프로세스를 시작할 준비가 되면 Azure Database Migration Service는 필요한 단계를 모두 수행합니다. 프로세스가 Microsoft에서 확인한 모범 사례를 활용한다는 사실에 안심하고 마이그레이션 프로젝트를 시작하고 잊어버릴 수 있습니다.

## <a name="regional-availability"></a>국가별 가용성
Azure Database Migration Service는 현재 다음 지역에서 사용할 수 있습니다.

![Azure Database Migration Service 지역별 가용성](media\overview\dms-regional-availability.png)

> [!NOTE]
> 온라인 마이그레이션 및 SKU 권장 사항 기능은 현재 **미국 중부**, **미국 동부 2** 및 **유럽 서부** 지역에서만 사용할 수 있습니다.

Azure Database Migration Service의 지역별 가용성에 대한 최신 정보는 Azure 글로벌 인프라 사이트에서 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure Portal을 사용하여 Azure Database Migration Service 인스턴스를 만듭니다](quickstart-create-data-migration-service-portal.md).
- [SQL Server를 Azure SQL Database로 마이그레이션](tutorial-sql-server-to-azure-sql.md)
- [Azure Database Migration Service 사용을 위한 필수 구성 요소 개요](pre-reqs.md)
- [Azure Database Migration Service 사용에 대한 FAQ](faq.md)
