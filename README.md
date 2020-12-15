# 王文茏｜Part 4｜模块一

## 简答题

### 一: 请简述 React 16 版本中初始渲染的流程

1. babel将jsx代码转换为React createElement代码
2. render阶段
   1. render函数内调用createElement, 获得root节点对应的ReactElement及其子节点
   2. 初始化FiberRoot和RootFiber, 将其互相连接
   3. 获取rootFiber child对象, 创建同步update任务并加入队列
   4. 构建WorkInProgress Fiber Tree
      1. 自上而下构建fiber对象, 为父节点和第一个子节点创建连return和child连接, 为同级节点间创建sibling连接
      2. 自下而上构建fiber对象, 为每个节点创建真实DOM对象, 添加到其stateNode中. append到其父节点上, 构建和收集effect链表结构
3. commit阶段
   1. 遍历effects, 为类组件调用`getSnapShotBeforeUpdate`生命周期函数
   2. 遍历effects, 根据effectTag执行DOM操作
   3. 遍历effects, 调用对应回调函数:
      1. 类组件生命周期函数`componentDidUpdate, componentDidMount`
      2. 函数组件的钩子函数
      3. render方法的callback参数

### 二: 为什么 React 16 版本中 render 阶段放弃了使用递归

js原生的函数递归不可被打断, 而且js是单线程, 所以render阶段一旦开始就不能被打断, 直到render结束.

这样当渲染复杂页面时, 比如较深的virtual DOM tree, render长期占用js主线程, 无法执行其他行为, 如响应用户操作, 渲染动画等

造成页面卡顿, 影响用户体验

### 三: 请简述 React 16 版本中 commit 阶段的三个子阶段分别做了什么事情

1. 为类组件调用`getSnapShotBeforeUpdate`生命周期函数
2. 根据effectTag执行DOM操作
3. 调用对应回调函数:
   1. 类组件生命周期函数`componentDidUpdate, componentDidMount`
   2. 函数组件的钩子函数
   3. render方法的callback参数

### 四: 请简述 workInProgress Fiber 树存在的意义是什么

1. 作为双缓存, 在内存中预先构建出新的Fiber tree, 完成后直接替换current Fiber tree, 这样可以快速更新DOM, 将新视图一次性挂载到页面上, 提高效率减少卡顿
2. 和current Fiber tree互为备份, 通过互相对比可以得到节点是新增, 更新或是删除, 便于更新视图