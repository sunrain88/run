# 怎么优化if else

## 前提

我们编写js代码时经常遇到复杂逻辑判断的情况，通常大家可以用if/else或者switch来实现多个条件判断，某些情况下，还会有分支嵌套，但这样会有个问题，随着逻辑复杂度的增加，代码中的if/else/switch会变得越来越臃肿，越来越看不懂，那么如何更优雅的写判断逻辑，可以尝试以下写法。

主要的思想就是抽离条件判断和绑定对应的执行代码

### 一元判断条件

可以使用Object数据结构，判断条件为key，执行函数为value

```js
function getName(name) {
    const handleMap = {
        '张三': (params) => { console.log('我是法外狂徒') },
        '李四': (params) => { console.log('不是我')  },
        'other': () => { console.log('404') }
    }
    nameMap[name] ? nameMap[name](params) : nameMap['other']()
}
```

经过以上优化后原本的`if`判断就转化成了`key value`对应值，条件和执行函数一一对应，很容易理解，并且很好扩展和维护，如果有新的情况，只需要去增加`key value`即可。不过这个写法也有些问题，只能处理参数和`key`简单相等的情况，如果判断条件是多元或者更复杂一些的情况，就无法处理了。

### 多元判断条件

当我们遇见多元判断条件时，可以引入二维数组结构来处理这种情况

```js
function getName(name) {
   const handleMap = [
       [
           name => name === 'test',
           (params) => {
               console.log('my name is test')
           }
       ],
       [
           name => name[0] === '张',
           (params) => {
               console.log('我姓张')
       }
       ],
       [
           name => name.length === 2 && name[1] === '三',
           (params) => {
               console.log('我猜是张三')
           }
       ],
       [
           name => !name,
           (params) => {
               console.log('没有名字')
           }
       ]
   ]
   handleMap.map(item => item[0](name) && item[1](params))
}
```

这种二维数组结构可以处理多元判断类型的代码，数组第一位为判断条件，数组第二位为执行函数，并且当多个数组的判断条件符合时，会触发多个执行函数，这种写法更适用于复杂判断情况和益于扩展。

以上写法还可以优化，当这个判断函数执行频率较高时，会频繁的创建`handleMap`，我们可以将其独立抽离出去，减少性能和内存消耗。

### Map优化

使用了Object和二维数组作为`handleMap`来处理我们的判断分支逻辑，这两者都有各自的优点和缺点，object只能处理简单判断逻辑，二维数组则会将所有的判断逻辑都执行一次。我们可以尝试一下使用map数据结构来处理我们的判断逻辑。

```js
function getName(name) {
    const fn1 = () => { console.log('我是中文名') }
    const fn2 = () => { console.log('Im english name') }
    const fn3 = () => { console.log('Im 中英结合') }
    const handleMap = new Map([
        ['boo', fn2],
        ['louis',fn2],
        ['张三',fn1],
        ['李四',fn1],
        ['李五',fn1],
    ])
    handleMap.get(name)()
}
```