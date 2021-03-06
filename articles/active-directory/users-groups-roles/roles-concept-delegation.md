---
title: Azure Active Directory에서 관리자 역할 위임 | Microsoft Docs
description: Azure Active Directory의 위임 모델, 예제 및 역할 보안
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/09/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: ad772a2e0c036c30aca2918496ac01f31157fc64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "40209208"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Azure Active Directory의 관리 위임

조직 성장에 따라 복잡성이 커지는 가운데, 한 가지 대처 방법은 Azure AD(Active Directory) 관리자 역할을 사용하여 액세스 관리의 오버헤드 일부를 줄이는 것입니다. 사용자에게 가능한 최소 권한을 할당하여 해당 앱에 액세스하고 작업을 수행하도록 할 수 있습니다. 모든 응용 프로그램 소유자에게 전역 관리자 역할을 할당하는 것을 원하지는 않겠지만 기존의 전역 관리자에게 응용 프로그램 관리 책임을 강제로 부과하는 것도 문제가 될 수 있습니다. 조직은 여러 가지 이유로 좀 더 분산된 관리 방식으로 전환하고 있습니다. 이 문서는 조직에서 위임에 계획하는 데 도움이 될 수 있습니다.

<!--What about reporting? Who has which role and how do I audit?-->


## <a name="centralized-versus-delegated-permissions"></a>중앙 집중식 권한 및 위임된 권한

조직이 커지면서 특정 관리 역할을 수행하는 사용자를 추적하는 것이 어려울 수 있습니다. 관리 권한을 주지 않아야 하는 직원에게 관리 권한이 있으면 조직에 보안 위험이 초래될 수 있습니다. 일반적으로 관리자 수와 권한의 세분성은 배포의 크기와 복잡성에 따라 다릅니다.

* 소규모 또는 개념 증명 배포의 경우, 한 명 또는 소수의 관리자가 모든 작업을 수행하므로 위임이 수행되지 않습니다. 이 경우 전역 관리자 역할이 있는 각 관리자를 만듭니다.
* 컴퓨터, 응용 프로그램 및 데스크톱을 포함하는 대규모 배포의 경우 추가적인 위임이 필요합니다. 몇 명의 관리자가 보다 구체적인 직무 책임(역할)을 맡을 수도 있습니다. 예를 들어, 일부는 권한 있는 ID 관리자이고, 일부는 응용 프로그램 관리자일 수 있습니다. 또한 관리자가 장치와 같은 특정 개체 그룹만 관리할 수도 있습니다.
* 좀 더 큰 배포의 경우에는 권한을 훨씬 더 세분화해야 할 수 있으며, 관리자에게 특별한 역할 또는 혼합 역할을 맡길 수도 있습니다.

Azure AD 포털에서 [모든 역할의 모든 멤버를 확인](directory-manage-roles-portal.md)할 수 있으므로 배포 및 대리자 권한을 빠르게 검토할 수 있습니다.

