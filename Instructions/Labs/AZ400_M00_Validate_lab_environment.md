---
lab:
  title: 랩 환경 유효성 검사
  module: 'Module 0: Welcome'
---

# <a name="validate-lab-environment"></a>랩 환경 유효성 검사

# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="instructions"></a>Instructions

> **참고**: **개인 Microsoft 계정** 설정 및 해당 계정에 연결된 활성 Microsoft Azure Pass 구독이 이미 있는 경우 4단계부터 시작합니다.

1. 강사 또는 다른 소스로부터 새로운 **Azure Pass 프로모션 코드**를 받습니다.
2. 프라이빗 브라우저 세션을 사용하여 [https://account.microsoft.com](https://account.microsoft.com)에서 새로운 **개인 MSA(Microsoft 계정)** 을 받습니다.
3. 같은 브라우저 세션을 사용하여 [https://www.microsoftazurepass.com](https://www.microsoftazurepass.com)으로 이동하고 MSA(Microsoft 계정)를 사용하여 Azure Pass를 상환합니다. 자세한 내용은 [Microsoft Azure Pass 교환](https://www.microsoftazurepass.com/Home/HowTo?Length=5)을 참조하세요. 교환을 위한 지침을 따릅니다.

4. 브라우저를 열고 [https://portal.azure.com](https://portal.azure.com)으로 이동한 다음, Azure 포털 화면 위에서 **Azure DevOps**를 검색합니다. 최종 페이지에서 **Azure DevOps 조직**을 클릭합니다.
5. 그런 다음 **내 Azure DevOps 조직**이라고 표시된 링크를 클릭하거나 [https://aex.dev.azure.com](https://aex.dev.azure.com)으로 바로 이동합니다.
6. On the **몇 가지 추가 정보를 입력해 주세요** 페이지에서 **계속**을 선택합니다.
7. 왼쪽의 드롭다운 상자에서 “Microsoft 계정” 대신 **기본 디렉터리**를 선택합니다.
8. 메시지가 표시되면(“몇 가지 추가 정보를 입력해 주세요”) 이름, 메일 주소 및 위치를 입력하고 **계속**을 클릭합니다.
9. **기본 디렉터리**를 선택한 상태로 [https://aex.dev.azure.com](https://aex.dev.azure.com)으로 돌아가서 파란색 **새 조직 만들기** 단추를 클릭합니다.
10. **계속**을 클릭하여 *서비스 약관*에 동의합니다.
11. 메시지가 표시되면(“거의 완료됨”) Azure DevOps 조직 이름은 기본값으로 유지하고(전역적으로 고유한 이름이어야 함) 목록에서 가까운 호스트 위치를 선택합니다.
12. **Azure DevOps**에서 새로 만든 조직이 열리면 왼쪽 아래에서 **조직 설정**을 클릭합니다.
13. **조직 설정** 화면에서 **청구**를 클릭합니다(이 화면이 열리기까지 몇 초 정도 걸림).
14. **청구 설정**을 클릭하고 화면 오른쪽에서 **Azure Pass - 스폰서쉽** 구독을 선택합니다. 그런 다음 **저장**을 클릭하여 해당 구독과 조직을 연결합니다.
15. 화면 위쪽에 연결된 Azure Subscription ID가 표시되면 **MS Hosted CI/CD**의 **Paid parallel jobs**의 수를 0에서 **1**로 변경합니다. 그런 다음 아래쪽에 있는 **저장** 단추를 클릭합니다.
16. **조직 설정**에서 **보안** 섹션으로 이동하여 **정책**을 클릭합니다.
17. **OAuth를 통해 타사 애플리케이션 액세스**를 **켜기**로 전환합니다.
    > 참고: OAuth 설정으로 DemoDevOpsGenerator와 같은 도구에서 확장을 등록할 수 있습니다. 이 기능이 없으면 필요한 확장이 부족하여 일부 랩이 실패할 수 있습니다.
18. **공개 프로젝트 허용**을 위해 **켜기**로 전환합니다.
    > 참고: 일부 랩에서 사용되는 확장은 무료 버전을 사용할 수 있는 공개 프로젝트가 필요합니다.
19. 백 엔드에 새로운 설정이 반영되도록 **3시간 기다린 후에 CI/CD 기능을 사용합니다**. 그렇지 않으면 “구입되거나 부여된 호스팅된 병렬 처리가 없습니다”라는 메시지가 계속 표시됩니다.
