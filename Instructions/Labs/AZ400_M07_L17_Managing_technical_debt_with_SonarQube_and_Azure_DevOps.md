---
lab:
  title: SonarCloud 및 Azure DevOps를 사용하여 기술적인 문제 관리
  module: 'Module 07: Implement security and validate code bases for compliance'
---

# <a name="managing-technical-debt-with-sonarcloud-and-azure-devops"></a>SonarCloud 및 Azure DevOps를 사용하여 기술적인 문제 관리

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

## <a name="lab-overview"></a>랩 개요

Azure DevOps 맥락에서 **기술 부채**란 전술 목표 달성을 위해 사용하는 방법이 가장 효율적이지 않다는 의미입니다. 이러한 문제가 발생하면 소프트웨어 개발 및 배포의 전략 목표를 달성하기가 어려워집니다. 기술적인 문제가 발생하면 생산성이 저하될 수밖에 없습니다. 코드를 파악하기가 힘들고 오류 발생 가능성이 높아질 뿐 아니라, 코드 변경 시간이 오래 걸리고 코드의 유효성을 검사하기도 어려워지기 때문입니다. 기술적인 문제는 적절하게 관리 감독하지 않으면 계속 누적될 수 있습니다. 그러면 장기적으로는 소프트웨어의 전반적인 품질과 개발 팀의 생산성도 크게 낮아집니다.

