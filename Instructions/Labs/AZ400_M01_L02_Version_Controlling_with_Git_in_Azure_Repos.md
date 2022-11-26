---
lab:
  title: '랩 02: Azure Repos에서 Git를 사용하여 버전 제어'
  module: 'Module 01: Get started on a DevOps transformation journey'
---

# <a name="lab-02-version-controlling-with-git-in-azure-repos"></a>랩 02: Azure Repos에서 Git를 사용하여 버전 제어

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

신규 프로젝트의 기본 버전 제어 공급자는 Git입니다. TFVC의 중앙 집중식 버전 제어 기능이 필요한 경우가 아니면 프로젝트 버전 제어에 Git을 사용해야 합니다.

이 랩에서는 Azure DevOps의 중앙 집중식 Git 리포지토리와 쉽게 동기화할 수 있는 로컬 Git 리포지토리를 설정하는 방법을 배웁니다. 또한 Git 분기 지정 및 병합 지원에 대해서도 알아봅니다. 여기서는 Visual Studio Code를 사용하지만 어떤 Git 호환 클라이언트를 사용하든 프로세스는 동일합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- 기존 리포지토리를 복제합니다.
- 커밋으로 작업을 저장합니다.
- 변경 기록을 검토합니다.
- Visual Studio Code를 통해 분기를 사용합니다.

## <a name="estimated-timing-50-minutes"></a>예상 소요 시간: 50분

## <a name="instructions"></a>지침

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿과 Visual Studio Code 구성을 기반으로 하여 미리 구성된 Parts Unlimited 팀 프로젝트를 설정합니다.

#### <a name="task-1-configure-the-parts-unlimited-team-project"></a>작업 1: Parts Unlimited 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 **Parts Unlimited** 템플릿을 기반으로 새 프로젝트를 생성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 <https://docs.microsoft.com/en-us/azure/devops/demo-gen> 을 참조하세요.

1. **로그인**을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락**을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **Azure Repos에서 Git를 사용하여 버전 제어**를 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택**을 클릭합니다.
1. 템플릿 목록에서 **PartsUnlimited** 템플릿을 찾은 다음 **템플릿 선택**을 클릭합니다.
1. **새 프로젝트 만들기** 페이지로 돌아와서 **프로젝트 만들기**를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 Azure DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

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

1. **터미널** 창에서 다음 명령을 실행하여 Git 커밋용 사용자 이름과 이메일을 구성합니다(괄호 안의 자리 표시자는 < 및 > 기호를 제거하여 원하는 사용자 이름과 이메일로 바꾸면 됩니다).

    ```git
    git config --global user.name "<John Doe>"
    git config --global user.email <johndoe@example.com>
    ```

### <a name="exercise-1-clone-an-existing-repository"></a>연습 1: 기존 리포지토리 복제

이 연습에서는 Visual Studio Code를 사용하여 이전 연습에서 프로비전한 Git 리포지토리를 복제합니다.

#### <a name="task-1-clone-an-existing-repository"></a>작업 1: 기존 리포지토리 복제

이 작업에서는 Visual Studio Code를 사용하여 Git 리포지토리를 복제하는 프로세스를 단계별로 진행합니다.

