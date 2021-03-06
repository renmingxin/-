# Vue基础部分
## 1.Vue的 nextTick 原理
答:
1. nextTick 可以让我们在下次 DOM 更新循环结束之后执行延迟回调，用于获得更新后的 DOM。
2. 在created()钩子函数执行的时候DOM 其实并未进行任何渲染，而此时进行DOM操作无异于徒劳，所以此处一定要将DOM操作的js代码放进Vue.nextTick()的回调函数中。与之对应的就是mounted()钩子函数，因为该钩子函数执行时所有的DOM挂载和渲染都已完成，此时在该钩子函数中进行任何DOM操作都不会有问题 。
+ href：'./vue/1.Vue的 nextTick 原理.html'

## 2.Vue的生命周期？
答：
1. 创建前(beforeCreate)：实例创建前，this指向创建的实例，不能访问到data、computed、watch、methods上的方法和数据。
2. 创建后(create)：实例创建完成，可访问data、computed、watch、methods上的方法和数据、未挂载到DOM上、不能访问到$el属性、$ref属性内容为空数组。
3. 挂载前(beforeMount)：在挂载开始之前被调用，beforeMount之前，会找到对应的template，并编译成render函数，beforeMount 就是在挂载前执行的，然后开始创建 VDOM 并替换成真实 DOM，最后执行。
4. 挂载后(mounted)：实例挂载到DOM上，此时可以通过DOM API获取到DOM节点，$ref属性可以访问。
5. 更新前(beforeUpdate)：响应式数据更新时调用，发生在虚拟DOM打补丁之前。
6. 更新后(updated)：虚拟DOM重新渲染和打补丁之后调用，组件DOM已经更新，可执行依赖于DOM的操作
7. 销毁前(beforeDestroy)：实例销毁之前调用。这一步，实例仍然完全可用，this仍能获取到实例。
8. 销毁后(destoryed)：实例销毁后调用，调用后，vue实例指示的所有东西会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。
+ <-注意->：
created阶段的ajax请求与mounted请求的区别：前者页面视图未出现，如果请求信息过多，页面会长时间处于白屏状态。

+ <-单个组件的生命周期->：
    1. 初始化组件时，仅执行了beforeCreate/Created/beforeMount/mounted四个钩子函数
    2. 当改变data中定义的变量（响应式变量）时，会执行beforeUpdate/updated钩子函数
    3. 当切换组件（当前组件未缓存）时，会执行beforeDestory/destroyed钩子函数
    4. 初始化和销毁时的生命钩子函数均只会执行一次，beforeUpdate/updated可多次执行

+ href：'./vue/2.vue的生命周期.html'

## 3.v-model的原理？
答：
+ 官方文档中对v-model的描写仅仅是一个语法糖。
1. 先靠v-bind：绑定响应式数据
2. 触发v-on：input事件
+ href：'./vue/3.v-model的原理.html'

## 4.watch和computed的区别和运用的场景
答：
1. 前端是计算属性，依赖其他属性计算值。并且computer的值有缓存，只有当计算值变化才变化触发渲染。后者监听值的变化就会执行回调。
2. computer就是简单的计算一下，适用于渲染页面。watch适合做一些复杂的业务逻辑。
3. 前者有依赖两个watcher，computer watcher 和渲染watcher。判断计算出的值变化后渲染watcher派发更新触发渲染。
+ 业务差异：
    1. computed是计算一个新的属性，并将该属性挂载到vm（Vue实例）上，而watch是监听已经存在且已挂载到vm上的数据，所以用watch同样可以监听computed计算属性的变化（其它还有data、props）
    2. computed本质是一个惰性求值的观察者，具有缓存性，只有当依赖变化后，第一次访问 computed 属性，才会计算新的值，而watch则是当数据发生变化便会调用执行函数
    3. 从使用场景上说，computed适用一个数据被多个数据影响，而watch适用一个数据影响多个数据；

## 5.Vue的响应系统核心？
答：
1. observe:遍历data中的属性，使用 Object.defineProperty 的get/set方法对其进行数据劫持
2. dep：每个属性拥有自己的消息订阅器dep，用于存放所有订阅了该属性的观察者对象
3. watcher：观察者（对象），通过dep实现对响应属性的监听，监听到结果后，主动触发自己的回调进行响应

## 6.Vue父子之间的通信？
答：
1. 使用v-model实现父传子，子传父。因为v-model默认解析成:value和:input
2. 父传子：
    1. 通过props
    2. 通过$children 访问子组件数组，注意该数组乱序
    3. 对于多级父传子，可以使用v-bind={$attrs},通过对象的方式筛选出父组件中传入子组件不需要的props
    4. $listens包含了父作用域中的(不含 .native修饰器的) v-on 事件监听器
2. 子传父
    1. 父组件传递函数给子组件，子组件通过$emit触发
    2. 修改父组件的props
    3. 通过$parent访问父组件
    4. .sync
3. 兄弟(平行)组件
    1. EventBus
4. Vuex 解决一切

## 7.路由原理？
答：
+ 前端路由实现起来其实很简单，本质就是监听 URL 的变化，然后匹配路由规则，显示相应的页面，并且无须刷新。目前单页面使用的路由就只有两种实现方式
  1. hash模式
  2. history模式

+ hash模式：www.test.com/#/ 就是 Hash URL，当 # 后面的哈希值发生变化时，不会向服务器请求数据，可以通过 hashchange 事件来监听到 URL 的变化，从而进行跳转页面。

+ History模式:是 HTML5 新推出的功能，比之 Hash URL 更加美观

## 8.自我封装一个vue-router？

## 9.Vue数据劫持？
答：
+ href:'@/EmploymentClass/数据双向绑定/数据劫持.html'

