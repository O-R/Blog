# dd

React是一个UI层面的库，它采用虚拟DOM技术减少Javascript与真正DOM的交互，提升了前端性能；采用单向数据流机制，父组件通过`props`将数据传递给子组件，这样让数据流向一目了然。一旦组件的`props`或则`state`发生改变，组件及其子组件都将重新re-render和vdom-diff，从而完成数据的流向交互。但是这种机制在某些情况下比如说数据量较大的情况下可能会存在一些性能问题。下面就来分析react的性能瓶颈，并用结合着`react-addons-perf`工具来说明react组件拆分的重要性。

## react性能瓶颈

要了解react的性能瓶颈，就需要知道react的渲染流程。它的渲染可以分为两个阶段：

* `初始组件化` 该阶段会执行组件及其所有子组件的`render`方法，从而生成第一版的虚拟dom。
* `组件更新渲染`。 组件的`props`或者`state`任意发生改变就会触发组件的更新渲染。默认情况下其也会执行该组件及其所有子组件的render方法获取新的虚拟dom。 我们说的性能瓶颈指的是组件更新阶段的情况。

### react组件更新流程

通过上面分析可以知道组件更新具体过程如下：

* 执行该组件及其所有子组件的render方法获取更新后的虚拟DOM，即`re-render`，即使子组件无需更新。

* 然后对新旧两份虚拟DOM进行diff来进行组件的更新 在这个过程中，可以通过组件的`shouldComponentUpdate`方法返回值来决定是否需要re-render。 react的整个更新渲染流程可以借用一张图来加以说明：

