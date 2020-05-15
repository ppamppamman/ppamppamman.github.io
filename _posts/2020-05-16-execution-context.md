---
layout: post
title:  "Javascript 실행 컨텍스트"
author: ppamppamman
categories: [ TIL, Javascript ]
image: https://user-images.githubusercontent.com/13144573/82100972-23105800-9746-11ea-87e7-1b24a69b6d7d.png
image_desc: "일기장 아님 아무튼 아님" 
---

실행되고 있는 것에 대한 문맥. 말이 참 어렵다.  

```js
let elsa = new Elsa("갑");
let ppamppam = new Ppamppam("을");

function Company(){
  function ElsaSaid() {
    let work = new Work("하기 싫음");
    console.log("빰빰맨님 이 업무좀 해주세요.");
    
    function ppamppam_do() {
      ppamppam.hardwork(work);
    }
  }
  ElsaSaid();
}
Company();
```  

엘사님이 나한테 업무 요청을 했다.  
이 경우를 처리하는 엔진이 자바스크립트 엔진이라면, 여기에서 필요한 것은 다음과 같이 정리될 수 있다.

0) 엘사와 빰빰맨을 지칭할 수 있어야 한다.  
1) 엘사가 말하려는 내용을 생각했다.  
2) 엘사가 말했다.  
3) 부탁하고자 하는 내용을 어떻게 해달라고 부탁했다.  

모든 함수에는 유효 범위가 있다. 스코프이다. 자바스크립트의 경우 스코프를 이해햐기 위해서는 실행 컨텍스트를 이해해야 한다.  

```js
...
Company();
```  

Company() 함수가 호출 된다. 

```js
function Company(){
  let elsa = new Elsa("갑");
  let ppamppam = new Ppamppam("을");
  function ElsaSaid() {
    let work = new Work("하기 싫음");
    console.log("빰빰맨님 이 업무좀 해주세요.");
    ppamppamMustDo();
  }
  function ppamppamMustDo() {
      ppamppam.hardWork(work);
    }
  ElsaSaid();
}
```  
Company();가 호출되었고, 변수가 할당되며 직후 function 키워드를 만난다.  
ElsaSaid Function 오브젝트를 생성하고 ElsaSaid의 [[Scope]]에 스코프가 설정된다.
``` [[Scope]]```는 엔진이 설정하는 프로퍼티이다.  
이 때 스코프는 ElsaSaid가 속한 영역으로, Company Function 오브젝트의 범위이다.  

그러고 나서 ElsaSaid()함수가 호출된다.  
엔진컨트롤이 ElsaSaid()로 들어가기 전에 ElsaSaid의 실행 컨텍스트를 만든다.
함수 실행을 위한 컨텍스트 환경을 구축하게 된다.  

실행 컴포넌트 안에 렉시컬 환경 컴포넌트가 생성된다. 구조화하면 아래와 같다.
```js
실행컴포넌트 {
  렉시컬 환경 컴포넌트 {

  }
}
```

ElsaSaid에서 변수를 만났다. 이 때는 환경 레코드 안의 선언적 환경레코드에 key:value 형태로 저장된다. 구조화하면 아래와 같다.  
```js
실행컴포넌트 {
  렉시컬 환경 컴포넌트 {
    환경 레코드 {
      선언적 환경 레코드 {
        work : Object
      }
    }
  }
}
```  

ElsaSaid의 스코프에 ppamppamMustDo()는 이미 선언이 되었다. ppamppamMustDo()는 ElsaSaid()의 밖에 있는 것이지만 ElsaSaid 안에서 ppamppamMustDo()를 호출할 수 있는 것이다. 이 때는 외부 렉시컬 환경 참조를 이용한다. 이를 구조화 하면 다음과 같다.

```js
실행컴포넌트 {
  렉시컬 환경 컴포넌트 {
    환경 레코드 {
      선언적 환경 레코드 {
        work : Object
      }
    },
    외부 렉시컬 환경 참조 {
      elsa : Object 갑,
      ppamppam : Object 을,
      ppamppamMustDo : function(){}
    }
  }
}
```  
실행 컨텍스트는 스코프와 그 참조에 대한 덩어리를 만드는 것이라 이해해야 한다.
함수가 메모리에 올라가고 메모리에서 실행될 때, 함수외부의 값을 구하기 위해 메모리를 빠져나오거나, 메모리에 새로 올리거나 할 이유가 없다.  
렉시컬 컨텍스트를 정적이라고 지칭하는 이유가 이것이다. 

정리하면 아래와 같다.  
0) 엘사와 빰빰맨을 ElsaSaid 함수에서 지칭할 수 있어야 한다. = 외부 렉시컬 환경 참조를 활용한다.  
1) 엘사가 말하려는 내용을 생각했다. = 함수의 단순 선언.  
2) 엘사가 말했다. = 함수의 호출과 동시에 실행 컨텍스트의 생성  
3) 부탁하고자 하는 내용을 어떻게 해달라고 부탁했다. = ElsaSaid 함수의 line by line 호출.