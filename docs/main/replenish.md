---
title: React jsx -> fiber -> dom 的转化
# date: 2023-03-13 13:57:13
# categories: React
# tags: [React]
# cover:
---

## 读源码基础

1. 位运算
2. call、apply、bind 原理
3. 链表相关知识(单链表、双链表、循环链表)
4. 节流、防抖原理 `ensureRootIsScheduled: if (existingCallbackNode !== null) {`
5. 设计模式相关知识
6. 安全相关知识
7. 栈（数组） `pushProvider`
8. Set `合成事件`存储注册

## jsx -> React.createElement

<img src="http://t-blog-images.aijs.top/img/202303131356155.webp" />

## React.createElement -> ReactElment 树

<img src="https://7kms.github.io/react-illustration-series/static/reactelement-tree.31feb14c.png" style="width:500px;max-width:100%" />

## ReactElment -> update -> updateQueue -> fiber

<img src="http://t-blog-images.aijs.top/img/202303131435472.webp" />

## ReactElment 树 -> fiber 树 -> dom 树

<img src="https://7kms.github.io/react-illustration-series/static/code2dom.91f1b68b.png" />

**见下文 jsx 代码经 babel 到 ReactElment 树**

## jsx 代码经 babel 到 ReactElment 树

<img src="http://t-blog-images.aijs.top/img/202303131503939.webp" />

## this.setState -> fiber

<img src="http://t-blog-images.aijs.top/img/202303131647078.webp" />

## 补 React 图解

### processUpdateQueue 源码解析

<a href="https://juejin.cn/post/7088149898389880863" target="_blank" >processUpdateQueue 源码解析</a>

<a href="https://github.com/facebook/react/blob/cd20376f03212c91057c003bcb640ca9568e87e6/packages/react-reconciler/src/ReactFiberClassUpdateQueue.js#LL466-L667C2" target="_blank" >见 processUpdateQueue 源码</a>

### 如何计算新的 state

<a href="https://github.com/facebook/react/blob/cd20376f03212c91057c003bcb640ca9568e87e6/packages/react-reconciler/src/ReactFiberClassUpdateQueue.js#LL383-L464C2" target="_blank" >见 getStateFromUpdate 源码</a>

<img src="http://t-blog-images.aijs.top/img/202303131913820.webp" />

<a href="https://7kms.github.io/react-illustration-series/main/fibertree-create#%E6%8E%A2%E5%AF%BB%E9%98%B6%E6%AE%B5-beginwork:~:text=%E7%8A%B6%E6%80%81%20workInProgress.memoizedState-,processUpdateQueue,-(workInProgress%2C" target="_blank" >到这里了</a>

### nextProps 怎么会有 ReactElement 对象

```js
// 2. 获取下级`ReactElement`对象
let nextChildren = nextProps.children; // 利用jsx的特点？
```

<a href="https://7kms.github.io/react-illustration-series/main/fibertree-create#:~:text=%3A%20null%3B-,//%202.%20%E8%8E%B7%E5%8F%96%E4%B8%8B%E7%BA%A7%60ReactElement%60%E5%AF%B9%E8%B1%A1,let%20nextChildren%20%3D%20nextProps.children%3B,-const%20isDirectTextChild%20%3D" target="_blank" >见</a>

<img src="http://t-blog-images.aijs.top/img/202303132215856.webp" />

### beginwork

### stateNode: 类组件的 stateNode 是怎样的，hostElement 的 stateNode 是怎样的？

<img src="http://t-blog-images.aijs.top/img/202303132230243.webp" />

### 父子组件的副作用, 这个与执行顺序有关，父子组件的顺序问题

<img src="http://t-blog-images.aijs.top/img/202303132232294.webp" />

### bubbleProperties

<img src="http://t-blog-images.aijs.top/img/202303141032048.webp" />

<img src="http://t-blog-images.aijs.top/img/202303141038197.webp" />

<a href="https://github.com/facebook/react/blob/6bd53a5bdfe24d9a70819c4eba40b488e62900cd/packages/react-reconciler/src/ReactFiberCompleteWork.js#LL607-L728C2" target="_blank" >bubbleProperties 源码</a>