1. Azure DevOps 조직 및 이전 연습에서 생성한 **Azure Repos에서 Git를 사용하여 버전 제어** 프로젝트가 표시된 웹 브라우저로 전환합니다.

    > **참고**: 또는 [https://dev.azure.com/`<your-Azure-DevOps-account-name>` /Version%20Controlling%20with%20Git%20in%20Azure%20Repos](https://dev.azure.com/`<your-Azure-DevOps-account-name>`/Version%20Controlling%20with%20Git%20in%20Azure%20Repos) URL로 이동하여 프로젝트 페이지에 직접 액세스할 수 있습니다. 여기서 `<your-Azure-DevOps-account-name>` 자리 표시자는 계정 이름을 나타냅니다.

1. Azure DevOps 포털의 세로 탐색 창에서 **Repos** 아이콘을 선택합니다.
1. **PartsUnlimited** 창 오른쪽 위에서 **복제**를 클릭합니다.

    > **참고**: *복제*는 Git 리포지토리의 로컬 복사본을 가져오는 작업입니다. 현재 널리 사용되고 있는 모든 개발 도구에서는 복제가 지원됩니다. 즉, Azure Repos에 연결하여 사용할 최신 원본을 가져올 수 있습니다.

2. **리포지토리 복제** 창에서 **HTTPS** 명령줄 옵션을 선택하고 리포지토리 복제 URL 옆의 **클립보드에 복사** 단추를 클릭합니다.

    > **참고**: 모든 Git 호환 도구에서 이 URL을 사용하여 코드베이스 복사본을 가져올 수 있습니다.

3. **리포지토리 복제** 창을 닫습니다.
4. 랩 컴퓨터에서 실행 중인 **Visual Studio Code**로 전환합니다.
5. **보기** 메뉴 머리글을 클릭하고 드롭다운 메뉴에서 **명령 팔레트**를 클릭합니다.

    > **참고**: 명령 팔레트에서는 타사 확장으로 구현되는 작업을 비롯하여 다양한 작업에 쉽고 편리하게 액세스할 수 있습니다. **Ctrl+Shift+P** 바로 가기 키나 **F1** 키를 사용하여 명령 팔레트를 열 수 있습니다.

6. 명령 팔레트 프롬프트에서 **Git: Clone** 명령을 실행합니다.

    > **참고**: 관련 명령을 모두 확인하려는 경우 **Git**부터 입력을 시작하면 됩니다.

7. **리포지토리 URL을 입력하거나 리포지토리 원본 선택** 텍스트 상자에 이 작업 앞부분에서 복사한 리포지토리 복제 URL을 붙여넣고 **Enter** 키를 누릅니다.
8. **폴더 선택** 대화 상자 내에서 C: 드라이브로 이동하여 새 폴더 **Git**를 만들고 선택한 다음 **리포지토리 위치 선택**을 클릭합니다.
9. 메시지가 표시되면 Azure DevOps 계정에 로그인합니다.
10. 복제 프로세스가 완료된 후 메시지가 표시되면 Visual Studio Code에서 **열기**를 클릭하여 복제된 리포지토리를 엽니다.

    > **참고**: 프로젝트 로드 문제와 관련하여 표시될 수 있는 경고는 무시해도 됩니다. 솔루션이 빌드에 적합한 상태가 아닐 수도 있습니다. 하지만 여기서는 Git 사용 방법만 중점적으로 확인할 것이므로 프로젝트는 빌드하지 않아도 됩니다.

### <a name="exercise-2-save-work-with-commits"></a>연습 2: 커밋으로 작업 저장

이 연습에서는 Visual Studio Code를 사용하여 변경 내용을 스테이징 및 커밋하는 여러 시나리오를 단계별로 진행합니다.

파일을 변경하면 Git에서 로컬 리포지토리에 변경 내용을 기록합니다. 커밋하려는 변경 내용은 "스테이징"을 통해 선택할 수 있습니다. 커밋은 항상 로컬 Git 리포지토리를 대상으로 진행되므로 변경이 완료되었거나 다른 사용자들과 공유 가능한 내용을 커밋해야 하는 것은 아닙니다. 작업을 진행하면서 추가로 커밋을 실행할 수 있으며, 변경 내용을 공유할 준비가 되면 다른 사용자들에게 해당 내용을 푸시할 수 있습니다.

Git 커밋의 구성 요소는 다음과 같습니다.

- 커밋에서 변경되는 파일. Git에서는 커밋 시에 모든 파일 변경 내용을 리포지토리에 보관합니다. 그러므로 변경 내용이 빠르게 저장되며, 지능형 병합이 가능합니다.
- 상위 커밋에 대한 참조. Git에서는 이러한 참조를 사용해 코드 기록을 관리합니다.
- 커밋을 설명하는 메시지. 커밋 작성 시 Git에 이 메시지를 제공합니다. 그러므로 커밋의 요점을 설명하는 정보를 이 메시지에 포함하는 것이 좋습니다.

#### <a name="task-1-commit-changes"></a>작업 1: 변경 내용 커밋

이 작업에서는 Visual Studio Code를 사용하여 변경 내용을 커밋합니다.

1. Visual Studio Code 창의 세로 도구 모음 위쪽에서 **탐색기** 탭을 선택하고 **/PartsUnlimited-aspnet45/src/PartsUnlimitedWebsite/Models/CartItem.cs** 파일로 이동하여 해당 파일을 선택합니다. 그러면 세부 정보 창에 파일 내용이 자동으로 표시됩니다.
1. **CartItem.cs** 파일의 `[key]` 항목 바로 위에 다음 주석이 포함된 줄을 추가합니다.

    ```csharp
    // My first change
    ```

    > **참고**: 여기서는 파일을 변경만 하면 되므로 주석의 내용은 상관이 없습니다.

1. **Ctrl+S** 를 눌러 변경 내용을 저장합니다.
1. Visual Studio Code 창에서 **소스 제어** 탭을 선택하여 Git에서 Git 리포지토리 로컬 복제본에 있는 파일의 최신 변경 내용을 인식했는지 확인합니다.
1. **소스 제어** 탭을 선택한 상태로 창 위쪽의 텍스트 상자에 커밋 메시지로 **My commit**을 입력하고 **Ctrl+Enter**를 눌러 로컬에서 변경 내용을 커밋합니다.
1. 변경 내용을 자동으로 스테이징하여 바로 커밋할 것인지를 묻는 메시지가 표시되면 **항상**을 클릭합니다.

    > **참고**: **스테이징** 과정에 대해서는 이 랩 뒷부분에서 설명합니다.

1. Visual Studio Code 창 왼쪽 아래의 **master** 레이블 오른쪽에는 **변경 내용 동기화** 아이콘(서로 반대 방향을 가리키는 세로 화살표 2개가 들어 있는 원 모양)이 있고, 위쪽 화살표 옆에는 숫자 **1**이 표시됩니다. 이 아이콘을 클릭하고 계속 진행할지를 묻는 메시지가 표시되면 **확인**을 클릭하여 **origin/master**와의 커밋 푸시 및 풀을 진행합니다.

#### <a name="task-2-review-commits"></a>작업 2: 커밋 검토

이 작업에서는 Azure DevOps 포털을 사용하여 커밋을 검토합니다.

1. Azure DevOps 인터페이스가 표시된 웹 브라우저 창으로 전환합니다.
1. Azure DevOps 포털의 세로 탐색 창에 있는 **Repos** 섹션에서**커밋**을 선택합니다.
1. 목록 맨 위에 커밋이 표시되는지 확인합니다.

#### <a name="task-3-stage-changes"></a>작업 3: 변경 내용 스테이징

이 작업에서는 Visual Studio Code를 사용하여 변경 내용을 스테이징하는 방법을 살펴봅니다. 변경 내용을 스테이징하면 커밋에 특정 파일을 선택하여 추가하고 다른 파일의 변경 내용은 제외할 수 있습니다.

1. **Visual Studio Code** 창으로 다시 전환합니다.
1. 첫 번째 주석을 다음으로 변경하고 파일을 저장하여 열려 있는 **CartItem.cs** 클래스를 업데이트합니다.
1. Visual Studio Code 창에서 **탐색기** 탭으로 다시 전환한 후 **/PartsUnlimited-aspnet45/src/PartsUnlimitedWebsite/Models/Category.cs** 파일로 이동하여 해당 파일을 선택합니다. 그러면 세부 정보 창에 파일 내용이 자동으로 표시됩니다.
1. **Category.cs** 파일의 `public int CategoryId { get; set; }` 항목 바로 위에 다음 주석이 포함된 줄을 추가하고 파일을 저장합니다.

    ```csharp
    // My third change
    ```

1. Visual Studio Code 창에서 **소스 제어** 탭으로 전환하여 **CartItem.cs** 항목 위에 마우스 포인터를 놓고 해당 항목 오른쪽의 더하기 기호를 클릭합니다.

    > **참고**: 이 작업을 수행하면 **CartItem.cs** 파일의 변경 내용만 스테이징되어 커밋 가능하도록 준비되며 **Category.cs**는 준비되지 않습니다.

1. **소스 제어** 탭을 선택한 상태로 창 위쪽의 텍스트 상자에 커밋 메시지로 **Added comments**를 입력합니다.
1. **소스 제어** 탭 위쪽에서 줄임표 기호를 클릭하고 드롭다운 메뉴에서 **커밋**을 클릭한 후 계단식 메뉴에서 **스테이징된 항목 커밋**을 클릭합니다.
1. Visual Studio Code 창 왼쪽 아래에서 **변경 내용 동기화** 단추를 클릭하여 커밋된 변경 내용을 서버와 동기화합니다. 계속 진행할지를 묻는 메시지가 표시되면 **확인**을 클릭하여 **origin/master**와의 커밋 푸시 및 풀을 진행합니다.

    > **참고**: 스테이징된 변경 내용만 커밋되었으므로 나머지 변경 내용은 아직 동기화 보류 중인 상태입니다.

### <a name="exercise-3-review-history"></a>연습 3: 기록 검토

이 연습에서는 Azure DevOps 포털을 사용하여 커밋 기록을 검토합니다.

Git에서는 각 커밋에 저장된 상위 참조 정보를 사용하여 개발 과정의 전체 기록을 관리합니다. 터미널이나 다양하게 제공되는 Visual Studio Code 확장 중 하나를 사용하면 이 커밋 기록을 손쉽게 검토하여 파일을 변경한 시기를 파악하고 코드 버전 간의 차이점을 확인할 수 있습니다. Azure DevOps 포털을 사용하여 변경 내용을 검토할 수도 있습니다.

Git에서는 끌어오기 요청을 통해 **분기 및 병합** 기능을 사용합니다. 따라서 개발 과정의 커밋 기록이 항상 시간순으로 제공되는 것은 아닙니다. 그러므로 기록을 사용해 버전을 비교할 때는 두 시점 간의 파일 변경 내용이 아니라 두 커밋 간의 파일 변경 내용을 확인해야 합니다. 가령 2주 전에 feature 분기에서 변경 내용이 커밋되었는데 이 분기가 어제 master 분기에 병합되어 master 분기의 파일에서는 해당 변경 내용이 최신 항목일 수도 있습니다.

#### <a name="task-1-compare-files"></a>작업 1: 파일 비교

이 작업에서는 Azure DevOps 포털을 사용하여 커밋 기록을 살펴봅니다.

1. Visual Studio Code 창에서 **소스 제어** 탭을 연 상태로 파일의 스테이징되지 않은 버전을 나타내는 **Category.cs**를 선택합니다.

    > **참고**: 그러면 비교 보기가 열리므로 적용된 변경 내용을 쉽게 찾을 수 있습니다. 여기서는 주석만 하나 추가되었습니다.

1. **Azure DevOps** 포털의 **커밋** 창이 표시된 웹 브라우저 창으로 전환하여 소스 분기와 병합 항목을 검토합니다. 그러면 소스에 변경 내용이 적용된 시기와 방식을 편리하게 시각화할 수 있습니다.
1. 아래쪽의 **병합된 PR 27** 항목으로 스크롤한 다음 해당 항목 위에 마우스 포인터를 놓으면 오른쪽에 줄임표 기호가 표시됩니다.
1. 줄임표를 클릭하고 드롭다운 메뉴에서 **파일 찾아보기**를 선택한 후에 결과를 검토합니다.

    > **참고**: 이 보기에는 커밋에 해당하는 소스 상태가 표시됩니다. 따라서 각 소스 파일을 검토하고 다운로드할 수 있습니다.

### <a name="exercise-4-work-with-branches"></a>연습 4: 분기 사용

이 연습에서는 Visual Studio Code 및 Azure DevOps 포털을 사용하여 분기 관리 시나리오를 단계별로 진행합니다.

Azure DevOps 포털 내 **Azure Repos**의 **분기** 보기에서 Azure DevOps Git 리포지토리의 분기를 관리할 수 있습니다. 이 보기를 사용자 지정하여 가장 중요한 분기를 추적할 수도 있습니다. 그러면 팀에서 적용한 변경 내용을 항상 파악할 수 있습니다.

변경 내용을 분기에 커밋해도 다른 분기에는 아무런 영향이 없습니다. 그러므로 주 프로젝트에 변경 내용을 병합하지 않고도 다른 사용자와 분기를 공유할 수 있습니다. 새 분기를 만들어 특정 기능의 변경 내용이나 버그 수정을 master 분기 및 기타 작업에서 격리할 수도 있습니다. 분기는 구조가 단순하므로 빠르고 쉽게 분기 간을 전환할 수 있습니다. 분기 사용 시 Git에서는 소스의 복사본을 여러 개 만들지 않으며, 분기 사용을 시작할 때 커밋에 저장된 기록 정보를 사용하여 해당 분기에 파일을 다시 만듭니다. Git 워크플로에서는 기능 및 버그 수정 관리용 분기를 만들어서 사용합니다. 코드 공유, 끌어오기 요청을 통해 코드 검토 등 Git 워크플로에서 수행하는 나머지 작업에서는 모두 분기를 사용합니다. 분기별로 작업을 격리하는 경우 현재 분기만 변경하면 작업 중인 내용을 쉽게 변경할 수 있습니다.

#### <a name="task-1-create-a-new-branch-in-your-local-repository"></a>작업 1: 로컬 리포지토리에 새 분기 만들기

이 작업에서는 Visual Studio Code를 사용하여 분기를 만듭니다.

1. 랩 컴퓨터에서 실행 중인 **Visual Studio Code**로 전환합니다.
1. **소스 제어** 탭을 선택한 상태로 Visual Studio Code 창 왼쪽 아래에서 **master**를 클릭합니다.
1. 팝업 창에서 **+ 새 분기 만들기...** 를 선택합니다.
1. **분기 이름** 텍스트 상자에 **dev**를 입력하여 새 분기를 지정하고 **Enter** 키를 누릅니다.
1. **'dev' 분기를 만들 참조 선택** 텍스트 상자에서 참조 분기로 **master**를 선택합니다.

    > **참고**: 그러면 **dev** 분기로 자동 전환됩니다.

#### <a name="task-2-work-with-branches"></a>작업 2: 분기 사용

이 작업에서는 이전 작업에서 만든 분기를 Visual Studio Code를 통해 사용합니다.

Git에서는 작업 중인 분기를 추적하며 분기 체크 아웃 시 파일이 해당 분기의 최신 커밋과 일치하는지를 확인합니다. 분기를 활용하면 같은 로컬 Git 리포지토리에 있는 소스 코드의 여러 버전을 동시에 사용할 수 있습니다. Visual Studio Code를 사용하여 분기를 게시, 체크 아웃 및 삭제할 수 있습니다.

1. **Visual Studio Code** 창에서 **소스 제어** 탭을 선택한 상태로 Visual Studio Code 창 왼쪽 아래에 있는 **변경 내용 게시** 아이콘을 클릭합니다. 이 아이콘은 새로 만든 분기를 나타내는 **dev** 레이블 바로 오른쪽에 있습니다.
1. **Azure DevOps** 포털의 **커밋** 창이 표시된 웹 브라우저 창으로 전환하여 **분기**를 선택합니다.
1. **분기** 창의 **내 항목** 탭에서 분기 목록에 **dev**가 포함되어 있는지 확인합니다.
1. **dev** 분기 항목 위에 마우스 포인터를 올리면 오른쪽에 줄임표 기호가 표시됩니다.
1. 줄임표를 클릭하고 팝업 메뉴에서 분기 삭제를 선택한 다음 **분기 삭제**를 확인하라는 메시지가 표시되면 **삭제**를 클릭합니다.
1. **Visual Studio Code** 창으로 다시 전환하여 **소스 제어** 탭을 선택한 상태로 Visual Studio Code 창 왼쪽 아래에서 **dev** 항목을 클릭합니다. 그러면 Visual Studio Code 창 위쪽에 기존 분기가 표시됩니다.
1. **dev** 분기 2개가 목록에 표시됨을 확인합니다.

    > **참고**: 로컬(**dev**) 분기가 목록에 표시되는 이유는, 원격 리포지토리에서 분기를 삭제해도 로컬 분기에는 아무런 영향이 없기 때문입니다. 서버(**origin/dev**) 분기는 정리되지 않았으므로 목록에 표시됩니다.

1. 분기 목록에서 **master** 분기를 선택하여 체크 아웃합니다.
1. **Ctrl+Shift+P**를 눌러 **명령 팔레트**를 엽니다.
1. **명령 팔레트** 프롬프트에서 **Git: Delete** 입력을 시작하여 **Git: Delete Branch**가 표시되면 선택합니다.
1. 삭제할 분기 목록에서 **dev** 항목을 선택합니다.
1. Visual Studio Code 창 왼쪽 아래에서 **master** 항목을 다시 클릭합니다. 그러면 Visual Studio Code 창 위쪽에 기존 분기가 표시됩니다.
1. 로컬 **dev** 분기는 목록에 더 이상 표시되지 않으며 원격 **origin/dev** 분기는 계속 표시됨을 확인합니다.
1. **Ctrl+Shift+P**를 눌러 **명령 팔레트**를 엽니다.
1. **명령 팔레트** 프롬프트에서 **Git: Fetch** 입력을 시작하고 **Git: Fetch(Prune)** 가 표시되면 선택합니다.

    > **참고**: 이 명령을 실행하면 로컬 스냅숏의 원래 분기가 업데이트ehlau 스냅숏에 더 이상 없는 분기는 삭제됩니다.

    > **참고**: Visual Studio Code 창 오른쪽 아래의 **출력** 창을 선택하면 이러한 작업의 정확한 내용을 확인할 수 있습니다. 출력 콘솔에 Git 로그가 표시되지 않으면 소스로 **Git**를 선택합니다.

1. Visual Studio Code 창 왼쪽 아래에서 **master** 항목을 다시 클릭합니다.
1. 분기 목록에 **origin/dev** 분기가 더 이상 표시되지 않음을 확인합니다.

## <a name="review"></a>검토

이 랩에서는 Visual Studio Code를 사용하여 기존 리포지토리를 복제하고 커밋을 수행하여 작업을 저장했습니다. 그런 다음 변경 내용 기록을 검토했으며 분기를 사용해 보았습니다.
