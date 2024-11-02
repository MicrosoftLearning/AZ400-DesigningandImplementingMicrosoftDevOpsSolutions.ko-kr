---
lab:
  title: 동적 구성 및 기능 플래그 사용
  module: 'Module 04: Implement a secure continuous deployment using Azure Pipelines'
---

# 동적 구성 및 기능 플래그 사용

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://learn.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독에서 기여자 또는 소유자 역할이 있는 Microsoft 계정 또는 Microsoft Entra 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://learn.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://learn.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

## 랩 개요

[Azure App Configuration](https://learn.microsoft.com/azure/azure-app-configuration/overview)은 애플리케이션 설정 및 기능 플래그를 중앙에서 관리할 수 있는 서비스를 제공합니다. 최신 프로그램, 특히 클라우드에서 실행되는 프로그램은 일반적으로 많은 구성 요소가 분산되어 있습니다. 이러한 구성 요소 간에 구성 설정을 분산하면 애플리케이션 배포 중에 문제 해결이 어려운 오류가 발생할 수 있습니다. App Configuration을 사용하여 애플리케이션에 대한 모든 설정을 한 곳에서 저장하고 액세스를 보호합니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- 동적 구성을 활성화합니다.
- 기능 플래그를 관리합니다.

## 예상 소요 시간: 45분

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

### 연습 1: (완료된 경우 건너뛰기) CI/CD 파이프라인 가져오기 및 실행

이 연습에서는 CI/CD 파이프라인을 가져와 eShopOnWeb 애플리케이션을 빌드하고 배포합니다. CI 파이프라인은 애플리케이션을 빌드하고 테스트를 실행할 준비가 이미 되어 있습니다. CD 파이프라인은 애플리케이션을 Azure Web App에 배포합니다.

#### 작업 1: CI 파이프라인 가져오기 및 실행

먼저 [eshoponweb-ci.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml)이라는 CI 파이프라인을 가져오겠습니다.

1. **Pipelines > Pipelines**로 이동합니다.
1. **파이프라인 만들기** 단추(파이프라인이 없는 경우) 또는 **새 파이프라인** 단추(이미 만든 파이프라인이 있는 경우)를 클릭합니다.
1. **Azure Repos Git(Yaml)** 을 선택합니다.
1. **eShopOnWeb** 리포지토리를 선택합니다.
1. **기존 Azure Pipelines YAML 파일**을 선택합니다.
1. **기본** 분기와 **/.ado/eshoponweb-ci.yml** 파일을 선택한 다음 **계속**을 클릭합니다.
1. 
          **실행** 단추를 클릭하여 파이프라인을 실행합니다.
1. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **Pipelines > Pipelines**로 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/제거** 옵션을 클릭합니다. 이름을 **eshoponweb-ci**로 설정하고 **저장**을 클릭합니다.

#### 작업 2: CD 파이프라인 가져오기 및 실행

이름이 [eshoponweb-cd-webapp-code.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-cd-webapp-code.yml)인 CD 파이프라인을 가져오겠습니다.

1. **Pipelines > Pipelines**로 이동합니다.
1. **새 파이프라인** 단추를 클릭합니다.
1. **Azure Repos Git(Yaml)** 을 선택합니다.
1. **eShopOnWeb** 리포지토리를 선택합니다.
1. **기존 Azure Pipelines YAML 파일**을 선택합니다.
1. **기본** 분기와 **/.ado/eshoponweb-cd-webapp-code.yml** 파일을 선택한 다음 **계속**을 클릭합니다.
1. YAML 파이프라인 정의에서 다음을 사용자 지정합니다.
   - **YOUR-SUBSCRIPTION-ID**를 Azure 구독 ID로 바꿉니다.
   - **az400eshop-NAME** 전역적으로 고유한 이름을 설정하려면 NAME을 바꿉니다.
   - **AZ400-EWebShop-NAME**, 랩에서 이전에 정의된 리소스 그룹 이름이 포함됨.

1. **저장 및 실행**을 클릭하고 파이프라인이 성공적으로 실행될 때까지 기다립니다.

    > **참고**: 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

    CD 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **리소스**: CI 파이프라인 완료 시 자동으로 트리거되도록 준비됩니다. 또한 bicep 파일에 대한 리포지토리를 다운로드합니다.
    - **AzureResourceManagerTemplateDeployment**: bicep 템플릿을 사용하여 Azure 웹앱을 배포합니다.

1. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **Pipelines > Pipelines**로 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/제거** 옵션을 클릭합니다. 이름을 **eshoponweb-cd-webapp-code**로 설정하고 **저장**을 클릭합니다.

### 연습 2: Azure App Configuration 관리

이 연습에서는 Azure에서 App Configuration 리소스를 만들고, 관리 ID를 사용하도록 설정한 다음 전체 솔루션을 테스트합니다.

> **참고**: 이 연습에서는 코딩 기술이 필요하지 않습니다. 웹 사이트의 코드는 이미 Azure App Configuration 기능을 구현합니다.

애플리케이션에서 이를 구현하는 방법을 알고 싶다면 [ASP.NET Core 앱에서 동적 구성 사용](https://learn.microsoft.com/azure/azure-app-configuration/enable-dynamic-configuration-aspnet-core) 및 [Azure App Configuration에서 기능 플래그 관리](https://learn.microsoft.com/azure/azure-app-configuration/manage-feature-flags) 자습서를 살펴보십시오.

#### 작업 1: App Configuration 리소스 만들기

1. Azure Portal에서 **App Configuration** 서비스를 검색합니다.
1. **앱 구성 만들기**를 클릭한 후 다음을 선택합니다.
    - 사용자의 Azure 구독.
    - 이전에 만든 리소스 그룹(이름이 **AZ400-EWebShop-NAME**이어야 함).
    - 위치.
    - 고유한 이름(예: **appcs-NAME-REGION**).
    - **무료** 가격 책정 계층을 선택합니다.
1. **검토 + 만들기**와 **만들기**를 차례로 클릭합니다.
1. App Configuration 서비스를 만든 후 **개요**로 이동하고 **엔드포인트** 값을 복사/저장합니다.

#### 작업 2: 관리 ID 사용

1. 파이프라인을 사용하여 배포된 웹앱으로 이동합니다(이름이 **az400-webapp-NAME**이어야 함).
1. **설정** 섹션에서 **ID**를 클릭한 다음, **시스템 할당** 섹션에서 상태를 **켜기**로 전환한 후 **저장 > 예**를 클릭하고 작업이 완료될 때까지 몇 초 정도 기다립니다.
1. App Configuration 서비스로 돌아간 후 **액세스 제어**를 클릭한 다음 **역할 할당 추가**를 클릭합니다.
1. **역할** 섹션에서 **App Configuration 데이터 판독기**를 선택합니다.
1. **멤버** 섹션에서 **ID 관리**를 선택한 다음, 웹앱의 관리 ID를 선택합니다(이름이 서로 같아야 함).
1. **검토 및 할당**을 클릭합니다.

#### 작업 3: 웹앱 구성

웹 사이트가 App Configuration에 액세스하고 있는지 확인하려면 해당 구성을 업데이트해야 합니다.

1. 웹앱으로 돌아갑니다.
1. **설정** 섹션에서 **환경 변수**를 클릭합니다.
1. 새 애플리케이션 설정 두 가지를 추가합니다.
    - 첫 번째 앱 설정
        - **이름:** UseAppConfig
        - **값:** true
    - 두 번째 앱 설정
        - **이름:** AppConfigEndpoint
        - **값:** *이전에 App Configuration 엔드포인트에서 저장/복사한 값. 다음과 같이 표시됨<https://appcs-NAME-REGION.azconfig.io>*

1. **적용**을 클릭한 다음 **확인**을 클릭하고 설정이 업데이트될 때까지 기다립니다.
1. **개요**로 이동하여 **찾아보기**를 클릭합니다.
1. 이 단계에서는 App Configuration 데이터가 포함되어 있지 않으므로 웹 사이트에 변경 내용이 표시되지 않습니다. 이는 다음 작업에서 수행할 작업입니다.

#### 작업 4: 구성 관리 테스트

1. 웹 사이트의 **브랜드** 드롭다운 목록에서 **Visual Studio**를 선택하고 화살표 단추(**>**)를 클릭합니다.
1. *'검색과 일치하는 결과가 없습니다'* 라는 메시지가 표시됩니다. 이 랩의 목표는 웹 사이트의 코드를 업데이트하거나 다시 배포하지 않고도 해당 값을 업데이트할 수 있도록 하는 것입니다.
1. 이 작업을 시도하려면 App Configuration으로 돌아갑니다.
1. **작업** 섹션에서 **구성 탐색기**를 선택합니다.
1. **만들기 > 키-값**을 클릭한 후 다음을 추가합니다.
    - **키:** eShopWeb:Settings:NoResultsMessage
    - **값:***사용자 지정 메시지 입력*
1. **적용**을 클릭한 다음 웹 사이트로 돌아가 페이지를 새로 고칩니다.
1. 이전 기본값 대신 새 메시지가 표시됩니다.

#### 작업 5: 기능 플래그 테스트

기능 관리자를 계속 테스트해 보겠습니다.

1. 이 작업을 시도하려면 App Configuration으로 돌아갑니다.
1. **작업** 섹션에서 **기능 관리자**를 선택합니다.
1. **만들기**를 클릭한 후 다음을 추가합니다.
    - **기능 플래그 사용:** 확인
    - **기능 플래그 이름:** SalesWeekend
1. **적용**을 클릭한 다음 웹 사이트로 돌아가 페이지를 새로 고칩니다.
1. '이번 주말에 판매 중인 모든 티셔츠'라는 텍스트가 있는 이미지가 표시됩니다.
1. App Configuration에서 이 기능을 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 해당 이미지가 사라지는 것을 볼 수 있습니다.

   > [!IMPORTANT]
   > 불필요한 요금을 방지하려면 Azure Portal에서 만든 리소스를 삭제해야 합니다. **eshoponweb-cd-webapp-code** 파이프라인을 사용하지 않도록 설정하거나 **eshoponweb-ci**의 다음 실행 후에 삭제된 리소스 그룹 및 관련 리소스를 다시 만듭니다.

## 검토

이 랩에서는 구성을 동적으로 사용하도록 설정하고, 기능 플래그를 관리하는 방법을 알아보았습니다.
