#### js深入原型到原型链

首先，先用构造函数创建一个对象
```
function Person() {

}
const person = new Person();
person.name = 'yxh';
console.log(person,name); // yxh
```

以上Person就是一个构造函数，我们使用new创建了一个实例对象person。

##### 1.什么是prototype?
首先，每个函数都有一个prototype属性，并且是只有函数才会有的属性，
然后这个函数的prototype属性指向了一个对象，这个对象就是使用该构造函数创建的实例的原型
```
function Person() {

}

Person.prototype.name = 'yxh';
const person1 = new Person();
console.log(person1.name); // yxh
```
##### 2.那到底什么才是原型？
每一个js对象，除了null,在创建的时候就会与之关联另外一个对象，这个对象就是原型，也就是上面例子的person的原型

进一步的解释：每个构造函数都会有一个prototype的属性，这个属性指向由这个构造函数创建的实例的原型

##### 3.那我们要如何去将实例与实例的原型之间的关系表达出来呢？
答案是使用__proto__，每个js对象，除去null都具有这个属性，这个属性就指向该对象的原型，可以理解为这个对象
其实是内存中的一个地址，而不管是实例的__proto__，即person1.__proto__或是构造函数的prototype属性，即Person.prototype
都指向了这个地址，这样也就相当于是通过实例的__proto__属性，将实例将其原型联系起来了

即Person.prototype === person1.__proto__

##### 4.constructor是什么？
构造函数通过prototype指向了原型，而原型通过constructor属性指向回构造函数
即：Person.prototype.constructor === Person

##### 5.实例与原型的关系？
一句话简而言之：实例中没有的属性或是方法会到他的原型中去找，如果还是找不到，就去找原型的
原型，一直找到最顶层为止（这一条由原型串联起来的链条也就叫做原型链了）

##### 6.既然实例的原型中找不到某个属性或是方法时会去实例的原型的原型上去找，那么问题来了
原型的原型是什么？或是原型的原型的原型是什么？或是原型链的最顶端是什么？像是绕口令一样啊，(((φ(◎ロ◎;)φ)))
既然原型是一个对象，那么我们可以通过new Object()的方式来创建它，这样一来，我们可以很容易的知道
原型的原型其实是Object.prototype，也就是 Person.prototype.__proto__ === Object.prototype,
Object.prototype 这个原型也有自己 的constructor属性，这个属性指向Object构造函数

##### 7.既然我们知道了实例的原型的原型是Object.prototype，那我们再深入一下，实例的原型的原型的原型又是什么呢？
实例的原型的原型是Object.proptype，那么Object.prototype.__proto__其实是null,也就是
Object.prototype.__proto__ === null；null表示没有对象，此处不应该有值，也就是说原型的原型是没有原型的，到
Object.prototype就是原型链的顶端，查找属性或者方法也就是查找到Object.prototype属性

##### 8.那么原型链其实很明确了，顾名思义，原型链就是查找实例的某个属性和方法时各个原型串联起来查找的路径
即：person1 => Person.prototype => Object.prototype => null
而将各个原型链接起来产生关联的正是这个__proto__，
即: 1.person1.__proto__ === Person.prototype, 
    2.Person.prototype .__proto__ === Object.Prototype.
    3.Object.Prototype.__proto__ === null

##### 9.补充
person1.constructor === Person
person1中并没有constructor方法，所以去它的原型中去找，而原型中恰好有这个方法，所以以上成立
即Person.prototype.constructor === Person

##### 10.补充 Function.__proto__ === Function.prototype
以上并不是Function自己生成了自己，而是Function本身是一个内置对象，在运行前就已经存在了，所以并不会自己去生成自己，它一直都在，之所以这样写是为了和其他对象的原型链保持一致而已，可能是
js所特有的吧
***

#### js深入之词法作用域和动态作用域

##### 1.作用域是什么
域，顾名思义就是区域，作用域就是代码中定义变量的区域
其中作用域规定了如何查找变量，也就是确认当前执行的代码对变量的访问权限，也就是说代码仅能够访问到作用域内的变量
js使用的是静态作用域也就是词法作用域，与静态作用域向对应的是其他语言的动态作用域

