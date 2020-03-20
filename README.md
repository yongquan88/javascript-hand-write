# javascript-hand-write

## 防抖函数（debounce）

> 防抖函数的原理：在事件被触发 n 秒后才执行回调，如果在 n 秒内再次被触发，则重新计时。

###### 手写精简版：

```javascript
function debounce(fn, wait) {
  let timer = null;
  return function() {
    //注意这里不能用箭头函数，否则函数内的this指向了外面的this了
    let context = this;
    let args = Array.prototype.slice.call(arguments, 0);
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(context, args);
    }, wait);
  };
}
```

###### 适用场景：

- 按钮提交场景：防止按钮多次提交，只执行最后提交的一次
- 服务端验证场景：表单验证需要服务端配合，只执行一段连续输入的最后一次，还有搜索联想词功能也是这样

## 实现节流函数（throttle）

> 函数节流原理：在规定单位时间内，只能触发一次函数。如果在这个单位时间内多次触发，只有一次生效。

###### 手写精简版

```javascript
funtion throttle(fn,wait){
  let prev=Date.now();
  return function (){ //注意这里不能用箭头函数，否则函数内的this就指向外层的函数了
    let context =this;
    let args = Array.prototype.slice.call(arguments,0);
    let now = Date.now()
    if(now-prev>wait){
      fn.apply(context,args);
      prev=now;
    }
  }
}
```

###### 适用场景：

- 拖拽场景：单位时间内只执行一次，防止超高频次触发位置变动
- 缩放场景：监控浏览器的 resize
- 动画场景：避免短时间内多次触发动画引起性能问题(ps:小程序听声音找朋友-滑卡片动画)

## 实现一个 call 函数

> 思路：将需要改变 this 指向方法挂到目标 this 上执行并返回。
> call(context,arg1,arg2,...args):call 函数可接受多个参数，第一个是目标 this 作用域，不传的就默认指向 window。

```javascript
Function.prototype.mycall = function(context) {
  if (typeof this !== 'function') {
    //先判断this是不是一个函数
    return new TypeError('Error');
  }
  let context = context || window; // 目标this
  let fn = this; // 要改变this的方法
  let args = [...arguments].slice(1); // 参数
  context.fn = fn;
  let result = context.fn(...args);
  delete context.fn;
  return result;
};
```

## 实现一个 apply 函数

> 实现思路：将需要改变 this 指向的函数挂到目标 this 上，然后执行并返回。
> apply(context,arr):apply 接受 2 个参数，第一个是目标 this 作用域，如果不穿默认指向 window，第二个是一个数组的形式

```javascript
Function.prototype.myapply = function(context) {
  if (typeof this !== 'function') {
    //先判断this是不是一个方法
    return new TypeError('Error');
  }
  let context = context || window; //找到目标this
  let fn = this; //找到需要改变this的函数
  context.fn = fn; //把函数挂到到目标this上
  let result;
  if (arguments[1]) {
    //判断是否有第二个参数，它是一个数组
    result = context.fn(...arguments[1]);
  } else {
    result = context.fn();
  }
  delete context.fn;
  return result;
};
```

## 实现一个 bind 函数

> 实现思路：类似 call 和 apply，但这里是返回一个函数

```javascript
Function.prototype.mybind = function(context) {
  if (typeof this !== 'function') {
    //判断this是不是一个方法
    return new TypeError('Error');
  }
  let fn = this;
  let args = [...arguments].slice(1);
  return F(){
    // 借用apply实现，第二个参数需要合并两个函数的参数
    return fn.apply(context,args.concat([...arguments]))
  }
};
```

## Object.create 实现原理

> Object.create(proto,propertiesObject) 是使用指定原型 proto 对象及其 propertiesObject 去创建一个新的对象。

```javascript
var newObj = Object.create({ b: 123 }, { a: { value: 88 } });
console.log(newObj.__proto__); // {b:123}
console.log(newObj); //{a:88}
```

> 自己实现一个 Object.create()的思路：
>
> - 创建一个对象
> - 继承指定父对象
> - 使用 Object.defineProperties()为新对象扩展新属性

```javascript
Object.mycreate = function(obj, properties) {
  var F = function() {}; // 新建一个对象
  F.prototype = obj; // 把传进来的obj设置为新对象的原型
  if (properties) {
    // 给新对象设置属性
    Object.keys(properties).forEach(key => {
      Object.defineProperty(F, key, properties[key]);
    });
  }
  return new F(); // new 一个实例并返回
};
```

联想：Object.create() 与 new Object() 的区别？