1. bubbleProperties 根据 fiber.child 及 fiber.child.sibling 更新 subtreeFlags 和 childLanes, 主要是为了标记子树有没有更新,
2. 这样可以通过 fiber.subtreeFlags 快速判断子树是否有副作用钩子，不需要深度遍历.
3. 在 React17 版本后使用 subtreeFlags 替换了 finishWork.firstEffect 的副作用链表, 操作主要发生在 bubbleProperties 函数中, 核心代码如下

作者：王八吉吉
链接：https://juejin.cn/post/6977203911799406628
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

<img src="http://t-blog-images.aijs.top/img/202303140938435.webp" style="width:600px;max-width:100%" />

### classInstance 的 render()

<img src="http://t-blog-images.aijs.top/img/202303140944164.webp" />

<img src="http://t-blog-images.aijs.top/img/202303141000464.webp" />

### 如何取到次级节点第一个 fiber 的？

reconcileChildren 用来向下构建 fiber 节点

<img src="http://t-blog-images.aijs.top/img/202303141020235.webp" />

### markUpdateLaneFromFiberToRoot 的 sourceFiber 不理解这个 sourceFiber 是如何取哪个作为 sourceFiber

### 更新过程中，DOM 属性变化，不创建 DOM 对象，仅标记，commit 阶段会处理

<img src="http://t-blog-images.aijs.top/img/202303141053033.webp" />

### fiber.lanes

1. 如何区别 fiber.lanes 有没有渲染优先级？
2. 渲染优先级什么时候加到 fiber 上的？ markUpdateLaneFromFiberToRoot

<a href="https://7kms.github.io/react-illustration-series/main/fibertree-update#markupdatelanefromfibertoroot" target="_blank" >markUpdateLaneFromFiberToRoot</a>

<img src="http://t-blog-images.aijs.top/img/202303141056621.webp" />

### props 中有节点的标签信息？buton 变成 a 会怎样？

<img src="http://t-blog-images.aijs.top/img/202303141105782.webp" />

### fiber 的 flags 属性，什么时候赋值的，这里有用 fiber.flags = 0 判断有没有副作用

1. `markUpdate`
   <img src="http://t-blog-images.aijs.top/img/202303141122731.webp" />
2. `markRef` `if (current.ref !== workInProgress.ref) { markRef(workInProgress);}`

3. `调和函数`会给增、删、移动节点设置 fiber.flags

### 为什么 lastEffect 指针不变呢？

<img src="http://t-blog-images.aijs.top/img/202303141150467.webp" />

### 闭包和类的原始性能只有在极端场景下才会有明显的差别，极端情况是什么情况？

### Hook 什么时候与 fiber 关联的？

<img src="http://t-blog-images.aijs.top/img/202303151129459.webp" />

<img src="http://t-blog-images.aijs.top/img/202303141453053.webp" />

### 副作用 hook 如何转化成 fiber.flags 的？

<img src="http://t-blog-images.aijs.top/img/202303141519615.webp" />

### TODO: 补充到自定义 ahooks（暂时不记得是哪篇需要的）

<img src="http://t-blog-images.aijs.top/img/202303150927644.webp" />

### updateWorkInProgressHook 为什么是单步克隆，怎么不直接

每次取一个 hook 用，所以不需要一次克隆所有

<img src="http://t-blog-images.aijs.top/img/202303151143229.webp" />

### mountState,mountReducer -> dispatch -> dispatchAction bind 进行 scheduleUpdateOnFiber

### updateState,updateReducer -> dispatch -> dispatchAction bind 进行 scheduleUpdateOnFiber

<img src="http://t-blog-images.aijs.top/img/202303151336472.webp" />

### queue.pending 是环形链表？ 1. 为什用环形链表？2. 如何找环形链表的入口？

<img src="http://t-blog-images.aijs.top/img/202303151405480.webp" />

<img src="http://t-blog-images.aijs.top/img/202303151431163.webp" />

### pending -> 新状态

updateReducer 调用 basicStateReducer

<img src="http://t-blog-images.aijs.top/img/202303151449905.webp" />

### 优先级如何产生 requestUpdateLane

<img src="http://t-blog-images.aijs.top/img/202303151536389.webp" />

getEventPriority 代码因长，放链接
<a href="https://github.com/facebook/react/blob/cfc1274e3be5a93a4c93f8fb87f2109993afe1dd/packages/react-dom-bindings/src/events/ReactDOMEventListener.js#LL411-L517C2" target="_blank" >getEventPriority 源码</a>

