---
lab:
  title: Docker 컨테이너를 Azure App Service 웹앱에 배포
  module: 'Module 03: Implement CI with Azure Pipelines and GitHub Actions'
---

# Docker 컨테이너를 Azure App Service 웹앱에 배포

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://learn.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독에서 Owner 또는 Contributor 역할이 지정된 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://learn.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://learn.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

## 랩 개요

이 랩에서는 Azure DevOps CI/CD 파이프라인을 사용하여 사용자 지정 Docker 이미지를 빌드하고, Azure Container Registry에 푸시하고, Azure App Service에 컨테이너로 배포하는 방법을 배웁니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Microsoft에서 호스트하는 Linux 에이전트를 사용하여 사용자 지정 Docker 이미지 빌드
- Azure Container Registry에 이미지 푸시
- Azure DevOps를 사용하여 Docker 이미지를 Azure App Service에 컨테이너로 배포

## 예상 소요 시간: 30분

## Instructions

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 **작업 항목 프로세스** 드롭다운에서 **스크럼**을 선택합니다. **만들기**를 클릭합니다.

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **가져오기**를 클릭합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git 을 붙여넣고 **가져오기**를 클릭합니다.

2. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용하여 개발하는 **.devcontainer** 폴더 컨테이너 설정(VS Code 또는 GitHub Codespaces에서 로컬로).
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

#### 작업 3: (완료된 경우 건너뛰기) 기본(main) 분기를 기본 분기로 설정

1. **리포지토리>분기**로 이동합니다.
2. **기본** 분기를 마우스로 가리킨 다음 열 오른쪽에 있는 줄임표를 클릭합니다.
3. **기본 분기 설정을** 클릭합니다.

### 연습 1: 서비스 연결 관리

이 연습에서는 Azure 구독을 사용하여 서비스 연결을 구성한 다음, CI 파이프라인을 가져오고 실행합니다.

#### 작업 1: (완료된 경우 건너뛰기) 서비스 연결 관리

작업에서 태스크를 실행하기 위해 Azure Pipelines에서 외부 및 원격 서비스로의 연결을 만들 수 있습니다.

이 작업에서는 Azure CLI를 사용하여 서비스 주체를 만듭니다. 그러면 Azure DevOps에서 다음을 수행할 수 있습니다.

- Azure 구독에 리소스를 배포합니다.
- docker 이미지를 Azure Container Registry 푸시합니다.
- Azure App Service Azure Container Registry docker 이미지를 끌어올 수 있도록 역할 할당을 추가합니다.

> **참고**: 서비스 주체가 이미 있으면 다음 작업을 바로 진행해도 됩니다.

Azure Pipelines에서 Azure 리소스를 배포하려면 서비스 주체가 필요합니다.

