---
lab:
  title: '랩 08: Docker 컨테이너를 Azure App Service 웹앱에 배포'
  module: 'Module 03: Create and manage containers using Docker and Kubernetes'
---

# <a name="lab-08-deploying-docker-containers-to-azure-app-service-web-apps"></a>랩 08: Docker 컨테이너를 Azure App Service 웹앱에 배포

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독에서 Owner 또는 Contributor 역할이 지정된 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/en-us/azure/active-directory/roles/manage-roles-portal#view-my-roles)을 참조하세요.

## <a name="lab-overview"></a>랩 개요

이 랩에서는 Azure DevOps CI/CD 파이프라인을 사용하여 사용자 지정 Docker 이미지를 빌드하고, Azure Container Registry에 푸시하고, Azure App Service에 컨테이너로 배포하는 방법을 배웁니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Microsoft에서 호스트하는 Linux 에이전트를 사용하여 사용자 지정 Docker 이미지를 빌드합니다.
- Azure Container Registry에 이미지 푸시
- Azure DevOps를 사용하여 Docker 이미지를 Azure App Service에 컨테이너로 배포

## <a name="estimated-timing-60-minutes"></a>예상 소요 시간: 60분

## <a name="instructions"></a>Instructions

### <a name="exercise-1-configure-the-lab-prerequisites"></a>연습 1: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿과 Azure 리소스(Azure App Service 웹앱, Azure Container Registry 인스턴스 및 Azure SQL 데이터베이스 포함)를 기반으로 하여 팀 프로젝트를 설정합니다.

#### <a name="task-1-configure-the-team-project"></a>작업 1: 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 Docker 템플릿을 기반으로 새 프로젝트를 생성합니다.