1. `DiscreteEventPriority` 直接触发
2. `ContinuousEventPriority` 可能会继续触发
3. `case 'message':` 是 React 内部调度通过消息传递的调度优先级，会转化成事件优先级？

```js
case 'message': {
      // We might be in the Scheduler callback.
      // Eventually this mechanism will be replaced by a check
      // of the current priority on the native scheduler.
      const schedulerPriority = getCurrentSchedulerPriorityLevel();
      switch (schedulerPriority) {
        case ImmediateSchedulerPriority:
          return DiscreteEventPriority;
        case UserBlockingSchedulerPriority:
          return ContinuousEventPriority;
        case NormalSchedulerPriority:
        case LowSchedulerPriority:
          // TODO: Handle LowSchedulerPriority, somehow. Maybe the same lane as hydration.
          return DefaultEventPriority;
        case IdleSchedulerPriority:
          return IdleEventPriority;
        default:
          return DefaultEventPriority;
      }
}
default:
      return DefaultEventPriority;
```

### 这个是异步的

<img src="http://t-blog-images.aijs.top/img/202303160919293.webp" />

<a href="https://7kms.github.io/react-illustration-series/main/hook-state#异步更新" target="_blank" >异步更新</a>

1. 低优先级之前的高优先级被计算，放置到 baseState
2. 所有高优先级被计算，放置到 memoizedState
3. 第一个地优先级之前的高优先级，被干掉，其他的不变

### Hook 的 state 计算有点复杂: baseQueue -> newBaseState,newState 【未理解】

QUES: <span style="color: red; font-size: 32px">未理解</span>

```js
hook.memoizedState = newState; // 搞不清楚这个newState的计算
hook.baseState = newBaseState; //
hook.baseQueue = newBaseQueueLast;
queue.lastRenderedState = newState; //
```

<img src="http://t-blog-images.aijs.top/img/202303151742009.webp" />

### Hook baseQueue 变化

<img src="https://7kms.github.io/react-illustration-series/static/before-basequeue-combine.aa99bd25.png" />

<div style="height: 4px; width: 100%"></div>

<img src="https://7kms.github.io/react-illustration-series/static/after-basequeue-combine.1f0a9771.png" />

### \$\$typeof

<img src="http://t-blog-images.aijs.top/img/202303161024652.webp" />

<a href="https://oveLReacted.io/zh-hans/why-do-react-elements-have-typeof-property/" target="_blank" >为什么 React 元素有一个\$\$typeof 属性？</a>

动机： 验证一些不安全的可能性

1. React 元素（elements）是设计好的 plain object
2. 服务端没有办法存储 Symbol 类型的值，也无法添加。所以当原本的数据被替换成恶意的对象时，这个对象是不包含\$\$typeof 属性的
3. React 会检测合法的\$\$typeof 属性，不符合条件 React 就不会处理这个元素
4. 不支持 symbol 的浏览器会使用 `0xeac7` (至于为什么选用`0xeac7`这个数字，Dan 给出的答案是，它很像 React)
5. 大意就是 Symbol.for()是可以在像类似 iframe 或者 worker 这种全局环境中都能独立标识一个值。所以$$typeof属性就不会影响到一些可以在全局环境中传递的React Element。或者当界面有多个副本存在时，也能agree到正确的$$typeof。

<a href="https://hanqizheng.github.io/2020/07/26/$$typeof.html" target="_blank" >React 元素的\$\$typeof 属性</a>

验证：\$\$typeof

<a href="https://reactjs.org/docs/react-api.html#isvalidelement" target="_blank" >isValidElement</a>

## reconciliation

### 兴趣与生活的冲突

今天在思考一件非技术的事，之前在学 go 语言，后来一直在忙于“生活”，算是向生活妥协

前些天看到有之前的同事也在朋友圈发 go 语言的代码，又勾起我的兴趣

<img src="http://t-blog-images.aijs.top/img/202303161034838.webp" style="width:800px;max-width:100%" />

### 回归理性

