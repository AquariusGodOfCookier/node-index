## webComponent

谷歌公司由于掌握了 Chrome 浏览器，一直在推动浏览器的原生组件，即 Web Components API。
相比第三方框架，原生组件简单直接，符合直觉，不用加载任何外部模块，代码量小。
目前，它还在不断发展，但已经可用于生产环境。


### 一、自定义元素
本文演示如何把这个卡片，写成 Web Components 组件                        
网页只要插入下面的代码，就会显示用户卡片。                       
```html
<user-card></user-card>
```
这种自定义的 HTML 标签，称为自定义元素（custom element）。
根据规范，自定义元素的名称必须包含连词线，用与区别原生的 HTML 元素。所以，`<user-card>`不能写成`<usercard>`。


### 二、customElements.define()
自定义元素 `<user-card>` 目前还是空的，下面在类里面给出这个元素的内容。
```js
class UserCard extends HTMLElement {
  constructor() {
    super();
  }
}
```
上面代码中，`UserCard`就是自定义元素的类。注意，这个类的父类是`HTMLElement`，因此继承了 HTML 元素的特性。                         
接着，使用浏览器原生的`customElements.define()`方法，告诉浏览器`<user-card>`元素与这个类关联。
```js
window.customElements.define('user-card', UserCard);
```

### 三、自定义元素的内容
自定义元素 `<user-card>` 目前还是空的，下面在类里面给出这个元素的内容。
```js
class UserCard extends HTMLElement {
  constructor() {
    super();

    var image = document.createElement('img');
    image.src = 'https://semantic-ui.com/images/avatar2/large/kristy.png';
    image.classList.add('image');

    var container = document.createElement('div');
    container.classList.add('container');

    var name = document.createElement('p');
    name.classList.add('name');
    name.innerText = 'User Name';

    var email = document.createElement('p');
    email.classList.add('email');
    email.innerText = 'yourmail@some-email.com';

    var button = document.createElement('button');
    button.classList.add('button');
    button.innerText = 'Follow';

    container.append(name, email, button);
    this.append(image, container);
  }
}
```

上面代码最后一行，`this.append()` 的`this`表示自定义元素实例。                  
完成这一步以后，自定义元素内部的 DOM 结构就已经生成了。                  


### 四、<template>标签
使用 JavaScript 写上一节的 DOM 结构很麻烦，Web Components API 提供了<template>标签，可以在它里面使用 HTML 定义 DOM。
```html
<template id="userCardTemplate">
  <img src="https://semantic-ui.com/images/avatar2/large/kristy.png" class="image">
  <div class="container">
    <p class="name">User Name</p>
    <p class="email">yourmail@some-email.com</p>
    <button class="button">Follow</button>
  </div>
</template>
```

然后，改写一下自定义元素的类，为自定义元素加载`<template>`。
```js
class UserCard extends HTMLElement {
  constructor() {
    super();

    var templateElem = document.getElementById('userCardTemplate');
    var content = templateElem.content.cloneNode(true);
    this.appendChild(content);
  }
}
```

上面代码中，获取`<template>`节点以后，克隆了它的所有子元素，这是因为可能有多个自定义元素的实例，这个模板还要留给其他实例使用，
所以不能直接移动它的子元素。
到这一步为止，完整的代码如下。
```html
<body>
  <user-card></user-card>
  <template>...</template>

  <script>
    class UserCard extends HTMLElement {
      constructor() {
        super();

        var templateElem = document.getElementById('userCardTemplate');
        var content = templateElem.content.cloneNode(true);
        this.appendChild(content);
      }
    }
    window.customElements.define('user-card', UserCard);    
  </script>
</body>
```

### 五、添加样式
自定义元素还没有样式，可以给它指定全局样式，比如下面这样。
```
user-card {
  /* ... */
}
```

