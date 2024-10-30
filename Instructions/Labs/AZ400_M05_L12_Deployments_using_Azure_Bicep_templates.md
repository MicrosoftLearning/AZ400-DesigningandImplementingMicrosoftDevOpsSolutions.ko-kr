---
lab:
  title: Azure Bicep 템플릿을 사용한 배포
  module: 'Module 05: Manage infrastructure as code using Azure and DSC'
---

# Azure Bicep 템플릿을 사용한 배포

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독의 소유자 역할, 그리고 Azure 구독과 연결된 Microsoft Entra 테넌트의 전역 관리자 역할이 지정되어 있는 Microsoft 계정 또는 Microsoft Entra 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

## 랩 개요

이 랩에서는 Azure Bicep 템플릿을 만들고 Azure Bicep 모듈 개념을 사용하여 모듈화합니다. 그런 다음, 모듈을 사용하도록 기본 배포 템플릿을 수정하고 마지막으로 모든 리소스를 Azure에 배포합니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure Bicep 템플릿의 구조 이해
- 재사용 가능한 Bicep 모듈 만들기
- 모듈을 사용하도록 기본 템플릿을 수정합니다.
- Azure YAML 파이프라인을 사용하여 Azure에 모든 리소스 배포

## 예상 소요 시간: 45분

## 지침

### 연습 0: (완료되면 건너뛰기) 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 다른 필드는 기본값으로 유지합니다. **만들기**를 클릭합니다.

    ![새 프로젝트 만들기 패널의 스크린샷.](images/create-project.png)

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **리포지토리 가져오기**를 클릭합니다. **가져오기**를 선택합니다. **Git 리포지토리 가져오기** 창에서 다음 URL <https://github.com/MicrosoftLearning/eShopOnWeb.git> 을 붙여넣고 **가져오기**를 클릭합니다.

    ![리포지토리 가져오기 패널의 스크린샷.](images/import-repo.png)

1. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - **.devcontainer** 폴더 컨테이너 설정을 통해 컨테이너를 사용하여 개발합니다(VS Code 또는 GitHub Codespaces에서 로컬로).
    - **infra** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep 및 ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 8 웹 사이트가 포함되어 있습니다.

#### 작업 3: (완료된 경우 건너뛰기) 기본(main) 분기를 기본 분기로 설정

1. **Repos > 분기**로 이동합니다.
1. **기본** 분기를 마우스로 가리킨 다음 열 오른쪽에 있는 줄임표를 클릭합니다.
1. **기본 분기로 설정**을 클릭합니다.

### 연습 1: Azure Bicep 템플릿 이해 및 재사용 가능한 모듈을 사용하여 간소화

이 랩에서는 Azure Bicep 템플릿을 검토하고 재사용 가능한 모듈을 사용하여 간소화합니다.

#### 작업 1: Azure Bicep 템플릿 만들기

이 작업에서는 Visual Studio Code를 사용하여 Azure Bicep 템플릿을 만듭니다.

1. 브라우저 탭에서 Azure DevOps 프로젝트를 열고 **리포지토리** 및 **파일**로 이동합니다. `infra` 폴더를 열고 `simple-windows-vm.bicep` 파일을 찾습니다.

   ![simple-windows-vm.bicep 파일 경로의 스크린샷.](./images/m06/browsebicepfile.png)

1. 템플릿을 검토하여 구조를 자세히 파악합니다. 형식이 포함된 일부 매개 변수, 기본값 및 유효성 검사, 일부 변수, 그리고 다음 형식의 리소스가 상당수 있습니다.

   - Microsoft.Storage/storageAccounts
   - Microsoft.Network/publicIPAddresses
   - Microsoft.Network/virtualNetworks
   - Microsoft.Network/networkInterfaces
   - Microsoft.Compute/virtualMachines

1. 리소스 정의가 얼마나 간단한지, 그리고 템플릿 전반에서 명시적 `dependsOn` 대신 기호 이름을 암시적으로 참조하는 기능에 주의하세요.

#### 작업 2: 스토리지 리소스에 대한 bicep 모듈 만들기

이 작업에서는 스토리지 계정만 만들고 기본 템플릿을 통해 가져오는 스토리지 템플릿 모듈 **storage.bicep**을 만듭니다. 스토리지 템플릿 모듈은 기본 템플릿 **main.bicep**에 값을 다시 전달해야 하며 이 값은 스토리지 템플릿 모듈의 출력 요소에 정의됩니다.

1. 먼저 기본 템플릿에서 스토리지 리소스를 제거해야 합니다. 브라우저 창의 오른쪽 위 모서리에서 **편집** 단추를 클릭합니다.

   ![파이프라인 편집 버튼의 스크린샷.](./images/m06/edit.png)

1. 이제 스토리지 리소스를 삭제합니다.

   ```bicep
   resource storageAccount 'Microsoft.Storage/storageAccounts@2022-05-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: 'Standard_LRS'
     }
     kind: 'Storage'
   }
   ```

1. 파일을 커밋합니다. 아직 완료된 것은 아닙니다.

   ![파일 커밋 버튼 스크린샷.](./images/m06/commit.png)