![](https://gw.alicdn.com/tps/TB1t1yUNpXXXXcmXFXXXXXXXXXX-620-1034.png_500x500s50.jpg)

默认地，组件的`shouldComponentUpdate`返回true，即React默认会调用所有组件的render方法来生成新的虚拟DOM， 然后跟旧的虚拟DOM比较来决定组件最终是否需要更新。

### react性能瓶颈

借图说话，例如下图是一个组件结构tree，当我们要更新某个子组件的时候，如下图的绿色组件（从根组件传递下来应用在绿色组件上的数据发生改变）： ![](https://segmentfault.com/img/bVGUAL?w=850&h=316)
理想情况下，我们只希望关键路径上的组件进行更新，如下图: ![](https://segmentfault.com/img/bVGUAR?w=843&h=309)
但是，实际效果却是每个组件都完成`re-render`和`virtual-DOM diff`过程，虽然组件没有变更，这明显是一种浪费。如下图黄色部分表示浪费的re-render和virtual-DOM diff。 ![](https://segmentfault.com/img/bVGUBc?w=846&h=309)

根据上面的分析，react的性能瓶颈主要表现在：

> 对于`props`和`state`没有变化的组件，react也要重新生成虚拟DOM及虚拟DOM的diff。

### 用`shouldComponentUpdate`来进行性能优化

 针对react的性能瓶颈，我们可以通过react提供的`shouldComponentUpdate`方法来做点优化的事，可以有选择的进行组件更新，从而提升react的性能，具体如下:

 > `shouldComponentUpdate`需要判断当前属性和状态是否和上一次的相同，如果相同则不需要执行后续生成虚拟DOM及其diff的过程，否则需要更新。

具体可以这么显示实现：

```javascript

   shouldComponentUpdate(nextProps, nextState) {
    return !isEqual(nextProps, this.props) || !isEqual(nextState, this.state)
  }

```

  其中，**isEqual**方法为判断两个对象是否相等(指的是其对象内容相等，而不是全等)。 通过显示覆盖`shouldComponentUpdate`方法来判断组件是否需要更新从而避免无用的更新，但是若为每个组件添加该方法会显得繁琐，好在react提供了官方的解决方案，具体做法：

> 方案对组件的`shouldComponentUpdate`进行了封装处理，实现对组件的当前属性和状态与上一次的进行浅对比，从而决定组件是否需要更新。

react在发展的不同阶段提供两套官方方案：

* `PureRenderMin` 一种是基于ES5的`React.createClass`创建的组件，配合该形式下的`mixins`方式来组合PureRenderMixin提供的`shouldComponentUpdate`方法。当然用ES6创建的组件也能使用该方案。

```javascript

import PureRenderMixin from 'react-addons-pure-render-mixin';

class Example extends React.Component {
  constructor(props) {
    super(props);
    this.shouldComponentUpdate = PureRenderMixin.shouldComponentUpdate.bind(this);
  }

```

* `PureComponent`

  该方案是在[React 15.3.0](https://github.com/facebook/react/releases/tag/v15.3.0)版本发布的针对`ES6`而增加的一个组件基类：`React.PureComponent`。这明显对ES6方式创建的组件更加友好。

```javascript

  import React, { PureComponent } from 'react'
  class Example extends PureComponent {
      render() {
          // ...
      }
  }

```

需要指出的是，不管是`PureRenderMin`还是`PureComponent`，他们内部的`shouldComponentUpdate`方法都是浅比较(`shallowCompare`)`props`和`state`对象的，即只比较对象的**第一层**的属性及其值是不是相同。例如下面state对象变更为如下值：

```javascript

state = { value: { foo: 'bar' } }

```

因为state的value被赋予另一个对象，使`nextState.value`与`this.props.value`始终不等，导致浅比较通过不了。在实际项目中，这种嵌套的对象结果是很常见的，如果使用`PureRenderMin`或者`PureComponent`方式时起不到应有的效果。 虽然可以通过深比较方式来判断，但是深比较类似于深拷贝，递归操作，性能开销比较大。

为此，可以对组件尽可能的拆分，使组件的`props`和`state`对象数据达到扁平化，结合着使用`PureRenderMin`或者`PureComponent`来判断组件是否更新，可以更好地提升react的性能，不需要开发人员过多关心。

## 组件拆分 

`组件拆分`，在react中就是将组件尽可能的细分，便于复用和优化。拆分的具体原则：

* `尽量使拆分后的组件更容易判断是否更新` 这不太好理解，举个例子吧：假设我们定义一个父组件，其包含了5000个子组件。有一个输入框输入操作，每次输入一个数字，对应的那个子组件背景色变红。

```javascript

{this.state.inputText}
{el.name}
}

```

本例中，输入框组件和列表子组件有着明显的不同，一个是动态的，输入值比较频繁；一个是相对静态的，不管input怎么输入它就是5000项。输入框每输入一个数字都会导致所有组件re-render，这样就会造成列表子组件不必要的更新。 可以看出，上面列表组件的更新不容易被取消，因为输入组件和列表子组件的状态都置于父组件state中，二者共享；react不可能用`shouldComponentUpdate`的返回值来使组件一部分组件更新，另一部分不更新。 只有把他们拆分为不同的组件，每个组件只关心对应的`props`。拆分的列表组件只关心自己那部分属性，其他组件导致父组件的更新在列表组件中可以通过判断自己关心的属性值情况来决定是否更新，这样才能更好地进行组件优化。

* `尽量使拆分组件的props和state数据扁平化` 这主要是从组件优化的角度考虑的，如果组件不需过多关注性能，可以忽略。 拆分组件之所以扁平化，是因为React提供的优化方案`PureRenderMin`或者`PureComponent`是浅比较组件的`props`和`state`来决定是否更新组件。 上面的列表组件中，**this.state.items**存放的是对象数组，为了更好的判断每项列表是否需要更新，可以将每个`li`列表项单独拆分为一个列表项组件，每个列表项相关的props就是items数组中的每个对象，这种扁平化数据很容易判断是否数据发生变化。

## 组件拆分的一个例子

为了这篇文章专门写了一个有关添加展示Todo列表的[事例库](https://github.com/wonyun/componentDivison)。克隆代码到本地可以在本地运行效果。 该事例库是一个有着5000项的Todo列表，可以删除和新增Todo项。该事例展示了组件拆分前和拆分后的体验对比情况，可以发现有性能明显的提升。 下面我们结合react的性能检测工具`react-addons-perf`来说明组件拆分的情况。 拆分前的组件`TodosBeforeDivision`的render部分内容如下：

```javascript

{this.state.value}
 add todo {
     this.state.items.map(
         el=>{ return ( )
     })
     }

```

组件拆分前，输入框输入字符、增加todo或者删除todo项可以看出有明显的卡顿现象，如下图所示： ![](https://segmentfault.com/img/bVNapx?w=640&h=387)

为了弄清楚是什么原因导致卡顿现象，我们使用chrome的devTool来定位，具体的做法是使用最新版的chrome浏览器的`Performance`选项来完成。先点击该选项中的`record`按钮开始记录，这时我们在组件输入框输入一个字符，然后点击`stop`来停止记录，我们会看到组件从输入开始到结束这段时间内的一个性能profile。 ![](https://segmentfault.com/img/bVM9Dy)

从图可以看出我们在输入单个字符时，输入框的input事件逻辑几乎占据整个响应时间，具体的处理逻辑主要是react层面的`batchedUpdates`方法批量更新列表组件，而不是用户自定义的逻辑。 那么，批量更新为啥占据这么多时间呢，为了搞清楚原因，我们借助基于`react-addons-perf`的chrome插件[`chrome-react-perf`](https://github.com/crysislinux/chrome-react-perf)，它以chrome插件的形式输出分析的结果。 使用该插件需要注意一点的是：

> `chrome-react-perf`插件的使用需要在项目中引入`react-addons-perf`模块，并必须将其对象挂载到`window`全局对象的`Perf`属性上，否则不能使用。

在devTool工具中选择`Perf`选项试图，点击`start`按钮后其变成`stop`按钮，在组件输入框中输入一个字符，然后点击`Perf`试图中的`stop`按钮，就会得出对应的性能试图。 ![](https://segmentfault.com/img/bVM9S0)

上图提供的4个视图中，`Print Wasted`对分析性能最有帮组，**它表示组件没有变化但是参与了更新过程，即浪费了re-render和vdom-diff这一过程，是毫无意义的过程**。从图可以看出：`TodosBeforeDivision`和`TodoItem`组件分别浪费了167.88ms、144.47ms，这完全可以通过拆分组件避免的开销，这是react性能优化重点。

为此我们需要对`TodosBeforeDivision`组件进行拆分，拆分为一个带有input和button的动态组件`AddTodoForm`和一个相对静态的组件`TodoList`。二者分别继承`React.PureComponent`可以避免不必要的组件更新。

```javascript

export default class AddTodoForm extends React.PureComponent{ ... render(){ return (

{this.state.value}
  add todo
) } ... }

```

其中`TodoList`组件还需要为每项Todo任务拆分为一个组件`TodoItem`，这样每个`TodoItem`组件的`props`对象为扁平化的数据，可以充分利用`React.PureComponent`来进行对象浅比较从而更好地决定组件是否要更新，这样避免了新增或者删除一个`TodoItem`项时，其他`TodoItem`组件不必更新。

```javascript

export default class TodoList extends React.PureComponent {
  ...
  render() {
    return (
      { this.props.initailItems.map(el => { return }) }
    )
  }
   ...
}

export default class TodoItem extends React.PureComponent {
  ...
  render() {
    return (
       {this.props.item.text } 
       { this.props.tags.map((tag) => { return { tag }; }) }
  ) }
  ...
}

```

这样拆分后的组件，在用上面的性能检测工具查看对应的效果： ![图片描述](https://segmentfault.com/img/bVM95R) ![图片描述](https://segmentfault.com/img/bVM95T)

从上面的截图可以看出，拆分后的组件性能有了上百倍的提升，虽然其中还包含一些其他优化，例如不将function在组件属性位置绑定this以及常量对象props缓存起来等避免每次re-render时重新生成新的function和新的对象props。

总的来说，对react组件进行拆分对react性能的提升是非常重要的，这也是react性能优化的一个方向。

## 参考文献

* [A DEEP DIVE INTO REACT PERF DEBUGGING](http://benchling.engineering/deep-dive-react-perf-debugging/)

* [React PureComponent 源码解析](https://segmentfault.com/a/1190000006741060)

* [React性能优化总结](https://segmentfault.com/a/1190000007811296)

* [如何利用工具提高 React 页面渲染性能之 Perf](https://yq.aliyun.com/articles/66958?utm_campaign=wenzhang&utm_medium=article&utm_source=QQ-qun&utm_content=m_8887)

* [react组件性能调优](http://younth.github.io/2017/04/07/react-perf/)