##### 2.静态作用域与动态作用域的区别
静态作用域，函数的作用域在函数被定义的时候就确定了
动态作用域，函数的作用域是在函数被调用的时候才决定的

##### js的词法作用域的核心：函数的作用域在函数定义的时候就决定了，这句话其实也是闭包的核心，
***理解了这句话，闭包也就理解了***
***

#### js深入之执行上下文栈

##### 1.js引擎的执行顺序
js引擎并非是一行一行的去分析并执行程序，而是一段一段的分析执行的，当执行一段代码的时候，会进行一个准备工作
当执行一段代码时会进行一个准备工作，比如变量提升和函数提升

##### 2.那么这个一段一段的”段“到底是怎么划分的呢，而那个准备工作又是什么呢？
其实是根据可执行代码来进行划分的，共有三种，全局代码，函数代码和eval代码
而这个准备工作其实就是执行函数的时候去创建执行上下文（execution context）

##### 3.执行上下文栈又是什么？
那么当我们写的函数很多的时候，该如何管理创建的那么多执行上下文呢？答案就是使用执行上下文栈来管理执行上下文

##### 4.执行上下文栈的工作原理是什么？
js引擎在执行代码的时候，首先遇到的就是全局代码，然后创建全局执行上下文，所以一开始先会在执行上下文栈中压入一个全局执行上下文
只有当整个全局代码都执行结束的时候，执行上下文栈才会被清空，所以在全局代码结束之前，这个全局执行上下文
就一直都在执行上下文栈中
然后当js引擎遇到第二种可执行代码，函数代码的时候，就会创建该函数代码的执行上下文，然后将其压入执行上下文栈中
当这个函数执行完毕后，js引擎就会将该函数的执行上下文弹出执行上下文栈
***
#### js深入之变量对象
js引擎在执行函数时，都会创建对应的执行上下文，同时把这个执行上下文压入执行上下文栈进行管理，当函数执行完成后，再将该
执行上下文弹出执行上下文栈，
每个执行上下文，都包含三个很重要的属性
    1.变量对象（Variable, 也就是VO）
    2.作用域链
    3.this

##### 1.什么是变量对象
变量对象中保存了执行上下文中定义的变量和函数声明
而因为执行上下文分为全局执行上下文和函数执行上下文，所以变量对象也分为两种
一是全局执行上下文的变量对象，另一个是函数执行上下文的变量对象
我理解的是全局执行上下文的变量对象就是全局对象
果然猜对了。。哈哈

##### 2.全局对象中有什么
全局对象可以通过this引用，或者this就指向这个全局对象，在浏览器中全局对象就是window
全局对象是Object构造函数的一个实例
全局对象中有一堆预定义的函数和属性，这个打开浏览器的控制台，输入window就可以看到
全局对象中保存了各种全局变量，可以说全局对象是全局变量的宿主

既然全局执行上下文的变量对象就是全局变量，那么函数执行上下文的变量对象就是，，
其实是叫做活动对象也就是（activation object,也就是AO）

##### 3.活动对象（AO）和变量对象(VO)的区别？
js引擎在执行函数代码的时候，会创建函数的执行上下文，此时的执行上下文中的变量对象（VO）中的
属性都是不可以访问的，这个阶段属于未执行阶段，当js引擎将该函数执行上下文压入执行上下文栈执行的时候
此时就进入了执行阶段，而此时的变量对象（VO）就变成了活动对象（AO）,里面的属性就都能被访问了，其实他们都属于同一个
对象，只是在不同的执行阶段，名字不一样，可访问的内容也不一样

##### 4.那么在执行上下文的不同执行阶段分别都发生了什么呢
未执行阶段：变量对象初始化，只初始化了Arguments对象，这也就是此刻的变量对象中的属性不可以被访问的原因，因为没有可以访问的属性啊。。
执行阶段分为两个阶段：
    1.进入执行上下文：变量对象转变为活动对象，活动对象的各个属性被创建，即各种变量被创建，
    包括变量名和对应的值，但是没有实参的变量属性值设置为undefined；活动对象的函数被声明
    如果存在相同属性则替换覆盖这个属性
    2.执行阶段，就是会顺序执行代码，然后根据代码去修改活动对象的各种属性的值