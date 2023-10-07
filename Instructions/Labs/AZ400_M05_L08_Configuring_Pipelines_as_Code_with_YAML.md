---
lab:
  title: YAML을 통해 파이프라인을 코드로 구성
  module: 'Module 05: Implement a secure continuous deployment using Azure Pipelines'
---

# YAML을 통해 파이프라인을 코드로 구성

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독의 소유자 역할과 Azure 구독과 연결된 Microsoft Entra 테넌트에서 전역 관리자 역할이 있는 Microsoft 계정 또는 Microsoft Entra 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

## 랩 개요

대다수 팀은 YAML을 사용하여 빌드 및 릴리스 파이프라인을 정의하는 방식을 선호합니다. 비주얼 디자이너를 사용하는 팀과 같은 파이프라인 기능에 액세스하면서 다른 원본 파일처럼 관리할 수 있는 태그 파일을 사용할 수 있습니다. 리포지토리 루트에 해당 파일만 추가하면 손쉽게 YAML 빌드 정의를 프로젝트에 추가할 수 있습니다. Azure DevOps에서는 널리 사용되는 프로젝트 형식용 기본 템플릿, 그리고 빌드 및 릴리스 작업 정의 프로세스를 간편하게 진행할 수 있는 YAML 디자이너도 제공됩니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성합니다.

## 예상 소요 시간: 60분

## Instructions

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb_MultiStageYAML** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb_MultiStageYAML**로 설정하고 다른 필드는 기본값으로 유지합니다. **만들기**를 클릭합니다.

    ![프로젝트 만들기](images/create-project.png)

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb_MultiStageYAML** 프로젝트를 엽니다. **Repos > 파일**, **리포지토리 가져오기**를 클릭합니다. **가져오기**를 선택합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git 을 붙여넣고 **가져오기**를 클릭합니다.

    ![리포지토리 가져오기](images/import-repo.png)

2. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용하여 개발하는 **.devcontainer** 폴더 컨테이너 설정(VS Code 또는 GitHub Codespaces에서 로컬로).
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

#### 작업 2: Azure 리소스 생성

