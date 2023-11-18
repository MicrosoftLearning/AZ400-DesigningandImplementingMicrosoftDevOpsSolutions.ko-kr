---
lab:
  title: Azure Load Testing을 사용하여 애플리케이션 성능 모니터링
  module: 'Module 09: Implement continuous feedback'
---

# Application Insights 및 Azure Load Testing을 사용하여 애플리케이션 성능 모니터링

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독의 소유자 역할, 그리고 Azure 구독과 연결된 Microsoft Entra 테넌트의 전역 관리자 역할이 지정되어 있는 Microsoft 계정 또는 Microsoft Entra 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/azure/active-directory/roles/manage-roles-portal#view-my-roles)을 참조하세요.

## 랩 개요

**Azure Load Testing** 은 대규모 부하를 생성할 수 있는 완전 관리형 부하 테스트 서비스입니다. 이 서비스는 호스팅되는 위치에 관계없이 애플리케이션에 대한 트래픽을 시뮬레이션합니다. 개발자, 테스터 및 QA(품질 보증) 엔지니어는 이를 사용하여 애플리케이션 성능, 확장성 또는 용량을 최적화할 수 있습니다.
URL을 사용하고 테스트 도구에 대한 사전 지식 없이 웹 애플리케이션에 대한 부하 테스트를 빠르게 만듭니다. Azure Load Testing은 부하 테스트를 대규모로 실행하는 복잡성과 인프라를 추상화합니다.
고급 부하 테스트 시나리오의 경우 인기 있는 오픈 소스 부하 및 성능 도구인 기존 Apache JMeter 테스트 스크립트를 다시 사용하여 부하 테스트를 만들 수 있습니다. 예를 들어 테스트 계획은 여러 애플리케이션 요청으로 구성되거나, HTTP가 아닌 엔드포인트를 호출하거나, 입력 데이터와 매개 변수를 사용하여 테스트를 보다 동적으로 만들 수 있습니다.

이 랩에서는 Azure Load Testing을 사용하여 다양한 부하 시나리오를 사용하는 라이브 실행 웹 애플리케이션에 대한 성능 테스트를 시뮬레이션하는 방법에 대해 알아봅니다. 마지막으로 AZURE Load Testing을 CI/CD 파이프라인에 통합하는 방법을 알아봅니다. 

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure App Service 웹앱 배포.
- YAML 기반 CI/CD 파이프라인을 작성하고 실행합니다.
- Azure Load Testing을 배포합니다.
- Azure Load Testing을 사용하여 Azure 웹앱 성능을 조사합니다.
- AZURE Load Testing을 CI/CD 파이프라인에 통합합니다.

## 예상 소요 시간: 60분

## Instructions

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 **작업 항목 프로세스** 드롭다운에서 **스크럼**을 선택합니다. **만들기**를 클릭합니다.

    ![프로젝트 만들기](images/create-project.png)

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **가져오기**를 클릭합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git을 붙여넣고 **가져오기**를 클릭합니다.

    ![리포지토리 가져오기](images/import-repo.png)

2. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용(VS Code 또는 GitHub Codespaces에서 로컬로 사용)하여 개발하는 **.devcontainer** 폴더 컨테이너 설정
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

#### 작업 2: Azure 리소스 생성