## 10.什么是MVC？什么是MVVM？
答：

 + __MVC的定义__：是Model-View-Controller的简写。即模型-视图-控制器。M和V指的意思跟MVVM中的M和V的意思一样。C即是Controller指的是页面业务逻辑。使用MVC的目的就是将M和V的代码分离。MVC是单向通信。也就是View跟Model，必须通过Controller来承上启下。

 + __MVVM的定义__:是Mode-View-ViewModel的简写。即模型-视图-视图模型。【视图模型】是MVVM模式的核心，它是链接view和model的桥梁。它有两个方向：一是将【模型】转化成【视图】，即将后端传递的数据转化成所看到的页面。实现的方式是：实属绑定。而是将【视图】转化成【模型】,即将所看到的页面转化为后端的数据。实现的方式是：DOM事件监听。这两个方向都实现的，我们称之为数据双向绑定。总结：在MVVM的框架下视图和模型是不能直接通信的。它们通过ViewModel来通信，ViewModel通常要实现一个observer观察者，当数据发生变化，ViewModel能够监听到数据的变化，然后通知到对应的视图走自动更新，而当用户操作视图，ViewModel也能监听到视图的变化，然后通知数据做改动，这实际上实现了数据的双向绑定。并且MVVM中的View和ViewModel可以互相通信。

 + __MVVM总结__：
 在JQuery时期，如果需要刷新UI时，需要先取到的DOM再更新UI，这样数据和业务的逻辑就和页面有强耦合。
 在MVVM中，UI是通过数据驱动的，数据一旦改变就会相应的刷新对应的UI，UI如果改变，也会改变对应的数据。这种方式就可以在业务处理中只关心数据的流转，而无需直接和页面打交道。ViewModel只关心数据和业务的处理，不关心View如何处理数据，在这种情况下，View和Model都可以独立出来，任何一方改变了也不一定需要改变另一方，并且可以将一些可服用的逻辑放在一个ViewModel中，让多个View复用这个ViewModel。

 + 在MVVM中，最核心的业就是数据双向绑定，例如Angluar的脏数据监测，Vue中的数据劫持。
   1. 脏数据检测：当触发了指定事件后会进入脏数据检测，这时会调用$digest循环遍历所有的数据观察者，判断当前值是否和先前的值有区别，如果检测到变化的话，会调用$watch函数，然后再次调用$digest循环直到发现没有变化。循环至少为2次，至多为10次。
   脏数据检测虽然存在低效的问题，但是不关心数据是通过什么方式改变的，都可以完成任务，但是这在Vue中的双向绑定是存在问题的。并且脏数据检测可以实现批量检测出更新的值，再去统一更新UI，大大减少了操作DOM的次数。所以低效也是相对的，这就仁者见仁智者见智了。

   2. 数据劫持：Vue内部使用了Object.definePropery()来实现双向绑定，通过这个函数可以监听到set和get的事件。href:'@/EmploymentClass/数据双向绑定/数据劫持.html'  
   > url：‘https://juejin.im/post/5ba34e54e51d450e5162789b#heading-68’
 
 + href:'@/深入vue源码/重写getter，setter源码实现MVVM原理/index.html'

 ## 11.Proxy与Object.defineProperty对比

 + Object.defineProperty 虽然已经能够实现双向绑定了，但是他还是有缺陷的。
   1. 只能对属性进行数据劫持，所以需要深度遍历整个对象。
   2. 对于数组不能监听到数据的变化。
   3. 数组的方法也是在原型上二次添加进去的

+ 反观Proxy就没以上的问题，原生支持监听数组的变化，并且可以直接对整个对象进行拦截，所以Vue也将在下个大版本中使用Proxy替换Object.defineProperty

## 12.什么是虚拟DOM？为什么需要虚拟DOM？算法的实现？
答:
1.  众所周知，操作 DOM 是很耗费性能的一件事情，既然如此，我们可以考虑通过 JS 对象来模拟 DOM 对象，毕竟操作 JS 对象比操作 DOM 省时的多。
2.  不需要数据双向绑定修改后刷新所有的dom，修改哪里就只干涉那一小部分。
3.  还有个重要一点实虚拟dom可以跨平台：
4. 虚拟 DOM 是 DOM 在内存中的一种轻量级表达方式，是一种统一约定！可以通过不同的渲染引擎生成不同平台下的 UI！
5. 虚拟 DOM 的可移植性非常好，这意味着可以渲染到 DOM 以外的任何端


+ 总结一下虚拟DOM算法：
  1. 通过JS来模拟创建DOM对象
  2. 判断两个对象的差异 
  3. 渲染差异

+ 总结：可维护性、最小的代价、效率、函数式UI、数据驱动

+ href:'https://juejin.im/post/5b10dd36e51d4506e04cf802'

## 13.什么是路由鉴权（指验证用户是否拥有访问系统的权利）
答：
1. 登录页和其他页面分开，登录以后实例化Vue并且初始化需要的路由
2. 动态路由，通过addRoute实现

## 14.请谈谈Vue和React的区别？
1. React是单向数据流，Vue双向数据绑定（更好进行表单验证）。
2. 改变数据方式不同，setState有使用坑。
3. props Vue可变（但是会发出警告），React不可变
4. 判断是否需要更新React可以通过钩子函数判断，Vue使用依赖追踪，修改了什么菜渲染什么
5. React 16版本以后 有些钩子函数会执行多次
6. React需要使用JSX，需要Babel编译。Vue虽然可以使用模板，但是也可以通过直接编写render函数不需要编译就能运行。
7. React的思路是all in js（万物都可js），通过js生成html所以设计了jsx，可以用js来操作css。Vue则是把html，css，js写到一个文件中，用各自处理的方式。html提供了模板引擎来处理。
8. React的组件是es6类的写法。Vue是生命式的写法，通过传入各种options，api和参数都很多。所以React更容易和TS结合，而Vue稍微复杂。

+ 而这两点的区别，其实也是因为 React 和 Vue的设计理念上的区别。React更偏向于构建稳定大型的应用，非常的科班化。相比之下，Vue更偏向于简单迅速的解决问题，更灵活，不那么严格遵循条条框框。因此也会给人一种大型项目用React，小型项目用 Vue的感觉。

