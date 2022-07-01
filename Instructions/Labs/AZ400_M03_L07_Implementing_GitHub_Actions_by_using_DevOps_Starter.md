---
lab:
  title: '랩 07: DevOps Starter를 사용하여 GitHub Action 구현'
  module: 'Module 03: Implement CI with Azure Pipelines and GitHub Actions'
ms.openlocfilehash: aa16fd8e941c3e94eef0d4c9b0fdd23caa3e7866
ms.sourcegitcommit: 73179152f51e48ada9641c4a6a33ea941606c469
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2022
ms.locfileid: "146774606"
---
# <a name="lab-07-implementing-github-actions-by-using-devops-starter"></a>랩 07: DevOps Starter를 사용하여 GitHub Action 구현

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독에서 Owner 또는 Contributor 역할이 지정된 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/en-us/azure/active-directory/roles/manage-roles-portal#view-my-roles)을 참조하세요.

- 이 랩에 사용할 수 있는 GitHub 계정이 아직 없으면 [새 GitHub 계정 등록](https://github.com/join)에서 제공되는 지침에 따라 새 계정을 만듭니다.

## <a name="lab-overview"></a>랩 개요

이 랩에서는 DevOps Starter를 사용하여 Azure 웹앱을 배포하는 GitHub Action 워크플로를 구현하는 방법을 배웁니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- DevOps Starter를 사용하여 GitHub Action 워크플로를 구현합니다.
- GitHub Action 워크플로의 기본 특성을 설명합니다.

## <a name="estimated-timing-30-minutes"></a>예상 소요 시간: 30분

## <a name="instructions"></a>Instructions

### <a name="exercise-1-create-a-devops-starter-project"></a>연습 1: DevOps Starter 프로젝트 만들기

이 연습에서는 DevOps Starter를 사용하여 다음을 포함한 다양한 리소스의 프로비전을 지원합니다.

- GitHub 리포지토리 호스팅:

  - 샘플 .NET Core 웹 사이트의 코드
  - 웹 사이트 코드를 호스트하는 Azure 웹앱을 배포하는 Azure Resource Manager 템플릿
  - 웹 사이트를 빌드, 배포 및 테스트하는 워크플로

- GitHub 워크플로를 사용하여 자동으로 배포되는 Azure 웹앱

#### <a name="task-1-create-devops-starter-project"></a>작업 1: DevOps Starter 프로젝트 만들기

이 작업에서는 GitHub 리포지토리를 자동으로 설정하는 Azure DevOps Starter 프로젝트를 만들고, GitHub 리포지토리의 내용에 따라 Azure 웹앱을 배포하는 GitHub 워크플로를 만들고 트리거합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [**Azure Portal**](https://portal.azure.com)로 이동하고 이 랩에서 사용하는 Azure 구독에서 Contributor 이상의 역할을 가진 사용자 계정으로 로그인합니다.
1. Azure Portal에서 **DevOps Starter** 리소스 종류를 검색하여 선택하고 **DevOps Starter** 블레이드에서 **+ 추가**, **+ 새로 만들기** 또는 **+ 만들기** 를 클릭합니다.
1. **DevOps Starter** 블레이드의 **새 애플리케이션으로 새로 시작** 페이지에서 **GitHub를 사용하여 DevOps Starter를 설정하려면 여기를 클릭** 텍스트의 **여기** 를 클릭합니다.

    > **참고**: 그러면 **DevOps Starter 설정** 블레이드가 표시됩니다.

1. **DevOps starter 설정** 블레이드에서 **GitHub** 타일이 선택되어 있는지 확인하고 **완료** 를 클릭합니다.
1. **DevOps Starter** 블레이드로 돌아가서 **다음: 프레임워크 >** 를 클릭합니다.
1. **DevOps Starter** 블레이드의 **애플리케이션 프레임워크 선택** 페이지에서 **ASP.NET Core** 타일을 선택하고 **다음: 서비스 >** 를 클릭합니다.
1. **DevOps Starter** 블레이드의 **애플리케이션을 배포할 Azure 서비스 선택** 페이지에서 **Windows 웹앱** 타일이 선택되어 있는지 확인하고 **다음: 만들기 >** 를 클릭합니다.
1. **DevOps Starter** 블레이드의 **리포지토리 및 구독 선택** 페이지에서 **권한 부여** 를 클릭합니다.

    > **참고**: 그러면 **Azure GitHub Actions 권한 부여** 팝업 웹 브라우저 창이 표시됩니다.

1. **Azure GitHub Actions 권한 부여** 팝업 창에서 필요한 권한을 검토하고 **AzureGithubActions 권한 부여** 를 클릭합니다.

    > **참고**: 그러면 웹 팝업 브라우저 창이 Azure DevOps 사이트로 리디렉션되면서 Azure DevOps 정보를 입력하라는 메시지가 표시됩니다.

1. 메시지가 표시되면 팝업 웹 브라우저 창에서 **계속** 을 클릭합니다.
1. **DevOps Starter** 블레이드의 **리포지토리 및 구독 선택** 페이지로 돌아와 다음 설정을 지정하고 **검토 + 만들기** 를 클릭합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 조직 | GitHub 계정 이름 |
    | 리포지토리 | **az400m08l01** |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 웹앱 이름 | **azurewebsites.net** DNS 네임스페이스에서 유효하고 전역적으로 고유한 호스트 이름 |
    | 위치 | Azure 웹앱을 프로비저닝할 수 있는 Azure 지역의 이름. **미국 동부** 권장 |

   > **참고**: 사용할 수 없는 리소스로 인해 일부 **위치** 가 실패할 수 있습니다. **미국 동부** 권장
   > **참고**: 프로비전이 완료될 때까지 기다립니다. 이 작업은 1분 정도 걸립니다.

1. **Deploy_DevOps_Project_az400m08l01 \| 개요** 블레이드에서 **리소스로 이동** 을 클릭합니다.
1. **az400m08l01** 블레이드의 **GitHub 워크플로** 타일에서 **권한 부여** 를 클릭합니다.
1. **GitHub 권한 부여** 블레이드에서 **권한 부여** 를 다시 클릭합니다.
1. **az400m08l01** 블레이드로 돌아가 **GitHub 워크플로** 타일의 작업 진행률을 모니터링합니다.

    > **참고**: GitHub 워크플로의 빌드, 배포 및 기능 테스트 작업이 완료될 때가지 기다립니다. 이 작업은 5분 정도 걸립니다.

#### <a name="task-2-review-the-results-of-creating-the-devops-starter-project"></a>작업 2: DevOps Starter 프로젝트 만들기 결과 검토

이 작업에서는 DevOps Starter 프로젝트 만들기 결과를 검토합니다.

1. Azure Portal이 표시되는 웹 브라우저 창의 **az400m08l01** 블레이드에서 **GitHub 워크플로** 섹션을 검토하고 **빌드**, **배포** 및 **기능 테스트** 작업이 정상적으로 완료되었는지 확인합니다.
1. **az400m08l01** 블레이드에서 **Azure 리소스** 섹션을 검토하고 여기에 App Service 웹앱 인스턴스 및 해당 Application Insights 리소스가 포함되어 있는지 확인합니다.
1. **az400m08l01** 블레이드 위쪽에서 이전 작업에서 만든 **워크플로 파일** 및 GitHub 리포지토리 링크를 확인합니다.
1. **az400m08l01** 블레이드 위쪽에서 GitHub 리포지토리 링크를 클릭합니다.
1. GitHub 리포지토리 페이지에서 다음 레이블이 있는 3개의 폴더를 확인합니다.

    - **.github\workflows** - YAML 형식의 워크플로 파일 포함
    - **Application** - 샘플 웹 사이트의 코드 포함
    - **ArmTemplates** - 워크플로에서 Azure 리소스 프로비저닝에 사용되는 Azure Resource Manager 템플릿 포함

1. GitHub 리포지토리 페이지에서 **.github/workflows** 를 클릭한 다음, **devops-starter-workflow.yml** 항목을 클릭합니다.
1. **devops-starter-workflow.yml** 의 내용이 표시된 GitHub 리포지토리 페이지에서 해당 내용을 검토하고 **빌드**, **배포** 및 **기능 테스트** 작업 정의가 포함되었는지 확인합니다.
1. GitHub 리포지토리 페이지의 도구 모음에서 **작업** 을 클릭합니다.
1. GitHub 리포지토리 페이지의 **작업** 탭에 있는 **모든 워크플로** 섹션에서 가장 최근에 실행된 워크플로를 나타내는 항목을 클릭합니다.
1. 워크플로 실행 페이지에서 워크플로 상태뿐만 아니라 **주석** 및 **아티팩트** 목록도 검토합니다.
1. GitHub 리포지토리 페이지의 도구 모음에서 **설정** 을 클릭하고 **설정** 탭에서 **비밀** 을 클릭합니다.
1. **작업 비밀** 창에서 Azure 구독에 액세스하는 데 필요한 자격 증명을 나타내는 **AZURE_CREDENTIALS** 항목을 확인합니다.
1. **az400m08l01/Application/aspnet-core-dotnet-core/Pages/Index.cshtml** GitHub 리포지토리 페이지로 이동한 다음 오른쪽 위 모서리에서 연필 아이콘을 클릭하여 편집 모드로 전환합니다.
1. 줄 19를 `<div class="description line-1"> GitHub Workflow has been successfully updated</div>`로 변경합니다.
1. 페이지의 아래쪽으로 스크롤하여 **변경 내용 커밋** 을 클릭합니다.
1. GitHub 리포지토리 페이지의 도구 모음에서 **작업** 을 클릭합니다.
1. **모든 워크플로** 섹션에서 **Index.cshtml 업데이트** 항목을 클릭합니다.
1. **devops-starter-workflow.yml** 섹션에서 배포 진행률을 모니터링하고 정상적으로 완료되었는지 확인합니다.
     > **참고**: **“azure/CLI@1”를 사용하는 작업이 실패** 하면 **devops-starter-workflow.yml** 파일(기본 azure cli 버전 변경)에 다음 변경 내용을 커밋하고 성공적으로 완료되었는지 확인합니다.
     > <!-- {% raw %}) -->
     > ```
     >     - name: Deploy ARM Template
     >       uses: azure/CLI@v1
     >       continue-on-error: false
     >       with:
     >         azcliversion: 2.29.2
     >         inlineScript: |
     >           az group create --name "${{ env.RESOURCEGROUPNAME }}" --location "${{ env.LOCATION }}"
     >           az deployment group create --resource-group "${{ env.RESOURCEGROUPNAME }}" --template-file ./ArmTemplates/windows-webapp-template.json --parameters webAppName="${{ env.AZURE_WEBAPP_NAME }}" hostingPlanName="${{ env.HOSTINGPLANNAME }}" appInsightsLocation="${{ env.APPINSIGHTLOCATION }}" sku="${{ env.SKU }}"
     > ```
     > <!-- {% endraw %}) -->

1. Azure Portal에서 DevOps Starter 블레이드를 표시하는 브라우저 창으로 전환하여 **애플리케이션 엔드포인트** 항목 옆에 있는 **찾아보기** 링크를 클릭합니다.
1. 새로 연 웹 브라우저 창에서 GitHub 리포지토리에서 커밋한 변경 내용을 나타내는 업데이트된 텍스트가 웹앱 홈 페이지에 표시되는지 확인합니다.

### <a name="exercise-2-remove-the-azure-lab-resources"></a>연습 2: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### <a name="task-1-remove-the-azure-lab-resources"></a>작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m08l01')].name" --output tsv
    ```

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m08l01')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## <a name="review"></a>검토

이 랩에서는 DevOps Starter를 사용하여 Azure 웹앱을 배포하는 GitHub Action 워크플로를 구현했습니다.
