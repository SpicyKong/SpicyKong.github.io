---
layout: post
title: Notion to Blog
categories: Etc
tags: [Project]
---

## 1. 블로그를 시작하다


나는 불성실한 편이다. 꾸준히 무언가를 한다는 것은 나 같이 게으른 부류의 사람들에겐 크나큰 도전 이다. 그럼에도 이 도전을 시작하는 이유는 그동안 인터넷을 통해 공부를 한 것에 대한 나름의 감사라고 표현을 하는 편이 멋있겠지만, 그냥 어딘가에 나를 소개하고 싶었다. 앞으로 이 블로그를 통해 나의 관심사를 공유해 나갈 예정이다. 


### 1.1. 번거로움


막상 깃허브 블로그를 구성하고 글을 작성하려고 보니 크게 두 가지 문제점이 눈에 띄였다.

1. **글 작성 과정이 번거롭다.**

	`포맷에 맞게 파일명 작성` - `YAML 헤더 작성` - `글 작성(코드에디터에서)` - `이미지를 로컬에 저장한 뒤 첨부` - `깃에 push` 


	글을 작성할 때엔 위와 같은 과정을 거쳐야 한다. 새로운 장치에서는 다시 원격 저장소에 연결 하는 등의 세팅을 다시 해야 한다. 또한 내가 메인으로 사용하는 모바일 디바이스인 아이패드에서 깃을 자유롭게 사용하기 위해선 유료 앱을 구매해야 한다. 번거롭고 안멋지다.

