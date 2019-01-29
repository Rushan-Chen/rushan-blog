---
layout: post
title: "JavaScript四大语法模块"
subtitle: "Grammer of JavaScript"
author: "Rushan"
header-img: "img/post-bg-javascript.png"
header-mask: 0.2
tags:
  - JavaScript
  - 语法
---

# JavaScript 4大块语法

- 一个数据集合(对象、json)。
- 两种函数（同步、异步）
- 三大结构（顺序语句、条件语句、循环语句）
- 多种数据格式（数字，字符串，数组，布尔，json，对象）的使用方式

# 自问

## 1. 一个数据集合(对象)

- 1.1 如何定义一个对象？
    - 对象直接量。
    ```js
    let xiaomingObj = {
        name: "xiaoming",
        age: 18,
        student: true
    };
    ```

    - 用构造函数new一个。
    ```js
    let obj = new Obiect();
    ```

    - 用Object.create(obj)方法，传入要继承的对象原型。
    ```js
    let xiaoxinObj = Object.create(xiaomingObj);
    ```

- 1.2 如何访问对象的属性？
    - 点表示法 `obj.prop`用对象.属性名访问，属性名必须是一个有效的变量名 (如以数字开头、含有空格` `、减号`-`、星号`*`等特殊字符，只能用`[]`访问)
    - 括号表示法 `obj['key']`用对象['键']访问 (键是可以是一个 **表达式的值**。当键是一个表达式值时，设置和访问时都是可以 **动态** 计算的)

## 2. 两种函数（同步、异步）

- 2.1 什么是函数？
    
    引用自：[JS函数 - 徐高阳][函数]

    【函数三要素】

    假如你要请一个人帮忙做某事，你必须要明确到底请谁（函数名）？告诉他你的要求（参数）是什么，他处理完事情后就会给你一个结果（返回值）。

    - 当你要定义（**设计**）一个函数的时候，你就要站在设计函数的角度去思考。

        - 我设计这个函数到底是为了完成什么功能？
        - 我设计这个函数需不需要调用者给我一些数据？
        - 我设计这个函数有没有必要返回一个结果数据？
        - 我设计这个函数应该用什么方式返回数据？
    
    - 当你要 **调用** 一个函数的时候，你就要站在使用者的角度去思考。

        - 我调用的这个函数能否完成我想要的功能。
        - 我调用的这个函数都需要一些什么数据才能完成功能。
        - 我调用的这个函数会不会给一个结果数据。
        - 我调用的这个函数用的是什么方式返回数据。
    
    - 从设计函数的角度和从使用一个函数的角度，都反映出三个重要的要素。

        - 函数名。一个函数名对应了一个函数执行体，这个执行体会完成一个具体的功能。
        - 参数。一个函数在执行的时候可能需要参数，也可能不需要。（参数判断）
        - 返回值。一个函数执行完后，可能返回一个结果，也可能不返回。

        这三点就是函数最重要的三个要素。

- 2.2 如何定义函数？（函数语法定义方式和表达式定义方式的区别）

    参考：[两种定义函数方式的差异 - 徐高阳][函数定义]

    - 函数语法定义方式

    ```js
    function print(content) {
        console.log(content);
    }
    ```

    - 函数表达式定义方式

    ```js
    let print = function(content){
        console.log(content);
    }
    ```

    - 差异
        - 用函数语法定义一个函数，能获得 **定义提前** 的优待。
        - 用函数表达式定义一个函数变量，跟变量一样，要先定义才能使用。

    - 函数表达式定义的价值
    
    ```js
    // 用函数表达式定义方法定义函数print

    const print = function(){
        console.log('hello, JS');
    }

    // 5秒后调用

    setTimeout(print, 5000);
    ```

    改进：

    ```js
    // 如果print函数不需要重复调用，那直接把函数表达式给setTimeout，省去变量命名的烦恼

    setTimeout(function(){
        console.log('hello, JS');
    }, 5000);
    ```

- 2.3 两种返回结果的方式：同步、异步

    - 如何调用同步函数？同步函数怎么返回结果。
    

    - 如何调用异步函数？异步函数怎么返回结果。


    - 【两个返回结果的注意点】

        - 同步函数可以采用callback返回结果，但不要这么做。
        - 异步函数只能采用callback返回最终结果。但调用异步函数本身也有一个立即返回值。两个返回结果要区分。

