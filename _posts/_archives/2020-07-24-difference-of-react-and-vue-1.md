---
layout: post
title:  "리액트와 뷰 - 1, 생김새"
author: ppamppamman
categories: [ Dev, Javascript, React, Vue ]
image: https://user-images.githubusercontent.com/13144573/84148736-5d95b800-aa9a-11ea-957f-226238dc6163.png
image_desc: "차이점 설명" 
---

## JSX 기반 스타일링, 마크업

### ```<template>```은 어디서 왔을까
템플릿이라는 개념은 서버사이드 엔진에서 자연스럽게 쓰고 있는 개념이다.  

[WhatWG]("https://html.spec.whatwg.org/multipage/scripting.html#the-template-element")에 따르면 ```<template>```을 다음과 같이 설명하고 있다.

> The template element is used to declare fragments of HTML that can be cloned and inserted in the document by script.  

>템플릿 요소는 스크립트로 문서에 복제 및 삽입 할 수있는 HTML 조각을 선언하는 데 사용된다.

MDN에서는 다음과 같이 설명하고 있다.
> HTML ```<template>``` 요소는 페이지를 불러온 순간 즉시 그려지지는 않지만, 이후 JavaScript를 사용해 인스턴스를 생성할 수 있는 HTML 코드를 담을 방법을 제공합니다.  

> 템플릿은 콘텐츠 조각을 나중에 사용하기 위해 담아놓는 컨테이너로 생각하세요. 페이지를 불러오는 동안 구문 분석기가 ```<template>``` 요소의 콘텐츠도 읽기는 하지만, 이는 유효성을 검증하기 위함이며 렌더링 하기 위함은 아닙니다.

```<template>``` 내에서 콘텐츠를 감싸는 것은 몇가지 중요한 속성들을 제공한다.

> 1. ```<template>```의 콘텐츠는 활성화가 될 때까지 효과적으로 비활성화됩니다. 기본적으로 여러분의 마크업 숨겨진 DOM이며 렌더링되지 않습니다.  

이게 무슨 말이냐면, 템플릿 태그로 감싸진 태그는 기본적으로 렌더 트리에 등록되지 않는다는 말이다.

> 2. 템플릿 안의 어떠한 컨텐츠라도 부작용을 가지지 않습니다. 템플릿이 사용될 때까지 스크립트는 실행되지 않으며 이미지는 로딩되지 않고 오디오는 재생되지 않는 등

기존의 부작용은 필요한 시점에 에셋이 로드되지 않거나 돔이 그려지기 전 스크립트가 돌아버리는 문제를 말한다.

> 3. 컨텐츠는 문서 내에 있지 않도록 고려되어야 합니다. 메인 페이지에서 document.getElementById()나 querySelector()의 사용은 템플릿의 자식 노드들을 반환하지 않을 것입니다.

템플릿 태그 내부에 선언된 요소에 만약 document레벨에서 노드 접근이 허용된다면, ~~~한 것을 못할 것이다.

> 4. 템플릿은 ```<head>```, ```<body>``` 혹은 ```<frameset>``` 내의 어느 곳에도 위치할 수 있습니다.

헤드 태그에서도 작동할 수 있다.