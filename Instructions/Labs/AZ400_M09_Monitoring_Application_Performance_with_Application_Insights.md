---
lab:
  title: '랩 21: Application Insights를 사용하여 애플리케이션 성능 모니터링'
  module: 'Module 09: Implement continuous feedback'
ms.openlocfilehash: f6bd76e03bef3782b5aabf447711974f11bf823a
ms.sourcegitcommit: f72fcf5ee578f465b3495f3cf789b06c530e88a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2022
ms.locfileid: "139262617"
---
# <a name="lab-21-monitoring-application-performance-with-application-insights"></a>랩 21: Application Insights를 사용하여 애플리케이션 성능 모니터링
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-overview"></a>랩 개요

Application Insights는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights를 사용하여 라이브 웹 애플리케이션을 모니터링할 수 있습니다. Application Insights는 성능 관련 이상 현상을 자동으로 검색합니다. 또한 문제를 쉽게 진단할 수 있는 효율적인 분석 도구를 제공하며, 지속적인 성능과 유용성 개선도 지원합니다. .NET, Node.js 및 Java EE, 호스트된 온-프레미스, 하이브리드 또는 퍼블릭 클라우드를 포함하여 다양한 플랫폼에서 앱과 함께 작동합니다. 다양한 개발 도구에서 제공되는 연결점을 사용하여 DevOps 프로세스와 통합할 수 있습니다. 또한 Visual Studio App Center와 통합하여 모바일 앱의 원격 분석을 모니터링하고 분석할 수도 있습니다.

이 랩에서는 Application Insights를 기존 웹 애플리케이션에 추가하는 방법과 Azure Portal을 통해 애플리케이션을 모니터링하는 방법을 알아봅니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure App Service 웹앱 배포
- Application Insights를 사용하여 Azure 웹앱 애플리케이션 트래픽 생성 및 모니터링
- Application Insights를 사용하여 Azure 웹앱 성능 조사
- Application Insights를 사용하여 Azure 웹앱 사용량 추적
- Application Insights를 사용하여 Azure 웹앱 경고 생성

## <a name="lab-duration"></a>랩 소요 시간

-   예상 시간: **60분**

## <a name="instructions"></a>Instructions

### <a name="before-you-start"></a>시작하기 전에

#### <a name="sign-in-to-the-lab-virtual-machine"></a>랩 가상 머신에 로그인

다음 자격 증명을 사용하여 Windows 10 가상 머신에 로그인해야 합니다.
    
-   사용자 이름: **Student**
-   암호: **Pa55w.rd**

#### <a name="review-applications-required-for-this-lab"></a>이 랩에 필요한 애플리케이션 검토

이 랩에서 사용할 애플리케이션을 확인합니다.
    
-   Microsoft Edge

#### <a name="set-up-an-azure-devops-organization"></a>Azure DevOps 조직 설정 

이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

#### <a name="prepare-an-azure-subscription"></a>Azure 구독 준비

-   기존 Azure 구독을 확인하거나 새 구독을 만듭니다.
-   Azure 구독의 Owner 역할, 그리고 Azure 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할이 지정되어 있는 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/en-us/azure/active-directory/roles/manage-roles-portal#view-my-roles)을 참조하세요.

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿과 Azure 리소스(Azure 웹앱 및 Azure SQL 데이터베이스 포함)를 기반으로 하여 미리 구성된 Parts Unlimited 팀 프로젝트를 설정합니다. 

#### <a name="task-1-configure-the-team-project"></a>작업 1: 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 **Parts Unlimited** 템플릿을 기반으로 새 프로젝트를 생성합니다.

1.  랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다. 

    > **참고**: 사이트에 대한 자세한 내용은 https://docs.microsoft.com/en-us/azure/devops/demo-gen 을 참조하세요.

1.  **로그인** 을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1.  필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락** 을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1.  **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **애플리케이션 성능 모니링** 을 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택** 을 클릭합니다.
1.  템플릿 목록에서 **PartsUnlimited** 템플릿을 선택한 다음 **템플릿 선택** 을 클릭합니다.
1.  **새 프로젝트 만들기** 페이지로 돌아와서 **프로젝트 만들기** 를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1.  **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동** 을 클릭합니다.

#### <a name="task-2-create-azure-resources"></a>작업 2: Azure 리소스 생성

이 작업에서는 Azure Portal에서 Cloud Shell을 사용하여 Azure 웹앱과 Azure SQL 데이터베이스를 만듭니다.

> **참고**: 이 랩에서는 Azure App Service에 Parts Unlimited 사이트를 배포합니다. 이 요구 사항을 충족하려면 필요한 인프라를 스핀업해야 합니다. 