1. 그런 다음 `Infra` 폴더 위에 마우스를 놓고 줄임표 아이콘을 클릭한 후 **새로 만들기** 및 **파일**을 선택합니다. 이름에 **`storage.bicep`** 입력 후 **만들기**를 클릭합니다.

   ![새 파일 메뉴의 스크린샷.](./images/m06/newfile.png)

1. 이제 다음 코드 조각을 파일에 복사하고 변경 사항을 커밋합니다.

   ```bicep
   @description('Location for all resources.')
   param location string = resourceGroup().location

   @description('Name for the storage account.')
   param storageAccountName string

   resource storageAccount 'Microsoft.Storage/storageAccounts@2022-05-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: 'Standard_LRS'
     }
     kind: 'Storage'
   }

   output storageURI string = storageAccount.properties.primaryEndpoints.blob
   ```

#### 작업 3: 템플릿 모듈을 사용하도록 기본 템플릿 수정하기

이 작업에서는 이전 작업에서 만든 템플릿 모듈을 참조하도록 기본 템플릿을 수정합니다.

1. `simple-windows-vm.bicep` 파일로 다시 이동하고 **편집** 단추를 한 번 더 클릭합니다.

1. 다음으로 변수 뒤에 다음 코드를 추가합니다.

   ```bicep
   module storageModule './storage.bicep' = {
     name: 'linkedTemplate'
     params: {
       location: location
       storageAccountName: storageAccountName
     }
   }
   ```

1. 또한 모듈의 출력을 대신 사용하도록 가상 머신 리소스의 스토리지 계정 Blob URI에 대한 참조를 수정해야 합니다. 가상 머신 리소스를 찾고 diagnosticsProfile 섹션을 다음으로 바꿉니다.

   ```bicep
   diagnosticsProfile: {
     bootDiagnostics: {
       enabled: true
       storageUri: storageModule.outputs.storageURI
     }
   }
   ```

1. 기본 템플릿에서 다음 세부 정보를 검토합니다.

   - 기본 템플릿의 모듈은 다른 템플릿에 연결하는 데 사용됩니다.
   - 모듈에는 `storageModule`이라는 기호 이름이 있습니다. 이 이름은 모든 종속성의 구성에 사용됩니다.
   - 템플릿 모듈을 사용할 때는 **증분** 배포 모드만 사용할 수 있습니다.
   - 상대 경로는 템플릿 모듈에 사용됩니다.
   - 매개 변수를 사용하여 기본 템플릿의 값을 템플릿 모듈로 전달합니다.

1. 템플릿을 커밋합니다.

### 연습 2: YAML 파이프라인을 사용하여 Azure에 템플릿 배포하기

이 에서는 Azure DevOps YAML 파이프라인을 사용하여 템플릿을 Azure 환경에 배포합니다.

#### 작업 1: YAML 파이프라인으로 Azure에 리소스 배포

1. **Pipelines** 허브의 **파이프라인** 창으로 다시 이동합니다.
1. **첫 번째 파이프라인 만들기** 창에서 **파이프라인 만들기**를 클릭합니다.

    > **참고**: 여기서는 마법사를 사용하여 프로젝트를 기준으로 YAML 파이프라인 정의를 만듭니다.

1. **코드 위치** 창에서 **Azure Repos Git(YAML)** 옵션을 클릭합니다.
1. **리포지토리 선택** 창에서 **eShopOnWeb**을 클릭합니다.
1. **파이프라인 구성** 창에서 아래로 스크롤하여 **기존 Azure Pipelines YAML 파일**을 선택합니다.
1. **기존 YAML 파일 선택** 블레이드에서 다음 매개 변수를 지정합니다.
   - 분기: **main**
   - 경로: **.ado/eshoponweb-cd-windows-cm.yml**
1. **계속**을 클릭하여 이러한 설정을 저장합니다.
1. 변수 섹션에서 리소스 그룹의 이름을 선택하고 원하는 위치를 설정한 다음 서비스 연결 값을 이전에 만든 기존 서비스 연결 중 하나로 바꿉니다.
1. 오른쪽 상단의 **저장 및 실행** 단추를 클릭하고 커밋 대화 상자가 나타나면 **저장 및 실행**을 다시 클릭합니다.

   ![저장 후 실행 버튼의 스크린샷.](./images/m06/saveandrun.png)

1. 배포가 완료될 때까지 기다린 후 결과를 검토합니다.
   ![YAML 파이프라인을 사용하여 Azure에 리소스를 성공적으로 배포한 스크린샷.](./images/m06/deploy.png)

   > [!IMPORTANT]
   > 불필요한 요금이 부과되지 않도록 Azure Portal에서 만든 리소스를 삭제하는 것을 잊지 마세요.

## 검토

이 랩에서는 Azure Bicep 템플릿을 만들고, 템플릿 모듈을 사용하여 모듈화하고, 모듈을 사용하도록 기본 배포 템플릿을 수정하고, 종속성을 업데이트하고, 마지막으로 YAML 파이프라인을 사용하여 Azure에 템플릿을 배포하는 방법을 알아보았습니다.
