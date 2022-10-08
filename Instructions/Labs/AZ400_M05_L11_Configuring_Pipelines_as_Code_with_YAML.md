---
lab:
  title: '랩 11: YAML을 통해 파이프라인을 코드로 구성'
  module: 'Module 05: Implement a secure continuous deployment using Azure Pipelines'
---

# <a name="lab-11-configuring-pipelines-as-code-with-yaml"></a>랩 11: YAML을 통해 파이프라인을 코드로 구성

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독의 Owner 역할, 그리고 Azure 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할이 지정되어 있는 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/en-us/azure/active-directory/roles/manage-roles-portal#view-my-roles)을 참조하세요.

## <a name="lab-overview"></a>랩 개요

대다수 팀은 YAML을 사용하여 빌드 및 릴리스 파이프라인을 정의하는 방식을 선호합니다. 비주얼 디자이너를 사용하는 팀과 같은 파이프라인 기능에 액세스하면서 다른 원본 파일처럼 관리할 수 있는 태그 파일을 사용할 수 있습니다. 리포지토리 루트에 해당 파일만 추가하면 손쉽게 YAML 빌드 정의를 프로젝트에 추가할 수 있습니다. Azure DevOps에서는 널리 사용되는 프로젝트 형식용 기본 템플릿, 그리고 빌드 및 릴리스 작업 정의 프로세스를 간편하게 진행할 수 있는 YAML 디자이너도 제공됩니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성합니다.

## <a name="estimated-timing-60-minutes"></a>예상 소요 시간: 60분

## <a name="instructions"></a>Instructions

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿과 Azure 리소스(Azure 웹앱 및 Azure SQL 데이터베이스 포함)를 기반으로 하여 미리 구성된 Parts Unlimited 팀 프로젝트를 설정합니다.

#### <a name="task-1-configure-the-team-project"></a>작업 1: 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 **PartsUnlimited-YAML** 템플릿을 기반으로 새 프로젝트를 생성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 [Azure DevOps Services 데모 생성기 도구란 무엇인가요?](https://docs.microsoft.com/en-us/azure/devops/demo-gen)를 참조하세요.

1. **로그인**을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락**을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **YAML을 통해 파이프라인을 코드로 구성**을 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택**을 클릭합니다.
1. 템플릿 목록의 도구 모음에서 **일반**을 클릭하고 **PartsUnlimited-YAML** 템플릿을 선택한 다음 **템플릿 선택**을 클릭합니다.
1. **새 프로젝트 만들기** 페이지로 돌아와서 **프로젝트 만들기**를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1. **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동**을 클릭합니다.

#### <a name="task-2-create-azure-resources"></a>작업 2: Azure 리소스 생성

