---
title: Azure AD Xamarin 시작 | Microsoft Docs
description: 로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 Xamarin 응용 프로그램을 빌드합니다.
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8af6846da78d12460b7866297c9802c5dab20a69
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967526"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>빠른 시작: Microsoft 로그인을 통합하는 Xamarin 앱 빌드

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Xamarin을 사용하면 iOS, Android 및 Windows(모바일 장치 및 PC)에서 실행할 수 있는 Mobile Apps를 C#으로 작성할 수 있습니다. Xamarin을 사용하여 앱을 빌드하는 경우 Azure AD(Azure Active Directory)를 사용하면 간단하게 Azure AD 계정으로 사용자를 인증할 수 있습니다. 또한 앱에서 Office 365 API 또는 Azure API 같은 Azure AD를 통해 보호되는 웹 API를 안전하게 사용할 수 있습니다.

보호된 리소스에 액세스해야 하는 Xamarin 앱의 경우 Azure AD는 ADAL(Active Directory 인증 라이브러리)을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 이 작업이 얼마나 쉬운지 보여 드리기 위해 이 문서에서는 다음 작업을 수행하는 DirectorySearcher 앱을 빌드해 보겠습니다.

* iOS, Android, Windows 데스크톱, Windows Phone 및 Windows 스토어에서 실행됩니다.
* 단일 PCL(이식 가능 클래스 라이브러리)을 사용하여 사용자를 인증하고 Azure AD Graph API에 대한 토큰을 가져옵니다.
* 지정된 UPN을 가진 사용자를 디렉터리에서 검색합니다.

## <a name="prerequisites"></a>필수 조건

* [기본 프로젝트](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip)를 다운로드하거나 [완성된 샘플](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)을 다운로드하세요. 각 다운로드는 Visual Studio 2013 솔루션입니다.
* 또한 사용자를 만들고 앱을 등록할 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](quickstart-create-new-tenant.md).

준비가 완료되면 다음 4가지 섹션의 절차를 수행합니다.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>1단계: Xamarin 개발 환경 설정

이 자습서에는 iOS, Android 및 Windows용 프로젝트가 포함되어 있으므로 Visual Studio와 Xamarin이 둘 다 필요합니다. 필요한 환경을 만들려면 MSDN에서 [Visual Studio 및 Xamarin 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx)의 프로세스를 완료하세요. 이러한 지침에는 설치 관리자가 완료되기를 기다리는 동안 Xamarin에 대해 자세히 알아보기 위해 검토할 수 있는 자료가 포함되어 있습니다.

필요한 설정을 마치면 Visual Studio에서 솔루션을 엽니다. 그러면 5개의 플랫폼별 프로젝트와 모든 플랫폼에서 공유되는 1개의 PCL, DirectorySearcher.cs의 6개 프로젝트를 찾을 수 있습니다.

## <a name="step-2-register-the-directorysearcher-app"></a>2단계: DirectorySearcher 앱 등록

