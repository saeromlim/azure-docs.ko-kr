---
title: Data Factory를 사용하여 Azure Cosmos DB 간 데이터 복사 | Microsoft Docs
description: 지원되는 원본 데이터 저장소에서 Azure Cosmos DB로 또는 Cosmos DB에서 지원되는 싱크 데이터 저장소로 Data Factory를 사용하여 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 1afd64fbd7019164f0e1f5c850f2dcd8250cdbfc
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600339"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Cosmos DB 간 데이터 복사

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-azure-documentdb-connector.md)
> * [현재 버전](connector-azure-cosmos-db.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Cosmos DB(SQL API) 간 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Cosmos DB에서 모든 지원되는 싱크 데이터 저장소로 또는 모든 지원되는 원본 데이터 저장소에서 Azure Cosmos DB로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

특히 이 Azure Cosmos DB 커넥터는 다음을 지원합니다.

- Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)
- JSON 문서를 있는 그대로 가져오기/내보내기 또는 표 형식 데이터 집합(예: SQL 데이터베이스, CSV 파일 등) 간 데이터 복사 JSON 파일 또는 다른 Cosmos DB 컬렉션 간 있는 그대로 문서를 복사하려면 [JSON 문서 Import/Export](#importexport-json-documents)를 참조하세요.

Data Factory는 Cosmos DB에 최상의 성능을 제공하기 위해 [Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)와 통합됩니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Cosmos DB에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Cosmos DB 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **CosmosDb**로 설정해야 합니다. | yes |
| connectionString |Azure Cosmos DB 데이터베이스에 연결하는 데 필요한 정보를 지정합니다. 아래 샘플과 같이 연결 문자열에 데이터베이스 정보를 지정해야 합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

**예제:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Azure Cosmos DB 데이터 집합에서 지원하는 속성 목록을 제공합니다.

Azure Cosmos DB 간 데이터를 복사하려면 데이터 집합의 type 속성을 **DocumentDbCollection**으로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성을 **DocumentDbCollection**으로 설정해야 합니다. |yes |
| collectionName |Cosmos DB 문서 컬렉션의 이름입니다. |yes |

**예제:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory에서의 스키마

Azure Cosmos DB와 같은 스키마 없는 데이터 저장소의 경우 복사 작업은 다음 방법 중 하나로 스키마를 유추합니다. 따라서 [JSON 문서를 있는 그대로 가져오기/내보내기](#importexport-json-documents)하려는 경우가 아닌 한 **구조** 섹션에서 데이터의 구조를 지정하는 것이 좋습니다.

*. 데이터 집합 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하는 경우 Data Factory 서비스는 이 구조를 스키마로 인식합니다. 이 경우 행에 열의 값이 포함되어 있지 않으면 null 값이 제공됩니다.
*. 데이터 집합 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하지 않는 경우 Data Factory 서비스는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 이 경우 첫 번째 행에 전체 스키마가 포함되어 있지 않으면 일부 열이 복사 작업의 결과에서 누락됩니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Cosmos DB 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="azure-cosmos-db-as-source"></a>원본으로 Azure Cosmos DB

Azure Cosmos DB에서 데이터를 복사하려면 복사 작업의 원본 형식을 **DocumentDbCollectionSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성을 **DocumentDbCollectionSource**로 설정해야 합니다. |yes |
| 쿼리 |데이터를 읽는 Cosmos DB 쿼리를 지정합니다.<br/><br/>예제: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |아니요 <br/><br/>지정하지 않는 경우 실행되는 SQL 문: `select <columns defined in structure> from mycollection` |
| nestingSeparator |문서가 중첩되고 결과 집합을 평면화하는 방법을 나타내는 특수 문자<br/><br/>예를 들어, Cosmos DB 쿼리가 중첩된 결과 `"Name": {"First": "John"}`을 반환하는 경우 복사 작업은 nestedSeparator가 점인 경우 "John"이라는 값으로 "Name.First"로 열 이름을 식별합니다. |아니요(기본값: 점 `.`) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>싱크로 Azure Cosmos DB

Azure Cosmos DB로 데이터를 복사하려면 복사 작업의 싱크 형식을 **DocumentDbCollectionSink**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 형식 속성을 **DocumentDbCollectionSink**로 설정해야 합니다. |yes |
| writeBehavior |Cosmos DB에 데이터를 작성하는 방법을 설명합니다. 허용되는 값은 `insert` 및 `upsert`입니다.<br/>**upsert**의 동작은 동일한 ID의 문서가 이미 존재하는 경우 문서를 바꾸는 것이며, 존재하지 않는 경우 문서를 삽입하는 것입니다. 원래 문서에서 또는 열 매핑으로 지정되지 않은 경우 ADF는 문서에 대한 ID를 자동으로 생성합니다. 이는 upsert가 예상대로 작동하도록 문서에 "ID"가 있는지 확인해야 한다는 의미입니다. |아니요(기본값: 삽입) |
| writeBatchSize | Data Factory는 [Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)를 사용하여 Cosmos DB에 데이터를 작성합니다. "writeBatchSize"는 매번 라이브러리에 제공하는 문서의 크기를 제어합니다. writeBatchSize를 증가시켜 성능을 향상시킬 수 있습니다. |아니요, 기본값은 10,000입니다. |
| nestingSeparator |중첩된 해당 문서를 나타내는 원본 열 이름에 특수 문자가 필요합니다. <br/><br/>예를 들어, 출력 데이터 집합 구조에서 `Name.First`는 nestedSeparator가 점인 경우 Cosmos DB 문서에서 다음 JSON 구조를 생성합니다. `"Name": {"First": "[value maps to this column from source]"}` |아니요(기본값: 점 `.`) |

**예제:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>JSON 문서 Import/Export

이 Cosmos DB 커넥터를 사용하여 다음을 쉽게 수행할 수 있습니다.

* Azure Blob, Azure Data Lake Store 및 기타 Azure Data Factory에서 지원하는 파일 기반 저장소 등 다양한 원본에서 Cosmos DB로 JSON 문서를 가져오기
* Cosmos DB 컬렉션에서 다양한 파일 기반 저장소로 JSON 문서 내보내기
* 두 Cosmos DB 컬렉션 간에 데이터를 있는 그대로 복사

이러한 스키마 독립적 복사를 수행하려면:

* 데이터 복사 도구를 사용하는 경우, **“JSON 파일 또는 Cosmos DB 컬렉션으로 있는 그대로 내보내기”** 옵션을 선택합니다.
* 작업을 작성할 때 복사 작업에서 Cosmos DB 데이터 집합의 “structure”(스키마) 섹션 및 Cosmos DB 원본/싱크의 “nestingSeparator” 속성을 지정하면 안 됩니다. JSON 파일에서 가져오기/내보내기를 수행하는 경우, 해당 파일 저장소 데이터 집합에서 형식 유형을 “JsonFormat”으로 지정하고 “filePattern”을 올바르게 구성합니다(자세한 내용은 [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format) 섹션 참조). “structure”(스키마) 섹션을 지정하지 말고 나머지 형식 설정을 건너뜁니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