但是，组件的样式应该与代码封装在一起，只对自定义元素生效，不影响外部的全局样式。所以，可以把样式写在`<template>`里面。
```html
<template id="userCardTemplate">
  <style>
   :host {
     display: flex;
     align-items: center;
     width: 450px;
     height: 180px;
     background-color: #d4d4d4;
     border: 1px solid #d5d5d5;
     box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.1);
     border-radius: 3px;
     overflow: hidden;
     padding: 10px;
     box-sizing: border-box;
     font-family: 'Poppins', sans-serif;
   }
   .image {
     flex: 0 0 auto;
     width: 160px;
     height: 160px;
     vertical-align: middle;
     border-radius: 5px;
   }
   .container {
     box-sizing: border-box;
     padding: 20px;
     height: 160px;
   }
   .container > .name {
     font-size: 20px;
     font-weight: 600;
     line-height: 1;
     margin: 0;
     margin-bottom: 5px;
   }
   .container > .email {
     font-size: 12px;
     opacity: 0.75;
     line-height: 1;
     margin: 0;
     margin-bottom: 15px;
   }
   .container > .button {
     padding: 10px 25px;
     font-size: 12px;
     border-radius: 5px;
     text-transform: uppercase;
   }
  </style>

  <img src="https://semantic-ui.com/images/avatar2/large/kristy.png" class="image">
  <div class="container">
    <p class="name">User Name</p>
    <p class="email">yourmail@some-email.com</p>
    <button class="button">Follow</button>
  </div>
</template>
```

### 六、自定义元素的参数
`<user-card>`内容现在是在`<template>`里面设定的，为了方便使用，把它改成参数。
```html
<user-card
  image="https://semantic-ui.com/images/avatar2/large/kristy.png"
  name="User Name"
  email="yourmail@some-email.com"
></user-card>
```

`<template>`代码也相应改造。
```html
<template id="userCardTemplate">
  <style>...</style>

  <img class="image">
  <div class="container">
    <p class="name"></p>
    <p class="email"></p>
    <button class="button">Follow John</button>
  </div>
</template>
```

```js
class UserCard extends HTMLElement {
  constructor() {
    super();

    var templateElem = document.getElementById('userCardTemplate');
    var content = templateElem.content.cloneNode(true);
    content.querySelector('img').setAttribute('src', this.getAttribute('image'));
    content.querySelector('.container>.name').innerText = this.getAttribute('name');
    content.querySelector('.container>.email').innerText = this.getAttribute('email');
    this.appendChild(content);
  }
}
window.customElements.define('user-card', UserCard);    
```


### 七、Shadow DOM
我们不希望用户能够看到`<user-card>`的内部代码，Web Component 允许内部代码隐藏起来，
这叫做 Shadow DOM，即这部分 DOM 默认与外部 DOM 隔离，内部任何代码都无法影响外部。                     
自定义元素的`this.attachShadow()`方法开启 Shadow DOM，详见下面的代码。                                 
```js
class UserCard extends HTMLElement {
  constructor() {
    super();
    var shadow = this.attachShadow( { mode: 'closed' } );

    var templateElem = document.getElementById('userCardTemplate');
    var content = templateElem.content.cloneNode(true);
    content.querySelector('img').setAttribute('src', this.getAttribute('image'));
    content.querySelector('.container>.name').innerText = this.getAttribute('name');
    content.querySelector('.container>.email').innerText = this.getAttribute('email');

    shadow.appendChild(content);
  }
}
window.customElements.define('user-card', UserCard);
```

### 八、组件的扩展
#### （1）与用户互动                    
用户卡片是一个静态组件，如果要与用户互动，也很简单，就是在类里面监听各种事件。
```js
this.$button = shadow.querySelector('button');
this.$button.addEventListener('click', () => {
  // do something
})
```


### 参考代码
- [入门示例教程Demo](./04_01、入门示例教程Demo/index.html)




### 参考资料
- [Web Components 入门实例教程](http://www.ruanyifeng.com/blog/2019/08/web_components.html)
- [Web Components - MDN](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)
