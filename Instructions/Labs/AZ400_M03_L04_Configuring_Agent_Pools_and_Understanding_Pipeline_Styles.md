---
lab:
  title: 에이전트 풀 구성 및 파이프라인 스타일 이해
  module: 'Module 03: Implement CI with Azure Pipelines and GitHub Actions'
---

# <a name="configuring-agent-pools-and-understanding-pipeline-styles"></a>에이전트 풀 구성 및 파이프라인 스타일 이해

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

- [Windows용 Git 다운로드 페이지](https://gitforwindows.org/). 이 랩의 필수 구성 요소로 설치됩니다.

- [Visual Studio Code](https://code.visualstudio.com/) 이 랩의 필수 구성 요소로 설치됩니다.

## <a name="lab-overview"></a>랩 개요

YAML 기반 파이프라인을 사용하면 CD/CI를 코드로 완벽하게 구현할 수 있습니다. 이 구현에서는 파이프라인 정의가 Azure DevOps 프로젝트에 포함된 코드와 같은 리포지토리에 저장됩니다. YAML 기반 파이프라인은 끌어오기 요청, 코드 검토, 기록, 분기, 템플릿 등 클래식 파이프라인에서 제공되는 폭넓은 기능을 지원합니다.

어떤 파이프라인 스타일을 선택하든 Azure Pipelines를 사용하여 솔루션을 배포하거나 코드를 작성하려면 에이전트가 필요합니다. 작업을 한 번에 하나씩 실행하는 에이전트는 컴퓨팅 리소스를 호스트합니다. 작업은 에이전트의 호스트 머신에서 바로 실행할 수도 있고 컨테이너에서 실행할 수도 있습니다. Microsoft에서 호스트하는 에이전트(자동으로 관리됨)를 사용하여 작업을 실행하거나 직접 설정 및 관리하는 자체 호스팅 에이전트를 구현할 수 있습니다.

이 랩에서는 YAML 파이프라인에서 자체 호스팅 에이전트를 구현하고 사용하는 방법을 알아봅니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- YAML 기반 파이프라인을 구현합니다.
- 자체 호스팅 에이전트를 구현합니다.

## <a name="estimated-timing-45-minutes"></a>예상 소요 시간: 45분

## <a name="instructions"></a>지침

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿을 기반으로 하여 미리 구성된 Parts Unlimited 팀 프로젝트를 설정합니다.

#### <a name="task-1-configure-the-team-project"></a>작업 1: 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 **PartsUnlimited** 템플릿을 기반으로 새 프로젝트를 생성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 <https://docs.microsoft.com/en-us/azure/devops/demo-gen> 을 참조하세요.

1. **로그인**을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락**을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **에이전트 풀 구성 및 파이프라인 스타일 이해**를 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택**을 클릭합니다.
1. **템플릿 선택** 페이지에서 **PartsUnlimited** 템플릿을 클릭한 다음 **템플릿 선택**을 클릭합니다.
1. **프로젝트 만들기**를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1. **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동**을 클릭합니다.

### <a name="exercise-1-author-yaml-based-azure-pipelines"></a>연습 1: YAML 기반 Azure Pipelines 작성

이 연습에서는 클래식 Azure 파이프라인을 YAML 기반 파이프라인으로 변환합니다.

#### <a name="task-1-create-an-azure-devops-yaml-pipeline"></a>작업 1: Azure DevOps YAML 파이프라인 만들기

이 작업에서는 템플릿 기반 Azure DevOps YAML 파이프라인을 만듭니다.

1. **에이전트 풀 구성 및 파이프라인 스타일 이해** 프로젝트가 열린 Azure DevOps 포털이 표시되어 있는 웹 브라우저 왼쪽의 세로 탐색 창에서 **Pipelines**를 클릭합니다.
1. **파이프라인** 창의 **최근** 탭에서 **새 파이프라인**을 클릭합니다.
1. **코드 위치** 창에서 **Azure Repos Git**를 클릭합니다.
1. **리포지토리 선택** 창에서 **PartsUnlimited**를 클릭합니다.
1. **파이프라인 YAML 검토** 창에서 샘플 파이프라인을 검토하고, **실행** 단추 옆에 있는 아래쪽 방향 캐럿 기호를 클릭하고, **저장**을 클릭합니다.

### <a name="exercise-2-manage-azure-devops-agent-pools"></a>연습 2: Azure DevOps 에이전트 풀 관리

이 연습에서는 자체 호스팅 Azure DevOps 에이전트를 구현합니다.

#### <a name="task-1-configure-an-azure-devops-self-hosting-agent"></a>작업 1: Azure DevOps 자체 호스팅 에이전트 구성

이 작업에서는 LOD VM을 Azure DevOps 자체 호스팅 에이전트로 구성한 다음 빌드 파이프라인을 실행하는 데 사용합니다.

1. Lab VM(Lab Virtual Machine) 또는 개인 컴퓨터를 내에서 웹 브라우저를 시작하고 [Azure DevOps 포털](https://dev.azure.com)로 이동한 다음 Azure DevOps 조직과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. Azure DevOps 포털의 Azure DevOps 페이지 오른쪽 위에서 **사용자 설정** 아이콘을 클릭합니다. 미리 보기 기능이 켜져 있는지 여부에 따라 메뉴에 **보안** 또는 **개인용 액세스 토큰** 항목이 표시되어야 합니다. **보안**이 표시되면 해당 항목을 클릭한 다음, **개인용 액세스 토큰**을 선택합니다. **개인용 액세스 토큰** 창에서 **+ 새 토큰**을 클릭합니다.
1. **새 개인용 액세스 토큰 만들기** 창에서 **모든 범위 표시** 링크를 클릭하고 다음 설정을 지정한 후에 **만들기**를 클릭합니다. 다른 값은 모두 기본값으로 유지합니다.

    | 설정 | 값 |
    | --- | --- |
    | Name | **에이전트 풀 구성 및 파이프라인 스타일 이해 랩** |
    | 범위(사용자 정의됨) | **에이전트 풀**(필요하면 아래에 더 많은 범위 옵션 표시)|
    | 사용 권한 | **읽기 및 관리** |

1. **성공** 창에서 개인용 액세스 토큰의 값을 클립보드에 붙여넣습니다.

    > **참고**: 지금 토큰을 복사해야 합니다. 이 창을 닫으면 토큰을 검색할 수 없습니다.

1. **성공** 창에서 **닫기**를 클릭합니다.
1. Azure DevOps 포털에서 **개인용 액세스 토큰** 창 왼쪽 위의 **Azure DevOps** 기호를 클릭한 다음 왼쪽 아래의 **조직 설정** 레이블을 클릭합니다.
1. **개요** 창 왼쪽의 세로 메뉴에 있는 **Pipelines** 섹션에서 **에이전트 풀**을 클릭합니다.
1. **에이전트 풀** 창의 오른쪽 위에서 **풀 추가**를 클릭합니다.
1. **에이전트 풀 추가** 창의 **풀 유형** 드롭다운 목록에서 **자체 호스팅**을 선택하고 **이름** 텍스트 상자에 **az400m05l05a-pool**을 입력한 다음 **만들기**를 클릭합니다.
1. **에이전트 풀** 창으로 돌아와서 새로 만든 **az400m05l05a-pool**을 나타내는 항목을 클릭합니다.
1. **az400m05l05a-pool** 창의 **작업** 탭에서 **새 에이전트** 단추를 클릭합니다.
1. **에이전트 가져오기** 창에서 **Windows** 및 **x64** 탭이 선택되어 있는지 확인하고 **다운로드**를 클릭하여 에이전트 이진 파일이 포함된 zip 보관 파일을 사용자 프로필 내의 로컬 **다운로드** 폴더에 다운로드합니다.

    > **참고**: 이 시점에서 현재 시스템 설정으로 인해 파일을 다운로드할 수 없다는 오류 메시지가 표시되면 Internet Explorer 창 오른쪽 위에 있는 **설정** 메뉴 머리글을 나타내는 톱니바퀴 기호를 클릭합니다. 그런 다음 드롭다운 메뉴에서 **인터넷 옵션**을 선택하고 **인터넷 옵션** 대화 상자에서 **고급**을 클릭합니다. 그리고 나서 **고급** 탭의 **원래대로**를 클릭하고 **Internet Explorer 기본 설정 복원** 대화 상자에서 **다시 설정**을 다시 클릭한 후에 **닫기**를 클릭하고 다운로드를 다시 시도해 봅니다.

1. 관리자 권한으로 Windows PowerShell을 시작하고, **관리자: Windows PowerShell** 콘솔에서 다음 명령을 실행하여 **C:\\agent** 디렉터리를 만들고, 다운로드한 보관 파일의 콘텐츠를 이 디렉터리로 추출합니다.

    ```powershell
    cd \
    mkdir agent ; cd agent
    $TARGET = Get-ChildItem "$Home\Downloads\vsts-agent-win-x64-*.zip"
    Add-Type -AssemblyName System.IO.Compression.FileSystem
    [System.IO.Compression.ZipFile]::ExtractToDirectory($TARGET, "$PWD")
    ```

1. 동일한 **관리자: Windows PowerShell** 콘솔에서 다음 명령을 실행하여 에이전트를 구성합니다.

    ```powershell
    .\config.cmd
    ```

1. 메시지가 표시되면 다음 설정의 값을 지정합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 서버 URL 입력 | **<https://dev.azure.com/>`<organization_name>`** 형식의 Azure DevOps 조직의 URL. 여기서 `<organization_name>` 은 Azure DevOps 조직의 이름을 나타냄 |
    | 인증 유형 입력(PAT의 경우 Enter 키 누르기) | **Enter** |
    | 개인용 액세스 토큰 입력 | 이 작업의 앞부분에서 적어 둔 액세스 토큰 |
    | 에이전트 풀 입력(기본값을 사용하려면 Enter 키 누르기) | **az400m05l05a-pool** |
    | 에이전트 이름 입력 | **az400m05-vm0** |
    | 작업 폴더 입력(_work를 사용하려면 Enter 키 누르기) | **Enter** |
    | **(표시되는 경우만 해당)** 각 단계의 작업에서 압축 풀기 수행 여부 입력 (N을 입력하려면 Enter 키 누르기) | **경고**: 메시지가 표시되는 경우에만 **Enter** 키를 누릅니다.|
    | 에이전트를 서비스로 실행할지 여부 입력 (Y/N)(N을 입력하려면 Enter 키 누르기) | **예** |
    | SERVICE_SID_TYPE_UNRESTRICTED 사용 입력 (Y/N)(N을 입력하려면 Enter 키 누르기) | **예** |
    | 서비스에 사용할 사용자 계정 입력(NT AUTHORITY\NETWORK SERVICE에 대해 Enter 키 누르기) | **Enter** |
    | 구성이 완료된 직후 서비스가 시작되지 않도록 할지 여부를 입력할까요? (Y/N)(N을 입력하려면 Enter 키 누르기) | **Enter** |

    > **참고**: 자체 호스팅 에이전트는 서비스로 실행할 수도 있고 대화형 프로세스로 실행할 수도 있습니다. 처음에는 대화형 모드를 사용하는 것이 좋습니다. 이렇게 하면 에이전트 기능을 쉽게 확인할 수 있기 때문입니다. 프로덕션 환경에서는 자동 로그온을 사용하도록 설정한 대화형 프로세스나 서비스로 에이전트를 실행해야 합니다. 이 두 모드에서는 실행 상태가 영구 보존되며, 운영 체제를 다시 시작할 때 에이전트가 자동으로 다시 시작되기 때문입니다.

1. Azure DevOps 포털이 표시된 브라우저 창으로 전환하여 **에이전트 가져오기** 창을 닫습니다.
1. **az400m05l05a-pool** 창의 **에이전트** 탭으로 돌아와서 새로 구성한 에이전트가 **온라인** 상태로 목록에 표시되는지 확인합니다.
1. Azure DevOps 포털이 표시된 웹 브라우저 창의 왼쪽 위에서 **Azure DevOps** 레이블을 클릭합니다.
1. 프로젝트 목록이 표시된 브라우저 창에서 **에이전트 풀 구성 및 파이프라인 스타일 이해** 프로젝트를 나타내는 타일을 클릭합니다.
1. **에이전트 풀 구성 및 파이프라인 스타일 이해** 창 왼쪽의 세로 탐색 창에 있는 **Pipelines** 섹션에서 **파이프라인**을 클릭합니다.
1. **파이프라인** 창의 **최근** 탭에서 **PartsUnlimited**를 선택하고 **PartsUnlimited** 창에서 **편집**을 선택합니다.
1. **PartsUnlimited** 편집 창의 기존 YAML 기반 파이프라인에서 대상 에이전트 풀을 지정하는 줄 `vmImage: windows-2019`를, 새로 만든 자체 호스팅 에이전트 풀을 지정하는 다음 콘텐츠로 바꿉니다.

    ```yaml
    name: az400m05l05a-pool
    demands:
    - agent.name -equals az400m05-vm0
    ```
    > **경고**: 복사/붙여넣기에 주의하여 들여쓰기를 위에 표시된 것과 동일하게 사용해야 합니다.


1. `Task: NugetToolInstaller@0`의 경우 **설정(작업 위에 회색으로 표시되는 링크)** 을 클릭하고, **설치할 NuGet.exe 버전**을 **4.0.0**으로 수정하고, **추가**를 클릭합니다.
1.  **PartsUnlimited** 편집 창 오른쪽 위의 **저장**을 클릭하고 **저장** 창에서 **저장**을 다시 클릭합니다. 그러면 이 파이프라인을 기반으로 하는 빌드가 자동으로 트리거됩니다.
1.  Azure DevOps 포털 왼쪽의 세로 탐색 창에 있는 **Pipelines** 섹션에서 **파이프라인**을 클릭합니다.
1.  **파이프라인** 창의 **최근** 탭에서 **PartsUnlimited** 항목을 클릭하고 **PartsUnlimited** 창의 **실행** 탭에서 최신 실행을 선택합니다. 그런 다음 해당 실행의 **요약** 창에서 아래쪽으로 스크롤하여 **작업** 섹션에서 **1단계**를 클릭하고 작업이 정상적으로 완료될 때까지 모니터링합니다.



## <a name="review"></a>검토

이 랩에서는 YAML 파이프라인에서 자체 호스팅 에이전트를 구현하고 사용하는 방법을 알아보았습니다.
