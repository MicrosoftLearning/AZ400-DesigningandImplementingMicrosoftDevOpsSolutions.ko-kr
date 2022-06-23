---
lab:
  title: '랩 15: Azure 파이프라인에서 보안 및 규정 준수 구현'
  module: 'Module 07: Implement security and validate code bases for compliance'
ms.openlocfilehash: cdf7c9baa30b1fb2ca27662295dff72430307cc1
ms.sourcegitcommit: d78aebd7b14277a53f152e26cea68a30b0e90d73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2022
ms.locfileid: "146276175"
---
# <a name="lab-15-implement-security-and-compliance-in-an-azure-pipeline"></a>랩 15: Azure 파이프라인에서 보안 및 규정 준수 구현

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-requirements"></a>랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

## <a name="lab-overview"></a>랩 개요

이 랩에서는 **WhiteSource Bolt와 Azure DevOps** 를 사용하여 취약한 오픈 소스 구성 요소, 오래된 라이브러리 및 코드의 라이선스 규정 준수 문제를 자동으로 검색합니다. 그리고 흔히 발생하는 웹 애플리케이션 보안 문제를 재현하도록 설계된 WebGoat를 사용합니다. OWASP에서 유지 관리하는 이 웹 애플리케이션은 의도적으로 안전하지 않게 제작되었습니다.

