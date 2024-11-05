---
lab:
  title: Docker 컨테이너를 Azure App Service 웹앱에 배포
  module: 'Module 02: Implement CI with Azure Pipelines and GitHub Actions'
---

# Docker 컨테이너를 Azure App Service 웹앱에 배포

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://learn.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독에서 기여자 또는 소유자 역할이 있는 Microsoft 계정 또는 Microsoft Entra 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://learn.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://learn.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

## 랩 개요

이 랩에서는 Azure DevOps CI/CD 파이프라인을 사용하여 사용자 지정 Docker 이미지를 빌드하고, Azure Container Registry에 푸시하고, Azure App Service에 컨테이너로 배포하는 방법을 배웁니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Microsoft에서 호스트하는 Linux 에이전트를 사용하여 사용자 지정 Docker 이미지 빌드
- Azure Container Registry에 이미지 푸시
- Azure DevOps를 사용하여 Docker 이미지를 Azure App Service에 컨테이너로 배포

## 예상 소요 시간: 20분

## 지침

### 연습 0: (완료된 경우 건너뛰기)랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 **작업 항목 프로세스** 드롭다운에서 **스크럼**을 선택합니다. **만들기**를 클릭합니다.

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > Files**, **가져오기**를 클릭합니다. **Git 리포지토리 가져오기** 창에서 다음 URL <https://github.com/MicrosoftLearning/eShopOnWeb.git>을 붙여넣고 **가져오기**를 클릭합니다.

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

### 연습 1: CI 파이프라인 가져오기 및 실행

이 연습에서는 Azure 구독을 사용하여 서비스 연결을 구성한 다음, CI 파이프라인을 가져오고 실행합니다.

#### 작업 1: CI 파이프라인 가져오기 및 실행

1. **Pipelines > Pipelines**로 이동합니다.
1. **새 파이프라인** 단추 클릭(또는 이전에 만들어진 다른 파이프라인이 없는 경우 **파이프라인 만들기** 클릭).
1. **Azure Repos Git(YAML)** 을 선택합니다.
1. **eShopOnWeb** 리포지토리를 선택합니다.
1. **기존 Azure Pipelines YAML 파일**을 선택합니다.
1. **기본** 분기와 **/.ado/eshoponweb-ci-docker.yml** 파일을 선택한 다음, **계속**을 클릭합니다.
1. YAML 파이프라인 정의에서 다음을 사용자 지정합니다.
   - **YOUR-SUBSCRIPTION-ID**를 Azure 구독 ID로 바꿉니다.
   - **resourceGroup**를 서비스 연결을 만들 때 사용한 리소스 그룹 이름(예: **AZ400-RG1**)으로 바꿉니다.