## 15.你知道vue3.0更新后的改变吗？
答：
1. Virtual DOM（虚拟DOM）完全重写，大大优化。
2. 增多一些编译提醒 来介绍运行的成本。
3. 废除Object.definedProperty来监听数据变动，改用原生ES6中的Proxy观察机制。
4. 组件生成增快100%
5. 速度快一倍/减少一半的内存使用
6. 新的 runtime 版只要约 10kb gzipped
7. 自定义的renderer API
8. 更好的拥抱TS（因为本身就是用TS重写Vue3），因为可以像写React类组件的方式书写Vue。

## 16.第一次页面加载会触发哪几个钩子？
答：会触发 下面这几个beforeCreate, created, beforeMount, mounted 。


## 17.vue中key值的作用？
答：当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。key的作用主要是为了高效的更新虚拟DOM。

## 18.vue中计算属性computed的优势？
答：
1. 使得数据处理结构清晰；
2. 依赖于数据，数据更新，处理结果自动更新；
3. 计算属性内部this指向vm实例；
4. 在template调用时，直接写计算属性名即可；
5. 相较于methods，不管依赖的数据变不变，methods都会重新计算，但是依赖数据不变的时候computed从缓存中获取，不会重新计算。

## 19.vue等单页面应用及其优缺点？
答：
+ 优点：Vue 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件，核心是一个响应的数据绑定系统。MVVM、数据驱动、组件化、轻量、简洁、高效、快速、模块友好。
+ 缺点：不支持低版本的浏览器，最低只支持到IE9；不利于SEO的优化（如果要支持SEO，建议通过服务端来进行渲染组件）；第一次加载首页耗时相对长一些；不可以使用浏览器的导航按钮需要自行实现前进、后退。

## 20.Vue在created和mounted这两个生命周期中请求数据有什么区别呢？
答：在created中，页面视图未出现，如果请求信息过多，页面会长时间处于白屏状态，DOM节点没出来，无法操作DOM节点。在mounted不会这样，比较好。

## 21.说说你对keep-alive的理解
答：keep-alive是一个抽象组件：它自身不会渲染一个DOM元素，也不会出现在父组件链中；使用keep-alive包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。
+ 其有三个参数（include,exclude,max）
+ include定义缓存白名单，会缓存的组件；
+ exclude定义缓存黑名单，不会缓存的组件；
+ 以上两个参数可以是逗号分隔字符串、正则表达式或一个数组,include="a,b"、:include="/a|b/"、:include="['a', 'b']"；
+ 匹配首先检查组件自身的 name 选项，如果 name 选项不可用，则匹配它的局部注册名称 (父组件 components 选项的键值)。匿名组件不能被匹配；
+ max最多可以缓存多少组件实例。一旦这个数字达到了，在新实例被创建之前，已缓存组件中最久没有被访问的实例会被销毁掉；
+ 不会在函数式组件中正常工作，因为它们没有缓存实例；
+ 当组件在内被切换，它的activated和deactivated这两个生命周期钩子函数将会被对应执行。

## 22.v-if和v-for的优先级是什么？如果这两个同时出现时，那应该怎么优化才能得到更好的性能？
答：当它们处于同一节点，v-for的优先级比v-if更高，这意味着v-if将分别重复运行于每个v-for循环中。当你只想为部分项渲染节点时，这种优先级的机制会十分有用。

## 23.使用v-for遍历对象时，是按什么顺序遍历的？如何保证顺序？
答：按Object.keys()的顺序的遍历，转成数组保证顺序。

## 24.在v-for中使用key，会提升性能吗，为什么？
答：如果渲染不是一个简单的列表，用key性能会更好一点，因为vue是采用diff算法来对比新旧虚拟节点来更新节点，在diff算法中，当新节点跟旧节点头尾交叉对比没有结果时，先处理旧节点生成一个健为key，值为节点下标index的map映射，如果新节点有key，会通过map映射找到对应的旧节点，如果新节点没有key，会采用遍历查找的方式去找到对应的旧节点，一种一个map映射，另一种是遍历查找。相比而言。map映射的速度更快。

## 25.key除了在v-for中使用，还有什么作用？
答：
还可以强制替换元素/组件而不是重复使用它。在以下场景可以使用

+ 完整地触发组件的生命周期钩子
+ 触发过渡
```html
    <transition>
        <span :key="text">{{ text }}</span>
    </transition>
```
当 text 发生改变时，<span>会随时被更新，因此会触发过渡。

## 26.使用key要什么要注意的吗？
答：
1. 不要使用对象或数组之类的非基本类型值作为key，请用字符串或数值类型的值；
2. 不要使用数组的index作为key值，因为在删除数组某一项，index也会随之变化，导致key变化，渲染会出错。
    + 例：在渲染[a,b,c]用 index 作为 key，那么在删除第二项的时候，index 就会从 0 1 2 变成 0 1（而不是 0 2)，随之第三项的key变成1了，就会误把第三项删除了。


## 27.说说组件的命名规范
答：给组件命名有两种方式，一种是使用链式命名my-component，一种是使用大驼峰命名MyComponent，

## 28.为什么组件中data必须用函数返回一个对象？
答：对象为引用类型，当重用组件时，由于数据对象都指向同一个data对象，当在一个组件中修改data时，其他重用的组件中的data会同时被修改；而使用返回对象的函数，由于每次返回的都是一个新对象（Object的实例），引用地址不同，则不会出现这个问题。

## 29.Vue父子组件双向绑定的方法有哪些？
答：
1. 通过在父组件上自定义一个监听事件<myComponent @diy="handleDiy"></myComponent>,在子组件用this.$emit('diy',data)来触发这个diy事件，其中data为子组件向父组件通信的数据,在父组件中监听diy个事件时，可以通过$event访问data这个值。
2. 通过在父组件上用修饰符.sync绑定一个数据<myComponent :show.sync="show"></myComponent>,在子组件用this.$emit('update:show',data)来改变父组件中show的值。
3. 通过v-model。

## 30.组件的name选项有什么作用？
答：
1. 递归组件时，组件调用自身使用；
2. 用is特殊特性和component内置组件标签时使用；
3. keep-alive内置组件标签中include和exclude属性中使用。

