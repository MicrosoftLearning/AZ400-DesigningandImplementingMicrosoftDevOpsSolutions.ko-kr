---
lab:
  title: Azure Project Wikis를 사용하여 팀 지식 공유
  module: 'Module 09: Implement continuous feedback'
---

# Azure Project Wikis를 사용하여 팀 지식 공유

## 학생용 랩 매뉴얼

## 랩 요구 사항

- 이 랩은 **Microsoft Edge** 또는 [Azure DevOps 지원 브라우저](https://docs.microsoft.com/azure/devops/server/compatibility)가 필요합니다.

- **Azure DevOps 조직 설정:** 이 랩에 사용할 수 있는 Azure DevOps 조직이 아직 없는 경우 AZ-400 랩 필수 구성 요소에서 [사용할 수 있는 지침에 따라 만듭니다](https://microsoftlearning.github.io/AZ400-DesigningandImplementingMicrosoftDevOpsSolutions/Instructions/Labs/AZ400_M00_Validate_lab_environment.html).

- **샘플 EShopOnWeb 프로젝트 설정:** 이 랩에 사용할 수 있는 샘플 EShopOnWeb 프로젝트가 아직 없는 경우 AZ-400 랩 필수 구성 요소에서 [사용할 수 있는 지침에 따라 만듭니다](https://microsoftlearning.github.io/AZ400-DesigningandImplementingMicrosoftDevOpsSolutions/Instructions/Labs/AZ400_M00_Validate_lab_environment.html).

## 랩 개요

이 랩에서는 Azure DevOps에서 Wiki를 만들고 구성합니다. 이 과정에서 Markdown 콘텐츠 관리와 Mermaid 다이어그램 작성을 수행합니다.

## 목표

이 랩을 완료하면 다음 작업을 수행할 수 있습니다.

- Azure 프로젝트에서 Wiki를 만듭니다.
- Markdown을 추가하고 편집합니다.
- Mermaid 다이어그램을 만듭니다.

## 예상 소요 시간: 45분

## 지침

### 연습 0: 랩 필수 구성 요소 구성

이 연습에서는 랩 필수 구성 요소의 유효성을 검사하고, Azure DevOps 조직을 모두 준비하고, EShopOnWeb 프로젝트를 만든 것에 대해 알려 줍니다. 자세한 내용은 위의 지침을 참조하세요.

### 연습 1: 코드를 wiki로 게시

이 연습에서는 Azure DevOps 리포지토리를 wiki로 게시하고 게시된 위키를 관리하는 단계를 안내합니다.

> **참고**: Git 리포지토리에 기본 콘텐츠는 Azure DevOps Wiki에 게시할 수 있습니다. 예를 들어 소프트웨어 개발 키트, 제품 설명서 또는 추가 정보 파일을 지원하도록 작성된 콘텐츠를 Wiki에 직접 게시할 수 있습니다. 동일한 Azure DevOps 팀 프로젝트 내에서 여러 위키를 게시할 수 있습니다.

#### 작업 1: Azure DevOps 리포지토리의 분기를 wiki로 게시

이 작업에서는 Azure DevOps 리포지토리의 분기를 wiki로 게시합니다.

> **참고**: 게시된 Wiki가 제품 버전에 해당하는 경우 새 버전의 제품을 릴리스할 때 새 분기를 게시할 수 있습니다.

1. 왼쪽의 세로 메뉴에서 리포**지토리를 클릭하고 **파일** 창의 **위쪽 섹션에서 EShopOnWeb** 리포지토리를 선택했는지 확인**합니다(Git 아이콘이 있는 위쪽 드롭다운에서 선택). 분기 드롭다운 목록(분기 아이콘이 있는 "파일" 위에 있음)에서 기본** 선택하고 **기본 분기의 내용을 검토합니다.
2. 파일 창 왼쪽의 **리포지토리 폴더 및 파일 계층 구조 목록에서 src** 폴더를 확장하고 **Web-> wwwroot -> 이미지** 하위 폴더로 이동**합니다.** **이미지** 하위 폴더에서 brand.png** 항목을 찾고 **마우스 포인터를 오른쪽 끝에 마우스 포인터로 가져가서 자세히** 메뉴를 나타내는 세로 줄임표(점 3개) 기호를 **표시하고 다운로드**를 클릭하여 **brand.png** 파일을 랩 컴퓨터의 로컬 **다운로드** 폴더에 다운로드**합니다.

    >**참고**: 이 랩의 다음 연습에서 이 이미지를 사용합니다.

3. Wiki 원본 파일을 Repos 현재 폴더 구조 내의 별도 폴더에 저장합니다. 리포지토리** 내에서 **파일을** 선택합니다**. **폴더 구조 위에 EShopOnWeb** 리포지토리 제목이 있습니다. **줄임표(점 3개)** 를 선택하고, 새로 만들기/폴더**를 선택하고**, 문서를** 새 폴더 이름의 제목으로 제공합니다**. 리포지토리에서는 빈 폴더를 만들 수 없으므로 READ를 제공합니다 **. ME** 를 새 파일 이름으로 지정합니다.
4. 만들기 단추를** 눌러 폴더 및 파일 **만들기를 확인합니다.
5. READ입니다. ME 파일은 기본 제공 보기 모드에서 열립니다. '코드'로 저장되므로 커밋 단추를 클릭하여 **변경 내용을 커밋**** 해야 합니다**. 커밋 창에서 커밋을 눌러 **다시 한 번 확인합니다**.

6. 왼쪽의 Azure DevOps 세로 메뉴에서 개요**를 클릭하고 **개요** **섹션에서 Wiki**를 선택하고 ****코드를 wiki*로 게시합니다.
7. 코드** 게시 창에서 **다음 설정을 지정하고 게시**를 클릭합니다**.

    | 설정 | 값 |
    | ------- | ----- |
    | 리포지토리 | **EShopOnWeb** |
    | 지점 | **main** |
    | 폴더 | **/Documents** |
    | Wiki 이름 | **EShopOnWeb(문서)** |

    >**참고**: 그러면 Wiki 섹션이 자동으로 열리고 편집기가** 게시**됩니다. 여기서 위키 페이지 제목을 제공할 수 있을 뿐만 아니라 실제 콘텐츠를 추가할 수 있습니다. MarkDown 형식을 사용하는 것이 좋지만 일부 MarkDown 레이아웃 구문에 도움이 되도록 리본 메뉴를 사용하는 것이 좋습니다.

8. 위키 페이지 **제목** 필드에 "온라인 소매점 시작!"을 입력합니다.

9. 위키 페이지의 본문에 다음 텍스트를 붙여넣습니다.

    ```text
    ##Welcome to Our Online Retail Store!
    At our online retail store, we offer a **wide range of products** to meet the **needs of our customers**. Our selection includes everything from *clothing and accessories to electronics, home decor, and more*.
    
    We pride ourselves on providing a seamless shopping experience for our customers. Our website offers the following benefits:
    1. user-friendly,
    1. and easy to navigate, 
    1. allowing you to find what you're looking for,
    1. quickly and easily. 
    
    We also offer a range of **_payment and shipping options_** to make your shopping experience as convenient as possible.
    
    ### about the team
    Our team is dedicated to providing exceptional customer service. If you have any questions or concerns, our knowledgeable and friendly support team is always available to assist you. We also offer a hassle-free return policy, so if you're not completely satisfied with your purchase, you can easily return it for a refund or exchange.
    
    ### Physical Stores
    |Location|Area|Hours|
    |--|--|--|
    | New Orleans | Home and DIY  |07.30am-09.30pm  |
    | Seattle | Gardening | 10.00am-08.30pm  |
    | New York | Furniture Specialists  | 10.00am-09.00pm |
    
    ## Our Store Qualities
    - We're committed to providing high-quality products
    - Our products are offered at affordable prices 
    - We work with reputable suppliers and manufacturers 
    - We ensure that our products meet our strict standards for quality and durability. 
    - Plus, we regularly offer sales and discounts to help you save even more.
    
    #Summary
    Thank you for choosing our online retail store for your shopping needs. We look forward to serving you!
    ```

10. 이 샘플 텍스트는 제목 및 부제목(##), 굵게(**), 기울*집(), 테이블 만들기 방법 등에 이르기까지 몇 가지 일반적인 MarkDown 구문 기능에 대한 개요를 제공합니다.

11. 완료되면 **오른쪽 위 모서리에 있는 저장 단추를 누릅니** 다.

12. **브라우저를 새로 고치** 거나 다른 DevOps 포털 옵션을 선택하고 Wiki 섹션으로 돌아갑니다. 이제 EshopOnWeb(문서)** Wiki가 제공되고 **온라인 **소매점**이 Wiki의 홈페이지**로 **시작됩니다.

#### 작업 2: 게시된 Wiki의 콘텐츠 관리

이 작업에서는 이전 작업에서 게시한 wiki의 콘텐츠를 관리합니다.

1. 왼쪽의 세로 메뉴에서 Repos를 클릭하고 **파일 창의 위쪽 섹션에** **있는 드롭다운 메뉴에 EShopOnWeb** 리포지토리 및 **기본** 분기가 표시되는**지 확인하고, 리포지토리 폴더 계층 구조에서 문서** 폴더를 선택하고**, Welcome-to-our-Online-Retail-Store!를 선택합니다 **.** md** 파일입니다.
2. MarkDown 형식이 원시 텍스트 형식으로 표시되는 방식을 확인하여 여기에서 파일 콘텐츠를 계속 편집할 수 있습니다.

> **참고**: Wiki 소스 파일은 소스 코드로 처리되므로 기존 소스 제어(복제, 끌어오기 요청, 승인 등)의 모든 사례를 이제 Wiki 페이지에도 적용할 수 있습니다.

### 연습 2: 프로젝트 wiki 만들기 및 관리

이 연습에서는 프로젝트 wiki를 만들고 관리하는 단계를 안내합니다.

> **참고**: 기존 리포지토리와 독립적으로 wiki를 만들고 관리할 수 있습니다.

#### 작업 1: 인어공주 다이어그램 및 이미지를 포함한 프로젝트 위키 만들기

이 작업에서는 프로젝트 위키를 만들고 인어 다이어그램 및 이미지를 추가합니다.

1. 랩 컴퓨터의 **EShopOnweb 프로젝트의 위키 창**이 표시된 **Azure DevOps 포털의 **EShopOnWeb**(문서)** 위키 콘텐츠가 선택된 상태에서 창 맨 위에서 EShopOnWeb(문서)** 드롭다운 목록 머리글(화살표 아래쪽 아이콘)을 클릭하고 **드롭다운 목록에서 새 프로젝트 위키** 만들기를 선택합니다**.
2. **페이지 제목** 텍스트 상자에 프로젝트 디자인을** 입력**합니다.
3. 페이지 본문에 커서를 놓고 머리글 설정을 나타내는 도구 모음에서 가장 왼쪽 아이콘을 클릭한 다음 드롭다운 목록에서 머리글 1**을 클릭합니다**. 그러면 줄 시작 부분에 해시 문자(**#**)가 자동으로 추가됩니다.
4. 새로 추가된 **#** 문자 바로 뒤에 **Authentication and Authorization**을 입력하고 **Enter** 키를 누릅니다.
5. 머리글 설정을 나타내는 도구 모음에서 가장 왼쪽에 있는 아이콘을 클릭하고 드롭다운 목록에서 머리글 2**를 클릭합니다**. 그러면 줄 시작 부분에 해시 문자(**##**)가 자동으로 추가됩니다.
6. 새로 추가된 **##** 문자 바로 뒤에 **Azure DevOps OAuth 2.0 Authorization Flow**를 입력하고 **Enter** 키를 누릅니다.
7. **다음 코드를 복사하여 붙여** 넣어 위키에 인어 다이어그램을 삽입합니다.

    ```
    ::: mermaid
    sequenceDiagram
     participant U as User
     participant A as Your app
     participant D as Azure DevOps
     U->>A: Use your app
     A->>D: Request authorization for user
     D-->>U: Request authorization
     U->>D: Grant authorization
     D-->>A: Send authorization code
     A->>D: Get access token
     D-->>A: Send access token
     A->>D: Call REST API with access token
     D-->>A: Respond to REST API
     A-->>U: Relay REST API response
    :::
    ```

    >**참고**: 인어 구문에 대한 자세한 내용은 인어 정보 참조 [](https://mermaid-js.github.io/mermaid/#/)

8. 편집기 창 오른쪽의 미리 보기 창에서 다이어그램** 로드를 클릭하고 **결과를 검토합니다.

    >**참고**: 출력은 OAuth 2.0을 사용하여 REST API에 [대한 액세스 권한을 부여하는 방법을 보여 주는 순서도와 유사해야 합니다.](https://docs.microsoft.com/azure/devops/integrate/get-started/authentication/oauth)

9. 편집기 창의 오른쪽 위 모서리에서 저장 단추 옆에 **있는 아래쪽으로 향한 캐리트를 클릭하고 드롭다운 메뉴에서 수정 메시지**와 함께 저장을 클릭합니다**.**
10. **페이지 저장** 대화 상자에서 OAuth 2.0 인어 다이어그램**을 사용하여 인증 및 권한 부여 섹션을 입력**하고 저장**을 클릭합니다**.
11. **프로젝트 디자인** 편집기 창에서 이 작업의 앞부분에서 추가한 Mermaid 요소의 끝에 커서를 놓고 Enter** 키를 눌러 **추가 줄을 추가하고 머리글 설정을 나타내는 도구 모음에서 가장 왼쪽 아이콘을 클릭한 다음 드롭다운 목록에서 머리글 2**를 클릭합니다**. 그러면 줄 시작 부분에 이중 해시 문자(**##**)가 자동으로 추가됩니다.
12. 새로 추가된 **##** 문자 바로 뒤에 **User Interface**를 입력하고 **Enter** 키를 누릅니다.
13. 프로젝트 디자인 편집기 창의 **도구 모음에서 파일** 삽입 작업을 나타내는 **용지 클립 아이콘을 클릭하고 열기 대화 상자에서** **다운로드** 폴더로 이동하여 **이전 연습에서 다운로드한 Brand.png** 파일을 선택하고 **열기**를 클릭합니다**.**
14. **프로젝트 디자인** 편집기 창으로 돌아가서 미리 보기 창을 검토하고 이미지가 제대로 표시되는지 확인합니다.
15. 편집기 창의 오른쪽 위 모서리에서 저장 단추 옆에 **있는 아래쪽으로 향한 캐리트를 클릭하고 드롭다운 메뉴에서 수정 메시지**와 함께 저장을 클릭합니다**.**
16. 저장 페이지** 대화 상자에서 Tailwind Traders 이미지가** 있는 사용자 인터페이스 섹션을 입력**하고 저장**을 클릭합니다**.**
17. 편집기 창으로 돌아가서 오른쪽 위 모서리에서 닫기를** 클릭합니다**.

#### 작업 2: 프로젝트 위키 관리

이 작업에서는 새로 만든 프로젝트 wiki를 관리합니다.

>**참고**: 위키 페이지의 가장 최근 변경 사항을 되돌리기 시작합니다.

1. 랩 컴퓨터의 Azure DevOps 포털에서 EShopOnWeb** 프로젝트의 위키 창을** **표시**하고 프로젝트 디자인** 위키의 **내용이 선택된 상태에서 오른쪽 위 모서리에서 세로 줄임표 기호를 클릭하고 드롭다운 메뉴에서 수정 내용** 보기를 클릭합니다**.
2. 수정 내용** 창에서 **가장 최근 변경 내용을 나타내는 항목을 클릭합니다.
3. 결과 창에서 문서의 이전 버전과 현재 버전 간의 비교를 검토하고 되돌리기를 클릭합니다 **. 확인 메시지가 표시되면 [다시 되돌리기 **]를 클릭한 **다음 [페이지** 찾아보기]를 클릭합니다**.**
4. 프로젝트 디자인** 창으로 **돌아가서 변경 내용이 성공적으로 되돌리기 확인합니다.

    >**참고**: 이제 프로젝트 위키에 다른 페이지를 추가하고 위키 홈페이지로 설정합니다.

5. 프로젝트 디자인** 창의 **왼쪽 아래 모서리에서 + 새 페이지를** 클릭합니다**.
6. 페이지 편집기 창**의 페이지 제목** 텍스트 상자에 프로젝트 디자인 개요**를 입력**하고 저장**을 클릭한 **다음 닫기를** 클릭합니다**.
7. 프로젝트 디자인** 프로젝트 위키 내**의 페이지를 나열하는 창으로 돌아가서 프로젝트 디자인 개요** 항목을 찾아 **마우스 포인터로 선택하고 프로젝트 디자인** 페이지 항목 위로 **끌어서 놓습니다.
8. 나타나는 창에서 이동** 단추를 눌러 **변경 내용을 확인합니다.
9. **프로젝트 디자인 개요** 항목이 위키 홈 페이지로 지정된 홈 아이콘이 있는 최상위 페이지로 나열되는지 확인합니다.

## 검토

이 랩에서는 Markdown 콘텐츠 관리 및 인어 다이어그램 만들기를 포함하여 Azure DevOps에서 Wiki를 만들고 구성했습니다.
