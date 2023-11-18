---
lab:
  title: Azure DevOps와 Azure Key Vault 통합
  module: 'Module 05: Implement a secure continuous deployment using Azure Pipelines'
---

# Azure DevOps와 Azure Key Vault 통합

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://learn.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없으면 [조직 또는 프로젝트 컬렉션 만들기](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization)에서 제공되는 지침에 따라 조직을 만듭니다.

- 기존 Azure 구독을 확인하거나 새 구독을 만듭니다.

## 랩 개요

Azure Key Vault에서는 키, 암호, 인증서 등의 중요한 데이터를 안전하게 저장하고 관리할 수 있습니다. Azure Key Vault에는 하드웨어 보안 모듈에 대한 지원과 다양한 암호화 알고리즘 및 키 길이가 포함됩니다. Azure Key Vault를 사용하면 개발자의 일반적인 실수인 소스 코드를 통해 중요한 데이터를 공개할 가능성을 최소화할 수 있습니다. Azure Key Vault에 액세스하려면 적절한 인증 및 권한 부여가 필요하며 해당 콘텐츠에 대한 세분화된 권한을 지원합니다.

이 랩에서는 다음 단계를 수행하여 Azure Pipelines과 Azure Key Vault를 통합하는 방법을 살펴봅니다.

- ACR 암호를 비밀로 저장하는 Azure Key Vault를 만듭니다.
- Azure Key Vault에서 비밀에 대한 액세스를 제공하는 Azure 서비스 주체를 만듭니다.
- 서비스 주체가 비밀을 읽을 수 있도록 권한 구성.
- Azure Key Vault에서 암호를 검색하여 후속 작업에 전달하도록 파이프라인을 구성합니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Microsoft Entra 서비스 주체를 만듭니다.
- Azure Key Vault를 만듭니다.

## 예상 소요 시간: 40분

## 지침

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩의 필수 구성 요소를 설정합니다. 구체적으로는 [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)을 기반으로 하여 새 Azure DevOps 프로젝트와 리포지토리를 설정합니다.

#### 작업 1: (완료된 경우 건너뛰기) 팀 프로젝트 만들기 및 구성

이 작업에서는 여러 랩에서 사용할 **eShopOnWeb** Azure DevOps 프로젝트를 만듭니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직을 엽니다. **새 프로젝트**를 클릭합니다. 프로젝트 이름을 **eShopOnWeb**으로 설정하고 다른 필드는 기본값으로 유지합니다. **만들기**를 클릭합니다.

    ![프로젝트 만들기](images/create-project.png)

#### 작업 2: (완료된 경우 건너뛰기) eShopOnWeb Git 리포지토리 가져오기

이 작업에서는 여러 랩에서 사용할 eShopOnWeb Git 리포지토리를 가져옵니다.

1. 랩 컴퓨터의 브라우저 창에서 Azure DevOps 조직 및 이전에 만든 **eShopOnWeb** 프로젝트를 엽니다. **Repos > 파일**, **가져오기**를 클릭합니다. **Git 리포지토리 가져오기** 창에서 다음 URL https://github.com/MicrosoftLearning/eShopOnWeb.git을 붙여넣고 **가져오기**를 클릭합니다.

    ![리포지토리 가져오기](images/import-repo.png)

2. 리포지토리는 다음과 같은 방식으로 구성됩니다.
    - **.ado** 폴더에는 Azure DevOps YAML 파이프라인이 포함되어 있습니다.
    - **컨테이너를 사용하여 개발하는 .devcontainer** 폴더 컨테이너 설정(VS Code 또는 GitHub Codespaces의 로컬).
    - **.azure** 폴더에는 일부 랩 시나리오에서 사용되는 코드 템플릿으로 Bicep&ARM 인프라가 포함되어 있습니다.
    - **.github** 폴더 컨테이너 YAML GitHub 워크플로 정의.
    - **src** 폴더에는 랩 시나리오에서 사용되는 .NET 6 웹 사이트가 포함되어 있습니다.

### 연습 1: CI 파이프라인을 설정하여 eShopOnWeb 컨테이너 빌드하기

다음을 위한 CI YAML 파이프라인 설정:

- 컨테이너 이미지를 유지하는 Azure Container Registry 만들기
- Docker Compose를 사용하여 **eshoppublicapi** 및 **eshopwebmvc** 컨테이너 이미지를 빌드하고 푸시합니다. **eshopwebmvc** 컨테이너만 배포됩니다.

#### 작업 1: (완료된 경우 건너뛰기) 서비스 주체 만들기

이 작업에서는 Azure CLI를 사용하여 서비스 주체를 만듭니다. 그러면 Azure DevOps에서 다음을 수행할 수 있습니다.

- Azure 구독에 리소스를 배포합니다.
- 나중에 만든 Key Vault 비밀에 대한 읽기 권한 갖기

> **참고**: 서비스 주체가 이미 있으면 다음 작업을 바로 진행해도 됩니다.

Azure Pipelines에서 Azure 리소스를 배포하려면 서비스 주체가 필요합니다. 여기서는 파이프라인의 비밀을 검색할 것이므로 Azure Key Vault를 만들 때 서비스에 대한 권한을 부여해야 합니다.

프로젝트 설정 페이지에서 새 서비스 연결을 만들거나 파이프라인 정의 내에서 Azure 구독에 연결하면 Azure Pipelines에서 서비스 주체가 자동으로 작성됩니다(자동 옵션). Azure Portal이나 Azure CLI를 통해 서비스 주체를 수동으로 만든 다음 여러 프로젝트에서 재사용할 수도 있습니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고, Azure Portal**[로 ](https://portal.azure.com)** 이동하고, 이 랩에서 사용할 Azure 구독의 소유자 역할이 있고 이 구독과 연결된 Microsoft Entra 테넌트에서 Global 관리istrator의 역할을 가진 사용자 계정으로 로그인합니다.
2. Azure Portal의 페이지 위쪽 검색 텍스트 상자 바로 오른쪽에 있는 **Cloud Shell** 아이콘을 클릭합니다.
3. **Bash**와 **PowerShell** 중 선택하라는 메시지가 표시되면 **Bash**를 선택합니다.

   >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지 없음**이라는 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다.

4. Bash** 프롬프트의 ****Cloud Shell** 창에서 다음 명령을 실행하여 Azure 구독 ID 및 구독 이름 특성의 값을 검색합니다.

    ```bash
    az account show --query id --output tsv
    az account show --query name --output tsv
    ```

    > **참고**: 두 값을 모두 텍스트 파일에 복사합니다. 이 랩의 뒷부분에서 계정 이름이 필요합니다.

5. **Bash** 프롬프트의 **Cloud Shell** 창에서 다음 명령을 실행하여 리소스 주체를 만듭니다. 여기서 **myServicePrincipalName**은 문자와 숫자로 구성된 고유한 문자열로 바꾸고, **mySubscriptionID**는 Azure subscriptionId로 바꿉니다.

    ```bash
    az ad sp create-for-rbac --name myServicePrincipalName \
                         --role contributor \
                         --scopes /subscriptions/mySubscriptionID
    ```

    > **참고**: 명령은 JSON 출력을 생성합니다. 출력을 텍스트 파일에 복사합니다. 이 랩의 후반부에서 필요합니다.

6. 그 다음, 랩 컴퓨터에서 웹 브라우저를 시작하여 Azure DevOps **eShopOnWeb** 프로젝트로 이동합니다. **프로젝트 설정 > 서비스 연결(파이프라인 아래)** 및 **새 서비스 연결**을 클릭합니다.

    ![새 서비스 커넥트ion](images/new-service-connection.png)

7. **새 서비스 연결** 블레이드에서 **Azure Resource Manager** 및 **다음**을 선택합니다(아래로 스크롤해야 할 수 있음).

8. **서비스 주체(수동)** 를 선택하고 **다음**을 클릭합니다.

9. 이전 단계에서 수집한 정보를 사용하여 비어 있는 필드를 채웁니다.
    - 구독 ID 및 이름입니다.
    - 서비스 주체 ID(appId), 서비스 주체 키(암호) 및 테넌트 ID(테넌트).
    - **서비스 연결 이름**에 **azure subs**를 입력합니다. 이 이름은 Azure 구독과 통신하기 위해 Azure DevOps Service 연결이 필요한 경우 YAML 파이프라인에서 참조됩니다.

    ![Azure 서비스 연결](images/azure-service-connection.png)

10. **확인 및 저장**을 클릭합니다.

#### 작업 2: CI 파이프라인 설정 및 실행

이 작업에서는 기존 CI YAML 파이프라인 정의를 가져오고, 수정하고, 실행합니다. 새 ACR(Azure Container Registry)을 만들고 eShopOnWeb 컨테이너 이미지를 빌드/게시합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 Azure DevOps **eShopOnWeb** 프로젝트로 이동합니다. **파이프라인 > 파이프라인**으로 이동하여 **파이프라인 만들기**(또는 **새 파이프라인**)를 클릭합니다.

2. **코드 위치** 창에서 **Azure Repos Git(YAML)** 을 선택하고 **eShopOnWeb** 리포지토리를 선택합니다.

3. **구성** 섹션에서 **기존 Azure Pipelines YAML 파일**을 선택합니다. 경로 **/.ado/eshoponweb-ci-dockercompose.yml**을 제공하고 **계속**을 클릭합니다.

    ![파이프라인 선택](images/select-ci-container-compose.png)

4. YAML 파이프라인 정의에서 **AZ400-EWebShop-NAME**의 **NAME**을 대체하여 리소스 그룹 이름을 사용자 지정하고 **YOUR-SUBSCRIPTION-ID**를 사용자의 고유한 Azure subscriptionId로 바꿉니다.

5. **저장 및 실행**을 클릭하고 파이프라인이 성공적으로 실행될 때까지 기다립니다.

    > **참고**: 이 빌드를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 빌드 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **AzureResourceManagerTemplateDeployment**는 **bicep**을 사용하여 Azure Container Registry를 배포합니다.
    - **PowerShell** 작업은 bicep 출력(acr 로그인 서버)을 가져오고 파이프라인 변수를 만듭니다.
    - **DockerCompose** 작업은 eShopOnWeb에 대한 컨테이너 이미지를 빌드하고 Azure Container Registry를 푸시합니다.

6. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **파이프라인 > 파이프라인으로** 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/제거** 옵션을 클릭합니다. 이름을 **eshoponweb-ci-dockercompose**로 설정정하고 **저장**을 클릭합니다.

7. 실행이 완료되면 Azure Portal에서 이전에 정의된 리소스 그룹을 열고, 생성된 컨테이너 이미지 **eshoppublicapi** 및 **eshopwebmvc**가 있는 ACR(Azure Container Registry)을 찾아야 합니다. 배포 단계에서만 **eshopwebmvc**를 사용합니다.

    ![ACR의 컨테이너 이미지](images/azure-container-registry.png)

8. **액세스 키를** 클릭하고 **암호** 값을 복사하면 Azure Key Vault에서 비밀로 유지되므로 이 값이 다음 작업에서 사용됩니다.

    ![ACR 암호](images/acr-password.png)

#### 작업 2: Azure Key Vault 만들기

이 작업에서는 Azure Portal을 사용하여 Azure Key Vault를 만듭니다.

이 랩 시나리오의 경우 ACR(Azure Container Registry)에 저장된 컨테이너 이미지를 끌어오고 실행하는 ACI(Azure Container Instance)가 있습니다. 이 ACR의 암호를 키 자격 증명 모음에 비밀로 저장하려고 합니다.

1. Azure Portal의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **키 자격 증명 모음**을 입력하고 **Enter** 키를 누릅니다.
2. **키 자격 증명 모음** 블레이드를 선택하고 **만들기 > 키 자격 증명 모음**을 클릭합니다.
3. **키** 자격 증명** 모음 만들기 블레이드의 **기본 사항 탭에서 다음 설정을 지정하고 다음**을 **클릭합니다.

    | 설정 | 값 |
    | --- | --- |
    | 구독 | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | Resource group | 새 리소스 그룹의 이름 **AZ400-EWebShop-NAME** |
    | 키 자격 증명 모음 이름 | **ewebshop-kv-NAME**(NAME 바꾸기)과 같은 고유하고 유효한 이름 |
    | 지역 | 랩 환경의 위치에 가까운 Azure 지역 |
    | 가격 책정 계층 | **Standard** |
    | 삭제된 자격 증명 모음 보존 일수 | **7** |
    | 제거 보호 | **제거 보호 사용 안 함** |

4. **키** 자격 증명 모음 만들기 블레이드의 **액세스 구성** 탭에서 **자격 증명 모음 액세스 정책을** 선택한 **다음 액세스 정책** 섹션에서 + 만들기**를 클릭하여 **새 정책을 설정합니다.

    > **참고**: 권한 있는 애플리케이션 및 사용자만 허용하여 키 자격 증명 모음에 대한 액세스를 보호해야 합니다. 이렇게 하면 자격 증명 모음의 데이터에 액세스할 때 파이프라인에서 인증에 사용할 이전에 생성된 서비스 주체에 읽기(가져오기/나열) 권한을 제공해야 합니다. 

    1. 사용 권한 블레이드의 **비밀 권한** 아래에서 **권한 가져오기** 및 **나열**을 **검사.** **다음**을 클릭합니다.
    2. 보안 주체** 블레이드에서 **지정된 ID 또는 이름을 사용하여 이전에 만든 서비스 주체**를 검색**하고 목록에서 선택합니다. **다음**, 다음 **, ****만들기**(액세스 정책)를 클릭합니다.
    3. **검토 + 만들기** 블레이드에서 **만들기**를 클릭합니다.

5. 키** 자격 **증명 모음 만들기 블레이드로 돌아가서 검토 + 만들기 > 만들기를 클릭합니다 **.**

    > **참고**: Azure Key Vault가 프로비전될 때까지 기다립니다. 이는 1분 이내에 완료됩니다.

6. **배포가 완료됨** 블레이드에서 **리소스로 이동**을 클릭합니다.
7. Azure Key Vault(ewebshop-kv-NAME) 블레이드의 블레이드 **왼쪽에 있는 세로 메뉴의 개체** 섹션에서 비밀을** 클릭합니다**.
8. **비밀** 블레이드에서 **생성/가져오기**를 클릭합니다.
9. **비밀 만들기** 블레이드에서 다음 설정을 지정한 후에 **만들기**를 클릭합니다. 다른 값은 모두 기본값으로 유지합니다.

    | 설정 | 값 |
    | --- | --- |
    | 업로드 옵션 | **수동** |
    | 이름 | **acr-secret** |
    | 값 | 이전 작업에서 복사한 ACR 액세스 암호 |

#### 작업 3: Azure Key Vault에 연결된 변수 그룹 만들기

이 작업에서는 서비스 주체(커넥트 서비스 주체)를 사용하여 Key Vault에서 ACR 암호 비밀을 검색하는 변수 그룹을 Azure DevOps에 만듭니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 Azure DevOps 프로젝트 **eShopOnWeb**으로 이동합니다.

2. Azure DevOps 포털의 세로 탐색 창에서 **파이프라인 > 라이브러리**를 선택합니다. **+ 변수 그룹을** 클릭합니다.

3. **새 변수 그룹** 블레이드에서 다음 설정을 지정합니다.

    | 설정 | 값 |
    | --- | --- |
    | 변수 그룹 이름 | **eshopweb-vg** |
    | Azure Key Vault의 비밀 연결 | **사용** |
    | Azure 구독 | **사용 가능한 Azure 서비스 연결 > Azure 구독** |
    | 키 자격 증명 모음 이름 | 키 자격 증명 모음 이름입니다.|

4. **변수**에서 **+ 추가**를 클릭하고 **acr-secret** 비밀을 선택합니다. **확인**을 클릭합니다.
5. **Save**를 클릭합니다.

    ![변수 그룹 만들기](images/vg-create.png)

#### 작업 4: ACI(Azure Container Instance)에 컨테이너를 배포하도록 CD 파이프라인 설정

이 작업에서는 CD 파이프라인을 가져오고, 사용자 지정하고, Azure Container Instance에서 이전에 만든 컨테이너 이미지를 배포하기 위해 실행합니다.

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 Azure DevOps **eShopOnWeb** 프로젝트로 이동합니다. **파이프라인 > 파이프라인으로** 이동하고 **새 파이프라인**을 클릭합니다.

2. **코드 위치** 창에서 **Azure Repos Git(YAML)** 을 선택하고 **eShopOnWeb** 리포지토리를 선택합니다.

3. **구성** 섹션에서 **기존 Azure Pipelines YAML 파일**을 선택합니다. 다음 경로 **/.ado/eshoponweb-cd-aci.yml**을 제공하고 **계속**을 클릭합니다.

4. YAML 파이프라인 정의에서 다음을 사용자 지정합니다.

    - **YOUR-SUBSCRIPTION-ID**를 Azure 구독 ID로 바꿉니다.
    - **az400eshop-NAME** 전역적으로 고유한 이름을 설정하려면 NAME을 바꿉니다.
    - ACR 로그인 서버가 있는 **YOUR-ACR.azurecr.io** 및 **ACR-USERNAME**(둘 다 ACR 이름이 필요하며, ACR > 액세스 키에서 검토할 수 있음).
    - **AZ400-EWebShop-NAME**, 랩에서 이전에 정의된 리소스 그룹 이름이 포함됨.

5. **저장 및 실행을** 클릭합니다.
6. 파이프라인을 열고 성공적으로 실행되기를 기다립니다.

    > **중요**: "이 파이프라인을 실행하기 전에 리소스에 액세스할 수 있는 권한이 필요합니다."라는 메시지가 표시되면 보기, 허용 및 허용을 다시 클릭합니다. 파이프라인에서 리소스를 만들 수 있도록 허용하려면 이 작업이 필요합니다.

    > **참고**: 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. CD 정의는 다음과 같은 작업으로 구성되어 있습니다.
    - **리소스**: CI 파이프라인 완료 시 자동으로 트리거되도록 준비됩니다. 또한 bicep 파일에 대한 리포지토리를 다운로드합니다.
    - **변수(배포 단계용)** 는 변수 그룹에 연결하여 Azure Key Vault 비밀 **acr-secret**을 사용합니다.
    - **AzureResourceManagerTemplateDeployment**는 bicep 템플릿을 사용하여 ACI(Azure Container Instance)를 배포하며, 이전에 생성된 컨테이너 이미지를 ACI가 ACR(Azure Container Registry)에서 다운로드할 수 있도록 ACR 로그인 매개 변수를 제공합니다.

7. 파이프라인은 프로젝트 이름을 기준으로 이름을 사용합니다. 파이프라인을 더 잘 식별하기 위해 **이름을 바꿔**보겠습니다. **파이프라인 > 파이프라인으로** 이동하여 최근에 만든 파이프라인을 클릭합니다. 줄임표 및 **이름 바꾸기/제거** 옵션을 클릭합니다. 이름을 **eshoponweb-cd-aci**로 설정하고 **저장**을 클릭합니다.

### 연습 2: Azure 랩 리소스 제거

이 연습에서는 예상치 못한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

>**참고**: 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용되지 않는 리소스를 제거하면 예기치 않은 요금이 발생하지 않습니다.

#### 작업 1: Azure 랩 리소스 제거

이 작업에서는 Azure Cloud Shell을 사용하여 불필요한 비용이 발생하지 않도록 이 랩에서 프로비전한 Azure 리소스를 제거합니다.

1. Azure Portal에서 생성된 리소스 그룹을 열고 **리소스 그룹 삭제**를 클릭합니다.

## 검토

이 랩에서는 다음 단계를 사용하여 Azure Key Vault를 Azure DevOps 파이프라인과 통합했습니다.

- Azure Key Vault 비밀에 대한 액세스를 제공하고 Azure DevOps에서 Azure에 대한 배포를 인증하는 Azure 서비스 주체를 만들었습니다.
- Git 리포지토리에서 가져온 두 개의 YAML 파이프라인을 실행했습니다.
- 변수 그룹을 사용하여 Azure Key Vault에서 암호를 검색하고 후속 작업에 사용하도록 하나의 파이프라인을 구성했습니다.