## 31.什么是递归组件？举个例子说明下？
答：递归引用可以理解为组件调用自身，在开发多级菜单组件时就会用到，调用前要先设置组件的name选项， 注意一定要配合v-if使用，避免形成死循环，用element-vue组件库中NavMenu导航菜单组件开发多级菜单为例：
```html
<template>
    <el-submenu :index="menu.id" popper-class="layout-sider-submenu" :key="menu.id">
        <template slot="title">
            <Icon :type="menu.icon" v-if="menu.icon"/>
            <span>{{menu.title}}</span>
        </template>
        <template v-for="(child,i) in menu.menus">
            <side-menu-item v-if="Array.isArray(child.menus) && child.menus.length" :menu="child"></side-menu-item>
            <el-menu-item :index="child.id" :key="child.id" v-else>
                <Icon :type="child.icon" v-if="child.icon"/>
                <span>{{child.title}}</span>
            </el-menu-item>
        </template>
    </el-submenu>
</template>
<script>
    export default{
        name: 'sideMenuItem',
        props: {
            menu: {
                type: Object,
                default(){
                    return {};
                }
            }
        }
    }
</script>
```

## 32.说下$attrs和$listeners的使用场景？
答：
+ $attrs: 包含了父作用域中（组件标签）不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。
在创建基础组件时候经常使用，可以和组件选项inheritAttrs:false和配合使用在组件内部标签上用v-bind="$attrs"将非prop特性绑定上去；
+ $listeners: 包含了父作用域中（组件标签）的 (不含.native) v-on 事件监听器。
在组件上监听一些特定的事件，比如focus事件时，如果组件的根元素不是表单元素的，则监听不到，那么可以用v-on="$listeners"绑定到表单元素标签上解决。

## 33.EventBus注册在全局上时，路由切换时会重复触发事件，如何解决呢？
答：在有使用$on的组件中要在beforeDestroy钩子函数中用$off销毁。

## 34.Vue组件里写的原生addEventListeners监听事件，要手动去销毁吗？为什么？
答：要，不然会造成多次绑定和内存泄露。关于移除事件监听的坑。

## 35.Vue组件里的定时器要怎么销毁？
答：
+ 如果页面上有很多定时器，可以在data选项中创建一个对象timer，给每个定时器取个名字一一映射在对象timer中， 在beforeDestroy构造函数中for(let k in this.timer){clearInterval(k)}销毁。

## 36.Vue中能监听到数组变化的方法有哪些？为什么这些方法能监听到呢？
答：
+ push()、pop()、shift()、unshift()、splice()、sort()、reverse()，这些方法在Vue中被重新定义了，故可以监听到数组变化；
+ filter()、concat()、slice()，这些方法会返回一个新数组，也可以监听到数组的变化。

## 37.在Vue中那些数组变化无法监听，为什么，怎么解决？
答：
1. 用this.$set(this.items, indexOfItem, newValue)或this.items.splice(indexOfItem, 1, newValue)来解决第一种情况；
2. 修改数组的长度时。
    + 第一个情况，利用已有索引直接设置一个数组项时Object.defineProperty()是可以监听到，利用不存在的索引直接设置一个数组项时Object.defineProperty()是不可以监听到，但是官方给出的解释是由于JavaScript的限制，Vue不能检测以上数组的变动，其实根本原因是性能问题，性能代价和获得的用户体验收益不成正比。
    + 第二个情况，原因是Object.defineProperty()不能监听到数组的length属性。
3. 利用索引直接设置一个数组项时；
4. 用this.items.splice(newLength)来解决第二种情况。

## 38.在Vue中那些对象变化无法监听，为什么，怎么解决？
答：
1. 对象属性的添加用this.$set(this.obj,"key","newValue")来解决
2. 对象属性的删除 用Object.assign来解决第二种情况。

## 39.删除对象用delete和Vue.delete有什么区别？
答：
1. delete：只是被删除对象成员变为' '或undefined，其他元素键值不变；
2. Vue.delete：直接删了对象成员，如果对象是响应式的，确保删除能触发更新视图，这个方法主要用于避开 Vue 不能检测到属性被删除的限制。

## 40.watch和计算属性有什么区别？
答：
+ watch：一个数据影响多个数据，当需要在数据变化时执行异步或开销较大的操作时；
+ 计算属性：一个数据受多个数据影响。是基于它的响应式依赖进行缓存的，只在相关响应式依赖发生改变时它才会重新求值。

## 41.计算属性和方法有什么区别？
答：
+ 计算属性：是基于它们的响应式依赖进行缓存的，只在相关响应式依赖发生改变时它们才会重新求值。
+ 方法：每当触发重新渲染时，调用方法将总会再次执行函数。当我们不希望有缓存，可以使用方法，但是如果求值开销大时建议用计算属性。

## 42.说说你对DOM选项el、template、render的理解？
答：
+ el：提供一个在页面上已存在的DOM元素作为Vue实例的挂载目标。可以是CSS选择器，也可以是一个HTMLElement实例。
    + 因为所有的挂载元素会被Vue生成的DOM替换。因此不推荐挂载Vue实例到html或者body上。
    + 如果在const vm = new Vue({})中存在这个选项，实例将立即进入编译过程，否则，需要显式调用vm.$mount()手动开启编译。
```html
    <!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script>
    </head>
    <body>
        <div id="app">我是el挂载的内容:小明今年{{age}}岁了</div>
    </body>
    <script>
        const vm= new Vue({
            el:'#app',
            data:{
                age:17
            },
        }
    </script>
</html>
```
+ template：一个字符串模板作为Vue实例的标识使用。就是文档里面的html部分。如果el存在，模板将会替换挂载的元素。挂载元素的内容都将被忽略，除非模板的内容有分发插槽。
    + 如果值以 # 开始，则它将被用作选择符，并使用匹配元素的 innerHTML 作为模板。
