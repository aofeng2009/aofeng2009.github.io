---
layout: post
title: 用Redux写ReactNative
tags: [technology]
description: >
  本文简单的介绍了Redux的使用，转载请注明出处[链接](http://example.net/)
---

## 目录 ##
> **1.为什么要用Redux**  
> **2.简单介绍Redux的使用**  
> **3.Redux使用过程中发现的问题**  

## 1.为什么要用Redux  

### 1.ReactNative

　　<font size='3'>笔者目前做的项目是一个电商项目，所以活动促销会比较频繁，为了加快项目的迭代速度，满足产品快速上线的需求，从今年起项目开始了ReactNative（以下简称RN）的使用，确实RN在使用过程中提高了产品的更新节奏，配合离线包的分发几乎可以实时的完成新功能的上线。  
之前一直不想用，因为有人说没有native流畅，有些native功能可能支持的不健全，有学习成本，诸如此等等吧。相信很多人都有这样的思考过程，但是当我们开发下定决心去尝试之后，忽然觉的这么好的框架大家为什么不用它进行开发呢？可以避开苹果商店的审核（不同于jspatch），又可以快速上线。至于大家担心的问题，比如流畅性，RN是通过JavaScriptCore引擎渲染native原生控件 <a href="https://facebook.github.io/react-native/docs/javascript-environment.html">地址</a>而非webview的形式加载网页；同时随着设备的不断更新，流畅性已经不再是瓶颈。至于有些功能支持的不健全，比如scrollview各种事件控件响应，其实是可以通过让native去实现的方式，这里就不细展开了。有兴趣的可以看下requireNativeComponent的使用，学习成本的问题，现在都在讨论大前端，对于客户端开发者而言多学一点前端语言我觉的不是坏事 :) </font>
### 2.问题出现

　　<font size='3'>随着第一版的RN项目上线，以及之后的产品迭代，RN真的让人有了眼前一亮的感觉。从之前仅支持首页在用RN，后来大家觉得可以再进一步，向更多的页面扩展。但是在开发的过程中却遇到了新的问题。分散的页面组件，没有好的数据组织，组件之前的相互依赖调用，缺乏集中的数据管理等等，使得代码耦合严重，我们知道RN开发最重要的是对组件state的管理，操作它来完成界面的绘制和功能的使用，虽然单一界面的state管理起来还比较容易，但是如果组件内状态值很多，而状态值又都关联着界面那就麻烦了，操作状态就要格外小心；还有如果组件的状态需要共享给其他组件，或者组件需要操作其他组件的状态等等，这就给开发带来很大的麻烦，各种传参，共享存储可能都要用上。</font>
### 3.主角登场

　　<font size='3'>Redux就是为了解决这些问题产生的，它的设计就是用来提供可预测化的状态管理。它的设计更像是一种管理哲学，像封建统治，数据完全控制在皇帝（Store）手上，皇帝和大臣们议事制定国策（reducer），皇帝的子民各种行为（action）都要被国策负责管理，通过不断的改进优化政策，使国家面貌焕然一新。</font>
## 2.简单介绍Redux的使用
###	1.环境搭建

　　<font size='3'>本文假设你已经有了一个RN的项目，首先需要在package.json中引入Redux的开发包，redux，react-redux，也可以通过命令行的方式引入。关于这两个包的详细介绍及使用参考地址： https://www.npmjs.com/ 搜索即可。笔者就不再赘述。</font>
###	2.使用介绍

　　<font size='3'>有了以上两个开发包就可以进行Redux的开发了。回顾之前我们讲述的，状态数据掌握在Store中，各个容器组件必须要设法使用Store，Redux提供了Provider和connect机制来分享自己的Store。前者提供了一套机制，作为根组件可以把Store传递给所有的子组件。</font>
```javascript
render() {
  return (
    <Provider store={store}>
      <Root />
    </Provider>
  )
}
```
<font size='3'>只提供了Provider还不足以完成数据的分享，还需要另外一个方法connect.顾名思义将Store和React组件进行连接。</font>
> connect(mapStateToProps, mapDispatchToProps, mergeProps, options)  

**mapStateToProps**:<font size='3'>该函数参数用来指定Store发生变化时候，哪些参数是Store通知组件需要关心的。返回值通常是个纯对象，里面包含了组件关心的参数。这个对象会与组件的 props 合并，组件render之前都会执行该函数，从而获取到最新的状态。</font>
```javascript
function mapStateToProps(store){
    return {
        data: store.data
    }
}
```
**mapDispatchToProps**:<font size='3'>用来告诉组件需要如何触发dispatch。它可以是个函数也可以是一个对象。