2. **이미지 첨부도 번거롭다.**

	깃허브 저장소의 용량 제한은 대략 1GB ~ 5GB 정도라고 한다. 내 사용 패턴을 예상해 보았을때 조금만 신경을 쓴다면(PNG가 아닌 압축된 이미지 포맷만을 사용한다면), 이 용량도 충분히 넉넉한 편이다. 하지만 매번 이미지를 첨부할 때 신경을 써야 한다는 것이 피곤하게 느껴졌다 


	대안으로는 이슈 작성 창에 이미지를 첨부하면 생성되는 이미지 호스팅 링크를 이용하는 방법도 있었다. ([링크](https://stackoverflow.com/questions/18360714/official-image-host-for-github-projects)) 이렇게 한다면 리포지토리 용량 문제는 해결할 수 있다. 하지만 매번 이미지를 첨부할 때마다 깃허브 이슈 작성 창에 들어가 복붙 해야한다는 것 + 저 이미지가 언제 만료 될지도 모른 채 사용해야 한다는 사실이 마음에 들지 않았다.


물론 velog나 Tistory와 같은 블로그 서비스를 이용하면 말끔히 해결된다. 그러나 깃허브 블로그를 구축해 관리하는 쪽이 더 멋져 보였다. 그래서 위 두 문제를 해결할 수 있는지 찾아본 뒤, 너무 귀찮아 진다면 넘어가려고 했었다.


### 1.2. 개선할 수 있을까?

1. **글 쓰기 개선**

	재작년부터 노션이라는 메모 서비스를 유용하게 사용하고 있다. 언제 어디서나 로그인만으로 쉽게 내 노트에 접근이 가능하다는 점과 글 작성 환경이 직관적이라는 점이 특히나 마음에 들었다. 


	그런데 노션에서는 [API](https://developers.notion.com/docs/getting-started)를 제공하고 있다. API를 통해 내가 작성한 페이지의 마크다운을 얻어올 수 있다. 여기에 [깃허브 API](https://docs.github.com/en/rest)도 함께 사용하면, 노션에 글을 작성한 뒤 블로그에 글을 게시하는 것이 가능해 보였다. 이렇게 한다면 첫 번째 이슈를 해결할 수 있을 것 같았다.

2. **이미지 개선**

	의외로 이미지 문제도 쉬운 해결책이 보였다. 노션 페이지에 이미지를 붙여 넣으면 그 이미지는 노션 자체 호스팅 서버에 저장된다. 처음에는 이미지 원본 링크를 외부에 공유했을 때 접근이 되지 않아 불가능한 줄 알았는데, 해당 페이지가 `Publish` 된 경우 외부에서도 접근이 가능했다. ([Publish?](https://www.notion.so/ko-kr/help/public-pages-and-web-publishing)) 그러니깐 두 번째 이슈도 해결할 기미가 보였다.


노션만세


## 2. 일단 결과물부터..


지금 이 글은 다음과 같은 과정을 거쳐 내 블로그에 게시가 된다. 


### **첫 번째, 먼저 블로그 글을 관리하는 데이터베이스에 새 글을 작성한다.**


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2Fffe547f4-a97d-4db8-90b3-1c9e030349c0%2FUntitled.png?table=block&id=d562041d-6fac-4683-b748-e08813868bc1&userId=&cache=v2)


### **두 번째, 해당 페이지에 적용될 동작 선택**

- `Nothing` 다음 업데이트 때, 아무것도 하지 않음.
- `Publish` 다음 업데이트 때, 해당 페이지를 블로그에 게시함
- `Unpublish` 다음 업데이트 때, 해당 페이지와 동일한 제목&날짜를 가지는 게시물을 삭제

아래처럼 게시물 마다 동작을 세팅해두면 된다.


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2F9dc0c517-ae3b-4f18-b0f9-69b88f2c2e53%2FUntitled.png?table=block&id=112911e4-50c8-4ce1-b7a4-8c5623ecd432&userId=&cache=v2)


### **세 번째, 업데이트 반영**


나는 깃허브 액션을 이용해, 수동으로 업데이트 코드가 동작 되도록 설정했다. (아래 화면에서 초록색 버튼을 누르면 업데이트가 시작된다.) 게시물이 성공적으로 업데이트 된 경우, 글의 상태가 `Nothing`으로 변경되도록 만들었다.


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2F674b0eda-c5f8-432b-9cf6-36ffc9be63d3%2FUntitled.png?table=block&id=8352628d-4997-4ae2-8174-5bd54d1f3686&userId=&cache=v2)


(아래 사진을 보면 모두 상태가 Nothing으로 변경되었다.)


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2F6b6e15ac-c9b3-480d-b01b-f235535cfaf0%2FUntitled.png?table=block&id=062edc6a-3bf3-4cae-98d8-10c4e427253f&userId=&cache=v2)


### **네 번째, 글 게시 완료! 🎉🎉**


게시된 글을 감상하면 된다.


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2F809c79d3-8e0e-4bca-8ecf-b79c67e36639%2FUntitled.png?table=block&id=ec3a6095-245d-4742-a756-ddc85c45e67c&userId=&cache=v2)


## 3. 개발 과정


