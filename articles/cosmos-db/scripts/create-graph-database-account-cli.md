---
title: Azure CLI 스크립트 - Azure Cosmos DB Gremlin API 계정, 데이터베이스 및 그래프 만들기 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Azure Cosmos DB Gremlin API 계정, 데이터베이스 및 그래프 만들기
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/29/2018
ms.author: sngun
ms.openlocfilehash: d73a99279432746c81ee958918f8c235993c9719
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995026"
---
# <a name="azure-cosmos-db-create-a-gremlin-api-account-using-the-azure-cli"></a>Azure Cosmos DB: Azure CLI를 사용하여 Gremlin API 계정 만들기

이 샘플 CLI 스크립트는 Azure Cosmos DB Gremlin API 계정, 데이터베이스 및 그래프를 만듭니다. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-gremlin-account/create-cosmosdb-gremlin-account.sh?highlight=15-35 "Create an Azure Cosmos DB Gremlin API account, database, and graph")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Azure Cosmos DB 계정을 만듭니다. |
| [az group delete](/cli/azure/resource#az-resource-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI 설명서](../cli-samples.md)에서 확인할 수 있습니다.
