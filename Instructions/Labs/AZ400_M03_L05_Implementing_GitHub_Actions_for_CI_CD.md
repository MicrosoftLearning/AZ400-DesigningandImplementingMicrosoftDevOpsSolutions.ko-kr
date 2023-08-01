---
lab:
  title: CI/CD에 대한 GitHub Actions 구현
  module: 'Module 03: Implement CI with Azure Pipelines and GitHub Actions'
---

# CI/CD에 대한 GitHub Actions 구현

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독에서 Owner 또는 Contributor 역할이 지정된 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

- **이 랩에 사용할 수 있는 GitHub 계정이 아직 없으면** [새 GitHub 계정 등록](https://github.com/join)에서 제공되는 지침에 따라 새 계정을 만듭니다.

## 랩 개요

이 랩에서는 Azure 웹앱을 배포하는 GitHub Action 워크플로를 구현하는 방법을 배웁니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- CI/CD에 대한 GitHub Action 워크플로를 구현합니다.
- GitHub Action 워크플로의 기본 특성을 설명합니다.

## 예상 소요 시간: 40분

## Instructions

### 연습 0: GitHub 리포지토리로 eShopOnWeb 가져오기

이 연습에서는 기존 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb) 리포지토리 코드를 사용자의 고유한 GitHub 프라이빗 리포지토리로 가져옵니다.

리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용하여 개발하는 **.devcontainer** 폴더 컨테이너 설정(VS Code 또는 GitHub Codespaces에서 로컬로).
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

#### 작업 1: GitHub에서 퍼블릭 리포지토리 만들기 및 eShopOnWeb 가져오기

