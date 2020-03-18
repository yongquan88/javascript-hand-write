# javascript-hand-write

## 防抖函数（debounce）

> 防抖函数的原理：在事件被触发 n 秒后才执行回调，如果在 n 秒内再次被触发，则重新计时。

###### 手写精简版：

```javascript
function debounce(fn, wait) {
  let timer = null;
  return () => {
    let context = this;
    let args = Array.prototype.slice.call(arguments, 0);
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.aplly(context, args);
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
  return ()=>{
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

> 思路：将需要改变 this 指向方法挂到目标 this 上执行并返回

```javascript
Function.prototype.mycall = function(context) {
  let context = context || window; // 目标this
  let fn = this; // 要改变this的方法
  let args = [...arguments].slice(1); // 参数
  context.fn = fn;
  let result = context.fn(...args);
  delete context.fn;
  return result;
};
```