```html
    <script>
    const vm= new Vue({
        el:'#app',
        data:{
            age:17
        },
        template:'<div>我是template的内容:小明今年{{age}}岁了</div>',
    })
</script>

```
```html
    <script type="x-template" id="mb">
	<div>我是template的内容:小明今年{{age}}岁了</div>
    </script>
    <script>
        const vm= new Vue({
            el:'#app',
            data:{
                age:17
            },
            template:'#mb',
        })
    </script>
```
+ render :Vue 选项中的 render 函数若存在，则 Vue 构造函数不会从 template 选项或通过 el 选项指定的挂载元素中提取出的 HTML 模板编译渲染函数。
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script>
    </head>
    <body>
        <div id="app">
            我是el挂载的内容:小明今年{{age}}岁了
        </div>
    </body>
    <script>
        const vm= new Vue({
            el:'#app',
            data:{
                age:17
            },
            template:'<div>我是template的内容:小明今年{{age}}岁了</div>',
            render(h){
                return h('div',`我是render的内容:小明今年${this.age}岁了`)
            }
        })
    </script>
</html>

```
+ 最后会输出我是render的内容:小明今年17岁了。

## 43.<template></template>有什么用？
答：当做一个不可见的包裹元素，减少不必要的DOM元素，整个结构会更加清晰。

## 44.Vue变量名如果以_、$开头的属性会发生什么问题？怎么访问到它们的值？
答：以 _或 $ 开头的属性 不会 被 Vue 实例代理，因为它们可能和 Vue 内置的属性、API 方法冲突，你可以使用例如 vm.$data._property 的方式访问这些属性。

## 44.Vue.observable你有了解过吗？说说看
答：让一个对象可响应。可以作为最小化的跨组件状态存储器。

## 45.怎么修改Vue项目打包后生成文件路径？
答：
+ 在Vue CLI2中修改config/index.js文件中的build.assetsPublicPath的值；
+ 在Vue CLI3中配置publicPath的值。

## 46.怎么解决Vue项目打包后静态资源图片失效的问题？
答：在项目中一般通过配置alias路径别名的方式解决,下面是Vue CLI3的配置。
```js
    configureWebpack: {
    resolve: {
        extensions: ['.js', '.vue', '.json'],
        alias: {
            '@': resolve('src'),
            'assets': resolve('src/assets'),
            'css': resolve('src/assets/css'),
            'images': resolve('src/assets/images'),
        }
    },
},

    
```
## 47.怎么解决Vue中动态设置img的src不生效的问题？
答：因为动态添加src被当做静态资源处理了，没有进行编译，所以要加上require。
```js
    <template>
        <img class="logo" :src="logo" alt="公司logo">
    </template>
    <script>
    export default {
        data() {
            return {
                logo:require("assets/images/logo.png"),
            };
        }
    };
    </script>
```
## 48.在Vue项目中如何引入第三方库（比如jQuery）？有哪些方法可以做到？
答：
1. 先在主入口页面 index.html 中用 script 标签引入<script src="./static/jquery-1.12.4.js"></script>,如果你的项目中有用ESLint检测，会报'$' is not defined，要在文件中加上/* eslint-disable */


2. 先在主入口页面 index.html 中用 script 标签引入<script src="./static/jquery-1.12.4.js"></script>,然后在webpack 中配置一个 externals，最后在main.js中用import $ from 'jquery'，即可在项目中使用。
```js
externals: {
    'jquery': 'jQuery'
}

```
3. 先在webpack中配置alias，最后在main.js中用import $ from 'jquery'，即可在项目中使用。
```js
resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
        '@': resolve('src'),
        'jquery': resolve('static/jquery-1.12.4.js')
    }
}

```
4.在webpack中新增一个plugins，即可在项目中使用
```js
plugins: [
        new webpack.ProvidePlugin({
            $:"jquery",
            jQuery:"jquery",
            "windows.jQuery":"jquery"
        })
    ]
```

# vuex部分
## 1.vuex是什么？
答：Vuex 是一个专为 Vue.js 应用程序开发的状态管理插件。它采用集中式存储管理应用的所有组件的状态，而更改状态的唯一方法是提交mutation。

## 2.Vuex解决了什么问题？
答：
1. 多个组件依赖于同一状态时，对于多层嵌套的组件的传参将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。
2. 来自不同组件的行为需要变更同一状态。以往采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

## 3.什么时候用Vuex？
答：
1. 多个组件依赖于同一状态时。
2. 来自不同组件的行为需要变更同一状态。

## 4.Vuex的5个核心属性是什么？
答：分别是 state、getters、mutations、actions、modules 。

## 5.Vuex中状态储存在哪里，怎么改变它？
答：存储在state中，改变Vuex中的状态的唯一途径就是显式地提交 (commit) mutation。

## 6.Vuex中状态是对象时，使用时要注意什么？
答：因为对象是引用类型，复制后改变属性还是会影响原始数据，这样会改变state里面的状态，是不允许，所以先用深度克隆复制对象，再修改。

## 7.怎么在组件中批量使用Vuex的state、getter、mutation、action？
答：
+ 使用mapState辅助函数, 利用对象展开运算符将state混入computed对象中。
+ 使用mapGetters辅助函数, 利用对象展开运算符将getter混入computed 对象中。
+ 使用mapMutations辅助函数,在组件中这么使用
+ 使用mapActions辅助函数,在组件中这么使用
```javascript
    import {mapState} from 'vuex'
    export default{
        computed:{
            ...mapState(['price','number'])
        }
    }
```
```javascript
    import {mapGetters} from 'vuex'
    export default{
        computed:{
            ...mapGetters({
                myTotal:'total',
                myDiscountTotal:'discountTotal',
            })
        }
    }
```
```javascript
    import { mapMutations } from 'vuex'
    methods:{
        ...mapMutations({
            setNumber:'SET_NUMBER',
        })
    }
    this.setNumber(10)
```
```javascript
    methods:{
        ...mapActions({
            setNumber:'SET_NUMBER',
        })
    }
    //然后调用this.setNumber(10)相当调用this.$store.dispatch('SET_NUMBER',10)
