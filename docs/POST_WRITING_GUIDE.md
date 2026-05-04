# Post Writing Guide

이 저장소의 글은 `docs/_posts` 아래에 Jekyll post 형식으로 작성합니다.

## 파일 위치와 이름

새 글은 아래 형식으로 생성합니다.

```text
docs/_posts/YYYY-MM-DD-post_slug.md
```

예시:

```text
docs/_posts/2026-05-04-react-query-cache.md
```

파일명 규칙:

- 날짜는 `YYYY-MM-DD` 형식을 사용합니다.
- slug는 영어 소문자, 숫자, 하이픈 또는 언더스코어만 사용합니다.
- 공백, 한글 파일명, 특수문자는 피합니다.

## Front Matter

모든 post 파일의 맨 위에는 아래 YAML front matter를 넣습니다.

```yaml
---
layout: post
title: "글 제목"
date: 2026-05-04 00:00:00 +0900
categories: jekyll update
tags: [frontend, react, performance]
---
```

필드 규칙:

- `layout`: 항상 `post`
- `title`: 포스트 목록과 글 상세 페이지에 표시되는 제목
- `date`: 파일명 날짜와 맞추고, 시간대는 `+0900`
- `categories`: 기존 URL 구조 유지를 위해 `jekyll update` 사용
- `tags`: 포스트 목록 상단 태그 필터에 노출됨

권장 태그:

```text
frontend
react
javascript
typescript
css
html
performance
tooling
eslint
vite
npm
yarn
package-manager
pnp
rendering
async-state
api
media
semantics
date
```

## 본문 구조

본문 첫 줄에는 제목을 한 번 더 씁니다.

```markdown
# 글 제목
```

기본 구조는 아래 순서를 권장합니다.

````markdown
# 글 제목

## What it is

개념을 짧게 설명합니다.

## Why it matters

이 개념이 왜 필요한지 설명합니다.

## How it works

동작 방식, 흐름, 예시를 설명합니다.

## Example

```js
const example = "code";
```

## Key Takeaways

- 핵심 요약 1
- 핵심 요약 2
- 핵심 요약 3
````

## 작성 스타일

- `#` 제목은 글 전체에서 한 번만 사용합니다.
- 큰 단락은 `##`, 세부 항목은 `###`를 사용합니다.
- 코드, 명령어, 파일명은 백틱으로 감쌉니다.
- 코드 블록에는 언어를 명시합니다. 예: ` ```js `, ` ```bash `
- 목록은 짧고 명확하게 작성합니다.
- 표는 비교가 필요할 때만 사용합니다.
- 포스트 제목과 첫 번째 `#` 제목은 가능하면 동일하게 맞춥니다.

## 템플릿

새 글 작성 시 아래를 복사해서 시작합니다.

````markdown
---
layout: post
title: "새 글 제목"
date: 2026-05-04 00:00:00 +0900
categories: jekyll update
tags: [frontend]
---

# 새 글 제목

## What it is


## Why it matters


## How it works


## Example

```js
// example
```

## Key Takeaways

- 
- 
- 
````

## 주의사항

- `docs/_posts/portfolio.md`는 포트폴리오 원문으로 쓰는 파일이며 일반 post 목록에서는 제외되어 있습니다.
- 포트폴리오 내용은 `docs/portfolio.markdown`에서 불러옵니다.
- 일반 기술 글만 `docs/_posts/YYYY-MM-DD-title.md` 형식으로 추가합니다.