이 작업에서는 Azure Portal을 사용하여 Azure 웹앱을 만듭니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고[**, Azure Portal**](https://portal.azure.com)로 이동하고, 이 랩에서 사용할 Azure 구독에서 소유자 역할이 있고 이 구독과 연결된 Microsoft Entra 테넌트에서 전역 관리자 역할이 있는 사용자 계정으로 로그인합니다.
2. Azure Portal의 도구 모음에서 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
3. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.

    >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다.

    > **참고:** 지역 목록 및 해당 별칭을 보려면 Azure Cloud Shell - Bash에서 다음 명령을 실행합니다.

    ```bash
    az account list-locations -o table
    ```

4. **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 리소스 그룹을 만듭니다. 여기서 `<region>` 자리 표시자는 가장 가까운 지역(예: 'centralus', 'westeurope' 또는 사용자가 선택하는 기타 사용 가능한 지역) 이름으로 바꿉니다.

    ```bash
    LOCATION='<region>'
    ```

    ```bash
    RESOURCEGROUPNAME='az400m05l11-RG'
    az group create --name $RESOURCEGROUPNAME --location $LOCATION
    ```

5. 다음 명령을 실행하여 Windows App Service 요금제를 만들려면:

    ```bash
    SERVICEPLANNAME='az400m05l11-sp1'
    az appservice plan create --resource-group $RESOURCEGROUPNAME --name $SERVICEPLANNAME --sku B3
    ```

6. 고유한 이름을 사용하여 웹앱을 만듭니다.

    ```bash
    WEBAPPNAME=eshoponWebYAML$RANDOM$RANDOM
    az webapp create --resource-group $RESOURCEGROUPNAME --plan $SERVICEPLANNAME --name $WEBAPPNAME
    ```

    > **참고**: 웹앱의 이름을 적어 둡니다. 이 랩의 후반부에서 필요합니다.

7. Azure Cloud Shell은 닫고, 브라우저에서 Azure Portal은 열어 둡니다.

### 연습 1: Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성

이 연습에서는 Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성합니다.

#### 작업 1: YAML 빌드 정의 추가

이 작업에서는 기존 프로젝트에 YAML 빌드 정의를 추가합니다.

1. **Pipelines** 허브의 **파이프라인** 창으로 다시 이동합니다.
2. **첫 번째 파이프라인 만들기** 창에서 **파이프라인 만들기**를 클릭합니다.

    > **참고**: 여기서는 마법사를 사용하여 프로젝트를 기준으로 YAML 파이프라인 정의를 만듭니다.

3. **코드 위치** 창에서 **Azure Repos Git(YAML)** 옵션을 클릭합니다.
4. **리포지토리 선택** 창에서 **eShopOnWeb_MultiStageYAML**을 클릭합니다.
5. **파이프라인 구성** 창에서 아래로 스크롤하여 **기존 Azure Pipelines YAML 파일**을 선택합니다.
6. **기존 YAML 파일 선택** 블레이드에서 다음 매개 변수를 지정합니다.
   - 분기: **main**
   - 경로: **.ado/eshoponweb-ci.yml**
7. **계속**을 클릭하여 이러한 설정을 저장합니다.
8. **파이프라인 YAML 검토** 화면에서 **실행**을 클릭하여 빌드 파이프라인 프로세스를 시작합니다.
9. 빌드 파이프라인이 성공적으로 완료될 때까지 기다립니다. 소스 코드 자체에 대한 경고는 이 랩 연습과 관련이 없으므로 무시하십시오.

    > **참고**: YAML 파일의 각 작업(경고와 오류 포함)을 검토할 수 있습니다.

#### 작업 2: YAML 정의에 지속적인 업데이트 추가

이 작업에서는 이전 작업에서 만든 파이프라인의 YAML 기반 정의에 지속적인 업데이트를 추가합니다.

> **참고**: 빌드 및 테스트 프로세스가 정상적으로 완료되었으므로 이제 YAML 정의에 업데이트를 추가할 수 있습니다.

1. 파이프라인 실행 창 오른쪽 위의 줄임표 기호를 클릭하고 드롭다운 메뉴에서 **파이프라인 편집**을 클릭합니다.
2. **eShopOnWeb_MultiStageYAML/.ado/eshoponweb-ci.yml** 파일의 내용이 표시된 창에서 파일의 끝(56번 줄)으로 이동한 후 **Enter/Return** 키를 눌러 빈 줄을 새로 추가합니다.
3. **57**번 줄에서 다음 콘텐츠를 추가하여 YAML 파이프라인에서 **릴리스** 단계를 정의합니다.

    > **참고**: 파이프라인을 더욱 효율적으로 구성하고 진행률을 추적하는 데 필요한 어떤 단계든 정의할 수 있습니다.

    ```yaml
    - stage: Deploy
      displayName: Deploy to an Azure Web App
      jobs:
      - job: Deploy
        pool:
          vmImage: 'windows-2019'
        steps:
    ```

4. YAML 정의 끝부분의 새 줄에 커서를 설정합니다.

    > **참고**: 이 커서 위치에 새 작업이 추가됩니다.

5. 코드 창 오른쪽의 작업 목록에서 **Azure App Service 배포** 작업을 검색하여 선택합니다.
6. **Azure App Service 배포** 창에서 다음 설정을 지정하고

    - **Azure 구독** 드롭다운 목록에서 **추가**를 클릭합니다. 이 랩 앞부분에서 Azure 리소스를 배포한 Azure 구독을 선택합니다. 그런 다음 권한 부여를 클릭하고 메시지가 표시되면 Azure 리소스를 배포할 때 사용한 것과 같은 사용자 계정을 사용하여 인증을 진행합니다.
    - **App Service 이름** 드롭다운 목록에서 랩 앞부분에서 배포한 웹앱의 이름을 선택합니다.
    - **패키지 또는 폴더** 텍스트 상자에서 기본값을 `$(Build.ArtifactStagingDirectory)/**/Web.zip`으로 **업데이트**합니다.
7. **추가** 단추를 클릭하여 도우미 창에서 설정을 확인합니다.

    > **참고**: 그러면 YAML 파이프라인 정의에 배포 작업이 자동으로 추가됩니다.

8. 편집기에 추가된 코드 조각은 azureSubscription 및 WebappName 매개 변수의 이름을 반영하여 아래와 유사해야 합니다.

    ```yaml
        - task: AzureRmWebAppDeployment@4
          inputs:
            ConnectionType: 'AzureRM'
            azureSubscription: 'AZURE SUBSCRIPTION HERE (b999999abc-1234-987a-a1e0-27fb2ea7f9f4)'
            appType: 'webApp'
            WebAppName: 'eshoponWebYAML369825031'
            packageForLinux: '$(Build.ArtifactStagingDirectory)/**/Web.zip'
    ```

9. 작업이 **단계** 작업의 자식으로 나열되어 있는지 확인합니다. 그렇지 않은 경우, 추가된 작업에서 모든 줄을 선택하고 **Tab** 키를 두 번 눌러 4칸 들여쓰기를 적용합니다. 그러면 해당 작업이 **단계** 작업의 자식으로 나열됩니다.

    > **참고**: 이 랩에서는 **packageForLinux** 매개 변수를 사용하면 안 되지만 Windows나 Linux에서는 해당 매개 변수를 사용할 수 있습니다.

    > **참고**: 기본적으로 이 두 단계는 독립적으로 실행됩니다. 그러므로 첫 단계의 빌드 출력을 추가로 변경하지 않으면 두 번째 단계에서 사용하지 못할 수도 있습니다. 이러한 변경 내용을 구현하기 위해, 새 작업을 추가하여 배포 스테이지의 시작 부분에서 배포 아티팩트를 다운로드합니다.

10. **배포** 스테이지의 **단계** 노드 아래에 있는 첫 번째 줄에 커서를 놓고 Enter/Return 키를 눌러 빈 줄(64번 줄)을 추가합니다.
11. **작업** 창에서 **빌드 아티팩트 다운로드** 작업을 검색하여 선택합니다.
12. 이 작업에 대해 다음 매개 변수를 지정합니다.
    - 다음에 의해 생성된 아티팩트 다운로드: **현재 빌드**
    - 다운로드 유형: **특정 아티팩트**
    - 아티팩트 이름: **목록에서 "웹 사이트"를 선택하거나 목록에** 자동으로 표시되지 않는 경우 **"웹 사이트"** 를 직접 입력합니다.
    - 대상 디렉터리: **$(Build.ArtifactStagingDirectory)**
13. **추가**를 클릭합니다.
14. 추가된 코드 조각은 아래와 유사합니다.

    ```yaml
        - task: DownloadBuildArtifacts@0
          inputs:
            buildType: 'current'
            downloadType: 'single'
            artifactName: 'Website'
            downloadPath: '$(Build.ArtifactStagingDirectory)'
    ```

15. YAML 들여쓰기를 끈 경우, 편집기에서 추가한 작업을 선택한 상태로 **Tab** 키를 두 번 눌러 4칸 들여쓰기를 적용합니다.

    > **참고**: 여기서도 해당 작업을 쉽게 읽을 수 있도록 앞뒤에 빈 줄을 추가할 수도 있습니다.

16. **저장**을 클릭하고 **저장** 창에서 **저장**을 다시 클릭하여 변경 내용을 기본 분기에 직접 커밋합니다.

    > **참고**: 원래 CI-YAML이 새 빌드를 자동으로 트리거하도록 구성되지 않았기 때문에 이 빌드를 수동으로 시작해야 합니다.

17. Azure DevOps 왼쪽 메뉴에서 **파이프라인**으로 이동한 후 **파이프라인을** 다시 선택합니다.
18. **EShopOnWeb_MultiStageYAML** 파이프라인을 열고 **파이프라인 실행**을 클릭합니다.
19. 표시되는 창에서 **실행**을 확인합니다.
20. 서로 다른 2가지 스테이지인 **.NET Core 솔루션 빌드** 및 **Azure 웹앱에 배포**가 표시됩니다.
21. 파이프라인이 시작될 때까지 기다렸다가 빌드 스테이지가 성공적으로 완료될 때까지 기다립니다.
22. 배포 스테이지가 시작되고 나면 **필요한 권한**과 다음을 나타내는 주황색 막대가 표시됩니다.

    ```text
    This pipeline needs permission to access a resource before this run can continue to Deploy to an Azure Web App
    ```

23. **보기**를 클릭합니다.
24. **검토 대기** 창에서 **허용**을 클릭합니다.
25. **허용 팝업** 창에서 메시지의 유효성을 검사하고 **허용**을 클릭하여 확인합니다.
26. 이렇게 하면 배포 스테이지가 시작됩니다. 이 스테이지가 성공적으로 완료될 때까지 기다립니다.

     > **참고**: YAML 파이프라인 구문 문제로 인해 배포가 실패할 경우, 다음을 참조로 사용하십시오.

     ```yaml
    #NAME THE PIPELINE SAME AS FILE (WITHOUT ".yml")
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
          displayName: Test
          inputs:
            command: 'test'
            projects: 'tests/UnitTests/*.csproj'
        
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
        
        - task: PublishBuildArtifacts@1
          displayName: Publish Artifacts ADO - Bicep
          inputs:
            PathtoPublish: '$(Build.SourcesDirectory)/.azure/bicep/webapp.bicep'
            ArtifactName: 'Bicep'
            publishLocation: 'Container'
    
    - stage: Deploy
      displayName: Deploy to an Azure Web App
      jobs:
      - job: Deploy
        pool:
          vmImage: 'windows-2019'
        steps:
        - task: DownloadBuildArtifacts@0
          inputs:
            buildType: 'current'
            downloadType: 'single'
            artifactName: 'Website'
            downloadPath: '$(Build.ArtifactStagingDirectory)'
        - task: AzureRmWebAppDeployment@4
          inputs:
            ConnectionType: 'AzureRM'
            azureSubscription: 'AZURE SUBSCRIPTION HERE (b999999abc-1234-987a-a1e0-27fb2ea7f9f4)'
            appType: 'webApp'
            WebAppName: 'eshoponWebYAML369825031'
            packageForLinux: '$(Build.ArtifactStagingDirectory)/**/Web.zip'
    
    ```

#### 작업 4: 배포된 사이트 검토

1. Azure Portal이 표시된 웹 브라우저 창으로 다시 전환한 다음 Azure 웹앱 속성이 표시된 블레이드로 이동합니다.
2. Azure 웹앱 블레이드에서 **개요**를 클릭하고 개요 블레이드에서 **찾아보기**를 클릭하여 새 웹 브라우저 탭에서 사이트를 엽니다.
3. EShopOnWeb 전자 상거래 웹 사이트를 보여 주는 새 브라우저 탭에서 배포된 사이트가 예상대로 로드되는지 확인합니다.

### 연습 2: Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인에 대한 환경 설정 구성

이 연습에서는 Azure DevOps의 YAML 기반 파이프라인에 승인을 추가합니다.

#### 작업 1: 파이프라인 환경 설정

코드로서의 YAML 파이프라인에는 Azure DevOps 클래식 릴리스 파이프라인과 마찬가지로 릴리스/품질 게이트가 없습니다. 그러나 **환경**을 사용하여 YAML Pipelines-as-Code에 대해 몇 가지 유사성을 구성할 수 있습니다. 이 작업에서는 이 메커니즘을 사용하여 빌드 스테이지에 대한 승인을 구성합니다.

1. Azure DevOps 프로젝트 **EShopOnWeb_MultiStageYAML**에서 **파이프라인**으로 이동합니다.
2. 왼쪽의 파이프라인 메뉴에서 **환경**을 선택합니다.
3. **환경 만들기**를 클릭합니다.
4. **새 환경** 창에서 **승인**이라는 환경의 이름을 추가합니다.
5. **리소스**에서 **없음**을 선택합니다.
6. **만들기** 단추를 눌러 설정을 확인합니다.
7. 환경이 만들어지면 '리소스 추가' 단추 옆에 있는 '줄임표'(...)를 클릭합니다.
8. **승인 및 확인**을 선택합니다.
9. **첫 번째 검사 추가**에서 **승인**을 선택합니다.
10. Azure DevOps 사용자 계정 이름을 **승인자** 필드에 추가합니다.

    > **참고:** 실제 시나리오에서는 이 프로젝트에서 작업하는 DevOps 팀의 이름이 반영됩니다.

11. **만들기** 단추를 눌러 정의된 승인 설정을 확인합니다.
12. 마지막으로, 배포 단계의 YAML 파이프라인 코드에 필요한 '환경: 승인' 설정을 추가해야 합니다. 이렇게 하려면 **Repos**로 이동하여 **.ado 폴더**를 찾은 다음, **eshoponweb-ci.yml** Pipeline-as-Code 파일을 선택합니다.
13. 콘텐츠 보기에서 **편집** 단추를 클릭하여 편집 모드로 전환합니다.
14. **배포 작업**의 시작으로 이동합니다(-job: 60번 줄에서 배포).
15. 바로 아래에 빈 줄을 새로 추가하고 다음 코드 조각을 추가합니다.

    ```yaml
      environment: approvals
    ```

    결과 코드 조각은 다음과 같습니다.

    ```yaml
     jobs:
      - job: Deploy
        environment: approvals
        pool:
          vmImage: 'windows-2019'
    ```

16. 환경은 배포 스테이지의 특정 설정이므로 '작업'에서 사용할 수 없습니다. 따라서 현재의 작업 정의를 추가로 변경해야 합니다.
17. **60**번 줄에서 '- job: Deploy'의 이름을 **- deployment: Deploy**로 바꿉니다.
18. 그 다음, **63**번 줄(vmImage: Windows-2019)에서 빈 줄을 새로 추가합니다.
19. 다음 Yaml 코드 조각을 붙여넣습니다.

    ```yaml
        strategy:
          runOnce:
            deploy:
    ```

20. 나머지 코드 조각(**67**번 줄 끝까지)을 선택하고 **Tab** 키를 사용하여 YAML 들여쓰기를 수정합니다.

    결과 YAML 코드 조각은 **이제 배포 단계를** 반영하여 다음과 같이 표시됩니다.

    ```yaml
    - stage: Deploy
      displayName: Deploy to an Azure Web App
      jobs:
      - deployment: Deploy
        environment: approvals
        pool:
          vmImage: 'windows-2019'
        strategy:
          runOnce:
            deploy:
              steps:
              - task: DownloadBuildArtifacts@0
                inputs:
                  buildType: 'current'
                  downloadType: 'single'
                  artifactName: 'Website'
                  downloadPath: '$(Build.ArtifactStagingDirectory)'
              - task: AzureRmWebAppDeployment@4
                inputs:
                  ConnectionType: 'AzureRM'
                  azureSubscription: 'AZURE SUBSCRIPTION HERE (b999999abc-1234-987a-a1e0-27fb2ea7f9f4)'
                  appType: 'webApp'
                  WebAppName: 'eshoponWebYAML369825031'
                  packageForLinux: '$(Build.ArtifactStagingDirectory)/**/Web.zip'
    ```

21. **커밋**을 클릭하고, 표시되는 커밋 창에서 **커밋**을 다시 클릭하여 코드 YAML 파일의 변경 내용을 확인합니다.
22. 왼쪽의 Azure DevOps 프로젝트 메뉴로 이동하여 **파이프라인**, **파이프라인을** 차례로 선택하고 앞에서 사용한 **EshopOnWeb_MultiStageYAML** 파이프라인을 확인합니다.
23. 파이프라인을 엽니다.
24. **파이프라인 실행**을 클릭하여 새 파이프라인 실행을 트리거합니다. **실행**을 클릭하여 확인합니다.
25. 이전과 마찬가지로, 빌드 스테이지가 예상대로 시작됩니다. 해당 스테이지가 완료될 때까지 기다립니다.
26. 그 다음, 배포 스테이지에 대해 *environment:approvals*가 구성되어 있으므로 시작하기 전에 승인 확인이 요청됩니다.
27. 이는 파이프라인 보기에서 볼 수 있으며, **대기 중(0/1개 검사가 통과됨)** 으로 표시됩니다. **이를 계속 실행하여 Azure 웹앱에 배포하려면 우선 승인을 검토해야 한다는** 알림 메시지도 표시됩니다.
28. 이 메시지의 옆에 있는 **보기** 단추를 클릭합니다.
29. 표시되는 창에서 **Azure 웹앱에 배포에 대한 검사 및 수동 유효성 검사**를 클릭하고 **승인 대기** 메시지를 클릭합니다.
30. **승인**을 클릭합니다.
31. 이렇게 하면 배포 단계가 시작되고 Azure 웹앱 소스 코드를 성공적으로 배포할 수 있습니다.

    > **참고:** 이 예제에서는 승인만 사용했지만 Azure Monitor, REST API 등과 같은 다른 검사를 비슷한 방식으로 사용할 수 있다는 점을 알아두십시오.

### 연습 3: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### 작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
2. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m05l11-RG')].name" --output tsv
    ```

3. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m05l11-RG')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## 검토

이 랩에서는 Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성했습니다.