이 작업에서는 Azure Portal에서 클라우드 셸을 사용하여 Azure 웹앱을 만듭니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고, Azure Portal**[로 ](https://portal.azure.com)** 이동하고, 이 랩에서 사용할 Azure 구독의 소유자 역할이 있고 이 구독과 연결된 Microsoft Entra 테넌트에서 Global 관리istrator의 역할을 가진 사용자 계정으로 로그인합니다.
2. Azure Portal의 도구 모음에서 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
3. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.
    >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다.

4. **Cloud Shell** 창의 **Bash** 프롬프트에서 다음 명령을 실행하여 리소스 그룹을 만듭니다. 여기서 `<region>` 자리 표시자는 ‘eastus’ 등의 가장 가까운 Azure 지역 이름으로 바꿉니다.

    ```bash
    RESOURCEGROUPNAME='az400m09l16-RG'
    LOCATION='<region>'
    az group create --name $RESOURCEGROUPNAME --location $LOCATION
    ```

5. 다음 명령을 실행하여 Windows App Service 요금제를 만들려면:

    ```bash
    SERVICEPLANNAME='az400l16-sp'
    az appservice plan create --resource-group $RESOURCEGROUPNAME \
        --name $SERVICEPLANNAME --sku B3 
    ```

6. 고유한 이름을 사용하여 웹앱을 만듭니다.

    ```bash
    WEBAPPNAME=partsunlimited$RANDOM$RANDOM
    az webapp create --resource-group $RESOURCEGROUPNAME --plan $SERVICEPLANNAME --name $WEBAPPNAME 
    ```

    > **참고**: 웹앱의 이름을 적어 둡니다. 이 랩의 후반부에서 필요합니다.

### 연습 1: Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성

이 연습에서는 Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성합니다.

#### 작업 1: YAML 빌드 추가 및 정의 배포

이 작업에서는 기존 프로젝트에 YAML 빌드 정의를 추가합니다.

1. **Pipelines** 허브의 **파이프라인** 창으로 다시 이동합니다.
2. **첫 번째 파이프라인 만들기** 창에서 **파이프라인 만들기**를 클릭합니다.

    > **참고**: 여기서는 마법사를 사용하여 프로젝트를 기준으로 YAML 파이프라인 정의를 만듭니다.

3. **코드 위치** 창에서 **Azure Repos Git(YAML)** 옵션을 클릭합니다.
4. **리포지**토리 선택 창에서 eShopOnWeb**을 클릭합니다**.
5. 파이프라인** 구성 창에서 **아래로 스크롤하여 시작 파이프라인**을 선택합니다**.
6. **시작 파이프라인에서 모든 줄을 선택하고** 삭제합니다.
7. **변경 내용을 저장**하기 전에 매개 변수를 수정해야 한다는 것을 알고 아래에서 전체 템플릿 파이프라인을 복사**합니다**.

```
#Template Pipeline for CI/CD 
# trigger:
# - main

resources:
  repositories:
    - repository: self
      trigger: none

stages:
- stage: Build
  displayName: Build .Net Core Solution
  jobs:
  - job: Build
    pool:
      vmImage: ubuntu-latest
    steps:
    - task: DotNetCoreCLI@2
      displayName: Restore
      inputs:
        command: 'restore'
        projects: '**/*.sln'
        feedsToUse: 'select'

    - task: DotNetCoreCLI@2
      displayName: Build
      inputs:
        command: 'build'
        projects: '**/*.sln'
    
    - task: DotNetCoreCLI@2
      displayName: Publish
      inputs:
        command: 'publish'
        publishWebProjects: true
        arguments: '-o $(Build.ArtifactStagingDirectory)'
    
    - task: PublishBuildArtifacts@1
      displayName: Publish Artifacts ADO - Website
      inputs:
        pathToPublish: '$(Build.ArtifactStagingDirectory)'
        artifactName: Website
    
- stage: Deploy
  displayName: Deploy to an Azure Web App
  jobs:
  - job: Deploy
    pool:
      vmImage: 'windows-2019'
    steps:
    - task: DownloadBuildArtifacts@1
      inputs:
        buildType: 'current'
        downloadType: 'single'
        artifactName: 'Website'
        downloadPath: '$(Build.ArtifactStagingDirectory)'

```
4. YAML 정의(줄 69)의 끝에 있는 새 줄에 커서를 설정합니다.

    > **참고**: 이 커서 위치에 새 작업이 추가됩니다.

5. 코드 창 오른쪽의 작업 목록에서 **Azure App Service 배포** 작업을 검색하여 선택합니다.
6. **Azure App Service 배포** 창에서 다음 설정을 지정하고

    - **Azure 구독** 드롭다운 목록에서 **추가**를 클릭합니다. 이 랩 앞부분에서 Azure 리소스를 배포한 Azure 구독을 선택합니다. 그런 다음 권한 부여를 클릭하고 메시지가 표시되면 Azure 리소스를 배포할 때 사용한 것과 같은 사용자 계정을 사용하여 인증을 진행합니다.
    - **App Service 이름** 드롭다운 목록에서 랩 앞부분에서 배포한 웹앱의 이름을 선택합니다.
    - **패키지 또는 폴더** 텍스트 상자에서 기본값을 `$(Build.ArtifactStagingDirectory)/**/Web.zip`으로 **업데이트**합니다.
7. **추가** 단추를 클릭하여 도우미 창에서 설정을 확인합니다.

    > **참고**: 그러면 YAML 파이프라인 정의에 배포 작업이 자동으로 추가됩니다.

8. 편집기에 추가된 코드 조각은 azureSubscription 및 WebappName 매개 변수의 이름을 반영하여 아래와 유사해야 합니다.

> **참고**: 이 랩에서는 **packageForLinux** 매개 변수를 사용하면 안 되지만 Windows나 Linux에서는 해당 매개 변수를 사용할 수 있습니다.

    ```yaml
        - task: AzureRmWebAppDeployment@4
          inputs:
            ConnectionType: 'AzureRM'
            azureSubscription: 'AZURE SUBSCRIPTION HERE (b999999abc-1234-987a-a1e0-27fb2ea7f9f4)'
            appType: 'webApp'
            WebAppName: 'eshoponWebYAML369825031'
            packageForLinux: '$(Build.ArtifactStagingDirectory)/**/Web.zip'
    ```
9. **저장**을 클릭하고 **저장** 창에서 **저장**을 다시 클릭하여 변경 내용을 마스터 분기에 직접 커밋합니다.

    > **참고**: 원래 CI-YAML이 새 빌드를 자동으로 트리거하도록 구성되지 않았기 때문에 이 빌드를 수동으로 시작해야 합니다.

10. Azure DevOps 왼쪽 메뉴에서 **파이프라인**으로 이동한 후 **파이프라인을** 다시 선택합니다.
11. **EShopOnWeb_MultiStageYAML** 파이프라인을 열고 **파이프라인 실행**을 클릭합니다.
12. 표시되는 창에서 **실행**을 확인합니다.
13. 서로 다른 2가지 스테이지인 **.NET Core 솔루션 빌드** 및 **Azure 웹앱에 배포**가 표시됩니다.
14. 파이프라인이 시작될 때까지 기다렸다가 빌드 스테이지가 성공적으로 완료될 때까지 기다립니다.
15. 배포 단계가 시작되고 나면 필요한** 사용 권한과 **다음을 나타내는 주황색 막대가 표시됩니다.

    ```text
    This pipeline needs permission to access a resource before this run can continue to Deploy to an Azure Web App
    ```

16. **보기**를 클릭합니다.
17. **검토 대기** 창에서 **허용**을 클릭합니다.
18. **허용 팝업** 창에서 메시지의 유효성을 검사하고 **허용**을 클릭하여 확인합니다.
19. 이렇게 하면 배포 스테이지가 시작됩니다. 이 스테이지가 성공적으로 완료될 때까지 기다립니다.

#### 작업 2: 배포된 사이트 검토

1. Azure Portal이 표시된 웹 브라우저 창으로 다시 전환한 다음 Azure 웹앱 속성이 표시된 블레이드로 이동합니다.
2. Azure 웹앱 블레이드에서 **개요**를 클릭하고 개요 블레이드에서 **찾아보기**를 클릭하여 새 웹 브라우저 탭에서 사이트를 엽니다.
3. EShopOnWeb 전자 상거래 웹 사이트를 보여 주는 새 브라우저 탭에서 배포된 사이트가 예상대로 로드되는지 확인합니다.

### 연습 2: Azure Load Testing 배포 및 설정

이 연습에서는 Azure에 Azure Load Testing 리소스를 배포하고 라이브 실행 Azure 앱 Service에 대해 다른 부하 테스트 시나리오를 구성합니다.

#### 작업 1: Azure 부하 테스트 배포

이 작업에서는 Azure 구독에 Azure Load Testing 리소스를 배포합니다.

1. Azure Portal에서(https://portal.azure.com)Azure 리소스** 만들기로 **이동합니다.
2. 'Search Services 및 Marketplace' 검색 필드에 Azure Load Testing**을 입력**합니다.
3. 검색 결과에서 Azure Load Testing **(Microsoft에서 게시)을 선택합니다**.
4. Azure 부하 테스트 페이지에서 만들기**를 클릭하여 **배포 프로세스를 시작합니다.
5. '부하 테스트 리소스 만들기' 페이지에서 리소스 배포에 필요한 세부 정보를 제공합니다.
- **구독**: Azure 구독 선택
- **리소스 그룹**: 이전 연습에서 Web App Service를 배포하는 데 사용한 리소스 그룹을 선택합니다.
- **이름**: EShopOnWebLoadTesting
- **지역**: 해당 지역에 가까운 지역 선택

    > **참고**: Azure Load Testing 서비스는 모든 Azure 지역에서 사용할 수 없습니다.

6. 검토 및 만들기**를 클릭하여 **설정의 유효성을 검사합니다.
7. 만들기**를 클릭하여 **확인하고 Azure Load Testing 리소스를 배포합니다.
8. '배포 진행 중' 페이지로 전환됩니다. 배포가 성공적으로 완료될 때까지 몇 분 정도 기다립니다.
9. 배포 진행률 페이지에서 리소스**로 이동을 클릭하여 **EShopOnWebLoadTesting** Azure Load Testing 리소스로 이동합니다**. 

    > **참고**: Azure Load Testing 리소스를 배포하는 동안 블레이드를 닫거나 Azure Portal을 닫은 경우 Azure Portal Search 필드 또는 리소스/최근 리소스 목록에서 다시 찾을 수 있습니다. 

#### 작업 2: Azure Load Testing 테스트 만들기

이 작업에서는 다른 부하 구성 설정을 사용하여 다른 Azure Load Testing 테스트를 만듭니다. 

10. EShopOnWebLoadTesting** Azure Load Testing 리소스 블레이드 **내에서 **빠른 테스트를 시작하고 빠른 테스트**** 단추를 클릭합니다**.
11. 다음 매개 변수 및 설정을 완료하여 부하 테스트를 만듭니다.
- **테스트 URL**: 이전 연습에서 배포한 Azure 앱 서비스의 URL을 입력합니다(EShopOnWeb... azurewebsites.net)**https:// 포함**
- **부하** 지정: 가상 사용자
- **가상 사용자** 수: 50
- **테스트 기간(초)**: 120
- **램프업 시간(초)**: 0
12. 테스트 실행을** 클릭하여 테스트의 구성을 확인합니다**.
13. 테스트는 약 2분 동안 실행됩니다. 
14. 테스트가 실행되면 EShopOnWebLoadTesting** Azure Load Testing 리소스 페이지로 다시 **이동하고 테스트로 이동하여 **테스트를**** 선택하고 **테스트 **Get_eshoponweb...**
15. 위쪽 메뉴에서 만들기 **, **빠른 테스트** 만들기를 클릭하여 **두 번째 부하 테스트를 만듭니다.
16. 다음 매개 변수 및 설정을 완료하여 다른 부하 테스트를 만듭니다.
- **테스트 URL**: 이전 연습에서 배포한 Azure 앱 서비스의 URL을 입력합니다(EShopOnWeb... azurewebsites.net)**https:// 포함**
- **부하** 지정: RPS(초당 요청 수)
- **RPS(초당 요청 수):** 100
- **응답 시간(밀리초)**: 500
- **테스트 기간(초)**: 120
- **램프업 시간(초)**: 0
17. 테스트 실행을** 클릭하여 테스트의 구성을 확인합니다**.
18. 테스트는 약 2분 동안 실행됩니다.

#### 작업 3: Azure 부하 테스트 결과 유효성 검사

이 작업에서는 Azure Load Testing TestRun의 결과의 유효성을 검사합니다. 

두 빠른 테스트가 모두 완료되면 몇 가지 변경을 수행하고 결과의 유효성을 검사해 보겠습니다.

19. **EShopOnWebLoadTesting** 리소스 블레이드에서 테스트**로 이동하고 **첫 번째 Get_eshoponwebyaml 선택합니다. 테스트. 위쪽 메뉴에서 편집**을 클릭합니다**.
20. 여기서 포털을 사용하면 테스트 이름을** 기본 생성 이름에서 보다 설명적인 이름으로 변경할 **수 있습니다. 또한 앞에서 정의한 매개 변수를 계속 변경할 수 있습니다.
21. **테스트 편집 블레이드에서 테스트** 계획** 탭으로 **이동합니다. 
22. 여기서는 Azure Load Testing이 **프레임워크로 사용하는 Apache JMeter** 부하 테스트 스크립트 파일을 관리할 수 있습니다. 파일 **quick_test.jmx를 확인합니다**. 이 파일을 **선택하여 랩 가상 머신에서 엽니다** . 팝업 창에서 편집기로 Visual Studio Code**를 선택하여 **파일을 엽니다.
23. 파일의 XML 언어 구조를 확인합니다.

    > 참고: Apache JMeter의 고급 구문에 대한 추가 정보와 이해는 다음 [Azure Load Testing - Jmeter](https://learn.microsoft.com/en-us/azure/load-testing/how-to-create-and-run-load-test-with-jmeter-script) 링크를 검사.

24. 테스트** 보기로 **돌아가서 두 테스트 중 하나를 선택하여 테스트 중 하나를 클릭하여** 더 자세한 보기를 **엽니다. 그러면 더 자세한 테스트 페이지로 리디렉션됩니다. 여기에서 결과 목록에서 TestRun_mm/dd/yy-hh:hh**를 선택하여 **실제 실행의 세부 정보를 확인할 수 있습니다.
25. 자세한 **TestRun** 페이지에서 Azure Load Testing 시뮬레이션의 실제 결과를 식별합니다. 일부 값은 다음과 같습니다.
- 로드/총 요청 수
- 기간
- 응답 시간(90번째 백분위수 응답 시간을 반영하는 시간(초)의 결과를 보여 줍니다. 즉, 요청의 90%에 대해 응답 시간이 지정된 결과 내에 있음을 의미합니다.
- 초당 요청의 처리량
26. 아래에서는 이러한 값 중 몇 가지가 대시보드 그래프 선 및 차트 보기를 사용하여 표시됩니다.
27. 시뮬레이션된 두 테스트의 결과를** 서로 비교하고 **더 많은 사용자가 App Service 성능에 미치는 영향을** 파악하는 데 몇 분 **정도 걸립니다.

### 연습 2: Azure DevOps 파이프라인에서 CI/CD를 사용하여 부하 테스트 자동화

CI/CD 파이프라인에 추가하여 Azure Load Testing에서 부하 테스트 자동화를 시작합니다. Azure Portal에서 부하 테스트를 실행한 후 구성 파일을 내보내고 Azure Pipelines에서 CI/CD 파이프라인을 구성합니다(GitHub Actions에 대해 유사한 기능이 있음).

이 연습을 완료하면 Azure Load Testing을 사용하여 부하 테스트를 실행하도록 구성된 CI/CD 워크플로가 있습니다.

#### 작업 1: ADO 서비스 커넥트ion 세부 정보 식별

이 작업에서는 Azure DevOps Service 커넥트ion의 서비스 주체에 필요한 권한을 부여합니다.

1. **Azure DevOps 포털**에서(https://dev.azure.com)EShopOnWeb** 프로젝트로 **이동합니다.
2. 왼쪽 아래 모서리에서 Project 설정** 선택합니다**.
3. **파이프라인** 섹션에서 서비스 커넥트 선택합니다****.
4. 랩 연습을 시작할 때 Azure 리소스를 배포하는 데 사용한 Azure 구독의 이름이 있는 서비스 커넥트ion을 확인합니다.
5. **서비스 커넥트ion을** 선택합니다. 개요 탭에서 **세부 정보**로 이동하고 **서비스 주체** 관리를 선택합니다**.**
6. 그러면 ID 개체에 대한 서비스 주체** 세부 정보가 열리는 **Azure Portal로 리디렉션됩니다.
7. **다음 단계에서 필요하므로 표시 이름** 값(Name_of_ADO_Organization_EShopOnWeb_-b86d9ae1-7552-4b75-a1e0-27fb2ea7f9f4와 같은 형식)을 복사합니다.

#### 작업 2: 서비스 주체 권한 부여

Azure Load Testing은 Azure RBAC를 사용하여 부하 테스트 리소스에 대한 특정 작업을 수행하기 위한 권한을 부여합니다. CI/CD 파이프라인에서 부하 테스트를 실행하려면 부하 테스트 기여자** 역할을 서비스 주체에 부여**합니다.

1. Azure Portal에서 **Azure** Load Testing** 리소스로 **이동합니다.
2. 액세스 제어(IAM)** > 추가 > 역할 할당 추가를 선택합니다**.
3. 역할 탭의 **작업 함수 역할 목록에서 부하 테스트 참가자**를 선택합니다**.**
4. **구성원 탭**에서 구성원** 선택을 선택한 **다음 이전에 복사한 표시 이름을** 사용하여 **서비스 주체를 검색합니다.
5. 서비스 주체를 **선택한 다음 선택을 선택합니다.******
6. **검토 + 할당 탭**에서 검토 + 할당**을 선택하여 **역할 할당을 추가합니다.

이제 Azure Pipelines 워크플로 정의에서 서비스 연결을 사용하여 Azure 부하 테스트 리소스에 액세스할 수 있습니다.

#### 작업 3: 부하 테스트 입력 파일 내보내기 및 Azure DevOps 원본 제어로 가져오기

CI/CD 워크플로에서 Azure Load Testing을 사용하여 부하 테스트를 실행하려면 소스 제어 리포지토리에 부하 테스트 구성 설정 및 입력 파일을 추가해야 합니다. 기존 부하 테스트가 있는 경우 Azure Portal에서 구성 설정 및 모든 입력 파일을 다운로드할 수 있습니다.

Azure Portal에서 기존 부하 테스트에 대한 입력 파일을 다운로드하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure** Load Testing** 리소스로 **이동합니다.
2. 왼쪽 창에서 테스트를 선택하여 부하 테스트 목록을 확인한 다음 테스트를 선택합니다****.****
3. 작업 중인 **테스트 실행 옆에 있는 줄임표(...)** 를 선택한 다음 입력 파일** 다운로드를 선택합니다**.
4. 브라우저는 부하 테스트 입력 파일이 포함된 압축된 폴더를 다운로드합니다.
5. zip 도구를 사용하여 입력 파일을 추출합니다. 폴더에는 다음 파일이 포함됩니다.

- *config.yaml*: 부하 테스트 YAML 구성 파일입니다. CI/CD 워크플로 정의에서 이 파일을 참조합니다.
- *quick_test.jmx*: JMeter 테스트 스크립트

6. 추출된 모든 입력 파일을 소스 제어 리포지토리에 커밋합니다. 이렇게 하려면 Azure DevOps Portal **(https://dev.azure.com))로 이동하고 **EShopOnWeb** DevOps 프로젝트로 **이동합니다. 
7. 리포지**토리를 선택합니다**. 소스 코드 폴더 구조에서 테스트 하위 폴더를 확인 **합니다** . 줄임표(...)를 확인하고 새 > 폴더**를 선택합니다**.
8. jmeter를** 폴더 이름으로 지정하고 **파일 이름에 대해 placeholder.txt**를 지정**합니다(참고: 폴더를 빈 폴더로 만들 수 없음).
9. 커밋**을 클릭하여 **자리 표시자 파일 및 jmeter 폴더 만들기를 확인합니다.
10. 폴더 구조**에서 **새로 만든 **jmeter** 하위 폴더로 이동합니다. **줄임표(...)** 를 클릭하고 파일 업로드를** 선택합니다**.
11. **찾아보기** 옵션을 사용하여 압축을 푼 zip 파일의 위치로 이동하고 config.yaml**과 **quick_test.jmx**를 모두 **선택합니다.
12. 커밋**을 클릭하여 **소스 제어에 파일 업로드를 확인합니다.

#### 작업 4: CI/CD 워크플로 YAML 정의 파일 업데이트

이 작업에서는 Azure Load Testing - Azure DevOps Marketplace 확장을 가져오고 AzureLoadTest 작업을 사용하여 기존 CI/CD 파이프라인을 업데이트합니다.

1. 부하 테스트를 만들고 실행하기 위해 Azure Pipelines 워크플로 정의는 Azure DevOps Marketplace의 Azure Load Testing 작업 확장을** 사용합니다**. [Azure DevOps Marketplace에서 Azure Load Testing 작업 확장을](https://marketplace.visualstudio.com/items?itemName=AzloadTest.AzloadTesting) 열고 무료로** 가져오기를 선택합니다**.
2. Azure DevOps 조직을 선택한 다음 설치**를 선택하여 **확장을 설치합니다.
3. Azure DevOps 포털 및 프로젝트 내에서 파이프라인**으로 이동하여 **이 연습의 시작 부분에 생성된 파이프라인을 선택합니다. **편집**을 클릭합니다.
4. YAML 스크립트에서 줄 56**으로 **이동하고 Enter/RETURN 키를 눌러 빈 줄을 새로 추가합니다. YAML 파일의 배포 단계 바로 앞에 있습니다.
5. 57줄에서 오른쪽에 있는 작업 도우미를 선택하고 Azure Load Testing을 **검색합니다**.
6. 시나리오의 올바른 설정으로 그래픽 창을 완료합니다.
- Azure 구독: Azure 리소스를 실행하는 구독 선택
- 부하 테스트 파일: '$(Build.SourcesDirectory)/tests/jmeter/config.yaml' 
- 부하 테스트 리소스 그룹: Azure 부하 테스트 리소스를 보유하는 리소스 그룹
- 부하 테스트 리소스 이름: ESHopOnWebLoadTesting
- 부하 테스트 실행 이름: ado_run
- 부하 테스트 실행 설명: ADO에서 부하 테스트
7. 추가를 클릭하여 매개 변수를 YAML의 코드 조각으로 삽입 확인 ****
8. YAML 코드 조각의 들여쓰기에서 오류(빨간색 구불구불한 선)를 제공하는 경우 2개의 공백 또는 탭을 추가하여 코드 조각을 올바르게 배치하여 수정합니다.  
9. 아래 샘플 코드 조각은 YAML 코드의 모양을 보여줍니다.
```
     - task: AzureLoadTest@1
      inputs:
        azureSubscription: 'AZURE DEMO SUBSCRIPTION(b86d9ae1-1234-4b75-a8e7-27fb2ea7f9f4)'
        loadTestConfigFile: '$(Build.SourcesDirectory)/tests/jmeter/config.yaml'
        resourceGroup: 'az400m05l11-RG'
        loadTestResource: 'EShopOnWebLoadTesting'
        loadTestRunName: 'ado_run'
        loadTestRunDescription: 'load testing from ADO'
```
10. 삽입된 YAML 코드 조각 아래에 Enter/RETURN을 눌러 빈 줄을 새로 추가합니다. 
11. 이 빈 줄 아래에 파이프라인 실행 중 Azure Load Testing 작업의 결과를 보여 주는 게시 태스크에 대한 코드 조각을 추가합니다.

```
    - publish: $(System.DefaultWorkingDirectory)/loadTest
      artifact: loadTestResults
```
12.  YAML 코드 조각의 들여쓰기에서 오류(빨간색 구불구불한 선)를 제공하는 경우 2개의 공백 또는 탭을 추가하여 코드 조각을 올바르게 배치하여 수정합니다.  
13. CI/CD 파이프라인 **에 두 코드 조각이 모두 추가되면 변경 내용을 저장** 합니다. 
14. 저장되면 실행을** 클릭하여 **파이프라인을 트리거합니다.
15. 분기(기본)를 확인하고 실행** 단추를 클릭하여 **파이프라인 실행을 시작합니다.
16. 파이프라인 상태 페이지에서 빌드** 단계를 클릭하여 **파이프라인의 여러 작업에 대한 자세한 로깅 세부 정보를 엽니다.
17. 파이프라인이 빌드 단계를 시작하고 파이프라인 흐름의 **AzureLoadTest** 작업에 도달할 때까지 기다립니다. 
18. 작업이 실행되는 동안 Azure Portal에서 Azure Load Testing으로 이동하여 **파이프라인이 adoloadtest1**이라는 **새 RunTest를 만드는 방법을 확인**합니다. TestRun 작업의 결과 값을 표시하도록 선택할 수 있습니다.
19. AzureLoadTest 작업이** 성공적으로 완료된 **Azure DevOps CI/CD 파이프라인 실행 보기로 다시 이동합니다. 자세한 로깅 출력에서 부하 테스트의 결과 값도 표시됩니다.

```
Task         : Azure Load Testing
Description  : Automate performance regression testing with Azure Load Testing
Version      : 1.2.30
Author       : Microsoft Corporation
Help         : https://docs.microsoft.com/azure/load-testing/tutorial-cicd-azure-pipelines#azure-load-testing-task
==============================================================================
Test '0d295119-12d0-482d-94be-a7b84787c004' already exists
Uploaded test plan for the test
Creating and running a testRun for the test
View the load test run in progress at: https://portal.azure.com/#blade/Microsoft_Azure_CloudNativeTesting/NewReport//resourceId/%2fsubscriptions%4b75-a1e0-27fb2ea7f9f4%2fresourcegroups%2faz400m05l11-rg%2fproviders%2fmicrosoft.loadtestservice%2floadtests%2feshoponwebloadtesting/testId/0d295119-12d0-787c004/testRunId/161046f1-d2d3-46f7-9d2b-c8a09478ce4c
TestRun completed

-------------------Summary ---------------
TestRun start time: Mon Jul 24 2023 21:46:26 GMT+0000 (Coordinated Universal Time)
TestRun end time: Mon Jul 24 2023 21:51:50 GMT+0000 (Coordinated Universal Time)
Virtual Users: 50
TestStatus: DONE

------------------Client-side metrics------------

Homepage
response time        : avg=1359ms min=59ms med=539ms max=16629ms p(90)=3127ms p(95)=5478ms p(99)=13878ms
requests per sec     : avg=37
total requests       : 4500
total errors         : 0
total error rate     : 0
Finishing: AzureLoadTest

```
20. 이제 파이프라인 실행의 일부로 자동화된 부하 테스트를 수행했습니다. 마지막 작업에서는 실패 조건을 지정합니다. 즉, 웹앱의 성능이 특정 임계값 미만인 경우 배포 단계를 시작할 수 없습니다. 

#### 작업 5: 부하 테스트 파이프라인에 실패/성공 조건 추가

이 작업에서는 부하 테스트 실패 조건을 사용하여 애플리케이션이 품질 요구 사항을 충족하지 않을 때 경고를 받습니다(결과적으로 파이프라인이 실행되지 않음).

1. Azure DevOps에서 EShopOnWeb 프로젝트로 이동하고 Repos**를 엽니다**.
2. 리포지토리 내에서 이전에 만들고 사용한 /tests/jmeter** 하위 폴더로 찾**습니다.
3. 부하 테스트 *config.yaml** 파일을 엽니다. 편집**을 클릭하여 **파일 편집을 허용합니다.
4. 파일의 끝에 다음 코드 조각을 추가합니다.

```
failureCriteria:
  - avg(response_time_ms) > 300
  - percentage(error) > 50
```
5. 커밋 및 커밋**을 한 번 더 클릭하여 config.yaml에 **변경 내용을 저장합니다.
6. 파이프라인으로 다시 이동하여 **EShopOnWeb** 파이프라인을 **다시 실행합니다**. 몇 분 후에 AzureLoadTest** 태스크에 **대한 **실패한** 상태 실행을 완료합니다. 
7. 파이프라인에 대한 자세한 로깅 보기를 열고 AzureLoadtest**의 세부 정보의 유효성을 **검사합니다. 유사한 샘플 출력은 다음과 같습니다.

```
Creating and running a testRun for the test
View the load test run in progress at: https://portal.azure.com/#blade/Microsoft_Azure_CloudNativeTesting/NewReport//resourceId/%2fsubscriptions%2fb86d9ae1-7552-47fb2ea7f9f4%2fresourcegroups%2faz400m05l11-rg%2fproviders%2fmicrosoft.loadtestservice%2floadtests%2feshoponwebloadtesting/testId/0d295119-12d0-a7b84787c004/testRunId/f4bec76a-8b49-44ee-a388-12af34f0d4ec
TestRun completed

-------------------Summary ---------------
TestRun start time: Mon Jul 24 2023 23:00:31 GMT+0000 (Coordinated Universal Time)
TestRun end time: Mon Jul 24 2023 23:06:02 GMT+0000 (Coordinated Universal Time)
Virtual Users: 50
TestStatus: DONE

-------------------Test Criteria ---------------
Results          :1 Pass 1 Fail

Criteria                     :Actual Value        Result
avg(response_time_ms) > 300                       1355.29               FAILED
percentage(error) > 50                                                  PASSED


------------------Client-side metrics------------

Homepage
response time        : avg=1355ms min=58ms med=666ms max=16524ms p(90)=2472ms p(95)=5819ms p(99)=13657ms
requests per sec     : avg=37
total requests       : 4531
total errors         : 0
total error rate     : 0
##[error]TestResult: FAILED
Finishing: AzureLoadTest
```

8. 부하 테스트 출력**의 마지막 줄에 ##[error]TestResult: FAILED**가 표시됩니다. 평균 응답 시간이 > 300이거나 오류 비율이 > 20인 FailCriteria**를 정의**했으므로 이제 평균 응답 시간이 300을 초과하면 작업이 실패로 플래그가 지정됩니다. 

    > 참고: 실제 시나리오에서는 App Service의 성능에 대한 유효성을 검사하고 성능이 특정 위험 요소보다 낮으면 일반적으로 웹앱에 더 많은 부하가 있음을 의미하므로 추가 Azure 앱 서비스에 대한 새 배포를 트리거할 수 있습니다. Azure 랩 환경의 응답 시간을 제어할 수 없으므로 오류를 보장하기 위해 논리를 되돌리기 결정했습니다.

9.  파이프라인 작업의 failed 상태 실제로 Azure Load Testing 요구 사항 조건 유효성 검사의 성공이 반영됩니다.

### 연습 3: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### 작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
2. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m16l01')].name" --output tsv
    ```

3. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m16l01')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## 검토

이 연습에서는 Azure Pipelines를 사용하고 TestRuns를 사용하여 Azure Load Testing 리소스를 배포하여 Azure 앱 Service에 웹앱을 배포했습니다. 다음으로, Jmeter 부하 테스트 config.yaml 파일을 Azure DevOps Repos 소스 제어에 통합하고 Azure Load Testing을 사용하여 CI/CD 파이프라인을 확장했습니다. 마지막 연습에서는 LoadTest의 성공 조건을 정의하는 방법을 알아보았습니다.
