<p align="center">
	<img src="http://image.kyobobook.co.kr/images/book/xlarge/754/x9791158390754.jpg">
</p>

## 목차
<!-- TOC -->

- [목차](#목차)
- [2 양반양 바인딩](#2-양반양-바인딩)
- [3 디렉티브](#3-디렉티브)
    - [v-show](#v-show)
    - [v??](#v)
    - [결론](#결론)
- [리스트 렌더링](#리스트-렌더링)
- [5 상호작용](#5-상호작용)
    - [이벤트 처리](#이벤트-처리)
    - [HTML 이벤트](#html-이벤트)
- [6 필터](#6-필터)
- [8 사용자 정의 이벤트](#8-사용자-정의-이벤트)
    - [8.2 부모 - 자식 간 통신](#82-부모---자식-간-통신)

<!-- /TOC -->

## 2 양반양 바인딩

```html
<html>
<head>
    <title>Hello Vue</title>
</head>
<body>
<div id="app">
    <h1>Hello Mr.{{ message }}</h1>
    <input v-model="message">
</div>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.0.1/vue.js"></script>

<script>
  new Vue({
    el: '#app',
    data: {
      message: 'Greetings your Majesty!'
    }
  })
</script>
</html>
```

* `<h1>Hello Mr.{{ message }}</h1>` 와 `data.message` 양반양 바인딩이 됨
* html 값이 변경되면 자바스크립트 객체가 변경되고 그 반대도 같다.

## 3 디렉티브
* 디렉티브는 라이브러리에서 DOM 엘리먼트가 무언가를 수행하도록 지기사는 특수한 토큰입니다.
* v-show: 조건에 따라 엘리먼트를 화면에 표시합니다.
* v-if: v-show 대신 사용할 수있씁니다
* v-else:v-if 의 평가 결과 거짓이면 엘리먼트를 표시합니다.

### v-show
```html
<html>
<head>
    <title>Hello Vue</title>
</head>
<body>
<div id="app">
    <h1>You must send a message for help!</h1>
    <textarea v-model="message"></textarea>
    <button v-show="message"></button>
    <pre>{{$data}}</pre>
</div>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.0.1/vue.js"></script>

<script>
  new Vue({
    el: '#app',
    data: {
      message: 'Greetings your Majesty!'
    }
  })
</script>
</html>
```
* meesage 데이터 여부에 따라 버튼이 `display: non`이 된다.

### v??

```html
<html>
<head>
    <title>Hello Vue</title>
</head>
<body>
<div id="app">
    <tempate v-if="!message">
        <h1 v-if="!message">You must send a message for help!</h1>
        <h2 v-else>You have send a message!</h2>
        <p>You can Node.js</p>
        <p>Data Format JSON Type</p>

    </tempate>
    <textarea v-model="message"></textarea>
    <button v-if="message">
        Send word to allies for help
    </button>
    <pre>{{$data}}</pre>
</div>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.0.1/vue.js"></script>

<script>
  new Vue({
    el: '#app',
    data: {
      message: 'Greetings your Majesty!'
    }
  })
</script>
</html>
```

### 결론
* v-show를 사용하면 페이지 렌더링 중에 더 많은 로드 시간이 발생한다
* v-if 를 사용할 경우 초기 렌더링에서 조건이 거짓이면 아무것도 하지 않음 즉 조건이 true 가 되기전까지 실제로 렌더링 하지 않음
* **따라서 무언가를 매우 자주 토클해야하는 경우에는 v-show를 사용하고, 런타임 조간이 자주 변경될 가능성이 낮은 경우네느 v-if를 사용해야함**

## 리스트 렌더링

1. 배열 기반의 리스트 출력
2. 템플릿 반복
3. 객체의 프로포티를 순회

```html
<html>
<head>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
          integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
    <title>Hello Vue</title>
</head>
<body>
<div class="container">
    <h1>Let`s hear some stories!</h1>

    <div>
        <ul class="list-group">
            <li v-for="(value, key, index) in person" class="list-group-item">
                {{index}} : {{key}} = {{value}}
            </li>
        </ul>
    </div>
    <pre>
        {{ $data }}
    </pre>


</div>

</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.0.1/vue.js"></script>

<script type="text/javascript">
  new Vue({
    el: '.container',
    data: {
      person: {
        name: 'yun',
        height: '1.70',
        weigh: '67kg',
        eyeColor: 'brown',
        favoriteFood: 'Cake'
      }
    }
  })
</script>
</html>
```

## 5 상호작용

### 이벤트 처리
* HTML 이벤트는 DOM 엘리먼트에서 일어납니다. HTML 페이지에서 vue.js를 사용하면 Vue.js가 이러한 이벤트에 반응 합니다.
* 이벤트는 기본적인 사용자 항호작용에서 렌더링 모델에서 일어나는 일에 이르기까지 모든 것을 나타낼 수 있습니다.

### HTML 이벤트
* 웹 페이지가 완전히 로드됨
* 입력 폼이 변경됨
* 버튼이 클릭됨
* 폼이 제출됨

## 6 필터


## 8 사용자 정의 이벤트

* 때대로 사용자 정의 이벤트가 필요할 때가 있습니다. 이를 위해 Vue 인스턴스 메서드를 사용 할 수 있습니다. 모든 Vue 인스턴스는 이벤트 인터페이스를 구현 하고 있습니다.

### 8.2 부모 - 자식 간 통신
* 부모 컴포넌트가 자식 컴포넌트의 이벤트를 청취할 필요가 있을 때는 상황이 약간 달라집니다. this가 각기 다른 인스턴스를 바인딩될 것이기 때문에 this.$on/this.$emit을 사용 할 수 없습니다.

* v-on(@) 이벤트 리스너를 기억하십니까? 부모 컴포넌트는 자식 컴포너트가 사용되는 템플릿에서 곧바로 v-on을 사용해서 자식 컴포넌트에서 발생한 이벤트를 직접 청휘할 수 있씁니다.

* 이전 예제에 이어서 name 르포로터를 가진 음식 컴포넌트를 만들겠습니다. 템플릿에서 name을 표시하는 버튼을 추가합니다. 버튼이 클릭되면 vote 이벤트가 발생하게 만들겠습니다.