1. go 语言好是好，相比于 java 来讲，“优雅知性”
2. 有多少家公司需要用（当然不谈云原生，没意义，因为很多公司在用 k8s，贡献的人却不多）
3. 除了某些巨头公司、新的创业公司，剩下的占比很大的是 java
4. 即使掌握了 go 语言，去创业公司？去巨头公司？可能性都不是很大，那剩下的就是自娱了，但时间和生活并不允许
5. 这时候就要向生活妥协了，做出让步，除了兴趣外，更重要的是生活

### 妥协 和解

<a href="https://zhidao.baidu.com/question/2147937715158868028.html" target="_blank" >妥协近义词</a>

`reconciliation:` 百度翻译为：`调解;和解;协调;和谐一致`

哎～，这不就扯到 react reconciliation 了吗？(真的没想聊技术)

### react 为什么对算法进行妥协（or 和解 or 做出让步）？

**原因：计算量大，谁受得了**

1. 即使使用最优的算法，该算法的复杂程度仍为 O(n 3 )，其中 n 是树中元素的数量。
2. 如果在 React 中使用该算法，那么展示 1000 个元素则需要 10 亿次的比较。这个开销实在是太过高昂

### 如何妥协？启发式算法

1. 两个不同类型的元素会产生出不同的树；
2. 开发者可以使用 key 属性标识哪些子元素在不同的渲染中可能是不变的

### 小结

😄 拉你回现实：技术源于生活, 服务生活

## vscode 折叠策略

```js
"editor.foldingStrategy": "indentation", // 折叠策略，按照缩紧
```

<a href="https://blog.csdn.net/Maybe_ss/article/details/122577167" target="_blank" >vsCode 代码块无法折叠收起问题&代码块折叠/展开快捷键</a>

### 为什么叫合成事件，如何合成，合成到什么样子？

既然是合成事件

1. 肯定不是一一映射，如果一一映射叫：“映射事件”不是更好
2. 肯定不是仅作兼容，如果兼容叫：“adapt 事件”不是更好

<a href="https://juejin.cn/post/6991645668934680584" target="_blank" >浅谈 React 合成事件</a>

<a href="https://github.com/facebook/react/blob/v17.0.2/packages/react-dom/src/events/DOMPluginEventSystem.js#L89-L93" target="_blank" >DOMPluginEventSystem.js</a>

```js
SimpleEventPlugin.registerEvents();
// 下面4组是合成事件，上面一组是事件一对一的映射
EnterLeaveEventPlugin.registerEvents();
ChangeEventPlugin.registerEvents();
SelectEventPlugin.registerEvents();
BeforeInputEventPlugin.registerEvents();
```

<a href="https://github.com/facebook/react/blob/12adaffef7105e2714f82651ea51936c563fe15c/packages/react-dom/src/events/plugins/BeforeInputEventPlugin.js#LL57-L88C2" target="_blank" >BeforeInputEventPlugin.js</a>

<a href="https://github.com/facebook/react/blob/12adaffef7105e2714f82651ea51936c563fe15c/packages/react-dom/src/events/plugins/ChangeEventPlugin.js#LL33-L44C2" target="_blank" >ChangeEventPlugin.js</a>

<a href="https://github.com/facebook/react/blob/12adaffef7105e2714f82651ea51936c563fe15c/packages/react-dom/src/events/plugins/EnterLeaveEventPlugin.js#LL29-L34C2" target="_blank" >EnterLeaveEventPlugin.js</a>

**SimpleEventPlugin**
<a href="https://github.com/facebook/react/blob/12adaffef7105e2714f82651ea51936c563fe15c/packages/react-dom/src/events/DOMEventProperties.js#LL216-L230C2" target="_blank" >DOMEventProperties.js</a>

### 合成事件具体表现

