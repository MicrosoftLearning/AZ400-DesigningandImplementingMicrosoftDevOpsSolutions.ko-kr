---
lab:
  title: '랩 09: 릴리스 대시보드 만들기'
  module: 'Module 04: Design and implement a release strategy'
ms.openlocfilehash: cb1bcf7024cd3c3b1b8d4325eeeba94852dc2e77
ms.sourcegitcommit: f72fcf5ee578f465b3495f3cf789b06c530e88a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2022
ms.locfileid: "139262579"
---
# <a name="lab-09-creating-a-release-dashboard"></a>랩 09: 릴리스 대시보드 만들기
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-overview"></a>랩 개요

이 랩에서는 릴리스 대시보드를 만들고 REST API를 사용해 Azure DevOps 릴리스 데이터를 검색하는 과정을 단계별로 진행합니다. 이러한 방식으로 검색한 데이터는 사용자 지정 애플리케이션이나 대시보드에 사용할 수 있습니다.

이 랩에서는 Azure DevOps Starter 리소스를 활용합니다. 이 리소스를 만들면 애플리케이션을 빌드하여 Azure에 배포하는 Azure DevOps 프로젝트가 자동으로 작성됩니다. 

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- 릴리스 대시보드 만들기
- REST API를 사용하여 릴리스 정보 쿼리

## <a name="lab-duration"></a>랩 소요 시간

-   예상 소요 시간: **45분**

## <a name="instructions"></a>Instructions

### <a name="before-you-start"></a>시작하기 전에

#### <a name="sign-in-to-the-lab-virtual-machine"></a>랩 가상 머신에 로그인

다음 자격 증명을 사용하여 Windows 10 컴퓨터에 로그인했는지 확인합니다.
    
-   사용자 이름: **Student**
-   암호: **Pa55w.rd**

#### <a name="review-the-installed-applications"></a>설치된 애플리케이션 검토

Windows 10 데스크톱에서 작업 표시줄을 찾습니다. 작업 표시줄에는 이 랩에서 사용할 애플리케이션에 대한 아이콘이 포함되어 있습니다.
    
-   Microsoft Edge

#### <a name="set-up-an-azure-devops-organization"></a>Azure DevOps 조직 설정

이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

#### <a name="prepare-an-azure-subscription"></a>Azure 구독 준비

-   기존 Azure 구독을 확인하거나 새 구독을 만듭니다.
-   Azure 구독의 Owner 역할이 지정되어 있는 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 나열](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-list-portal)을 참조하세요.

### <a name="exercise-1-create-a-release-dashboard"></a>연습 1: 릴리스 대시보드 만들기

이 연습에서는 Azure DevOps 조직에서 릴리스 대시보드를 만듭니다.

#### <a name="task-1-create-an-azure-devops-starter-resource"></a>작업 1: Azure DevOps Starter 리소스 만들기

이 작업에서는 Azure 구독에서 Azure DevOps Starter 리소스를 만듭니다. 그러면 Azure DevOps 조직에서 해당 프로젝트가 자동으로 작성됩니다.

