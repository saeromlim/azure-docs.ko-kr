---
title: Azure 리소스에 대한 관리 ID
description: Azure 리소스에 대한 관리 ID 개요
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 43bfe3abcbe6a66f124565cde8ddb839ba76d045
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47107253"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID란?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

클라우드 응용 프로그램을 빌드할 때 일반적으로 직면하는 난관 중 하나는 코드에서 클라우드 서비스에 인증하는 데 사용되는 자격 증명을 관리하는 방법입니다. 자격 증명을 안전하게 보호하는 것이 중요합니다. 자격 증명이 개발자 워크스테이션에 표시되지 않고 소스 제거에 체크 인되지 않는 것이 가장 좋습니다. Azure Key Vault를 사용하면 자격 증명, 비밀 및 기타 키를 안전하게 저장할 수 있지만, 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. 

Azure AD(Azure Active Directory)의 Azure 리소스에 대한 관리 ID 기능은 이 문제를 해결합니다. 이 기능은 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다.

Azure 리소스에 대한 관리 ID 기능은 Azure 구독용 Azure AD에 무료로 제공됩니다. 추가 비용은 없습니다.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## 이 기능이 어떻게 작동하나요?<a name="how-does-it-work"></a>

두 가지 종류의 관리 ID가 있습니다.

- **시스템 할당 관리 ID**는 Azure 서비스 인스턴스에서 직접 사용하도록 설정됩니다. ID를 사용하도록 설정하면 Azure는 인스턴스의 구독에서 신뢰하는 Azure AD 테넌트에 인스턴스의 ID를 만듭니다. ID가 만들어지면 자격 증명이 인스턴스에 프로비전됩니다. 시스템 할당 ID의 수명 주기는 사용하도록 설정된 Azure 서비스 인스턴스와 직접적으로 연관됩니다. 인스턴스가 삭제되면 Azure는 Azure AD에서 자격 증명과 ID를 자동으로 정리합니다.
- **사용자 할당 관리 ID**는 독립 실행형 Azure 리소스로 생성됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다.

코드가 관리 ID를 사용하여 Azure AD 인증을 지원하는 서비스용 액세스 토큰을 요청할 수 있습니다. 서비스 인스턴스가 사용하는 자격 증명 롤링은 Azure에서 처리합니다.

다음 다이어그램은 관리 서비스 ID가 Azure VM(가상 머신)에서 작동하는 방식을 보여줍니다.

