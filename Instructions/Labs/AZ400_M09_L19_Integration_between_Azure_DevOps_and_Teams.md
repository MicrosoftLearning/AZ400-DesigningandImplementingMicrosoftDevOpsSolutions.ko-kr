---
lab:
  title: '랩 19: Azure DevOps와 Teams 통합'
  module: 'Module 09: Implement continuous feedback'
ms.openlocfilehash: 8b897b8b6bdf577fc2b37c2921af8f7f0ebb248a
ms.sourcegitcommit: d78aebd7b14277a53f152e26cea68a30b0e90d73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2022
ms.locfileid: "146276139"
---
# <a name="lab-19-integration-between-azure-devops-and-teams"></a>랩 19: Azure DevOps와 Teams 통합

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩에는 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- Microsoft Teams. 이 랩의 필수 구성 요소로 설치됩니다.

- Microsoft 365 구독을 설정합니다. [Microsoft Teams 등록 페이지](https://teams.microsoft.com/start)에서 무료 평가판 구독을 만듭니다.

> **참고**: Microsoft 365 구독과 Azure DevOps 조직은 같은 Azure Active Directory(Azure AD) 테넌트를 공유해야 합니다.

## <a name="lab-overview"></a>랩 개요

**[Microsoft Teams](https://teams.microsoft.com/start)** 는 Microsoft 365의 팀워크를 위한 허브입니다. Microsoft Teams에서는 팀의 모든 채팅, 모임, 파일, 앱을 한 곳에서 관리하고 사용할 수 있습니다. 또한 Microsoft 365 및 Azure DevOps 전반에서 다양한 팀, 대화, 콘텐츠 및 도구를 활용할 수 있는 허브를 소프트웨어 개발 팀에 제공합니다.

이 랩에서는 Azure DevOps와 Microsoft Teams 간의 통합 시나리오를 구현합니다.

> **참고**: **Azure DevOps Services** 와 Microsoft Teams를 통합하면 개발 주기 전반에서 활용할 수 있는 포괄적인 채팅 및 공동 작업 환경이 제공됩니다. 그러므로 개발 팀은 작업 항목, 끌어오기 요청, 코드 커밋, 빌드 및 릴리스 이벤트 등과 관련한 알림과 경고를 통해 Azure DevOps 팀 프로젝트에서 진행되는 중요한 활동 관련 정보를 지속적으로 파악할 수 있습니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Microsoft Teams와 Azure DevOps 통합
- Teams의 대시보드와 Azure DevOps Kanban 보드 통합
- Azure Pipelines와 Microsoft Teams 통합
- Microsoft Teams에서 Azure Pipelines 앱 설치
- Azure Pipelines 알림 구독

## <a name="estimated-timing-60-minutes"></a>예상 소요 시간: 60분

## <a name="instructions"></a>Instructions

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿과 Microsoft Teams에서 만든 팀을 기반으로 하여 미리 구성된 **Tailwind Traders** 팀 프로젝트를 설정합니다.

#### <a name="task-1-configure-the-team-project"></a>작업 1: 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 **Tailwind Traders** 템플릿을 기반으로 새 프로젝트를 생성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 <https://docs.microsoft.com/en-us/azure/devops/demo-gen> 을 참조하세요.

1. **로그인** 을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락** 을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지에서 **템플릿 선택** 을 클릭합니다.
1. 템플릿 목록의 도구 모음에서 **일반** 을 클릭하고, **Tailwind Traders** 를 선택하고, **템플릿 선택** 을 클릭합니다.
1. **새 프로젝트 만들기** 페이지로 돌아가 **새 프로젝트 이름** 텍스트 상자에 **Tailwind Traders** 를 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택합니다.
1. **새 프로젝트 만들기** 페이지에서 누락된 확장을 설치하라는 메시지가 표시되면 **ARM 출력** 아래의 확인란을 선택하고 **프로젝트 만들기** 를 클릭합니다(GitHub 포크 무시).

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1. **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동** 을 클릭합니다.

#### <a name="task-2-create-a-team-in-microsoft-teams"></a>작업 2: Microsoft Teams에서 팀 만들기

이 작업에서는 Microsoft Teams에서 팀을 만듭니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Microsoft Teams 다운로드 페이지](https://www.microsoft.com/en-us/microsoft-365/microsoft-teams/download-app)로 이동하여 Microsoft Teams 다운로드한 다음 기본 설정으로 설치합니다.
1. 랩 컴퓨터에서 데스크톱 앱을 사용해 **Microsoft Teams** 를 시작합니다.

    > **참고**: 웹 브라우저를 통해 [Microsoft Teams 시작 페이지](https://teams.microsoft.com/dl/launcher/launcher.html?url=/_%23/l/home/0/0&type=home)로 이동할 수도 있습니다.

1. 로그인하라는 메시지가 표시되면 Azure DevOps 조직 액세스 권한이 있으며 Microsoft 365 구독에 포함된 사용자 계정으로 로그인합니다.
1. Microsoft Teams 페이지 왼쪽의 도구 모음에서 **팀** 을 클릭하고 팀 목록 아래쪽에서 **팀 가입 또는 만들기** 를 클릭합니다.

    >**참고**: 팀은 공통의 목표를 달성하기 위해 함께 작업을 하는 사용자 집합입니다.

1. **팀 가입 또는 만들기** 창에서 **팀 만들기** 를 클릭합니다.
1. **팀 만들기** 패널에서 처음부터를 클릭하고 **팀 종류** 패널에서 **프라이빗** 을 클릭합니다.
1. **비공개 팀에 관한 일부 간단한 세부 정보** 패널에서 **팀 이름 지정** 을 **Tailwind Traders** 로 바꾸고 **만들기** 를 클릭합니다.
1. **Tailwind Traders에 구성원 추가** 패널에서 **건너뛰기** 를 클릭합니다.

### <a name="exercise-1-integrate-azure-boards-with-microsoft-teams"></a>연습 1: Azure Boards와 Microsoft Teams 통합

이 연습에서는 Azure Boards와 Microsoft Teams 간의 통합을 구현합니다.

#### <a name="task-1-install-and-configure-azure-boards-app-in-microsoft-teams"></a>작업 1: Microsoft Teams에서 Azure Boards 앱 설치 및 구성

이 작업에서는 Microsoft Teams에서 새로 만든 팀에서 Azure Boards 앱을 설치하고 구성합니다.

1. Microsoft Teams 창 왼쪽 아래에서 **앱** 아이콘을 클릭합니다. 그러면 **앱** 창이 열립니다.
1. **앱** 창의 **모든 앱 검색** 텍스트 상자에 **Azure Boards** 를 입력하고 앱 목록에서 **Azure Boards** 를 클릭합니다.
**열기** 단추의 바로 오른쪽에 있는 아래쪽 화살표를 클릭하고 드롭다운에서 **팀에 추가** 항목을 선택합니다.
1. **팀에 대한 Azure Boards 설정** 패널의 **검색** 텍스트 상자에 **Tailwind Traders** 를 입력합니다. 검색 결과에서 **Tailwind Traders > 일반** 항목을 선택하고 **봇 설정** 을 클릭합니다.

1. **Tailwind Traders** 팀의 **일반** 채널에 있는 게시물 목록에서 제목이 **Azure Boards** 인 게시물을 선택하고 **Enter** 키를 눌러 다음 과 같이 봇이 게시한 추가 메시지를 검토합니다.

    ```
    Here are some of the things you can do:
    link [project url] - Link to a project to create work items and receive notifications
    subscriptions - Add or remove subscriptions for this channel
    addAreapath [area path] - Add an area path from your project to this channel
    signin - Sign in to your Azure Boards account
    signout - Sign out from your Azure Boards account
    unlink - Unlink a project from this channel
    feedback - Report a problem or suggest a feature
    To know more see documentation.
    ```

1. **새 대화** 를 열고 **@** 을 입력합니다. **Azure** 를 입력하면 결과가 표시됩니다. **Azure Boards** 를 선택한 다음, 명령 패널에서 **signin** 을 선택합니다. 단계에 따라 Azure DevOps 조직에 액세스할 수 있는지 확인합니다.
1. Azure DevOps **Tailwind Traders** 프로젝트의 URL을 복사합니다. 예제: <https://dev.azure.com/myorg/myproject>. Teams 채널에서 **새 대화** 를 열고 **@** 을 입력합니다. **Azure** 를 입력하면 결과가 표시됩니다. **Azure Boards** 를 선택한 다음, 명령 패널에서 **link** 를 선택합니다. 이때 복사한 프로젝트 URL을 붙여넣습니다. 게시물은 ´**Azure Boards** link <https://dev.azure.com/myorg/myproject> ´와 같이 표시됩니다. **Enter** 키를 누릅니다.

    받은 메시지를 검토합니다.

    ```
    NAME has linked this channel to project  Tailwind Traders. Create work items using @Azure         Boards create.
    To monitor work items, please add subscription
    Add subscription
    ```

1. **구독 추가** 를 클릭합니다. 드롭다운의 이벤트 목록에서 **작업 항목 만들어짐** 을 선택하고 **다음** 을 클릭합니다. 기본값을 유지하고 **제출** 을 클릭합니다. **확인** 을 클릭하고 닫습니다. 새로 추가된 구독에 관한 세부 정보가 포함된 메시지가 채널에 게시됩니다.
1. Azure DevOps 포털에서 **Tailwind Traders** 프로젝트를 표시하는 웹 브라우저로 전환하고 **Boards > 작업 항목** 을 클릭합니다. **새 작업 항목** 을 클릭하고 드롭다운에서 **사용자 스토리** 를 선택합니다. 사용자 스토리 제목을 **Teams를 사용하여 Azure Boards 통합 테스트** 같이 입력하고 **저장** 합니다. 최근에 설정한 Teams 채널은 생성된 사용자 스토리에 관한 정보가 포함된 알림/카드를 게시합니다.

#### <a name="task-2-add-azure-boards-kanban-boards-to-microsoft-teams"></a>작업 2: Microsoft Teams에 Azure Boards Kanban 보드 추가

이 작업에서는 Microsoft Teams의 탭에 Azure Boards Kanban 보드를 추가합니다.

> **참고**: Kanban 보드는 백로그를 대화형 간판으로 변환하여 작업 흐름을 시각적으로 제공합니다. 아이디어 구상에서 제품 완성까지의 작업을 진행하는 과정에서 보드의 항목을 업데이트합니다. 각 열은 작업 단계를 나타내며 각 카드는 해당 작업 단계의 사용자 스토리(파란색 카드) 또는 버그(빨간색 카드)를 나타냅니다. 탭을 사용하면 Teams Kanban 보드 또는 즐겨 사용하는 대시보드를 Microsoft Teams에 바로 추가할 수 있습니다. 팀 구성원은 **탭** 을 통해 사용자의 개인 앱 공간이나 채널 내의 전용 캔버스에서 서비스에 액세스할 수 있습니다. 기존 웹앱을 활용하여 Teams 내에서 유용한 탭 환경을 만들 수 있습니다.

1. 랩 컴퓨터에서 Azure DevOps 포털의 **Tailwind Traders** 프로젝트가 표시된 웹 브라우저로 전환하여 Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에 있는 **Boards** 를 클릭하고 **보드** 섹션에서 **Boards** 를 클릭합니다.
1. **Boards** 창의 **내 팀 보드(1)** 섹션에서 **Tailwind Traders 팀 보드** 항목을 클릭합니다.
1. **Tailwind Traders 팀** 창을 표시한 상태로 웹 브라우저 창에서 이 보드의 URL을 클립보드에 복사합니다.
1. Microsoft Teams 창으로 전환하여 새로 만든 팀인 **Tailwind Traders** 의 **일반** 채널이 선택되어 있는지 확인하고 **일반** 창 위쪽 섹션에서 **게시물, 파일 및 Wiki 탭** 옆에 있는 더하기 기호를 클릭합니다. 그러면 **탭 추가** 패널이 표시됩니다.
1. **탭 추가** 패널에서 **웹 사이트** 를 클릭하고 웹 사이트 패널에서 **탭 이름** 은 **Tailwind Traders 팀 보드** 로, **URL** 은 방금 클립보드에 복사한 URL로 설정한 후에 **저장** 을 클릭합니다.
1. Microsoft Teams 창에서 **Tailwind Traders** 팀의 **일반** 채널을 선택한 상태로 위쪽 메뉴의 탭 목록에서 새로 추가한 **Tailwind Traders 팀 보드** 탭을 클릭하고 Azure DevOps 포털에서 사용 가능한 **Tailwind Traders 팀** 보드와 일치하는 콘텐츠가 이 탭에 포함되어 있는지 확인합니다(로그인해야 할 수 있음).

> **참고**: 일일 스탠드업 회의에서 모든 작업을 모니터링할 수 있으며 해당하는 작업 항목 상태가 변경될 때마다 업데이트는 실시간으로 반영됩니다. Microsoft Teams에서 Kanban 보드를 수정할 수도 있습니다.

### <a name="exercise-2-integrate-azure-pipelines-with-microsoft-teams"></a>연습 2: Azure Pipelines와 Microsoft Teams 통합

이 연습에서는 Azure Pipelines와 Microsoft Teams 간의 통합을 구현합니다.

#### <a name="task-1-install-and-configure-azure-pipelines-app-in-microsoft-teams"></a>작업 1: Microsoft Teams에서 Azure Pipelines 앱 설치 및 구성

이 작업에서는 Microsoft Teams에서 지정한 팀에서 Azure Pipelines 앱을 설치하고 구성합니다.

> **참고**: Microsoft Teams에서 Azure Pipelines 앱을 설치하면 파이프라인의 이벤트를 모니터링할 수 있습니다. 릴리스, 보류 중인 승인, 완료된 빌드와 같은 이벤트용으로 구독을 설정하여 관리할 수 있습니다. 그러면 Teams 채널에 알림이 바로 게시됩니다. Teams 채널 내에서 릴리스를 승인할 수도 있습니다.

1. Microsoft Teams 창으로 이동하고 왼쪽 아래에서 **앱** 아이콘을 클릭합니다. 그러면 **앱** 창이 열립니다.
1. **앱** 창의 **모든 앱 검색** 텍스트 상자에 **Azure Pipelines** 를 입력하고 앱 목록에서 **Azure Pipelines** 를 클릭합니다.
1. **Azure Pipelines** 패널에서 **열기** 단추 바로 오른쪽의 아래쪽 화살표를 클릭하고 드롭다운 목록에서 **팀에 추가** 항목을 선택합니다.
1. **팀에 대한 Azure Pipelines 설정** 패널의 **검색** 텍스트 상자에 **Tailwind Traders** 를 입력하고, 결과 목록에서 **Tailwind Traders > 일반** 항목을 선택한 다음, **봇 설정** 을 클릭합니다. **Tailwind Traders** 팀의 **일반** 채널에 있는 게시물 보기로 자동으로 리디렉션됩니다.
1. **Tailwind Traders** 팀의 **일반** 채널에 있는 게시물 목록에서 **새 대화** 를 열고 **@** 을 입력합니다. **Azure** 를 입력하면 결과가 표시됩니다. **Azure Pipelines** 를 선택하고 **Enter** 키를 눌러 다음과 같이 봇을 통해 게시된 추가 메시지를 검토합니다.

    ```
    Here are some of the things you can do:
    subscribe [pipeline url/ project url] - Subscribe to a pipeline or all pipelines in a project to receive notifications
    subscriptions - Add or remove subscriptions for this channel
    feedback - Report a problem or suggest a feature
    signin - Sign in to your Azure Pipelines account
    signout - Sign out from your Azure Pipelines account
    To know more see documentation.
    ```

#### <a name="task-2-subscribe-to-the-azure-pipeline-notifications-in-microsoft-teams"></a>작업 2: Microsoft Teams에서 Azure Pipeline 알림 구독

이 작업에서는 Microsoft Teams에서 Azure Pipeline 알림을 구독합니다.

> **참고**: `@Azure Pipelines` 핸들을 사용하여 앱과 상호 작용을 시작할 수 있습니다.

1. **게시물** 탭이 선택된 상태에서 **Tailwind Traders** 팀의 **일반** 채널에서 **@** 을 입력합니다. **Azure** 를 입력하면 결과가 표시됩니다. **Azure Pipelines** 를 선택한 다음, 명령 패널에서 **signin** 을 선택하고, **Enter** 키를 누릅니다.
1. **Azure Pipelines 로그인** 창에서 **로그인** 을 클릭합니다.
1. **서비스 후크(읽기 및 쓰기)** , **빌드(빌드 및 실행)** , **릴리스(읽기, 쓰기, 실행 및 관리)** , **프로젝트 및 팀(읽기)** , **ID 선택(읽기)** 및 **Teams 통합** 권한을 부여하라는 메시지가 표시되면 **수락** 을 클릭하고 **닫기** 를 클릭합니다.

    >**참고**: 이제 `@azure pipelines subscribe [pipeline url]` 명령을 실행하여 Azure Pipelines를 구독할 수 있습니다.

1. Azure DevOps 포털의 **Tailwind Traders** 프로젝트가 표시된 웹 브라우저로 전환하고, Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에 있는 **Pipelines** 를 클릭하고, **파이프라인** 창에서 **Website-CI** 항목을 클릭하고, **Website-CI** 창에 있는 동안 웹 브라우저 창에서 해당 URL을 클립보드에 복사합니다.

    >**참고**: URL은 `https://dev.azure.com/<organization_name>/Tailwind%20Traders/_build?definitionId=<number>` 형식입니다. 여기서 `<organization_name>`은 DevOps 조직의 이름을 나타내는 자리 표시자입니다.

    >**참고**: URL에 *definitionId* 또는 *buildId/releaseId* 가 있는 파이프라인 내의 어떤 페이지 URL이나 파이프라인 URL로 사용할 수 있습니다.

1. Teams로 돌아가서 **게시물** 탭이 선택된 상태에서 **Tailwind Traders** 팀의 **일반** 채널에서 **@** 을 입력합니다. **Azure** 를 입력하면 결과가 표시됩니다. **Azure Pipelines** 를 선택한 다음, 명령 패널에서 **subscribe** 를 선택합니다. 이때 복사한 파이프라인 URL을 붙여넣습니다. 게시물은 `@Azure Pipelines subscribe https://dev.azure.com/<organization_name>/Tailwind%20Traders/_build?definitionId=<number>` 같이 표시됩니다(`<organization_name>` 자리 표시자를 DevOps 조직 이름으로 바꿔야 함). **Enter** 키를 누릅니다.
1. 구독이 정상적으로 생성되었다는 확인 메시지가 표시될 때까지 기다립니다.

    >**참고**: 빌드 파이프라인의 경우 채널이 **실행 스테이지 단계가 변경됨** 및 **실행 스테이지 승인 대기 중** 알림을 구독하게 됩니다.

1. Azure DevOps 포털의 **Tailwind Traders** 프로젝트가 표시된 웹 브라우저로 전환하고, Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에서 **파이프라인** 을 클릭하고, **파이프라인** 섹션에서 **릴리스** 를 클릭하고, 릴리스 목록에서 *Website-CD* 항목을 클릭한 다음, **Website-CD** 항목을 선택한 상태로 웹 브라우저 창에서 해당 URL을 클립보드에 복사합니다.

    >**참고**: URL은 `https://dev.azure.com/<organization_name>/Tailwind%20Traders/_release?_a=releases&view=mine&definitionId=2` 형식입니다. 여기서 `<organization_name>`은 DevOps 조직의 이름을 나타내는 자리 표시자입니다.

1. **게시물** 탭이 선택된 상태에서 **Tailwind Traders** 팀의 **일반** 채널에서 **@** 을 입력합니다. **Azure** 를 입력하면 결과가 표시됩니다. **Azure Pipelines** 를 선택한 다음, 명령 패널에서 **subscribe** 를 선택합니다. 이때 복사한 릴리스 URL을 붙여넣습니다. 게시물은 릴리스 파이프라인을 구독하기 위해 `@Azure Pipelines subscribe https://dev.azure.com/<organization_name>/Tailwind%20Traders/_release?_a=releases&view=mine&definitionId=2` 같이 표시됩니다(`<organization_name>` 자리 표시자를 DevOps 조직 이름으로 바꿔야 함). **Enter** 키를 누릅니다.

    >**참고**: 릴리스 파이프라인의 경우 채널이 **배포 시작됨**, **배포 완료됨** 및 **배포 승인 보류 중 알림** 을 구독하게 됩니다.

#### <a name="task-3-using-filters-to-customize-subscriptions-to-azure-pipelines-in-microsoft-teams"></a>작업 3: 필터를 사용하여 Microsoft Teams에서 Azure Pipelines 구독 사용자 지정

이 작업에서는 Microsoft Teams에서 Azure Pipelines 구독을 사용자 지정합니다.

>**참고**: 사용자가 파이프라인을 구독하면 필터가 적용되지 않고 구독 몇 개가 기본적으로 작성됩니다. 사용자는 이러한 구독을 사용자 지정해야 하는 경우가 많습니다. 빌드가 실패하거나 배포가 프로덕션 환경으로 푸시될 때만 알림을 받으려는 경우를 예로 들 수 있습니다. Azure Pipelines 앱에서는 필터가 지원되므로 채널에 표시되는 알림을 사용자 지정할 수 있습니다.

>**참고**: `@Azure Pipelines subscriptions` 명령을 사용하여 구독을 나열하고 관리할 수 있습니다. 이 명령은 채널의 현재 구독을 모두 나열합니다.

1. **게시물** 탭이 선택된 상태에서 **Tailwind Traders** 팀의 **일반** 채널에서 **@** 을 입력합니다. **Azure** 를 입력하면 결과가 표시됩니다. **Azure Pipelines**, **구독** 을 차례로 선택하고 **Enter** 키를 누릅니다. **Azure Pipelines** 봇의 회신에서 **구독 추가** 를 선택합니다.
1. **Azure Pipelines** **구독 추가** 패널의 **이벤트 선택** 드롭다운 목록에서 **빌드 완료됨** 이 선택되어 있는지 확인하고 **다음** 을 클릭합니다.
1. **Azure Pipelines** **구독 추가** 패널의 **파이프라인 선택** 드롭다운 목록에서 **Website-CI** 가 선택되어 있는지 확인하고 **다음** 을 클릭합니다.
1. **Azure Pipelines** **구독 추가** 패널의 **빌드 상태** 드롭다운 목록에서 **[모두]** 가 선택되어 있는지 확인하고 **제출** 을 클릭합니다.
1. **Azure Pipelines** **구독 추가** 패널에서 **확인** 을 클릭하여 확인 메시지를 승인합니다.
1. **Azure Pipelines** **구독 보기** 패널에서 구독 목록을 검토하고 패널을 닫습니다.
1. Azure DevOps 포털의 **Tailwind Traders** 프로젝트가 표시된 웹 브라우저로 전환하고, Azure DevOps 포털 맨 왼쪽의 세로 메뉴 모음에 있는 **파이프라인** 을 클릭하고, 파이프라인 창에서 **Website-CI** 항목을 클릭하고, Website-CI 창에 있는 동안 **파이프라인 실행 > 실행** 을 클릭합니다.
1. Teams 채널은 파이프라인 실행 실패에 관한 알림을 예상 동작으로 게시합니다(파이프라인에 설정이 없음).

## <a name="review"></a>검토

이 랩에서는 Azure DevOps Services와 Microsoft Teams 간의 통합 시나리오를 구현했습니다.
