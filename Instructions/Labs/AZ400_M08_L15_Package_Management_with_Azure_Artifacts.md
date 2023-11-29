---
lab:
  title: Azure Artifacts로 패키지 관리
  module: 'Module 08: Design and implement a dependency management strategy'
---

# Azure Artifacts로 패키지 관리

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없는 경우 AZ-400 랩 필수 구성 요소에서 [사용할 수 있는 지침에 따라 만듭니다](https://microsoftlearning.github.io/AZ400-DesigningandImplementingMicrosoftDevOpsSolutions/Instructions/Labs/AZ400_M00_Validate_lab_environment.html).

- **샘플 EShopOnWeb 프로젝트 설정:** 이 랩에 사용할 수 있는 샘플 EShopOnWeb 프로젝트가 아직 없는 경우 AZ-400 랩 필수 구성 요소에서 [사용할 수 있는 지침에 따라 만듭니다](https://microsoftlearning.github.io/AZ400-DesigningandImplementingMicrosoftDevOpsSolutions/Instructions/Labs/AZ400_M00_Validate_lab_environment.html).

- [Visual Studio 다운로드 페이지](https://visualstudio.microsoft.com/downloads/)에서 제공되는 Visual Studio 2022 Community Edition. Visual Studio 2022 설치에는 **ASP<nolink>.NET 및 웹 개발**, **Azure 개발** 및 **.NET Core 플랫폼 간 개발** 워크로드가 포함되어야 합니다.

## 랩 개요

Azure Artifacts를 활용하면 Azure DevOps에서 NuGet, npm 및 Maven 패키지를 쉽게 검색, 설치 및 게시할 수 있습니다. Azure Artifacts는 Build 등의 기타 Azure DevOps 기능과 긴밀하게 통합되므로 기존 워크플로에서 패키지 관리를 원활하게 진행할 수 있습니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- 피드 만들기 및 피드에 연결
- NuGet 패키지 만들기 및 게시
- NuGet 패키지 가져오기
- NuGet 패키지 업데이트

## 예상 소요 시간: 40분

## 지침

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩 필수 구성 요소의 유효성을 검사하고, Azure DevOps 조직을 모두 준비하고, EShopOnWeb 프로젝트를 만든 것에 대해 알려 줍니다. 자세한 내용은 위의 지침을 참조하세요.

#### 작업 1: Visual Studio에서 EShopOnWeb 솔루션 구성

이 작업에서는 랩을 준비하도록 Visual Studio를 구성합니다.

1. Azure DevOps 포털에서 **EShopOnWeb** 팀 프로젝트를 보고 있는지 확인합니다.

    > **참고**: 자리 표시자가 Azure DevOps 조직 이름을 나타내는 /EShopOnWeb[ URL `<your-Azure-DevOps-account-name>` 로 이동하여 ](https://dev.azure.com/`<your-Azure-DevOps-account-name>`/EShopOnWeb)https://dev.azure.com/`<your-Azure-DevOps-account-name>`프로젝트 페이지에 직접 액세스할 수 있습니다.

2. EShopOnWeb** 창의 왼쪽에 있는 **세로 메뉴에서 Repos**를 클릭합니다**.
3. **파일** 창에서 **복제**를 클릭하고, **VS Code에서 복제** 옆에 있는 드롭다운 화살표를 선택한 다음, 드롭다운 메뉴에서 **Visual Studio**를 선택합니다.
4. 계속할지 여부를 묻는 메시지가 표시되면 [열기 **]를 클릭합니다**.
5. 메시지가 표시되면 Azure DevOps 조직을 설정하는 데 사용한 사용자 계정으로 로그인합니다.
6. Visual Studio 인터페이스 내의 Azure DevOps** 팝업 창에서 **기본 로컬 경로(C:\EShopOnWeb)를 수락하고 복제**를 클릭합니다**. 그러면 프로젝트가 Visual Studio로 자동으로 가져옵니다.
7. 랩에서 사용할 수 있는 Visual Studio 창을 열어 둡니다.

### 연습 1: Azure Artifacts 작업

이 연습에서는 다음 단계를 사용하여 Azure Artifacts를 사용하는 방법을 알아봅니다.

- 피드 만들기 및 피드에 연결
- NuGet 패키지 만들기 및 게시
- NuGet 패키지 가져오기
- NuGet 패키지 업데이트

#### 작업 1: 피드 만들기 및 연결

이 작업에서는 피드를 만들고 연결합니다.

1. Azure DevOps 포털의 프로젝트 설정을 표시하는 웹 브라우저 창의 세로 탐색 창에서 아티팩트(Artifacts **)를 선택합니다**.
2. 아티팩트 허브가 **** 표시되면 창 위쪽에서 + 피드** 만들기를 클릭합니다**.

    > **참고**: 이 피드는 조직 내 사용자가 사용할 수 있는 NuGet 패키지의 컬렉션이며 공용 NuGet 피드와 함께 피어로 제공됩니다. 이 랩의 시나리오는 Azure Artifacts를 사용하기 위한 워크플로에 중점을 두므로 실제 아키텍처 및 개발 결정은 순수하게 설명됩니다.  이 피드에는 이 조직의 프로젝트에서 공유할 수 있는 일반적인 기능이 포함됩니다.

3. 새 피드** 만들기 창의 ****이름** 텍스트 상자에 EShopOnWebShared****를 입력**하고 범위** 섹션에서 조직** 옵션을 선택하고 **기본값으로 다른 설정을 그대로 두고 만들기**를 클릭합니다.**

    > **참고**: 이 NuGet 피드에 연결하려는 모든 사용자는 해당 환경을 구성해야 합니다.

4. 아티팩트 허브로 **돌아가서 피드**할 커넥트 클릭합니다**.**
5. **피드**할 커넥트 창의 **NuGet** 섹션에서 Visual Studio**를 선택하고 **Visual Studio** 창에서 **원본** URL을 **복사합니다. (https://pkgs.dev.azure.com/<Azure-DevOps-Org-Name>_packaging/EShopOnWebShared/nuget/v3/index.json)
6. Visual Studio** 창으로 **다시 전환합니다.
7. Visual Studio 창에서 도구 메뉴 머리글을 클릭하고 **드롭다운 메뉴에서 NuGet 패키지 관리자** 선택하고 **계단식 메뉴에서 패키지 관리자 설정** 선택합니다**.**
8. **옵션** 대화 상자에서 패키지 원본을** 클릭하고 **더하기 기호를 클릭하여 새 패키지 원본을 추가합니다.
9. 대화 상자 아래쪽의 이름** 텍스트 상자에서 **패키지 원본**을 EShopOnWebShared**로 **바꾸고 **원본 **** 텍스트 상자에 복사한 URL을 Azure DevOps 포털에 붙여넣습니다.
10. 업데이트를** 클릭한 **다음 확인을** 클릭하여 **추가를 완료합니다.

    > **참고**: 이제 Visual Studio가 새 피드에 연결되었습니다.

#### 작업 2: 사내에서 개발한 NuGet 패키지 만들기 및 게시

이 작업에서는 사내에서 개발한 사용자 지정 NuGet 패키지를 만들고 게시합니다.

1. 새 패키지 원본을 구성하는 데 사용한 Visual Studio 창의 기본 메뉴에서 파일을** 클릭하고 **드롭다운 메뉴에서 새로** 만들기를 클릭한 **다음 계단식 메뉴에서 프로젝트를** 클릭합니다**.

    > **참고**: 이제 다른 팀이 프로젝트 원본과 직접 작업하지 않고도 통합하고 최신 상태를 유지할 수 있도록 NuGet 패키지로 게시될 공유 어셈블리를 만듭니다.

2. **새 프로젝트 만들기 창의 **최근 프로젝트** 템플릿** 페이지에서 검색 텍스트 상자를 사용하여 클래스 라이브러리** 템플릿을 찾고**, C#에 대한 템플릿을 선택하고, 다음**을 클릭합니다**.
3. **새 프로젝트** 만들기 창의 **클래스 라이브러리** 페이지에서 다음 설정을 지정하고 만들기**를 클릭합니다**.

    | 설정 | 값 |
    | --- | --- |
    | 프로젝트 이름 | **EShopOnWeb.Shared** |
    | 위치 | 기본값 수락 |
    | 솔루션 | **새 솔루션 만들기** |
    | 솔루션 이름 | **EShopOnWeb.Shared** |

    동일한 디렉터리에** 솔루션 및 프로젝트 배치 설정을 **사용하도록 설정합니다.

4. 다음을 클릭합니다. .NET 6.0(장기 지원)**을 프레임워크 옵션으로 수락**합니다.
5. 만들기 단추를 눌러 프로젝트 만들기를 **확인** 합니다.
6. Visual Studio 인터페이스의 솔루션 탐색기 창에서 **Class1.cs**를 마우스 오른쪽 단추로 클릭하고 **마우스 오른쪽 단추로 클릭 메뉴에서 삭제**를 선택한 **다음 확인 메시지가 표시되면 [확인 **]을 클릭합니다**.**
7. Ctrl+Shift+B**를 누르**거나 **EShopOnWeb.Shared 프로젝트를** 마우스 오른쪽 단추로 클릭하고 빌드**를 선택하여 **프로젝트를 빌드합니다.

    > **참고**: 다음 작업에서는 NuGet.exe**를 사용하여 **빌드된 프로젝트에서 직접 NuGet 패키지를 생성하지만 프로젝트를 먼저 빌드해야 합니다.

8. Azure DevOps 포털을 표시하는 웹 브라우저로 전환합니다.
9. 피드 창의 **커넥트 **** 이동하여 NuGet 섹션에서 NuGet.exe****를 선택합니다**. 그러면 NuGet.exe** 창이 **표시됩니다.
10. NuGet.exe** 창에서 도구** 가져오기를 클릭합니다**.**
11. **도구** 가져오기 창에서 최신 NuGet** 다운로드 링크를 클릭합니다**. 그러면 사용 가능한 NuGet 배포 버전** 페이지를 표시하는 **다른 브라우저 탭이 자동으로 열립니다.
12. **사용 가능한 NuGet 배포 버전** 페이지에서 nuget.exe - 권장되는 최신 v6.x**를 선택하고 **실행 파일을 로컬 **EShopOnWeb.Shared 프로젝트** 폴더에 다운로드합니다(기본 폴더 위치를 유지한 경우 C:\EShopOnWeb\EShopOnWeb.Shared여야 합니다).
13. **nuget.exe** 파일을 선택하고 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 속성을 선택하여 **해당 속성을** 엽니다.
14. 속성 컨텍스트 창의 일반 탭에서 **보안 섹션 아래의 차단** 해제를 선택합니다**.** 적용 및 **확인을 눌러 **확인**합니다**.
15. 랩 워크스테이션에서 시작 메뉴 열고 Windows PowerShell**을 **검색합니다. 다음으로, 계단식 메뉴에서 관리자** 권한으로 Windows PowerShell 열기를 클릭합니다**.
16. **관리istrator: Windows PowerShell** 창에서 다음 명령을 실행하여 EShopOnWeb.Shared 폴더로 이동합니다.

    ```text
    cd c:\EShopOnWeb\EShopOnWeb.Shared
    ```

    다음을 실행하여 프로젝트에서 .nupkg** 파일을 만듭니**다.

    > **참고**: 배포를 위해 NuGet 비트를 패키징하는 바로 가기입니다. NuGet은 매우 사용자 지정할 수 있습니다. 자세한 내용은 NuGet 패키지 만들기 페이지를 참조 [하세요](https://docs.microsoft.com/nuget/create-packages/overview-and-workflow).

    ```text
    .\nuget.exe pack ./EShopOnWeb.Shared.csproj
    ```

    > **참고**: 관리istrator: Windows PowerShell** 창에 **표시되는 경고를 무시합니다.

    > **참고**: NuGet은 프로젝트에서 식별할 수 있는 정보를 기반으로 최소 패키지를 빌드합니다. 예를 들어 이름은 EShopOnWeb.Shared.1.0.0.nupkg입니다****. 해당 버전 번호가 어셈블리에서 검색되었습니다.

17. 패키지를 성공적으로 만든 후 다음을 실행하여 EShopOnWebShared** 피드에 패키지를 **게시합니다.

    > **참고**: 비어 있지 않은 문자열일 **수 있는 API 키를** 제공해야 합니다. 여기서는 AzDO**를 사용하고 **있습니다. 메시지가 표시되면 Azure DevOps 조직에 로그인합니다.

    ```text
    .\nuget.exe push -source "EShopOnWebShared" -ApiKey AzDO EShopOnWeb.Shared.1.0.0.nupkg
    ```

18. 성공적인 패키지 푸시 작업이 확인되기를 기다립니다.
19. Azure DevOps 포털을 표시하는 웹 브라우저 창으로 전환하고 세로 탐색 창에서 아티팩트(Artifacts **)를 선택합니다**.
20. 아티팩트** 허브 창에서 **왼쪽 위 모서리에 있는 드롭다운 목록을 클릭하고 피드 목록에서 EShopOnWebShared** 항목을 선택합니다**.

    > **참고**: **EShopOnWebShared** 피드에는 새로 게시된 NuGet 패키지가 포함되어야 합니다.

21. NuGet 패키지를 클릭하여 세부 정보를 표시합니다.

#### 작업 3: Azure DevOps 패키지 피드로 오픈 소스 NuGet 패키지 가져오기

고유한 패키지를 개발하는 것 외에도 오픈 소스 NuGet(DotNet 패키지 라이브러리)https://www.nuget.org) 을 사용하지 않는 것은 어떨까요? 몇 백만 개의 패키지를 사용할 수 있는 경우 애플리케이션에 항상 유용한 것이 있습니다.

이 작업에서는 제네릭 "헬로 월드" 샘플 패키지를 사용하지만 라이브러리의 다른 패키지에 대해 동일한 방법을 사용할 수 있습니다.

1. 동일한 PowerShell 창에서 다음 **nuget** 명령을 실행하여 샘플 패키지를 설치합니다.

    ```text
    .\nuget install HelloWorld -ExcludeVersion
    ```

2. 설치 프로세스의 출력을 확인합니다. 첫 번째 줄에는 패키지를 다운로드하려고 시도하는 다른 피드가 표시됩니다.

    ```text
    Feeds used:
      https://api.nuget.org/v3/index.json
      https://pkgs.dev.azure.com/<AZURE_DEVOPS_ORGANIZATION>/eShopOnWeb/_packaging/EShopOnWebPFeed/nuget/v3/index.json
    ```

3. 다음으로, 실제 설치 프로세스 자체에 대한 추가 출력을 표시합니다.

    ```text
    Installing package 'Helloworld' to 'C:\eShopOnWeb\EShopOnWeb.Shared'.
      GET https://api.nuget.org/v3/registration5-gz-semver2/helloworld/index.json
      OK https://api.nuget.org/v3/registration5-gz-semver2/helloworld/index.json 114ms
    MSBuild auto-detection: using msbuild version '17.5.0.10706' from 'C:\Program Files\Microsoft Visual Studio\2022\Professional\MSBuild\Current\bin'.
      GET https://pkgs.dev.azure.com/pdtdemoworld/7dc3351f-bb0c-42ba-b3c9-43dab8e0dc49/_packaging/188ec0d5-ff93-4eb7-b9d3-293fbf759f06/nuget/v3/registrations2-semver2/helloworld/index.json
      OK https://pkgs.dev.azure.com/pdtdemoworld/7dc3351f-bb0c-42ba-b3c9-43dab8e0dc49/_packaging/188ec0d5-ff93-4eb7-b9d3-293fbf759f06/nuget/v3/registrations2-semver2/helloworld/index.json 698ms
    
    Attempting to gather dependency information for package 'Helloworld.1.3.0.17' with respect to project 'C:\eShopOnWeb\EShopOnWeb.Shared', targeting 'Any,Version=v0.0'
    Gathering dependency information took 21 ms
    Attempting to resolve dependencies for package 'Helloworld.1.3.0.17' with DependencyBehavior 'Lowest'
    Resolving dependency information took 0 ms
    Resolving actions to install package 'Helloworld.1.3.0.17'
    Resolved actions to install package 'Helloworld.1.3.0.17'
    Retrieving package 'HelloWorld 1.3.0.17' from 'nuget.org'.
      GET https://api.nuget.org/v3-flatcontainer/helloworld/1.3.0.17/helloworld.1.3.0.17.nupkg
      OK https://api.nuget.org/v3-flatcontainer/helloworld/1.3.0.17/helloworld.1.3.0.17.nupkg 133ms
    Installed HelloWorld 1.3.0.17 from https://api.nuget.org/v3/index.json with content hash 1Pbk5sGihV5JCE5hPLC0DirUypeW8hwSzfhD0x0InqpLRSvTEas7sPCVSylJ/KBzoxbGt2Iapg72WPbEYxLX9g==.
    Adding package 'HelloWorld.1.3.0.17' to folder 'C:\eShopOnWeb\EShopOnWeb.Shared'
    Added package 'HelloWorld.1.3.0.17' to folder 'C:\eShopOnWeb\EShopOnWeb.Shared'
    Successfully installed 'HelloWorld 1.3.0.17' to C:\eShopOnWeb\EShopOnWeb.Shared
    Executing nuget actions took 686 ms
    ```

4. HelloWorld 패키지는 EShopOnWeb.Shared 폴더 아래의 하위 폴더 **HelloWorld**에 설치되었습니다. Visual Studio **솔루션 탐색기** EShopOnWeb.Shared** 프로젝트로 **이동하여 HelloWorld** 하위 폴더를 **확인합니다. 하위 폴더의 왼쪽에 있는 작은 화살표를 클릭하여 폴더 및 파일 목록을 엽니다.
5. 패키지의 원본을 **증명하는 signature.p7s** 서명 파일이 있는 lib** 하위 폴더**를 확인합니다. 다음으로 HelloWorld.nupkg** 패키지 파일 자체를 확인**합니다.

#### 작업 4: Azure Artifacts에 오픈 소스 NuGet 패키지 업로드

이 패키지를 이전에 만든 Azure Artifacts 패키지 피드에 업로드하여 DevOps 팀이 재사용할 수 있는 "승인된" 패키지라고 생각해 보겠습니다.

1. PowerShell 창에서 다음 명령을 실행합니다.

    ```text
    .\nuget.exe push -source "EShopOnWebShared" -ApiKey AzDO c:\EShopOnWeb\EShopOnWeb.Shared\HelloWorld\HelloWorld.nupkg
    ```

    > **참고**: 오류 메시지가 표시됩니다.

    ```text
    Response status code does not indicate success: 409 (Conflict - 'HelloWorld 1.3.0.17' cannot be published to the feed because it exists in at least one of the feed's upstream sources. Publishing this copy would prevent you from using 'HelloWorld 1.3.0.17' from 'NuGet Gallery'. For more information, see https://go.microsoft.com/fwlink/?linkid=864880 (DevOps Activity ID: AE08BE89-C2FA-4FF7-89B7-90805C88972C)).
    ```

Azure DevOps 아티팩트 패키지 피드를 만든 경우 의도적으로 dotnet 예제의 nuget.org 같은 업스트림 원본을 허용**합니다**. 그러나 DevOps 팀이 "내부 전용"** 패키지 피드를 **만드는 것을 차단하는 것은 없습니다.

1. Azure DevOps 포털로 이동하여 아티팩트로** 이동**한 다음 EShopOnWebShared 피드를** 선택합니다**.
2. 업스트림 원본 검색 클릭 ****
3. **업스트림 패키지**로 이동 창에서 NuGet**을 패키지 유형으로 선택하고 **검색 필드에 HelloWorld**를 입력**합니다.
4. 검색** 단추를 눌러 **확인합니다.
5. 이렇게 하면 사용 가능한 다른 버전이 있는 모든 HelloWorld 패키지 목록이 생성됩니다.
6. **왼쪽 화살표 키를** 클릭하여 EShopOnWebShared 피드로** 돌아갑니다**.
7. 톱니바퀴를 클릭하여 피드 설정** 엽니다**. 피드 설정 페이지에서 업스트림 원본을** 선택합니다**.
8. 다양한 개발 언어에 대한 다양한 업스트림 패키지 관리자 확인합니다. 목록에서 NuGet 갤러리**를 선택합니다**. **삭제** 단추를 누른 다음 저장** 단추를 누릅니**다.

9. 이러한 저장된 변경 내용을 사용하면 다음 명령을 다시 실행하여 PowerShell 창에서 NuGet.exe를 사용하여 HelloWorld** 패키지를 업로드**할 수 있습니다.

    ```text
     .\nuget.exe push -source "EShopOnWebShared" -ApiKey AzDO c:\EShopOnWeb\EShopOnWeb.Shared\HelloWorld\HelloWorld.nupkg
    ```

    > **참고**: 이제 업로드에 성공해야 합니다. 

    ```text
    Pushing HelloWorld.nupkg to 'https://pkgs.dev.azure.com/pdtdemoworld/7dc3351f-bb0c-42ba-b3c9-43dab8e0dc49/_packaging/188ec0d5-ff93-4eb7-b9d3-293fbf759f06/nuget/v2/'...
      PUT https://pkgs.dev.azure.com/<AZUREDEVOPSORGANIZATION>/7dc3351f-bb0c-42ba-b3c9-43dab8e0dc49/_packaging/188ec0d5-ff93-4eb7-b9d3-293fbf759f06/nuget/v2/
    MSBuild auto-detection: using msbuild version '17.5.0.10706' from 'C:\Program Files\Microsoft Visual Studio\2022\Professional\MSBuild\Current\bin'.
      Accepted https://pkgs.dev.azure.com/pdtdemoworld<AZUREDEVOPSORGANIZATION>/7dc3351f-bb0c-42ba-b3c9-43dab8e0dc49/_packaging/188ec0d5-ff93-4eb7-b9d3-293fbf759f06/nuget/v2/ 1645ms
    Your package was pushed.
    PS C:\eShopOnWeb\EShopOnWeb.Shared>
    ```

10. Azure DevOps 포털 **에서 아티팩트 패키지 피드 페이지를 새로 고칩니다** . 패키지 목록에는 EShopOnWeb.Shared** 사용자 지정 개발 패키지와 HelloWorld** 공용 공급 패키지가 **모두 **표시됩니다.
11. Visual Studio EShopOnWeb.Shared** Solution에서 EShopOnWeb.Shared** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **상황에 맞는 메뉴에서 NuGet 패키지** 관리를 선택합니다**.**
12. NuGet 패키지 관리자 창에서 패키지 원본**이 EShopOnWebShared**로 **설정되어 있는지 확인**합니다.
13. 찾아보기를** 클릭하고 **NuGet 패키지 목록이 로드되기를 기다립니다.
14. 이 목록에는 EShopOnWeb.Shared** 사용자 지정 개발 패키지와 **HelloWorld** 공용 공급 패키지도 모두 **표시됩니다.

## 검토

이 랩에서는 다음 단계를 사용하여 Azure Artifacts를 사용하는 방법을 알아보았습니다.

- 피드 만들기 및 피드에 연결
- NuGet 패키지 만들기 및 게시
- 사용자 지정 개발 NuGet 패키지를 가져왔습니다.
- 공용 소스 NuGet 패키지를 가져왔습니다.