> **참고**: Docker 템플릿 기반 프로젝트는 컨테이너화된 ASP.NET Core 앱을 만들어 Azure App Service에 배포합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 [https://docs.microsoft.com/en-us/azure/devops/demo-gen](https://docs.microsoft.com/en-us/azure/devops/demo-gen)을 참조하세요.

1. **로그인**을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락**을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **Azure App Service 웹앱에 Docker 컨테이너 배포**를 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택**을 클릭합니다.
1. 템플릿 목록의 도구 모음에서 **DevOps 랩**을 클릭하고 **DevOps 랩** 머리글을 선택한 다음 **Docker** 템플릿을 클릭한 후 **템플릿 선택**을 클릭합니다.
1. **새 프로젝트 만들기** 페이지로 돌아와서 누락된 확장을 설치하라는 메시지가 표시되면 **Docker 통합** 레이블 아래의 체크박스를 선택하고 **프로젝트 만들기**를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1. **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동**을 클릭합니다.

#### <a name="task-2-create-azure-resources"></a>작업 2: Azure 리소스 생성

이 작업에서는 Azure Cloud Shell을 사용하여 이 랩에 필요한 Azure 리소스를 만듭니다.

- Azure Container Registry
- Azure Web App for Containers
- Azure SQL Database

1. 랩 컴퓨터에서 웹 브라우저를 시작하여 [**Azure Portal**](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 Azure 구독의 Owner 역할, 그리고 해당 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할을 가진 사용자 계정으로 로그인합니다.
1. Azure Portal에 표시되는 웹 브라우저의 도구 모음에서 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
1. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.

    >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다.

1. Cloud Shell 창의 **Bash** 세션에서 다음 명령을 실행하여 이 랩에서 리소스를 배포할 Azure 지역을 나타내는 변수, 이러한 리소스(Azure Container Registry 인스턴스, Azure App Service 계획 이름, Azure 웹앱 이름, Azure SQL Database 논리 서버 이름 및 Azure SQL 데이터베이스 이름 포함)를 포함하는 리소스 그룹 및 이러한 리소스의 이름을 만듭니다.

1. Cloud Shell 창의 Bash 세션에서 다음 명령을 실행하여 이 랩에서 배포하는 Azure 리소스를 호스트할 리소스 그룹을 만듭니다. 여기서 `<Azure_region>` 자리 표시자는 이러한 리소스를 배포할 Azure 지역 이름(예: ‘eastus’)으로 바꿉니다.

    ```bash
    LOCATION='<Azure_region>'
    ```

    >**참고**: `az account list-locations -o table`을 실행하여 Azure 지역 이름을 식별할 수 있습니다.

1. 다음 명령을 실행하여 Azure 리소스(Azure Container Registry 인스턴스, Azure App Service 계획 이름, Azure 웹앱 이름, Azure SQL Database 논리 서버 이름 및 Azure SQL 데이터베이스 이름 포함) 이름을 나타내는 변수를 만듭니다

    ```bash
    RG_NAME='az400m1501a-RG'
    ACR_NAME=az400m151acr$RANDOM$RANDOM
    APP_SVC_PLAN='az400m1501a-app-svc-plan'
    WEB_APP_NAME=az400m151web$RANDOM$RANDOM
    SQLDB_SRV_NAME=az400m15sqlsrv$RANDOM$RANDOM
    SQLDB_NAME='az400m15sqldb'
    ```

    >**참고**: `az account list-locations -o table`을 실행하여 Azure 지역 이름을 식별할 수 있습니다.

1. 다음 명령을 실행하여 이 랩에 필요한 Azure 리소스가 필요한 모든 리소스를 만듭니다.

    ```bash
    az group create --name $RG_NAME --location $LOCATION
    az acr create --name $ACR_NAME --resource-group $RG_NAME --location $LOCATION --sku Standard --admin-enabled true
    az appservice plan create --name 'az400m1501a-app-svc-plan' --location $LOCATION --resource-group $RG_NAME --is-linux
    az webapp create --name $WEB_APP_NAME --resource-group $RG_NAME --plan $APP_SVC_PLAN --deployment-container-image-name elnably/dockerimagetest
    IMAGE_NAME=myhealth.web
    az webapp config container set --name $WEB_APP_NAME --resource-group $RG_NAME --docker-custom-image-name $IMAGE_NAME --docker-registry-server-url $ACR_NAME.azurecr.io/$IMAGE_NAME:latest --docker-registry-server-url https://$ACR_NAME.azurecr.io
    az sql server create --name $SQLDB_SRV_NAME --resource-group $RG_NAME --location $LOCATION --admin-user sqladmin --admin-password Pa55w.rd1234
    az sql db create --name $SQLDB_NAME --resource-group $RG_NAME --server $SQLDB_SRV_NAME --service-objective S0 --no-wait 
    az sql server firewall-rule create --name AllowAllAzure --resource-group $RG_NAME --server $SQLDB_SRV_NAME --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

    >**참고**: 프로비저닝 프로세스가 완료될 때까지 기다립니다. 5분 정도 걸릴 수 있습니다.

1. 다음 명령을 실행하여 새로 만든 Azure 웹앱의 연결 문자열을 구성합니다. 여기서 $SQLDB_SRV_NAME 및 $SQLDB_NAME 자리 표시자는 Azure SQL Database 논리 서버 및 해당 데이터베이스 인스턴스 이름 값으로 바꿉니다

    ```bash
    CONNECTION_STRING="Data Source=tcp:$SQLDB_SRV_NAME.database.windows.net,1433;Initial Catalog=$SQLDB_NAME;User Id=sqladmin;Password=Pa55w.rd1234;"
    az webapp config connection-string set --name $WEB_APP_NAME --resource-group $RG_NAME --connection-string-type SQLAzure --settings defaultConnection="$CONNECTION_STRING"
    ```

1. Azure Portal이 표시된 웹 브라우저에서 Cloud Shell 창을 닫고 **리소스 그룹** 블레이드로 이동한 다음 **리소스 그룹** 블레이드에서 **az400m1501a-RG** 항목을 선택합니다.
1. **az400m1501a-RG** 리소스 그룹 블레이드에서 해당 리소스 목록을 검토합니다.

    >**참고**: 논리 Azure SQL Database 서버의 이름을 기록합니다. 이 랩의 후반부에서 필요합니다.

1. **az400m1501a-RG** 리소스 그룹 블레이드의 리소스 목록에서 Container Registry 인스턴스를 나타내는 항목을 클릭합니다.
1. 컨테이너 레지스트리 블레이드 왼쪽의 세로 메뉴에 있는 **설정** 섹션에서 **액세스 키**를 클릭합니다.
1. 컨테이너 레지스트리 인스턴스의 **액세스 키** 블레이드에서 **레지스트리 이름**, **로그인 서버**, **관리 사용자** 및 **암호** 항목의 값을 확인합니다.

    >**참고**: **레지스트리 이름** 및 **로그인 서버** 값을 기록합니다. Values 레지스트리 이름과 관리 사용자 이름은 동일해야 합니다. 이 랩의 뒷부분에서 계정 이름이 필요합니다.

### <a name="exercise-2-deploy-a-docker-container-to-azure-app-service-web-app-using-azure-devops"></a>연습 2: Azure DevOps를 사용하여 Azure App Service 웹앱에 Docker 컨테이너 배포

이 연습에서는 Azure DevOps를 사용하여 Azure App Service 웹앱에 Docker 컨테이너를 배포합니다.

#### <a name="task-1-configure-continuous-integration-ci-and-continuous-delivery-cd"></a>작업 1: CI(연속 통합) 및 CD(지속적인 업데이트) 구성

이 작업에서는 Docker 컨테이너를 만들어 Azure App Service 웹앱에 배포하는 CI/CD 파이프라인을 구현하기 위해 이전 연습에서 생성한 Azure DevOps 프로젝트를 사용합니다.

1. 랩 컴퓨터에서 **Azure App Service 웹앱에 Docker 컨테이너 배포** 프로젝트가 열려 있는 Azure DevOps 포털이 표시된 웹 브라우저 창으로 전환하여 Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에서 **Repos**를 클릭합니다.

    >**참고**: 먼저 Docker 이미지에 대한 참조를 수정해야 합니다.

1. **Docker** 리포지토리 창의 파일 목록에서 **docker-compose.ci.build.yml**을 선택합니다.
1. **docker-compos.ci.build.yml** 창에서 **편집**을 클릭하고, 대상 Docker 이미지를 참조하는 줄 **5**를 `image: az400mp/aspnetcore-build:1.0-2.0`으로 바꾼 다음, **커밋**을 선택합니다. 그리고 작업을 확인하라는 메시지가 표시되면 **커밋**을 다시 클릭합니다.
1. **Docker** 리포지토리 창의 파일 목록에서 **src/MyHealth.Web** 폴더로 이동하고 **Dockerfile**을 선택합니다.
1. **Dockerfile** 창에서 **편집**을 클릭하고, 기본 Docker 이미지를 참조하는 줄 **1**을 `FROM az400mp/aspnetcore1.0:1.0.4`로 바꾼 다음, **커밋**을 선택합니다. 그리고 작업을 확인하라는 메시지가 표시되면 **커밋**을 다시 클릭합니다.
1. **Azure App Service 웹앱에 Docker 컨테이너 배포** 프로젝트가 열려 있는 Azure DevOps 포털이 표시된 웹 브라우저 창의 Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에서 **파이프라인**를 클릭합니다.

    >**참고**: 이제 빌드 파이프라인을 수정합니다.

1. **파이프라인** 창에서 **MHCDocker.build** 파이프라인을 나타내는 항목을 클릭하고 **MHCDocker.build** 창에서 **편집**을 클릭합니다.

    >**참고**: 빌드 파이프라인은 다음과 같은 작업으로 구성되어 있습니다.

    | 작업 | 사용량 |
    | ----- | ----- |
    | **서비스 실행** | 필요한 패키지를 복원하여 빌드 환경 준비 |
    | **서비스 빌드** | **myhealth.web** 이미지 빌드 |
    | **서비스 푸시** | **$(Build.BuildId)** 태그가 지정된 **myhealth.web** 이미지를 컨테이너 레지스트리로 푸시 |
    | **아티팩트 게시** | Azure DevOps 아티팩트를 통해 데이터베이스를 배포하도록 dacpac 공유 허용 |

1. **MHCDocker.build** 파이프라인 창에서 **파이프라인** 항목이 선택되어 있는지 확인하고 **에이전트 사양** 드롭다운 목록에서 **ubuntu-18.04**를 선택합니다.
1. **MHCDocker.build** 파이프라인 창의 파이프라인 작업 목록에서 **서비스 실행**을 클릭하고 오른쪽의 **Docker Compose** 창에 있는 **Azure 구독** 드롭다운 목록에서 이 랩에서 사용 중인 Azure 구독을 나타내는 항목을 선택한 다음 **권한 부여**를 클릭하여 해당 서비스 연결을 만듭니다. 메시지가 표시되면 Azure 구독의 Owner 역할, 그리고 Azure 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할이 지정되어 있는 계정을 사용하여 로그인합니다.

    >**참고**: 이 단계에서는 Azure 서비스 연결을 만듭니다. 구체적으로는 SPA(서비스 주체 인증)를 사용하여 대상 Azure 구독에 대한 연결을 정의하고 보호합니다.

1. 파이프라인의 작업 목록에서 **서비스 실행** 작업을 선택한 상태로 오른쪽의 **Docker Compose** 창에 있는 **Azure Container Registry** 드롭다운 목록에서 이 랩의 앞부분에서 만든 ACR 인스턴스를 나타내는 항목을 선택합니다(**필요하면 목록을 새로 고치거나** 로그인 서버 이름 입력).
1. 위의 두 단계를 반복하여 **서비스 빌드**, **서비스 푸시** 작업에서 **Azure 구독** 및 **Azure Container Registry** 설정을 구성합니다. 단, 이번에는 Azure 구독을 선택하는 대신 새로 만든 서비스 연결을 선택합니다.
1. 창 위쪽에 있는 동일한 **MHCDocker.build** 파이프라인 창에서 **저장 및 큐에 넣기** 단추 옆에 있는 아래쪽 캐럿을 클릭하고 **저장**을 클릭하여 변경 내용을 저장한 다음 메시지가 다시 표시되면 **저장**을 클릭합니다.

    >**참고**: 다음으로 릴리스 파이프라인을 수정합니다.

1. Azure DevOps 포털이 표시된 웹 브라우저 창에서 Azure DevOps 포털 맨 왼쪽 세로 메뉴 모음의 **파이프라인** 섹션에 있는 **릴리스**를 클릭합니다.
1. **파이프라인/릴리스** 창에서 **MHCDocker.release** 항목을 선택하고 **편집**을 클릭해야 합니다.
1. **모든 파이프라인/MHCDocker.release** 창에 있는 배포의 **개발** 단계를 나타내는 사각형에서 **2개 작업, 2개 태스크** 링크를 클릭합니다.

    >**참고**: 릴리스 파이프라인은 다음과 같은 작업으로 구성되어 있습니다.

    | 작업 | 사용량 |
    | ----- | ----- |
    | **Azure SQL 실행: DacpacTask** | 대상 스키마 및 데이터를 포함한 dacpac 아티팩트를 Azure SQL 데이터베이스에 배포 |
    | **Azure App Service 배포** | 빌드 단계 중에 지정된 컨테이너 레지스트리에서 생성된 Docker 이미지를 가져와서 해당 이미지를 Azure App Service 웹앱에 배포 |

1. 파이프라인의 작업 목록에서 **Azure SQL 실행: DacpacTask** 작업을 클릭하고, 오른쪽의 **Azure SQL 데이터베이스 배포** 창에 있는 **Azure 구독** 드롭다운 목록에서 이 작업 앞부분에서 만든 Azure 서비스 연결을 나타내는 항목을 선택합니다.
1. 파이프라인의 작업 목록에서 **Azure App Service 배포** 작업을 클릭한 다음 오른쪽의 **Azure App Service 배포** 창에 있는 **Azure 구독** 드롭다운 목록에서 이 작업 앞부분에서 만든 Azure 서비스 연결을 나타내는 항목을 선택합니다. **App Service 이름** 드롭다운 목록에서 이 랩 앞부분에서 배포한 Azure App Service 웹앱을 나타내는 항목을 선택합니다.

    >**참고**: 다음으로 배포에 필요한 에이전트 풀 정보를 구성해야 합니다.

1. **DB 배포** 작업을 선택하고, 오른쪽의 **에이전트 작업** 창에 있는 **에이전트 풀** 드롭다운 목록에서 **Azure Pipelines**를 선택한 다음, **에이전트 사양** 드롭다운 목록에서 **windows-2019**를 선택합니다.
1. **웹앱 배포** 작업을 선택하고, 오른쪽의 **에이전트 작업** 창에 있는 **에이전트 풀** 드롭다운 목록에서 **Azure Pipelines**를 선택한 다음, **에이전트 사양** 드롭다운 목록에서 **ubuntu-18.04**를 선택합니다.
1. 창 위쪽에서 **변수** 머리글을 클릭합니다.
1. 파이프라인의 변수 목록에서 다음 변수 값을 설정합니다.

    | 변수 | 값 |
    | -------- | ----- |
    | ACR | 이 랩의 이전 연습에서 기록한 Azure Container Registry 로그인 이름(**azurecr.io** 접미사 포함) |
    | DatabaseName | **az400m15sqldb** |
    | 암호 | **Pa55w.rd1234** |
    | SQLadmin | **sqladmin** |
    | SQLserver | 이 랩의 이전 연습에서 기록한 Azure SQL Database 논리 서버 이름(**database.windows.net** 접미사 포함) |

1. 창 오른쪽 위 모서리에서 **저장** 단추를 클릭하여 변경 내용을 저장한 다음 메시지가 다시 표시되면 **확인**을 클릭합니다.

#### <a name="task-2-trigger-build-and-release-pipelines-by-using-code-commit"></a>작업 2: 코드 커밋을 사용하여 빌드 및 릴리스 파이프라인 트리거

이 연습에서는 코드 커밋을 사용하여 빌드 및 릴리스 파이프라인을 트리거합니다.

1. Azure DevOps 포털이 표시된 웹 브라우저 창에서 Azure DevOps 포털 맨 왼쪽 세로 메뉴 모음의 **Repos**를 클릭합니다.

    >**참고**: 그러면 **파일** 창이 자동으로 표시됩니다.

1. **파일** 창에서 **src/MyHealth.Web/Views/Home** 폴더로 이동하여 **Index.cshtml** 파일을 나타내는 항목을 클릭한 다음 **편집**을 클릭하여 열고 편집합니다.
1. **Index.cshtml** 창의 **28**번 줄에서 **JOIN US**를 **CONTACT US**로 변경한 다음 창 오른쪽 위 모서리에서 **커밋**을 클릭합니다. 작업을 확인하라는 메시지가 표시되면 **커밋**을 다시 클릭합니다.

    >**참고**: 이 작업을 수행하면 소스 코드 자동 빌드를 시작합니다.

1. Azure DevOps 포털이 표시된 웹 브라우저 창에서 Azure DevOps 포털 맨 왼쪽 세로 메뉴 모음의 **파이프라인**을 클릭합니다.
1. **파이프라인** 창에서 커밋에 의해 트리거된 파이프라인 실행을 나타내는 항목을 클릭합니다.
1. **MHCDocker.build** 창에서 파이프라인 실행을 나타내는 항목을 클릭합니다.
1. 파이프라인 실행 **요약** 탭의 **작업** 섹션에서 **Docker** 항목을 클릭하고 표시되는 창에서 작업이 정상적으로 완료될 때까지 개별 작업의 진행률을 모니터링합니다.

    >**참고**: 이 빌드는 Docker 이미지를 생성하여 이를 Azure Container Registry로 푸시합니다. 빌드가 완료되면 해당 요약을 검토할 수 있습니다.

1. Azure DevOps 포털이 표시된 웹 브라우저 창에서 Azure DevOps 포털 맨 왼쪽 세로 메뉴 모음의 **파이프라인** 섹션에 있는 **릴리스**를 클릭합니다.
1. **릴리스** 창에서 정상적인 빌드에 의해 트리거된 최신 릴리스를 나타내는 항목을 클릭합니다.
1. **MHCDocker.release > 릴리스-1** 창에서 **개발** 단계를 나타내는 사각형을 선택합니다.
1. **MHCDocker.release > 릴리스-1 > 개발** 창에서 정상적으로 완료될 때까지 릴리스 진행률을 모니터링합니다.

    >**참고**: 이 릴리스는 빌드 프로세스에 의해 생성된 Docker 이미지를 App Service 웹앱에 배포합니다. 릴리스가 완료되면 해당 요약 및 로그를 검토할 수 있습니다.

1. 릴리스 파이프라인이 완료되면 [Azure Portal](https://portal.azure.com)이 표시된 웹 브라우저로 전환한 다음 이 랩의 앞부분에서 프로비전한 App Service 웹앱 블레이드로 이동합니다.
1. App Service 웹앱에서 대상 웹앱을 나타내는 **URL** 링크 항목을 클릭합니다.

    >**참고**: 그러면 새 웹 브라우저 탭이 자동으로 열리고 대상 웹 사이트가 표시됩니다.

1. 대상 웹앱에 CI/CD 파이프라인 트리거를 위해 적용한 변경 사항을 포함한 HealthClinic.biz 웹 사이트가 표시되는지 확인합니다.

### <a name="exercise-3-remove-the-azure-lab-resources"></a>연습 3: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### <a name="task-1-remove-the-azure-lab-resources"></a>작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m1501')].name" --output tsv
    ```

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m1501')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## <a name="review"></a>검토

이 랩에서는 Azure DevOps CI/CD 파이프라인을 사용하여 사용자 지정 Docker 이미지를 만든 다음 이를 Azure Container Registry에 푸시한 후 Azure DevOps를 사용하여 Azure App Service에 컨테이너로 배포했습니다.
