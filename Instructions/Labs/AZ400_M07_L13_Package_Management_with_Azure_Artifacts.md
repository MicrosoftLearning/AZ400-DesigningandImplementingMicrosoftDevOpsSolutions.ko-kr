---
lab:
  title: Azure Artifacts로 패키지 관리
  module: 'Module 07: Design and implement a dependency management strategy'
---

# Azure Artifacts로 패키지 관리

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [AZ-400 랩 필수 구성 요소](https://microsoftlearning.github.io/AZ400-DesigningandImplementingMicrosoftDevOpsSolutions/Instructions/Labs/AZ400_M00_Validate_lab_environment.html)에서 제공하는 지침에 따라 조직을 만듭니다.

- **eShopOnWeb 프로젝트 샘플 설정:** 이 랩에 사용할 수 있는 eShopOnWeb 프로젝트 샘플이 아직 없으면 [AZ-400 랩 필수 구성 요소](https://microsoftlearning.github.io/AZ400-DesigningandImplementingMicrosoftDevOpsSolutions/Instructions/Labs/AZ400_M00_Validate_lab_environment.html)에서 제공하는 지침에 따라 샘플을 만듭니다.

- [Visual Studio 다운로드 페이지](https://visualstudio.microsoft.com/downloads/)에서 제공되는 Visual Studio 2022 Community Edition. Visual Studio 2022 설치에는 **ASP<nolink>.NET 및 웹 개발**, **Azure 개발** 및 **.NET Core 플랫폼 간 개발** 워크로드가 포함되어야 합니다.

- **.NET Core SDK:** [.NET Core SDK(2.1.400+) 다운로드 및 설치](https://go.microsoft.com/fwlink/?linkid=2103972)

- **Azure Artifacts 자격 증명 공급자:** [자격 증명 공급자를 다운로드하여 설치합니다](https://go.microsoft.com/fwlink/?linkid=2099625).

## 랩 개요

Azure Artifacts를 활용하면 Azure DevOps에서 NuGet, npm 및 Maven 패키지를 쉽게 검색, 설치 및 게시할 수 있습니다. Azure Artifacts는 Build 등의 기타 Azure DevOps 기능과 긴밀하게 통합되므로 기존 워크플로에서 패키지 관리를 원활하게 진행할 수 있습니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- 피드 만들기 및 피드에 연결
- NuGet 패키지 만들기 및 게시
- NuGet 패키지 가져오기
- NuGet 패키지 업데이트

## 예상 소요 시간: 35분

## 지침

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩 필수 구성 요소를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 다른 필드는 기본값으로 유지합니다. **만들기**를 클릭합니다.

   ![새 프로젝트 만들기 패널의 스크린샷.](images/create-project.png)

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **리포지토리 가져오기**를 클릭합니다. **가져오기**를 선택합니다. **Git 리포지토리 가져오기** 창에서 다음 URL <https://github.com/MicrosoftLearning/eShopOnWeb.git>을 붙여넣고 **가져오기**를 클릭합니다.

   ![리포지토리 가져오기 패널의 스크린샷.](images/import-repo.png)

1. 리포지토리는 다음과 같은 방식으로 구성됩니다.
   - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
   - **.devcontainer** 폴더 컨테이너 설정을 통해 컨테이너를 사용하여 개발합니다(VS Code 또는 GitHub Codespaces에서 로컬로).
   - **infra** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep 및 ARM 인프라가 포함되어 있습니다.
   - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
   - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 8 웹 사이트가 포함되어 있습니다.

#### 작업 3: (완료된 경우 건너뛰기) 기본(main) 분기를 기본 분기로 설정

1. **Repos > Branches**로 이동합니다.
1. **기본** 분기를 마우스로 가리킨 다음 열 오른쪽에 있는 줄임표를 클릭합니다.
1. **기본 분기로 설정**을 클릭합니다.

#### 작업 4: Visual Studio에서 eShopOnWeb 솔루션 구성

이 작업에서는 랩에서 사용할 수 있도록 Visual Studio를 구성합니다.

1. Azure DevOps 포털에서 **eShopOnWeb** 팀 프로젝트를 보고 있는지 확인합니다.

   > **참고**: `<your-Azure-DevOps-account-name>` 자리 표시자가 Azure DevOps 조직 이름을 나타내는 [https://dev.azure.com/`<your-Azure-DevOps-account-name>`/eShopOnWeb](https://dev.azure.com/`<your-Azure-DevOps-account-name>`/eShopOnWeb) URL로 이동하여 프로젝트 페이지에 직접 액세스할 수 있습니다.

1. **eShopOnWeb** 창의 왼쪽에 있는 세로 메뉴에서 **Repos**를 클릭합니다.
1. **파일** 창에서 **복제**를 클릭하고, **VS Code에서 복제** 옆에 있는 드롭다운 화살표를 선택한 다음, 드롭다운 메뉴에서 **Visual Studio**를 선택합니다.
1. 계속 진행할지를 묻는 메시지가 표시되면 **열기**를 클릭합니다.
1. 메시지가 표시되면 Azure DevOps 조직을 설정하는 데 사용한 사용자 계정으로 로그인합니다.
1. Visual Studio 인터페이스 내의 **Azure DevOps** 팝업 창에서 기본 로컬 경로(C:\eShopOnWeb)를 그대로 적용하고 **복제**를 클릭합니다. 그러면 프로젝트를 Visual Studio로 자동으로 가져옵니다.
1. Visual Studio 창은 랩에서 사용할 수 있도록 열어 둡니다.

### 연습 1: Azure Artifacts 사용

이 연습에서는 다음 단계를 수행하여 Azure Artifacts 사용 방법을 알아봅니다.

- 피드 만들기 및 피드에 연결
- NuGet 패키지 만들기 및 게시
- NuGet 패키지 가져오기
- NuGet 패키지 업데이트

#### 작업 1: 피드 만들기 및 피드에 연결

이 작업에서는 피드를 만들어 해당 피드에 연결합니다.

1. Azure DevOps 포털의 프로젝트 설정이 표시된 웹 브라우저 창의 세로 탐색 창에서 **Artifacts**를 클릭합니다.
1. **Artifacts** 허브를 표시한 상태에서 창 위쪽의 **피드 + 만들기**를 클릭합니다.

   > **참고**: 조직 내의 사용자에게 제공되는 NuGet 패키지 컬렉션인 이 피드는 공용 NuGet 피드와 함께 피어로 저장됩니다. 이 랩에서는 Azure Artifacts 사용을 위한 워크플로를 중점적으로 진행하므로 아키텍처 및 개발과 관련된 실제 결정 사항은 설명을 위해서만 제시되는 것입니다. 이 피드에는 조직의 여러 프로젝트에서 공유할 수 있는 공통 기능이 포함됩니다.

1. **새 피드 만들기** 창의 **이름** 텍스트 상자에 **`eShopOnWebShared`** 를(을) 입력합니다. **표시 유형** 섹션에서 특정 사용자를 선택하고 **범위** 섹션에서 **Project:eShopOnWeb** 옵션을 선택하고 다른 설정을 기본값으로 두고 **만들기**를 클릭합니다.

   > **참고**: 이 NuGet 피드에 연결하려는 모든 사용자는 환경을 구성해야 합니다.

1. **Artifacts** 허브로 돌아와서 **피드에 연결**을 클릭합니다.
1. **피드에 연결** 창의 **NuGet** 섹션에서 **Visual Studio**를 선택하고 **Visual Studio** 창에서 **소스** URL을 복사합니다. `https://pkgs.dev.azure.com/Azure-DevOps-Org-Name/_packaging/eShopOnWebShared/nuget/v3/index.json`
1. **Visual Studio** 창으로 다시 전환합니다.
1. Visual Studio 창에서 **도구** 메뉴 머리글을 클릭하고, 드롭다운 메뉴에서 **NuGet 패키지 관리자**를 선택합니다. 그런 다음 계단식 메뉴에서 **패키지 관리자 설정**을 선택합니다.
1. **옵션** 대화 상자에서 **패키지 원본**을 클릭하고 더하기 기호를 클릭하여 새 패키지 원본을 추가합니다.
1. 대화 상자 아래쪽의 **이름** 텍스트 상자에 있는 **패키지 원본**를 **eShopOnWebShared**로 바꾸고 **원본** 텍스트 상자에 Azure DevOps 포털에서 복사한 URL을 붙여넣습니다.
1. **업데이트**와 **확인**을 차례로 클릭하여 추가를 완료합니다.

   > **참고**: 이제 Visual Studio가 새 피드에 연결되었습니다.

#### 작업 2: 사내에서 개발한 NuGet 패키지 만들기 및 게시

이 작업에서는 사내에서 개발한 사용자 지정 NuGet 패키지를 만들고 게시합니다.

1. 새 패키지 원본을 구성하는 데 사용한 Visual Studio 창의 기본 메뉴에서 **파일**을 클릭하고 드롭다운 메뉴에서 **새로 만들기**를 클릭한 다음 계단식 메뉴에서 **프로젝트**를 클릭합니다.

   > **참고**: 여기서는 NuGet 패키지로 게시되는 공유 어셈블리를 만듭니다. 이렇게 하면 다른 팀이 프로젝트 소스를 직접 사용하지 않고도 해당 패키지를 통합하여 최신 정보를 파악할 수 있습니다.

1. **새 프로젝트 만들기** 창의 **최근 프로젝트 템플릿** 페이지에서 검색 텍스트 상자를 사용하여 **클래스 라이브러리** 템플릿을 찾고, C#용 템플릿을 선택하고, **다음**을 클릭합니다.
1. **새 프로젝트 만들기** 창의 **클래스 라이브러리** 페이지에서 다음 설정을 지정하고 **만들기**를 클릭합니다.

   | 설정       | 값                    |
   | ------------- | ------------------------ |
   | 프로젝트 이름  | **eShopOnWeb.Shared**    |
   | 위치      | 기본값 수락 |
   | 솔루션      | **새 솔루션 만들기**  |
   | 솔루션 이름 | **eShopOnWeb.Shared**    |

   **동일한 디렉터리에 솔루션 및 프로젝트 배치** 확인란을 선택합니다.

1. 다음을 클릭합니다. **.NET 8**을 프레임워크 옵션으로 수락합니다.
1. **만들기** 단추를 눌러 프로젝트 만들기를 확인합니다.
1. Visual Studio 인터페이스 내의 **솔루션 탐색기** 창에서 **Class1.cs**를 마우스 오른쪽 단추로 클릭한 다음 오른쪽 클릭 메뉴에서 **삭제**를 선택합니다. 삭제를 확인하라는 메시지가 표시되면 **확인**을 클릭합니다.
1. **Ctrl+Shift+B**를 누르거나 **EShopOnWeb.Shared 프로젝트를 마우스 오른쪽 단추로 클릭**하고 **빌드**를 선택하여 프로젝트를 빌드합니다.
1. 랩 워크스테이션에서 시작 메뉴를 열고 **Windows PowerShell**을 검색합니다. 다음으로, 계단식 메뉴에서 **관리자 권한으로 Windows PowerShell 열기**를 클릭합니다.
1. 
          **관리자: Windows PowerShell** 창에서 다음 명령을 실행하여 eShopOnWeb.Shared 폴더로 이동합니다.

   ```powershell
   cd c:\eShopOnWeb\eShopOnWeb.Shared
   ```

   > **참고**: **eShopOnWeb.Shared** 폴더는 **eShopOnWeb.Shared.csproj** 파일의 위치입니다. 다른 위치 또는 프로젝트 이름을 선택한 경우 대신 해당 위치로 이동합니다.

1. 다음을 실행하여 프로젝트에서 **.nupkg** 파일을 만듭니다(`XXXXXX` 자리 표시자 값을 고유한 문자열로 변경).

   ```powershell
   dotnet pack .\eShopOnWeb.Shared.csproj -p:PackageId=eShopOnWeb-XXXXX.Shared
   ```

   > **참고**: **dotnet pack** 명령은 프로젝트를 빌드하고 **bin\Release** 폴더에 NuGet 패키지를 만듭니다. **릴리스** 폴더가 없는 경우 **디버그** 폴더를 대신 사용할 수 있습니다.

   > **참고**: **관리자: Windows PowerShell** 창에 표시되는 경고는 무시하세요.

   > **참고**: dotnet pack은 프로젝트에서 확인 가능한 정보를 토대로 최소 패키지를 빌드합니다. 인수 `-p:PackageId=eShopOnWeb-XXXXXX.Shared` 를 사용하면 프로젝트에 포함된 이름을 사용하는 대신 특정 이름으로 패키지를 만들 수 있습니다. 예를 들어 `12345` 문자열을 `XXXXXX` 자리 표시자로 대체하는 경우 패키지 이름은 **eShopOnWeb-12345.Shared.1.0.0.nupkg**가 됩니다. 버전 번호가 어셈블리에서 검색되었습니다.

1. PowerShell 창에서 다음 명령을 실행하여 **bin\Release** 폴더를 엽니다.

   ```powershell
   cd .\bin\Release
   ```

1. 다음을 실행하여 **eShopOnWebShared** 피드에 패키지를 게시합니다. 원본을 Visual Studio **원본** URL에서 이전에 복사한 URL로 바꿉니다`https://pkgs.dev.azure.com/Azure-DevOps-Org-Name/_packaging/eShopOnWebShared/nuget/v3/index.json`

   ```powershell
   dotnet nuget push --source "https://pkgs.dev.azure.com/Azure-DevOps-Org-Name/_packaging/eShopOnWebShared/nuget/v3/index.json" --api-key az "eShopOnWeb.Shared.1.0.0.nupkg"
   ```

   > **중요**: Azure DevOps를 사용하여 인증할 수 있도록 운영 체제에 대한 자격 증명 공급자를 설치해야 합니다. [Azure Artifacts 자격 증명 공급자](https://go.microsoft.com/fwlink/?linkid=2099625)에서 설치 지침을 찾을 수 있습니다. Powershell 창에서 다음 명령을 실행하여 새 리소스 그룹을 확인합니다. `iex "& { $(irm https://aka.ms/install-artifacts-credprovider.ps1) } -AddNetfx"` 

   > **참고**: 여기서 **API 키**를 입력해야 합니다. 비어 있지 않은 아무 문자열이나 입력하면 됩니다. 여기서는 **az**를 사용합니다. 메시지가 표시되면 Azure DevOps 조직에 로그인합니다.

   > **참고**: 프롬프트가 표시되지 않거나 **경고가 표시되는 경우: 플러그 인 자격 증명 공급자가 자격 증명을 획득할 수 없습니다. 인증에는 수동 작업이 필요할 수 있습니다. --interactive for `dotnet`, /p:NuGetInteractive=true for MSBuild로 명령을 다시 실행하거나 -NonInteractive switch for NuGet**을 제거하여 명령에 **--interactive** 매개 변수를 추가할 수 있습니다.

1. 성공적인 패키지 푸시 작업이 확인되기를 기다립니다.
1. Azure DevOps 포털이 표시된 웹 브라우저 창으로 전환하여 세로 탐색 창에서 **Artifacts**를 선택합니다.
1. **Artifacts** 허브 창 왼쪽 위 모서리 있는 드롭다운 목록을 클릭하고 피드 목록에서 **eShopOnWebShared** 항목을 선택합니다.

   > **참고**: **eShopOnWebShared** 피드에는 새로 게시한 NuGet 패키지가 포함됩니다.

1. NuGet 패키지를 클릭하여 세부 정보를 표시합니다.

#### 작업 3: Azure DevOps 패키지 피드로 오픈 소스 NuGet 패키지 가져오기

고유한 패키지를 개발하는 것 외에도 오픈 소스 NuGet(<https://www.nuget.org>) DotNet 패키지 라이브러리를 사용하는 것이 좋습니다. 수백만 개의 패키지를 사용할 수 있으므로 애플리케이션에 도움이 되는 것이 항상 있습니다.

이 작업에서는 제네릭 "Newtonsoft.Json" 샘플 패키지를 사용하지만 라이브러리의 다른 패키지에 동일한 방법을 사용할 수 있습니다.

1. 이전 작업에서 새 패키지 푸시에 사용한 동일한 PowerShell 창에서 **eShopOnWeb.Shared** 폴더(`cd..`)로 이동하고 다음 **dotnet** 명령을 실행하여 샘플 패키지를 설치합니다.

   ```powershell
   dotnet add package Newtonsoft.Json
   ```

1. 설치 프로세스의 출력을 확인합니다. 패키지 다운로드를 시도하는 다양한 피드를 보여줍니다.

   ```powershell
   Feeds used:
     https://api.nuget.org/v3/registration5-gz-semver2/newtonsoft.json/index.json
     https://pkgs.dev.azure.com/<AZURE_DEVOPS_ORGANIZATION>/eShopOnWeb/_packaging/eShopOnWebShared/nuget/v3/index.json
   ```

1. 다음으로, 실제 설치 프로세스 자체에 대한 추가 출력을 표시합니다.

   ```powershell
   Determining projects to restore...
   Writing C:\Users\AppData\Local\Temp\tmpxnq5ql.tmp
   info : X.509 certificate chain validation will use the default trust store selected by .NET for code signing.
   info : X.509 certificate chain validation will use the default trust store selected by .NET for timestamping.
   info : Adding PackageReference for package 'Newtonsoft.Json' into project 'c:\eShopOnWeb\eShopOnWeb.Shared\eShopOnWeb.Shared.csproj'.
   info :   GET https://api.nuget.org/v3/registration5-gz-semver2/newtonsoft.json/index.json
   info :   OK https://api.nuget.org/v3/registration5-gz-semver2/newtonsoft.json/index.json 124ms
   info : Restoring packages for c:\eShopOnWeb\eShopOnWeb.Shared\eShopOnWeb.Shared.csproj...
   info :   GET https://api.nuget.org/v3/vulnerabilities/index.json
   info :   OK https://api.nuget.org/v3/vulnerabilities/index.json 84ms
   info :   GET https://api.nuget.org/v3-vulnerabilities/2024.02.15.23.23.24/vulnerability.base.json
   info :   GET https://api.nuget.org/v3-vulnerabilities/2024.02.15.23.23.24/2024.02.17.11.23.35/vulnerability.update.json
   info :   OK https://api.nuget.org/v3-vulnerabilities/2024.02.15.23.23.24/vulnerability.base.json 14ms
   info :   OK https://api.nuget.org/v3-vulnerabilities/2024.02.15.23.23.24/2024.02.17.11.23.35/vulnerability.update.json 30ms
   info : Package 'Newtonsoft.Json' is compatible with all the specified frameworks in project 'c:\eShopOnWeb\eShopOnWeb.Shared\eShopOnWeb.Shared.csproj'.
   info : PackageReference for package 'Newtonsoft.Json' version '13.0.3' added to file 'c:\eShopOnWeb\eShopOnWeb.Shared\eShopOnWeb.Shared.csproj'.
   info : Writing assets file to disk. Path: c:\eShopOnWeb\eShopOnWeb.Shared\obj\project.assets.json
   log  : Restored c:\eShopOnWeb\eShopOnWeb.Shared\eShopOnWeb.Shared.csproj (in 294 ms).
   ```

1. Newtonsoft.Json 패키지는 패키지에 **Newtonsoft.Json**으로 설치되었습니다. Visual Studio **솔루션 탐색기**에서 **eShopOnWeb.Shared** 프로젝트로 이동하여 종속성을 확장하고 패키지 아래에 있는 **Newtonsoft.Json**을 확인합니다. 패키지 왼쪽의 작은 화살표를 클릭하여 폴더 및 파일 목록을 엽니다.

Azure DevOps Artifacts 패키지 피드를 만들 때 패키지가 호스트된 곳에서 Newtonsoft.Json 패키지를 가져오는 dotnet 예제의 nuget.org 같은 **업스트림 소스**를 의도적으로 허용합니다. 이는 패키지의 중복을 방지하고 최신 버전이 항상 사용되는지 확인하는 일반적인 방법입니다.

1. Azure DevOps 포털에서 Artifacts 패키지 피드 페이지를 **새로 고칩니다**. 패키지 목록에는 **eShopOnWeb.Shared** 사용자 지정 개발 패키지와 **Newtonsoft.Json** 공용 공급 패키지가 모두 표시됩니다.
1. Visual Studio **eShopOnWeb.Shared** 솔루션에서 **eShopOnWeb.Shared** 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **NuGet 패키지 관리**를 선택합니다.
1. NuGet 패키지 관리자 창에서 **패키지 원본**이 **eShopOnWebShared**로 설정되어 있는지 확인합니다.
1. **찾아보기**를 클릭하고 NuGet 패키지 목록이 로드되기를 기다립니다.
1. 또한 이 목록에는 **eShopOnWeb.Shared** 사용자 지정 개발 패키지와 **Newtonsoft.Json** 공용 공급 패키지가 모두 표시됩니다.

## 검토

이 랩에서는 다음 단계를 수행하여 Azure Artifacts 사용 방법을 알아보았습니다.

- 피드 만들기 및 피드에 연결
- NuGet 패키지 만들기 및 게시
- 사용자 지정 개발 NuGet 패키지를 가져왔습니다.