이 작업에서는 빈 공용 GitHub 리포지토리를 만들고 기존 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb) 리포지토리를 가져옵니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [GitHub 웹 사이트](https://github.com/)로 이동한 후 계정을 사용하여 로그인한 다음, **새로** 만들기를 클릭하여 새 리포지토리를 만듭니다.

    ![리포지토리 만들기](images/github-new.png)

2. **새 리포지토리 만들기** 페이지에서 **리포지토리 가져오기** 링크(페이지 제목 아래)를 클릭합니다.

    > 참고: https://github.com/new/import 에서 가져오기 웹 사이트를 직접 열 수도 있습니다.

3. **GitHub로 프로젝트 가져오기** 페이지에 있는 항목

    | 필드 | 값 |
    | --- | --- |
    | 이전 리포지토리의 복제 URL| https://github.com/MicrosoftLearning/eShopOnWeb |
    | 소유자 | 계정 별칭 |
    | 리포지토리 이름 | eShopOnWeb |
    | 개인 정보 취급 방침 | **공용** |

4. **가져오기 시작**을 클릭하고 리포지토리가 준비될 때까지 기다립니다.

5. 리포지토리 페이지에서 **설정**으로 이동하여 **작업 > 일반**을 클릭하고 **모든 작업 및 재사용 가능한 워크플로 허용** 옵션을 선택합니다. **Save**를 클릭합니다.

    ![GitHub Actions 사용](images/enable-actions.png)

### 연습 1: GitHub 리포지토리 및 Azure 액세스 설정

이 연습에서는 Azure 서비스 주체를 만들어 GitHub가 GitHub Actions에서 Azure 구독에 액세스할 수 있는 권한을 부여해보겠습니다. 또한 웹 사이트를 빌드, 테스트하고 Azure에 배포하는 GitHub 워크플로를 설정해봅니다.

#### 작업 1: Azure 서비스 주체 만들기 및 GitHub 비밀로 저장

이 작업에서는 GitHub에서 원하는 리소스를 배포하는 데 사용되는 Azure 서비스 주체를 만듭니다. 또는 [Azure에서 OpenID 연결](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure)을 비밀 없는 인증 메커니즘으로 사용할 수도 있습니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure Portal(https://portal.azure.com/) )을 엽니다.
2. 포털에서 **리소스 그룹**을 찾아 클릭합니다.
3. **+ 만들기**를 클릭하여 연습을 위한 새 리소스 그룹을 만듭니다.
4. **리소스 그룹 만들기** 탭에서 리소스 그룹의 이름을 **rg-az400-eshoponweb-NAME**으로 설정합니다(고유한 별칭으로 NAME 바꾸기). **검토+만들기 > 만들기**를 클릭합니다.
5. Azure Portal에서 검색 창 옆에 있는 **Cloud Shell**을 엽니다.

    > 참고: Cloud Shell을 처음 열 경우, [영구 스토리지](https://learn.microsoft.com/azure/cloud-shell/persisting-shell-storage)를 구성해야 합니다.

6. 터미널이 **Bash** 모드에서 실행 중인지 확인하고 다음 명령을 실행하여 **SUBSCRIPTION-ID 및 RESOURCE-GROUP**을 **** 사용자 고유의 식별자(둘 다 리소스 그룹의 **개요** 페이지에서 찾을 수 있음)로 바꿉니다.

    `az ad sp create-for-rbac --name GH-Action-eshoponweb --role contributor --scopes /subscriptions/SUBSCRIPTION-ID/resourceGroups/RESOURCE-GROUP --sdk-auth`

    > 참고: 한 줄로 입력하거나 붙여넣었는지 확인합니다.
    > 참고: 이 명령을 실행하면 이전에 만든 리소스 그룹에 대한 기여자 액세스 권한이 있는 서비스 주체가 생성됩니다. 이러한 방식을 사용하면 GitHub Actions가 이 리소스 그룹하고만 상호 작용하는 데 필요한 권한만 갖게 됩니다(구독의 나머지 부분은 포함 안 됨).

7. 명령은 JSON 개체를 출력하고 나중에 워크플로에 대한 GitHub 비밀로 사용합니다. JSON을 복사합니다. JSON에는 Azure AD 애플리케이션 ID(서비스 주체)의 이름으로 Azure에 대해 인증하는 데 사용되는 식별자가 포함되어 있습니다.

    ```JSON
        {
            "clientId": "<GUID>",
            "clientSecret": "<GUID>",
            "subscriptionId": "<GUID>",
            "tenantId": "<GUID>",
            (...)
        }
    ```
8. 또한 다음 명령을 실행하여 나중에 배포할 **Azure App Service** 리소스 공급자를 등록해야 합니다.
   ```bash
   az provider register --namespace Microsoft.Web
   ``` 
10. 브라우저 창에서 **eShopOnWeb** GitHub 리포지토리로 돌아갑니다.
11. 리포지토리 페이지에서 **설정**으로 이동하여 **작업 > 비밀 및 변수를** 클릭합니다. **새 리포지토리 비밀**을 클릭합니다.
    - 이름: **AZURE_CREDENTIALS**
    - 비밀: **이전에 복사한 JSON 개체 붙여넣기**(GitHub는 [azure/login](https://github.com/Azure/login) 작업에서 사용된 동일한 이름으로 여러 개의 비밀을 보관할 수 있음)

12. **비밀 추가**를 클릭합니다. 이제 GitHub Actions 리포지토리 비밀을 사용하여 서비스 주체를 참조할 수 있습니다.

#### 작업 2: GitHub 워크플로 수정 및 실행

이 작업에서는 지정된 GitHub 워크플로를 수정하고 실행하여 사용자의 고유한 구독에 솔루션을 배포합니다.

1. 브라우저 창에서 **eShopOnWeb** GitHub 리포지토리로 돌아갑니다.
2. 리포지토리 페이지에서 **코드**로 이동하고 **eShopOnWeb/.github/workflows/eshoponweb-cicd.yml** 파일을 엽니다. 이 워크플로는 지정된 .NET 6 웹 사이트 코드에 대한 CI/CD 프로세스를 정의합니다.
3. **on** 섹션의 주석 처리를 제거합니다('#' 삭제). 워크플로는 기본 분기에 대한 모든 푸시와 함께 트리거되며 수동 트리거('workflow_dispatch')도 제공합니다.
4. **env** 섹션에서 다음과 같이 변경합니다.
    - **RESOURCE-GROUP** 변수에서 **NAME**을 바꿉니다. 이전 단계에서 만든 것과 동일한 리소스 그룹이어야 합니다.
    - (선택 사항) [LOCATION](https://azure.microsoft.com/explore/global-infrastructure/geographies)에 가장 가까운 **Azure 지역**을 선택할 수 있습니다. 예를 들어 'eastus', 'eastasia', 'westus' 등이 있습니다.
    - **SUBSCRIPTION-ID**에서 **YOUR-SUBS-ID**를 바꿉니다.
    - **WEBAPP-NAME**의 **NAME**을 고유한 별칭으로 바꿉니다. 이는 Azure App Service를 사용하는 전역적으로 고유한 웹 사이트를 만드는 데 사용됩니다.
5. 워크플로를 꼼꼼히 읽으면 이해에 도움이 되는 주석이 제공됩니다.

6. **커밋 시작** 및 **변경 내용 커밋**을 클릭하여 기본값을 그대로 둡니다(기본 분기 변경). 워크플로가 자동으로 실행됩니다.

#### 작업 3: GitHub 워크플로 실행 검토

이 작업에서는 GitHub 워크플로 실행을 검토합니다.

1. 브라우저 창에서 **eShopOnWeb** GitHub 리포지토리로 돌아갑니다.
2. 리포지토리 페이지에서 **작업**으로 이동하면 실행하기 전에 워크플로 설정이 표시됩니다. 타일을 클릭합니다.

    ![GitHub 워크플로 진행 중](images/gh-actions.png)

3. 워크플로가 완료될 때까지 기다립니다. **요약**에서는 두 가지 워크플로 작업, 즉 실행에서 유지된 상태 및 아티팩트를 볼 수 있습니다. 각 작업을 클릭하여 로그를 검토할 수 있습니다.

    ![성공적인 워크플로](images/gh-action-success.png)

4. 브라우저 창에서 Azure Portal(https://portal.azure.com/) )로 돌아갑니다. 이전에 만든 리소스 그룹을 엽니다. GitHub 작업이 bicep 템플릿을 사용하여 Azure App Service 요금제 + App Service를 생성한 것을 볼 수 있습니다. 게시된 웹 사이트가 App Service를 열고 **찾아보기**를 클릭하는 것을 볼 수 있습니다.

    ![WebApp 찾아보기](images/browse-webapp.png)

#### (선택 사항) 작업 4: GitHub 환경을 사용하여 수동 승인 사전 배포 추가

이 작업에서는 GitHub 환경을 사용하여 워크플로의 배포 작업에 정의된 작업을 실행하기 전에 수동 승인을 요청합니다.

1. 리포지토리 페이지에서 **코드**로 이동하고 **eShopOnWeb/.github/workflows/eshoponweb-cicd.yml** 파일을 엽니다.
2. **배포** 작업 섹션에서 **개발**이라는 **환경**의 참조를 찾을 수 있습니다. GitHub에서 사용한 [환경](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)은 대상에 대한 보호 규칙(및 비밀)을 추가합니다.

3. 리포지토리 페이지에서 **설정**으로 이동하여 **환경**을 열고 **새 환경**을 클릭합니다.
4. 이름을 **개발**로 지정하고 **환경 구성**을 클릭합니다.
5. **개발 구성** 탭에서 **필수 검토자** 옵션을 선택하고 GitHub 계정을 검토자로 선택합니다. **보호 규칙 저장**을 클릭합니다.
6. 이제 보호 규칙을 테스트할 수 있습니다. 리포지토리 페이지에서 **작업**으로 이동하고 **eShopOnWeb 빌드 및 테스트** 워크플로를 클릭한 다음, **워크플로 실행 > 워크플로 실행**을 클릭하여 수동으로 실행합니다.

    ![수동 트리거 워크플로](images/gh-manual-run.png)

7. 워크플로의 시작된 실행을 클릭하고 **buildandtest** 작업이 완료될 때까지 기다립니다. **배포** 작업에 도달하면 검토 요청이 표시됩니다.

8. **배포 검토**를 클릭하고 **개발**을 선택한 다음 **승인 및 배포**를 클릭합니다.

    ![승인](images/gh-approve.png)

9. 워크플로는 **배포** 작업 실행을 따른 후 완료됩니다.

### 연습 2: Azure 랩 리소스 제거

이 연습에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
2. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'rg-az400-eshoponweb')].name" --output tsv
    ```

3. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'rg-az400-eshoponweb')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## 검토

이 랩에서는 Azure 웹앱을 배포하는 GitHub Action 워크플로를 구현했습니다.
