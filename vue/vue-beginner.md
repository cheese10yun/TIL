## Vue 입문

## 목차
- [Vue 입문](#vue-%EC%9E%85%EB%AC%B8)
- [목차](#%EB%AA%A9%EC%B0%A8)
- [Vue는 무엇인가 ?](#vue%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
- [MVVM 패턴 이란?](#mvvm-%ED%8C%A8%ED%84%B4-%EC%9D%B4%EB%9E%80)
- [Vue Instance](#vue-instance)
- [Vue 라이프싸이클](#vue-%EB%9D%BC%EC%9D%B4%ED%94%84%EC%8B%B8%EC%9D%B4%ED%81%B4)
	- [Vue 객체가 생성될될 때 아래의 초기화 작업을 수행한다.](#vue-%EA%B0%9D%EC%B2%B4%EA%B0%80-%EC%83%9D%EC%84%B1%EB%90%A0%EB%90%A0-%EB%95%8C-%EC%95%84%EB%9E%98%EC%9D%98-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%9E%91%EC%97%85%EC%9D%84-%EC%88%98%ED%96%89%ED%95%9C%EB%8B%A4)
- [Vue Components](#vue-components)
	- [Global or Local Components](#global-or-local-components)
	- [차이점](#%EC%B0%A8%EC%9D%B4%EC%A0%90)
- [부모 자식 컴포넌트 관계](#%EB%B6%80%EB%AA%A8-%EC%9E%90%EC%8B%9D-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EA%B4%80%EA%B3%84)
- [Props](#props)
- [같은 레벨의 콤포넌트 간의 통신](#%EA%B0%99%EC%9D%80-%EB%A0%88%EB%B2%A8%EC%9D%98-%EC%BD%A4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EA%B0%84%EC%9D%98-%ED%86%B5%EC%8B%A0)
- [Event Buns 컴포넌트 간 통신](#event-buns-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EA%B0%84-%ED%86%B5%EC%8B%A0)
- [Vue Routers](#vue-routers)
- [Nested Routers](#nested-routers)
- [Named Views](#named-views)
- [Nested View vs Named Views](#nested-view-vs-named-views)
- [Vue Templates](#vue-templates)
	- [Atrributes](#atrributes)
	- [JS Expressions](#js-expressions)
	- [Directives](#directives)
	- [Filters](#filters)
- [Vue 데이터 바인딩](#vue-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B0%94%EC%9D%B8%EB%94%A9)
	- [Data Binding](#data-binding)
	- [Interpolation - 값 대입](#interpolation---%EA%B0%92-%EB%8C%80%EC%9E%85)
	- [Binding Expressions - 값 연결](#binding-expressions---%EA%B0%92-%EC%97%B0%EA%B2%B0)
	- [Directives](#directives-1)
	- [Class Binding](#class-binding)
	- [Single File Components](#single-file-components)
	- [Vue Loader](#vue-loader)

## Vue는 무엇인가 ?

* MVVM 패턴의 ViewModel 레이어 해당하는 View 단 라이브러리
* 화면에 요소가 변경이 되면 뷰 모델의 돔 리스너를 거쳐서 모델로 신호가 감
* 데이터 바인딩 과 화면 단위를 컴포넌트 형태로 제공하며, 관련 API 지원하는데 궁궁적인 목적이 있다.
* Angular 에서 지원하는 2 way data bindings 을 동일하게 제공
* Componet 간 통신 기본 공격은 React의 1 way data flow 비슷
* Virtual Dom 을 이용한 렌더링 방식 React 와 거의 유사

## MVVM 패턴 이란?

backend 로직과 Clinet 의 마크업 & 데이터 표현단을 분리하기 의한 구조로 전통적인 MVC 패턴의 방식에 기인하였다. 간단하게 생각하서 화면 앞단의 화면 동작 관련 로직과 뒷단의 DB 데이터 처리 및 서버 로직을 분리하고, 뒷단에서 넘어온 데이터를 Model에 담아 View로 넘겨주는 중간 지점이라고 보면 되겠다.

## Vue Instance

* Vie.js 라이브러리를 로딩했을 때 존재하는 Vue 생성자로 인스턴를 생성해야한다.
```javascript
// vm은 ViewModel을 뜻한다.
var vm = new Vue({
	//option
})
```
* 의를 풀어서 애기하면 Vue 라이브러리 로딩후 접근 가능한 Vue 라는 기존 객체에, 화면에서 사용할 옵션 (데이터, 속석, 메서드 등등)을 포함하여 화면의 단위를 생성한다.

```javascript
var vm = new Vue({
	template: ...,
	el: ...,
	metohds: {},
	created: {}
	//....
})
```

* `template` : 화면에 나타는 요소들 (div, p ...) + Model(뒷단) 값을
* `el` : 엘리먼트, id, class
* `metohds` : even(click, etc...)
* `created` : 라이프 사이클
* 각 option 으로 미리 정의한 Vue 객체를 확장하여 재사용이 가능하다. 하지만 아래 방법 보다는 template에서 custom element로 작성하는 것이 더 좋다

```javascript
var MyComponet = Vue.extend({
	template: '<p>hello {{ meesage }}</p>'
	data: {
		message: 'Vue'
	}
	//...
})

// 위에서 정의한 내용을 기본으로 하는 컴포넌트 생성
var MyComponetInstance = new MyComponetInstance();
```

## Vue 라이프싸이클

### Vue 객체가 생성될될 때 아래의 초기화 작업을 수행한다.

* 데이터 관찰
* 템플릿 컴파일
* DOM 에 객체 연결
* 데이터 변경시 DOM 업데이트

```javascript
var vm = new Vue({
	data: {
		a: 1
	},
	createdd: () => {
		// this. vm 을 가르킴
		console.log(`a is : ${this.a}`)
	}
})
```

* created 이 외에도 라이플싸이클 단계에 따라 아래 메서드를 사용할 수 있다.
	- mounted
	- updated
	- destroyed

위와 같이 초기회 메서드로 커스텀 로직을 수행하기 때문에 Vue에서 따로 Controller를 갖고 있지 않다
* [link](https://kr.vuejs.org/v2/guide/instance.html#라이프사이클-다이어그램) 참고

```javascript
new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue.js!'
    },
    beforeCreate: function () {
      console.log('beforeCreate');
    },
    created: function () {
      console.log('created');
    },
    mounted: function () {
      console.log('mounted');
      this.message = 'hello Yun'
    },
    updated: function () {
      console.log("updated");
    }
  })
```
* updated 는 데이터 수정이 발생 할경우 실행되는 이벤트

## Vue Components

* 화면에 비춰지는 뷰의 단위를 쪼개어 재활용이 가능한 형태로 관리하는 것이 컴포넌트

### Global or Local Components

* 컴포넌트를 뷰 인스턴스에 등록해서 사용할 때 다금과 같이 global 하게 등록 할 수 있다.
```javascript
Vue.commpoent('my-component', {
	// ...
})
```

* local 하게 등록하는 방법
```javascript
var cmp = {
	data: function(){
		return {
			// ...
		}
	}
	template: '<hr>'
	metohds: {}
}

// 아래 Vue 인스턴스에서만 활용할 수 있는 로컬 컴포넌트 등록
new Vue({
	commpoents: {
		'my-cmp': cmp
	}
})
```


### 차이점
* 글로벌로 인스턴스를 생성할 경우 다른곳에서 동일한 인스턴스를 사용시 해당 속성들을 가져옴 (당연하 거임...)

## 부모 자식 컴포넌트 관계

* 부모 -> 자식 : props down
* 자식 -> 부모 : events up

## Props
* 모든 컴포넌트는 각 컴포넌트 자체의 스코프를 갖는다
	- ex) 하위 컴포넌트가 상위 컴포넌트의 값을 바로 참조 할수 없는 형식
* 상위에서 하위로 값을 전당하려면 props 속성을 사용한다.


## 같은 레벨의 콤포넌트 간의 통신

* 동일한 상위 컴포넌트를 가진 2개의 하위 컴포넌트 간의 통신은
* child -> parent -> 다시 2 개의 Children
* 즉 동일 부모인 자식간의 통신은 부모를 통해서 가능하다

## Event Buns 컴포넌트 간 통신
* Non parent - chlid 컴포넌트 간의 통신을 위해 Event Bus 를 활용할 수 있다.
* 즉 동일 부모가 아니라도 event bus 를 통해서 데이터 통신이 가능하다.

```javascript
export const eventBus = new Vue();
new Vue({...})
```

## Vue Routers
* Vue 를 이용한 SPA 를 제작할 때 유용한 라이브러리
* Vue 코어 라이브러리 외에 Router 라이브러리르 공식 지원하고 있어 아래와 같이 설치한다.

## Nested Routers
* 라우터로 화면 이동시 Nested Routers 를 이영하여 여러개의 캄포넌트를 동시애 렌더링 할 수 있다.
* 렌더링 되는 컴포넌트의 구조는 가장 큰 상위의 컴포넌트가 하위의 컴포넌트를 포함하는 `Parent - Child` 형태이다.

## Named Views
* 라우터로 특정 URL 로 이동시, 해당 URL 에 해당하는 여려개의 View(컴포넌트) 를 동시에 렌더링 한다.
* 각 컴포넌트에 해당하는 name 속성과 roter-view 지정 필요

## Nested View vs Named Views
* 특정 URL 에서 1 개의 컴포넌트에 여러 개의 하위 컴포넌트를 갖는 것을 Nested Routes
* 특정 URL 에서 여러 개의 컴포넌트를 쪼개진 뷰 단위로 렌더링 하는 것을 Named View

## Vue Templates

* vue 는 DOM 의 요소들, 함수, 데이터 속성을 모두 Templates 안에 포함된다.
* Vue 는 Template 으로 렌더링 할 때 Virtual DOM 을 사용
* 원하면 render function 을 직접 구현하여 사용할 수 있음
* DOM 조작을 최소화 하고 렌더링을 꼭 다시 해야만 하는 요소를 계산하여 선능 을 최소화

### Atrributes
* HTML Attributes 를 Vue 의 변수와 연결할 때는 v-bind 를 이용
```HTML
<dvi v-bind:id="dynamicID"></div>
```

### JS Expressions

* {{ }} 안에 javascript 표현식이 가능하다
```HTML
<div>{{ number +1 }}</div>
```

### Directives

* `v-` 접두사를 붙인 attribuetes 로, javascript 포현식으로 값을 나타내느게 일반적이다.

```HTML
<p v-if="seen">Now you see me</p>
<p v-bind:href="url"></p>
<p v-on:click="doSomething"></p>
```

### Filters

* 화면에 표시되는 텍스트의 형식을 편하게 바꿀 수 있도록 고안된 기능이며, `|` 을 이용하여 여러 개의 필터를 적용 할 수 있다.


## Vue 데이터 바인딩

### Data Binding

* DOM 기반 HTML Template 에 vue 데이터를 바인딩 하는 방법은 아래와 같은 크게 3 가지가 있다.
	* Interpolation (값 대입)
	* Binding Expressions (값 연결)
	* Directives (디렉티브 사용)

### Interpolation - 값 대입

* Vue 가장 기본적인 데이터 바인딩 체계  Mustaxche `{{}}` 를 따른다

```HTML
<span>Message : {{msg}}</span>
<span>This is will nerver chagne : {{*msg}}</span>
<div id="item-{{id}}"></div>
```
* `*`는 값이 변경되도 화면에 갱신하지 않는다.


### Binding Expressions - 값 연결
* `{{}}` 를 이용한 데이터 바인딩을 할 때 자바스크립트 표현식을 사용할 수 있다.

### Directives
* Vue 에서 제공하는 특별한 Attrobutes 이며 `-v` 의 prefix 접두사를 갖는다.

### Class Binding

* CSS 스타일링을 위해서 class 를 아래 2가지 방법으로 추가가 가능하다.
	- `class="{{ className }}"`
	- v-bind:class
* 주의할 점은 위의 두 방법을 함께 사용하지 않고 한 방법만 사용하는것이 좋다

### Single File Components

* 앱의 복잡도가 증가할 때 `.vue` 라는 파일 단위 안에 HTML, js, css 를 관리할 수 있는 방법
* 복잡도가 커짐에 따라 야기될 수 있는 문제들
	- 모든 컴포넌트에 고유 이름을 붙야애함
	- js 파일에 template 안의 html 문법이 강조 안됨
	- css 스타일리은 거의 불가
	- es5를 이용하여 계속 앱을 작성할 경우 bable 빌드가 지원되지 않음

### Vue Loader
* `.vue` 형태의 파일을 javascript 모듈 형태로 변환해주는 Wepback loader