```
## 8.Vuex中要从state派生一些状态出来，且多个组件使用它，该怎么做，？
答：
1. 使用getter属性，相当Vue中的计算属性computed，只有原状态改变派生状态才会改变。
```javascript
    const store = new Vuex.Store({
    state: {
        price: 10,
        number: 10,
        discount: 0.7,
    },
    getters: {
        total: state => {
            return state.price * state.number
        },
        discountTotal: (state, getters) => {
            return state.discount * getters.total
        }
    },
});
```
2. 然后在组件中可以用计算属性computed通过this.$store.getters.total这样来访问这些派生转态。
```javascript
    computed: {
    total() {
        return this.$store.getters.total
    },
    discountTotal() {
        return this.$store.getters.discountTotal
    }
}
```
## 9.在Vuex中使用mutation要注意什么。
答：mutation 必须是同步函数

## 10.Vuex中action和mutation有什么区别？
答：
1. action 提交的是 mutation，而不是直接变更状态。mutation可以直接变更状态。
2. action 可以包含任意异步操作。mutation只能是同步操作。
3. 提交方式不同，action 是用this.$store.dispatch('ACTION_NAME',data)来提交。mutation是用this.$store.commit('SET_NUMBER',10)来提交。
4. 接收参数不同，mutation第一个参数是state，而action第一个参数是context，其包含了

## 11.Vuex中action和mutation有什么相同点？
答：第二参数都可以接收外部提交时传来的参数。 this.$store.dispatch('ACTION_NAME',data)和this.$store.commit('SET_NUMBER',10)

## 12.Vuex中action通常是异步的，那么如何知道action什么时候结束呢？
答：
+ 在action函数中返回Promise，然后再提交时候用then处理
```javascript
    actions:{
        SET_NUMBER_A({commit},data){
            return new Promise((resolve,reject) =>{
                setTimeout(() =>{
                    commit('SET_NUMBER',10);
                    resolve();
                },2000)
            })
        }
    }
    this.$store.dispatch('SET_NUMBER_A').then(() => {
    // ...
})
```

## 13. 有用过Vuex模块吗，为什么要使用，怎么使用？
答：
+ 有，因为使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。所以将 store 分割成模块（module）。每个模块拥有自己的 state、mutations、actions、getters，甚至是嵌套子模块，从上至下进行同样方式的分割。
在module文件新建moduleA.js和moduleB.js文件。在文件中写入
```javascript
    const state={
        //...
    }
    const getters={
        //...
    }
    const mutations={
        //...
    }
    const actions={
        //...
    }
    export default{
        state,
        getters,
        mutations,
        actions
    }
```
然后再index.js引入模块
```javascript
    import Vue from 'vue';
    import Vuex from 'vuex';
    Vue.use(Vuex);
    import moduleA from './module/moduleA'
    import moduleB from './module/moduleB'
    const store = new Vuex.Store({
        modules:{
            moduleA,
            moduleB
        }
    })
    export default store
```

## 14. 在模块中，getter和mutation和action中怎么访问全局的state和getter？
答：
1. 在getter中可以通过第三个参数rootState访问到全局的state,可以通过第四个参数rootGetters访问到全局的getter。
2. 在mutation中不可以访问全局的satat和getter，只能访问到局部的state。
3. 在action中第一个参数context中的context.rootState访问到全局的state，context.rootGetters访问到全局的getter。

## 15.在组件中怎么访问Vuex模块中的getter和state,怎么提交mutation和action？
答：
1. 直接通过this.$store.getters和this.$store.state来访问模块中的getter和state。
2. 直接通过this.$store.commit('mutationA',data)提交模块中的mutation。
3. 直接通过this.$store.dispatch('actionA,data')提交模块中的action。

## 16.在Vuex插件中怎么监听组件中提交mutation和action？
答：
1. 用Vuex.Store的实例方法subscribe监听组件中提交mutation
2. 用Vuex.Store的实例方法subscribeAction监听组件中提交action 在store/plugin.js文件中写入

```javascript
export default function createPlugin(param) {
    return store => {
        store.subscribe((mutation, state) => {
            console.log(mutation.type)//是那个mutation
            console.log(mutation.payload)
            console.log(state)
        })
        // store.subscribeAction((action, state) => {
        //     console.log(action.type)//是那个action
        //     console.log(action.payload)//提交action的参数
        // })
        store.subscribeAction({
            before: (action, state) => {//提交action之前
                console.log(`before action ${action.type}`)
            },
            after: (action, state) => {//提交action之后
                console.log(`after action ${action.type}`)
            }
        })
    }
}
```
然后在store/index.js文件中写入
```javascript
    import createPlugin from './plugin.js'
    const myPlugin = createPlugin()
    const store = new Vuex.Store({
    // ...
    plugins: [myPlugin]
    })
```
## 17.在v-model上怎么用Vuex中state的值？
答：需要通过computed计算属性来转换。
```javascript
   <input v-model="message">
    computed: {
        message: {
            get () {
                return this.$store.state.message
            },
            set (value) {
                this.$store.commit('updateMessage', value)
            }
        }
    }
```

# vue-router部分

## 1. 怎么重定向页面？
答：

```javascript
    const router = new VueRouter({
    routes: [
        { path: '/a', redirect: '/b' }
    ]
})
``` 

```javascript
    const router = new VueRouter({
    routes: [
        { path: '/a', redirect: '/b' }
    ]
})
```

## 2.怎么配置404页面

```javascript
    const router = new VueRouter({
        routes: [
            {
                path: '*', redirect: {path: '/'}
            }
        ]
    })
```

## 3.切换路由时，保存组件的值，怎么实现呢？
答：使用keep-alive标签包裹

```html
    <keep-alive :include="include">
        <router-view></router-view>
    </keep-alive>
```

## 4.路由有几种模式？说说它们的区别？
答：

1. hash模式：兼容所有浏览器，包括不支持 HTML5 History Api 的浏览器，例<http://www.abc.com/#/index，hash值为#/index>， hash的改变会触发hashchange事件，通过监听hashchange事件来完成操作实现前端路由。hash值变化不会让浏览器向服务器请求。

```javascript
    // 监听hash变化，点击浏览器的前进后退会触发
    window.addEventListener('hashchange', function(event){ 
        let newURL = event.newURL; // hash 改变后的新 url
        let oldURL = event.oldURL; // hash 改变前的旧 url
    },false)
