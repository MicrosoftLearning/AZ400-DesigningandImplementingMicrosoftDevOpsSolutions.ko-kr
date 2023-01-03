---
lab:
  title: 동적 구성 및 기능 플래그 사용
  module: 'Module 05: Implement a secure continuous deployment using Azure Pipelines'
---

# <a name="enable-dynamic-configuration-and-feature-flags"></a>동적 구성 및 기능 플래그 사용

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://learn.microsoft.com/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독에서 Owner 또는 Contributor 역할이 지정된 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://learn.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://learn.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

## <a name="lab-overview"></a>랩 개요

[Azure App Configuration](https://learn.microsoft.com/azure/azure-app-configuration/overview)은 애플리케이션 설정 및 기능 플래그를 중앙에서 관리할 수 있는 서비스를 제공합니다. 최신 프로그램, 특히 클라우드에서 실행되는 프로그램은 일반적으로 많은 구성 요소가 분산되어 있습니다. 이러한 구성 요소 간에 구성 설정을 분산하면 애플리케이션 배포 중에 문제 해결이 어려운 오류가 발생할 수 있습니다. App Configuration을 사용하여 애플리케이션에 대한 모든 설정을 한 곳에서 저장하고 액세스를 보호합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- 동적 구성 사용
- 기능 플래그 관리

## <a name="estimated-timing-60-minutes"></a>예상 소요 시간: 60분

## <a name="instructions"></a>Instructions

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### <a name="task-1-skip-if-done-create-and-configure-the-team-project"></a>작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1.  랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 **작업 항목 프로세스** 드롭다운에서 **스크럼**을 선택합니다. **만들기**를 클릭합니다.

#### <a name="task-2-skip-if-done-import-eshoponweb-git-repository"></a>작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1.  랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **가져오기**를 클릭합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git 을 붙여넣고 **가져오기**를 클릭합니다.

1.  리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용(VS Code 또는 GitHub Codespaces에서 로컬로 사용)하여 개발하는 **.devcontainer** 폴더 컨테이너 설정
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

#### <a name="task-3-skip-if-done-set-main-branch-as-default-branch"></a>작업 3: (완료된 경우 건너뛰기) 기본(main) 분기를 기본 분기로 설정

1. **리포지토리 > 분기**로 이동합니다.
1. **main** 분기를 마우스로 가리킨 다음 열 오른쪽에 있는 줄임표를 클릭합니다.
1. **기본 분기로 설정**을 클릭합니다.

### <a name="exercise-1-skip-if-done-import-and-run-cicd-pipelines"></a>연습 1: (완료된 경우 건너뛰기) CI/CD 파이프라인 가져오기 및 실행

이 연습에서는 CI 파이프라인을 가져온 후 실행하고, Azure 구독을 사용하여 서비스 연결을 구성한 다음, CD 파이프라인을 가져오고 실행합니다.

#### <a name="task-1-import-and-run-the-ci-pipeline"></a>작업 1: CI 파이프라인 가져오기 및 실행

먼저 [eshoponweb-ci.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml)이라는 CI 파이프라인을 가져오겠습니다.

1. **파이프라인 > 파이프라인**으로 이동합니다.

1. **파이프라인 만들기** 단추를 선택합니다.

1. **Azure Repos Git(Yaml)** 을 선택합니다.

1. **eShopOnWeb** 리포지토리를 선택합니다.

1. **기존 Azure Pipelines YAML 파일**을 선택합니다.

1. **/.ado/eshoponweb-ci.yml** 파일을 선택한 다음 **계속**을 클릭합니다.

1. **실행** 단추를 클릭하여 파이프라인을 실행합니다.

1. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **파이프라인 > 파이프라인으로** 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/제거** 옵션을 클릭합니다. 이름을 **eshoponweb-ci**로 설정하고 **저장**을 클릭합니다.

#### <a name="task-2-manage-the-service-connection"></a>작업 2: 서비스 연결 관리

작업에서 태스크를 실행하기 위해 Azure Pipelines에서 외부 및 원격 서비스로의 연결을 만들 수 있습니다.

이 작업에서는 Azure CLI를 사용하여 서비스 주체를 만듭니다. 그러면 Azure DevOps에서 다음을 수행할 수 있습니다.
- Azure 구독에서 리소스 배포
- eShopOnWeb 애플리케이션 배포

> **참고**: 서비스 주체가 이미 있으면 다음 작업을 바로 진행해도 됩니다.

Azure Pipelines에서 Azure 리소스를 배포하려면 서비스 주체가 필요합니다.

프로젝트 설정 페이지에서 새 서비스 연결을 만들거나 파이프라인 정의 내에서 Azure 구독에 연결하면 Azure Pipelines에서 서비스 주체가 자동으로 작성됩니다(자동 옵션). Azure Portal이나 Azure CLI를 통해 서비스 주체를 수동으로 만든 다음 여러 프로젝트에서 재사용할 수도 있습니다.

1.  랩 컴퓨터에서 웹 브라우저를 시작하여 [**Azure Portal**](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 Azure 구독의 Owner 역할, 그리고 해당 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할을 가진 사용자 계정으로 로그인합니다.
1.  Azure Portal의 페이지 위쪽 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
1.  **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다. 

   >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다. 

1.  **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 Azure 구독 ID 특성의 값을 검색합니다. 

    ```sh
    subscriptionName=$(az account show --query name --output tsv)
    subscriptionId=$(az account show --query id --output tsv)
    echo $subscriptionName
    echo $subscriptionId
    ```

    > **참고**: 두 값을 모두 텍스트 파일에 복사해 두세요. 이 랩의 뒷부분에서 계정 이름이 필요합니다.

1.  **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 리소스 주체를 만듭니다.

    ```sh
    az ad sp create-for-rbac --name sp-az400-azdo --role contributor --scopes /subscriptions/$subscriptionId
    ```

    > **참고**: 이 명령을 실행하면 JSON 출력이 생성됩니다. 텍스트 파일에 출력을 복사해 두세요. 이 랩의 후반부에서 필요합니다.

1. 그 다음, 랩 컴퓨터에서 웹 브라우저를 시작하여 Azure DevOps **eShopOnWeb** 프로젝트로 이동합니다. **프로젝트 설정 > 서비스 연결(파이프라인 아래)** 및 **새 서비스 연결**을 클릭합니다.

1. **새 서비스 연결** 블레이드에서 **Azure Resource Manager** 및 **다음**을 선택합니다(아래로 스크롤해야 할 수 있음).

1. **서비스 주체(수동)** 를 선택하고 **다음**을 클릭합니다.

1. 이전 단계에서 수집한 정보를 사용하여 비어 있는 필드를 채웁니다.
    - 구독 ID 및 이름
    - 서비스 주체 ID(또는 clientId), 키(또는 암호) 및 TenantId.
    - **서비스 연결 이름**에 **azure subs**를 입력합니다. 이 이름은 Azure 구독과 통신하기 위해 Azure DevOps Service 연결이 필요한 경우 YAML 파이프라인에서 참조됩니다.

1. **확인 및 저장**을 클릭합니다.

#### <a name="task-3-import-and-run-the-cd-pipeline"></a>작업 3: CD 파이프라인 가져오기 및 실행

이름이 [eshoponweb-cd-webapp-code.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-cd-webapp-code.yml)인 CD 파이프라인을 가져오겠습니다.

1. **파이프라인 > 파이프라인**으로 이동합니다.

1. **새 파이프라인** 단추를 클릭합니다.

1. **Azure Repos Git(Yaml)** 을 선택합니다.

1. **eShopOnWeb** 리포지토리를 선택합니다.

1. **기존 Azure Pipelines YAML 파일**을 선택합니다.

1. **/.ado/eshoponweb-cd-webapp-code.yml** 파일을 선택한 다음 **계속**을 클릭합니다.

1. YAML 파이프라인 정의에서 다음을 사용자 지정합니다.
- **YOUR-SUBSCRIPTION-ID**를 Azure 구독 ID로 바꿉니다.
- **az400eshop-NAME** 전역적으로 고유한 이름을 설정하려면 NAME을 바꿉니다.
- **AZ400-EWebShop-NAME**, 랩에서 이전에 정의된 리소스 그룹 이름이 포함됨.

1. **저장 및 실행**을 클릭하고 파이프라인이 성공적으로 실행될 때까지 기다립니다.

    > **참고**: 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

    CD 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **리소스**: CI 파이프라인 완료 시 자동으로 트리거되도록 준비됩니다. 또한 bicep 파일에 대한 리포지토리를 다운로드합니다.
    - **AzureResourceManagerTemplateDeployment**: bicep 템플릿을 사용하여 Azure 웹앱을 배포합니다.

1. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **파이프라인 > 파이프라인으로** 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/제거** 옵션을 클릭합니다. 이름을 **eshoponweb-cd-webapp-code**로 설정하고 **저장**을 클릭합니다.

### <a name="exercise-2-manage-azure-app-configuration"></a>연습 2: Azure App Configuration 관리

이 연습에서는 Azure에서 App Configuration 리소스를 만들고, 관리 ID를 사용하도록 설정한 다음 전체 솔루션을 테스트합니다.

>참고: 이 연습에서는 코딩 기술이 필요하지 않습니다. 웹 사이트의 코드는 이미 Azure App Configuration 기능을 구현합니다.
애플리케이션에서 이를 구현하는 방법을 알고 싶다면 [ASP.NET Core 앱에서 동적 구성 사용](https://learn.microsoft.com/azure/azure-app-configuration/enable-dynamic-configuration-aspnet-core) 및 [Azure App Configuration에서 기능 플래그 관리](https://learn.microsoft.com/azure/azure-app-configuration/manage-feature-flags) 자습서를 살펴보십시오.

#### <a name="task-1-create-the-app-configuration-resource"></a>작업 1: App Configuration 리소스 만들기

1. Azure Portal에서 **App Configuration** 서비스를 검색합니다.
1. **앱 구성 만들기**를 클릭한 후 다음을 선택합니다.
    - Azure 구독
    - 이전에 만든 리소스 그룹(이름이 **AZ400-EWebShop-NAME**이어야 함).
    - 위치
    - 고유한 이름(예: **appcs-NAME-REGION**)
    - **무료** 가격 책정 계층을 선택합니다.
1. **검토 + 만들기**와 **만들기**를 차례로 클릭합니다.
1. App Configuration 서비스를 만든 후 **개요**로 이동하고 **엔드포인트** 값을 복사/저장합니다.

#### <a name="task-2-enable-managed-identity"></a>작업 2: 관리 ID 사용

1. 파이프라인을 사용하여 배포된 웹앱으로 이동합니다(이름이 **az400-webapp-NAME**이어야 함).
1. **설정** 섹션에서 **ID**를 클릭한 다음, **시스템 할당** 섹션에서 상태를 **켜기**로 전환한 후 **저장 > 예**를 클릭하고 작업이 완료될 때까지 몇 초 정도 기다립니다.
1. App Configuration 서비스로 돌아간 후 **액세스 제어**를 클릭한 다음 **역할 할당 추가**를 클릭합니다.
1. **역할** 섹션에서 **App Configuration 데이터 판독기**를 선택합니다.
1. **멤버** 섹션에서 **ID 관리**를 선택한 다음, 웹앱의 관리 ID를 선택합니다(이름이 서로 같아야 함).
1. **검토 및 만들기**를 클릭합니다.

#### <a name="task-3-configure-the-web-app"></a>작업 3: 웹앱 구성

웹 사이트가 App Configuration에 액세스하고 있는지 확인하려면 해당 구성을 업데이트해야 합니다.
1. 웹앱으로 돌아갑니다.
1. **설정** 섹션에서 **구성**을 클릭합니다.
1. 새 애플리케이션 설정 두 가지를 추가합니다.
    - 첫 번째 앱 설정
        - **이름:** UseAppConfig
        - **값:** true
    - 두 번째 앱 설정
        - **이름:** AppConfigEndpoint
        - **값:** *이전에 App Configuration 엔드포인트에서 저장/복사한 값. 다음과 같이 표시됨 https://appcs-NAME-REGION.azconfig.io*
1. **확인**을 클릭한 다음, **저장**을 클릭하고 설정이 업데이트될 때까지 기다립니다.
1. **개요**로 이동하여 **찾아보기**를 클릭합니다.
1. 이 단계에서는 App Configuration 데이터가 포함되어 있지 않으므로 웹 사이트에 변경 내용이 표시되지 않습니다. 이는 다음 작업에서 수행할 작업입니다.

#### <a name="task-4-test-the-configuration-management"></a>작업 4: 구성 관리 테스트

1. 웹 사이트의 **브랜드** 드롭다운 목록에서 **Visual Studio**를 선택하고 화살표 단추( **>** )를 클릭합니다.
1. *'검색과 일치하는 결과가 없습니다'* 라는 메시지가 표시됩니다.
이 랩의 목표는 웹 사이트의 코드를 업데이트하거나 다시 배포하지 않고도 해당 값을 업데이트할 수 있도록 하는 것입니다.
1. 이 작업을 시도하려면 App Configuration으로 돌아갑니다.
1. **작업** 섹션에서 **구성 탐색기**를 선택합니다.
1. **만들기 > 키-값**을 클릭한 후 다음을 추가합니다.
    - **키:** eShopWeb:Settings:NoResultsMessage
    - **값:** *사용자 지정 메시지 입력*
1. **적용**을 클릭한 다음 웹 사이트로 돌아가 페이지를 새로 고칩니다.
1. 이전 기본값 대신 새 메시지가 표시됩니다.

축하합니다! 이 작업에서는 Azure App Configuration에서 **구성 탐색기**를 테스트했습니다.

#### <a name="task-5-test-the-feature-flag"></a>작업 5: 기능 플래그 테스트

기능 관리자를 계속 테스트해 보겠습니다.
1. 이 작업을 시도하려면 App Configuration으로 돌아갑니다.
1. **작업** 섹션에서 **기능 관리자**를 선택합니다.
1. **만들기**를 클릭한 후 다음을 추가합니다.
    - **기능 플래그 사용:** 확인
    - **기능 플래그 이름:** SalesWeekend
1. **적용**을 클릭한 다음 웹 사이트로 돌아가 페이지를 새로 고칩니다.
1. '이번 주말에 판매 중인 모든 티셔츠'라는 텍스트가 있는 이미지가 표시됩니다.
1. App Configuration에서 이 기능을 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 해당 이미지가 사라지는 것을 볼 수 있습니다.

축하합니다! 이 작업에서는 Azure App Configuration에서 **기능 관리자**를 테스트했습니다.

### <a name="exercise-3-remove-the-azure-lab-resources"></a>연습 3: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### <a name="task-1-remove-the-azure-lab-resources"></a>작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'AZ400-EWebShop-')].name" --output tsv
    ```

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'AZ400-EWebShop-')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## <a name="review"></a>검토

이 랩에서는 구성을 동적으로 사용하도록 설정하고, 기능 플래그를 관리하는 방법을 알아보았습니다.
