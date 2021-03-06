---
title: Azure Blueprints의 리소스 잠금 이해
description: 청사진을 할당할 때 리소스를 보호하기 위한 잠금 옵션에 대해 알아봅니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973255"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure Blueprints의 리소스 잠금 이해

일관성 있는 환경을 원하는 규모로 만든다고 해도 해당 일관성을 유지할 수 있는 메커니즘이 있어야 환경을 적절하게 활용할 수 있습니다. 이 문서에서는 Azure Blueprints에서 리소스 잠금이 작동하는 방식에 대해 설명합니다.

## <a name="locking-modes-and-states"></a>잠금 모드 및 상태

잠금 모드는 청사진 할당에 적용되며 **없음** 또는 **모든 리소스**의 두 가지 옵션만 포함합니다. 청사진 할당 시에 구성되는 이 옵션은 할당이 구독에 정상 적용되고 나면 변경할 수 없습니다.

구독에 할당된 청사진 내의 아티팩트 정의를 기준으로 생성되는 리소스의 상태는 **잠겨있지 않음**, **읽기 전용** 또는 **편집/삭제할 수 없음**의 세 가지입니다. 어떤 종류의 아티팩트이든 **잠겨있지 않음** 상태일 수 있습니다. 그러나 리소스 그룹이 아닌 아티팩트는 **읽기 전용** 상태이며 리소스 그룹은 **편집/삭제할 수 없음** 상태입니다. 이러한 상태에 따라 리소스 관리 방식이 구분됩니다.

**읽기 전용** 상태는 정의된 그대로입니다. 즉, 리소스를 어떤 방식으로도 변경할 수 없으며 삭제할 수도 없습니다. **편집/삭제할 수 없음**의 경우 리소스 그룹의 “컨테이너” 특성으로 인해 약간 차이가 있습니다. 즉, 리소스 그룹 개체는 읽기 전용이지만 리소스 그룹 내에서 리소스를 만들고 업데이트하고 삭제할 수는 있습니다. 단, 이러한 리소스가 **읽기 전용** 잠금 상태인 청사진 할당에 포함되지 않아야 합니다.

## <a name="overriding-locking-states"></a>잠금 상태 재정의

‘소유자’ 역할 등 구독에 대해 적절한 RBAC([역할 기반 액세스 제어](../../../role-based-access-control/overview.md)) 권한이 있는 사용자는 일반적으로 모든 리소스를 변경하거나 삭제할 수 있지만, 배포된 할당의 일부분으로 Blueprints가 잠금을 적용하는 경우에는 이러한 작업이 불가능합니다. **잠금** 옵션으로 할당을 설정한 경우에는 구독 소유자도 포함된 리소스를 변경할 수 없습니다.

따라서 정의된 청사진과 해당 청사진을 통해 생성하려는 환경을 실수로/프로그래밍 방식으로 삭제하거나 변경하는 상황을 방지하여 일관성을 유지할 수 있습니다.

## <a name="removing-locking-states"></a>잠금 상태 제거

할당을 통해 생성된 리소스를 삭제해야 하는 경우에는 먼저 할당을 제거해야 합니다. 할당을 제거하면 Blueprints를 통해 생성된 잠금이 제거됩니다. 그러나 리소스는 남아 있으므로 적절한 권한이 있는 사용자가 일반적인 방법을 통해 삭제해야 합니다.

## <a name="how-blueprint-locks-work"></a>청사진 잠금의 작동 방식

할당에서 **잠금** 옵션을 선택한 경우 RBAC 역할 `denyAssignments`가 청사진 할당 중에 아티팩트 리소스에 적용됩니다. 역할은 청사진 할당의 관리 ID를 통해 추가되며 같은 관리 ID를 통해서만 아티팩트 리소스에서 제거할 수 있습니다. 따라서 잠금 메커니즘이 적용되며, Blueprints 외부에서 청사진을 제거할 수 없습니다. 역할과 잠금을 제거하려면 청사진 할당을 제거해야 합니다. 적절한 권한이 있는 사용자만이 제거 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결