###### 作为函数
<font size='3'>函数参数是dispatch和ownProps（组件自身的Props），返回值是ActionCreator，或者绑定action creator.</font>
```javascript
function mapDispatchToProps(dispatch, ownProps) {
  return {
    onDo: () => {
      dispatch({
        type: 'DO_SOMETHING'
      });
    }
  };
}
or：
function mapDispatchToProps(dispatch, ownProps) {
  return bindActionCreators(actions, dispatch);
}
```
###### 作为对象
<font size='3'>与Redux store绑定在一起，对象内的每个函数做为Redux action creator，与组件的props合并。</font>
```javascript
const mapDispatchToProps = {
  onClick: (text) => {
    type: 'SET_TEXT',
    text: text
  };
}
```
**mergeProps**:<font size='3'>顾名思义是用来整合前边两个参数的，可以在函数内部进行定制筛选组件需要的状态。</font>  
**options**：<font size='3'>笔者没有使用到，具体可以参考官方文档。</font>  
### 3.例子

 　　<font size='3'>接下来以一个简单的例子介绍下Store、Action、reducer是怎么协同工作的。本例实现了一个弹窗的功能，点击屏幕上的弹窗Text，显示一个Modal，先看工作流图：</font>
  >  ![692x227](/images/20170715redux-structure.png "redux structure image"){:.lead}  

**（1）入口：**  
	   <font size='3'>程序入口引入Redux的Provider，并且进行初始化。</font>
```javascript     
import React, { Component } from 'react';
import { Provider } from 'react-redux';
import {store} from './store';
import Index from './index';

export default class reduxDemo extends Component{
  render(){
    return (
      <Provider store={store}>
        <Index />
      </Provider>
    )
  }
}
```
**（2）根组件：**  
　　<font size='3'>connect方法将store中的bShowmodal共享给Index组件。</font>
```javascript  
import React, { Component } from 'react';
import { Modal, View, Text, Dimensions} from 'react-native';
import { connect } from 'react-redux';
import { showModalAction } from './action'

var screenWidth = Dimensions.get('window').width;
var screenHeight = Dimensions.get('window').height;

class Index extends Component {
  render() {
    return (
      <View style={{flex:1, justifyContent:'center', alignItems: 'center'}}>
        <Text style={{alignItems:'center'}} onPress={()=>{this.onTextClick()}}>弹窗</Text>
        <Modal visible={this.props.bShowModal}>
          <View style={{left: (screenWidth - 300)/2, top: (screenHeight - 300)/2, width: 300, height: 300, justifyContent:'center',alignItems: 'center',backgroundColor: '#929292'}}>
            <Text>Hello Redux</Text>
          </View>
        </Modal>
       </View>
    )
  }

  onTextClick() {
  	this.props.dispatch(showModalAction);
  }
}

function mapStateToProps(store){
  return {
    bShowModal: store.bShowModal
  }
}
export default connect(mapStateToProps)(Index);
```
**（3）action：**  
　　<font size='3'>2中onTextClick 有个showModalAction的action，定义如下：很简单定义一个类型。以便在reducer中进行匹配。</font>
```javascript
export const showModalAction = {
  type: 'SHOW_MODAL'
}
```
**（4）reducer：**  
　　<font size='3'>此处为处理状态的地方，通过对不同类型的action进行识别和与之数据改造，来完成store中数据的改变，从而触发React组件的状态改变，进行render绘制。设计Reducer需要对state进行初始化，方法名可以根据逻辑自由定义，switch一定要做default情况的处理。</font>
```javascript
const initialState = {
  bShowModal:false
};

export default function show(state=initialState,action) {
  switch(action.type) {
    case 'SHOW_MODAL':
    return {
      ...state,
      bShowModal:true
    }
    default:
    return {
      ...state,
      bShowModal:false
    }
  }
}
```
　　<font size='3'>最后通过打印下函数的调用顺序，至此一个非常简单的Redux Demo就完事了，是不是很简单。</font>  
  >  ![748x196](/images/20170715redux-excute-order.png "redux structure image"){:.lead}  

## 3.Redux 高级用法
**1.** <font size='3'>为了提高Redux使用的灵活性，Redux作者在架构中提供了一种扩展能力Middleware，Middleware是Dispatch action和action到达reducer之间的第三方扩展。有了这个扩展开发者可以自定义一些操作，比如异步支持，打印等功能。</font>  
　　<font size='3'>简单介绍一个Middleware，[redux-thunk]。当组件dispatch一个action的时候reducer立即处理，然后返回新的状态，组件重新绘制。这是一套同步机制，可是有些时候是需要异步处理，比如延时执行、异步网络请求数据的发送和接收就不是同步的。</font>
```javascript
export default function thunkMiddleware({ dispatch, getState }) {
  	return next => action =>
      typeof action === 'function' ?
      action(dispatch, getState) :
      next(action);
}
```
　 <font size='3'>以上是redux-trunk的实现细节，可以看到dispatch的时候除了可以发送action，还可以dispatch函数，为函数传递了dispatch的能力。这样我们在实现函数的时候就可以自由控制dispatch的时机，从而实现插桩和异步。</font>  
**2.** <font size='3'>当工程随着业务的复杂，越来越大的时候reducer中的判断情况就会越来越多。Redux提供了一个方法combineReducers，它支持组合多个reducer。</font>
```javascript
combineReducers({
  Reducer1,
  Reducer2  
  ……
});
```
　　<font size='3'>这样项目的结构更加清晰，其他的高级用法以后再分析。研究的有些浅薄，先分享到这，欢迎随时交流。</font>