1. 파이프라인 정의를 검토합니다. CI 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **리소스**: 다음 작업에서 사용되는 리포지토리 파일을 다운로드합니다.
    - **AzureResourceManagerTemplateDeployment**: bicep 템플릿을 사용하여 Azure Container Registry를 배포합니다.
    - **PowerShell**: 이전 작업의 출력에서 **ACR 로그인 서버** 값을 검색하고 새 매개 변수 **acrLoginServer**를 만듭니다.
    - [**Docker**](https://learn.microsoft.com/azure/devops/pipelines/tasks/reference/docker-v0?view=azure-pipelines) **- Build**: Docker 이미지를 빌드하고 두 가지 태그 만들기(최신 및 현재 BuildID)
    - **Docker - 푸시**: Azure Container Registry로 이미지 푸시

1. **저장 및 실행**을 클릭합니다.

1. 파이프라인 실행을 엽니다. "이 파이프라인은 실행을 계속 빌드하려면 리소스에 액세스할 권한이 필요합니다."라는 경고 메시지가 표시되면 **보기**를 클릭한 다음 **허용** 및 **허용**을 다시 클릭합니다. 이렇게 하면 파이프라인에서 Azure 구독에 액세스할 수 있습니다.

    > **참고**: 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

1. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **Pipelines > Pipelines**로 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/이동** 옵션을 클릭합니다. 이름을 **eshoponweb-ci-docker**로 설정하고 **저장**을 클릭합니다.

1. [**Azure Portal**](https://portal.azure.com)로 이동한 후 최근에 만든 리소스 그룹에서 Azure Container Registry를 검색합니다(이름이 **AZ400-RG1**이어야 함). 왼쪽에서 **서비스** 아래의 **의 리포지토리**를 클릭하고 **eshoponweb/web**이 만들어졌는지 확인합니다. 리포지토리 링크를 클릭하면 두 개의 태그(그중 하나는 **최신**)가 표시되어야 합니다. 이러한 태그는 푸시된 이미지입니다. 이 항목이 표시되지 않으면 파이프라인의 상태를 검사합니다.

### 연습 2: CD 파이프라인 가져오기 및 실행

이 연습에서는 Azure 구독을 사용하여 서비스 연결을 구성한 다음, CD 파이프라인을 가져오고 실행합니다.

#### 작업 1: CD 파이프라인 가져오기 및 실행

이 작업에서는 CD 파이프라인을 가져오고 실행합니다.

1. **Pipelines > Pipelines**로 이동합니다.
1. **새 파이프라인** 단추를 클릭합니다.
1. **Azure Repos Git(YAML)** 을 선택합니다.
1. **eShopOnWeb** 리포지토리를 선택합니다.
1. **기존 Azure Pipelines YAML 파일**을 선택합니다.
1. **기본** 분기와 **/.ado/eshoponweb-cd-webapp-docker.yml** 파일을 선택한 다음, **계속**을 클릭합니다.
1. YAML 파이프라인 정의에서 다음을 사용자 지정합니다.
   - **YOUR-SUBSCRIPTION-ID**를 Azure 구독 ID로 바꿉니다.
   - **resourceGroup**를 서비스 연결을 만들 때 사용한 리소스 그룹 이름(예: **AZ400-RG1**)으로 바꿉니다.
   - **위치**를 리소스를 배포할 Azure 지역으로 바꿉니다.

1. 파이프라인 정의를 검토합니다. CD 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **리소스**: 다음 작업에서 사용되는 리포지토리 파일을 다운로드합니다.
    - **AzureResourceManagerTemplateDeployment**: bicep 템플릿을 사용하여 Azure App Service를 배포합니다.
    - **AzureResourceManagerTemplateDeployment**: Bicep을 사용하여 역할 할당을 추가합니다.

1. **저장 및 실행**을 클릭합니다.

1. 파이프라인 실행을 엽니다. “이 파이프라인은 실행을 계속 배포하려면 리소스에 액세스할 권한이 필요합니다."라는 경고 메시지가 표시되면 **보기**를 클릭한 다음 **허용** 및 **허용**을 다시 클릭합니다. 이렇게 하면 파이프라인에서 Azure 구독에 액세스할 수 있습니다.

    > **참고**: 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

    > [!IMPORTANT]
    > 오류 메시지 "TF402455: 이 분기에 대한 푸시는 허용되지 않습니다. 끌어오기 요청을 사용하여 이 분기를 업데이트해야 합니다."가 표시되는 경우, 이전 랩에서 사용하도록 설정된 "최소 검토자 수 필요" 분기 보호 규칙의 선택을 취소해야 합니다.

1. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **Pipelines > Pipelines**로 이동하여 최근에 만든 파이프라인에 마우스를 가져갑니다. 줄임표 및 **이름 바꾸기/이동** 옵션을 클릭합니다. 이름을 **eshoponweb-cd-webapp-docker**로 설정하고 **저장**을 클릭합니다.

    > **참고 1**: **/infra/webapp-docker.bicep** 템플릿을 사용하면 App Service 요금제, 시스템 할당 관리 ID가 사용 설정된 웹앱이 만들어지며 이전에 푸시된 Docker 이미지 **${acr.properties.loginServer}/eshoponweb/web:latest**를 참조합니다.

    > **참고 2**: **/infra/webapp-to-acr-roleassignment.bicep** 템플릿을 사용하면 웹앱에 대한 새 역할 할당이 만들어지며, Docker 이미지를 검색할 수 있는 AcrPull 역할이 포함됩니다. 첫 번째 템플릿에서 이 작업을 수행할 수 있지만, 역할 할당이 전파되는 데 다소 시간이 걸릴 수 있으므로 두 작업을 별도로 수행하는 것이 좋습니다.

#### 작업 2: 솔루션 테스트

1. Azure Portal에서 최근에 만든 리소스 그룹으로 이동하면 이제 세 가지 리소스(Ap Service, App Service 요금제, Container Registry)가 표시됩니다.

1. App Service로 이동한 다음, **찾아보기**를 클릭하면 웹 사이트로 이동합니다.

> [!IMPORTANT]
> 불필요한 요금이 부과되지 않도록 Azure Portal에서 만든 리소스를 삭제하는 것을 잊지 마세요.

## 검토

이 랩에서는 Azure DevOps CI/CD 파이프라인을 사용하여 사용자 지정 Docker 이미지를 빌드하고, Azure Container Registry에 푸시하고, Azure App Service에 컨테이너로 배포하는 방법을 배웠습니다.
