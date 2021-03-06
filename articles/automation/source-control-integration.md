---
title: Azure Automation에서 원본 제어 통합
description: 이 문서에서는 Azure Automation에서 GitHub를 사용하는 원본 제어 통합을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2d13a409d095bca64da781e5c5ca58553f9710c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046854"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation에서 원본 제어 통합

원본 제어는 GitHub 또는 Azure Dev Ops 소스 제어 리포지토리의 스크립트로 Automation 계정의 Runbook을 최신 상태로 유지할 수 있게 합니다. 원본 제어를 사용하면 팀과 쉽게 공동 작업하고 변경 내용을 추적하며 이전 버전의 Runbook으로 롤백할 수 있습니다. 예를 들어 원본 제어를 사용하면 개발, 테스트 또는 프로덕션 Automation 계정에 원본 제어의 여러 분기를 동기화할 수 있어 개발 환경에서 테스트된 코드를 프로덕션 Automation 계정 수준으로 쉽게 올릴 수 있습니다.

Azure Automation은 3가지 유형의 소스 제어를 지원합니다.

* GitHub
* Visual Studio Team Services(Git)
* Visual Studio Team Services(TFVC)

## <a name="pre-requisites"></a>필수 조건

* 소스 제어 리포지토리(GitHub 또는 Visual Studio Team Services)
* [실행 계정 및 연결](manage-runas-account.md)

> [!NOTE]
> 소스 제어 동기화 작업은 사용자 Automation 계정에서 실행되며 다른 Automation 작업과 동일한 요금으로 청구됩니다.

## <a name="configure-source-control"></a>소스 제어 구성

Automation 계정 내에서 **소스 제어(미리 보기)** 를 선택하고 **+ 추가**를 클릭합니다.

![소스 제어 선택](./media/source-control-integration/select-source-control.png)

**소스 제어 형식**을 선택하고 **인증**을 클릭합니다.

앱 요청 사용 권한 페이지를 검토하고 **수락**을 클릭합니다.

**소스 제어 요약** 페이지에서 정보를 입력하고 **저장**을 클릭합니다. 다음 표는 사용 가능한 필드에 대한 설명을 보여 줍니다.

|자산  |설명  |
|---------|---------|
|소스 제어 이름     | 소스 제어의 친숙한 이름        |
|소스 제어 형식     | 소스 제어 소스의 형식입니다. 사용 가능한 옵션은 다음과 같습니다.</br> Github</br>Visual Studio Team Services(Git)</br>Visual Studio Team Services(TFVC)        |
|리포지토리     | 리포지토리 또는 프로젝트의 이름입니다. 소스 제어 리포지토리에서 끌어옵니다. 예: $/ContosoFinanceTFVCExample         |
|Branch     | 소스 파일을 끌어올 분기입니다. 분기 대상 지정은 TFVC 소스 제어 형식에는 사용할 수 없습니다.          |
|폴더 경로     | 동기화할 Runbook이 포함된 폴더입니다. 예: /Runbooks         |
|자동 동기화     | 소스 제어 리포지토리에서 커밋이 이루어질 때 자동 동기화를 설정 또는 해제합니다.         |
|Runbook 게시     | **켜짐**으로 설정된 경우 소스 제어에서 Runbook이 동기화되면 자동으로 게시됩니다.         |
|설명     | 추가 정보를 제공하는 텍스트 필드        |

![소스 제어 요약](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>동기화 중

소스 제어 통합을 구성할 때 자동 동기화가 설정된 경우 초기 동기화가 자동으로 시작됩니다. 자동 동기화가 설정되지 않은 경우 **소스 제어(미리 보기)** 페이지의 표에서 소스를 선택합니다. **동기화 시작**을 클릭하여 동기화 프로세스를 시작합니다.  

**동기화 작업** 탭을 클릭하여 현재 동기화 작업 또는 이전 작업의 상태를 볼 수 있습니다. **소스 제어** 드롭다운 목록에서 소스 제어를 선택합니다.

![동기화 상태](./media/source-control-integration/sync-status.png)

작업을 클릭하면 작업 출력을 볼 수 있습니다. 다음은 소스 제어 동기화 작업의 출력에 대한 예입니다.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

## <a name="disconnecting-source-control"></a>원본 제어 연결 끊기

소스 제어 리포지토리에서 연결을 해제하려면 Automation 계정의 **계정 설정**에서 **소스 제어(미리 보기)** 를 엽니다.

제거하려는 소스 제어를 선택합니다. **소스 제어 요약** 페이지에서 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

Runbook 형식, 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)
