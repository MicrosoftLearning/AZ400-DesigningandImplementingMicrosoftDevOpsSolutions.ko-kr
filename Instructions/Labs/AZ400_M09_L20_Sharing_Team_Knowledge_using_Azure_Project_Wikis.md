---
lab:
  title: '랩 20: Azure 프로젝트 Wiki를 사용하여 팀의 지식 공유'
  module: 'Module 09: Implement continuous feedback'
ms.openlocfilehash: 5d9f8d7b375efd25ac91dfc44fd90695c853bc35
ms.sourcegitcommit: d78aebd7b14277a53f152e26cea68a30b0e90d73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2022
ms.locfileid: "146276127"
---
# <a name="lab-20-sharing-team-knowledge-using-azure-project-wikis"></a>랩 20: Azure 프로젝트 Wiki를 사용하여 팀의 지식 공유

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

## <a name="lab-overview"></a>랩 개요

이 랩에서는 Azure DevOps에서 Wiki를 만들고 구성합니다. 이 과정에서 Markdown 콘텐츠 관리와 Mermaid 다이어그램 작성을 수행합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure 프로젝트에서 Wiki를 만듭니다.
- Markdown을 추가하고 편집합니다.
- Mermaid 다이어그램을 만듭니다.

## <a name="estimated-timing-45-minutes"></a>예상 소요 시간: 45분

## <a name="instructions"></a>지침

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 Azure DevOps 데모 생성기 템플릿과 Microsoft Teams에서 만든 팀을 기반으로 하여 미리 구성된 **Tailwind Traders** 팀 프로젝트를 설정합니다.

#### <a name="task-1-configure-the-team-project"></a>작업 1: 팀 프로젝트 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 **Tailwind Traders** 템플릿을 기반으로 새 프로젝트를 생성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 <https://docs.microsoft.com/en-us/azure/devops/demo-gen> 을 참조하세요.

1. **로그인** 을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락** 을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **Azure 프로젝트 Wiki를 사용하여 팀의 지식 공유** 를 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택** 을 클릭합니다.
1. 템플릿 목록에서 **Tailwind Traders** 템플릿을 선택한 다음 **템플릿 선택** 을 클릭합니다.
1. **새 프로젝트 만들기** 페이지로 돌아와서 누락된 확장을 설치하라는 메시지가 표시되면 **ARM 출력** 레이블 아래의 체크박스를 선택하고 **프로젝트 만들기** 를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 Azure DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1. **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동** 을 클릭합니다.

### <a name="exercise-1-publish-code-as-a-wiki"></a>연습 1: 코드를 Wiki로 게시

이 연습에서는 Azure DevOps 리포지토리를 Wiki로 게시하고 게시된 Wiki를 관리하는 과정을 단계별로 진행합니다.

> **참고**: Git 리포지토리에 보관하는 콘텐츠는 Azure DevOps Wiki로 게시할 수 있습니다. 예를 들어 소프트웨어 개발 키트 지원을 위해 작성된 콘텐츠, 제품 설명서, README 파일 등을 Wiki에 바로 게시할 수 있습니다. 그리고 같은 Azure DevOps 팀 프로젝트 내에서 여러 Wiki를 게시할 수도 있습니다.

#### <a name="task-1-publish-a-branch-of-an-azure-devops-repo-as-wiki"></a>작업 1: Azure DevOps 리포지토리의 한 분기를 Wiki로 게시

이 작업에서는 Azure DevOps 리포지토리의 한 분기를 Wiki로 게시합니다.

> **참고**: 제품 버전에 해당하는 Wiki를 게시했다면 새 제품 버전을 릴리스할 때 새 분기를 게시할 수 있습니다.