```

2. history: 兼容能支持 HTML5 History Api 的浏览器，依赖HTML5 History API来实现前端路由。没有#，路由地址跟正常的url一样，但是初次访问或者刷新都会向服务器请求，如果没有请求到对应的资源就会返回404，所以路由地址匹配不到任何静态资源，则应该返回同一个index.html 页面，需要在nginx中配置。

3. abstract: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式。

## 5.讲一下完整的导航守卫流程？

1. 导航被触发。
2. 在失活的组件里调用离开守卫beforeRouteLeave(to,from,next)。
3. 调用全局的beforeEach( (to,from,next) =>{} )守卫。
4. 在重用的组件里调用 beforeRouteUpdate(to,from,next) 守卫。
5. 在路由配置里调用beforeEnter(to,from,next)路由独享的守卫。
6. 解析异步路由组件。
7. 在被激活的组件里调用beforeRouteEnter(to,from,next)。
8. 在所有组件内守卫和异步路由组件被解析之后调用全局的beforeResolve( (to,from,next) =>{} )解析守卫。
9. 导航被确认。
10. 调用全局的afterEach( (to,from) =>{} )钩子。
11. 触发 DOM 更新。
12. 用创建好的实例调用beforeRouteEnter守卫中传给 next 的回调函数

## 6.路由导航守卫和Vue实例生命周期钩子函数的执行顺序?谁前谁后？

答：路由导航守卫都是在Vue实例生命周期钩子函数之前执行的。

## 7.讲一下导航守卫的三个参数的含义？（to,from,next）?

答：
1. to：即将要进入的目标 路由对象。
2. from：当前导航正要离开的路由对象。
3. next：函数，必须调用，不然路由跳转不过去。
    + next()：进入下一个路由。
    + next(false)：中断当前的导航。
    + next('/')或next({ path: '/' }) : 跳转到其他路由，当前导航被中断，进行新的一个导航。

## 8. 在afterEach钩子中可以使用next()吗？
答：不可以，不接受next的参数。

## 9.全局导航守卫有哪些？怎么使用？
答：

1. router.beforeEach：全局前置守卫。
2. router.beforeResolve：全局解析守卫。
3. router.afterEach：全局后置钩子。

```javascript
    import VueRouter from 'vue-router';
    const router = new VueRouter({
        mode: 'history',
        base: '/',
        routes,
        scrollBehavior(to, from, savedPosition) {
            if (savedPosition) {
                return savedPosition;
            } else {
                return { x: 0, y: 0 };
            }
        }
    })
    router.beforeEach((to, from, next) => {
        //...
        next();
    })
    router.beforeResolve((to, from, next) => {
        //...
        next();
    })
    router.afterEach((to, from) => {
        //...
    });
```
## 10.什么是路由独享的守卫，怎么使用？
答：是beforeEnter守卫

```javascript
const router = new VueRouter({
    routes: [
        {
            path: '/foo',
            component: Foo,
            beforeEnter: (to, from, next) => {
            // ...
            }
        }
    ]
})
```
## 11.在组件内使用的导航守卫有哪些？怎么使用？
答：

1. beforeRouteLeave：在失活的组件里调用离开守卫。
2. beforeRouteUpdate：在重用的组件里调用,比如包含 router-view 的组件。
3. beforeRouteEnter：在进入对应路由的组件创建前调用。

```javascript
    beforeRouteLeave(to, from, next) {
        //...
    },
    beforeRouteUpdate(to, from, next) {
        //...
    },
    beforeRouteEnter(to, from, next) {
        //...
    },
```

## 12.在beforeRouteEnter导航守卫中可以用this吗？
答：不可以，因为守卫在导航确认前被调用,因此即将登场的新组件还没被创建。
可以通过传一个回调给next来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。
```javascript
    beforeRouteEnter(to, from, next) {
        next(vm => {
            console.log(vm)
        })
    }
```
## 13.说说你对router-link的了解
答：
+ router-link 是Vue-Router的内置组件，在具有路由功能的应用中作为声明式的导航使用。
+ router-link 有8个props，其作用是：
    1. to：必填，表示目标路由的链接。当被点击后，内部会立刻把to的值传到router.push()，所以这个值可以是一个字符串或者是描述目标位置的对象。

    ```html
        <router-link to="home">Home</router-link>
        <router-link :to="'home'">Home</router-link>
        <router-link :to="{ path: 'home' }">Home</router-link>
        <router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
        <router-link :to="{ path: 'user', query: { userId: 123 }}">User</router-link>
        注意path存在时params不起作用，只能用query
    ```
    
    2. replace：默认值为false，若设置的话，当点击时，会调用router.replace()而不是router.push()，于是导航后不会留下 history 记录。
    3. append：设置 append 属性后，则在当前 (相对) 路径前添加基路径。
    4. tag：让<router-link>渲染成tag设置的标签，如tag:'li,渲染结果为<li>foo</li>。
    5. active-class：默认值为router-link-active,设置链接激活时使用的 CSS 类名。默认值可以通过路由的构造选项 linkActiveClass 来全局配置。
    6. exact-active-class：默认值为router-link-exact-active,设置链接被精确匹配的时候应该激活的 class。默认值可以通过路由构造函数选项 linkExactActiveClass 进行全局配置的。
    7. exact：是否精确匹配，默认为false。

        ```html
            <!-- 这个链接只会在地址为 / 的时候被激活 -->
            <router-link to="/" exact></router-link>
        ```
    8. event：声明可以用来触发导航的事件。可以是一个字符串或是一个包含字符串的数组，默认是click。

## 14.怎么在组件中监听路由参数的变化？
答：有两种方法可以监听路由参数的变化，但是只能用在包含router-view的组件内。
```javascript
watch: {
    '$route'(to, from) {
        //这里监听
    },
},
```

```javascript
beforeRouteUpdate (to, from, next) {
    //这里监听
},
```
## 15.切换路由后，新页面要滚动到顶部或保持原先的滚动位置怎么做呢？
```javascript
const router = new Router({
    mode: 'history',
    base: process.env.BASE_URL,
    routes,
    scrollBehavior(to, from, savedPosition) {
        if (savedPosition) {
            return savedPosition;
        } else {
            return { x: 0, y: 0 };
        }
    }
});

