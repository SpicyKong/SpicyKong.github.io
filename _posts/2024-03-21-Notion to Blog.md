---
layout: post
title: Notion to Blog
categories: Etc
tags: [Project]
---

`노션 글 작성` - `딸깍` - `노션 글 가져오고` - `Github로 업로드` - `게시 완료`  를 목표로..


![Untitled.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/bc55d29d-9bcf-4907-ad9e-d252dc4154e4/1e821e4f-5275-466c-901a-d7c43629f86f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45HZZMZUHI%2F20240322%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20240322T031626Z&X-Amz-Expires=3600&X-Amz-Signature=12b68c2b52deab3c4c1871c4d04e727d294c0950d97a48c6b39d968699acb040&X-Amz-SignedHeaders=host&x-id=GetObject)


## 1. 노션 글 작성


글을 자동으로 관리하기 위해 나름의 규칙을 만들었다. 이 규칙만 따른다면 앞으로 다른 번거로운 일을 하지 않고도 블로그를 관리할 수 있다.

- 블로그 글은 노션의 특정 데이터베이스에만 작성한다.
- 글의 각 속성은 다음과 같다.
	- `Action` 다음번 업데이트때 이 글을 어떻게 할 지에 대한 동작이다. (Nothing, Publish, Unpublish) 만약 해당 Action을 완수했다면 Nothing으로 초기화된다.
	- `Published` 위 Action의 동작을 완수했다면 check된다.
	- `Categories` 카테고리
	- `Tags` 태그
	- `Date` 작성일
	- `Author` 작성자

## 2. 딸깍 - 노션 글 가져오기


노션의 페이지는 기대와는 달리 마크다운 파일을 얻기가 번거롭다. 페이지가 블럭이라는 객체로 구성되어 있기 때문에 직접 파싱해야 한다. 나는 [notion-to-md](https://github.com/souvikinator/notion-to-md) 라는 Javascript 라이브러리를 이용해 손쉽게 이 번거로움을 해결했다.([Python 오픈소스도 있다](https://github.com/echo724/notion2md)) 


> 💡 위 라이브러리는 [@notionhq/client](https://www.npmjs.com/package/@notionhq/client) 라는 라이브러리 기반으로 만들어져 있는데, 자바스크립트에서 노션 API를 사용할 일이 있다면 저 API를 이용하는 것을 추천한다. 저 라이브러리를 통해 간편하게 Notion API를 이용할 수 있다.


다음은 [notion-to-md](https://github.com/souvikinator/notion-to-md) 라이브러리를 이용해 원하는 페이지의 마크다운 정보를 가져오는 예제 코드이다.


```javascript
let notion = new Client({auth:노션 API 토큰});
let n2m = new NotionToMarkdown({notionClient:notion});

console.log(n2m.pageToMarkdown(원하는 페이지 아이디));
```


하지만 지킬에서는 모든 글에 다음과 같은 양식의 YAML 헤더 정보가 필요하다. 


```yaml
---
layout: post
title: 안녕하세요
tags: [노션, 블로그]
---
```


이 정보를 관리하고자 앞서 여러 속성을 지정했었다. 나는 각 속성은 [@notionhq/client](https://www.npmjs.com/package/@notionhq/client) 라이브러리를 이용해 가져왔다. 다음은 속성을 가져오는 예제 코드이다.


```javascript
const notion = new Client({auth: 노션 API 토큰});
let res = notion.databases.query({
	database_id: 노션 데이터베이스 아이디,
	filter: {
		property: propertyName, // 필터링할 데이터베이스 컬럼
		status: {
			equals: propertyValue // 컬럼 값이 같으면 가져옴
		}
	}
});

console.log(res.results);
// 여러 속성 정보가 배열의 형태로 담겨 있다.
```


위 두 예제를 참고하면 게시할 본문의 텍스트를 얻는데 까지는 성공할 수 있다.


## 3. 딸깍 - 깃허브 업데이트


내가 블로그를 관리하는데에 필요한 동작은 [글 업로드, 글 수정](https://docs.github.com/ko/rest/repos/contents?apiVersion=2022-11-28#create-or-update-file-contents), [글 삭제](https://docs.github.com/ko/rest/repos/contents?apiVersion=2022-11-28#delete-a-file) 총 3가지 동작이다. 각 동작은 모두 깃허브 API 문서에 친절하게 설명되어 있다. 주의할 점은 각 요청 빈도가 너무 잦으면 conflict가 발생하며 409 Code가 반환될 수 있다. 그래서 나는 요청이 동시에 한 번만 실행되도록 동기화 시켜주었다. 


먼저, 업로드 예제를 살펴보자.


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


이때 github로 본문 내용을 업로드 하려면 base64로 인코딩해 전달해야 한다. 그러나 한글이나 이모지의 경우 Ascii 범위를 벗어나기 때문에 그냥 전달하면 안된다. 찾아보니 `btoa(unescape(encodeURIComponent(본문)))` 와 같은 방법으로 전달하면 된다고 한다.


다음으로 업데이트와 삭제에는 `sha`코드가 필요하다. 다음 예제는 `sha`코드를 얻어오는 예제이다. (참고로, 그냥 실행시 결과가 이상할 수 있으니 비동기에 대한 이해를 한 후 수정해서 사용하면 된다.)


```javascript
let headers = new Headers;
headers.set("Accept", "application/vnd.github+json");
headers.set("Authorization", "Bearer " + process.env.GIT_TOKEN);
let httpInfo = {
	method: "GET",
	headers: headers
};

let res = fetch(this.endpoint+ "유저이름/리포이름" + "/contents/" + "파일이름", httpInfo);
console.log(res.sha);
```


## 4. 딸깍 - 노션 글 상태 업데이트


깃허브에 글을 업로드 했다면 이제 노션 글의 상태를 업데이트 해야한다. 이 역시 [@notionhq/client](https://www.npmjs.com/package/@notionhq/client) 라이브러리를 이용하면 손 쉽게 할 수 있다. 다음은 프로퍼티 객체를 얻은 상태에서 페이지의 속성을 업데이트 하는 예제이다.


```javascript
page.properties = 위에서 얻은 프로퍼티 객체
page.properties.Action.status.name = "Nothing";
page.properties.Action.status.color = "gray";
page.properties.Published.checkbox = true;
delete page.properties.Action.status.id; // 아이디 값을 삭제한 후 전송을 해야 오류가 나지 않는다.

await notion.pages.update({page_id:page.id, properties: page.properties});
```


이제 이 프로그램을 이용하면 내 블로그를 손쉽게 관리할 수 있다!


## 5. Github Action


문제는 블로그를 업데이트 하기 위해선, 이 프로그램이 실행 되어야 한다. 문제는 이 코드를 항상 . 이 문제에 대한 멋진 해결 방법은 Github Action을 이용하는 것이다. Github Action을 이용하면 특정 이벤트가 발생했을시 내가 사전에 설정한 코드를 동작시킬 수 있다. 


작성중..


테스트 할겸 미리 업로드