1. 왼쪽의 세로 메뉴에서 **Repos** 를 클릭하고, **파일** 창의 위쪽 섹션에서 **TailwindTraders-Website** 리포지토리가 선택되었는지 확인합니다(위쪽에 Git 아이콘과 함께 있는 드롭다운에서 선택). 분기 드롭다운 목록("파일" 위쪽에 분기 아이콘과 함께 있음)에서 **main** 을 선택하고 기본 분기의 내용을 검토합니다.
1. **Files** 창 왼쪽에 있는 리포지토리 폴더 및 파일 계층 구조 목록에서 **Documents** 폴더와 **Images** 하위 폴더를 확장합니다. 그런 다음 **Images** 하위 폴더에서 **Website.png** 항목을 찾아 오른쪽 끝에 마우스 포인터를 놓으면 **자세히** 메뉴를 나타내는 세로 줄임표(점 3개) 기호가 표시됩니다. **자세히** 를 클릭하고 **드롭다운** 메뉴에서 다운로드를 클릭하여 **Website.png** 파일을 랩 컴퓨터의 로컬 **Downloads** 폴더에 다운로드합니다.

    >**참고**: 이 이미지는 이 랩의 다음 연습에서 사용합니다.

1. 왼쪽의 세로 메뉴에서 **개요** 를 클릭하고, **개요** 섹션에서 **Wiki** 를 선택하고, **코드를 Wiki로 게시* 를 선택합니다.
1. **코드를 Wiki로 게시** 창에서 다음 설정을 지정하고 **게시** 를 클릭합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 리포지토리 | **TailwindTraders-Website** |
    | Branch | **main** |
    | 폴더 | **/Documents** |
    | Wiki 이름 | **Tailwind Traders(Documents)** |

    >**참고**: 이렇게 하면 **GitHubAction.md** 파일의 콘텐츠가 자동으로 표시됩니다.

1. **GitHubActions** 파일의 내용을 검토하고 Wiki의 전체 구조가 기본 리포지토리 구조와 일치함을 확인합니다.

#### <a name="task-2-manage-content-of-a-published-wiki"></a>작업 2: 게시한 Wiki의 내용 관리

이 작업에서는 이전 작업에서 게시한 Wiki의 내용을 관리합니다.

