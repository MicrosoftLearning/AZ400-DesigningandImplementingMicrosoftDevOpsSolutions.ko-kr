---
lab:
  title: Azure Bicep 템플릿을 사용한 배포
  module: 'Module 06: Manage infrastructure as code using Azure and DSC'
---

# Azure Bicep 템플릿을 사용한 배포

# 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/en-us/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)에서 제공되는 지침에 따라 조직을 만듭니다.

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

이 연습에서는 Visual Studio Code를 포함하는 랩의 필수 구성 요소를 설정합니다.

#### 작업 1: Git 및 Visual Studio Code 설치 및 구성

이 작업에서는 Visual Studio Code를 설치합니다. 이 필수 구성 요소를 이미 구현한 경우 다음 작업으로 직접 진행할 수 있습니다.

1. Visual Studio Code가 아직 설치되어 있지 않은 경우 랩 컴퓨터에서 웹 브라우저를 시작하고 Visual Studio Code 다운로드 페이지[로 이동하여 ](https://code.visualstudio.com/)다운로드하고 설치합니다.

### 연습 1: Azure Bicep 템플릿 작성 및 배포

이 랩에서는 Azure Bicep 템플릿 및 템플릿 모듈을 만듭니다. 그런 다음 템플릿 모듈을 사용하고 종속성을 업데이트하도록 기본 배포 템플릿을 수정하고 마지막으로 템플릿을 Azure에 배포합니다.

#### 작업 1: Azure Bicep 템플릿 만들기

이 작업에서는 Visual Studio Code를 사용하여 Azure Bicep 템플릿을 만듭니다.

1. 랩 컴퓨터에서 Visual Studio Code를 시작하고 Visual Studio Code에서 파일** 최상위 메뉴를 클릭하고 **드롭다운 메뉴에서 기본 설정을** 선택하고**, 계단식 메뉴에서 확장을 선택하고, 확장을** 선택하고**, Bicep을 입력하고, Bicep**을 입력**하고, Microsoft에서** **게시한 항목을 선택하고, 설치**를 클릭하여 **Azure Bicep 언어 지원을 설치합니다.
1. 웹 브라우저에서 **<https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-simple-windows/main.bicep>** 에 연결합니다. 파일에 대한 **원시** 옵션을 클릭합니다. 코드 창의 내용을 복사하여 Visual Studio Code 편집기에 붙여넣습니다.

   > **참고**: 템플릿을 처음부터 만드는 대신 간단한 Windows 템플릿 VM** 배포라는 **Azure 빠른 시작 템플릿[ 중 하나를 ](https://azure.microsoft.com/en-us/resources/templates/)사용합니다. 템플릿은 GitHub - [vm-simple-windows에서 템플릿을 다운로드할 수 있습니다](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-simple-windows).

1. 랩 컴퓨터에서 파일 탐색기 열고 템플릿을 저장하는 데 사용될 다음 로컬 폴더를 만듭니다.

   - **C:\\templates**

1. 기본.bicep 템플릿을 사용하여 Visual Studio Code 창으로 다시 전환하고, 파일 최상위 메뉴를 클릭하고 **, 드롭다운 메뉴에서 다른 이름으로** 저장을 클릭하고**, 새로 만든 로컬 폴더 **C:\\templates에 템플릿을 기본.bicep**으로 **저장합니다**.**
1. 템플릿을 검토하여 해당 구조를 더 잘 이해합니다. 템플릿에는 다음과 같은 5가지 리소스 종류가 포함되어 있습니다.

   - Microsoft.Storage/storageAccounts
   - Microsoft.Network/publicIPAddresses
   - Microsoft.Network/virtualNetworks
   - Microsoft.Network/networkInterfaces
   - Microsoft.Compute/virtualMachines

1. Visual Studio Code에서 파일을 다시 저장하지만 이번에는 C:\\templates를** 대상으로 **선택하고 storage.bicep**을 파일 이름으로 선택합니다**.

   > **참고**: 이제 C:templates기본.bicep** 및 C\\:\\templates\\\\ storage.bicep**이라는 **두 개의 동일한 JSON 파일이 **있습니다.

#### 작업 2: 스토리지 리소스에 대한 템플릿 모듈 만들기

이 작업에서는 스토리지 템플릿 모듈 **storage.bicep** 이 스토리지 계정만 만들고 첫 번째 템플릿에서 가져오게 되도록 이전 작업에 저장한 템플릿을 수정합니다. 스토리지 템플릿 모듈은 기본 템플릿( **기본.bicep**)에 값을 다시 전달해야 하며 이 값은 스토리지 템플릿 모듈의 출력 요소에 정의됩니다.

1. **Visual Studio Code 창에 표시된 storage.bicep** 파일의 **리소스 섹션** 아래에서 storageAccounts 리소스를 **제외한 모든 리소스 요소를 제거합니다**. 리소스 섹션은 다음과 같이 표시됩니다.

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

1. 다음으로, 모든 변수 정의를 제거합니다.

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

1. 다음으로 위치를 제외한 모든 매개 변수 값을 제거하고 다음 매개 변수 코드를 추가하여 다음 결과를 생성합니다.

   ```bicep
   @description('Location for all resources.')
   param location string = resourceGroup().location

   @description('Name for the storage account.')
   param storageAccountName string
   ```

1. 다음으로, 파일의 끝에서 현재 출력을 제거하고 storageURI 출력 값이라는 새 출력을 추가합니다. 아래와 같이 출력을 수정합니다.

   ```bicep
   output storageURI string = storageAccount.properties.primaryEndpoints.blob
   ```

1. storage.bicep 템플릿 모듈을 저장합니다. 이제 스토리지 템플릿은 다음과 같이 표시됩니다.

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

1. Visual Studio Code에서 파일 최상위 메뉴를 클릭하고 **드롭다운 메뉴에서 파일** 열기를 선택하고 **파일 열기 대화 상자에서 C:\\templates\\기본.bicep**으로 **이동하여 선택한 다음 열기**를 클릭합니다**.**
1. **기본.bicep** 파일의 리소스 섹션에서 스토리지 리소스 요소를 제거합니다.

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

1. 다음으로, 새로 삭제된 스토리지 리소스 요소가 있던 동일한 위치에 다음 코드를 직접 추가합니다.

   ```bicep
   module storageModule './storage.bicep' = {
     name: 'linkedTemplate'
     params: {
       location: location
       storageAccountName: storageAccountName
     }
   }
   ```

1. 또한 모듈의 출력을 대신 사용하도록 가상 머신 리소스의 스토리지 계정 Blob URI에 대한 참조를 수정해야 합니다. 가상 머신 리소스를 찾고 진단Profile 섹션을 다음으로 바꿉니다.

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
   - 모듈에는 storageModule이라는 기호 이름이 있습니다. 이 이름은 모든 종속성을 구성하는 데 사용됩니다.
   - 템플릿 모듈을 사용하는 경우에만 증분 배포 모드를 사용할 수 있습니다.
   - 상대 경로는 템플릿 모듈에 사용됩니다.
   - 매개 변수를 사용하여 기본 템플릿의 값을 템플릿 모듈로 전달합니다.

> **참고**: Azure ARM 템플릿을 사용하면 다른 사용자가 더 쉽게 사용할 수 있도록 스토리지 계정을 사용하여 연결된 템플릿을 업로드했을 것입니다. Azure Bicep 모듈을 사용하면 공용 및 프라이빗 레지스트리 옵션이 모두 있는 Azure Bicep 모듈 레지스트리에 업로드할 수 있습니다. 자세한 내용은 Azure Bicep 설명서에서 [찾을 수 있습니다](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/modules#file-in-registry).

1. 템플릿을 저장하는 경우

#### 작업 4: 템플릿 모듈을 사용하여 Azure에 리소스 배포

> **참고**: 로컬로 설치된 Azure CLI를 사용하거나 Azure Cloud Shell 또는 CI/CD 파이프라인에서 템플릿을 배포하는 등 여러 가지 방법으로 템플릿을 배포할 수 있습니다. 이 랩에서는 Azure Cloud Shell에서 Azure CLI를 사용합니다.

> **참고**: ARM 템플릿과 달리 Azure Portal을 사용하여 Bicep 템플릿을 직접 배포할 수 없습니다.

> **참고**: Azure Cloud Shell을 사용하려면 기본.bicep 및 storage.bicep 파일을 모두 Cloud Shell의 홈 디렉터리에 업로드합니다.

> **참고**: 현재 Azure CLI는 원격 Bicep 파일 배포를 지원하지 않습니다. bicep 파일을 빌드하여 ARM 템플릿 JSON을 가져와 스토리지 계정에 업로드한 다음 원격으로 배포할 수 있습니다.

1. 랩 컴퓨터의 Azure Portal을 표시하는 웹 브라우저에서 Cloud Shell 아이콘을 **클릭하여 Cloud Shell** 을 엽니다.
   > **참고**: 이 연습의 앞부분에서 PowerShell 세션이 여전히 활성 상태인 경우 Bash(다음 단계)로 전환합니다.
1. Cloud Shell 창에서 PowerShell을 클릭하고 **드롭다운 메뉴에서 Bash**를 클릭하고 **메시지가 표시되면 확인을** 클릭합니다**.**
1. Cloud Shell 창에서 파일** 업로드/다운로드 아이콘을 **클릭하고 드롭다운 메뉴에서 업로드**를 클릭합니다**.
1. **열기** 대화 상자에서 C:\\templates\\기본.bicep**으로 이동하여 선택하고 **열기**를 클릭합니다**.
1. 동일한 단계에 따라 C:\\templates storage.bicep** 파일도 업로드**합니다\\.
1. **Cloud Shell 창의 Bash** 세션에서 다음을 실행하여 새로 업로드된 템플릿을 사용하여 배포를 수행합니다.

   ```bash
   az deployment group what-if --name az400m06l15deployment --resource-group az400m06l15-RG --template-file main.bicep
   ```

1. 'adminUsername' 값을 제공하라는 메시지가 표시되면 Student를 입력**하고 Enter** 키를 누릅니**다.**
1. 'adminPassword' 값을 제공하라는 메시지가 표시되면 Pa55w.rd1234**를 **입력**하고 Enter** 키를 누릅니다. (암호 입력은 표시되지 않음)
1. 배포의 유효성을 검사하는 이 명령의 결과를 검토하고 템플릿에 오류가 있는지 살펴보겠습니다. 이는 특히 많은 리소스와 중요 비즈니스용 클라우드 환경에서 템플릿을 배포할 때 매우 유용합니다.

1. **Cloud Shell 창의 Bash** 세션에서 다음을 실행하여 새로 업로드된 템플릿을 사용하여 배포를 수행합니다.

   ```bash
   LOCATION='<region>'
   ```
   > **참고**: 지역 이름을 위치와 가까운 지역으로 바꿉다. 사용할 수 있는 위치를 모르는 경우 명령을 실행합니다 `az account list-locations -o table` .
  
   ```bash
   az group create --name az400m06l15-RG --location $LOCATION
   ```

   ```bash   
   az deployment group create --name az400m06l15deployment --resource-group az400m06l15-RG --template-file main.bicep
   ```

1. 'adminUsername' 값을 제공하라는 메시지가 표시되면 Student를 입력**하고 Enter** 키를 누릅니**다.**
1. 'adminPassword' 값을 제공하라는 메시지가 표시되면 Pa55w.rd1234**를 **입력**하고 Enter** 키를 누릅니다. (암호 입력은 표시되지 않음)

1. 위의 명령을 실행하여 템플릿을 배포할 때 오류가 발생하는 경우 다음을 시도합니다.

   - Azure 구독이 여러 개 있는 경우 리소스 그룹이 배포된 올바른 구독 컨텍스트로 구독 컨텍스트를 설정했는지 확인합니다.
   - 지정한 URI를 통해 연결된 템플릿에 액세스할 수 있는지 확인합니다.

> **참고**: 다음 단계로 이제 네트워크 및 가상 머신 리소스 정의와 같은 기본 배포 템플릿에서 다시 기본 리소스 정의를 모듈화할 수 있습니다.

> **참고**: 배포된 리소스를 사용할 계획이 없는 경우 관련 요금을 방지하려면 해당 리소스를 삭제해야 합니다. 리소스 그룹 **az400m06l15-RG**를 삭제하면 됩니다.

### 연습 2: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

> **참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### 작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal의 **Cloud Shell** 창에서 **Bash** 세션을 시작합니다.
1. 다음 명령을 실행하여 이 모듈의 전체 랩에서 생성된 모든 리소스 그룹을 나열합니다.

   ```bash
   az group list --query "[?starts_with(name,'az400m06l15-RG')].name" --output tsv
   ```

1. 다음 명령을 실행하여 이 모듈의 랩 전체에서 만든 모든 리소스 그룹을 삭제합니다.

   ```bash
   az group list --query "[?starts_with(name,'az400m06l15-RG')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

   > **참고**: 명령은 비동기적으로 실행되므로(--nowait 매개 변수에 의해 결정됨) 동일한 Bash 세션 내에서 즉시 다른 Azure CLI 명령을 실행할 수 있지만 리소스 그룹이 실제로 제거되기까지 몇 분 정도 걸립니다.

## 검토

이 랩에서는 Azure Resource Manager 템플릿을 만들고, 연결된 템플릿을 사용하여 모듈화하고, 기본 배포 템플릿을 수정하여 연결된 템플릿 및 업데이트된 종속성을 호출하고, 마지막으로 Azure에 템플릿을 배포하는 방법을 알아보았습니다.
