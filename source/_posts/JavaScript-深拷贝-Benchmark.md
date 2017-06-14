---
title: JavaScript 深拷贝 Benchmark
date: 2017-03-05 13:33:24
tags:
- JavaScript
- Benchmark 
- lodash 
- immutable 
---

stack overflow 上有一个关于深拷贝的效率的问题讨论。[What is the most efficient way to deep clone an object in JavaScript?](http://stackoverflow.com/questions/122102/what-is-the-most-efficient-way-to-deep-clone-an-object-in-javascript)。文中提到了使用 JSON.parse 和 JSON.stringify 对一个对象进行拷贝的方法,但题主对其效率问题提出了质疑。下面我们将对其中提到深拷贝的方法通过 node.js 进行 benchmark，看一下这些方法的性能如何。

安装 benchmark jquery loadsh immutable angular

``````
npm install --save benchmark lodash immutable jsdom
```

由于 angular 和 jQuery 所执行的上下文依赖于浏览器环境, 所以我们引入 jsdom 创建基于 node.js 的浏览器环境提供给 angular 和 jQuery 执行。

```
var _ = require('lodash');
var jsdom = require('jsdom')
var Immutable = require('immutable')
var Benchmark = require('benchmark')
var suite = new Benchmark.Suite;

var obj = { a: "hello", c: "test", po: 33, arr: [1, 2, 3, 4], anotherObj: { a: 33, str: "whazzup" } };

jsdom.env({
    url: "http://baidu.com",
    scripts: ['http://apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js',
        'https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.1/angular.min.js'],
    done: function (err, window) {
        var $ = window.$
        var angular = window.angular

        suite.add('JSON.parse#test', function () {
            var obj2 = JSON.parse(JSON.stringify(obj));
            })
            .add('lodash.clone#test', function () {
                var obj3 = _.clone(obj, true);
            })
            .add('angular.copy#test', function () {
                var obj4 = angular.copy(obj);
            })
            .add('jQuery.extend#test', function () {
                var obj5 = $.extend(true, {}, obj);
            })
            .add('Immutable#test', function () {
                var obj5 = Immutable.fromJS(obj).toJS();
            })
            .add('Object.assign#test', function () {
                var obj6 = Object.assign({}, obj);
            })
             .add('lodash.cloneDeep#test', function () {
                var obj7 = _.cloneDeep(obj);
            })
            // add listeners
            .on('cycle', function (event) {
                console.log(String(event.target));
            })
            .on('complete', function () {
                console.log('Fastest is ' + this.filter('fastest').map('name'));
            })
            // run async
            .run({ 'async': true });
    }
});
```

运行测试文件得到以下结果：

```
JSON.parse#test x 341,384 ops/sec ±0.83% (89 runs sampled)
lodash.clone#test x 363,328 ops/sec ±0.79% (88 runs sampled)
angular.copy#test x 227,521 ops/sec ±0.69% (82 runs sampled)
jQuery.extend#test x 164,081 ops/sec ±0.68% (88 runs sampled)
Immutable#test x 32,697 ops/sec ±6.34% (66 runs sampled)
Object.assign#test x 923,069 ops/sec ±2.31% (89 runs sampled)
lodash.cloneDeep#test x 156,321 ops/sec ±0.80% (87 runs sampled)
Fastest is Object.assign#test
```
由于 Object.assign 实际上执行的是潜复制，所以这里应该执行深复制最快的是 JSON.parse，这里由快到慢的顺序是：

1. Object.assign
2. JSON.parse/JSON.stringify
3. lodash
4. angular.copy
5. jQuery.extend
6. Immutable

但是JSON.parse执行深复制的时候，关于时间的处理是有一定问题的：

```
var obj = {date: new Date()}
undefined
obj
Object {date: Sun Mar 05 2017 14:49:49 GMT+0800 (CST)}
var obj2 = JSON.parse(JSON.stringify(obj))
undefined
obj2
Object {date: "2017-03-05T06:49:49.701Z"}
```

JSON.stringify会对日期对象进行字符串话的处理，复制前后的对象不在一致，这一点需要注意。