먼저, [Notion2Blog](https://github.com/SpicyKong/Notion2Blog)는 아래 3가지 서비스를 이용해 만들었다. 각 서비스 별 역할은 다음과 같다.

- [**Notion API**](https://developers.notion.com/reference): 노션 글 가져오기, 글 게시 후 페이지 상태 업데이트
- [**Github API**](https://docs.github.com/en/rest?apiVersion=2022-11-28): 깃허브 리포지토리에 글 업로드(혹은 업데이트) 및 삭제
- [**Github Action**](https://docs.github.com/ko/actions): 코드를 원하는 이벤트에 맞춰 동작 시킬 수 있음.

### 3.1 Notion API


노션 API를 처음 들었을 때는 이제 모든게 해결되었다고 생각했다. 하지만 기대와는 노션 API를 가지고 마크다운 파일을 얻기가 좀 번거롭다. 각 요소가 블럭 이라는 객체로 구성되어 있기 때문에 마크다운을 얻어오려면 처리해줘야 할 것이 많다. 


다행히도 어떤 착한분께서 이미 이 귀찮음을 해결해 놓으셨다. [notion-to-md](https://github.com/souvikinator/notion-to-md) 라는 Node.js 라이브러리를 이용하면 손쉽게 원하는 페이지의 마크다운을 얻어올 수 있다. (찾아보니 [Python 오픈소스도 있다](https://github.com/echo724/notion2md)) 


**3.1.1. Notion API - 마크다운 페이지 얻기**


먼저, 페이지 아이디로 마크다운 텍스트를 얻어오는 방법을 살펴보자. 


(Client라는 객체는 [@notionhq/client](https://www.npmjs.com/package/@notionhq/client)에 정의되어 있는데, 이 라이브러리를 이용하면 노션 API를 더욱 쉽게 사용할 수 있다, notion-to-md 라이브러리를 사용하려면 해당 라이브러리가 필요하다.)


```javascript
let notion = new Client({auth:노션 API 토큰});
let n2m = new NotionToMarkdown({notionClient:notion});

console.log(n2m.pageToMarkdown(원하는 페이지 아이디).parent);
// 마크다운이 출력된다. (간단하다)
```


**3.1.2. Notion API - 원본 이미지 얻기**


문제는 위와 같이 이미지 블록에 있는 URI를 이용해 마크다운 텍스트를 긁어온 경우, 얼마 지나지 않아 주소가 만료되어 이미지를 볼 수 없게 된다. 따라서 우리는 원본 이미지에 접근을 해야 한다.


우리가 사용한 notion-to-md라는 라이브러리에는 확장성을 고려해 `setCustomTransformer` 라는 메소드를 제공한다. 이 메소드를 이용하면 마크다운 텍스트가 구성될 때, 특정 블록을 처리하는 함수를 설정할 수 있다. 


그래서 나는 이미지 블록을 만나면, 아래와 같이 원본 이미지 주소가 삽입되도록 수정했다. 이렇게 하면 이미지 만료 걱정을 하지 않아도 된다.


```javascript
n2m.setCustomTransformer("image", async (block) => {
    let uri:string = block.image.file.url.split("?", 1)[0]+"?table=block&id=" + block.id + "&userId=&cache=v2";
    uri = uri.replace(/\//g, "%2F");
    uri = uri.replace(":", "%3A");
    let notion_image_url:string = process.env.NOTION_DOMAIN + "/image/";

    return `![Untitled.png](${notion_image_url}${uri})`;
});
```


**3.1.3. Notion API - 페이지 정보 얻기**


다음은 업데이트 할 글을 모두 가져온 뒤, 각 글의 페이지 아이디를 얻는 예제이다. `Action` 이라는 속성이 `Publish` 라면 해당 글을 가져오는 코드이다. 글이 여러 개 일 수 있기 때문에 배열로 반환된다.


```javascript
// 데이터베이스에서 업데이트할 글들을 가져온 뒤, 각 글의 속성에 접근하는 예시
const notion = new Client({auth: 노션 API 토큰}); // 처음 예시 코드처럼 Client를 생성하면 된다.
let res = notion.databases.query({
	database_id: 노션 데이터베이스 아이디,
	filter: {
		property: "Action", // 필터링할 데이터베이스 컬럼
		status: {
			equals: "Publish" // 컬럼 값이 Publish이면 가져옴
		}
	}
});

for (let page of res.results) {
    let pageId = page.id; // 손 쉽게 페이지 아이디 얻음.
} 
```


**3.1.4. Notion API - 페이지 속성 얻기 & 업데이트 하기**


지킬에서는 모든 포스트에 다음과 같은 양식의 YAML 헤더 정보가 필요하다. 이 헤더를 통해 제목, 부제목, 태그, 날짜 등 다양한 속성을 지정할 수 있다. 


```yaml
// YAML 헤더 예시
---
layout: post
title: 안녕하세요
categories: Thinking
tags: [노션, 블로그]
---
```


하지만 모든 노션 페이지마다 저런 텍스트가 있는 것은 예쁘지 않다. 그래서 나는 이러한 헤더 정보는 속성에 저장했다. 속성을 이용하면 아래 사진처럼 깔끔하게 관리할 수 있다.


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2F4700785a-88d8-4c0f-9ce0-08178d467573%2FUntitled.png?table=block&id=ec67afee-81d1-4131-87f0-bc637150a1a2&userId=&cache=v2)


(속성은 데이터 베이스 뷰에서 아래와 같이 보인다.)


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2F06f2592f-5628-4015-b3cc-65212a1c13e2%2FUntitled.png?table=block&id=6080ad21-60f4-4422-a887-6e5e23dadec0&userId=&cache=v2)


페이지의 속성을 얻는 방법은 페이지 아이디를 얻는 방식과 동일하다. `properties`라는 객체에 해당 페이지의 속성 정보가 모두 담겨있다. 다음 예제 코드를 살펴보자.


```javascript
// 데이터베이스에서 업데이트할 글들을 가져온 뒤, 각 글의 속성에 접근하는 예시
const notion = new Client({auth: 노션 API 토큰}); // 처음 예시 코드처럼 Client를 생성하면 된다.
let res = notion.databases.query({
	database_id: 노션 데이터베이스 아이디,
	filter: {
		property: "Action", // 필터링할 데이터베이스 컬럼
		status: {
			equals: "Publish" // 컬럼 값이 Publish이면 가져옴
		}
	}
});

for (let page of res.results) {
    let title  = page.properties.Date.date.start + "-" + page.properties.Name.title[0].text.content+".md";
    let realTitle = page.properties.Name.title[0].text.content;
    let categories = page.properties.Categories.select.name;
    let tags = Array.from(page.properties.Tags.multi_select, (tag) => tag["name"]).toString();
    
    /// ----- 여기부턴 페이지 속성 업데이트 코드 ----- ///
    page.properties.Action.status.name = "Nothing";
    page.properties.Action.status.color = "gray";
    page.properties.Published.checkbox = true;
    delete page.properties.Action.status.id; // update할 때에는, id 정보를 삭제하지 않으면 오류를 반환한다.
    await notion.pages.update({page_id:page.id, properties: page.properties});

} 
```


추가로 위 예제 코드에서 주석 이후의 코드는 속성을 업데이트하는 코드이다. 나의 경우 포스트가 깃허브에 성공적으로 게시된 경우 해당 페이지의  `Action` 속성이 `Nothing`으로 변경되게 만들었다.


### 3.2. Github API


**3.2.1. Github API - 새 글 Add**


다음 예제 코드는 Github API를 이용해 원하는 저장소에 글을 업로드 하는 예제이다. 주의 할 점은 content를 base64로 인코딩해 전달해야 하는데, 그냥 인코딩하면 ASCII 범위를 벗어나는 문자는 깨질 수 있다. 그래서 아래와 같이 사용한다고 한다.


```javascript
let headers = new Headers;
headers.set("Accept", "application/vnd.github+json");
headers.set("Authorization", "Bearer " + process.env.GIT_TOKEN);
let httpInfo = {
	method: "PUT",
	headers: headers,
	body: {
		message: "커밋 메시지",
		content: btoa(unescape(encodeURIComponent("마크다운 파일 본문 전체 스트링")))
	}
};

fetch(this.endpoint+ "유저이름/리포이름" + "/contents/" + "파일이름", httpInfo);
```


**3.2.2. Github API - 기존 글 Update**


기존 글의 내용을 변경하는 경우에는 추가로 body에 SHA 코드 라는 것을 전달하면 된다. 그 외에는 위의 코드와 동일하다.


```javascript
let headers = new Headers;
headers.set("Accept", "application/vnd.github+json");
headers.set("Authorization", "Bearer " + process.env.GIT_TOKEN);
let httpInfo = {
	method: "PUT",
	headers: headers,
	body: {
		message: "커밋 메시지",
		content: btoa(unescape(encodeURIComponent("마크다운 파일 본문 전체 스트링"))),
		sha: "sha 코드"
	}
};

fetch(this.endpoint+ "유저이름/리포이름" + "/contents/" + "파일이름", httpInfo);
```


**3.2.2. Github API - 기존 글 delete**


기존 글의 내용을 삭제하는 방식도 위와 비슷하다. Method만 `Delete`로 바꿔주면 된다. 


```javascript
let headers = new Headers;
headers.set("Accept", "application/vnd.github+json");
headers.set("Authorization", "Bearer " + process.env.GIT_TOKEN);
let httpInfo = {
	method: "DELETE",
	headers: headers,
	body: {
		message: "커밋 메시지",
		sha: "sha 코드"
	}
};

fetch(this.endpoint+ "유저이름/리포이름" + "/contents/" + "파일이름", httpInfo);
```


**3.2.2. Github API - SHA 코드 Get**


아래 예제는 `sha`코드를 얻어오는 예제이다. (참고로, 그냥 실행시 결과가 이상할 수 있으니 비동기에 대한 이해를 한 후 수정해서 사용하면 된다.)


```javascript
let headers = new Headers;
headers.set("Accept", "application/vnd.github+json");
headers.set("Authorization", "Bearer " + process.env.GIT_TOKEN);
let httpInfo = {
	method: "GET",
	headers: headers
};

let res = fetch(this.endpoint+ "유저이름/리포이름" + "/contents/" + "파일이름", httpInfo);
// res.sha에 저장되어 있음.
```


> 💡 **추가로, Github API 사용할 때, 요청 빈도가 너무 잦으면 conflict가 발생할 수 있다. 이 점을 주의하자.**


### 3.3. Github Action


Github Action을 이용하면 내가 원하는 코드를, 이벤트 발생 시, 실행 시킬 수 있다. 매 시간마다 실행이 되도록 할 수도 있는데, 나는 내가 깃허브 액션을 직접 실행하는 경우에만 실행되도록 세팅했다.


**3.3.1. Github Action - 시크릿 키 사용하기**


API 키와 같은 민감한 정보들을 리포지토리에 공개하면 당연히 안된다. 깃허브 액션에서는 이러한 민감한 정보를 손수비게 관리해주는 `secrets`이라는 것이 있다. `리포지토리`- `Settings` - `Secrets and variables` - `Actions` 를 통해 접근할 수 있다. 


나는 여기에 API키와 같은 정보들을 저장 한 뒤 사용했다.


![Untitled.png](https://spicykong.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbc55d29d-9bcf-4907-ad9e-d252dc4154e4%2Fd83808ad-a988-4823-971d-6601b5ba3be4%2FUntitled.png?table=block&id=5c59b9c5-1e9f-4991-a850-343e10910383&userId=&cache=v2)


**3.3.2. Github Action - Workflow 만들기**


워크플로우에 대한 자세한 구문은 다른 잘 설명된 자료가 많으니 그걸 이용하자.. 일단 나는 아래와 같이 작성했다. 주석을 보면 대략 흐름을 알 수는 있다.


```yaml
name: Blog Manager

on:
  workflow_dispatch: # 수동으로 동작 시키기

jobs:
  update:

    runs-on: ubuntu-latest # 우분투 최신 버전 사용
    env:  # 시크릿 키를 환경 변수에 등록
      FILE_PATH: ${{ secrets.FILE_PATH }}
      GIT_REPO_URL: ${{ secrets.GIT_REPO_URL }}
      GIT_TOKEN: ${{ secrets.GIT_TOKEN }}
      NOTION_DB_ID: ${{ secrets.NOTION_DB_ID }}
      NOTION_TOKEN: ${{ secrets.NOTION_TOKEN }}
      NOTION_DOMAIN: ${{ secrets.NOTION_DOMAIN }}
      
    strategy:
      matrix:
        node-version: [21.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
    - name: Download Notion2Blog # 업데이트 시키는 코드 다운로드
      uses: actions/checkout@v3 
      with:
        repository: SpicyKong/Notion2Blog
        
    - name: Use Node.js ${{ matrix.node-version }} # node.js 환경 구성
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - name: Install packages # 패키지 설치
      run: npm install -production
    - name: Update all changes # 코드 실행(config 파일에서 설정한대로)
      run: npm run update --if-present

```


## 4. 끝


끝

