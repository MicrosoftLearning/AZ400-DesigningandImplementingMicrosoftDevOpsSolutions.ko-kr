---
lab:
  title: Azure Bicep 템플릿을 사용한 배포
  module: 'Module 06: Manage infrastructure as code using Azure and DSC'
---

# Azure Bicep 템플릿을 사용한 배포

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

- Azure 구독의 소유자 역할과 Azure 구독과 연결된 Microsoft Entra 테넌트에서 전역 관리자 역할이 있는 Microsoft 계정 또는 Microsoft Entra 계정이 있는지 확인합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당 목록 표시](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) 및 [Azure Active Directory에서 관리자 역할 확인 및 할당](https://docs.microsoft.com/azure/active-directory/roles/manage-roles-portal)을 참조하세요.

## 랩 개요

이 랩에서는 Azure Bicep 템플릿을 만들고 Azure Bicep 모듈 개념을 사용하여 모듈화합니다. 그런 다음 기본 배포 템플릿을 수정하여 모듈을 사용하고 마지막으로 모든 리소스를 Azure에 배포합니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure Bicep 템플릿의 구조를 이해합니다.
- 재사용 가능한 Bicep 모듈을 만듭니다.
- 모듈을 사용하도록 기본 템플릿 수정
- Azure YAML 파이프라인을 사용하여 모든 리소스를 Azure에 배포합니다.

## 예상 소요 시간: 45분

## 지침

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 다른 필드는 기본값으로 유지합니다. **만들기**를 클릭합니다.

    ![프로젝트 만들기](images/create-project.png)

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **리포지토리 가져오기**를 클릭합니다. **가져오기**를 선택합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git 을 붙여넣고 **가져오기**를 클릭합니다.

    ![리포지토리 가져오기](images/import-repo.png)

1. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - 컨테이너를 사용하여 개발하는 **.devcontainer** 폴더 컨테이너 설정(VS Code 또는 GitHub Codespaces에서 로컬로).
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

### 연습 1: Azure Bicep 템플릿 이해 및 재사용 가능한 모듈을 사용하여 간소화

이 랩에서는 Azure Bicep 템플릿을 검토하고 재사용 가능한 모듈을 사용하여 간소화합니다.

#### 작업 1: Azure Bicep 템플릿 만들기

이 작업에서는 Visual Studio Code 사용하여 Azure Bicep 템플릿을 만듭니다.

1. 브라우저 탭에서 Azure DevOps 프로젝트를 열고 **리포지토리** 및 **파일**로 이동합니다. 폴더를 `.azure\bicep` 열고 파일을 찾습니다 `simple-windows-vm.bicep` .

   ![Simple-windows-vm.bicep 파일](./images/m06/browsebicepfile.png)

1. 템플릿을 검토하여 구조를 자세히 파악합니다. 형식, 기본값 및 유효성 검사, 일부 변수 및 이러한 형식의 리소스가 있는 몇 가지 매개 변수가 있습니다.

   - Microsoft.Storage/storageAccounts
   - Microsoft.Network/publicIPAddresses
   - Microsoft.Network/virtualNetworks
   - Microsoft.Network/networkInterfaces
   - Microsoft.Compute/virtualMachines

1. 리소스 정의가 얼마나 간단한지, 템플릿 전체에서 명시적 `dependsOn` 이 아닌 기호 이름을 암시적으로 참조하는 기능에 주의하세요.

#### 작업 2: 스토리지 리소스에 대한 bicep 모듈 만들기

이 작업에서는 스토리지 계정만 만들고 기본 템플릿에서 가져오는 스토리지 템플릿 모듈 **storage.bicep**을 만듭니다. 스토리지 템플릿 모듈은 기본 템플릿인 **기본.bicep**에 값을 다시 전달해야 하며 이 값은 스토리지 템플릿 모듈의 출력 요소에 정의됩니다.

1. 먼저 기본 템플릿에서 스토리지 리소스를 제거해야 합니다. 브라우저 창의 오른쪽 위 모서리에서 **편집** 단추를 클릭합니다.

   ![편집 단추](./images/m06/edit.png)

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

1. 파일을 커밋하지만 아직 완료되지 않았습니다.

   ![파일 커밋](./images/m06/commit.png)

1. 그런 다음 마우스를 bicep 폴더 위로 마우스로 가리키고 줄임표 아이콘을 클릭한 다음 새로 **만들기** 및 **파일을** 선택합니다. 이름으로 **storage.bicep** 을 입력하고 **만들기**를 클릭합니다.

   ![새 파일 메뉴](./images/m06/newfile.png)

1. 이제 다음 코드 조각을 파일에 복사하고 변경 내용을 커밋합니다.

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

#### 작업 3: 템플릿 모듈을 사용하도록 기본 템플릿 수정

이 작업에서는 이전 작업에서 만든 템플릿 모듈을 참조하도록 기본 템플릿을 수정합니다.

1. 파일로 `simple-windows-vm.bicep` 다시 이동하고 **편집** 단추를 다시 클릭합니다.

1. 다음으로 변수 다음에 다음 코드를 추가합니다.

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
   - 모듈에는 라는 기호 이름이 있습니다 `storageModule`. 이 이름은 모든 종속성을 구성하는 데 사용됩니다.
   - 템플릿 모듈을 사용하는 경우에만 **증분** 배포 모드를 사용할 수 있습니다.
   - 상대 경로는 템플릿 모듈에 사용됩니다.
   - 매개 변수를 사용하여 기본 템플릿의 값을 템플릿 모듈로 전달합니다.

1. 템플릿을 커밋합니다.

### 연습 2: YAML 파이프라인을 사용하여 Azure에 템플릿 배포

이 랩에서는 서비스 연결을 만들고 Azure DevOps YAML 파이프라인에서 사용하여 템플릿을 Azure 환경에 배포합니다.

#### 작업 1: (완료된 경우 건너뛰기) 배포를 위한 서비스 연결 만들기

이 작업에서는 Azure CLI를 사용하여 서비스 주체를 만듭니다. 그러면 Azure DevOps에서 다음을 수행할 수 있습니다.

- Azure 구독에 리소스를 배포합니다.
- 나중에 만든 Key Vault 비밀에 대한 읽기 권한 갖기

> **참고**: 서비스 주체가 이미 있으면 다음 작업을 바로 진행해도 됩니다.

Azure Pipelines에서 Azure 리소스를 배포하려면 서비스 주체가 필요합니다. 여기서는 파이프라인의 비밀을 검색할 것이므로 Azure Key Vault를 만들 때 서비스에 대한 권한을 부여해야 합니다.

프로젝트 설정 페이지에서 새 서비스 연결을 만들거나 파이프라인 정의 내에서 Azure 구독에 연결하면 Azure Pipelines에서 서비스 주체가 자동으로 작성됩니다(자동 옵션). Azure Portal이나 Azure CLI를 통해 서비스 주체를 수동으로 만든 다음 여러 프로젝트에서 재사용할 수도 있습니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고[**, Azure Portal**](https://portal.azure.com)로 이동하고, 이 랩에서 사용할 Azure 구독에서 소유자 역할이 있고 이 구독과 연결된 Microsoft Entra 테넌트에서 전역 관리자 역할이 있는 사용자 계정으로 로그인합니다.
1. Azure Portal의 페이지 위쪽 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
1. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.

   >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다.

1. **Cloud Shell** 창의 **Bash** 프롬프트에서 다음 명령을 실행하여 Azure 구독 ID 및 구독 이름 특성의 값을 검색합니다.

    ```bash
    az account show --query id --output tsv
    az account show --query name --output tsv
    ```

    > **참고**: 두 값을 모두 텍스트 파일에 복사해 두세요. 이 랩의 뒷부분에서 계정 이름이 필요합니다.

1. **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 리소스 주체를 만듭니다. 여기서 **myServicePrincipalName**은 문자와 숫자로 구성된 고유한 문자열로 바꾸고, **mySubscriptionID**는 Azure subscriptionId로 바꿉니다.

    ```bash
    az ad sp create-for-rbac --name myServicePrincipalName \
                         --role contributor \
                         --scopes /subscriptions/mySubscriptionID
    ```

    > **참고**: 이 명령을 실행하면 JSON 출력이 생성됩니다. 텍스트 파일에 출력을 복사해 두세요. 이 랩의 후반부에서 필요합니다.

1. 그 다음, 랩 컴퓨터에서 웹 브라우저를 시작하여 Azure DevOps **eShopOnWeb** 프로젝트로 이동합니다. **프로젝트 설정 > 서비스 연결(파이프라인 아래)** 및 **새 서비스 연결**을 클릭합니다.

    ![새 서비스 연결](images/new-service-connection.png)

1. **새 서비스 연결** 블레이드에서 **Azure Resource Manager** 및 **다음**을 선택합니다(아래로 스크롤해야 할 수 있음).

1. **서비스 주체(수동)** 를 선택하고 **다음**을 클릭합니다.

1. 이전 단계에서 수집한 정보를 사용하여 비어 있는 필드를 채웁니다.
    - 구독 ID 및 이름입니다.
    - 서비스 주체 ID(appId), 서비스 주체 키(암호) 및 테넌트 ID(테넌트).
    - **서비스 연결 이름**에 **azure subs**를 입력합니다. 이 이름은 Azure 구독과 통신하기 위해 Azure DevOps Service 연결이 필요한 경우 YAML 파이프라인에서 참조됩니다.

    ![Azure 서비스 연결](images/azure-service-connection.png)

1. **확인 및 저장**을 클릭합니다.

#### 작업 2: YAML 파이프라인을 통해 Azure에 리소스 배포
>>>>>>> 스태시된 변경 내용
1. **Pipelines** 허브의 **파이프라인** 창으로 다시 이동합니다.
1. **첫 번째 파이프라인 만들기** 창에서 **파이프라인 만들기**를 클릭합니다.

    > **참고**: 여기서는 마법사를 사용하여 프로젝트를 기준으로 YAML 파이프라인 정의를 만듭니다.

1. **코드 위치** 창에서 **Azure Repos Git(YAML)** 옵션을 클릭합니다.
1. **리포지토리 선택 창에서** **EShopOnWeb**을 클릭합니다.
1. **파이프라인 구성** 창에서 아래로 스크롤하여 **기존 Azure Pipelines YAML 파일**을 선택합니다.
1. **기존 YAML 파일 선택** 블레이드에서 다음 매개 변수를 지정합니다.
   - 분기: **main**
   - 경로: **.ado/eshoponweb-cd-windows-cm.yml**
1. **계속**을 클릭하여 이러한 설정을 저장합니다.
1. 변수 섹션에서 리소스 그룹의 이름을 선택하고 원하는 위치를 설정하고 서비스 연결 값을 이전에 만든 기존 서비스 연결 중 하나로 바꿉니다.
1. 오른쪽 위 코더에서 **저장 및 실행** 단추를 클릭하고 커밋 대화 상자가 나타나면 **저장 및 실행을** 다시 클릭합니다.

   ![변경한 후 YAML 파이프라인 저장 및 실행](./images/m06/saveandrun.png)

1. deploymemnt가 완료되고 결과를 검토할 때까지 기다립니다.
   ![YAML 파이프라인을 사용하여 Azure에 리소스를 성공적으로 배포](./images/m06/deploy.png)

#### 작업 3: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다(리소스 그룹 이름을 선택한 항목으로 바꿉니다).

   ```bash
   az group list --query "[?starts_with(name,'AZ400-EWebShop-NAME')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

   > **참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## 검토

이 랩에서는 Azure Bicep 템플릿을 만들고, 템플릿 모듈을 사용하여 모듈을 모듈화하고, 모듈 및 업데이트된 종속성을 사용하도록 기본 배포 템플릿을 수정하고, 마지막으로 YAML 파이프라인을 사용하여 Azure에 템플릿을 배포하는 방법을 알아보았습니다.
