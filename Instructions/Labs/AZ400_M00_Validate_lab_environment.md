---
lab:
  title: 랩 환경 유효성 검사
  module: 'Module 0: Welcome'
---

# 랩 환경 유효성 검사

## 학생용 랩 매뉴얼

## Azure DevOps 조직을 만들기 위한 지침(한 번만 수행하면 됩니다).

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

## 샘플 Azure DevOps Project를 만드는 지침(한 번만 수행하면 됩니다).

### 연습 0: 랩 필수 구성 요소 구성

> **참고**: 이러한 단계를 계속하기 전에 Azure DevOps 조직을 만드는 단계를 완료했는지 확인합니다.

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트에 다음 설정을 지정합니다.
    - name: **eShopOnWeb**
    - 표시 유형: **프라이빗**
    - 고급: 버전 제어: **Git**
    - 고급: 작업 항목 프로세스: **스크럼**

2. **만들기**를 클릭합니다.

    ![프로젝트 만들기](images/create-project.png)

#### 작업 2: eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **리포지토리 가져오기**를 클릭합니다. **가져오기**를 선택합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git 을 붙여넣고 **가져오기**를 클릭합니다.

    ![리포지토리 가져오기](images/import-repo.png)

2. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용하여 개발하는 **.devcontainer** 폴더 컨테이너 설정(VS Code 또는 GitHub Codespaces에서 로컬로).
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

이제 이 AZ-400 과정에 대한 다양한 개별 랩을 계속 진행하는 데 필요한 필수 구성 요소 단계를 완료했습니다.