```js
// BeforeInputEventPlugin.js
function registerEvents() {
  registerTwoPhaseEvent('onBeforeInput', [
    'compositionend',
    'keypress',
    'textInput',
    'paste',
  ]);
  registerTwoPhaseEvent('onCompositionEnd', [
    'compositionend',
    'focusout',
    'keydown',
    'keypress',
    'keyup',
    'mousedown',
  ]);
  registerTwoPhaseEvent('onCompositionStart', [
    'compositionstart',
    'focusout',
    'keydown',
    'keypress',
    'keyup',
    'mousedown',
  ]);
  registerTwoPhaseEvent('onCompositionUpdate', [
    'compositionupdate',
    'focusout',
    'keydown',
    'keypress',
    'keyup',
    'mousedown',
  ]);
}
// ChangeEventPlugin.js
function registerEvents() {
  registerTwoPhaseEvent('onChange', [
    'change',
    'click',
    'focusin',
    'focusout',
    'input',
    'keydown',
    'keyup',
    'selectionchange',
  ]);
}
// EnterLeaveEventPlugin.js
function registerEvents() {
  registerDirectEvent('onMouseEnter', ['mouseout', 'mouseover']);
  registerDirectEvent('onMouseLeave', ['mouseout', 'mouseover']);
  registerDirectEvent('onPointerEnter', ['pointerout', 'pointerover']);
  registerDirectEvent('onPointerLeave', ['pointerout', 'pointerover']);
}
// SelectEventPlugin.js
function registerEvents() {
  registerTwoPhaseEvent('onSelect', [
    'focusout',
    'contextmenu',
    'dragend',
    'focusin',
    'keydown',
    'keyup',
    'mousedown',
    'mouseup',
    'selectionchange',
  ]);
}

// SimpleEventPlugin.js

// registerSimpleEvents ->
import { registerSimpleEvents } from '../DOMEventProperties';
export { registerSimpleEvents as registerEvents, extractEvents };
// DOMEventProperties.js
export function registerSimpleEvents() {
  registerSimplePluginEventsAndSetTheirPriorities(
    discreteEventPairsForSimpleEventPlugin,
    DiscreteEvent /*事件优先级 */,
  );
  registerSimplePluginEventsAndSetTheirPriorities(
    userBlockingPairsForSimpleEventPlugin,
    UserBlockingEvent /*事件优先级 */,
  );
  registerSimplePluginEventsAndSetTheirPriorities(
    continuousPairsForSimpleEventPlugin,
    ContinuousEvent /*事件优先级 */,
  );
  setEventPriorities(otherDiscreteEvents, DiscreteEvent /*事件优先级 */);
}

/*事件优先级： 见上文： getEventPriority */
```

<a href="https://github.com/facebook/react/blob/12adaffef7105e2714f82651ea51936c563fe15c/packages/react-dom/src/events/DOMEventProperties.js#LL147-L178C2" target="_blank" >registerSimplePluginEventsAndSetTheirPriorities</a>

注释说：给转化成二维数组放到 map 里面

```js
/**
 * Turns
 * ['abort', ...]
 *
 * into
 *
 * topLevelEventsToReactNames = new Map([
 *   ['abort', 'onAbort'],
 * ]);
 *
 * and registers them.
 */

function registerSimplePluginEventsAndSetTheirPriorities(
  eventTypes: Array<DOMEventName | string>,
  priority: EventPriority,
): void {
  // 使用二维数组的好处，数据成对出现，不需要用3个数组来处理
  for (let i = 0; i < eventTypes.length; i += 2) {
    const topEvent = ((eventTypes[i]: any): DOMEventName); // 见下图
    const event = ((eventTypes[i + 1]: any): string); // 见下图
    const capitalizedEvent = event[0].toUpperCase() + event.slice(1);
    const reactName = 'on' + capitalizedEvent; // react的事件名
    eventPriorities.set(topEvent, priority); // 设置事件优先级
    topLevelEventsToReactNames.set(topEvent, reactName);
    registerTwoPhaseEvent(reactName, [topEvent]);
  }
}
```

<img src="http://t-blog-images.aijs.top/img/202303161412805.webp" />

### 这个 nativeEvent 怎么搞进来？没发现呢

bind 返回的 listener 被调用时候传进来的 `nativeEvent`?

<img src="http://t-blog-images.aijs.top/img/202303161448135.webp" />

<img src="http://t-blog-images.aijs.top/img/202303161539967.webp" />

### 合成事件怎么晓得我操作哪个元素，进行回调？

如果是 A、B 两个按钮：在使用的时候，点击 A 按钮，事件到达 根 DOM 容器，react 如何识别，使用调用了 A 按钮的 onClick,而不是 B 的 onClick

<!-- 事件代理：不需要考虑谁触发的，回调就在那 -->

1. AB 平级，假设也传递了其他数据的情况，怎么晓得是 A 还是 B 的回调
2. A 内，B 外 嵌套，都会触发

<img src="http://t-blog-images.aijs.top/img/202303161918293.webp" />