Azure AD의 관리 액세스 권한이 아닌 Azure 리소스에 대한 액세스 권한 위임에 관심이 있는 경우 [RBAC(역할 기반 액세스 제어) 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="delegation-planning"></a>위임 계획

어려운 점은 조직의 고유한 요구에 맞는 위임 모델을 개발하는 것이지만, 실제로는 약간만 수정해서 적용할 수 있는 아주 간단한 모델들이 있습니다. 위임 모델을 개발하는 과정은 반복적인 디자인 프로세스이므로 다음 단계를 따르는 것이 좋습니다.

* 필요한 역할 정의
* 앱 관리 위임
* 응용 프로그램 등록 기능 부여
* 앱 소유권 위임
* 보안 계획 개발
* 비상 계정 설정
* 관리자 역할 보호
* 권한 상승을 일시적으로 허용

## <a name="define-roles"></a>역할 정의

관리자가 수행하는 Active Directory 작업과 해당 작업이 역할에 매핑되는 방식을 결정합니다. 예를 들어, Active Directory 사이트 만들기는 서비스 관리 작업이지만, 보안 그룹 구성원 자격 수정은 데이터 관리 작업에 속합니다. Azure Portal에서 [자세한 역할 설명을 확인](directory-manage-roles-portal.md)할 수 있습니다.

각 작업에 대해 빈도, 중요도 및 문제점이 평가되어야 합니다. 이러한 측면은 권한이 위임되어야 하는지 여부를 제어하므로 작업 정의의 중요한 측면입니다. 정기적으로 수행되고, 위험 요소가 한정되어 있고, 완료하기 귀찮은 작업들이 위임하기에 좋은 작업입니다. 다른 한편으로 드물게 수행하면서도 조직 전체에 중대한 영향을 미치고, 고도의 기술 수준을 요구하는 작업은 위임 전에 신중히 고려해야 합니다. 대신, [일시적으로 계정을 필요한 역할로 승격](../active-directory-privileged-identity-management-configure.md)하거나 작업을 다시 할당할 수 있습니다.

## <a name="delegate-app-administration"></a>앱 관리 위임

조직 내에서 앱이 확산됨에 따라 위임 모델에도 부담을 줄 수 있습니다. 전역 관리자에게 응용 프로그램 액세스 관리에 대한 부담을 부과하는 경우 시간이 지나면서 오버헤드가 증가할 수 있습니다. 엔터프라이즈 응용 프로그램 구성과 같은 작업을 위해 전역 관리자 역할을 부여한 경우 다음과 같은 권한이 적은 역할로 오프로드할 수 있습니다. 이렇게 하면 보안 조치가 개선되고 실수할 가능성을 줄이는 데 도움이 됩니다. 권한이 가장 많은 응용 프로그램 관리자 역할은 다음과 같습니다.

* **응용 프로그램 관리자** 역할: 등록, Single Sign-On 설정, 사용자/그룹 할당 및 라이선스, 응용 프로그램 프록시 설정 및 동의를 포함하여 디렉터리의 모든 응용 프로그램을 관리하는 능력을 부여합니다. 조건부 액세스를 관리하는 기능은 부여하지 않습니다.
* **클라우드 응용 프로그램 관리자** 역할: 응용 프로그램 프록시 설정에 대한 액세스 권한을 부여하지 않는다는 점을 제외하고(온-프레미스 권한이 없으므로) 응용 프로그램 관리자의 모든 능력을 보여합니다.### 앱별로 앱 소유자 권한 위임

## <a name="delegate-app-registration"></a>앱 등록 위임

기본적으로 모든 사용자는 응용 프로그램 등록을 만들 수 있습니다. 응용 프로그램 등록을 만드는 기능을 선택적으로 부여하려는 경우 사용자 설정에서 **사용자가 응용 프로그램을 등록할 수 있음**를 아니요로 설정하고 응용 프로그램 개발자 역할을 할당해야 합니다. 이 역할은 **사용자가 응용 프로그램을 등록할 수 있음**을 해제한 경우에만 응용 프로그램 등록 생성 능력을 부여합니다. 또한 **사용자가 본인을 대신해 회사 데이터에 액세스하는 응용 프로그램에 동의할 수 있음**이 아니요로 설정된 경우 응용 프로그램 개발자는 직접 동의할 수도 있습니다. 응용 프로그램 개발자는 새 응용 프로그램 등록을 만들 때 자동으로 첫 번째 소유자로 추가됩니다.

## <a name="delegate-app-ownership"></a>앱 소유권 위임

좀 더 세분화된 앱 액세스 위임의 경우 개별 엔터프라이즈 응용 프로그램에 소유권을 할당할 수 있습니다. 이를 통해 응용 프로그램 등록 소유자 할당에 대한 기존 지원을 보완할 수 있습니다. 소유권은 엔터프라이즈 앱 블레이드에서 엔터프라이즈별 응용 프로그램 기준으로 할당됩니다. 이점은 소유자가 자신이 소유한 엔터프라이즈 응용 프로그램만 관리할 수 있다는 것입니다. 예를 들어, Salesforce 응용 프로그램의 소유자를 할당할 수 있으며, 해당 소유자는 다른 응용 프로그램이 아닌 Salesforce에 대한 액세스 및 구성을 관리할 수 있습니다. 하나의 엔터프라이즈 응용 프로그램에 많은 소유자가 있을 수 있으며 한 명의 사용자가 여러 엔터프라이즈 응용 프로그램의 소유자일 수 있습니다. 앱 소유자 역할에는 다음 두 가지가 있습니다.

* **엔터프라이즈 응용 프로그램 소유자** 역할은 Single Sign-On 설정, 사용자 및 그룹 할당, 추가 소유자 추가를 비롯하사용자여 소유하는 엔터프라이즈 응용 프로그램을 관리하는 능력을 부여합니다. 응용 프로그램 프록시 설정 또는 조건부 액세스를 관리하는 능력은 부여하지 않습니다.
* **응용 프로그램 등록 소유자** 역할은 응용 프로그램 매니페스트 및 추가 소유자 추가를 포함하여 사용자가 소유하는 앱의 응용 프로그램 등록을 관리하는 능력을 부여합니다.

## <a name="develop-a-security-plan"></a>보안 계획 개발

Azure AD는 Azure AD 관리자 역할을 계획하고, 이러한 역할에 대해 보안 계획을 실행하기 위한 포괄적인 가이드인 [하이브리드 및 클라우드 배포에 대한 권한 있는 액세스 보호](directory-admin-roles-secure.md)를 제공합니다. 

## <a name="establish-emergency-accounts"></a>비상 계정 설정

문제가 발생할 때 ID 관리 저장소에 계속 액세스할 수 있으려면 [응급 액세스 관리 계정 만들기](directory-emergency-access.md)에 따라 비상 액세스 계정을 준비합니다.

## <a name="secure-your-administrator-roles"></a>관리자 역할 보호

권한 있는 계정을 제어하는 공격자는 상당한 피해를 입힐 수 있으므로 먼저, 모든 Azure AD 테넌트에서 기본적으로 사용할 수 있는 [기본 액세스 정책](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/)(공개 미리 보기)을 사용하여 이러한 계정을 보호합니다. 정책은 권한 있는 Azure AD 계정에 대해 Multi-Factor Authentication을 적용합니다. 다음 Azure AD 역할에는 Azure AD 기준 정책이 적용됩니다.

* 전역 관리자
* SharePoint 관리자
* Exchange 관리자
* 조건부 액세스 관리자
* 보안 관리자

## <a name="elevate-privilege-temporarily"></a>일시적으로 권한 상승

대부분의 일상적인 작업의 경우 모든 사용자에게 전역 관리자 권한이 필요한 것은 아닙니다. 또한 모든 사용자가 영구적으로 전역 관리자 역할에 할당되는 것은 아닙니다. 사용자가 전역 관리자 역할을 해야 하는 경우, 자신의 계정 또는 다른 관리 계정에서 Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)의 역할 할당을 활성화해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 역할 설명에 대한 참조를 보려면 [Azure AD에서 관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.