```
## 16.在什么场景下会用到嵌套路由？
答：做个管理系统，顶部栏和左侧菜单栏是全局通用的，那就应该放在父路由，而右下的页面内容部分放在子路由。
+ 比如在app.vue中
```html
<template>
    <div>
        <router-view/>
    </div>
</template>
```
+ 在layout.vue文件中
```html
<template>
  <div>
    <div>
        //...头部导航
    </div>
    <div>
        //...侧边栏导航
    </div>
    <div>
        //...主内容
        <router-view/>
    </div>
    
  </div>
</template>
```
+ 在routes.js文件中
```js
function load(component) {
    return resolve => require([`views/${component}`], resolve);
}
const routes=[
    {
        path: '/',
        redirect: '/home',
        name: 'layout',
        component: load('layout'),
        children: [
            {
                path: '/home',
                name: 'home',
                component: load('home'),
                meta: {
                    title: '首页'
                },
            },
        ]
    }
]

```
+ 然后layout页面就渲染在app.vue文件中的<router-view/>上。home页面就渲染在layout.vue文件夹中的<router-view/>上。

## 17.什么是命名视图，举个例子说明一下？
答：在项目中，我们想同级展示多个视图，而不是嵌套展示。例如项目首页，有头部导航，侧边栏导航、主内容区域。头部导航、侧边栏导航我们不想用组件方式引入，想用视图方式展示。那么这个首页上，就有三个视图，头部导航视图，侧边栏导航视图、主内容区域视图同级展示。
+ 在layout.vue文件中
```html
<template>
  <div>
    <div>
        //...头部导航
        <router-view name='header'></router-view>
    <div>
        //...侧边栏导航
        <router-view name='sider'></router-view>
    </div>
    <div>
        //...主内容
        <router-view/>
    </div>
  </div>
</template>
```
+ 如果 router-view 没有设置name，那么默认为default。一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 components 配置 (记得加上s)。
在routes.js文件中
```js
function load(component) {
    return resolve => require([`views/${component}`], resolve);
}
const routes=[
    {
        path: '/',
        redirect: '/home',
        name: 'layout',
        component: load('layout'),
        children: [
            {
                path: '/home',
                name: 'home',
                components: {
                    default: load('main'),
                    header: load('header'),
                    sider: load('sider')
                },
                meta: {
                    title: '首页'
                },
            },
        ]
    }
]

```

## 18.如何获取路由传过来的参数？ 
答：路由有三种传参方式，获取方式各不相同。
1. meta：路由元信息，写在routes配置文件中。
```js
{
    path: '/home',
    name: 'home',
    component: load('home'),
    meta: {
        title: '首页'
    },
},
```
获取方式this.$route.meta.title获取

2. query
```js
this.$route.push({
    path:'/home',
    query:{
        userId:123
    }
})
```
浏览器地址：http://localhost:8036/home?userId=123 
获取方式：this.$route.query.userId

3. params：这种方式比较麻烦。
```js
//在index.js里面的话首先要在地址上做配置
{
    path: '/home/:userId',
    name: 'home',
    component: load('home'),
    meta: {
        title: '首页'
    },
},

```
```js
//访问传参
    const userId = '123'
    this.$router.push({ name: 'home', params: { userId } })
```
注意用params传参，只能用命名的路由（用name访问），如果用path，params不起作用。
this.$router.push({ path: '/home', params: { userId }})不生效。

## 19.说说active-class是哪个组件的属性？
答：router-link组件的属性，设置链接激活时使用的 CSS 类名。默认值可以通过路由的构造选项 linkActiveClass 来全局配置。

## 20.在vue组件中怎么获取到当前的路由信息？
答：通过this.$route来获取

## 21.怎样动态加载路由？
答：使用Router的实例方法addRoutes来实现动态加载路由，一般用来实现菜单权限。使用时要注意，静态路由文件中不能有404路由，而要通过addRoutes一起动态添加进去。

```js
const routes = [
    {
        path: '/overview',
        name: 'overview',
        component: () => import('@/views/account/overview/index'),
        meta: {
            title: '账户概览',
            pid: 869,
            nid: 877
        },
    },
    {
        path: '*',
        redirect: {
            path: '/'
        }
    }
]
vm.$router.options.routes.push(...routes);
vm.$router.addRoutes(routes);
```

## 22.怎么实现路由懒加载呢？
答：
```js
function load(component) {
    return () => import(`views/${component}`);
}

const routes = [
    {
        path: '/home',
        name: 'home',
        component: load('home'),
        meta: {
            title: '首页'
        },
    },
]

```

## 23.路由之间是怎么跳转的？有哪些方式？
答：
1. 声明式 通过使用内置组件<router-link :to="/home">来跳转
2. 编程式 通过调用router实例的push方法router.push({ path: '/home' })或replace方法router.replace({ path: '/home' })

## 24.如果vue-router使用history模式，部署时要注意什么？
答：要注意404的问题，因为在history模式下，只是动态的通过js操作window.history来改变浏览器地址栏里的路径，并没有发起http请求，当直接在浏览器里输入这个地址的时候，就一定要对服务器发起http请求，但是这个目标在服务器上又不存在，所以会返回404。
所以要在Ngnix中将所有请求都转发到index.html上就可以了。
```code
    location / {
        try_files  $uri $uri/ @router index index.html;
    }
    location @router {
        rewrite ^.*$ /index.html last;
    }
```

## 24.route和router有什么区别？
答：
+ route是“路由信息对象”，包括path，params，hash，query，fullPath，matched，name等路由信息参数。 
+ router是“路由实例对象”，包括了路由的跳转方法，钩子函数等。

## 25.Vue路由怎么跳转打开新窗口？
```js
    const obj = {
    path: xxx,//路由地址
    query: {
       mid: data.id//可以带参数
    }
    };
    const {href} = this.$router.resolve(obj);
    window.open(href, '_blank');
```