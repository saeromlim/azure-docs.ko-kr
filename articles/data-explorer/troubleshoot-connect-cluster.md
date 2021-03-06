---
title: Azure 데이터 탐색기에서 클러스터에 연결하지 못함
description: 이 문서에서는 Azure 데이터 탐색기에서 클러스터에 연결하는 데 관한 문제 해결 단계를 설명합니다.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f66dcc55b01b407c59c65ea300757ab4ee1002ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965061"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>문제 해결: Azure 데이터 탐색기에서 클러스터에 연결하지 못함

Azure 데이터 탐색기에서 클러스터에 연결할 수 없다면 다음 단계를 수행합니다.

1. 연결 문자열이 올바른지 확인합니다. `https://<ClusterName>.<Region>.kusto.windows.net` 형식이어야 합니다. 다음 예와 같습니다. `https://docscluster.westus.kusto.windows.net`

1. 적절한 권한이 있는지 확인합니다. 권한이 없다면 *권한 없음*의 응답을 받게 됩니다.

    사용 권한에 대한 자세한 내용은 [데이터베이스 사용 권한 관리](manage-database-permissions.md)를 참조하세요. 필요한 경우 적절한 역할에 추가할 수 있도록 클러스터 관리자와 함께 작업하세요.

1. 클러스터가 삭제되지 않았는지 확인합니다. 구독에서 활동 로그를 검토합니다.

1. [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/>)를 확인합니다. 클러스터에 연결하려는 지역에서 Azure 데이터 탐색기의 상태를 찾습니다.

    **양호**(녹색 확인 표시) 상태가 아닌 경우 상태가 개선된 후에 클러스터에 연결해 보세요.

1. 문제를 해결하는 데 여전히 지원이 필요한 경우 [Azure Portal](https://portal.azure.com)에서 지원 요청을 시작하세요.