프로젝트 설정 페이지에서 새 서비스 연결을 만들거나 파이프라인 정의 내에서 Azure 구독에 연결하면 Azure Pipelines에서 서비스 주체가 자동으로 작성됩니다(자동 옵션). Azure Portal이나 Azure CLI를 통해 서비스 주체를 수동으로 만든 다음 여러 프로젝트에서 재사용할 수도 있습니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하여 [**Azure Portal**](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 Azure 구독의 Owner 역할, 그리고 해당 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할을 가진 사용자 계정으로 로그인합니다.
2. Azure Portal의 페이지 위쪽 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
3. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.

   >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다.

4. **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 Azure 구독 ID 특성의 값을 검색합니다.

    ```bash
    subscriptionName=$(az account show --query name --output tsv)
    subscriptionId=$(az account show --query id --output tsv)
    echo $subscriptionName
    echo $subscriptionId
    ```

    > **참고**: 두 값을 모두 텍스트 파일에 복사해 두세요. 이 랩의 뒷부분에서 계정 이름이 필요합니다.

5. **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 리소스 주체를 만듭니다.

    ```bash
    az ad sp create-for-rbac --name sp-az400-azdo --role contributor --scopes /subscriptions/$subscriptionId
    ```

    > **참고**: 이 명령을 실행하면 JSON 출력이 생성됩니다. 텍스트 파일에 출력을 복사해 두세요. 이 랩의 후반부에서 필요합니다.

6. 그 다음, 랩 컴퓨터에서 웹 브라우저를 시작하여 Azure DevOps **eShopOnWeb** 프로젝트로 이동합니다. **프로젝트 설정 > 서비스 연결(파이프라인 아래)** 및 **새 서비스 연결**을 클릭합니다.
7. **새 서비스 연결** 블레이드에서 **Azure Resource Manager** 및 **다음**을 선택합니다(아래로 스크롤해야 할 수 있음).
8. **서비스 주체(수동)** 를 선택하고 **다음**을 클릭합니다.
9. 이전 단계에서 수집한 정보를 사용하여 비어 있는 필드를 채웁니다.
    - 구독 ID 및 이름입니다.
    - 서비스 주체 ID(또는 clientId), 키(또는 암호) 및 TenantId.
    - **서비스 연결 이름**에 **azure-connection**을 입력합니다. 이 이름은 Azure 구독과 통신하기 위해 Azure DevOps Service 연결이 필요한 경우 YAML 파이프라인에서 참조됩니다.

10. **확인 및 저장**을 클릭합니다.

### 연습 2: CI 파이프라인 가져오기 및 실행

이 연습에서는 CI 파이프라인을 가져오고 실행합니다.

#### 작업 1: CI 파이프라인 가져오기 및 실행

1. **파이프라인 > 파이프라인**으로 이동합니다.
2. **새 파이프라인** 단추를 클릭합니다.
3. **Azure Repos Git(Yaml)** 을 선택합니다.
4. **eShopOnWeb** 리포지토리를 선택합니다.
5. **기존 Azure Pipelines YAML 파일**을 선택합니다.
6. **/.ado/eshoponweb-ci-docker.yml** 파일을 선택한 다음 **계속**을 클릭합니다.
7. YAML 파이프라인 정의에서 다음을 사용자 지정합니다.
   - **YOUR-SUBSCRIPTION-ID**를 Azure 구독 ID로 바꿉니다.
   - 파이프라인에서 만들 리소스 그룹 이름이 있는 **rg-az400-container-NAME**입니다(기존 리소스 그룹일 수도 있음).

8. **저장 및 실행**을 클릭하고 파이프라인이 성공적으로 실행될 때까지 기다립니다.

    > **참고**: 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

    CI 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **리소스**: 다음 작업에 사용할 리포지토리 파일을 다운로드합니다.
    - **AzureResourceManagerTemplateDeployment**: bicep 템플릿을 사용하여 Azure Container Registry를 배포합니다.
    - **PowerShell**: 이전 작업의 출력에서 **ACR 로그인 서버** 값을 검색하고 새 매개 변수 **acrLoginServer**를 만듭니다.
    - [**Docker**](https://learn.microsoft.com/azure/devops/pipelines/tasks/reference/docker-v0?view=azure-pipelines) **- 빌드**: Docker 이미지를 빌드하고 두 가지 태그 만들기(최신 및 현재 BuildID)
    - **Docker - 푸시**: Azure Container Registry로 이미지 푸시

9. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **파이프라인 > 파이프라인으로** 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/이동** 옵션을 클릭합니다. 이름을 **eshoponweb-ci-docker**로 설정하고 **저장**을 클릭합니다.

10. [**Azure Portal**](https://portal.azure.com)로 이동한 후 최근에 만든 리소스 그룹에서 Azure Container Registry를 검색합니다(이름이 **rg-az400-container-NAME**이어야 함). **eshoponweb/web**이 만들어졌고 두 가지 태그(둘 중 하나는 **최신** 태그)가 포함되었는지 확인합니다.

### 연습 3: CD 파이프라인 가져오기 및 실행

이 연습에서는 Azure 구독을 사용하여 서비스 연결을 구성한 다음, CD 파이프라인을 가져오고 실행합니다.

#### 작업 1: 새 역할 할당 추가

이 작업에서는 새 역할 할당을 추가하여 Azure App Service가 Azure Container Registry에서 Docker 이미지를 끌어올 수 있도록 허용합니다.

1. [**Azure Portal**](https://portal.azure.com)로 이동합니다.
2. Azure Portal의 페이지 위쪽 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
3. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.
4. **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 Azure 구독 ID 특성의 값을 검색합니다.

    ```sh
    spId=$(az ad sp list --display-name sp-az400-azdo --query "[].id" --output tsv)
    echo $spId
    roleName=$(az role definition list --name "User Access Administrator" --query "[0].name" --output tsv)
    echo $roleName
    ```

5. 서비스 주체 ID와 역할 이름을 가져온 다음, 이 명령을 실행하여 역할 할당을 만들어 보겠습니다(**rg-az400-container-NAME**을 리소스 그룹 이름으로 바꿈).

    ```sh
    az role assignment create --assignee $spId --role $roleName --resource-group "rg-az400-container-NAME"
    ```

이제 명령 실행이 성공했는지 확인하는 JSON 출력이 표시됩니다.

#### 작업 2: CD 파이프라인 가져오기 및 실행

이 작업에서는 CI 파이프라인을 가져오고 실행합니다.

1. **파이프라인 > 파이프라인**으로 이동합니다.
2. **새 파이프라인** 단추를 클릭합니다.
3. **Azure Repos Git(Yaml)** 을 선택합니다.
4. **eShopOnWeb** 리포지토리를 선택합니다.
5. **기존 Azure Pipelines YAML 파일**을 선택합니다.
6. **/.ado/eshoponweb-cd-webapp-docker.yml** 파일을 선택한 다음 **계속**을 클릭합니다.
7. YAML 파이프라인 정의에서 다음을 사용자 지정합니다.
   - **YOUR-SUBSCRIPTION-ID**를 Azure 구독 ID로 바꿉니다.
   - **rg-az400-container-NAME**, 랩에서 이전에 정의된 리소스 그룹 이름이 포함됨.

8. **저장 및 실행**을 클릭하고 파이프라인이 성공적으로 실행될 때까지 기다립니다.

    > **참고**: 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

    CI 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **리소스**: 다음 작업에 사용할 리포지토리 파일을 다운로드합니다.
    - **AzureResourceManagerTemplateDeployment**: bicep 템플릿을 사용하여 Azure App Service를 배포합니다.
    - **AzureResourceManagerTemplateDeployment**: Bicep을 사용하여 역할 할당을 추가합니다.

9. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **파이프라인>파이프라인으로** 이동하고 최근에 만든 파이프라인을 마우스로 가리킵니다. 줄임표 및 **이름 바꾸기/이동** 옵션을 클릭합니다. 이름을 **eshoponweb-cd-webapp-docker**로 설정하고 **저장**을 클릭합니다.

    > **참고 1**: **/.azure/bicep/webapp-docker.bicep** 템플릿을 사용하면 App Service 요금제, 시스템 할당 관리 ID가 사용 설정된 웹앱이 만들어지며 이전에 푸시된 Docker 이미지 **${acr.properties.loginServer}/eshoponweb/web:latest**를 참조합니다.

    > **참고 2**: **/.azure/bicep/webapp-to-acr-roleassignment.bicep** 템플릿을 사용하면 Docker 이미지를 검색할 수 있도록 AcrPull 역할이 있는 웹앱에 대한 새 역할 할당이 만들어집니다. 첫 번째 템플릿에서 이 작업을 수행할 수 있지만, 역할 할당이 전파되는 데 다소 시간이 걸릴 수 있으므로 두 작업을 별도로 수행하는 것이 좋습니다.

#### 작업 3: 솔루션 테스트

1. Azure Portal에서 최근에 만든 리소스 그룹으로 이동하면 이제 세 가지 리소스(Ap Service, App Service 요금제, Container Registry)가 표시됩니다.

1. App Service로 이동한 다음, **찾아보기**를 클릭하면 웹 사이트로 이동합니다.

축하합니다! 이 연습에서는 사용자 지정 Docker 이미지를 사용하여 웹 사이트를 배포했습니다.

### 연습 4: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### 작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
2. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'rg-az400-container-')].name" --output tsv
    ```

3. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'rg-az400-container-')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## 검토

이 랩에서는 Azure DevOps CI/CD 파이프라인을 사용하여 사용자 지정 Docker 이미지를 빌드하고, Azure Container Registry에 푸시하고, Azure App Service에 컨테이너로 배포하는 방법을 배웠습니다.