- 2.4 立即执行函数的技巧

    是前端加载第三方库的方式。

    参考[定义完就执行](https://github.com/xugy0926/getting-started-with-javascript/blob/master/topics/%E5%87%BD%E6%95%B0%20-%20%E5%AE%9A%E4%B9%89%E5%AE%8C%E5%B0%B1%E6%89%A7%E8%A1%8C.md)。

## 3. 三大结构（顺序语句、条件语句、循环语句）

- 顺序语句的执行流程。
    - 执行流程：从上到下一句一句执行。

- 条件语句的执行流程。什么内容才能成为条件，是语句？还是表达式？
    - 执行流程：当条件为true时，才执行语句块的内容。
    - 条件内容是： **表达式**。

    ```js
    let xiaomingObj = {age:19};
    if(xiaoming.age > 18){
        xiaomingObj.adult = true
    } // `xiaoming.age > 18`是表达式
    ```
    
- 循环语句的执行流程。循环语句的三大特性：什么是初始化语句？什么是循环判断条件？什么是累加器？为什么需要三大特性才能保证循环正确执行。

    - 执行流程：当循环判断条件为true的时候，执行循环体，然后执行累加器，再次判断循环条件。直到循环判断条件为false，退出循环；或者遇到跳转语句，进行跳转。

    - 三大特性
        - 初始化语句：`let i = 0;`
        - 循环判断条件：`i < arr.length;`
        - 累加器：`i++`

    - 原因：初始化语句确定循环的起点，循环判断条件决定是否继续循环，累加器让程序进入下一轮循环。


## 4. 多种数据格式（字符串，数字，数组，布尔，json，对象）的使用方式

- 4.1 字符串怎么相加来拼接字符串？

    使用 `+` 运算符。

    ```js
    let xiaomingObj = {name:"小明"};
    console.log(xiaomingObj.name + '，一起学JS');
    ```

- 4.2 数字和字符串可以相加吗？那数组和字符串能相加吗？和对象呢？和其他格式呢？

    可以。

    ```js
    // Example-4.2:字符串操作

    let xiaomingObj = {
        name: "小明",
        student: true,
        heightInCM: 175,
        friends: ['小红', '小靖', '小方'],
        families: {
            father: '陈立',
            mother: '蔡晴',
            sister: '小粒'
        }
    };
    // 字符串和数字相加

    console.log('小明的身高：' + xiaomingObj.heightInCM + 'cm'); // 小明的身高：175cm

    // 字符串和数组相加

    console.log('小明的朋友们：'+ xiaomingObj.friends); // 小明的朋友们：小红,小靖,小方

    // 字符串和对象相加

    console.log('小明的个人信息: ' + xiaomingObj); // 小明的个人信息: [object Object]

        // 如果要打印出对象的明细，可以用`JSON.stringify`先转换对象

        console.log('小明的个人信息: ' + '\n' + JSON.stringify(xiaomingObj)); // 小明的个人信息: 
        // {"name":"小明","student":true,"heightInCM":175,"friends":...}}

        // 还可以美化输出

        console.log('小明的个人信息: ' + '\n' + JSON.stringify(xiaomingObj, null, 4)); // 输出的对象的属性换行缩进4个空格

    // 字符串和布尔值相加

    console.log('xiaoming is student: ' + xiaomingObj.student); // xiaoming is student: true

    ```

- 4.3 数组怎么用游标访问元素？数据可以删掉元素吗？可以增加吗？可以排序吗？
    - 游标访问元素：`arr[0]`
    - 删除元素：
        > `arr.pop()` 

        删除最后一个元素，返回值是该元素的值。[Array.prototype.pop()][.pop]

        > `arr.shift()` 

        删除第一个元素，返回值是该元素的值。[Array.prototype.shift()][.shift]

        > `arr.splice(start[, deleteCount[, item1[, item2[, ...]]]])`
        
        通过索引删除某个/多个元素。从索引`start`开始，删除`deleteCount`个元素，并插入`item1, item2, ...`元素。返回值是被删除的元素。

    - 增加元素：
        > `arr.push(element1, ..., elementN)` 
        
        在末尾增加元素，增加多个用逗号隔开。[Array.prototype.push()][.push]

        > `arr.unshift(element1, ..., elementN)`

        将一个或多个元素添加到数组的开头，并返回新数组的长度。[Array.prototype.unshift()][.unshift]

        > `arr.splice(start[, deleteCount[, item1[, item2[, ...]]]])` 
        
        第三个可选参数`item1, item2, ...`表示从`start`索引位置开始，要插入的元素。如果`deleteCount`为0，则只新增不删除。[Array.prototype.splice()][.splice]
    
    ```js
    // Example-4.3: 数组操作

    let colorList = ['white', 'blue', 'balck', 'green'];
    console.log('颜色列表：' + colorList);

    // 游标访问元素

    console.log('最喜欢的颜色：' + colorList[0]); // 最喜欢的颜色：white

    // 删除最后一个元素

    console.log('删除最后一个颜色：' + colorList.pop()); // 删除最后一个颜色：green

    // 删除第一个元素

    console.log('删除第一个颜色：' + colorList.shift()); // 删除第一个颜色：white

    // 删除第二个元素

    console.log('删除第二个颜色：' + colorList.splice(1,1)); // 删除第二个颜色：blue

    // 添加元素到数组的末尾

    colorList.push('red');
    console.log('末尾增加颜色：' + colorList[colorList.length-1]); // 末尾增加颜色：red

    // 添加元素到数组的头部

    colorList.unshift('white'); // 返回length属性：3
    console.log('新增第一个颜色：' + colorList[0]); // 新增第一个颜色：white

    // 从某索引开始插入元素

    console.log('颜色列表：' + colorList);
    colorList.splice(1, 0, 'green', 'gray'); // 删除0个元素，即不删除，直接插入元素
    console.log('从索引1开始插入2个颜色后：' + colorList); // 从索引1开始插入2个颜色后：white,green,gray,blue,red
    ```

- 4.4 什么样的表达式才能得到布尔值？将数字1和字符串1进行比较会得到true还是false？

    - 得到布尔值的表达式

        - 比较运算符 [Comparison operators][比较运算符]
            - 相等运算符（`===`, `!==`, `==`, `!=`, 一般用前2种，不用后2种）
            - 关系运算符（`>`, `>=`, `<`, `<=` ）

        - 逻辑运算符（`&&`, `||`, `!`）[Logical Operators][逻辑运算符]
            - `&&`, `||` 部分情况下是返回布尔值; `!` 返回布尔值
                ```js
                // Example-4.4: 布尔值

                const num = 13;

                true && true // => true
                num < 14 && num // => 13

                false || (num === 17) // => false
                false || num // => 13

                !num // => false
                !!num // => true
                ```

    - 数字1和字符串'1'的比较
    
        ```js
        1 === '1' // => false 不进行类型转换，不相等。

        1 > '1' // => false 因为用比较运算符时，'1'会进行类型转换，变为数字1。
        ```

- 4.5 怎么访问对象的属性？可以给某个对象动态地添加属性吗？

    - 访问对象的属性

        - 用`对象.属性`访问，属性名属性名必须是一个有效的变量名。若包含特殊字符，用`['']`访问。（标识符，静态）

        - 用`对象['键']`访问。（字符串，动态）

    - 动态添加属性
    
        依据[对象的属性差异 - 一起学JS](https://xugaoyang.com/post/WfYxHyN7Wf)

        ```js
        // Example-4.5: 对象

        const obj = {}; // 或者：`let obj = new Object();`

        const list = ['xiaoming', 'xiaohua'];

        for (var i = 0; i < list.length; i++) {
        obj['name=' + list[i]] = list[i];
        }

        console.log(obj);
        // => { 'name=xiaoming': 'xiaoming', 'name=xiaohua': 'xiaohua' }

        ```

        ```js
        // Example-4.5-2: 构建动态键的应用场景
        var obj = {};

        getData(page) {
        axios.get('url', params: {page}).then(function (response) {
            obj['page=' + page] = response.data;
        });
        }

        getData(1);
        getData(2);
        getData(3);

        //以上obj感觉像是一个键值对的数据库对象，再使用内容时操作起来很方便。
        ```

大纲来源：[JavaScript的语法学习指引](https://xugaoyang.com/post/Ao4OQ0GfRF)

[函数]:https://github.com/xugy0926/getting-started-with-javascript/blob/master/topics/JS%E5%87%BD%E6%95%B0.md

[函数定义]:https://github.com/xugy0926/getting-started-with-javascript/blob/master/topics/%E4%B8%A4%E7%A7%8D%E5%AE%9A%E4%B9%89%E5%87%BD%E6%95%B0%E6%96%B9%E5%BC%8F%E7%9A%84%E5%B7%AE%E5%BC%82.md

[.pop]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop

[.shift]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift

[.push]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push

[.unshift]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift

[.splice]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice

[比较运算符]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators

[逻辑运算符]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators