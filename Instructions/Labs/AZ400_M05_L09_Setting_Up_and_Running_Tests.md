---
lab:
  title: 테스트 설정 및 실행
  module: 'Module 05: Implement a secure continuous deployment using Azure Pipelines'
---

# 테스트 설정 및 실행

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://learn.microsoft.com/dotnet/architecture/modern-web-apps-azure/test-asp-net-core-mvc-apps)에서 제공되는 지침에 따라 조직을 만듭니다.

## 랩 개요

복잡한 소프트웨어는 변경에 대응하여 예기치 않은 방식으로 실패할 수 있습니다. 따라서 변경 후 테스트는 가장 사소한(또는 가장 중요하지 않은) 애플리케이션을 제외한 모든 애플리케이션에 필요합니다. 수동 테스트는 소프트웨어를 테스트하는 데 있어 속도가 가장 느리고, 안정성이 가장 낮고, 비용이 가장 많이 드는 방법입니다.

소프트웨어 애플리케이션에 대한 자동화된 테스트에는 여러 종류가 있습니다. 가장 간단하고 가장 낮은 수준의 테스트는 단위 테스트입니다. 약간 더 높은 수준에서 통합 테스트와 기능 테스트가 있습니다. UI 테스트, 부하 테스트, 스트레스 테스트 및 스모크 테스트와 같은 다른 종류의 테스트는 이 랩의 scope.

*다양한 유형의 테스트에 대해 자세히 알아보려면 [MVC 앱 테스트 ASP.NET Core](https://learn.microsoft.com/dotnet/architecture/modern-web-apps-azure/test-asp-net-core-mvc-apps) 문서를 읽는 것이 좋습니다.*

## 목표

이 랩을 완료하면 다음을 포함하는 .Net 애플리케이션에 대한 CI 파이프라인을 구성할 수 있습니다.

- 단위 테스트
- 통합 테스트
- 기능 테스트

## 예상 소요 시간: 60분

## Instructions

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 다른 필드는 기본값으로 유지합니다. **만들기**를 클릭합니다.

#### 작업 2: (완료되면 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **리포지토리 가져오기**를 클릭합니다. **가져오기**를 선택합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git 을 붙여넣고 **가져오기**를 클릭합니다.

1. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용하여 개발하는 **.devcontainer** 폴더 컨테이너 설정(VS Code 또는 GitHub Codespaces에서 로컬로).
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep & ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더에는 YAML GitHub 워크플로 정의가 포함되어 있습니다.
    - **src** 폴더에는 랩 시나리오에 사용되는 .NET 웹 사이트가 포함되어 있습니다.

### 연습 1: CI 파이프라인에서 테스트 설정

이 연습에서는 CI 파이프라인에서 테스트를 설치합니다.

#### 작업 1: (완료되면 건너뛰기) CI에 대한 YAML 빌드 정의 가져오기

이 작업에서는 연속 통합을 구현하는 데 사용할 YAML 빌드 정의를 추가합니다.

먼저 [eshoponweb-ci.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml)이라는 CI 파이프라인을 가져오겠습니다.

1. **파이프라인>파이프라인으로** 이동합니다.
1. **새 파이프라인** 단추를 클릭합니다.
1. **Azure Repos Git(YAML)을** 선택합니다.
1. **eShopOnWeb** 리포지토리를 선택합니다.
1. **기존 Azure Pipelines YAML 파일을** 선택합니다.
1. **/.ado/eshoponweb-ci.yml** 파일을 선택한 다음 **계속**을 클릭합니다.

    CI 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **DotNet 복원**: NuGet 패키지 복원을 사용하면 소스 제어에 저장하지 않고도 프로젝트의 모든 종속성을 설치할 수 있습니다.
    - **DotNet Build**: 프로젝트 및 모든 종속성을 빌드합니다.
    - **DotNet 테스트**: 단위 테스트를 실행하는 데 사용되는 .Net 테스트 드라이버입니다.
    - **DotNet 게시**: 호스팅 시스템에 배포하기 위해 애플리케이션 및 해당 종속성을 폴더에 게시합니다. 이 경우 **Build.ArtifactStagingDirectory입니다**.
    - **아티팩트 게시 - 웹 사이트**: 앱 아티팩트(이전 단계에서 만든)를 게시하고 파이프라인 아티팩트로 사용할 수 있도록 합니다.
    - **아티팩트 게시 - Bicep**: 인프라 아티팩트(Bicep 파일)를 게시하고 파이프라인 아티팩트로 사용할 수 있도록 합니다.
1. **저장** 단추(**저장 및 실행** 아님)를 클릭하여 파이프라인 정의를 저장합니다.

#### 작업 2: CI 파이프라인에 테스트 추가

이 작업에서는 CI 파이프라인에 통합 및 기능 테스트를 추가합니다.

단위 테스트 작업이 이미 파이프라인의 일부임을 알 수 있습니다.

- **단위 테스트는** 애플리케이션 논리의 단일 부분을 테스트합니다. 아닌 항목 중 일부를 나열하여 자세히 설명할 수 있습니다. 단위 테스트는 코드에서 종속성 또는 인프라와 작동하는 방식을 테스트하지 않으므로 통합 테스트가 필요합니다.

1. 이제 단위 테스트 작업 후에 통합 테스트 작업을 추가해야 합니다.

    ```YAML
    - task: DotNetCoreCLI@2
      displayName: Integration Tests
      inputs:
        command: 'test'
        projects: 'tests/IntegrationTests/*.csproj'
    ```

    > **통합 테스트는 코드가** 종속성 또는 인프라에서 작동하는 방식을 테스트합니다. 데이터베이스 및 파일 시스템과 같은 인프라와 상호 작용하는 코드를 캡슐화하는 것이 좋지만, 여전히 해당 코드 중 일부를 사용할 수 있고 이를 테스트하려고 할 수도 있습니다. 또한 애플리케이션의 종속성이 완전히 해결되면 코드 계층이 예상대로 상호 작용하는지도 확인해야 합니다. 이 기능은 통합 테스트에서 수행해야 합니다.

1. 그런 다음, 통합 테스트 작업 후에 기능 테스트 작업을 추가해야 합니다.

    ```YAML
    - task: DotNetCoreCLI@2
      displayName: Functional Tests
      inputs:
        command: 'test'
        projects: 'tests/FunctionalTests/*.csproj'
    ```

    > **기능 테스트는** 사용자의 관점에서 작성되며 요구 사항에 따라 시스템의 정확성을 확인합니다. 개발자의 관점에서 작성된 통합 테스트와 달리 시스템의 일부 구성 요소가 올바르게 함께 작동하는지 확인합니다.

16. **저장**을 클릭하고 **저장** 창에서 **저장**을 다시 클릭하여 변경 내용을 기본 분기에 직접 커밋합니다.

#### 작업 4: 테스트 요약 확인

1. **실행을** 클릭한 다음 **파이프라인 실행** 탭에서 **실행을** 다시 클릭합니다.

1. 파이프라인이 시작될 때까지 기다렸다가 빌드 스테이지가 성공적으로 완료될 때까지 기다립니다.

1. 완료되면 **테스트** 탭이 파이프라인 실행의 일부로 표시됩니다. 요약을 검사 클릭하여 클릭합니다. 아래와 같습니다.

    ![테스트 요약](images/AZ400_M05_L09_Tests_Summary.png)

1. 자세한 내용은 페이지 아래쪽에 다른 실행 테스트 목록을 보여 줍니다.

    >**참고**: 테이블이 비어 있는 경우 테스트 실행에 대한 모든 세부 정보를 갖도록 필터를 다시 설정해야 합니다.

    ![테스트 테이블](images/AZ400_M05_L09_Tests_Table.png)

## 검토

이 랩에서는 Azure Pipelines 및 .Net을 사용하여 다양한 테스트 유형을 설정 및 실행하는 방법을 알아보았습니다.