이 작업에서는 Azure Portal을 사용하여 Azure 웹앱과 Azure SQL 데이터베이스를 만듭니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하여 [**Azure Portal**](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 Azure 구독의 Owner 역할, 그리고 해당 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할을 가진 사용자 계정으로 로그인합니다.
1. Azure Portal의 도구 모음에서 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
1. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.

    >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다.

1. **Cloud Shell** 창의 **Bash** 프롬프트에서 다음 명령을 실행하여 리소스 그룹을 만듭니다. 여기서 `<region>` 자리 표시자는 ‘eastus’ 등의 가장 가까운 Azure 지역 이름으로 바꿉니다.

    ```bash
    LOCATION='<region>'
    ```

    ```bash
    RESOURCEGROUPNAME='az400m11l01-RG'
    az group create --name $RESOURCEGROUPNAME --location $LOCATION
    ```

1. 다음 명령을 실행하여 Windows App Service 요금제를 만들려면:

    ```bash
    SERVICEPLANNAME='az400l11a-sp1'
    az appservice plan create --resource-group $RESOURCEGROUPNAME --name $SERVICEPLANNAME --sku B3
    ```

1. 고유한 이름을 사용하여 웹앱을 만듭니다.

    ```bash
    WEBAPPNAME=partsunlimited$RANDOM$RANDOM
    az webapp create --resource-group $RESOURCEGROUPNAME --plan $SERVICEPLANNAME --name $WEBAPPNAME
    ```

    > **참고**: 웹앱의 이름을 적어 둡니다. 이 랩의 후반부에서 필요합니다.

1. 다음으로, Azure SQL Server를 만듭니다.

    ```bash
    USERNAME="Student"
    SQLSERVERPASSWORD="Pa55w.rd1234"
    SERVERNAME="partsunlimitedserver$RANDOM"

    az sql server create --name $SERVERNAME --resource-group $RESOURCEGROUPNAME \
    --location $LOCATION --admin-user $USERNAME --admin-password $SQLSERVERPASSWORD
    ```

1. 웹앱은 SQL 서버에 액세스할 수 있어야 하므로 SQL Server 방화벽 규칙에서 Azure 리소스에 대한 액세스를 허용해야 합니다.

    ```bash
    STARTIP="0.0.0.0"
    ENDIP="0.0.0.0"
    az sql server firewall-rule create --server $SERVERNAME --resource-group $RESOURCEGROUPNAME \
    --name AllowAzureResources --start-ip-address $STARTIP --end-ip-address $ENDIP
    ```

1. 이제 해당 서버 내에 데이터베이스를 만듭니다.

    ```bash
    az sql db create --server $SERVERNAME --resource-group $RESOURCEGROUPNAME --name PartsUnlimited \
    --service-objective S0
    ```

1. 만든 웹앱에는 해당 구성의 데이터베이스 연결 문자열이 필요하므로 다음 명령을 실행하여 이 문자열을 준비하고 웹앱의 앱 설정에 추가합니다.

    ```bash
    CONNSTRING=$(az sql db show-connection-string --name PartsUnlimited --server $SERVERNAME \
    --client ado.net --output tsv)

    CONNSTRING=${CONNSTRING//<username>/$USERNAME}
    CONNSTRING=${CONNSTRING//<password>/$SQLSERVERPASSWORD}

    az webapp config connection-string set --name $WEBAPPNAME --resource-group $RESOURCEGROUPNAME \
    -t SQLAzure --settings "DefaultConnectionString=$CONNSTRING" 
    ```

### <a name="exercise-1-configure-cicd-pipelines-as-code-with-yaml-in-azure-devops"></a>연습 1: Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성

이 연습에서는 Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성합니다.

#### <a name="task-1-delete-the-existing-pipeline"></a>작업 1: 기존 파이프라인 삭제

이 작업에서는 기존 파이프라인을 삭제합니다.

1. 랩 컴퓨터에서 Azure DevOps 포털의 **YAML을 통해 파이프라인을 코드로 구성** 프로젝트가 표시된 브라우저 창으로 전환한 다음 세로 탐색 창에서 **파이프라인**을 선택합니다.

    > **참고**: YAML 파이프라인을 구성하기 전에 기존 빌드 파이프라인을 사용하지 않도록 설정해야 합니다.

1. **파이프라인** 창에서 **PartsUnlimited** 항목을 선택합니다.
1. **PartsUnlimited** 블레이드 오른쪽 위의 세로 줄임표 기호를 클릭하고 드롭다운 메뉴에서 **삭제**를 선택합니다.
1. **PartsUnlimited**라고 쓰고 **삭제**를 클릭합니다.
1. 세로 탐색 창에서 **Repos > 파일**을 선택합니다. **azure-pipelines.yml** 파일에서 **master** 분기(**파일** 창 상단의 드롭다운)에 있는지 확인하고, 세로 줄임표 기호를 클릭하고 드롭다운 메뉴에서 **삭제**를 선택합니다. **커밋**을 클릭하여 master 분기에 변경 사항을 커밋합니다(기본 옵션 유지).

#### <a name="task-2-add-a-yaml-build-definition"></a>작업 2: YAML 빌드 정의 추가

이 작업에서는 기존 프로젝트에 YAML 빌드 정의를 추가합니다.

1. **Pipelines** 허브의 **파이프라인** 창으로 다시 이동합니다.
1. **첫 번째 파이프라인 만들기** 창에서 **파이프라인 만들기**를 클릭합니다.

    > **참고**: 여기서는 마법사를 사용하여 프로젝트를 기준으로 YAML 정의를 자동 작성합니다.

1. **코드 위치** 창에서 **Azure Repos Git(YAML)** 옵션을 클릭합니다.
1. **리포지토리 선택** 창에서 **PartsUnlimited**를 클릭합니다.
1. **파이프라인 구성** 창에서 **ASP<nolink>.NET**을 클릭하여 이 템플릿을 파이프라인의 시작점으로 사용합니다. 그러면 **파이프라인 YAML 검토** 창이 열립니다.

    > **참고**: 파이프라인 정의는 리포지토리 루트에 **azure-pipelines.yml** 파일로 저장됩니다. 이 파일에는 일반적인 ASP<nolink>.NET 솔루션을 빌드하고 테스트하는 데 필요한 단계가 포함됩니다. 필요에 따라 빌드를 사용자 지정할 수도 있습니다. 이 시나리오에서는 Windows 2019를 실행하는 VM을 강제로 사용하도록 **풀**을 업데이트합니다.

1. `trigger`가 **마스터**인지 확인합니다.

    > **참고**: Repos에서 리포지토리에 **master** 또는 **main** 분기가 있는지 확인합니다. 새로운 리포지토리에 대한 기본 분기 이름을 선택할 수 있습니다. [기본 분기 변경](https://docs.microsoft.com/en-us/azure/devops/repos/git/change-default-branch?view=azure-devops#choosing-a-name).

1. **파이프라인 YAML 검토** 창의 줄 **10**에서 `vmImage: 'windows-latest'`를 `vmImage: 'windows-2019'`로 바꿉니다.
1. **VSTest@2** 작업을 제거합니다.

    ```yaml
    - task: VSTest@2
      inputs:
        platform: '$(buildPlatform)'
        configuration: '$(buildConfiguration)'
    ```

1. **파이프라인 YAML 검토** 창에서 **저장 및 실행**을 클릭합니다.
1. **저장 및 실행** 창에서 기본 설정을 수락하고 **저장 및 실행**을 클릭합니다.
1. 파이프라인 실행 창의 **작업** 섹션에서 **작업**을 클릭하여 작업 진행률을 모니터링하여 작업이 정상적으로 완료되는지 확인합니다.

    > **참고**: YAML 파일의 각 작업(경고와 오류 포함)을 검토할 수 있습니다.

#### <a name="task-3-add-continuous-delivery-to-the-yaml-definition"></a>작업 3: YAML 정의에 지속적인 업데이트 추가

이 작업에서는 이전 작업에서 만든 파이프라인의 YAML 기반 정의에 지속적인 업데이트를 추가합니다.

> **참고**: 빌드 및 테스트 프로세스가 정상적으로 완료되었으므로 이제 YAML 정의에 업데이트를 추가할 수 있습니다.

1. 파이프라인 실행 창 오른쪽 위의 줄임표 기호를 클릭하고 드롭다운 메뉴에서 **파이프라인 편집**을 클릭합니다.
1. **azure-pipelines.yaml** 파일 콘텐츠가 표시된 창의 줄 **8**에서 `trigger` 섹션 뒤에 아래 콘텐츠를 추가하여 YAML 파이프라인에서 **빌드** 단계를 정의합니다.

    > **참고**: 파이프라인을 더욱 효율적으로 구성하고 진행률을 추적하는 데 필요한 어떤 단계든 정의할 수 있습니다.

    ```yaml
    stages:
    - stage: Build
      jobs:
      - job: Build
    ```

1. YAML 파일의 나머지 콘텐츠를 선택하고 **Tab** 키를 두 번 눌러 4칸 들여쓰기를 적용합니다(```job: Build```와 동일한 들여쓰기로 배치되어야 함).

    > **참고**: 이렇게 하면 `pool` 섹션으로 시작하는 모든 코드가 `job: Build`의 일부가 됩니다.

1. 파일 아래쪽에 아래 구성을 추가하여 두 번째 단계를 정의합니다.

    ```yaml
    - stage: Deploy
      jobs:
      - job: Deploy
        pool:
          vmImage: 'windows-2019'
        steps:
    ```

1. YAML 정의 끝부분의 새 줄에 커서를 설정합니다.

    > **참고**: 이 커서 위치에 새 작업이 추가됩니다.

1. 코드 창 오른쪽의 작업 목록에서 **Azure App Service 배포** 작업을 검색하여 선택합니다.
1. **Azure App Service 배포** 창에서 다음 설정을 지정하고

    - **Azure 구독** 드롭다운 목록에서 **추가**를 클릭합니다. 이 랩 앞부분에서 Azure 리소스를 배포한 Azure 구독을 선택합니다. 그런 다음 권한 부여를 클릭하고 메시지가 표시되면 Azure 리소스를 배포할 때 사용한 것과 같은 사용자 계정을 사용하여 인증을 진행합니다.
    - **App Service 이름** 드롭다운 목록에서 랩 앞부분에서 배포한 웹앱의 이름을 선택합니다.
    - **패키지 또는 폴더** 텍스트 상자에 `$(System.ArtifactsDirectory)/drop/*.zip`을 입력합니다.

    > **참고**: 그러면 YAML 파이프라인 정의에 배포 작업이 자동으로 추가됩니다.

1. 편집기에서 추가한 작업을 선택한 상태로 **Tab** 키를 두 번 눌러 4칸 들여쓰기를 적용합니다. 그러면 해당 작업이 **steps** 작업의 하위 작업으로 나열됩니다.

    > **참고**: 이 랩에서는 **packageForLinux** 매개 변수를 사용하면 안 되지만 Windows나 Linux에서는 해당 매개 변수를 사용할 수 있습니다.

    > **참고**: 기본적으로 이 두 단계는 독립적으로 실행됩니다. 그러므로 첫 단계의 빌드 출력을 추가로 변경하지 않으면 두 번째 단계에서 사용하지 못할 수도 있습니다. 여기서는 이러한 변경을 구현하기 위해 빌드 단계 종료 시 빌드 출력을 게시하는 작업과, 배포 단계 시작 시에 해당 출력을 다운로드하는 작업을 각각 사용합니다.

1. 다른 작업을 추가하려면 빌드 단계 끝부분의 빈 줄에 커서를 놓습니다. (`task: VSBuild@1` 바로 아래)
1. **작업** 창에서 **빌드 아티팩트 게시** 작업을 검색하여 선택합니다.
1. **빌드 아티팩트 게시** 창에서 기본 설정을 수락하고 **추가**를 클릭합니다.

    > **참고**: 그러면 빌드 아티팩트가 다운로드 가능 위치에 **drop** 별칭으로 게시됩니다.

1. 편집기에서 추가한 작업을 선택한 상태로 **Tab** 키를 두 번 눌러 4칸 들여쓰기를 적용합니다. (아니면 작업이 위에 있는 것처럼 들여쓰여질 때까지 **Tab**을 누릅니다.)

    > **참고**: 해당 작업을 쉽게 읽을 수 있도록 앞뒤에 빈 줄을 추가할 수도 있습니다.

1. **배포** 단계 **steps** 노드 아래의 첫 줄에 커서를 놓습니다.
1. **작업** 창에서 **빌드 아티팩트 다운로드** 작업을 검색하여 선택합니다.
1. **추가**를 클릭합니다.
1. 편집기에서 추가한 작업을 선택한 상태로 **Tab** 키를 두 번 눌러 4칸 들여쓰기를 적용합니다.

    > **참고**: 여기서도 해당 작업을 쉽게 읽을 수 있도록 앞뒤에 빈 줄을 추가할 수도 있습니다.

1. `artifactName`을 `drop`으로 지정하는 속성을 다운로드 작업에 추가합니다(공백이 일치해야 함).

    ```
    artifactName: 'drop'
    ```

1. **저장**을 클릭하고 **저장** 창에서 **저장**을 다시 클릭하여 변경 내용을 마스터 분기에 직접 커밋합니다.

    > **참고**: 이 작업을 수행하면 새 빌드가 자동으로 트리거됩니다.

1. 파이프라인이 다음 예와 비슷하게 보일 것입니다(**마지막 작업에서 본인의 구독 및 웹앱 참조**).

     > **참고**: 제대로 작동하려면 들여쓰기를 수정해야 합니다. 복사하여 붙여넣으면 수정할 수 있습니다.

    ```
    trigger:
    - master

    stages:
    - stage: Build
      jobs:
      - job: Build
        pool:
            vmImage: 'windows-2019'

        variables:
            solution: '**/*.sln'
            buildPlatform: 'Any CPU'
            buildConfiguration: 'Release'

        steps:
        - task: NuGetToolInstaller@1

        - task: NuGetCommand@2
          inputs:
            restoreSolution: '$(solution)'

        - task: VSBuild@1
          inputs:
            solution: '$(solution)'
            msbuildArgs: '/p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.artifactStagingDirectory)"'
            platform: '$(buildPlatform)'
            configuration: '$(buildConfiguration)'

        - task: PublishBuildArtifacts@1
          inputs:
            PathtoPublish: '$(Build.ArtifactStagingDirectory)'
            ArtifactName: 'drop'
            publishLocation: 'Container'

    - stage: Deploy
      jobs:
      - job: Deploy
        pool:
            vmImage: 'windows-2019'
        steps:
        - task: DownloadBuildArtifacts@0
          inputs:
            buildType: 'current'
            downloadType: 'single'
            downloadPath: '$(System.ArtifactsDirectory)'
            artifactName: 'drop'
        - task: AzureRmWebAppDeployment@4
          inputs:
            ConnectionType: 'AzureRM'
            azureSubscription: 'YOUR-AZURE-SUBSCRIPTION'
            appType: 'webApp'
            WebAppName: 'YOUR-WEBAPP-NAME'
            packageForLinux: '$(System.ArtifactsDirectory)/drop/*.zip'
    ```

1. Azure DevOps 포털이 표시된 웹 브라우저 창의 세로 탐색 창에서 **Pipelines**를 선택합니다.
1. **파이프라인** 창에서 새로 구성한 파이프라인을 나타내는 항목을 클릭합니다.
1. 가장 최근의 실행을 클릭합니다(자동으로 시작됨).
1. **요약** 창에서 파이프라인 실행의 진행률을 모니터링합니다.
1. *이 실행을 배포까지 계속 진행하려면 이 파이프라인에 리소스 액세스 권한이 필요합니다.* 라는 메시지가 표시되면 **보기**를 클릭하고 **검토 대기 중** 대화 상자에서 **허용**을 클릭합니다. 그런 다음 **액세스를 허용하시겠습니까?** 창에서 **허용**을 다시 클릭합니다.
1. **요약** 창 아래쪽에서 **배포** 단계를 클릭하여 배포 세부 정보를 확인합니다.

    > **참고**: 작업이 완료되면 앱이 Azure 웹앱에 배포됩니다.

#### <a name="task-4-review-the-deployed-site"></a>작업 4: 배포된 사이트 검토

1. Azure Portal이 표시된 웹 브라우저 창으로 다시 전환한 다음 Azure 웹앱 속성이 표시된 블레이드로 이동합니다.
1. Azure 웹앱 블레이드에서 **개요**를 클릭하고 개요 블레이드에서 **찾아보기**를 클릭하여 새 웹 브라우저 탭에서 사이트를 엽니다.
1. 배포한 사이트가 새 브라우저 탭에서 정상적으로 로드되는지 확인합니다.

### <a name="exercise-2-remove-the-azure-lab-resources"></a>연습 2: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### <a name="task-1-remove-the-azure-lab-resources"></a>작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m11l01-RG')].name" --output tsv
    ```

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m11l01-RG')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## <a name="review"></a>검토

이 랩에서는 Azure DevOps에서 YAML을 사용하여 CI/CD 파이프라인을 코드로 구성했습니다.