1.  랩 컴퓨터에서 웹 브라우저를 시작하여 [**Azure Portal**](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 Azure 구독의 Owner 역할, 그리고 해당 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할을 가진 사용자 계정으로 로그인합니다.
1. Azure Portal의 도구 모음에서 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
1. **Bash** 와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash** 를 선택합니다.
    >**참고**: **Cloud Shell** 을 처음 시작했는데 **탑재된 스토리지 없음** 이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기** 를 선택합니다.

1.  **Cloud Shell** 창의 **Bash** 프롬프트에서 다음 명령을 실행하여 리소스 그룹을 만듭니다. 여기서 `<region>` 자리 표시자는 ‘eastus’ 등의 가장 가까운 Azure 지역 이름으로 바꿉니다.

    ```bash
    RESOURCEGROUPNAME='az400m17l01a-RG'
    LOCATION='<region>'
    az group create --name $RESOURCEGROUPNAME --location $LOCATION
    ```

1.  다음 명령을 실행하여 Windows App Service 요금제를 만들려면:

    ```bash
    SERVICEPLANNAME='az400l17-sp'
    az appservice plan create --resource-group $RESOURCEGROUPNAME \
        --name $SERVICEPLANNAME --sku B3 
    ```
    > **참고**: `ModuleNotFoundError: No module named 'vsts_cd_manager'`로 시작하는 오류 메시지와 함께 `az appservice plan create` 명령이 실패하는 경우 다음 명령을 실행한 다음, 실패한 명령을 다시 실행합니다.

    ```bash
    az extension remove --name appservice-kube
    az extension add --yes --source "https://aka.ms/appsvc/appservice_kube-latest-py2.py3-none-any.whl"
    ```
1.  고유한 이름을 사용하여 웹앱을 만듭니다.

    ```bash
    WEBAPPNAME=partsunlimited$RANDOM$RANDOM
    az webapp create --resource-group $RESOURCEGROUPNAME --plan $SERVICEPLANNAME --name $WEBAPPNAME 
    ```

    > **참고**: 웹앱의 이름을 적어 둡니다. 이 랩의 후반부에서 필요합니다.

1. 이제 Application Insights 인스턴스를 만들겠습니다.

    ```bash
    az monitor app-insights component create --app $WEBAPPNAME \
        --location $LOCATION \
        --kind web --application-type web \
        --resource-group $RESOURCEGROUPNAME
    ```

    > **참고**: ‘명령에 확장 application-insights가 필요합니다. 지금 설치할까요?’라는 메시지가 표시된 경우 Y를 입력하고 Enter 키를 누릅니다.

1. Application Insights를 웹 애플리케이션에 연결해 보겠습니다.

    ```bash
    az monitor app-insights component connect-webapp --app $WEBAPPNAME \
        --resource-group $RESOURCEGROUPNAME --web-app $WEBAPPNAME
    ```

1.  다음으로, Azure SQL Server를 만듭니다.

    ```bash
    USERNAME="Student"
    SQLSERVERPASSWORD="Pa55w.rd1234"
    SERVERNAME="partsunlimitedserver$RANDOM"
    
    az sql server create --name $SERVERNAME --resource-group $RESOURCEGROUPNAME \
    --location $LOCATION --admin-user $USERNAME --admin-password $SQLSERVERPASSWORD
    ```

1.  웹앱은 SQL 서버에 액세스할 수 있어야 하므로 SQL Server 방화벽 규칙에서 Azure 리소스에 대한 액세스를 허용해야 합니다.

    ```bash
    STARTIP="0.0.0.0"
    ENDIP="0.0.0.0"
    az sql server firewall-rule create --server $SERVERNAME --resource-group $RESOURCEGROUPNAME \
    --name AllowAzureResources --start-ip-address $STARTIP --end-ip-address $ENDIP
    ```

1.  이제 해당 서버 내에 데이터베이스를 만듭니다.

    ```bash
    az sql db create --server $SERVERNAME --resource-group $RESOURCEGROUPNAME --name PartsUnlimited \
    --service-objective S0
    ```

1.  만든 웹앱에는 해당 구성의 데이터베이스 연결 문자열이 필요하므로 다음 명령을 실행하여 이 문자열을 준비하고 웹앱의 앱 설정에 추가합니다.

    ```bash
    CONNSTRING=$(az sql db show-connection-string --name PartsUnlimited --server $SERVERNAME \
    --client ado.net --output tsv)
    CONNSTRING=${CONNSTRING//<username>/$USERNAME}
    CONNSTRING=${CONNSTRING//<password>/$SQLSERVERPASSWORD}
    az webapp config connection-string set --name $WEBAPPNAME --resource-group $RESOURCEGROUPNAME \
    -t SQLAzure --settings "DefaultConnectionString=$CONNSTRING" 
    ```

### <a name="exercise-1-monitor-an-azure-app-service-web-app-by-using-azure-application-insights"></a>연습 1: Azure Application Insights를 사용하여 Azure App Service 웹앱 모니터링

이 연습에서는 Azure DevOps 파이프라인을 사용하여 Azure App Service에 웹앱을 배포하고 해당 웹앱을 대상으로 트래픽을 생성합니다. 그런 다음 Application Insights를 사용하여 웹 트래픽 검토, 애플리케이션 성능 조사, 애플리케이션 사용량 추적, 경고 구성을 수행합니다.

#### <a name="task-1-deploy-a-web-app-to-azure-app-service-by-using-azure-devops"></a>작업 1: Azure DevOps를 사용하여 Azure App Service에 웹앱 배포

이 작업에서는 Azure DevOps 파이프라인을 사용하여 Azure에 웹앱을 배포합니다.

> **참고**: 이 랩에서 사용하는 샘플 프로젝트에는 연속 통합 빌드가 포함되어 있습니다. 여기서는 이 빌드를 수정하지 않고 그대로 사용합니다. 그리고 지속적인 업데이트 릴리스 파이프라인도 포함되어 있습니다. 이 파이프라인의 경우 이전 작업에서 구현한 Azure 리소스에 배포하려면 약간 변경해야 합니다. 

1.  Azure DevOps 포털에서 **애플리케이션 성능 모니터링** 프로젝트가 표시된 웹 브라우저 창으로 전환하여 세로 탐색 창에 있는 **Pipelines** 를 선택하고 **파이프라인** 섹션에서 **릴리스** 를 선택합니다.
1.  릴리스 파이프라인 목록의 **PartsUnlimitedE2E** 창에서 **편집** 을 클릭합니다. 
1.  **모든 파이프라인 > PartsUnlimitedE2E** 창에서 **개발** 단계를 나타내는 사각형을 클릭하고 **개발** 창에서 **삭제** 를 클릭한 후에 **단계 삭제** 대화 상자에서 **확인** 을 클릭합니다.
1.  **모든 파이프라인 > PartsUnlimitedE2E** 창으로 돌아와 **QA** 단계를 나타내는 사각형을 클릭하고 **QA** 창에서 **삭제** 를 클릭한 후에 **단계 삭제** 대화 상자에서 **확인** 을 클릭합니다.
1.  **모든 파이프라인 > PartsUnlimitedE2E** 창으로 돌아와 **프로덕션** 단계를 나타내는 사각형에서 **작업 1개, 태스크 1개** 링크를 클릭합니다.
1.  **프로덕션** _ 단계의 작업 목록이 표시된 창에서 _ *Azure App Service 배포** 작업을 나타내는 항목을 클릭합니다.
1.  **Azure App Service 배포** 창의 **Azure 구독** 드롭다운 목록에서 이 랩에서 사용 중인 Azure 구독을 나타내는 항목을 선택하고 **권한 부여** 를 클릭하여 해당 서비스 연결을 만듭니다. 메시지가 표시되면 Azure 구독의 Owner 역할, 그리고 Azure 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할이 지정되어 있는 계정을 사용하여 로그인합니다.
1.  **모든 파이프라인 > PartsUnlimitedE2E** 창에서 **작업** 탭을 활성화한 상태로 **파이프라인** 탭 머리글을 클릭하여 파이프라인 다이어그램으로 돌아옵니다. 
1.  다이어그램에서 **프로덕션** 단계를 나타내는 사각형 왼쪽의 타원형 **배포 전 조건** 기호를 클릭합니다.
1.  **배포 전 조건** 창의 **트리거 선택** 섹션에서 **릴리스 후** 를 선택합니다.

    > **참고**: 이렇게 하면 프로젝트 빌드 파이프라인이 정상적으로 실행된 후에 릴리스 파이프라인이 호출됩니다.

1.  **모든 파이프라인 > PartsUnlimitedE2E** 창의 **파이프라인** 탭을 활성화한 상태로 **변수** 탭 머리글을 클릭합니다.
1.  변수 목록에서 **WebsiteName** 변수의 값을 이 랩 앞부분에서 만든 Azure App Service 웹앱의 이름과 일치하도록 설정합니다.
1.  창 오른쪽 위의 **저장** 을 클릭하고 메시지가 표시되면 **저장** 대화 상자에서 **확인** 을 다시 클릭합니다.

    > **참고**: 릴리스 파이프라인이 생성되었으므로 master 분기로 커밋을 수행하면 빌드 및 릴리스 파이프라인이 트리거됩니다.

1.  Azure DevOps 포털이 표시된 웹 브라우저 창의 세로 탐색 창에서 **리포지토리** 를 클릭합니다. 
1.  **파일** 창에서 **PartsUnlimited-aspnet45/src/PartsUnlimitedWebsite/Web.config** 파일로 이동하여 해당 파일을 선택합니다.

    > **참고**: 이 애플리케이션에는 Application Insights 키와 SQL 연결용 구성 설정이 이미 적용되어 있습니다. 

1. **Web.config** 창에서 Application Insights 키와 SQL 연결을 참조하는 줄을 검토합니다.

    ```xml
    <add key="Keys:ApplicationInsights:InstrumentationKey" value="0839cc6f-b99b-44b1-9d74-4e408b7aee29" />
    ```

    ```xml
    <connectionStrings>
       <add name="DefaultConnectionString" connectionString="Server=(localdb)\mssqllocaldb;Database=PartsUnlimitedWebsite;Integrated Security=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    > **참고**: 배포 후에 Azure Portal에서 이러한 설정의 값을 랩 앞부분에서 배포한 Azure SQL 데이터베이스 및 Azure Application Insights를 나타내도록 수정합니다.

    > **참고**: 이제 관련 코드를 수정하지 않고 파일 끝에 빈 줄만 추가하여 빌드 및 릴리스 프로세스를 트리거합니다.

1. **Web.config** 창에서 **편집** 을 클릭하고 파일 끝에 빈 줄을 추가한 다음 **커밋** 을 클릭합니다. 그런 다음 **커밋** 창에서 **커밋** 을 다시 클릭합니다.

    > **참고**: 이렇게 하면 새 빌드가 시작되며, 최종적으로는 Azure에 빌드가 배포됩니다. 빌드가 완료될 때까지 기다리지 말고 다음 단계를 진행하세요.

1.  Azure Portal이 표시된 웹 브라우저로 전환한 다음 랩 앞부분에서 프로비전한 App Service 웹앱으로 이동합니다. 
1.  App Service 웹앱 블레이드 왼쪽의 세로 메뉴를 클릭하고 **설정** 섹션에서 **구성** 탭을 클릭합니다.
1. **애플리케이션 설정** 목록에서 **APPINSIGHTS_INSTRUMENTATIONKEY** 항목을 클릭합니다. (이 항목이 표시되지 않으면 설정 아래에서 **Application Insights** 를 선택하고, Application Insights를 **사용** 하도록 설정한 다음, **적용** 을 선택합니다.) 
1.  **애플리케이션 설정 추가/편집** 블레이드의 **값** 텍스트 상자에서 텍스트를 복사하고 **취소** 를 클릭합니다.

    > **참고**: 이 값은 App Service 웹앱 배포 중에 추가한 기본 설정으로, Application Insights ID는 이미 포함되어 있습니다. 여기서는 앱에 필요한 새 설정을 다른 이름(값은 일치함)으로 추가해야 합니다. 이 랩의 샘플을 사용하려면 이 설정이 필요합니다.

1.  **애플리케이션 설정** 섹션에서 **새 애플리케이션 설정** 을 클릭합니다.
1. **애플리케이션 설정 추가/편집** 블레이드의 **이름** 텍스트 상자에는 **Keys:ApplicationInsights:InstrumentationKey** 를 입력하고 **값** 텍스트 상자에는 클립보드에 복사한 문자열을 입력한 후에 **확인** 을 클릭합니다. **저장** 을 선택합니다.

    > **참고**: 애플리케이션 설정과 연결 문자열을 변경하면 웹앱 다시 시작이 트리거됩니다.

1.  Azure DevOps 포털이 표시된 웹 브라우저 창으로 다시 전환하여 세로 탐색 창에서 **Pipelines** 를 선택하고 **파이프라인** 섹션에서 가장 최근에 실행한 빌드 파이프라인을 나타내는 항목을 클릭합니다.
1.  빌드가 아직 완료되지 않았으면 완료될 때까지 추적한 다음 세로 탐색 창의 **파이프라인** 섹션에서 **릴리스** 를 클릭하고 **PartsUnlimiteE2E** 창에서 **릴리스-1** 을 클릭한 후에 릴리스 파이프라인을 완료될 때까지 실행합니다.
1.  Azure Portal이 표시된 웹 브라우저 창으로 전환한 다음 **App Service 웹앱** 블레이드 왼쪽의 세로 메뉴 모음에서 **개요** 를 클릭합니다. 
1.  오른쪽의 **필수** 섹션에서 **URL** 링크를 클릭합니다. 그러면 다른 웹 브라우저 탭이 자동으로 열리고 **Parts Unlimited** 웹 사이트가 표시됩니다.
1.  **Parts Unlimited** 웹 사이트가 정상적으로 로드되는지 확인합니다. 

#### <a name="task-2-generate-and-review-application-traffic"></a>작업 2: 애플리케이션 트래픽 생성 및 검토

이 작업에서는 이전 작업에서 배포한 App Service 웹앱을 대상으로 트래픽을 생성하고, 웹앱과 연결된 Application Insights 리소스가 수집한 데이터를 검토합니다.

1.  **Parts Unlimited** 웹 사이트가 표시된 웹 브라우저 창의 페이지 간을 이동하여 트래픽을 생성합니다.
1.  **Parts Unlimited** 웹 사이트에서 **브레이크** 메뉴 항목을 클릭합니다.
1.  브라우저 창 위쪽의 URL 텍스트 상자에 표시된 URL 문자열 끝에 **1** 을 추가하고 **Enter** 키를 누릅니다. 그러면 **CategoryId** 매개 변수가 **11** 로 설정됩니다. 

    > **참고**: 이 범주가 없으므로 이 단계를 수행하면 서버 오류가 트리거됩니다. 페이지를 몇 번 새로 고쳐 오류를 더 생성합니다.

1.  Azure Portal이 표시된 웹 브라우저 탭으로 돌아옵니다.
1.  Azure Portal이 표시된 웹 브라우저 탭 내 **App Service** 웹앱 블레이드 왼쪽의 세로 메뉴 모음에 있는 **설정** 섹션에서 **Application Insights** 항목을 클릭하여 **Application Insights** 구성 블레이드를 표시합니다.

    > **참고**: 이 블레이드에는 여러 앱 유형과 Application Insights를 통합하는 데 사용할 수 있는 설정이 포함되어 있습니다. 기본 환경에서도 앱 추적 및 모니터용으로 다양한 데이터가 생성되지만, API를 사용하면 더욱 특수한 시나리오와 사용자 지정 이벤트 추적을 수행할 수 있습니다.

1.  **Application Insights** 구성 블레이드에서 **Application Insights 데이터 보기** 링크를 클릭합니다.
1.  그러면 나타나는 **Application Insights** 블레이드에 표시된 차트에 나와 있는 수집된 데이터의 다양한 특성을 검토합니다. 예를 들어 이 작업의 앞부분에서 생성한 트래픽과 트리거했지만 실패한 요청 등을 검토할 수 있습니다.

    > **참고**: 즉시 아무것도 표시되지 않으면 몇 분 동안 기다렸다가 개요 섹션에 로그가 표시되기 시작할 때까지 페이지를 새로 고칩니다.

#### <a name="task-3-investigate-application-performance"></a>작업 3: 애플리케이션 성능 조사

이 작업에서는 Application Insights를 사용하여 App Service 웹앱의 성능을 조사합니다.

1.  **Application Insights** 블레이드 왼쪽의 세로 메뉴에 있는 **조사** 섹션에서 **애플리케이션 맵** 을 클릭합니다.

    > **참고**: 애플리케이션 맵을 사용하면 분산 애플리케이션의 모든 구성 요소에서 성능 병목 상태 또는 오류 핫스폿을 파악할 수 있습니다. 맵의 각 노드는 애플리케이션 구성 요소 또는 해당 종속성과 상태 KPI 및 경고 상태를 나타냅니다. 구성 요소부터 Application Insights 이벤트와 같은 보다 자세한 진단까지 클릭하면서 살펴볼 수 있습니다. 앱에서 Azure 서비스를 사용하는 경우 SQL Database Advisor 추천 등 해당 서비스와 관련된 Azure 진단도 파악할 수 있습니다.

1.  **Application Insights** 블레이드 왼쪽의 세로 메뉴에 있는 **조사** 섹션에서 **스마트 검색** 을 클릭합니다.

    > **참고**: 스마트 검색을 사용하면 웹 애플리케이션에서 발생 가능한 성능 문제 관련 경고가 자동으로 표시됩니다. 앱에서 Application Insights로 보내는 원격 분석의 사전 분석을 수행합니다. 실패율이나 클라이언트 또는 서버 성능의 비정상적인 패턴이 갑자기 증가하는 경우 경고가 발생합니다. 이 기능에는 구성이 필요하지 않습니다. 애플리케이션에서 충분한 원격 분석을 보내는 경우 작동합니다. 여기서는 앱을 방금 배포했으므로 아직은 데이터가 없습니다.

1.  **Application Insights** 블레이드 왼쪽의 세로 메뉴에 있는 **조사** 섹션에서 **라이브 메트릭** 을 클릭합니다.

    > **참고**: 라이브 메트릭 스트림을 사용하면 프로덕션 환경 내의 라이브 웹 애플리케이션 하트비트를 프로브할 수 있습니다. 메트릭과 성능 카운터를 선택 및 필터링하여 서비스에 영향을 주지 않고 실시간으로 정보를 확인할 수 있습니다. 샘플 실패 요청 및 예외에서 스택 추적을 검사할 수도 있습니다.

1.  **Parts Unlimited** 웹 사이트가 표시된 웹 브라우저로 돌아온 다음 페이지 간을 이동하여 트래픽을 생성하고 서버 오류도 몇 번 트리거합니다. 
1.  Azure Portal이 표시된 웹 브라우저로 돌아와 수신되는 라이브 트래픽을 확인합니다. 
1.  **Application Insights** 블레이드 왼쪽의 세로 메뉴에 있는 **조사** 섹션에서 **트랜잭션 검색** 을 클릭합니다.

    > **참고**: 트랜잭션 검색에서는 필요한 정보를 파악하는 데 필요한 원격 분석을 정확하게 찾을 수 있는 유동적인 인터페이스를 제공합니다. 

1.  **트랜잭션 검색** 블레이드에서 **지난 24시간 동안의 모든 데이터 보기** 를 클릭합니다.

    > **참고**: 결과에는 모든 원격 분석 데이터가 포함됩니다. 여러 속성을 기준으로 결과를 필터링할 수 있습니다.

1.  **트랜잭션 검색** 블레이드 가운데의 결과 목록 바로 오른쪽에 있는 **그룹화된 결과** 를 클릭합니다. 

    > **참고**: 이러한 결과는 공통 속성을 기준으로 그룹화됩니다.

1.  **트랜잭션 검색** 블레이드 가운데의 결과 목록 바로 오른쪽에 있는 **결과** 를 클릭하여 모든 결과 목록이 표시된 원래 보기로 돌아옵니다.
1.  **트랜잭션 검색** 블레이드 위쪽의 **이벤트 유형 = 모두 선택됨** 을 클릭하고 드롭다운 목록에서 **모두 선택** 체크박스 선택을 취소한 후에 이벤트 유형 목록에서 **예외** 체크박스를 선택합니다.

    > **참고**: 결과에는 앞에서 생성했던 오류를 나타내는 예외 몇 개가 포함되어 있습니다. 

1.  결과 목록에서 예외 중 하나를 클릭합니다. 그러면 표시되는 **엔드투엔드 트랜잭션 세부 정보** 블레이드에서 요청과 관련한 해당 예외의 전체 시간 표시 막대 보기가 제공됩니다. 
1.  **엔드투엔드 트랜잭션 세부 정보** 블레이드 아래쪽에서 **모든 원격 분석 보기** 를 클릭합니다.

    > **참고**: **원격 분석** 보기에서는 동일한 데이터가 다른 형식으로 제공됩니다. **엔드투엔드 트랜잭션 세부 정보** 블레이드 오른쪽에서도 예외의 속성, 호출 스택 등 예외 자체의 세부 정보를 검토할 수 있습니다.

1.  **엔드투엔드 트랜잭션 세부 정보** 블레이드를 닫고 **트랜잭션 검색** 블레이드로 돌아온 다음 왼쪽 세로 메뉴의 **조사** 섹션에서 **사용 가능성** 을 클릭합니다.

    > **참고**: 웹앱이나 웹 사이트를 서버에 배포하고 나면 테스트를 설정하여 사용 가능성과 응답 여부를 모니터링할 수 있습니다. Application Insights는 전 세계 지점에서 정기적인 간격으로 애플리케이션에 웹 요청을 보냅니다. 그리고 애플리케이션이 응답하지 않거나 느리게 응답하는 경우 경고를 제공합니다. 

1.  **사용 가능성** 블레이드의 도구 모음에서 **+ 테스트 추가** 를 클릭합니다.
1.  **테스트 만들기** 창의 **테스트 이름** 텍스트 상자에 홈 페이지를 입력하고 **URL** 을 App Service 웹앱 루트로 설정한 후에 **만들기** 를 클릭합니다.

    > **참고**: 테스트는 즉시 실행되지 않으므로 데이터는 생성되지 않습니다. 나중에 다시 확인하면 라이브 사이트를 대상으로 실행된 테스트를 반영해 사용 가능성 데이터가 업데이트되었음을 확인할 수 있습니다. 지금은 데이터가 생성될 때까지 기다리지 않아도 됩니다.

1.  **사용 가능성** 블레이드 왼쪽의 세로 메뉴에 있는 **조사** 섹션에서 **실패** 를 클릭합니다.

    > **참고**: 실패 보기에서는 모든 예외 보고서가 대시보드 하나에 집계되어 표시됩니다. 이 대시보드에서 종속성, 예외 등의 필터를 기준으로 관련 데이터를 쉽게 찾을 수 있습니다. 

1.  **실패** 블레이드 오른쪽 위에 있는 **주요 3개 응답 코드** 목록에서 **500** 오류 수를 나타내는 링크를 클릭합니다.

    > **참고**: 그러면 이 HTTP 응답 코드와 일치하는 예외 목록이 표시됩니다. 제안 예외를 선택하면 앞에서 검토했던 것과 같은 예외 보기가 표시됩니다.

1.  **실패** 블레이드 왼쪽의 세로 메뉴에 있는 **조사** 섹션에서 **성능** 을 클릭합니다.

    > **참고**: 성능 보기에서 제공되는 대시보드에서는 수집한 원격 분석을 기준으로 애플리케이션 성능 세부 정보를 간편하게 확인할 수 있습니다.

1.  **성능** 블레이드 왼쪽의 세로 메뉴에 있는 **모니터링** 섹션에서 **메트릭** 을 클릭합니다.

    > **참고**: Application Insights의 메트릭은 애플리케이션의 원격 분석에서 전송된 측정된 값 및 이벤트 수입니다. 성능 문제를 감지하고 애플리케이션 사용 방식의 추세를 볼 수 있습니다. 다양한 표준 메트릭이 있으며 사용자 고유의 사용자 지정 메트릭 및 이벤트를 만들 수도 있습니다. 

1.  **메트릭** 블레이드의 필터 섹션에서 **메트릭 선택** 을 클릭하고 드롭다운 목록에서 **서버 요청** 을 선택합니다.

    > **참고**: 분할 기능을 사용하여 데이터를 구분할 수도 있습니다. 

1.  새로 표시된 차트 위쪽의 **분할 적용** 을 클릭한 다음 표시되는 필터의 **값 선택** 드롭다운 목록에서 **작업 이름** 을 선택합니다. 

    > **참고**: 이렇게 하면 서버 요청이 참조 페이지를 기준으로 분할되어 차트에서 각기 다른 색으로 표시됩니다.

#### <a name="task-4-track-application-usage"></a>작업 4: 애플리케이션 사용량 추적

> **참고**: Application Insights에서는 애플리케이션 사용량을 추적하는 데 사용할 수 있는 광범위한 기능 세트를 제공합니다. 

1.  **메트릭** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **사용자** 를 클릭합니다.

    > **참고**: 이 랩에서 사용하는 애플리케이션에는 아직 사용자가 많지 않지만 몇 가지 데이터는 제공됩니다. 

1.  **사용자** 블레이드의 기본 차트 아래에서 **자세한 정보 보기** 를 클릭합니다. 그러면 블레이드가 아래쪽으로 확장되어 추가 데이터가 표시됩니다.
1.  아래쪽으로 스크롤하여 지역, 운영 체제, 브라우저 관련 세부 정보를 검토합니다.

    > **참고**: 사용자별 데이터를 드릴다운하여 사용자별 사용 패턴을 더 자세히 파악할 수도 있습니다.

1.  **사용자** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **이벤트** 를 클릭합니다.
1.  **이벤트** 블레이드의 기본 차트 아래에서 **자세한 정보 보기** 를 클릭합니다.

    > **참고**: 이렇게 하면 나타나는 화면에는 현재까지 발생한 기본 제공 이벤트가 사이트 사용 현황을 기준으로 표시됩니다. 요구에 맞는 사용자 지정 데이터를 사용해 사용자 지정 이벤트를 프로그래밍 방식으로 추가할 수 있습니다.

1.  **이벤트** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **유입 경로** 를 클릭합니다.

    > **참고**: 고객 환경을 이해하는 것이 비즈니스에 가장 중요합니다. 애플리케이션을 사용하는 과정에서 여러 단계를 수행해야 하는 경우에는 대다수 고객이 적절한 프로세스를 진행하는지를 확인해야 합니다. 웹 애플리케이션에서 일련의 단계를 거치는 것을 깔때기라고 합니다. Azure Application Insights Funnels를 사용하여 사용자에 대한 정보를 얻고 단계별 전환율을 모니터링할 수 있습니다.

1.  **유입 경로** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **사용자 흐름** 을 클릭합니다.

    > **참고**: 사용자 흐름 도구는 지정한 초기 페이지 보기, 사용자 지정 이벤트 또는 예외에서 시작됩니다. 초기 이벤트가 정해지면 해당하는 사용자 세션 전후에 발생한 이벤트가 사용자 흐름에 표시됩니다. 다양한 두께의 선은 사용자가 따라간 각 경로의 횟수를 보여줍니다. **세션 시작** 노드가 세션 시작 부분입니다. **세션 종료** 노드에는 이전 노드를 시작했지만 페이지 보기나 사용자 지정 이벤트가 생성되지 않은 사용자의 수가 표시됩니다. 이러한 사용자는 다른 사이트로 이동한 사용자에 해당할 가능성이 높습니다.

1.  **사용자 흐름** 블레이드에서 **이벤트 선택** 을 클릭하고 **편집** 창의 **페이지 보기** 섹션에 있는 **초기 이벤트** 드롭다운 목록에서 **홈 페이지 - Parts Unlimited** 항목을 선택한 다음 **그래프 만들기** 를 클릭합니다.

1.  **사용자 흐름** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **재방문 주기** 를 클릭합니다.

    > **참고**: Application Insights의 재방문 주기 기능은 앱으로 돌아온 사용자 수와 특정 작업을 수행하거나 목표를 달성하는 빈도를 분석하는 데 도움을 줍니다. 예를 들어 게임 사이트를 실행하는 경우 게임에서 진 후에 사이트로 돌아오는 사용자 수와 이긴 후에 돌아온 사용자 수를 비교할 수 있습니다. 이러한 지식이 있으면 사용자 환경 및 비즈니스 전략을 둘 다 향상시킬 수 있습니다.

    > **참고**: 사용자 재방문 주기 분석 환경은 Azure 통합 문서로 전환되었습니다.

1.  **재방문 주기** 블레이드에서 **재방문 주기 분석 통합 문서** 를 클릭하고 **전체 재방문 주기** 차트를 검토한 다음 블레이드를 닫습니다.
1.  **재방문 주기** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **영향** 을 클릭합니다.

    > **참고**: 영향 기능은 로드 시간 등의 웹 사이트 속성이 앱의 여러 부분에서 전환율에 주는 영향의 정도를 분석합니다. 자세히 설명하자면, 영향 기능은 규모에 관계없이 임의의 페이지 보기, 사용자 지정 이벤트 또는 요청이 페이지 보기나 사용자 지정 이벤트에 주는 영향을 주는 방식을 파악합니다.

    > **참고**: 영향 분석 환경은 Azure 통합 문서로 전환되었습니다.

1.  **영향** 블레이드에서 **영향 분석 통합 문서** 를 클릭하고 **영향** 블레이드의 **페이지 보기** 섹션에 있는 **선택한 이벤트** 드롭다운 목록에서 **홈 페이지 - Parts Unlimited** 를 선택합니다. 그런 다음 **영향을 주는 이벤트** 에서 **제품 찾아보기 - Parts Unlimited** 를 선택하고 결과를 검토한 후에 블레이드를 닫습니다.
1.  **영향** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **코호트** 를 클릭합니다.

    > **참고**: 코호트는 공통점이 있는 다양한 사용자, 세션, 이벤트 또는 작업의 집합입니다. Application Insights에서 코호트는 분석 쿼리에 의해 정의됩니다. 특정 사용자 또는 이벤트 세트를 반복적으로 분석해야 하는 경우 코호트 기능이 사용자가 관심을 두는 세트를 보다 유연하게 정확히 나타낼 수 있습니다. 코호트는 필터와 비슷한 방식으로 사용되지만 코호트의 정의는 사용자 지정 분석 쿼리에서 빌드되므로 훨씬 더 적응성이 뛰어나고 복잡합니다. 필터와 달리, 코호트는 팀의 다른 멤버가 재사용할 수 있게 저장할 수 있습니다.

1.  **코호트** 블레이드 왼쪽의 세로 메뉴에 있는 **사용량** 섹션에서 **자세히** 를 클릭합니다.

    > **참고**: 이 블레이드에는 검토 가능한 여러 보고서와 템플릿이 포함되어 있습니다.

1.  **기타 \| 갤러리** 블레이드의 **사용량** 섹션에서 **페이지 보기 분석** 을 클릭하고 해당 블레이드의 콘텐츠를 검토합니다.

    > **참고**: 이 보고서에서는 페이지 보기 관련 인사이트가 제공됩니다. 그 외에도 다양한 보고서가 기본 제공됩니다. 기본 제공 보고서를 사용자 지정하고 새 보고서를 작성할 수 있습니다.

#### <a name="task-5-configure-web-app-alerts"></a>작업 5: 웹앱 경고 구성

1.  **기타 \| 갤러리** 블레이드에 있는 동안 왼쪽 세로 메뉴의 **모니터링** 섹션에서 **경고** 를 클릭합니다. 

    > **참고**: 경고를 사용하면 Application Insights 측정값이 지정한 조건에 도달할 때 작업을 수행하는 트리거를 설정할 수 있습니다.

1.  **경고** 블레이드의 도구 모음에서 **+ 새 경고 규칙** 을 클릭합니다.
1.  **경고 규칙 만들기** 블레이드의 **범위** 섹션에서는 현재 Application Insights 리소스가 기본적으로 선택됩니다. 
1.  **경고 규칙 만들기** 블레이드의 **조건** 섹션에서 **조건 추가** 를 클릭합니다.
1.  **신호 논리 구성** 블레이드에서 **실패한 요청** 메트릭을 검색하여 선택합니다.
1.  **신호 논리 구성** 블레이드 아래쪽의 **경고 논리** 섹션으로 스크롤하여 **임계값** 이 **정적** 으로 설정되어 있는지 확인한 다음 **임계값** 을 **1** 로 설정합니다. 

    > **참고**: 이렇게 하면 두 번째 실패한 요청이 보고된 후 경고가 트리거됩니다. 기본적으로는 지난 5분 동안 집계한 측정값을 기준으로 하여 1분마다 조건을 평가합니다. 

1.  **신호 논리 구성** 블레이드에서 **완료** 를 클릭합니다.

    > **참고**: 조건을 생성했으므로 이제 해당 조건을 실행할 **작업 그룹** 을 정의해야 합니다. 

1.  **경고 규칙 만들기** 블레이드로 돌아가서 **작업** 섹션에서 **작업 그룹 선택** 을 클릭하고 **이 경고 규칙에 연결할 작업 그룹 선택**에서 **+ 작업 그룹 만들기** 를 클릭합니다.
1.  **작업 그룹 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정하고 **다음: 알림 >** 을 클릭합니다.

    | 설정 | 값 |
    | --- | --- |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | 새 리소스 그룹의 이름 **az400m17l01b-RG** |
    | 작업 그룹 이름 | **az400m17-action-group** |
    | 표시 이름 | **az400m17-ag** |

1.  **작업 그룹 만들기** 블레이드의 **알림** 탭 내 **알림 유형** 드롭다운 목록에서 **메일/SMS 메시지/푸시/음성** 을 선택합니다. 그러면 **메일/SMS 메시지/푸시/음성** 블레이드가 열립니다. 
1.  **전자 메일/SMS 메시지/푸시/음성** 블레이드에서 **전자 메일** 체크박스를 선택하고 **전자 메일** 텍스트 상자에 전자 메일 주소를 입력한 후에 **확인** 을 클릭합니다.
1.  **작업 그룹 만들기** 블레이드의 **알림** 탭으로 돌아가서 **이름** 텍스트 상자에 **email** 을 입력하고 **다음: 작업 >** 을 클릭합니다.
1.  **작업 그룹 만들기** 블레이드의 **작업** 탭에 있는 **작업 유형** 드롭다운 목록에서 옵션을 변경하지는 않고 제공되는 옵션을 검토한 다음 **검토 + 만들기** 를 클릭합니다.
1.  **작업 그룹 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다.
1.  **경고 규칙 만들기** 블레이드의 **경고 규칙 세부 정보** 섹션에 있는 **경고 규칙 이름** 텍스트 상자에 **az400m17 lab alert rule** 을 입력합니다. 그런 다음 나머지 경고 규칙 설정을 수정하지는 않고 검토한 후에 **경고 규칙 만들기** 를 클릭합니다.
1.  **Parts Unlimited** 웹 사이트가 표시된 웹 브라우저 창으로 전환하여 **Parts Unlimited** 웹 사이트에서 **브레이크** 메뉴 항목을 클릭합니다.
1.  브라우저 창 위쪽의 URL 텍스트 상자에 표시된 URL 문자열 끝에 **1** 을 추가하고 **Enter** 키를 누릅니다. 그러면 **CategoryId** 매개 변수가 **11** 로 설정됩니다. 

    > **참고**: 이 범주가 없으므로 이 단계를 수행하면 서버 오류가 트리거됩니다. 페이지를 몇 번 새로 고쳐 오류를 더 생성합니다.

1.  5분 정도 지난 후 이메일 계정에 정의한 경고가 트리거되었다는 이메일이 수신되었는지 확인합니다.

### <a name="exercise-2-remove-the-azure-lab-resources"></a>연습 2: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다. 

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### <a name="task-1-remove-the-azure-lab-resources"></a>작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다. 

1.  Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1.  다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m17l01')].name" --output tsv
    ```

1.  다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m17l01')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## <a name="review"></a>검토

이 연습에서는 Azure DevOps 파이프라인을 사용하여 Azure App Service에 웹앱을 배포하고 해당 웹앱을 대상으로 트래픽을 생성했습니다. 그런 다음 Application Insights를 사용하여 웹 트래픽 검토, 애플리케이션 성능 조사, 애플리케이션 사용량 추적, 경고 구성을 수행했습니다.
