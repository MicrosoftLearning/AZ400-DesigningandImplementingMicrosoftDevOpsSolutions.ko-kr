---
lab:
  title: '랩 00: 랩 환경 유효성 검사'
  module: 'Module 0: Welcome'
ms.openlocfilehash: d5886a54ef4531d68bfa5da28ba2542ddb0cd463
ms.sourcegitcommit: 08d43004a29343fed5b35bf6819ba38b074201f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2022
ms.locfileid: "139687274"
---
# <a name="lab-00-validate-lab-environment"></a>랩 00: 랩 환경 유효성 검사
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="instructions"></a>Instructions

1. 강사 또는 다른 소스로부터 새로운 **Azure Pass 프로모션 코드**(30일간 유효)를 받습니다.
2. 프라이빗 브라우저 세션을 사용하여 [Microsoft account](https://account.microsoft.com)에서 새로운 **MSA(Microsoft 계정)** 를 받거나 기존 MSA를 사용합니다.
3. 같은 브라우저 세션을 사용하여 [Microsoft Azure Pass](https://www.microsoftazurepass.com)로 이동하고 MSA(Microsoft 계정)를 사용하여 Azure Pass를 상환합니다. 자세한 내용은 [Microsoft Azure Pass 교환](https://www.microsoftazurepass.com/Home/HowTo?Length=5)을 참조하세요. 교환을 위한 지침을 따릅니다. 
4. 동일한 브라우저 세션을 사용하여 [Microsoft Azure](https://portal.azure.com)로 이동한 다음 포털 화면 맨 위에서 **Azure DevOps** 를 검색합니다. 최종 페이지에서 **Azure DevOps 조직** 을 클릭합니다. 
5. 그런 다음 **내 Azure DevOps 조직** 이라고 표시된 링크를 클릭하거나 [내 정보](https://aex.dev.azure.com)로 바로 이동합니다.
6. On the **몇 가지 추가 정보를 입력해 주세요** 페이지에서 **계속** 을 선택합니다.
7. 왼쪽의 드롭다운 상자에서 “Microsoft 계정” 대신 **기본 디렉터리** 를 선택합니다.
8. 메시지가 표시되면(“몇 가지 추가 정보를 입력해 주세요”) 이름, 메일 주소 및 위치를 입력하고 **계속** 을 클릭합니다.
9. **기본 디렉터리** 를 선택한 상태로 [내 정보](https://aex.dev.azure.com)로 돌아가서 파란색 **새 조직 만들기** 단추를 클릭합니다.
10. **계속** 을 클릭하여 *서비스 약관* 에 동의합니다.
11. 메시지가 표시되면(“거의 완료됨”) Azure DevOps 조직 이름은 기본값으로 유지하고(전역적으로 고유한 이름이어야 함) 목록에서 가까운 호스트 위치를 선택합니다.
12. **Azure DevOps** 에서 새로 만든 조직이 열리면 왼쪽 아래에서 **조직 설정** 을 클릭합니다.
13. **조직 설정** 화면에서 **청구** 를 클릭합니다(이 화면이 열리기까지 몇 초 정도 걸림).
14. **청구 설정** 을 클릭하고 화면 오른쪽에서 **Azure Pass - 스폰서쉽** 구독을 선택합니다. 그런 다음 **저장** 을 클릭하여 해당 구독과 조직을 연결합니다.
15. 화면 위쪽에 연결된 Azure Subscription ID가 표시되면 **MS Hosted CI/CD** 의 **Paid parallel jobs** 의 수를 0에서 **1** 로 변경합니다. 그런 다음 아래쪽에 있는 **저장** 단추를 클릭합니다. 
16. 백 엔드에 새로운 설정이 반영되도록 **3시간 기다린 후에 CI/CD** 기능을 사용합니다. 그렇지 않으면 *“최대 요청 수에 도달했기 때문에 이 에이전트가 실행되지 않습니다..."* 메시지가 계속 표시됩니다.
17. 조직 설정에서 보안 -> **정책** 으로 이동합니다.
18. “OAuth를 통한 타사 애플리케이션 액세스”를 **설정합니다**.
19. “공용 프로젝트 허용”을 **설정합니다**.
20. 선택 사항: 빌드 파이프라인을 만들고 트리거하여 이 새 설정이 정상적으로 적용되었는지 확인할 수 있습니다. 이렇게 하려면 강사에게 말하거나 [Azure DevOps 데모 생성기 도구](https://azuredevopsdemogenerator.azurewebsites.net)를 사용하여 청구 사용이 설정된 새로 만든 조직에 데모 프로젝트를 만듭니다.