1. 왼쪽 세로 메뉴에서 **Repos** 를 클릭하고 **Files** 창 위쪽 섹션의 드롭다운 메뉴에 **TailwindTraders-Website** 리포지토리와 **main** 분기가 표시되어 있는지 확인합니다. 그런 다음 리포지토리 폴더 계층 구조에서 **Documents** 폴더를 선택하고 오른쪽 위에서 **새로 만들기** 를 클릭한 후에 드롭다운 메뉴에서 **파일** 을 클릭합니다.
1. **새 파일** 패널의 **새 파일 이름** 에서 **/Documents/** 접두사 뒤에 **.order** 를 입력하고 **만들기** 를 클릭합니다.
1. **.order** 창의 **콘텐츠** 탭에 다음 코드를 입력하고 **커밋** 을 클릭합니다.

    ```text
    GitHubActions
    Images
    ```

1. **커밋** 창에서 **커밋** 을 클릭합니다.
1. 왼쪽 세로 메뉴에서 **개요** 를 클릭하고 **개요** 섹션에서 **Wiki** 를 선택합니다. 그런 다음 창 위쪽 섹션에 **Tailwind Traders (Documents)** 가 표시되어 있는지 확인하고 Wiki 콘텐츠 순서를 검토합니다.

    >**참고**: Wiki 콘텐츠의 순서가 **.order** 파일에 나열된 파일 및 폴더의 순서와 일치해야 합니다.

1. 왼쪽 세로 메뉴에서 **Repos** 를 클릭하고 **Files** 창 위쪽 섹션의 드롭다운 메뉴에 **TailwindTraders-Website** 리포지토리와 **main** 분기가 표시되어 있는지 확인합니다. 그런 다음 파일 목록의 **Documents** 아래에서 **GitHubActions.md** 를 선택하고 **GitHubActions.md** 창에서 **편집** 을 클릭합니다.
1. **GitHubActions.md** 창에서 `#GitHub Actions` 헤더 바로 아래에 **Documents** 폴더 내의 이미지 중 하나를 참조하는 다음 Markdown 요소를 추가합니다.

    ```
    ![Tailwind Traders Website](Images/Website.png)
    ```

1. **GitHubActions.md** 창에서 **커밋** 을 클릭하고 **커밋** 창에서 **커밋** 을 클릭합니다.
1. **GitHubActions.md** 창의 **미리 보기** 탭에서 이미지가 표시되는지 확인합니다.
1. 왼쪽 세로 메뉴에서 **개요** 를 클릭하고 **개요** 섹션에서 **Wiki** 를 선택합니다. 그런 다음 창 위쪽 섹션에 **Tailwind Traders(Documents)** 가 표시되어 있는지 확인하고 **GitHubActions** 창의 내용에 새로 참조한 이미지가 포함되어 있는지 확인합니다.

### <a name="exercise-2-create-and-manage-a-project-wiki"></a>연습 2: 프로젝트 Wiki 만들기 및 관리

이 연습에서는 프로젝트 Wiki를 만들고 관리하는 과정을 단계별로 진행합니다.

> **참고**: 기존 리포지토리와는 관계없이 Wiki를 만들고 관리할 수 있습니다.

#### <a name="task-1-create-a-project-wiki-including-a-mermaid-diagram-and-an-image"></a>작업 1: Mermaid 다이어그램과 이미지가 포함된 프로젝트 Wiki 만들기

이 작업에서는 프로젝트 Wiki를 만든 후 Mermaid 다이어그램과 이미지를 추가합니다.

1. 랩 컴퓨터에서 **Azure 프로젝트 Wiki를 사용하여 팀의 지식 공유** 프로젝트의 **Wiki** 창이 표시된 Azure DevOps 포털 내의 **Tailwind Traders(Documents)** Wiki 콘텐츠를 선택한 상태로 창 위쪽에서 **Tailwind Traders(Documents)** 드롭다운 목록 머리글을 클릭합니다. 그런 후에 드롭다운 목록에서 **새 프로젝트 Wiki 만들기** 를 선택합니다.
1. **페이지 제목** 텍스트 상자에 **프로젝트 디자인** 을 입력합니다.
1. 페이지 본문 위에 커서를 놓고 머리글 설정을 나타내는 도구 모음 맨 왼쪽 아이콘을 클릭합니다. 그런 후에 드롭다운 목록에서 **머리글 1** 을 클릭합니다. 그러면 줄 시작 부분에 해시 문자( **#** )가 자동으로 추가됩니다.
1. 새로 추가된 **#** 문자 바로 뒤에 **Authentication and Authorization** 을 입력하고 **Enter** 키를 누릅니다.
1. 머리글 설정을 나타내는 도구 모음 맨 왼쪽 아이콘을 클릭합니다. 그런 후에 드롭다운 목록에서 **머리글 2** 를 클릭합니다. 그러면 줄 시작 부분에 해시 문자( **##** )가 자동으로 추가됩니다.
1. 새로 추가된 **##** 문자 바로 뒤에 **Azure DevOps OAuth 2.0 Authorization Flow** 를 입력하고 **Enter** 키를 누릅니다.

1. 다음 코드를 **복사하고 붙여넣어** Mermaid 다이어그램을 Wiki에 삽입합니다.

    ```
    ::: mermaid
    sequenceDiagram
     participant U as User
     participant A as Your app
     participant D as Azure DevOps
     U->>A: Use your app
     A->>D: Request authorization for user
     D-->>U: Request authorization
     U->>D: Grant authorization
     D-->>A: Send authorization code
     A->>D: Get access token
     D-->>A: Send access token
     A->>D: Call REST API with access token
     D-->>A: Respond to REST API
     A-->>U: Relay REST API response
    :::
    ```

    >**참고**: Mermaid 구문에 대한 자세한 내용은 [Mermaid 정보](https://mermaid-js.github.io/mermaid/#/)를 참조하세요.

1. 편집기 창 오른쪽의 미리 보기 창에서 **다이어그램 로드** 를 클릭하고 결과를 검토합니다.

    >**참고**: 출력은 [OAuth 2.0를 사용하여 REST API 액세스 권한 부여](https://docs.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/oauth?view=azure-devops) 방법을 설명하는 순서도와 비슷합니다.

1. 편집기 창 오른쪽 위에서 **저장** 단추 옆의 아래쪽 캐럿을 클릭하고 드롭다운 메뉴에서 **수정 메시지와 함께 저장** 을 선택합니다.
1. **페이지 저장** 대화 상자에 **Authentication and authorization section with the OAuth 2.0 Mermaid diagram** 을 입력하고 **저장** 을 클릭합니다.
1. **프로젝트 디자인** 편집기 창에서 이 작업 앞부분에서 추가한 Mermaid 요소 끝부분에 커서를 놓고 **Enter** 키를 눌러 줄을 하나 더 추가합니다. 그런 다음 머리글 설정을 나타내는 도구 모음 맨 왼쪽 아이콘을 클릭하고 드롭다운 목록에서 **머리글 2** 를 클릭합니다. 그러면 줄 시작 부분에 이중 해시 문자( **##** )가 자동으로 추가됩니다.
1. 새로 추가된 **##** 문자 바로 뒤에 **User Interface** 를 입력하고 **Enter** 키를 누릅니다.
1. **프로젝트 디자인** 편집기 창의 도구 모음에서 **파일 삽입** 작업을 나타내는 클립 아이콘을 클릭하고 **열기** 대화 상자에서 **Downloads** 폴더로 이동합니다. 그런 다음 이전 연습에서 다운로드한 **Website.png** 파일을 선택하고 **열기** 를 클릭합니다.
1. **프로젝트 디자인** 편집기 창으로 돌아와 미리 보기 창을 검토하여 이미지가 올바르게 표시되는지를 확인합니다.
1. 편집기 창 오른쪽 위에서 **저장** 단추 옆의 아래쪽 캐럿을 클릭하고 드롭다운 메뉴에서 **수정 메시지와 함께 저장** 을 선택합니다.
1. **페이지 저장** 대화 상자에 **User Interface section with the Tailwind Traders image** 를 입력하고 **저장** 을 클릭합니다.
1. 편집기 창으로 돌아와서 오른쪽 위의 **닫기** 를 클릭합니다.

#### <a name="task-2-manage-a-project-wiki"></a>작업 2: 프로젝트 Wiki 관리

이 작업에서는 새로 만든 프로젝트 Wiki를 관리합니다.

>**참고**: 먼저 Wiki 페이지의 최신 변경 내용을 되돌립니다.

1. 랩 컴퓨터에서 **Azure 프로젝트 Wiki를 사용하여 팀의 지식 공유** 프로젝트의 **Wiki 창** 이 표시된 Azure DevOps 포털 내의 **프로젝트 디자인** Wiki 콘텐츠를 선택한 상태로 오른쪽 위에서 세로 줄임표 기호를 클릭합니다. 그런 후에 드롭다운 메뉴에서 **수정 내용 보기** 를 클릭합니다.
1. **수정 내용** 창에서 최신 변경 내용을 나타내는 항목을 클릭합니다.
1. 그러면 표시되는 창에서 문서의 이전 버전이 현재 버전을 비교한 내용을 검토하고 **되돌리기** 를 클릭합니다. 되돌리기를 확인하라는 메시지가 표시되면 **되돌리기** 를 다시 클릭하고 **페이지 찾아보기** 를 클릭합니다.
1. **프로젝트 디자인** 창으로 돌아와 변경 내용 되돌리기가 정상적으로 완료되었는지 확인합니다.

    >**참고**: 이제 프로젝트 Wiki에 다른 페이지를 추가하고 Wiki 홈 페이지로 설정합니다.

1. **프로젝트 디자인** 창의 왼쪽 아래에서 **새 페이지** 를 클릭합니다.
1. 페이지 편집기 창의 **페이지 제목** 텍스트 상자에 **프로젝트 디자인 개요** 를 입력하고 **저장**, **닫기** 를 차례로 클릭합니다.
1. **프로젝트 디자인** 프로젝트 Wiki 내의 페이지가 나열된 창으로 돌아와 **프로젝트 디자인 개요** 항목을 찾아서 마우스 포인터로 선택한 다음 **프로젝트 디자인** 페이지 항목 위로 끌어서 놓습니다.
1. **프로젝트 디자인 개요** 항목이 최상위 페이지로 목록에 표시되며, Wiki 홈 페이지임을 나타내는 홈 아이콘이 표시되는지 확인합니다.

## <a name="review"></a>검토

이 랩에서는 Azure DevOps에서 Wiki를 만들고 구성했으며, 이 과정에서 Markdown 콘텐츠 관리와 Mermaid 다이어그램 작성을 수행했습니다.
