---
lab:
  title: 랩 환경 유효성 검사
  module: 'Module 0: Welcome'
---

# 랩 환경 유효성 검사

랩을 준비하려면 환경을 올바르게 설정하는 것이 중요합니다. 이 페이지에서는 모든 필수 구성 요소를 충족하도록 설정 프로세스를 안내합니다.

- 이 랩에는 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://learn.microsoft.com/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure 구독 설정:** 아직 Azure 구독이 없는 경우 이 페이지의 지침에 따라 구독을 만들거나 [https://azure.microsoft.com/free](https://azure.microsoft.com/free) 방문을 통해 무료로 가입하세요.

- **Azure DevOps 조직 설정:** 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없는 경우 이 페이지 또는 [조직 또는 프로젝트 컬렉션 만들기](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 지침에 따라 만들 수 있습니다.
- [Windows용 Git 다운로드 페이지](https://gitforwindows.org/). 이 랩의 필수 구성 요소로 설치됩니다.

- [Visual Studio Code](https://code.visualstudio.com/) 이 랩의 필수 구성 요소로 설치됩니다.

- [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli) 자체 호스팅 에이전트 컴퓨터에 Azure CLI를 설치합니다.

- [.NET SDK - 최신 버전](https://dotnet.microsoft.com/download/visual-studio-sdks). 자체 호스팅 에이전트 컴퓨터에 .NET SDK를 설치합니다.

## Azure DevOps 조직을 만드는 지침(한 번만 수행하면 됨)

> **참고**: 이미 **개인 Microsoft 계정**이 설정되어 있고 해당 계정에 연결된 활성 Azure 구독이 있는 경우 3단계부터 시작합니다.

1. 프라이빗 브라우저 세션을 사용하여 `https://account.microsoft.com`에서 새로운 **개인 MSA(Microsoft 계정)** 을 받습니다.

1. 동일한 브라우저 세션을 사용하여 `https://azure.microsoft.com/free`에서 무료 Azure 구독에 등록합니다.

1. 브라우저를 열고 `https://portal.azure.com`에서 Azure Portal로 이동한 다음, Azure Portal 화면 상단에서 **Azure DevOps**를 검색합니다. 최종 페이지에서 **Azure DevOps 조직**을 클릭합니다.

1. 그런 다음 **내 Azure DevOps 조직**이라고 표시된 링크를 클릭하거나 `https://aex.dev.azure.com`으로 바로 이동합니다.

1. On the **몇 가지 추가 정보를 입력해 주세요** 페이지에서 **계속**을 선택합니다.

1. 왼쪽의 드롭다운 상자에서 **Microsoft 계정** 대신 **기본 디렉터리**를 선택합니다.

1. 메시지가 표시되면(“몇 가지 추가 정보를 입력해 주세요”) 이름, 메일 주소 및 위치를 입력하고 **계속**을 클릭합니다.__

1. **기본 디렉터리**를 선택한 상태로 `https://aex.dev.azure.com`으로 돌아가서 파란색 **새 조직 만들기** 단추를 클릭합니다.

1. **계속**을 클릭하여 _서비스 약관_에 동의합니다.

1. 메시지가 표시되면(“거의 완료됨”) Azure DevOps 조직 이름은 기본값으로 유지하고(전역적으로 고유한 이름이어야 함) 목록에서 가까운 호스트 위치를 선택합니다.__

1. 새로 만든 조직이 **Azure DevOps**에서 열리면 왼쪽 하단에서 **조직 설정**을 선택합니다.

1. **조직 설정** 화면에서 **청구**를 선택합니다(이 화면이 열리기까지 몇 초 정도 소요).

1. **청구 설정**을 선택하고 화면 오른쪽에서 **Azure 구독**을 선택한 다음 **저장**을 선택하여 구독을 조직과 연결합니다.

1. 화면 위쪽에 연결된 Azure 구독 ID가 표시되면 **MS Hosted CI/CD**의 **Paid parallel jobs**의 수를 0에서 **1**로 변경합니다. 그런 다음 하단의 **저장** 버튼을 선택합니다.

   > **참고**: 새 설정이 백 엔드에 반영될 수 있도록** CI/CD 기능을 사용하기 전에 몇 분간 대기할 수 있습니다**. 그렇지 않으면 “구입되거나 부여된 호스팅된 병렬 처리가 없습니다”라는 메시지가 계속 표시됩니다.__

1. **조직 설정**에서 **Pipelines** 섹션으로 이동하여 **설정**을 클릭합니다.

1. **클래식 빌드 파이프라인 만들기 사용 안 함** 및 **클래식 릴리스 파이프라인 사용 안 함**에 대해 스위치를 **끄기**로 전환합니다.

   > **참고**: **클래식 릴리스 파이프라인 만들기 사용 안 함** 스위치를 **켜기**로 설정하면 DevOps Projects의 **파이프라인** 섹션에서 **릴리스** 메뉴와 같은 클래식 릴리스 파이프라인 만들기 옵션이 숨겨집니다.

1. **조직 설정**에서 **보안** 섹션으로 이동하여 **정책**을 클릭합니다.

1. **공개 프로젝트 허용**을 위해 **켜기**로 전환합니다.

   > **참고**: 일부 랩에서 사용되는 확장은 무료 버전을 사용할 수 있는 공개 프로젝트가 필요할 수 있습니다.

## Azure DevOps 프로젝트를 만들고 구성하는 지침(이 작업은 한 번만 수행하면 됨)

> **참고**: 이러한 단계를 계속하기 전에 Azure DevOps 조직을 만드는 단계를 완료했는지 확인합니다.

모든 랩 지침을 따르려면 새 Azure DevOps 프로젝트를 설정하고, [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb) 애플리케이션을 기반으로 하는 리포지토리를 만들고, Azure 구독에 대한 서비스 연결을 만들어야 합니다.

### 테스트 프로젝트 만들기

먼저, 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 브라우저를 열고 Azure DevOps 조직으로 이동합니다.

1. **새 프로젝트** 옵션을 선택하고 다음 설정을 사용합니다.

   - 이름: **eShopOnWeb**
   - 표시 유형: **프라이빗**
   - 고급: 버전 제어: **Git**
   - 고급: 작업 항목 프로세스: **스크럼**

1. **만들기**를 실행합니다.

   ![새 프로젝트 만들기 패널의 스크린샷.](images/create-project.png)

### eShopOnWeb Git 리포지토리 가져오기

이제 eShopOnWeb을 Git 리포지토리로 가져옵니다.

1. 브라우저를 열고 Azure DevOps 조직으로 이동합니다.

1. 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다.

1. **Repos > 파일**, **리포지토리 가져오기**를 선택한 다음 ** 가져오기**를 선택합니다.

1. **Git 리포지토리 가져오기** 창에서 다음 URL `https://github.com/MicrosoftLearning/eShopOnWeb`을(를) 붙여넣고 **가져오기**를 선택합니다.

   ![리포지토리 가져오기 패널의 스크린샷.](images/import-repo.png)

1. 리포지토리는 다음과 같은 방식으로 구성됩니다.

   - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
   - **.devcontainer** 폴더 컨테이너 설정을 통해 컨테이너를 사용하여 개발합니다(VS Code 또는 GitHub Codespaces에서 로컬로).
   - **.azure** 폴더에는 코드 템플릿으로 Bicep 및 ARM 인프라가 포함되어 있습니다.
   - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
   - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 8 웹 사이트가 포함되어 있습니다.

1. 웹 브라우저 창을 열어 둡니다.

1. **Repos > 분기**로 이동합니다.

1. **기본** 분기를 마우스로 가리킨 다음 열 오른쪽에 있는 줄임표를 클릭합니다.

1. **기본 분기로 설정**을 클릭합니다.

### Azure 리소스에 액세스하기 위한 서비스 연결 만들기

Azure 구독에서 리소스를 배포하고 액세스할 수 있도록 Azure DevOps에서 서비스 연결을 만들어야 합니다.

1. 웹 브라우저를 시작하고 Azure DevOps 포털(`https://aex.dev.azure.com`)로 이동합니다.

1. Azure DevOps 조직에 로그인합니다.

   > **참고**: Azure DevOps 조직에 처음 로그인하는 경우 프로필을 만들고 서비스 약관에 동의하라는 메시지가 표시되면, **계속**을 선택합니다.

1. **eShopOnWeb** 프로젝트를 열고 포털의 왼쪽 하단에서 **프로젝트 설정**을 선택합니다.

1. Pipelines에서 **서비스 연결**을 선택한 다음 **서비스 연결 만들기** 버튼을 선택합니다.

   ![새 서비스 연결 만들기 버튼의 스크린샷.](images/new-service-connection.png)

1. **새 서비스 연결** 블레이드에서 **Azure Resource Manager** 및 **다음**을 선택합니다(아래로 스크롤해야 할 수 있음).

1. **ID 형식** Dropbox에서 **앱 등록(자동)** 을 선택합니다.

1. **범위 수준**에서 **워크로드 ID 페더레이션** 및 **구독**을 선택합니다.

   > **참고**: 서비스 연결을 수동으로 구성하려는 경우 **앱 등록 또는 관리 ID(수동)** 를 사용할 수도 있습니다. 서비스 연결을 수동으로 만들려면 [Azure DevOps 설명서](https://learn.microsoft.com/azure/devops/pipelines/library/connect-to-azure)의 단계를 따르세요.

1. 다음 정보를 사용하여 비어 있는 필드를 채웁니다.

   - **구독**: Azure 구독을 선택합니다.
   - **리소스 그룹**: 리소스를 배포할 리소스 그룹을 선택합니다. 리소스 그룹이 없는 경우 [Azure Portal을 사용하여 Azure 리소스 그룹 관리](https://learn.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) 지침에 따라 Azure Portal에서 리소스 그룹을 만들 수 있습니다.
   - **서비스 연결 이름**: 유형 **`azure subs`** 이 이름은 Azure 구독에 액세스하기 위해 YAML 파이프라인에서 참조됩니다.

1. **모든 파이프라인에 액세스 권한 부여** 옵션이 선택 해제되어 있는지 확인하고 **저장**을 선택합니다.

   > **중요:** **모든 파이프라인에 대한 액세스 권한 부여** 옵션은 프로덕션 환경에 권장되지 않습니다. 이 옵션을 선택하면 프로젝트의 모든 파이프라인에 대한 서비스 연결에 대해 액세스 권한을 부여하는 것을 의미하며, 이 옵션을 선택하지 않으면 각 파이프라인을 처음 실행할 때 서비스 연결에 대한 액세스를 승인할 수 있습니다.

   > **참고**: **모든 파이프라인에 대한 액세스 권한 부여** 옵션이 사용하지 않도록 설정되어 있고(회색으로 표시됨) 변경할 수 없는 경우 랩을 계속 진행합니다.

   > **참고**: 서비스 연결을 만드는 데 필요한 권한이 없다는 오류 메시지가 표시되면 다시 시도하거나 서비스 연결을 수동으로 구성합니다.

이제 랩을 계속 진행하는 데 필요한 필수 구성 요소 단계를 완료했습니다.