1.  랩 컴퓨터에서 웹 브라우저를 시작하고, [**Azure Portal**](https://portal.azure.com)로 이동한 다음, 이 랩에서 사용할 Azure 구독에서 Owner 또는 Contributor 역할이 지정된 사용자 계정으로 로그인합니다.
1.  Azure Portal에서 **DevOps Starter** 리소스 유형을 검색하여 선택하고 **DevOps Starter** 블레이드에서 **+ 만들기** 를 클릭합니다. 
1.  **DevOps Starter** 블레이드의 **새 애플리케이션으로 새로 시작** 창에서 **.NET** 타일을 선택하고, 위쪽 **GitHub를 사용하여 DevOps Starter를 설정** 옆에서 설정을 변경하고, **여기** 를 클릭하고, **Azure DevOps**, **완료** 및 **다음: 프레임워크 >** 를 클릭합니다. 
1.  **DevOps Starter** 블레이드의 **애플리케이션 프레임워크 선택** 창에서 **ASP<nolink>.NET Core** 타일을 선택하고, **데이터베이스 추가** 슬라이더를 **켜기** 위치로 이동하고, **다음: 서비스 >** 를 클릭합니다. 
1.  **DevOps Starter** 블레이드의 **애플리케이션을 배포할 Azure 서비스 선택** 창에서 **Windows 웹앱** 타일이 선택되어 있는지 확인하고 **다음: 만들기 >** 를 클릭합니다. 
1.  **DevOps Starter** 블레이드의 **거의 완료** 창에서 다음 설정을 지정합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 프로젝트 이름 | **릴리스 대시보드 만들기** |
    | Azure DevOps 조직 | 이 랩에서 사용할 Azure DevOps 조직의 이름 |
    | Subscription | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 웹앱 이름 | 문자나 숫자로 시작하고 끝나며 문자, 숫자, 하이픈으로 이루어진 전역적으로 고유한 문자열(2~60자) |
    | 위치 | Azure 웹앱과 Azure SQL 데이터베이스를 배포할 Azure 지역의 이름 |

1.  **DevOps Starter** 블레이드의 **거의 완료** 창에서 **추가 설정** 을 클릭합니다.
1.  **추가 설정** 창에서 다음 설정을 지정하고 **확인** 을 클릭합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Resource group | **az400m10l02-rg** |
    | 가격 책정 계층 | **F1 무료** |
    | Application Insights 위치 | Azure 웹앱 위치로 선택한 것과 같은 Azure 지역의 이름 |
    | 서버 이름 | 문자나 숫자로 시작하고 끝나며 문자, 숫자, 하이픈으로 이루어진 전역적으로 고유한 문자열(3~63자) |
    | 사용자 이름 입력 | **dbadmin** |
    | 위치 | Azure 웹앱 위치로 선택한 것과 같은 Azure 지역의 이름 |
    | 데이터베이스 이름 | **az400m10l02-db** |

1.  **DevOps Starter** 블레이드로 돌아와 **거의 완료** 창에서 **완료** 를 클릭한 다음 **검토 + 만들기** 를 클릭합니다.

    > **참고**: 배포가 완료될 때까지 기다리세요. **DevOps Starter** 리소스 프로비전은 2분 정도 걸립니다.

1.  DevOps Starter 리소스가 프로비전되었다는 확인 메시지가 표시되면 **리소스로 이동** 단추를 클릭합니다. 그러면 브라우저가 DevOps Starter 블레이드로 리디렉션됩니다.
1.  DevOps Starter 블레이드에서 CI/CD 파이프라인이 정상적으로 완료될 때까지 해당 진행률을 추적합니다. 

    > **참고**: 해당 Azure 웹앱과 Azure SQL 데이터베이스를 만드는 과정은 5분 정도 걸릴 수 있습니다. 이 프로세스에서는 즉시 배포 가능한 리포지토리와 빌드 및 릴리스 파이프라인이 포함된 Azure DevOps 프로젝트가 자동으로 작성됩니다. Azure 리소스는 자동으로 트리거되는 배포 파이프라인 진행 과정에서 작성됩니다. 

#### <a name="task-2-create-azure-devops-releases"></a>작업 2: Azure DevOps 릴리스 만들기

이 작업에서는 배포가 실패하는 릴리스를 비롯하여 Azure DevOps 릴리스 여러 개를 만듭니다.

1.  Azure Portal이 표시된 웹 브라우저의 DevOps Starter 페이지 도구 모음에서 **프로젝트 홈 페이지** 를 클릭합니다. 그러면 다른 브라우저 탭이 자동으로 열리고 Azure DevOps 포털의 **릴리스 대시보드 만들기** 프로젝트가 표시됩니다. 로그인하라는 메시지가 표시되면 Azure DevOps 조직 자격 증명을 사용하여 인증합니다.

    > **참고**: 먼저 정상적으로 배포되는 새 릴리스를 만듭니다.

1.  Azure DevOps 포털 왼쪽의 세로 메뉴에서 **Repos** 를 클릭하고, 리포지토리의 폴더 목록에서 **Applications\\aspnet-core-dotnet-core\\Pages** 폴더로 이동하고, **Index.chtml** 항목을 클릭합니다. 
1.  **Index.cshtml** 창에서 **편집** 을 클릭하고, 줄 **20** 에서 `<div class="description line-2"> Your ASP.NET Core app is up and running on Azure</div>`를 `<div class="description line-2"> Your ASP.NET Core app v1.1 is up and running on Azure</div>`로 바꾸고, **커밋** 을 클릭하고, **커밋** 창에서 **커밋** 을 다시 클릭합니다. 이렇게 하면 자동으로 빌드 파이프라인이 트리거됩니다. 
1.  Azure DevOps 포털 왼쪽의 세로 탐색 창에 있는 **파이프라인** 을 클릭합니다.
1.  **파이프라인** 창의 **최신** 탭에서 **az400m10l02-CI** 항목을 클릭하고 **az400m10l02-CI** 창의 **실행** 탭에서 최신 실행을 선택합니다. 그런 다음 해당 실행의 **요약** 탭 **작업** 섹션에서 **빌드** 를 클릭하고 작업이 정상적으로 완료될 때까지 모니터링합니다. 
1.  작업이 완료되면 Azure DevOps 포털 왼쪽의 세로 탐색 창에 있는 **파이프라인** 섹션에서 **릴리스** 를 클릭합니다.
1.  **az400m10l02 - CD** 창의 **릴리스** 탭에서 **릴리스-2** 항목을 클릭하고 **릴리스-2** 창의 **파이프라인** 탭에서 **개발** 단계를 클릭합니다. 그런 다음 **개발** 창에서 **로그 보기** 를 클릭하고 배포가 정상적으로 완료될 때까지 진행률을 모니터링합니다. 

    > **참고**: 이제 배포가 실패하는 새 릴리스를 만듭니다. 배포가 실패하는 이유는 기본 제공 어셈블리 테스트에서 새 릴리스 관련 변경 내용을 잘못된 것으로 간주하기 때문입니다.

1.  Azure DevOps 포털 왼쪽의 세로 메뉴에서 **Repos** 를 클릭하고, 리포지토리의 폴더 목록에서 **Applications\\aspnet-core-dotnet-core\\Pages** 폴더로 이동하고, **Index.chtml** 항목을 클릭합니다. 
1.  **Index.cshtml** 창에서 **편집** 을 클릭하고, 줄 **4** 에서 `    ViewData["Title"] = "Home Page - ASP.NET Core";`를 `    ViewData["Title"] = "Home Page v1.2 - ASP.NET Core";`로 바꾸고, **커밋** 을 클릭하고, **커밋** 창에서 **커밋** 을 다시 클릭합니다. 이렇게 하면 자동으로 빌드 파이프라인이 트리거됩니다. 
1.  Azure DevOps 포털 왼쪽의 세로 탐색 창에 있는 **파이프라인** 을 클릭합니다.
1.  **파이프라인** 창의 **최신** 탭에서 **az400m10l02-CI** 항목을 클릭하고 **az400m10l02-CI** 창의 **실행** 탭에서 최신 실행을 선택합니다. 그런 다음 해당 실행의 **요약** 탭 **작업** 섹션에서 **빌드** 를 클릭하고 작업이 정상적으로 완료될 때까지 모니터링합니다. 
1.  작업이 완료되면 Azure DevOps 포털 왼쪽의 세로 탐색 창에 있는 **파이프라인** 섹션에서 **릴리스** 를 클릭합니다.
1.  **az400m10l02 - CD** 창의 **릴리스** 탭에서 **릴리스-2** 항목을 클릭하고 **릴리스-2** 창의 **파이프라인** 탭에서 **개발** 단계를 클릭합니다. 그런 다음 **개발** 창에서 **로그 보기** 를 클릭하고 **테스트 어셈블리** 단계 중에 배포가 실패할 때까지 진행률을 모니터링합니다. 

#### <a name="task-3-create-an-azure-devops-release-dashboard"></a>작업 3: Azure DevOps 릴리스 대시보드 만들기

이 작업에서는 대시보드를 만들어 릴리스 관련 위젯에 추가합니다.

1.  Azure DevOps 포털 왼쪽의 세로 메뉴에 있는 **개요** 를 클릭하고 **개요** 섹션에서 **대시보드** 를 클릭한 후에 **위젯 추가** 를 클릭합니다.
1.  **위젯 추가** 창에서 위젯 목록 아래쪽으로 스크롤한 다음 **배포 상태** 항목을 선택하고 **추가** 를 클릭합니다.
1.  이전 단계에서 설명한 절차에 따라 **릴리스 상태 세부 정보**, **릴리스 상태 개요** 및 **릴리스 파이프라인 개요** 위젯을 추가합니다.
    > **참고**: 마켓플레이스 [팀 프로젝트 상태](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.TeamProjectHealth)에서 **릴리스 상태 세부 정보** 및 **릴리스 상태 개요** 를 설치합니다.
1.  대시보드를 세로로 스크롤하지 않아도 되도록 마우스로 **릴리스 파이프라인 개요** 를 **배포 상태** 위젯 오른쪽으로 끌어서 놓고 **편집 완료** 를 클릭합니다.
1.  대시보드 창으로 돌아와 **배포 상태** 위젯을 나타내는 사각형에서 **위젯 구성** 을 클릭합니다. 
1.  **구성** 창에서 다음 설정을 지정합니다. 다른 설정은 모두 기본값으로 유지하고 **저장** 을 클릭합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 빌드 파이프라인 | **az400m10l02 - CI** |
    | 연결된 릴리스 파이프라인 | **az400m10l02 - CD; az400m10l02 - CD\dev** |

1.  대시보드 창으로 돌아와 **릴리스 상태 개요** 위젯을 나타내는 사각형 오른쪽 위에 마우스를 놓으면 **기타 작업** 메뉴를 나타내는 줄임표 기호가 표시됩니다. 이 기호를 클릭한 다음 드롭다운 메뉴에서 **구성** 을 클릭합니다.  
1.  **구성** 창에서 다음 설정을 지정합니다. 다른 설정은 모두 기본값으로 유지하고 **저장** 을 클릭합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 릴리스 정의 선택 | **az400m10l02 - CD** |

1.  대시보드 창으로 돌아와 **릴리스 상태 세부 정보** 위젯을 나타내는 사각형 오른쪽 위에 마우스를 놓으면 **기타 작업** 메뉴를 나타내는 줄임표 기호가 표시됩니다. 이 기호를 클릭한 다음 드롭다운 메뉴에서 **구성** 을 클릭합니다.  
1.  **구성** 창에서 다음 설정을 지정합니다. 다른 설정은 모두 기본값으로 유지하고 **저장** 을 클릭합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 정의 | **az400m10l02 - CD** |

1.  대시보드 창으로 돌아와 **릴리스 파이프라인 개요** 위젯을 나타내는 사각형 오른쪽 위에 마우스를 놓으면 **기타 작업** 메뉴를 나타내는 줄임표 기호가 표시됩니다. 이 기호를 클릭한 다음 드롭다운 메뉴에서 **구성** 을 클릭합니다.  
1.  **구성** 창에서 다음 설정을 지정합니다. 다른 설정은 모두 기본값으로 유지하고 **저장** 을 클릭합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 릴리스 파이프라인 | **az400m10l02 - CD** |

1.  대시보드 창으로 돌아온 다음 **새로 고침** 을 클릭하여 위젯에 표시되는 콘텐츠를 업데이트합니다.

    > **참고**: 위젯의 링크를 클릭하면 Azure DevOps 포털의 해당 창으로 바로 이동할 수 있습니다.

### <a name="exercise-2-query-release-information-via-rest-api"></a>연습 2: REST API를 통해 릴리스 정보 쿼리

이 연습에서는 Postman을 사용하여 REST API를 통해 릴리스 정보를 쿼리합니다.

#### <a name="task-1-generate-an-azure-devops-personal-access-token"></a>작업 1: Azure DevOps 개인용 액세스 토큰 생성

이 작업에서는 Azure DevOps 개인용 액세스 토큰을 생성합니다. 이 연습의 다음 작업에서 설치할 Postman 앱에서 인증을 할 때 이 토큰을 사용합니다.

1.  랩 컴퓨터의 Azure DevOps 포털이 표시된 웹 브라우저 창에서 Azure DevOps 페이지 오른쪽 위의 **사용자 설정** 아이콘을 클릭합니다. 그런 다음 드롭다운 메뉴에서 **개인용 액세스 토큰** 을 클릭하고 **개인용 액세스 토큰** 창에서 **+ 새 토큰** 을 클릭합니다.
1.  **새 개인용 액세스 토큰 만들기** 창에서 **모든 범위 표시** 링크를 클릭하고 다음 설정을 지정한 후에 **만들기** 를 클릭합니다. 다른 값은 모두 기본값으로 유지합니다.

    | 설정 | 값 |
    | --- | --- |
    | Name | **릴리스 대시보드 만들기 랩** |
    | Scope | **릴리스** |
    | 사용 권한 | **읽기** |
    | Scope | **빌드** |
    | 사용 권한 | **읽기** |

1.  **성공** 창에서 개인용 액세스 토큰의 값을 클립보드에 붙여넣습니다.

    > **참고**: 토큰의 값을 적어 두세요. 이 창을 닫으면 토큰을 검색할 수 없습니다. 

1.  **성공** 창에서 **닫기** 를 클릭합니다.

#### <a name="task-2-query-release-information-via-rest-api-by-using-postman"></a>작업 2: Postman을 사용하여 REST API를 통해 릴리스 정보 쿼리

이 작업에서는 Postman을 사용하여 REST API를 통해 릴리스 정보를 쿼리합니다.

1.  랩 컴퓨터에서 웹 브라우저를 시작하고 [Postman 다운로드 페이지](https://www.postman.com/downloads/)로 이동하여 **Download the App** 단추를 클릭합니다. 그런 다음 드롭다운 메뉴에서 **Windows 64-bit** 를 클릭하고 다운로드한 파일을 클릭한 후에 설치를 실행합니다. 설치가 완료되면 Postman 데스크톱 앱이 자동으로 시작됩니다. 
1.  **Create Postman Account** 창에 이메일 주소, 사용자 이름 및 암호를 입력하고 **Create free account** 를 클릭합니다. 

    > **참고**: 계정 프로비전 프로세스를 완료하려면 Postman 계정을 활성화하라는 Postman의 이메일이 수신됩니다.

1.  로그인한 후 Postman 데스크톱 앱 창의 왼쪽 위에서 **+ New** 를 클릭하고 **Create New** 창에서 **Request** 를 클릭합니다. 그런 다음 **SAVE REQUEST** 창의 **Request name** 텍스트 상자에 **Get-Releases** 를 입력하고 **+ Create Collection** 을 클릭합니다. 그리고 나서 **Name your collection** 텍스트 상자에 **Azure DevOps az400m10l02 queries** 를 입력하고 오른쪽의 확인 표시를 클릭한 후에 **Save to Azure DevOps az400m10l02 queries** 단추를 클릭합니다.
1.  다른 웹 브라우저 창을 열고 [**릴리스 - 목록** Microsoft Docs 페이지](https://docs.microsoft.com/en-us/rest/api/azure/devops/release/releases/list?view=azure-devops-rest-6.0)로 이동하여 해당 내용을 검토합니다. 
1.  Postman 데스크톱 앱으로 다시 전환하여 앱 창 오른쪽 위 섹션의 Launchpad 창에서 **Authorization** 탭 머리글을 클릭하고 **TYPE** 드롭다운 목록에서 **Basic Auth** 항목을 선택합니다. 그런 다음 **Password** 텍스트 상자에 이전 작업에서 복사한 토큰의 값을 붙여넣습니다. **Username** 텍스트 상자의 값은 설정하지 마세요.
1.  앱 창 오른쪽 위 섹션의 실행 패드 창에서 **GET** 이 드롭다운 창에 표시되는지 확인하고, **요청 URL 입력** 텍스트 상자에 다음을 입력하고, **보내기** 를 클릭하여 모든 릴리스의 목록을 표시합니다. 여기서 `<organization_name>`은 Azure DevOps 조직의 이름으로 바꿉니다.

    ```url
    https://vsrm.dev.azure.com/<organization_name>/Creating%20a%20Release%20Dashboard/_apis/release/releases?api-version=6.0
    ```

1.  앱 창 오른쪽 아래 섹션의 **Body** 탭에 나열된 출력을 검토하여 이 랩의 이전 연습에서 만든 릴리스 목록이 포함되어 있는지 확인합니다.
1.  Microsoft Docs의 내용이 표시된 웹 브라우저 창으로 전환하여 [**배포 - 목록** Microsoft Docs 페이지](https://docs.microsoft.com/en-us/rest/api/azure/devops/release/deployments/list?view=azure-devops-rest-6.0)로 이동한 후에 해당 내용을 검토합니다. 
1.  앱 창 오른쪽 위 섹션의 실행 패드 창에서 **GET** 이 드롭다운 창에 표시되는지 확인하고, **요청 URL 입력** 텍스트 상자에 다음을 입력하고, **보내기** 를 클릭하여 모든 배포의 목록을 표시합니다. 여기서 `<organization_name>`은 Azure DevOps 조직의 이름으로 바꿉니다.

    ```url
    https://vsrm.dev.azure.com/<organization_name>/Creating%20a%20Release%20Dashboard/_apis/release/deployments?api-version=6.0
    ```

1.  앱 창 오른쪽 아래 섹션의 **Body** 탭에 나열된 출력을 검토하여 이 랩의 이전 연습에서 시작한 배포 목록이 포함되어 있는지 확인합니다.
1.  앱 창 오른쪽 위 섹션의 실행 패드 창에서 **GET** 이 드롭다운 창에 표시되는지 확인하고, **요청 URL 입력** 텍스트 상자에 다음을 입력하고, **보내기** 를 클릭하여 모든 배포의 목록을 표시합니다. 여기서 `<organization_name>`은 Azure DevOps 조직의 이름으로 바꿉니다.

    ```url
    https://vsrm.dev.azure.com/<organization_name>/Creating%20a%20Release%20Dashboard/_apis/release/deployments?DeploymentStatus=failed&api-version=6.0
    ```

1.  앱 창 오른쪽 아래 섹션의 **Body** 탭에 나열된 출력을 검토하여 이 랩의 이전 연습에서 시작했으나 실패한 배포만 포함되어 있는지 확인합니다.

### <a name="exercise-3-remove-the-azure-lab-resources"></a>연습 3: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다. 

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### <a name="task-1-remove-the-azure-lab-resources"></a>작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다. 

1.  Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1.  다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m10l02-rg')].name" --output tsv
    ```

1.  다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

    ```sh
    az group list --query "[?starts_with(name,'az400m10l02-rg')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

    >**참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

#### <a name="review"></a>검토

이 랩에서는 릴리스 대시보드를 작성 및 구성하는 방법과 REST API를 사용하여 Azure DevOps 릴리스 데이터를 검색하는 방법을 배웠습니다.