지속적인 오픈 소스 소프트웨어 보안 및 규정 준수 관리 분야에서 가장 유명한 플랫폼인 [WhiteSource](https://www.whitesourcesoftware.com/)는 어떤 프로그래밍 언어, 빌드 도구, 개발 환경을 사용하든 관계없이 빌드 프로세스에 통합할 수 있습니다. 또한 백그라운드에서 계속해서 자동으로 작동하면서 오픈 소스 구성 요소의 보안, 라이선스 및 품질을 오픈 소스 리포지토리의 확정 데이터베이스와 대조하여 확인합니다. 이 데이터베이스는 WhiteSource에서 지속적으로 업데이트되고 있습니다.

WhiteSource는 Azure DevOps와 통합을 위해 특별히 개발된 간단한 오픈 소스 보안 및 관리 솔루션인 WhiteSource Bolt를 제공합니다.

> **참고**: WhiteSource Bolt는 프로젝트별로 작동하며 실시간 경고 기능은 제공하지 않습니다. 실시간 경고 기능을 사용하려면 **전체 플랫폼** 이 필요합니다.

WhiteSource Bolt는 일반적으로 전체 소프트웨어 개발 수명 주기에서(리포지토리에서 배포 후 단계까지) 또한 모든 프로젝트 및 제품에서 오픈 소스 관리를 자동화하려는 대규모 개발 팀에 권장됩니다.

Azure DevOps와 WhiteSource Bolt를 통합하는 경우의 이점은 다음과 같습니다.

- 취약한 오픈 소스 구성 요소를 감지하고 해결합니다.
- 프로젝트 또는 빌드당 포괄적인 오픈 소스 인벤토리 보고서를 생성합니다.
- 종속성의 라이선스를 포함하여 오픈 소스 라이선스 준수를 적용합니다.
- 업데이트할 권장 사항을 사용하여 오래된 오픈 소스 라이브러리를 식별합니다.

## <a name="objectives"></a>목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- WhiteSource Bolt 활성화.
- 빌드 파이프라인을 실행하고 WhiteSource 보안 및 규정 준수 보고서 검토.

## <a name="estimated-timing-45-minutes"></a>예상 소요 시간: 45분

## <a name="instructions"></a>지침

### <a name="exercise-0-configure-the-lab-prerequisites"></a>연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [Parts Unlimited MRP GitHub 리포지토리](https://www.github.com/microsoft/partsunlimitedmrp)를 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### <a name="task-1-create-and-configure-the-team-project"></a>작업 1: 팀 프로젝트 만들기 및 구성

이 작업에서는 Azure DevOps 데모 생성기를 사용하여 [WhiteSource Bolt 템플릿](https://azuredevopsdemogenerator.azurewebsites.net/?name=WhiteSource-Bolt&templateid=77362)을 기반으로 새 프로젝트를 생성합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure DevOps 데모 생성기](https://azuredevopsdemogenerator.azurewebsites.net)로 이동합니다. 이 유틸리티 사이트에서는 계정 내에서 새 Azure DevOps 프로젝트를 만드는 프로세스를 자동으로 진행할 수 있습니다. 이 프로젝트에는 랩에 필요한 작업 항목, 리포지토리 등의 콘텐츠가 미리 입력되어 있습니다.

    > **참고**: 사이트에 대한 자세한 내용은 <https://docs.microsoft.com/en-us/azure/devops/demo-gen> 을 참조하세요.

1. **로그인** 을 클릭하고 Azure DevOps 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
1. 필요한 경우 **Azure DevOps 데모 생성기** 페이지에서 **수락** 을 클릭하여 Azure DevOps 구독에 액세스하는 데 필요한 권한 요청을 수락합니다.
1. **새 프로젝트 만들기** 페이지의 **새 프로젝트 이름** 텍스트 상자에 **WhiteSource Bolt** 를 입력합니다. 그런 다음 **조직 선택** 드롭다운 목록에서 Azure DevOps 조직을 선택하고 **템플릿 선택** 을 클릭합니다.
1. 템플릿 목록의 도구 모음에서 **DevOps 랩** 을 클릭하고 **WhiteSource Bolt** 템플릿을 선택한 다음 **템플릿 선택** 을 클릭합니다.
1. **새 프로젝트 만들기** 페이지로 돌아와서 누락된 확장을 설치하라는 메시지가 표시되면 **WhiteSource Bolt** 아래의 체크박스를 선택하고 **프로젝트 만들기** 를 클릭합니다.

    > **참고**: 프로세스가 완료될 때까지 기다립니다. 이 작업은 2분 정도 걸립니다. 프로세스가 실패하면 DevOps 조직으로 이동하여 프로젝트를 삭제한 후에 다시 시도합니다.

1. **새 프로젝트 만들기** 페이지에서 **프로젝트로 이동** 을 클릭합니다.

### <a name="exercise-1-implement-security-and-compliance-in-an-azure-pipeline-using-whitesource-bolt"></a>연습 1: WhiteSource Bolt를 사용하여 Azure 파이프라인에서 보안 및 규정 준수 구현

이 연습에서는 WhiteSource Bolt를 활용하여 프로젝트 코드를 검사해 보안 취약성 및 라이선스 규정 준수 문제가 있는지를 파악하고 결과로 작성된 보고서를 확인합니다.

#### <a name="task-1-activate-whitesource-bolt"></a>작업 1: WhiteSource Bolt 활성화

이 작업에서는 새로 생성된 Azure DevOps 프로젝트에서 WhiteSource Bolt를 활성화합니다.

1. 랩 컴퓨터에서 **WhiteSource Bolt** 프로젝트가 열려 있는 Azure DevOps 포털이 표시된 웹 브라우저 창 내의 Azure DevOps 포털 맨 왼쪽 **세로 메뉴 모음에서** **Pipelines** 섹션을 클릭하고 **WhiteSource Bolt** 옵션("개발 그룹" 옵션 아래 세로 메뉴 모음에 있음)을 클릭합니다.
1. **거의 완료** 창에서 **회사 이메일** 및 **회사 이름** 을 입력하고 **국가** 드롭다운 목록에서 사용자의 국가를 나타내는 항목을 선택합니다. 그런 다음 *시작* 단추를 클릭하면 WhiteSource Bolt *무료* 버전 사용을 시작할 수 있습니다. 그러면 새 브라우저 탭이 자동으로 열리고 **Bolt 시작** 페이지가 표시됩니다.
1. Azure DevOps 포털이 표시된 웹 브라우저 탭으로 다시 전환한 다음 이동 경로를 탐색하여 **WhiteSource Bolt의 무료 버전 사용 중** 이 표시되는지 확인합니다.

#### <a name="task-2-trigger-a-build"></a>작업 2: 빌드 트리거

이 작업에서는 Java 코드 기반 Azure DevOps 프로젝트 내에서 빌드를 트리거합니다. 그리고 **WhiteSource Bolt** 확장을 사용하여 이 코드에 취약한 구성 요소가 있는지 확인합니다.

1. 랩 컴퓨터의 세로 메뉴 모음에서 **파이프라인** 섹션으로 이동하여 **WhileSourceBolt** 을 클릭하고 **파이프라인 실행** 을 클릭한 다음 **파이프라인 실행** 창에서 **실행** 을 클릭합니다.
1. 빌드 창의 **요약** 탭 **작업** 섹션에서 **1단계** 를 클릭하고 빌드 프로세스 진행률을 모니터링합니다.

    > **참고**: 이 빌드를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 빌드 정의는 다음과 같은 작업으로 구성되어 있습니다.

    | 작업 | 사용 |
    | ---- | ------ |
    | ![npm](images/m07/npm.png) **npm** |  빌드에 필요한 npm 패키지를 설치하고 게시합니다. |
    | ![maven](images/m07/maven.png) **Maven** |  제공된 pom xml 파일을 사용하여 Java 코드를 작성합니다. |
    | ![whitesourcebolt](images/m07/whitesourcebolt.png) **WhiteSource Bolt** |  제공된 작업 디렉터리/루트 디렉터리에서 코드를 검사하여 보안 취약성과 문제가 있는 오픈 소스 라이선스를 검색합니다. |
    | ![copy-files](images/m07/copy-files.png) **파일 복사** |  생성된 JAR 파일을 일치 패턴을 사용하여 원본 폴더에서 대상 폴더로 복사합니다. |
    | ![publish-build-artifacts](images/m07/publish-build-artifacts.png) **빌드 아티팩트 게시** |  빌드에서 생성된 아티팩트를 게시합니다. |

1. 빌드가 완료되면 **요약** 탭으로 다시 이동하여 **테스트 및 검사** 섹션을 검토합니다.

#### <a name="task-3-analyze-reports"></a>작업 3: 보고서 분석

이 작업에서는 WhiteSource Bolt 빌드 보고서를 검토합니다.

1. 빌드 창에서 **WhiteSource Bolt 빌드 보고서** 탭 머리글을 클릭하고 보고서가 완전히 렌더링될 때까지 기다립니다.
1. **WhiteSource Bolt 빌드 보고서** 탭을 표시한 상태로 WhiteSource Bolt가 전이적 종속성과 각 종속성의 라이선스를 비롯하여 소프트웨어의 오픈 소스 구성 요소를 자동으로 검색했는지 확인합니다.
1. **WhiteSource Bolt 빌드 보고서** 탭을 표시한 상태로 빌드 중에 검색된 취약성이 표시된 보안 대시보드를 검토합니다.

    > **참고**: 보고서에는 **취약성 점수**, **취약한 라이브러리**, **심각도 분포** 를 비롯하여 취약한 오픈 소스 구성 요소가 모두 포함된 목록이 표시됩니다. 모든 구성 요소의 상세 보기, 그리고 구성 요소 메타데이터 및 사용이 허가된 참조 자료 링크를 활용하면 오픈 소스 라이선스 배포 현황을 파악할 수 있습니다.

1. **WhiteSource Bolt 빌드 보고서** 탭을 표시한 상태로 아래쪽의 **오래된 라이브러리** 섹션으로 스크롤하여 해당 내용을 검토합니다.

    > **참고**: WhiteSource Bolt는 프로젝트의 오래된 라이브러리를 추적하여 라이브러리 세부 정보, 최신 버전 링크 및 수정 관련 권장 사항을 제공합니다.

## <a name="review"></a>검토

이 랩에서는 **WhiteSource Bolt와 Azure DevOps** 를 사용하여 취약한 오픈 소스 구성 요소, 오래된 라이브러리 및 코드의 라이선스 규정 준수 문제를 자동으로 검색합니다.