앱에서 토큰을 가져올 수 있도록 먼저 Azure AD 테넌트에 앱을 등록하고 Azure AD Graph API에 액세스할 수 있는 권한을 부여해야 합니다. 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 위쪽 막대에서 계정을 클릭합니다. 그런 다음 **디렉터리** 목록에서 앱을 등록할 Active Directory 테넌트를 선택합니다.
3. 왼쪽 창에서 **모든 서비스**를 클릭한 다음, **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **추가**를 선택합니다.
5. 새 **네이티브 클라이언트 응용 프로그램**을 만들려면 지시를 따릅니다.
  * **이름**은 사용자에게 앱에 대해 설명합니다.
  * **리디렉션 URI**는 Azure AD가 토큰 응답을 반환하는 데 사용하는 구성표 및 문자열의 조합입니다. 값(예: http://DirectorySearcher) 을 입력합니다.
6. 등록이 완료되면 Azure AD가 앱에 고유한 응용 프로그램 ID를 할당합니다. **응용 프로그램** 탭에서 이 값을 복사해 둡니다. 나중에 이 값이 필요합니다.
7. **설정** 페이지에서 **필요한 사용 권한**, **추가**를 차례로 선택합니다.
8. API로 **Microsoft Graph**를 선택합니다. **위임된 권한**에서 **디렉터리 데이터 읽기** 권한을 추가합니다. 이렇게 하면 앱에서 사용자의 Graph API를 쿼리할 수 있습니다.

## <a name="step-3-install-and-configure-adal"></a>3단계: ADAL 설치 및 구성

Azure AD에 앱이 있으므로 ADAL을 설치하고 ID 관련 코드를 작성할 수 있습니다. ADAL이 Azure AD와 통신할 수 있도록 앱 등록에 대한 정보를 제공합니다.

1. 패키지 관리자 콘솔을 사용하여 ADAL을 DirectorySearcher 프로젝트에 추가합니다.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    ADAL의 PCL 부분과 플랫폼별 부분의 두 라이브러리 참조가 각 프로젝트에 추가됩니다.
2. DirectorySearcherLib 프로젝트에서 DirectorySearcher.cs를 엽니다.
3. 클래스 멤버 값을 Azure Portal에서 입력한 값으로 바꿉니다. 코드에서 ADAL을 사용할 때마다 이러한 값을 참조합니다.

  * *테넌트*는 Azure AD 테넌트의 도메인(예: contoso.onmicrosoft.com)입니다.
  * *clientId*는 포털에서 복사한 앱의 클라이언트 ID입니다.
  * *returnUri*는 포털에 입력한 리디렉션 URI(예: http://DirectorySearcher) 입니다.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>4단계: ADAL을 사용하여 Azure AD에서 토큰 가져오기

거의 모든 앱 인증 논리가 `DirectorySearcher.SearchByAlias(...)`에 있습니다. 플랫폼별 프로젝트에서는 `DirectorySearcher` PCL에 컨텍스트 매개 변수를 전달하기만 하면 됩니다.

1. DirectorySearcher.cs를 연 다음 새 매개 변수를 `SearchByAlias(...)` 메서드에 추가합니다. `IPlatformParameters`는 ADAL이 인증을 수행하는 데 필요한 플랫폼별 개체를 캡슐화하는 컨텍스트 매개 변수입니다.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. ADAL의 기본 클래스인 `AuthenticationContext`를 초기화합니다. 이 작업을 수행하면 Azure AD와 통신하는 데 필요한 좌표가 ADAL에 전달됩니다.
3. 그런 다음 `AcquireTokenAsync(...)`를 호출합니다. 이 메서드는 `IPlatformParameters` 개체를 수락하고 앱에 토큰을 반환하는 데 필요한 인증 흐름을 호출합니다.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)`는 먼저 사용자에게 자격 증명을 요구하지 않고(이전 토큰을 캐시하거나 새로 고침) 요청된 리소스(이 경우 Graph API)에 대한 토큰을 반환하려고 합니다. 필요한 경우에만 요청된 토큰을 획득하기 전에 사용자에게 Azure AD 로그인 페이지를 표시합니다.
4. **Authorization** 헤더의 Graph API GET 요청에 액세스 토큰을 연결합니다.

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

`DirectorySearcher` PCL 및 앱의 ID 관련 코드에 대한 작업이 끝났습니다. 이제 각 플랫폼 보기에서 `SearchByAlias(...)` 메서드를 호출하고, 필요한 경우 UI 수명 주기를 올바르게 처리하기 위한 코드를 추가하면 됩니다.

### <a name="android"></a>Android
1. MainActivity.cs의 단추 클릭 처리기에서 `SearchByAlias(...)` 호출을 추가합니다.

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. `OnActivityResult` 수명 주기 메서드를 재정의하여 인증 리디렉션을 해당 메서드로 다시 전달해야 합니다. ADAL은 Android에서 이 작업을 위한 도우미 메서드를 제공합니다.

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows Desktop

MainWindow.xaml.cs에서 데스크톱의 `PlatformParameters` 개체에 `WindowInteropHelper`를 전달하여 `SearchByAlias(...)`를 호출하면 됩니다.

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
DirSearchClient_iOSViewController.cs에서 iOS `PlatformParameters` 개체는 보기 컨트롤러에 대한 참조를 사용합니다.

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows 범용
Windows 유니버설에서 MainPage.xaml.cs를 열고 `Search` 메서드를 구현합니다. 이 메서드는 공유 프로젝트의 도우미 메서드를 사용하여 필요에 따라 UI를 업데이트합니다.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

이제 5개의 서로 다른 플랫폼에서 OAuth 2.0을 사용하여 사용자를 인증하고 안전하게 Web API를 호출할 수 있는 Xamarin 앱이 작성되었습니다.

## <a name="step-5-populate-your-tenant"></a>5단계: 테넌트 채우기 

아직 사용자로 테넌트를 채우지 않은 경우 지금 채울 수 있습니다.

1. DirectorySearcher 앱을 실행하고 해당 사용자 중 하나로 로그인합니다.
2. 해당 UPN에 따라 다른 사용자를 검색합니다.

## <a name="next-steps"></a>다음 단계

ADAL은 앱에 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 대신 처리합니다. 사용자는 단일 API 호출 `authContext.AcquireToken*(…)`만 알고 있으면 됩니다.

* [완성된 샘플](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)(사용자 구성 값 제외)을 다운로드합니다.
* [Azure AD를 사용하여 .NET Web API 보안을 유지](quickstart-v1-dotnet-webapi.md)하는 방법을 알아봅니다.