[SonarCloud](https://sonarcloud.io/){:target="\_blank"}는 클라우드 기반 코드 품질 및 보안 서비스입니다. SonarCloud에서 제공하는 주요 기능은 다음과 같습니다.

- Java, JS, C#, C/C++, Objective-C, TypeScript, Python, ABAP, PLSQL, T-SQL 등의 23개 프로그래밍/스크립팅 언어 지원.
- 효율적인 정적 코드 분석기를 기반으로 하여 찾아내기 어려운 버그와 품질 이슈를 추적할 수 있는 수천 가지 규칙 제공.
- Travis, Azure DevOps, BitBucket, AppVeyor 등 널리 사용되고 있는 CI 서비스와의 클라우드 기반 통합 기능
- 분기와 끌어오기 요청의 모든 소스 파일을 살펴볼 수 있는 심층 코드 분석 기능. 이 분석을 수행하면 품질 게이트를 통과하고 빌드의 품질 수준을 높일 수 있습니다.
- 우수한 속도와 확장성

이 랩에서는 Azure DevOps를 SonarCloud와 통합하는 방법을 알아봅니다.

> **참고**: 이 랩을 실행하려면 Azure Pipelines를 실행할 수 있어야 합니다. 2021년 2월에 수행된 퍼블릭 프로젝트의 변경으로 인해 파이프라인에 대한 액세스를 요청해야 합니다. <https://devblogs.microsoft.com/devops/change-in-azure-pipelines-grant-for-public-projects/>

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- SonarCloud와 통합할 Azure DevOps 프로젝트 및 CI 빌드 설정.
- SonarCloud 보고서 분석.
- Azure DevOps 끌어오기 요청 프로세스에 정적 분석 기능 통합.

## <a name="estimated-timing-60-minutes"></a>예상 소요 시간: 60분

## <a name="instructions"></a>Instructions

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [Sonar Scanning Examples 리포지토리](https://github.com/SonarSource/sonar-scanning-examples.git) 기반 팀 프로젝트를 설정합니다.

#### <a name="task-1-create-the-team-project"></a>작업 1: 팀 프로젝트 만들기

이 작업에서는 [Sonar Scanning Examples 리포지토리](https://github.com/SonarSource/sonar-scanning-examples.git) 리포지토리를 기반으로 하여 새 Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고, [**Azure DevOps 포털**](https://dev.azure.com)로 이동하고, Azure DevOps 조직에 로그인합니다.
1. **Azure DevOps 포털**의 오른쪽 위에서 **+ 새 프로젝트**를 클릭합니다.
1. **새 프로젝트 만들기** 창의 프로젝트 이름 텍스트 상자에 **SonarExamples**를 입력하고 표시 유형 섹션에서 공개를 클릭한 다음 **만들기**를 클릭합니다.

    > **참고**: SonarCloud의 유료 요금제에 가입하려는 경우가 아니면 Azure DevOps 프로젝트를 공개로 설정해야 합니다. 유료 요금제에 *가입하려는* 경우에는 비공개 프로젝트를 만들 수 있습니다.

1. **SonarExamples** 창에서 Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에 있는 **Repos**를 클릭하고, **SonarExamples가 비어 있습니다. 일부 코드를 추가하세요!** 창의 **리포지토리 가져오기** 섹션에서 **가져오기**를 클릭합니다.
1. **Git 리포지토리 가져오기** 창에서 **Git**이 **리포지토리 유형** 드롭다운 목록에 표시되는지 확인하고, **복제 URL**에 **<https://github.com/SonarSource/sonar-scanning-examples.git>** 을 입력하고, **가져오기**를 클릭합니다.

    > **참고**: Scanning Examples 리포지토리에는 MSBuild를 사용하는 C#, Java를 사용하는 Maven/Gradle 등 여러 빌드 시스템과 언어용 샘플 프로젝트가 포함되어 있습니다.

#### <a name="task-2-generate-an-azure-devops-personal-access-token"></a>작업 2: Azure DevOps 개인용 액세스 토큰 생성

이 작업에서는 Azure DevOps 개인용 액세스 토큰을 생성합니다. 이 연습의 다음 작업에서 설치할 Postman 앱에서 인증을 할 때 이 토큰을 사용합니다.

1. 랩 컴퓨터의 Azure DevOps 포털이 표시된 웹 브라우저 창에서 Azure DevOps 페이지 오른쪽 위의 **사용자 설정** 아이콘을 클릭합니다. 그런 다음 드롭다운 메뉴에서 **개인용 액세스 토큰**을 클릭하고 **개인용 액세스 토큰** 창에서 **+ 새 토큰**을 클릭합니다.
1. **새 개인용 액세스 토큰 만들기** 창에서 **모든 범위 표시** 링크를 클릭하고 다음 설정을 지정한 후에 **만들기**를 클릭합니다. 다른 값은 모두 기본값으로 유지합니다.

     | 설정 | 값 |
     | --- | --- |
     | Name | **SonarCloud 및 Azure DevOps를 사용하여 기술적인 문제 관리 랩** |
     | 범위 | **사용자 정의** |
     | Scope | **코드** |
     | 사용 권한 | **읽기 및 쓰기** |

1. **성공** 창에서 개인용 액세스 토큰의 값을 클립보드에 붙여넣습니다.

     > **참고**: 토큰의 값을 적어 두세요. 이 창을 닫으면 토큰을 검색할 수 없습니다.

1. **성공** 창에서 **닫기**를 클릭합니다.

#### <a name="task-3-install-and-configure-the-sonarcloud-azure-devops-extension"></a>작업 3: SonarCloud Azure DevOps 확장 설치 및 구성

이 작업에서는 Azure DevOps 프로젝트에서 SonarCloud Azure DevOps 확장을 설치하고 구성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 Visual Studio Marketplace의 [SonarCloud 확장 페이지](https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarcloud)로 이동하여 **Get it free**를 클릭합니다. **Select an Azure Devops organization** 드롭다운 목록에 Azure DevOps 조직 이름이 표시되어 있는지 확인하고 **Install**을 클릭합니다.
1. 설치가 완료되면 **Proceed to organization**을 클릭합니다. 그러면 조직 홈 페이지가 표시된 Azure DevOps 포털로 브라우저가 리디렉션됩니다.

    > **참고**: Marketplace에서 확장을 설치하는 데 적합한 권한이 없으면 설치 승인 요청이 계정 관리자에게 전송됩니다.

    > **참고**: SonarCloud 확장에는 빌드 작업, 빌드 템플릿 및 사용자 지정 대시보드 위젯이 포함되어 있습니다.

1. 웹 브라우저 창에서 **SonarCloud 홈페이지** [https://sonarcloud.io/](https://sonarcloud.io/)로 이동합니다.
1. SonarCloud 홈 페이지에서 **Log in**을 클릭합니다.
1. **Log in or Sign up to SonarCloud**에서 **With Azure DevOps**를 클릭합니다.
1. **Let this app access your info?** 메시지가 표시되면 **Yes**를 클릭합니다. 메시지가 표시되면 **조직 대신 동의** 및 **동의**를 선택합니다.

    > **참고**: SonarCloud에서는 조직을 만든 후 해당 조직 내에 새 프로젝트를 만듭니다. SonarCloud에서 설정하는 조직과 프로젝트에는 Azure DevOps에서 설정한 조직과 프로젝트가 미러링됩니다.

1. **Azure에서 조직 가져오기**를 클릭합니다.
1. **Create an organization** 페이지의 **Azure DevOps organization name** 텍스트 상자에는 Azure DevOps 조직의 이름을 입력하고, **Personal Access Token** 텍스트 상자에는 이전 연습에서 적어 둔 토큰의 값을 붙여넣은 다음 **Continue**를 클릭합니다.
1. **조직 설정 가져오기** 섹션의 **키** 텍스트 상자에 조직을 지정하는 문자열을 입력하고 **계속**을 클릭합니다.

    > **참고**: 키는 SonarCloud 내에서 고유해야 합니다. **Key** 텍스트 상자 오른쪽에 녹색 확인 표시가 나타나는지 확인하세요. 이 표시가 나타나면 키가 고유성 관련 필수 조건을 충족하는 것입니다.

1. **플랜 선택** 섹션에서 이 랩에 사용할 플랜을 선택하고(**무료** 권장) **조직 만들기**를 클릭합니다.

    > **참고**: 그러면 Azure DevOps 조직을 미러링하는 SonarCloud 조직이 작성됩니다.

    > **참고**: 다음으로는 새로 만든 조직 내에서 Azure DevOps 프로젝트 **SonarExamples**를 미러링하는 SonarCloud 프로젝트를 만듭니다.

1. **Analyze projects - Select repositories** 페이지의 Azure DevOps 프로젝트 목록에서 **SonarExamples / SonarExamples** 항목 옆의 체크박스를 선택하고 **Set up**을 클릭합니다.
1. **분석 방법 선택** 페이지에서 **Azure 파이프라인 사용** 타일을 클릭합니다.
1. **Analyze with Azure Pipelines** 페이지의 **Install our extension** 섹션에서 **Continue**를 클릭합니다.

    > **참고**: 확장을 이미 설치했으면 확장 만들기를 건너뛰어도 됩니다.
1. **새 Sonarcloud 서비스 엔드포인트 추가**에서 Azure DevOps 프로젝트에 설명된 단계를 수행하고, 서비스 연결에 **SonarSC**이라는 이름을 지정하고, 모든 파이프라인에 대한 액세스 권한을 부여하는 확인란을 **선택**하고 **확인 및 저장**을 클릭합니다. Sonarcloud 웹 사이트로 돌아가서 **계속**을 클릭합니다.
1. **Azure Pipelines로 분석** 페이지의 **Azure Pipelines 구성** 섹션에서 **.NET**을 클릭합니다. 그러면 **Prepare Analysis Configuration**, **Run Code Analysis** 및 **Publish Quality Gate Result** 작업에서 수행해야 하는 일련의 단계가 표시됩니다. 파이프라인 정의에 대해 이러한 지침이 필요합니다.

    > **참고**: 각 목표를 달성하려면 수행해야 하는 단계 목록을 검토합니다. 후속 작업에서 이러한 단계를 구현합니다.

    > **참고**: SonarCloud Service Endpoint를 설정하는 데 필요한 토큰의 값과 **Project Key** 및 **Project Name**의 값을 적어 둡니다.

### <a name="exercise-1-set-up-an-azure-pipeline-that-integrates-with-sonarcloud"></a>연습 1: SonarCloud와 통합되는 Azure 파이프라인 설정

이 연습에서는 SonarCloud와 통합되는 Azure 파이프라인을 설정합니다.

> **참고**: 여기서는 **SonarExamples** 코드 분석을 위해 SonarCloud와 통합되는 새 빌드 파이프라인을 설정합니다.

#### <a name="task-1-initiate-creation-of-the-project-build-pipeline"></a>작업 1: 프로젝트 빌드 파이프라인 만들기 시작

이 작업에서는 프로젝트용 빌드 파이프라인 만들기를 시작합니다.

1. Azure DevOps 포털의 **SonarExamples** 창에 표시된 웹 브라우저 창으로 전환합니다. **프로젝트 설정**으로 이동하고 **표시 유형**을 비공개로 변경하고 **저장**합니다.

    > **참고**: Mod 00의 단계를 수행하여 프라이빗 프로젝트에만 병렬 작업을 설정했으며 조직에 현재 공용 프로젝트에 사용할 수 있는 작업이 없는 경우 이 작업을 수행해야 합니다.

#### <a name="task-2-create-a-pipeline-by-using-the-yaml-editor"></a>작업 2: YAML 편집기를 사용하여 파이프라인 만들기

이 작업에서는 YAML 편집기를 사용하여 파이프라인을 만듭니다.

> **참고**: YAML 파이프라인 구성을 계속 진행하려면 먼저 SonarCloud용 서비스 연결을 생성해야 합니다.

1. Azure DevOps 포털의 맨 왼쪽에 있는 세로 메뉴 모음에서 **Pipelines**를 클릭한 다음 **파이프라인 만들기**를 클릭합니다.
1. **코드 위치** 창에서 **Azure Repos Git**를 클릭합니다.
1. **리포지토리 선택** 창에서 **SonarExamples**를 클릭합니다.
1. **파이프라인 구성** 창에서 **.NET Desktop** YAML 템플릿을 클릭합니다.

    > **참고**: 그러면 YAML 편집기가 자동으로 표시되고 템플릿 YAML 파일이 열립니다. 이 파일을 올바르게 구성하려면 다음 파일과 일치하도록 조정하거나 내용을 바꿔야 합니다.

    ```yaml
    trigger:
    - master

    pool:
      vmImage: 'windows-latest'

    variables:
      buildConfiguration: 'Release'
      buildPlatform: 'any cpu'

    steps:
    - task: NuGetToolInstaller@0
      displayName: 'Use NuGet 4.4.1'
      inputs:
        versionSpec: 4.4.1

    - task: NuGetCommand@2
      displayName: 'NuGet restore'
      inputs:
        restoreSolution: 'SomeConsoleApplication.sln'

    - task: SonarCloudPrepare@1
      displayName: 'Prepare analysis configuration'
      inputs:
        SonarCloud: 'SC'
        organization: 'myorga'
        scannerMode: 'MSBuild'
        projectKey: 'dotnet-framework-on-azdo'
        projectName: 'Sample .NET Framework project with Azure DevOps'


    - task: VSBuild@1
      displayName: 'Build solution **\*.sln'
      inputs:
        solution: 'SomeConsoleApplication.sln'
        platform: '$(BuildPlatform)'
        configuration: '$(BuildConfiguration)'

    - task: VSTest@2
      displayName: 'VsTest - testAssemblies'
      inputs:
        testAssemblyVer2: |
         **\$(BuildConfiguration)\*Test*.dll
         !**\obj\**
        codeCoverageEnabled: true
        platform: '$(BuildPlatform)'
        configuration: '$(BuildConfiguration)'

    - task: SonarCloudAnalyze@1
      displayName: 'Run SonarCloud analysis'

    - task: SonarCloudPublish@1
      displayName: 'Publish results on build summary'
    ```

    > **참고**: **net-desktop-sonarcloud.yml** 파일은 [SonarSource GitHub 리포지토리](https://github.com/SonarSource/sonar-scanner-vsts/blob/master/yaml-pipeline-templates/net-desktop-sonarcloud.yml)에서 다운로드할 수 있습니다.

    > **참고**: 이 작업의 나머지 단계를 수행하여 YAML 파이프라인을 수정해야 합니다.

1. **NuGetCommand@2** 작업에서 `restoreSolution: 'SomeConsoleApplication.sln'`을 `restoreSolution: '**\SomeConsoleApplication.sln'`으로 바꿔서 솔루션이 리포지토리의 루트에 없다는 사실을 고려합니다.
1. **SonarCloudPrepare@1** 작업에서 **설정** 옵션을 클릭하여 시각적 도우미를 열고, 생성된 **sonarSC** 서비스 연결을 드롭다운에서 선택하고, **Sonarcloud 웹 사이트 > Azure Pipeline 구성**에 제안된 필드의 값을 바꿉니다. **추가**를 클릭하여 파이프라인에 대한 변경 내용을 포함합니다.
1. **VSBuild@1** 작업에서 `solution: 'SomeConsoleApplication.sln'`을 `solution: '**\SomeConsoleApplication.sln'`으로 바꿔서 솔루션이 리포지토리의 루트에 없다는 사실을 고려합니다.
1. **파이프라인 YAML 검토** 창에서 **저장 및 실행**을 클릭하고 **저장 및 실행** 창에서 **저장 및 실행**을 클릭합니다.

    > **참고**: YAML 편집기에서 이 작업을 완료한 경우 다음 작업을 건너뛰세요.

1. Azure Pipelines > Pipelines로 이동하여 **Sonarexample** 파이프라인을 클릭하고 파이프라인이 완료되기를 기다립니다.

#### <a name="task-3-check-pipeline-results"></a>작업 3: 파이프라인 결과 확인

이 작업에서는 파이프라인 결과를 확인합니다.

1. 빌드 실행 창(앞에서 만든 YAML 또는 클래식 창)에서 **요약** 탭의 내용을 검토하고 **확장** 탭 머리글을 클릭합니다.

    > **참고**: **품질 게이트 결과 게시** 작업을 사용하도록 설정해 둔 경우 **확장** 탭에는 SonarCloud 분석 보고서의 요약이 포함됩니다.

1. **확장** 탭에서 **자세한 SonarCloud 보고서**를 클릭합니다. 그러면 새 브라우저 탭이 자동으로 열리고 SonarCloud 프로젝트 페이지의 보고서가 표시됩니다.

    > **참고**: SonarCloud 프로젝트로 이동할 수도 있습니다.

1. 보고서에 품질 게이트 결과가 포함되어 있지 않음을 확인하고 결과가 없는 이유를 살펴봅니다.

    > **참고**: 품질 게이트 결과를 확인하려면 첫 번째 보고서를 실행한 후에 **새 코드 정의**를 설정해야 합니다. 이렇게 하면 후속 파이프라인 실행 시에는 품질 게이트 결과가 포함됩니다.

1. SonarCloud 프로젝트의 **개요** 탭(Sonarcloud 웹 사이트)에서 **관리** 아이콘(왼쪽 열) 및 **새 코드**를 클릭합니다.
1. SonarCloud 프로젝트의 **새 코드** 탭에서 **이전 버전**을 클릭합니다.
1. **Azure DevOps 포털**에서 최신 빌드가 실행되는 **SonarExamples** 프로젝트 창이 표시된 웹 브라우저 창으로 전환하여 **새로 실행**을 클릭하고 **파이프라인 실행** 창에서 **실행**을 클릭합니다.
1. 빌드 실행 창에서 **요약** 탭의 내용을 검토하고 **확장** 탭 머리글을 클릭합니다.
1. **확장** 탭에서 **자세한 SonarCloud 보고서**를 클릭합니다. 그러면 새 브라우저 탭이 자동으로 열리고 SonarCloud 프로젝트 페이지의 보고서가 표시됩니다.
1. 이제 보고서 및 Azure DevOps **확장** 탭에 **품질 게이트 결과가 포함되어 있는지** 확인합니다.

    > **참고**: 지금까지 SonarCloud에서 새 조직을 만들었으며, 분석을 수행하고 빌드 결과를 SonarCloud로 푸시하도록 Azure DevOps 빌드를 구성했습니다.

### <a name="exercise-2-analyze-sonarcloud-reports"></a>연습 2: SonarCloud 보고서 분석

이 연습에서는 SonarCloud 보고서를 분석합니다.

#### <a name="task-1-analyze-sonarcloud-reports"></a>작업 1: SonarCloud 보고서 분석

이 작업에서는 SonarCloud 보고서를 분석합니다.

1. SonarCloud 프로젝트의 **개요** 탭에 **주** 분기에 대한 보고서에 대한 요약이 표시됩니다. **주 분기** 아이콘(왼쪽 열)을 클릭하고 **전체 코드**를 선택하면 더 자세한 보고서가 표시됩니다.

    > **참고**: 이 페이지에는 **코드 스멜**, **범위**, **중복** 및 **크기**(코드 줄) 등의 다른 메트릭도 있습니다. 아래 표에 이러한 각 용어의 간략한 설명이 나와 있습니다.

    | 사용 약관 | Description |
    | --- | --- |
    | **버그** | 코드의 오류를 나타내는 문제입니다. 코드가 아직 손상되지 않았으면 최악의 상황에서 손상될 가능성이 높다는 의미입니다. 따라서 코드를 수정해야 합니다. |
    | **취약점** | 공격자가 악용 가능한 백도어를 나타내는 보안 관련 문제입니다. |
    | **코드 스멜(Code Smell)** | 코드 유지 관리 관련 문제입니다. 이 문제를 방치하면 유지 관리 담당자가 후속 변경을 수행할 때 평소보다 작업이 더 어려워집니다. 그리고 최악의 경우에는 코드 상태를 혼동하여 변경 시에 오류를 추가로 생성할 수도 있습니다. |
    | **Coverage** | 단위 테스트 등의 테스트에서 유효성을 검사하는 코드 비율을 나타냅니다. 버그를 효율적으로 방지하려면 이러한 테스트에서 대부분의 코드를 실행해 보거나 검사해야 합니다. |
    | **중복** | 중복 메트릭에는 소스 코드에서 중복된 부분이 표시됩니다. |
    | **크기** | 문, 함수, 클래스, 파일, 디렉터리 수를 포함한 프로젝트 내의 코드 줄 수가 표시됩니다. |

    > **참고**: 버그 수 옆에 표시되는 문자는 **안정성 등급**을 나타냅니다. 특히 **C**자는 이 코드에 중요한 버그가 하나 이상 있음을 나타냅니다. 안정성 등급에 대한 자세한 내용은 [SonarQube 설명서](https://docs.sonarqube.org/display/SONAR/Metric+Definitions#MetricDefinitions-Reliability)를 참조하세요. 이 설명서에서 [규칙 유형](https://docs.sonarqube.org/latest/user-guide/rules/) 관련 추가 정보를 살펴보고 [심각도](https://docs.sonarqube.org/latest/user-guide/issues/)도 확인할 수 있습니다.

1. **버그** 수를 나타내는 숫자를 클릭합니다. 그러면 **문제** 탭 내용이 자동으로 표시됩니다.
1. **문제** 탭 오른쪽에서 버그를 나타내는 큰 사각형을 클릭하여 해당 코드를 표시합니다.

    > **참고**: **Program.cs** 파일의 줄 9에서 오류 세부 정보를 검토합니다. 여기에는 **Change this condition so that it does not always evaluate to 'true'; some subsequent code is never executed.** 라는 권장 사항이 포함되어 있습니다.

1. 코드와 줄 번호 사이에 있는 세로선 위에 마우스 포인터를 놓고 코드 검사에서 확인된 문제를 파악합니다.

    > **참고**: 샘플 프로젝트는 매우 작으므로 기록 데이터가 없습니다. [SonarCloud에서 제공되는 수천 가지 공용 프로젝트](https://sonarcloud.io/explore/projects)에서 유용한 실제 결과를 확인할 수 있습니다.

### <a name="exercise-3-implement-azure-devops-pull-request-integration-with-sonarcloud"></a>연습 3: SonarCloud와 Azure DevOps 끌어오기 요청 통합 구현

이 연습에서는 Azure DevOps와 SonarCloud 간의 끌어오기 요청 통합을 설정합니다.

> **참고**: Azure DevOps 끌어오기 요청에 포함된 코드 분석을 수행하도록 SonarCloud 분석을 구성하려면 다음 작업을 수행해야 합니다.

- SonarCloud 프로젝트에 Azure DevOps 개인용 액세스 토큰을 추가합니다. 이 토큰을 사용하여 SonarCloud에 끌어오기 요청 액세스 권한이 부여됩니다.
- 끌어오기 요청에서 트리거된 빌드를 제어하는 Azure DevOps 분기 정책을 구성합니다.

#### <a name="task-1-create-an-azure-devops-personal-access-token-for-pull-request-integration-with-sonarcloud"></a>작업 1: SonarCloud와의 끌어오기 요청 통합에 사용할 Azure DevOps 개인용 액세스 토큰 만들기

이 작업에서는 SonarCloud 프로젝트와 Azure DevOps 끌어오기 요청 통합을 구현하기 위한 개인용 액세스 토큰 요구 사항을 검토합니다.

1. Azure DevOps 포털의 **SonarExamples** 프로젝트가 표시된 웹 브라우저 창으로 전환합니다.
1. 이 랩의 앞에서 생성한 Azure DevOps 개인용 액세스 토큰을 **다시 사용**하거나 이 랩의 앞부분에서 설명한 단계를 반복하여 **SonarExamples** 프로젝트에 대한 **코드** 범위 및 **읽기 & 쓰기** 권한이 있는 개인용 액세스 토큰을 생성합니다.

    > **참고**: 이 랩의 앞부분에서 생성한 개인용 액세스 토큰을 재사용할 수도 있습니다.

    > **참고**: 개인용 액세스 토큰을 만든 사용자의 보안 컨텍스트에는 끌어오기 요청 관련 SonarCloud 주석이 추가됩니다. SonarCloud에서 생성되는 주석을 명확하게 파악할 수 있도록 별도의 "봇" Azure DevOps 사용자를 만드는 것이 좋습니다.

#### <a name="task-2-configure-pull-request-integration-in-sonarcloud"></a>작업 2: SonarCloud에서 끌어오기 요청 통합 구성

이 작업에서는 SonarCloud 프로젝트에 Azure DevOps 개인용 액세스 토큰을 할당하여 SonarCloud에서 끌어오기 요청 통합을 구성합니다.

1. **SonarCloud 포털**의 **SonarExamples** 프로젝트가 표시된 웹 브라우저 창으로 전환합니다.
1. 프로젝트 대시보드 페이지에서 **관리** 탭 아이콘을 클릭하고 드롭다운 메뉴에서 **일반 설정**을 클릭합니다.
1. **일반 설정** 페이지에서 **끌어오기 요청**을 클릭합니다.
1. **끌어오기 요청** 설정의 **일반** 섹션에 있는 **공급자** 드롭다운 목록에서 **Azure DevOps Services**를 선택하고 **저장**을 클릭합니다.
1. **끌어오기 요**청 설정의 **Azure DevOps Services와 통합** 섹션에 있는 **개인용 액세스 토큰** 텍스트 상자에 앞에서 생성한 Azure DevOps 개인용 액세스 토큰을 붙여넣고 **저장**을 클릭합니다.

#### <a name="task-3-configure-a-branch-policy-for-integration-with-sonarcloud"></a>작업 3: SonarCloud와의 통합을 위한 분기 정책 구성

이 작업에서는 SonarCloud와의 통합을 위한 Azure DevOps 분기 정책을 구성합니다.

1. **Azure DevOps 포털**의 **SonarExamples** 프로젝트가 표시된 웹 브라우저 창으로 전환합니다.
1. Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에 있는 **Repos**를 클릭하고 **Repos** 섹션에서 **분기**를 클릭합니다.
1. **분기** 창의 분기 목록에서 **master** 분기 항목 오른쪽 모서리 위에 마우스를 놓으면 **자세히** 메뉴를 나타내는 세로 줄임표 문자가 표시됩니다. 이 문자를 클릭하고 팝업 메뉴에서 **분기 정책**을 클릭합니다.
1. **master** 창에서 **빌드 유효성 검사** 오른쪽에 있는 **+** 를 클릭합니다.
1. **빌드 정책 추가** 창의 **빌드 파이프라인** 드롭다운 목록에서 이 작업 앞부분에서 만든 파이프라인을 선택하고 **표시 이름**에는 **SonarCloud 분석**을 입력한 후에 **저장**을 클릭합니다.

    > **참고**: 이제 **master** 분기를 대상으로 하는 끌어오기 요청을 작성하면 Azure DevOps가 SonarCloud 분석을 트리거하도록 구성되었습니다.

#### <a name="task-4-validate-pull-request-integration"></a>작업 4: 끌어오기 요청 통합 유효성 검사

이 작업에서는 끌어오기 요청을 만들고 결과를 검토하여 Azure DevOps와 SonarCloud 간의 끌어오기 요청 통합 유효성을 검사합니다.

> **참고**: 리포지토리의 파일을 변경하고 SonarCloud 분석을 트리거하는 요청을 작성해야 합니다.

1. Azure DevOps 포털 왼쪽의 세로 메뉴 모음에 있는 **Repos**를 클릭합니다. 그러면 **파일** 창이 표시됩니다.
1. 가운데 창의 폴더 계층 구조에서 **sonarqube-scanner-msbuild\\CSharpProject\\SomeConsoleApplication** 폴더의 **Program.cs** 파일로 이동하고 **편집**을 클릭합니다.
1. **Program.cs** 창에서 줄 `public static bool AlwaysReturnsTrue()` 바로 위의 코드에 다음 빈 메서드를 추가합니다.

    ```csharp
    public void Unused(){

    }
    ```

1. **Program.cs** 창에서 **커밋**을 클릭합니다.
1. **커밋** 창의 **분기 이름** 텍스트 상자에 **branch1**을 입력하고 **끌어오기 요청 만들기** 체크박스를 선택한 다음 **커밋**을 클릭합니다.
1. **새 끌어오기 요청** 창에서 **만들기**를 선택합니다.
1. **업데이트된 Program.cs** 창의 **개요** 탭에서 빌드 프로세스가 완료될 때까지 진행률을 모니터링합니다.
1. **업데이트된 Program.cs** 창의 **개요** 탭에서 필수 검사는 정상적으로 완료되었지만 옵션 검사는 실패했다는 내용을 확인하고 **3개 검사 보기** 링크를 클릭합니다.
1. **검사** 창에서 SonarCloud 검사 결과를 검토하고 창을 닫습니다.

    > **참고**: 결과에는 분석 빌드는 정상적으로 완료되었지만 PR의 새 코드에서 코드 품질 검사가 실패했다는 내용이 표시됩니다. 새로 발견된 문제와 관련하여 PR에 주석이 게시되었습니다.

1. **업데이트된 Program.cs** 창의 **개요** 탭으로 돌아와서 아래쪽의 주석이 포함된 섹션으로 스크롤한 다음 새로 추가된 클래스와 관련하여 SonarCloud에서 생성된 주석을 검토합니다.

    > **참고**: 보고된 문제에는 끌어오기 요청에 해당하는 코드 변경 내용만 포함되어 있습니다. **Program.cs** 및 기타 파일의 기존 문제는 무시됩니다.

#### <a name="task-4-block-pull-requests-in-response-to-failing-code-quality-checks"></a>작업 4: 실패하는 코드 품질 검사에 대응하여 끌어오기 요청 차단

이 작업에서는 실패하는 코드 품질 검사에 대응하여 끌어오기 요청 차단을 구성합니다.

> **참고**: 이 시점에서는 코드 품질 검사가 실패해도 끌어오기 요청을 완료하고 해당 변경 내용을 커밋할 수 있습니다. 여기서는 관련 코드 품질 검사에 통과하지 않으면 커밋을 차단하도록 Azure DevOps 구성을 수정합니다.

1. **업데이트된 Program.cs** 창이 표시된 Azure DevOps 포털의 왼쪽 아래에서 **프로젝트 설정**을 클릭합니다.
1. **프로젝트 설정** 세로 메뉴의 **Repos** 섹션에서 **리포지토리**를 클릭합니다.
1. **모든 리포지토리** 창에서 **SonarExamples**를 클릭합니다.
1. **SonarExamples** 창에서 **정책** 탭 머리글을 클릭합니다.
1. **정책** 목록에서 아래쪽의 분기 목록으로 스크롤하여 **master** 분기를 나타내는 항목을 클릭합니다.
1. **master** 창에서 **상태 검사** 섹션까지 아래로 스크롤하고 **+** 를 클릭합니다.
1. **상태 정책 추가** 창의 **확인할 상태** 드롭다운 목록에서 **SonarCloud/품질 게이트** 항목을 선택하고 **정책 요구 사항** 옵션이 **필수**로 설정되어 있는지 확인한 후에 **저장**을 클릭합니다.

    > **참고**: 이 시점이 되면 사용자는 코드 품질 검사가 정상적으로 완료될 때까지 끌어오기 요청을 병합할 수 없습니다. 즉, 해당하는 SonarCloud 프로젝트에서 SonarCloud가 확인한 모든 문제를 해결하거나 **확인됨** 또는 **해결됨**으로 표시해야 합니다.

## <a name="review"></a>검토

이 랩에서는 SonarCloud와 Azure DevOps Services를 통합하는 방법을 알아보았습니다.
