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

- Azure 구독의 Owner 역할, 그리고 Azure 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할이 지정되어 있는 Microsoft 계정 또는 Azure AD 계정이 있는지 확인합니다.

- [Visual Studio Code](https://code.visualstudio.com/) 이 랩의 필수 구성 요소로 설치됩니다.

## 랩 개요

이 랩에서는 Azure Bicep 템플릿을 만들고 Azure Bicep 모듈 개념을 사용하여 모듈화합니다. 그런 다음 기본 배포 템플릿을 수정하여 모듈을 사용하고 마지막으로 모든 리소스를 Azure에 배포합니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure Bicep 템플릿을 이해하고 만듭니다.
- 스토리지 리소스에 재사용 가능한 Bicep 모듈을 만듭니다.
- Azure Blob Storage에 연결된 템플릿을 업로드하고 SAS 토큰을 생성합니다.
- 모듈을 사용하도록 기본 템플릿을 수정합니다.
- 기본 템플릿을 수정하여 종속성을 업데이트합니다.
- Azure Bicep 템플릿을 사용하여 모든 리소스를 Azure에 배포합니다.

## 예상 소요 시간: 60분

## Instructions

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 Visual Studio Code를 비롯한 랩의 필수 구성 요소를 설정합니다.

#### 작업 1: Git 및 Visual Studio Code 설치 및 구성

이 작업에서는 Visual Studio Code를 설치합니다. 이 필수 구성 요소를 이미 구현한 경우에는 다음 작업을 바로 진행해도 됩니다.

1. Visual Studio Code를 아직 설치하지 않았다면 랩 컴퓨터에서 웹 브라우저를 시작하고 [Visual Studio Code 다운로드 페이지](https://code.visualstudio.com/)로 이동하여 Visual Studio Code를 다운로드한 다음 설치합니다.

### 연습 1: Azure Bicep 템플릿 작성 및 배포

이 랩에서는 Azure Bicep 템플릿 및 템플릿 모듈을 만듭니다. 그런 다음 템플릿 모듈을 사용하고 종속성을 업데이트하도록 기본 배포 템플릿을 수정하고 마지막으로 템플릿을 Azure에 배포합니다.

#### 작업 1: Azure Bicep 템플릿 만들기

이 작업에서는 Visual Studio Code 사용하여 Azure Bicep 템플릿을 만듭니다.

1. 랩 컴퓨터에서 Visual Studio Code 시작하고, Visual Studio Code **파일** 최상위 메뉴를 클릭하고, 드롭다운 메뉴에서 **기본 설정을** 선택하고, 계단식 메뉴에서 **확장을** 선택하고, **확장 검색** 텍스트 상자에 **Bicep**을 입력하고, Microsoft에서 게시한 항목을 선택하고, **설치**를 클릭하여 Azure Bicep 언어 지원을 설치합니다.
2. 웹 브라우저에서 **<https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-simple-windows/main.bicep>** 에 연결합니다. 파일에 대해 **원시** 옵션을 클릭합니다. 코드 창의 내용을 복사하여 Visual Studio Code 편집기에 붙여넣습니다.

   > **참고**: 여기서는 템플릿을 처음부터 만들지 않고 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/) 중 하나인 **간단한 Windows 템플릿 VM 배포**를 사용합니다. GitHub([vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-simple-windows))에서 이 템플릿을 다운로드할 수 있습니다.

3. 랩 컴퓨터에서 파일 탐색기 열고 템플릿을 저장하는 데 사용할 다음 로컬 폴더를 만듭니다.

   - **C:\\templates**

4. 기본.bicep 템플릿을 사용하여 Visual Studio Code 창으로 다시 전환하고 **파일** 최상위 메뉴를 클릭하고 드롭다운 메뉴에서 **다른 이름으로 저장**을 클릭한 다음 새로 만든 로컬 폴더 **C:\\templates**에 **기본.bicep**으로 템플릿을 저장합니다.
5. 템플릿을 검토하여 구조를 자세히 파악합니다. 이 템플릿에는 5가지 리소스 종류가 포함되어 있습니다.

   - Microsoft.Storage/storageAccounts
   - Microsoft.Network/publicIPAddresses
   - Microsoft.Network/virtualNetworks
   - Microsoft.Network/networkInterfaces
   - Microsoft.Compute/virtualMachines

6. Visual Studio Code 파일을 다시 저장하지만 이번에는 **C:\\templates를** destination으로 선택하고 **storage.bicep**을 파일 이름으로 선택합니다.

   > **참고**: 이제 **C:templates기본.bicep 및 C:\\templates\\** **\\storage.bicep이라는 두 개의 동일한 JSON\\** 파일이 있습니다.

#### 작업 2: 스토리지 리소스에 대한 템플릿 모듈 만들기

이 작업에서는 스토리지 템플릿 모듈 **storage.bicep** 이 스토리지 계정만 만들고 첫 번째 템플릿에서 가져오게 되도록 이전 작업에 저장한 템플릿을 수정합니다. 스토리지 템플릿 모듈은 기본 템플릿인 **기본.bicep**에 값을 다시 전달해야 하며 이 값은 스토리지 템플릿 모듈의 outputs 요소에 정의됩니다.

1. Visual Studio Code 창에 표시된 **storage.bicep** 파일의 **리소스 섹션에서** **storageAccounts** 리소스를 제외한 모든 리소스 요소를 제거합니다. resources 섹션이 다음과 같이 표시되어야 합니다.

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

2. 다음으로, 모든 변수 정의를 제거합니다.

   ```bicep
   var storageAccountName = 'bootdiags${uniqueString(resourceGroup().id)}'
   var nicName = 'myVMNic'
   var addressPrefix = '10.0.0.0/16'
   var subnetName = 'Subnet'
   var subnetPrefix = '10.0.0.0/24'
   var virtualNetworkName = 'MyVNET'
   var networkSecurityGroupName = 'default-NSG'
   var securityProfileJson = {
     uefiSettings: {
       secureBootEnabled: true
       vTpmEnabled: true
     }
     securityType: securityType
   }
   var extensionName = 'GuestAttestation'
   var extensionPublisher = 'Microsoft.Azure.Security.WindowsAttestation'
   var extensionVersion = '1.0'
   var maaTenantName = 'GuestAttestation'
   var maaEndpoint = substring('emptyString', 0, 0)
   ```

3. 다음으로는 location을 제외한 모든 매개 변수 값을 제거하고 다음 매개 변수 코드를 추가합니다. 완성된 코드는 다음과 같아야 합니다.

   ```bicep
   @description('Location for all resources.')
   param location string = resourceGroup().location

   @description('Name for the storage account.')
   param storageAccountName string
   ```

4. 다음으로 파일의 끝에서 현재 출력을 제거하고 storageURI 출력 값이라는 새 출력을 추가합니다. 출력이 다음과 같이 표시되도록 수정합니다.

   ```bicep
   output storageURI string = storageAccount.properties.primaryEndpoints.blob
   ```

5. storage.bicep 템플릿 모듈을 저장합니다. 이제 스토리지 템플릿은 다음과 같이 표시됩니다.

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

1. Visual Studio Code **파일** 최상위 메뉴를 클릭하고 드롭다운 메뉴에서 **파일 열기**를 선택하고 파일 열기 대화 상자에서 **C:\\templates\\기본.bicep**으로 이동하여 선택한 다음 **열기**를 클릭합니다.
2. **기본.bicep** 파일의 리소스 섹션에서 스토리지 리소스 요소를 제거합니다.

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

3. 다음으로 새로 삭제한 스토리지 리소스 요소가 있었던 곳과 같은 위치에 다음 코드를 직접 추가합니다.

   ```bicep
   module storageModule './storage.bicep' = {
     name: 'linkedTemplate'
     params: {
       location: location
       storageAccountName: storageAccountName
     }
   }
   ```

4. 또한 모듈의 출력을 대신 사용하도록 가상 머신 리소스의 스토리지 계정 Blob URI에 대한 참조를 수정해야 합니다. 가상 머신 리소스를 찾고 diagnosticsProfile 섹션을 다음으로 바꿉니다.

   ```bicep
   diagnosticsProfile: {
     bootDiagnostics: {
       enabled: true
       storageUri: storageModule.outputs.storageURI
     }
   }
   ```

5. 기본 템플릿에서 다음 세부 정보를 검토합니다.

   - 기본 템플릿의 모듈은 다른 템플릿에 연결하는 데 사용됩니다.
   - 모듈에는 storageModule이라는 기호 이름이 있습니다. 이 이름은 모든 종속성을 구성하는 데 사용됩니다.
   - 템플릿 모듈을 사용하는 경우에만 증분 배포 모드를 사용할 수 있습니다.
   - 상대 경로는 템플릿 모듈에 사용됩니다.
   - 매개 변수를 사용하여 기본 템플릿의 값을 템플릿 모듈로 전달합니다.

   > **참고**: Azure ARM 템플릿을 사용하면 다른 사용자가 더 쉽게 사용할 수 있도록 스토리지 계정을 사용하여 연결된 템플릿을 업로드했을 것입니다. Azure Bicep 모듈을 사용하면 공용 및 프라이빗 레지스트리 옵션이 모두 있는 Azure Bicep 모듈 레지스트리에 업로드할 수 있습니다. 자세한 내용은 [Azure Bicep 설명서](https://learn.microsoft.com/azure/azure-resource-manager/bicep/modules)에서 찾을 수 있습니다.

6. 템플릿을 저장하는 경우

#### 작업 4: 템플릿 모듈을 사용하여 Azure에 리소스 배포

> **참고**: 로컬에 설치된 Azure CLI를 사용하거나 Azure Cloud Shell 또는 CI/CD 파이프라인에서 템플릿을 배포할 수 있습니다. 이 랩에서는 Azure Cloud Shell에서 Azure CLI를 사용합니다.

> **참고**: ARM 템플릿과 달리 Azure Portal 사용하여 Bicep 템플릿을 직접 배포할 수 없습니다.

> **참고**: Azure Cloud Shell 사용하려면 기본.bicep 및 storage.bicep 파일을 모두 Cloud Shell 홈 디렉터리에 업로드합니다.

> **참고**: 현재 Azure CLI는 원격 Bicep 파일 배포를 지원하지 않습니다. bicep 파일을 빌드하여 ARM 템플릿 JSON을 가져와 스토리지 계정에 업로드한 다음 원격으로 배포할 수 있습니다.

1. 랩 컴퓨터에서 Azure Portal이 표시된 웹 브라우저의 **Cloud Shell** 아이콘을 클릭하여 Cloud Shell을 엽니다.
   > **참고**: 이 연습의 앞부분에서 PowerShell 세션이 여전히 활성 상태인 경우 Bash(다음 단계)로 전환합니다.
2. Cloud Shell 창에서 **PowerShell**을 클릭하고 드롭다운 메뉴에서 **Bash**를 클릭한 다음 메시지가 표시되면 **확인**을 클릭합니다.
3. Cloud Shell 창에서 **파일 업로드/다운로드** 아이콘을 클릭하고 드롭다운 메뉴에서 **업로드**를 클릭합니다.
4. **열기** 대화 상자에서 **C:\\templates\\기본.bicep**으로 이동하여 선택하고 **열기**를 클릭합니다.
5. 동일한 단계에 따라 **C:\\templates storage.bicep 파일도 업로드합니다\\**.
6. Cloud Shell 창의 **Bash** 세션에서 다음 명령을 실행하여 새로 업로드한 템플릿을 사용해 배포를 수행합니다.

   ```bash
   LOCATION='<region>'
   ```

   > **참고**: 지역 이름을 위치와 가까운 지역으로 바꿉다. 사용할 수 있는 위치를 모르는 경우 명령을 실행합니다 `az account list-locations -o table` .

   ```bash
   az group create --name az400m06l15-RG --location $LOCATION
   ```

   ```bash
   az deployment group what-if --name az400m06l15deployment --resource-group az400m06l15-RG --template-file main.bicep
   ```

7. 'adminUsername'의 값을 입력하라는 메시지가 표시되면 **Student**를 입력하고 **Enter** 키를 누릅니다.
8. 'adminPassword'의 값을 입력하라는 메시지가 표시되면 **Pa55w.rd1234**를 입력하고 **Enter** 키를 누릅니다. (암호 입력은 표시되지 않음)
9. 배포의 유효성을 검사하는 이 명령의 결과를 검토하고 템플릿에 오류가 있는지 살펴보겠습니다. 이는 특히 많은 리소스와 중요 비즈니스용 클라우드 환경에서 템플릿을 배포할 때 매우 중요합니다.

10. Cloud Shell 창의 **Bash** 세션에서 다음 명령을 실행하여 새로 업로드한 템플릿을 사용해 배포를 수행합니다.

    ```bash
    az deployment group create --name az400m06l15deployment --resource-group az400m06l15-RG --template-file main.bicep
    ```

11. 'adminUsername'의 값을 입력하라는 메시지가 표시되면 **Student**를 입력하고 **Enter** 키를 누릅니다.
12. 'adminPassword'의 값을 입력하라는 메시지가 표시되면 **Pa55w.rd1234**를 입력하고 **Enter** 키를 누릅니다. (암호 입력은 표시되지 않음)

13. 위의 명령을 실행하여 템플릿을 배포할 때 오류가 발생하면 다음 방법을 시도해 봅니다.

- Azure 구독이 여러 개이면 리소스 그룹을 배포할 때 구독 컨텍스트를 올바르게 설정했는지 확인합니다.
- 지정한 URI에서 연결된 템플릿에 액세스할 수 있는지 확인합니다.

> **참고**: 다음 단계에서는 네트워크 및 가상 머신 리소스 정의와 같은 기본 배포 템플릿의 나머지 리소스 정의를 모듈화할 수 있습니다.

> **참고**: 배포한 리소스를 사용하지 않으려는 경우에는 관련 요금이 발생하지 않도록 해당 리소스를 삭제해야 합니다. 리소스 그룹 **az400m06l15-RG**를 삭제하면 됩니다.

### 연습 2: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

> **참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### 작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
2. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

   ```bash
   az group list --query "[?starts_with(name,'az400m06l15-RG')].name" --output tsv
   ```

3. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

   ```bash
   az group list --query "[?starts_with(name,'az400m06l15-RG')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

   > **참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## 검토

이 랩에서는 Azure Resource Manager 템플릿을 만든 다음 연결된 템플릿을 사용하여 해당 템플릿을 모듈화하는 방법을 알아보았습니다. 그런 다음 기본 배포 템플릿을 수정하여 연결된 템플릿과 업데이트된 종속성을 호출하고, 마지막으로 Azure에 템플릿을 배포했습니다.
