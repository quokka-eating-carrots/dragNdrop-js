# dragNdrop-js

[참고 강의](https://youtu.be/C22hQKE_32c)
[완성 페이지](https://frolicking-blini-c7233c.netlify.app/)

---

## 기본 설정

비어 있다는 의미에서 `<div>` 태그에 class명이 `empty`인 태그를 5개 만들어 줍니다. 첫 번째 `<div>` 안에 자식 요소로 `fill`이라는 class명을 가진 자식을 하나 넣어 줍니다. 그리고 드래그를 할 수 있게 `draggable` 속성을 넣어 줍니다.
```html
<div class="empty">
    <div class="fill" draggable="true"></div>
  </div>
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="empty"></div>
```

## css 설정

`<body>` 태그에 배경 색을 넣어 줍니다.<br>
`fill` class명에는 사진이 랜덤으로 오도록 `background-image`에 API url을 넣어 줍니다. [unsplash]("http://source.unsplash.com/random/150x150") 주소 가장 마지막에 `150x150`과 같이 사이즈를 명시할 수 있습니다.

```css
body {
  background-color: #A0D9E2;
}

.fill {
  background-image: url("http://source.unsplash.com/random/150x150");
  position: relative;
  height: 150px;
  width: 150px;
  top: 5px;
  left: 5px;
  cursor: pointer;
}

.empty {
  display: inline-block;
  height: 160px;
  width: 160px;
  margin: 10px;
  border: 3px #6AA3AC solid;
  background-color: #fff;
}

.hold {
  border: 4px #ccc solid;
}

.hovered {
  background-color: #f4f4f4;
  border-style: dashed; 
}

.invisible {
  display: none;
}
```
`empty` class에 `display= inline-block`으로 작성한 이유는 박스들이 정렬되기를 원하기 때문입니다. 추가적으로 html에 작성하지 않았던 `hold`, `hovered`, `invisible`이 나오는데 이 class들은 JavaScript를 작성하면서 추가될 class입니다.

## js 코드 작성

먼저 html에 작성하였던 `fill`, `empty` class명을 가진 태그들을 불러옵니다.
```javascript
const fill = document.querySelector(".fill");
const empties = document.querySelectorAll(".empty");
```

dragNdrop을 설정하는 것인 만큼 `addEventlistener`에서 내가 drag를 했고, drag가 끝났다는 것을 알려 줘야 합니다.
```javascript
fill.addEventListener('dragstart', dragStart);
fill.addEventListener('dragend', dragEnd);
```

그러면 이제 뒤에 명시되어 있는 `dragStart`와 `dragEnd`에 대한 함수를 작성해야 합니다.
```javascript
function dragStart() {
  this.className += ' hold';
  setTimeout(() => (this.className += ' invisible'), 0);
}
```

우선 `dragStart`에 아까 css에 작성하였던 `hold`와 `invisible` class를 추가합니다. `hold` class명을 추가하게 되면 내가 마우스로 잡고 있는 이미지에 테두리선이 설정한 css값으로 변하게 됩니다. `invisible` class를 추가하면서 `setTimeout`을 사용하였는데, 이때 `setTimeout`을 사용하지 않으면 내가 잡은 사진이 그대로 사라지게 됩니다. 기존에 위치하고 있던 사진이 사라져야 되는 건 맞지만, 내가 잡고 있는 사진도 사라지면 안 되기에 `setTimeout`을 사용하여 `invisible`을 추가해 줍니다.

```javascript
function dragEnd() {
  this.className = 'fill';
};
```

드래그가 끝난 곳엔 사진이 채워 줘야 하니 `fill`이라는 class를 넣어 줍니다(바꿔 줍니다).

```javascript
for(const empty of empties) {
  empty.addEventListener('dragover', dragOver);
  empty.addEventListener('dragenter', dragEnter);
  empty.addEventListener('dragleave', dragLeave);
  empty.addEventListener('drop', dragDrop);
}
```

for문을 통해서 반복적인 행동이 될 수 있게 함수를 작성합니다. `dragover`, `dragenter`, `dragleave`, `drop` 이 네 가지 행동을 반복합니다.

```javascript
function dragOver(e) {
  e.preventDefault();
}

function dragEnter(e) {
  e.preventDefault();
  this.className += ' hovered';
}

function dragLeave() {
  this.className = 'empty';
}

function dragDrop() {
  this.className = 'empty';
  this.append(fill);
}
```
`preventDefault`를 사용하여서 `dragOver`과 `dragEnter` 부분의 기본적인 상태를 방지해 줍니다. `dragEnter`에서는 사진을 넣을 부분에 hover가 되기 때문에 `hovered` class를 추가해 주고, `dragLeave`는 사진이 사라져서 다른 곳으로 들어갔기 때문에 `empty` class명으로 바꿔 줍니다. 마지막 `dragDrop` 부분은 사진이 들어오게 되면서 채워지기 때문에 `fill`이라고 설정해 둔 변수를 넣어 줍니다.

