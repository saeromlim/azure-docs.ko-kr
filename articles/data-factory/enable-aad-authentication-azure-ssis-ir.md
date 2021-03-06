---
title: Azure-SSIS 통합 런타임에 대한 Azure Active Directory 인증 활성화 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory 인증을 사용하는 연결을 활성화하도록 Azure-SSIS 통합 런타임을 구성하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: douglasl
ms.openlocfilehash: aa06110a6f6fe668388c6aecd98c1ddeeae37edd
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576632"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임에 대한 Azure Active Directory 인증 활성화

이 문서에서는 Azure Data Factory 서비스 ID를 사용하여 Azure-SSIS IR을 만드는 방법을 보여줍니다. Azure-SSIS 통합 런타임에 대한 MSI(관리 서비스 ID)가 있는 Azure AD(Azure Active Directory) 인증 서버를 통해 SQL 인증 대신 Data Factory MSI를 사용하여 Azure-SSIS 통합 런타임을 만들 수 있습니다.

Data Factory MSI에 대한 자세한 내용은 [Azure Data Factory 서비스 ID](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)를 참조하세요.

> [!NOTE]
> SQL 인증을 사용하여 Azure-SSIS 통합 런타임을 이미 만든 경우 이번에 PowerShell과 함께 Azure AD 인증을 사용하도록 IR를 다시 구성할 수 없습니다.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Azure AD에서 그룹 만들기 및 Data Factory MSI를 그룹의 구성원으로 만들기

기존 Azure AD 그룹을 사용하거나 Azure AD PowerShell을 사용하여 새로 만들 수 있습니다.

1.  [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 모듈을 설치합니다.

2.  `Connect-AzureAD`를 사용하여 로그인하고, 다음 명령을 실행하여 그룹을 만들고, 변수에 저장합니다.

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    출력은 다음 예제와 같으며 변수의 값을 검사합니다.

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  그룹에 Data Factory MSI를 추가합니다. [Azure Data Factory 서비스 ID](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)를 따라 기본 서비스 ID ID를 가져올 수 있습니다(예: 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc. 이 용도로 서비스 ID 응용 프로그램 ID를 사용하지 마세요).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    또한 나중에 그룹 구성원을 검사할 수도 있습니다.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure SQL Database에서 Azure AD 활성화

Azure SQL Database는 Azure AD 사용자로 데이터베이스 만들기를 지원합니다. 결과적으로, Active Directory 관리자로 Azure AD 사용자를 설정한 다음, Azure AD 사용자를 사용하여 SSMS에 로그인할 수 있습니다. 그런 다음, Azure AD 그룹에 대한 포함된 사용자를 만들어 서버에서 SSIS(SQL Server Integration Services) 카탈로그를 만들도록 IR을 활성화할 수 있습니다.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Azure SQL Database에 대해 Azure AD 인증 활성화

다음 단계를 사용하여 [SQL Database에 대한 Azure AD 인증을 구성](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)할 수 있습니다.

1.  Azure Portal의 왼쪽 탐색 영역에서 **모든 서비스** -> **SQL 서버**를 선택합니다.

2.  Azure AD 인증에 활성화할 SQL Database를 선택합니다.

3.  블레이드의 **설정** 섹션에서 **Active Directory 관리자**를 선택합니다.

4.  명령 모음에서 **관리자 설정**을 선택합니다.

5.  서버 관리자로 만들 Azure AD 사용자 계정을 선택한 다음, **선택**을 선택합니다.

6.  명령 모음에서 **저장**을 선택합니다.

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Azure AD 그룹을 나타내는 데이터베이스에 포함된 사용자를 만듭니다.

이 다음 단계를 위해 [Microsoft SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

1.  SQL Server Management Studio를 시작합니다.

2.  **서버에 연결** 대화 상자의 **서버 이름** 필드에 SQL 서버 이름을 입력합니다.

3.  **인증** 필드에서 **Active Directory - MFA 지원을 통한 유니버설 인증**을 선택합니다. (다른 두 개의 Active Directory 인증 유형을 사용할 수도 있습니다. [SQL Database, Managed Instance에서 Azure Active Directory 인증 구성 및 관리](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)를 참조하세요.)

4.  **사용자 이름** 필드에 서버 관리자로 설정한 Azure AD 계정의 이름(예: testuser@xxxonline.com)을 입력합니다.

5.  **연결**을 선택합니다. 로그인 프로세스를 완료합니다.

6.  **개체 탐색기**에서 **데이터베이스** -> 시스템 데이터베이스 폴더를 확장합니다.

7.  **마스터** 데이터베이스를 마우스 오른쪽으로 선택하고 **새 쿼리**를 선택합니다.

8.  쿼리 창에서 다음 줄을 입력하고, 도구 모음에서 **실행**을 선택합니다.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    명령이 성공적으로 완료되고 그룹에 대한 포함된 사용자가 만들어집니다.

9.  쿼리 창을 지우고, 다음 줄을 입력하고, 도구 모음에서 **실행**을 선택합니다.

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    명령이 성공적으로 완료되고 포함된 사용자에게 데이터베이스를 만들 수 있는 기능이 부여됩니다.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에서 Azure AD 활성화

Azure SQL Database Managed Instance는 AD 관리자 이외의 Azure AD 사용자로 데이터베이스 만들기를 지원하지 않습니다. 결과적으로, Active Directory 관리자로 Azure AD 그룹을 설정해야 합니다. 포함된 사용자를 만들 필요가 없습니다.

다음 단계를 사용하여 [SQL Database Managed Instance 서버에 대한 Azure AD 인증을 구성](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)할 수 있습니다.

7.  Azure Portal의 왼쪽 탐색 영역에서 **모든 서비스** -> **SQL 서버**를 선택합니다.

8.  Azure AD 인증에 활성화할 SQL 서버를 선택합니다.

9.  블레이드의 **설정** 섹션에서 **Active Directory 관리자**를 선택합니다.

10. 명령 모음에서 **관리자 설정**을 선택합니다.

11. Azure AD 그룹(예: SSISIrGroup)을 검색 및 선택하고, **선택**을 선택합니다.

12. 명령 모음에서 **저장**을 선택합니다.

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>포털에서 Azure-SSIS IR 프로비전

Azure Portal을 사용하여 Azure-SSIS IR을 프로비전하는 경우 **SQL 설정** 페이지에서 "ADF MSI로 AAD 인증 사용" 옵션을 선택합니다. (다음 스크린샷은 Azure SQL Database로 IR에 대한 설정을 표시합니다. Managed Instance로 IR의 경우 "카탈로그 데이터베이스 서비스 계층" 속성은 사용할 수 있습니다. 다른 설정은 동일합니다.)

Azure-SSIS 통합 런타임을 만드는 방법에 대한 자세한 정보는 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

![Azure-SSIS 통합 런타임에 대한 설정](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>PowerShell을 사용하여 Azure-SSIS IR 프로비전

PowerShell을 사용하여 Azure-SSIS IR을 프로비전하려면 다음 작업을 수행합니다.

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 모듈을 설치합니다.

2.  스크립트에서 *CatalogAdminCredential* 매개 변수를 설정하지 마십시오. 예: 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Type Managed `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -Description $AzureSSISDescription `
                                               -Edition $AzureSSISEdition `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