![관리 서비스 ID 및 Azure VM](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>시스템 할당 관리 ID가 Azure VM에서 작동하는 방식

1. Azure Resource Manager가 VM에서 시스템 할당 관리 ID를 사용하도록 설정하라는 메시지를 받습니다.
2. Azure Resource Manager가 Azure AD에 VM ID에 대한 서비스 주체를 만듭니다. 서비스 주체는 구독이 신뢰하는 Azure AD 테넌트에 생성됩니다.
3. Azure Resource Manager가 VM에서 ID를 구성합니다.
    1. Azure Instance Metadata Service ID 엔드포인트를 서비스 주체 클라이언트 ID 및 인증서로 업데이트합니다.
    1. VM 확장(2019년 1월에 사용 중단될 예정)을 프로비전하고 서비스 주체 클라이언트 ID 및 인증서를 추가합니다. (이 단계는 사용 중단될 예정입니다.)
4. VM에 ID가 생긴 후에는 서비스 주체 정보를 사용하여 Azure 리소스에 대한 VM 액세스 권한을 부여합니다. Azure Resource Manager를 호출하려면 Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 VM 서비스 주체에 적절한 역할을 할당합니다. Key Vault를 호출하려면 Key Vault의 특정 비밀 또는 키에 대한 액세스 권한을 코드에 부여합니다.
5. VM에서 실행되는 코드는 VM 내에서만 액세스할 수 있는 두 엔드포인트에서 토큰을 요청할 수 있습니다.

    - Azure Instance Metadata Service ID 엔드포인트(권장): `http://169.254.169.254/metadata/identity/oauth2/token`
        - 리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. Azure Resource Manager에 인증하려면 `resource=https://management.azure.com/`을 사용합니다.
        - API 버전 매개 변수는 IMDS 버전을 지정하고, api-version=2018-02-01 이상을 사용합니다.
    - VM 확장 엔드포인트(2019년 1월에 사용 중단될 예정): `http://localhost:50342/oauth2/token` 
        - 리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. Azure Resource Manager에 인증하려면 `resource=https://management.azure.com/`을 사용합니다.

6. 3단계에서 구성한 클라이언트 ID 및 인증서를 사용하여 5단계에서 지정한 대로 액세스 토큰을 요청하는 Azure AD에 대한 호출이 생성됩니다. Azure AD가 JWT(JSON Web Token) 액세스 토큰을 반환합니다.
7. 코드가 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>사용자 할당 관리 ID가 Azure VM에서 작동하는 방식

1. Azure Resource Manager가 사용자 할당 관리 ID를 만들라는 요청을 받습니다.
2. Azure Resource Manager가 Azure AD에 사용자 할당 관리 ID에 대한 서비스 주체를 만듭니다. 서비스 주체는 구독이 신뢰하는 Azure AD 테넌트에 생성됩니다.
3. Azure Resource Manager가 VM에서 사용자 할당 관리 ID를 구성하라는 요청을 받습니다.
    1. Azure Instance Metadata Service ID 엔드포인트를 사용자 할당 관리 ID 서비스 주체 클라이언트 ID 및 인증서로 업데이트합니다.
    1. VM 확장을 프로비전하고, 사용자 할당 관리 ID 서비스 주체 클라이언트 ID 및 인증서를 추가합니다. (이 단계는 사용 중단될 예정입니다.)
4. 사용자 할당 관리 ID가 생성된 후에는 서비스 주체 정보를 사용하여 Azure 리소스에 대한 ID 액세스 권한을 부여합니다. Azure Resource Manager를 호출하려면 Azure AD에서 RBAC를 사용하여 사용자 할당 ID의 서비스 주체에 적절한 역할을 할당합니다. Key Vault를 호출하려면 Key Vault의 특정 비밀 또는 키에 대한 액세스 권한을 코드에 부여합니다.

   > [!Note]
   > 이 단계를 3단계 전에 수행할 수도 있습니다.

5. VM에서 실행되는 코드는 VM 내에서만 액세스할 수 있는 두 엔드포인트에서 토큰을 요청할 수 있습니다.

    - Azure Instance Metadata Service ID 엔드포인트(권장): `http://169.254.169.254/metadata/identity/oauth2/token`
        - 리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. Azure Resource Manager에 인증하려면 `resource=https://management.azure.com/`을 사용합니다.
        - 클라이언트 ID 매개 변수는 토큰이 요청되는 ID를 지정합니다. 이 값은 단일 VM에 사용자 할당 ID가 두 개 이상 있을 때 분명히 하기 위해 필요합니다.
        - API 버전 매개 변수는 Azure Instance Metadata Service 버전을 지정합니다. `api-version=2018-02-01` 이상을 사용하세요.

    - VM 확장 엔드포인트(2019년 1월에 사용 중단될 예정): `http://localhost:50342/oauth2/token`
        - 리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. Azure Resource Manager에 인증하려면 `resource=https://management.azure.com/`을 사용합니다.
        - 클라이언트 ID 매개 변수는 토큰이 요청되는 ID를 지정합니다. 이 값은 단일 VM에 사용자 할당 ID가 두 개 이상 있을 때 분명히 하기 위해 필요합니다.
6. 3단계에서 구성한 클라이언트 ID 및 인증서를 사용하여 5단계에서 지정한 대로 액세스 토큰을 요청하는 Azure AD에 대한 호출이 생성됩니다. Azure AD가 JWT(JSON Web Token) 액세스 토큰을 반환합니다.
7. 코드가 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 사용하는 방법

관리 ID를 사용하여 Azure 리소스에 액세스하는 방법을 알아보려면 다음 자습서를 진행하세요.

Windows VM에서 관리 ID를 사용하는 방법:

* [Azure Data Lake Store에 액세스](tutorial-windows-vm-access-datalake.md)
* [Azure Resource Manager 액세스](tutorial-windows-vm-access-arm.md)
* [Azure SQL에 액세스](tutorial-windows-vm-access-sql.md)
* [액세스 키를 사용하여 Azure Storage에 액세스](tutorial-windows-vm-access-storage.md)
* [공유 액세스 서명을 사용하여 Azure Storage에 액세스](tutorial-windows-vm-access-storage-sas.md)
* [Azure Key Vault를 사용하여 비 Azure AD 리소스에 액세스](tutorial-windows-vm-access-nonaad.md)

Linux VM에서 관리 ID를 사용하는 방법:

* [Azure Data Lake Store에 액세스](tutorial-linux-vm-access-datalake.md)
* [Azure Resource Manager 액세스](tutorial-linux-vm-access-arm.md)
* [액세스 키를 사용하여 Azure Storage에 액세스](tutorial-linux-vm-access-storage.md)
* [공유 액세스 서명을 사용하여 Azure Storage에 액세스](tutorial-linux-vm-access-storage-sas.md)
* [Azure Key Vault를 사용하여 비 Azure AD 리소스에 액세스](tutorial-linux-vm-access-nonaad.md)

다른 Azure 서비스에서 관리 ID를 사용하는 방법:

* [Azure App Service](/azure/app-service/app-service-managed-service-identity)
* [Azure 기능](/azure/app-service/app-service-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)

## Azure 서비스에서 어떤 기능을 지원하나요?<a name="which-azure-services-support-managed-identity"></a>

Azure 리소스에 대한 관리 ID는 Azure AD 인증을 지원하는 서비스를 인증하는 데 사용할 수 있습니다. Azure 리소스에 대한 관리 ID 기능을 지원하는 Azure 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](services-support-msi.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 빠른 시작으로 Azure 리소스에 대한 관리 ID 기능을 시작하세요.

* [Windows VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-windows-vm-access-arm.md)
* [Linux VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-linux-vm-access-arm.md)
