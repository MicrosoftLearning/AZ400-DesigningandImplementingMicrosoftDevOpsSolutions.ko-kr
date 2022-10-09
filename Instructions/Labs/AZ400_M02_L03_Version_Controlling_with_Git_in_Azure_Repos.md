---
lab:
  title: '랩 03: Azure Repos에서 Git를 사용하여 버전 제어'
  module: 'Module 02: Development for enterprise DevOps'
---

# <a name="lab-03-version-controlling-with-git-in-azure-repos"></a>랩 03: Azure Repos에서 Git를 사용하여 버전 제어

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

- [Windows용 Git 다운로드 페이지](https://gitforwindows.org/). 이 랩의 필수 구성 요소로 설치됩니다.

- [Visual Studio Code](https://code.visualstudio.com/) 이 랩의 필수 구성 요소로 설치됩니다.

## <a name="lab-overview"></a>랩 개요

Azure DevOps에서는 두 가지 유형의 버전 제어가 지원됩니다. 그 중 하나는 Git를 사용한 제어이고 다른 하나는 TFVC(Team Foundation 버전 제어)를 통한 제어입니다. 이 두 가지 버전 제어 시스템의 기능을 요약하여 설명하자면 다음과 같습니다.

- **TFVC(Team Foundation 버전 제어)** : TFVC는 중앙 집중식 버전 제어 시스템입니다. 일반적으로 팀 멤버는 자신의 고유 개발 컴퓨터에 각 파일 버전 하나만 보유합니다. 기록 데이터는 서버에만 보관됩니다. 분기는 경로에 기반을 두며 서버에서 만들어집니다.

- **Git**: Git는 분산 버전 제어 시스템입니다. 개발자 머신 등의 로컬 환경에 Git 리포지토리를 저장할 수 있습니다. 각 개발자는 자신의 개발 컴퓨터에 소스 리포지토리의 복사본을 가지고 있습니다. 개발자는 자신의 개발 컴퓨터에서 각 변경 내용 집합을 커밋하고 네트워크 연결 없이 기록 및 비교와 같은 버전 제어 작업을 수행할 수 있습니다.

신규 프로젝트의 기본 버전 제어 공급자는 Git입니다. TFVC의 중앙 집중식 버전 제어 기능이 필요한 경우가 아니라면 프로젝트 버전 제어에 Git을 사용해야 합니다.

이 랩에서는 Azure DevOps에서 분기와 리포지토리를 사용하는 방법을 알아봅니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure Repos에서 분기 사용.
- Azure Repos에서 리포지토리 사용.

## <a name="estimated-timing-30-minutes"></a>예상 소요 시간: 30분

## <a name="instructions"></a>Instructions

### <a name="note"></a>참고

**랩 02: Azure Repos에서 Git을 사용하여 버전 제어**를 완료한 경우 **연습 0: 랩 필수 구성 요소 구성** 및 **연습 1: 기존 리포지토리 복제**의 단계를 완료한 것이므로 **연습 2: Azure DevOps에서 분기 관리**부터 바로 진행하면 됩니다.

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿과 Visual Studio Code 구성을 기반으로 하여 미리 구성된 Parts Unlimited 팀 프로젝트를 설정합니다.

#### <a name="task-1-configure-the-team-project"></a>작업 1: 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 **Parts Unlimited** 템플릿을 기반으로 새 프로젝트를 생성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 <https://docs.microsoft.com/en-us/azure/devops/demo-gen> 을 참조하세요.

1. **로그인**을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락**을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **Azure Repos에서 Git를 사용하여 버전 제어**를 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택**을 클릭합니다.
1. 템플릿 목록에서 **PartsUnlimited** 템플릿을 찾은 다음 **템플릿 선택**을 클릭합니다.
1. **새 프로젝트 만들기** 페이지로 돌아와서 **프로젝트 만들기**를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1. **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동**을 클릭합니다.

#### <a name="task-2-install-and-configure-git-and-visual-studio-code"></a>작업 2: Git 및 Visual Studio Code 설치 및 구성

이 작업에서는 Git 및 Visual Studio Code를 설치하고 구성합니다. 이 과정에서 Azure DevOps와 통신하는 데 사용되는 Git 자격 증명을 안전하게 저장하기 위해 Git 자격 증명 도우미를 구성합니다. 이 필수 구성 요소를 이미 구현한 경우에는 다음 작업을 바로 진행해도 됩니다.

1. Git 2.29.2 이상을 아직 설치하지 않았다면 웹 브라우저를 시작하고 [Windows용 Git 다운로드 페이지](https://gitforwindows.org/)로 이동하여 Git를 다운로드한 다음 설치합니다.
1. Visual Studio Code를 아직 설치하지 않았다면 웹 브라우저 창에서 [Visual Studio Code 다운로드 페이지](https://code.visualstudio.com/)로 이동하여 Visual Studio Code를 다운로드한 다음 설치합니다.
1. Visual Studio C# 확장을 아직 설치하지 않았다면 웹 브라우저 창에서 [C# 확장 설치 페이지](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)로 이동하여 확장을 설치합니다.
1. 랩 컴퓨터에서 **Visual Studio Code**를 엽니다.
1. Visual Studio Code 인터페이스의 주 메뉴에서 **터미널 \| 새 터미널**을 선택하여 **터미널** 창을 엽니다.
1. 현재 터미널에서 **PowerShell**을 실행 중인지 확인합니다. 이렇게 하려면 **터미널** 창 오른쪽 위의 드롭다운 목록에 **1: powershell**이 표시되어 있는지 확인합니다.

    > **참고**: 현재 터미널 셸을 **PowerShell**로 변경하려면 **터미널** 창 오른쪽 위의 드롭다운 목록을 클릭하고 **기본 셸 선택**을 클릭합니다. Visual Studio Code 창 위쪽에서 기본 설정 셸로 **Windows PowerShell**을 선택하고 드롭다운 목록 오른쪽의 더하기 기호를 클릭하여 선택한 기본 셸이 표시된 새 터미널을 엽니다.

1. **터미널** 창에서 다음 명령을 실행하여 자격 증명 도우미를 구성합니다.

    ```git
    git config --global credential.helper wincred
    ```

1. **터미널** 창에서 다음 명령을 실행하여 Git 커밋용 사용자 이름과 이메일을 구성합니다. 괄호 안의 자리 표시자는 원하는 사용자 이름과 이메일로 바꾸면 됩니다.

    ```git
    git config --global user.name "<John Doe>"
    git config --global user.email <johndoe@example.com>
    ```

### <a name="exercise-1-clone-an-existing-repository"></a>연습 1: 기존 리포지토리 복제

이 연습에서는 Visual Studio Code를 사용하여 이전 연습에서 프로비전한 Git 리포지토리를 복제합니다.

#### <a name="task-1-clone-an-existing-repository"></a>작업 1: 기존 리포지토리 복제

이 작업에서는 Visual Studio Code를 사용하여 Git 리포지토리를 복제하는 프로세스를 단계별로 진행합니다.

1. 필요한 경우 웹 브라우저를 시작하고 Azure DevOps 조직으로 이동한 다음 이전 연습에서 생성한 **Azure Repos에서 Git를 사용하여 버전 제어** 프로젝트를 엽니다.

    > **참고**: 또는 [https://dev.azure.com/`<your-Azure-DevOps-account-name>` /Version%20Controlling%20with%20Git%20in%20Azure%20Repos](https://dev.azure.com/`<your-Azure-DevOps-account-name>`/Version%20Controlling%20with%20Git%20in%20Azure%20Repos) URL로 이동하여 프로젝트 페이지에 직접 액세스할 수 있습니다. 여기서 `<your-Azure-DevOps-account-name>` 자리 표시자는 계정 이름을 나타냅니다.

1. DevOps 인터페이스의 세로 탐색 창에서 **Repos**를 선택합니다.
1. **PartsUnlimited** 창 오른쪽 위에서 **복제**를 클릭합니다.

    > **참고**: *복제*는 Git 리포지토리의 로컬 복사본을 가져오는 작업입니다. 현재 널리 사용되고 있는 모든 개발 도구에서는 복제가 지원됩니다. 즉, Azure Repos에 연결하여 사용할 최신 원본을 가져올 수 있습니다. **Repos** 허브로 이동합니다.

1. **리포지토리 복제** 창에서 **HTTPS** 명령줄 옵션을 선택하고 리포지토리 복제 URL 옆의 **클립보드에 복사** 단추를 클릭합니다.

    > **참고**: 모든 Git 호환 도구에서 이 URL을 사용하여 코드베이스 복사본을 가져올 수 있습니다.

1. **리포지토리 복제** 창을 닫습니다.
1. 랩 컴퓨터에서 실행 중인 **Visual Studio Code**로 전환합니다.
1. **보기** 메뉴 머리글을 클릭하고 드롭다운 메뉴에서 **명령 팔레트**를 클릭합니다.

    > **참고**: 명령 팔레트에서는 타사 확장으로 구현되는 작업을 비롯하여 다양한 작업에 쉽고 편리하게 액세스할 수 있습니다. **Ctrl+Shift+P** 바로 가기 키를 사용하여 명령 팔레트를 열 수 있습니다.

1. 명령 팔레트 프롬프트에서 **Git: Clone** 명령을 실행합니다.

    > **참고**: 관련 명령을 모두 확인하려는 경우 **Git**부터 입력을 시작하면 됩니다.

1. **리포지토리 URL을 입력하거나 리포지토리 원본 선택** 텍스트 상자에 이 작업 앞부분에서 복사한 리포지토리 복제 URL을 붙여넣고 **Enter** 키를 누릅니다.
1. **폴더 선택** 대화 상자 내에서 C: 드라이브로 이동하여 새 폴더 **Git**를 만들고 선택한 다음 **리포지토리 위치 선택**을 클릭합니다.
1. 메시지가 표시되면 Azure DevOps 계정에 로그인합니다.
1. 복제 프로세스가 완료된 후 메시지가 표시되면 Visual Studio Code에서 **열기**를 클릭하여 복제된 리포지토리를 엽니다.

    > **참고**: 프로젝트 로드 문제와 관련하여 표시될 수 있는 경고는 무시해도 됩니다. 솔루션이 빌드에 적합한 상태가 아닐 수도 있습니다. 하지만 여기서는 Git 사용 방법만 중점적으로 확인할 것이므로 프로젝트는 빌드하지 않아도 됩니다.

### <a name="exercise-2-manage-branches-from-azure-devops"></a>연습 2: Azure DevOps에서 분기 관리

이 연습에서는 Azure DevOps에서 분기를 사용합니다. Visual Studio Code에서 제공되는 기능을 사용할 수 있을 뿐 아니라 Azure DevOps 포털에서 리포지토리 분기를 직접 관리할 수도 있습니다.

#### <a name="task-1-create-a-new-branch"></a>작업 1: 새 분기 만들기

이 작업에서는 Azure DevOps 포털을 사용하여 분기를 만든 다음 Visual Studio Code를 사용하여 해당 분기를 가져옵니다.

1. Azure DevOps 조직 및 이전 연습에서 생성한 **Azure Repos에서 Git를 사용하여 버전 제어** 프로젝트가 표시된 웹 브라우저로 전환합니다.

    > **참고**: 또는 [<https://dev.azure.com/>`<your-Azure-DevOps-account-name>` /Version%20Controlling%20with%20Git%20in%20Azure%20Repos) URL로 이동하여 프로젝트 페이지에 직접 액세스할 수 있습니다. 여기서 `<your-Azure-DevOps-account-name>` 자리 표시자는 계정 이름을 나타냅니다.

1. 웹 브라우저 창에서 프로젝트의 **Repos** 창으로 이동하고 **분기**를 선택합니다.
1. **분기** 창에서 **새 분기**를 클릭합니다.
1. **분기 만들기** 패널의 **이름** 텍스트 상자에 **release**를 입력하고 **기준** 드롭다운 목록에 **master**가 표시되어 있는지 확인합니다. 그런 다음 **연결할 작업 항목** 드롭다운 목록에서 사용 가능한 작업 항목을 하나 이상 선택하고 **만들기**를 클릭합니다.
1. **Visual Studio Code** 창으로 전환합니다.
1. **Ctrl+Shift+P**를 눌러 **명령 팔레트**를 엽니다.
1. **명령 팔레트** 프롬프트에서 **Git: Fetch** 입력을 시작하고 **Git: Fetch**가 표시되면 이를 선택합니다. 이 명령을 실행하면 로컬 스냅숏의 원래 분기가 업데이트됩니다.
1. Visual Studio Code 창 왼쪽 아래에서 **master** 항목을 다시 클릭합니다.
1. 분기 목록에서 **origin/release**를 선택합니다. 그러면 새 로컬 분기 **release**가 작성되어 체크 아웃됩니다.

#### <a name="task-2-delete-and-restore-a-branch"></a>작업 2: 분기 삭제 및 복원

이 작업에서는 Azure DevOps 포털을 사용하여 이전 작업에서 만든 분기를 삭제하고 복원합니다.

1. Azure DevOps 포털의 **분기** 창 **내 항목** 탭이 표시된 웹 브라우저로 전환합니다.
1. **분기** 패널의 **내 항목** 탭에서 **release** 분기 항목 위에 마우스 포인터를 올리면 오른쪽에 줄임표 기호가 표시됩니다.
1. 줄임표를 클릭하고 팝업 메뉴에서 분기 삭제를 선택한 다음 **분기 삭제**를 확인하라는 메시지가 표시되면 **삭제**를 클릭합니다.
1. **분기** 창의 **내 항목** 탭에서 **모두** 탭을 클릭합니다.
1. **분기** 창의 **모두** 탭에 있는 **분기 이름 검색** 텍스트 상자에 **release**를 입력합니다.
1. 새로 삭제한 분기를 나타내는 항목이 포함된 **삭제된 분기** 섹션을 검토합니다.
1. **삭제된 분기** 섹션에서 **release** 분기 항목 위에 마우스 포인터를 올리면 오른쪽에 줄임표 기호가 표시됩니다.
1. 팝업 메뉴에서 줄임표를 클릭하고 **분기 복원**을 선택합니다.

    > **참고**: 삭제한 분기의 정확한 이름을 알고 있다면 이 기능을 사용하여 삭제한 분기를 복원할 수 있습니다.

#### <a name="task-3-lock-and-unlock-a-branch"></a>작업 3: 분기 잠금 및 잠금 해제

이 작업에서는 Azure DevOps 포털을 사용하여 master 분기를 잠그고 잠금을 해제합니다.

중요한 병합과 충돌할 가능성이 있는 신규 변경을 방지하거나 분기를 읽기 전용 상태로 설정하려는 경우에는 잠금을 설정하면 유용합니다. 분기의 변경 내용을 병합하기 전에 검토만 하려는 경우 분기를 잠그는 대신 분기 정책 및 끌어오기 요청을 사용할 수도 있습니다.

분기를 잠그더라도 리포지토리 복제, 분기의 업데이트 내용을 로컬 리포지토리로 가져오기 등의 작업은 계속 수행할 수 있습니다. 분기를 잠그는 경우 분기를 잠근 이유, 그리고 잠금이 해제된 후에 해당 분기를 사용하려면 수행해야 하는 작업을 팀원들에게 알려 주어야 합니다.

1. Azure DevOps 포털의 **분기** 창 **내 항목** 탭이 표시된 웹 브라우저로 전환합니다.
1. 분기 창의 내 항목 탭에서 **master** 분기 항목 위에 마우스 포인터를 올리면 오른쪽에 줄임표 기호가 표시됩니다.**** ****
1. 줄임표를 클릭하고 팝업 메뉴에서 **잠금**을 선택합니다.
1. 분기 창의 내 항목 탭에서 **master** 분기 항목 위에 마우스 포인터를 올리면 오른쪽에 줄임표 기호가 표시됩니다.**** ****
1. 줄임표를 클릭하고 팝업 메뉴에서 **잠금 해제**를 선택합니다.

#### <a name="task-4-tag-a-release"></a>작업 4: 릴리스에 태그 지정

이 작업에서는 Azure DevOps 포털을 사용하여 Azure DevOps Repos에서 릴리스에 태그를 지정합니다.

제품 팀이 사이트의 현재 버전을 v1.1.0-beta로 릴리스하기로 결정했습니다.

1. Azure DevOps 포털의 세로 탐색 창에 있는 **Repos** 섹션에서 **태그**를 선택합니다.
1. **태그** 창에서 **새 태그**를 클릭합니다.
1. **태그 만들기** 패널의 **이름** 텍스트 상자에 **v1.1.0-beta**를 입력하고 **기준** 드롭다운 목록에서는 **master** 항목을 그대로 선택해 둡니다. **설명** 텍스트 상자에는 **베타 릴리스 v1.1.0**을 입력하고 **만들기**를 클릭합니다.

    > **참고**: 프로젝트의 이 릴리스에 태그가 지정되었습니다. 다양한 상황에서 커밋에 태그를 지정할 수 있습니다. Azure DevOps에서는 태그를 유동적으로 편집하고 삭제할 수 있으며 태그 권한도 관리할 수 있습니다.

### <a name="exercise-3-manage-repositories"></a>연습 3: 리포지토리 관리

이 연습에서는 Azure DevOps 포털을 사용하여 Azure DevOps Repos에서 Git 리포지토리를 만들고 삭제합니다.

프로젝트 소스 코드 관리용으로 팀 프로젝트에 Git 리포지토리를 만들 수 있습니다. 각 Git 리포지토리에는 자체 권한 및 분기 세트가 사용되므로 프로젝트 내의 다른 작업과 쉽게 구분할 수 있습니다.

#### <a name="task-1-create-a-new-repo-from-azure-devops"></a>작업 1: Azure DevOps에서 새 리포지토리 만들기

이 작업에서는 Azure DevOps 포털을 사용하여 Azure DevOps Repos에서 Git 리포지토리를 만듭니다.

1. Azure DevOps 포털이 표시된 웹 브라우저의 세로 탐색 창 왼쪽 위에 있는 더하기 기호(프로젝트 이름 바로 오른쪽에 있음)를 클릭한 다음 계단식 메뉴에서 **새 리포지토리**를 클릭합니다.
1. **리포지토리 만들기** 패널의 **리포지토리 유형**에서 기본값인 **Git** 항목을 그대로 두고 **리포지토리 이름** 텍스트 상자에는 **새 리포지토리**를 입력합니다. 다른 설정은 기본값으로 유지하고 **만들기**를 클릭합니다.

    > **참고**: **README.md** 파일을 만들 수 있습니다. 이 파일은 웹 브라우저를 통해 리포지토리 루트로 이동하면 렌더링되는 기본 Markdown 파일입니다. **.gitignore** 파일을 사용하여 리포지토리를 미리 구성할 수도 있습니다. 이 파일은 이름 지정 패턴 및/또는 경로를 기준으로 소스 제어에서 무시할 파일을 지정합니다. 작성 중인 프로젝트 형식을 기준으로 하여 무시할 공통 패턴과 경로가 포함되어 있는 여러 가지 템플릿이 제공됩니다.

    > **참고**: 이제 리포지토리를 사용할 수 있습니다. Visual Studio Code 또는 기타 git 호환 도구로 이 리포지토리를 복제할 수 있습니다.

#### <a name="task-2-delete-and-rename-git-repos"></a>작업 2: Git 리포지토리 삭제 및 이름 바꾸기

이 작업에서는 Azure DevOps 포털을 사용하여 Azure DevOps Repos에서 Git 리포지토리를 삭제합니다.

리포지토리를 삭제하거나 이름을 바꿔야 하는 경우도 있습니다. Azure DevOps를 사용하면 이러한 작업도 손쉽게 수행할 수 있습니다.

1. Azure DevOps 포털이 표시된 웹 브라우저의 세로 탐색 창 아래쪽에서 **프로젝트 설정**을 클릭합니다.
1. **프로젝트 설정** 세로 탐색 창 아래쪽의 **Repos** 섹션으로 스크롤한 다음 **리포지토리**를 클릭합니다.
1. **모든 리포지토리** 패널의 **리포지토리** 탭에서 **새 리포지토리** 분기 항목 위에 마우스 포인터를 올리면 오른쪽에 줄임표 기호가 표시됩니다.
1. 줄임표를 클릭하고 팝업 메뉴에서 **삭제**를 선택한 다음 **New Repo 리포지토리 삭제** 패널에 **New Repo**를 입력하고 **삭제**를 클릭합니다.

## <a name="review"></a>검토

이 랩에서는 Azure DevOps 포털을 사용하여 분기와 리포지토리를 관리했습니다.
