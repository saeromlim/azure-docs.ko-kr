---
title: Azure Functions 오류 처리 지침 | Microsoft Docs
description: 함수를 실행하는 경우에 발생하는 오류를 처리하기 위한 일반적인 지침 및 바인딩 관련 오류 항목에 대한 링크를 제공합니다.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 5a8dae73c164b319b4c291685deff402f9798364
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091842"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 오류 처리

이 항목에서는 함수를 실행하는 경우 발생하는 오류를 처리하기 위한 일반적인 지침을 제공합니다. 또한 발생할 수 있는 바인딩 관련 오류를 설명하는 항목에 대한 링크를 제공합니다. 

## <a name="handing-errors-in-functions"></a>함수에서 오류 처리
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>바인딩 오류 코드

Azure 서비스와 통합하는 경우 기본 서비스의 API에서 발생하는 오류가 발생할 수 있습니다. 이 서비스를 위한 오류 코드 설명서 링크는 다음 트리거 및 바인딩 참조 항목의 **예외 및 반환 코드**  섹션에 있습니다.

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
