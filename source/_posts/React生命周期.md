---
title: React生命周期
date: 2022-5-4 22:05:19
tags:
categories: React
---

# react 的生命周期大全

react 的生命周期除了能够在一些指定的时间触发一些函数以外 还具备一些其他能力。

# 挂载阶段

## constructor

类组件本就是一个类的写法，那么 constructor 就是一个类的实例化过程当中最先执行的函数这就是一个生命周期

## static getDerivedStateFromProps

挂载的时候会执行一次

## render

类组就是通过 render 函数返回内容来渲染，所以 render 在 return 之前在第一次挂载的时候就类似于 vue 的 beforeMount

## componentDidMount

组件完成挂载的时候执行

这个是发送请求或者是开始设置定时器或者监听的最好时机

# 更新阶段（commit 阶段 提交阶段）

## static getDerivedStateFromProps(props,state)

接受的属性变化 或者是 setState 之后 或者是 forceUpdat 的时候都会执行一次

```

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/react/16.14.0/umd/react.development.js" type="application/javascript"></script>
    <script src="https://lf26-cdn-tos.bytecdntp.com/cdn/expire-1-M/react-dom/16.14.0/umd/react-dom.development.js" type="application/javascript"></script>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/babel-standalone/7.0.0-alpha.20/babel.min.js" type="application/javascript"></script>
</head>
<body>
    <div id="app"></div>
    <script type="text/babel">
        class CiaoPiao extends React.Component {
            state = {
                res: '下次加油!!!',
                luckyNumber: 7
            }
            static getDerivedStateFromProps(props,state){
                if(props.nums.includes(state.luckyNumber)) return { res:'suprise!!! 中奖拉' }
                return {}
            }
            render(){
                return <div> 中奖的结果: { this.state.res } </div>
            }
        }

        class Cp2  extends React.Component {
            state = {
                tips:'这点量还行',
                luckynumber: 7,
                crtxt: '下次加油 谢谢惠顾'
            }
            // 这个生命周期是 静态方法 getDerivedStateFromProps发生在  shouldComponentUpdate
            // 也是有新的属性或者是状态或者是强制更新会执行
            static getDerivedStateFromProps(props,state){
                console.log(' cp2 getDrivedStateFromProps 执行' )
                // 它可以临时的去修改 state ，在最终执行渲染之前可以再次的修改state的值
                // 这里返回的值就会覆盖 现在的state
                // 比如下面的代码 就是判断接受到的a属性  如果超过了5那么久返回一个对象这个对象 会跟当前的state合并
                // props.a 如果大于了5. 那么  返回的值当中有一个tips属性  这个tips属性就会覆盖上面的22行当中的原来的state当中的tips
                if(props.a > 5) return { tips:'不行太多了 超过5个了' }
                return {}
            }
            shouldComponentUpdate(){
                console.log(' cp2  shouldComponentUpdate 执行')
                return true;
            }
            render(){
                console.log(' cp2 render 执行' )
                return (
                    <div> 我是Cp2组件 { this.props.a }   { this.state.tips }  </div>
                )
            }
        }
        class Cp1 extends React.Component {
            constructor(){
                super();
                this.state = {
                    ct:1
                }
            }

            render(){
                return (
                    <div>
                         <Cp2 a={ this.state.ct }  number={ [] }></Cp2>
                         <button onClick={ ()=> this.setState({ ct: this.state.ct + 1 })  }>
                             修改ct状态的值 递增1
                         </button>
                         <hr / >
                         <div> 彩票的例子 </div>
                         <CiaoPiao  nums={ [ 4,5,6,1,3,7]}></CiaoPiao>
                    </div>
                )
             }
        }
        ReactDOM.render( <Cp1></Cp1>, document.getElementById("app") )
    </script>
    <style>
        .kfc{
            color:red
        }
    </style>

</body>
</html>
```

## shouldComponentUpdate( props , state)

state 这个参数是我们 setState 之后 最新的 state 的值

发生在更新之前 并且可以控制是否允许组件更新(只能控制是否更新不能控制状态是否变化)

通过返回的布尔值状态来控制是否渲染

```

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/react/16.14.0/umd/react.development.js" type="application/javascript"></script>
    <script src="https://lf26-cdn-tos.bytecdntp.com/cdn/expire-1-M/react-dom/16.14.0/umd/react-dom.development.js" type="application/javascript"></script>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/babel-standalone/7.0.0-alpha.20/babel.min.js" type="application/javascript"></script>
</head>
<body>
    <div id="app"></div>
    <script type="text/babel">
        class Cp2  extends React.Component {
            //  component 组件 Will 即将  Unmount 卸载 => 组件即将卸载（vue2 当中的 beforeDestroy / vue3当中的 beforeUnmount)
            componentWillUnmount(){
                console.log('cp2组件的 componentWillUnmount 执行')
            }
            render(){
                return (
                    <div> 我是Cp2组件  </div>
                )
            }
        }
        class Cp1 extends React.Component {
            constructor(){
                super();
                console.log('constructor 执行')
                // beforeCreate 对应vue的生命周期
                this.state  = {
                    ct: 1,
                    showCp2: true
                }
                // created 对应vue的生命周期
            }
            // component 组件 did 完成  mount 挂载 => 挂载完成之后  ( vue 当中的mounted)
            componentDidMount(){
                //console.log('componentDidMount 执行')
            }
            // component 组件 did 完成  Update 更新 => 组件更新完成之后  （vue 当中的 updated)
            componentDidUpdate(){
               console.log('componentDidUpdate 执行')
            }
            // component 组件 will 即将  Update 更新 => 组件更新之前执行 ( vue 当中的beforeUpdate )
            //  发生在 SCU 之后  在render之前
            componentWillUpdate(){
                console.log('componentWillUpdate  即将更新 执行')
            }
            // should是否允许 Component 组件 Update 更新=> 是否允许组件更新
            // SCU
            shouldComponentUpdate(props,state){
                // shouldComponentUpdate 第一个 ....
                // 第二个参数是修改了之后的 state
                console.log('state in the scu',state)

                // react通过shouldComponentUpdate 让我们具备控制是否更新视图的能力
                // 因为有些时候有些值变化并不会直接体现在视图上面 这个时候如果不控制那么 react的组件也会走完一整个更新渲染的流程非常的浪费性能
                if(state.ct < 5) return false; // 不更新
                return true; // 更新
            }

            UNSAFE_componentWillMount(){
                console.log('componentWillMount  执行')
            }
            render(){
                // 挂载阶段当中 类似于 beforeMount 对应vue的生命周期
                // 更新阶段当中  类似于 beforeUpdate(但这个并不完全一样)
               console.log('render执行')
                return (
                    <div>
                        我是cp1
                        <div> ct: { this.state.ct } </div>
                        <button onClick={ ()=> this.setState({ ct: this.state.ct+1 })  }>让ct递增</button>
                        <hr />
                       { this.state.showCp2 && <Cp2></Cp2> }
                       <button onClick={()=>{
                            this.setState({
                                showCp2: !this.state.showCp2
                            })
                       }}>
                             切换渲染 cp2组件
                       </button>
                    </div>
                )
             }
        }

        ReactDOM.render( <Cp1></Cp1>, document.getElementById("app") )
    </script>
    <style>
        .kfc{
            color:red
        }
    </style>

</body>
</html>
```

## render 先执行

## getSnapshotBeforeUpdate

发生在 render 之后 componentDidUpdate 之前执行 而且是在页面发生更新渲染到 dom 树之前

api 的语义上是说 在实际更新之前 你可以拍个照 留个已记录 传给 componentDidUpdate

```

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/react/16.14.0/umd/react.development.js" type="application/javascript"></script>
    <script src="https://lf26-cdn-tos.bytecdntp.com/cdn/expire-1-M/react-dom/16.14.0/umd/react-dom.development.js" type="application/javascript"></script>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/babel-standalone/7.0.0-alpha.20/babel.min.js" type="application/javascript"></script>
</head>
<body>
    <div id="app"></div>
    <script type="text/babel">

        class Cp1 extends React.Component {
            constructor(){
                super();
                this.state = {
                    ct:1,
                    arr: []
                }
            }
            componentDidUpdate(prevProps,prevState,snapshot){
                // 关键是有记得  componentDidUpdate 会得到哪些参数？？
                    console.log('componentDidUpdate 更新完成之后执行',snapshot);
                    document.querySelector('.box').style.top = snapshot + 23 + 'px';
            }
            // 在 render 之后   componentDidUpdate 之前执行 而且是在页面发生更新渲染到dom树之前
            // get  获取  snapshot 快照/照片 beforeUpdate 更新之前
            // api的语义上是说 在实际更新之前 你可以拍个照 留个已记录 传给 componentDidUpdate
            getSnapshotBeforeUpdate(){
                console.log('getSnapshotBeforeUpdate 执行')
                // 返回的值就是传递给 componentDidUpdate 的第三个参数
                // getSnapshotBeforeUpdate  作用不是作为生命周期使用
                // 而且给我们一种在dom更新前获取一些实际dom更新之前的某些信息丢给  componentDidUpdate 使用

                // 获取当前的mylist的高度
                return document.querySelector('.mylist').clientHeight
            }
            add = ()=>{
                this.state.arr.push( Date.now() );
                this.setState({
                    arr: [ ...this.state.arr]
                })
            }
            render(){
                console.log('render 执行')
                return (
                    <div>
                         <div>  { this.state.ct}     </div>
                         <button onClick={ ()=>{
                            this.setState({
                                ct: this.state.ct+1
                            })
                          } }> 让ct递增</button>
                          <div> arr的长度 </div>
                          <div class="mylist">
                                { this.state.arr.map( item => <div>{item}</div>  ) }
                                <div class="box"></div>
                           </div>
                          <button onClick={this.add}> 添加内容到arr当中z </button>
                    </div>
                )
             }
        }
        ReactDOM.render( <Cp1></Cp1>, document.getElementById("app") )
    </script>
    <style>
        .mylist{
            position: relative;
            width: 200px;
            border: 1px solid #000;
        }
        .box{
            width: 50px;
            height: 50px;
            border: 1px solid red;
            position: absolute;
            right:0;
            bottom:0;
            transform: translateX(100%);
            transition: top 300ms linear 200ms;

        }
    </style>
    <style>
        .kfc{
            color:red
        }
    </style>

</body>
</html>
```

## componentDidUpdate(preProps,preState,snaphot)

在组件更新完整之后执行，重点关注三个参数分别是什么

切记不要在 componentDidUpdate 随意的使用 setState

```

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/react/16.14.0/umd/react.development.js" type="application/javascript"></script>
    <script src="https://lf26-cdn-tos.bytecdntp.com/cdn/expire-1-M/react-dom/16.14.0/umd/react-dom.development.js" type="application/javascript"></script>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/babel-standalone/7.0.0-alpha.20/babel.min.js" type="application/javascript"></script>
</head>
<body>
    <div id="app"></div>
    <script type="text/babel">

        class Cp1 extends React.Component {
            constructor(){
                super();
                this.state = {
                    ct:1,
                    price: 20,
                    a: 5,
                    b: 6,
                    total : 20
                }
            }
            componentDidUpdate(prevProps,prevState){
                // componentDidUpdate函数会得到三个回调参数
                // 第一个是变化之前的 props
                // 第二个是变化之前的 state
                // 这个是之前的？那么当前的呢？？？？？   this.props  this.state
                console.log('更新完成之后执行');
                // 我希望是在更新完成之后 就主动的去修改total的值
                // setState 是不是修改状态？ yes => 那么会不会引起视图的更新？ yes => 会不会执行 componentDidUpdate?
                // componentDidUpdate 会不会让里面的setState再执行？？？ 死循环了没？
                // 官方的说辞 尽量不要在 componentDidUpdate里面调用setState
                // 错误的做法
                this.setState({
                    total: this.state.ct * this.state.price
                })


            }


            render(){
                return (
                    <div>
                         <div>
                             数量 { this.state.ct}   *   价格 { this.state.price } =
                             { this.state.total }
                         </div>
                         <button onClick={ ()=>{
                            this.setState({
                                ct: this.state.ct+1
                            })
                          } }> 让ct递增</button>
                    </div>
                )
             }
        }
        ReactDOM.render( <Cp1></Cp1>, document.getElementById("app") )
    </script>

    <style>
        .kfc{
            color:red
        }
    </style>

</body>
</html>
```

上面的代码执行之后会出现 内存溢出(死循环的提示)

![](http://192.168.196.197:8000/data/img/1666340771269-6ed49761-5119-43e7-a161-07b34abd4c6d.png)

正确的 在 componentDidUpdate 当中调用 setState

```

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/react/16.14.0/umd/react.development.js" type="application/javascript"></script>
    <script src="https://lf26-cdn-tos.bytecdntp.com/cdn/expire-1-M/react-dom/16.14.0/umd/react-dom.development.js" type="application/javascript"></script>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/babel-standalone/7.0.0-alpha.20/babel.min.js" type="application/javascript"></script>
</head>
<body>
    <div id="app"></div>
    <script type="text/babel">

        class Cp1 extends React.Component {
            constructor(){
                super();
                this.state = {
                    ct:1,
                    price: 20,
                    a: 5,
                    b: 6,
                    total : 20
                }
            }
            componentDidUpdate(prevProps,prevState){
                // componentDidUpdate函数会得到三个回调参数
                // 第一个是变化之前的 props
                // 第二个是变化之前的 state
                // 这个是之前的？那么当前的呢？？？？？   this.props  this.state
                console.log('更新完成之后执行');
                // 我希望是在更新完成之后 就主动的去修改total的值
                // setState 是不是修改状态？ yes => 那么会不会引起视图的更新？ yes => 会不会执行 componentDidUpdate?
                // componentDidUpdate 会不会让里面的setState再执行？？？ 死循环了没？
                // 官方的说辞 尽量不要在 componentDidUpdate里面调用setState
                // 错误的做法
                // this.setState({
                //     total: this.state.ct * this.state.price
                // })
                // 如果非要使用 一定要是有条件的调用不然就会引起内存溢出(死循环)
                // 正确的做法
                // 用之前的state和当前的state进行对比 如果是price 或者是 ct变化了我们才去修改total
                if(prevState.ct != this.state.ct){
                   this.setState({
                    total: this.state.ct * this.state.price
                   })
                }
                if(prevState.price != this.state.price){
                    this.setState({
                     total: this.state.ct * this.state.price
                    })
                }

            }


            render(){
                return (
                    <div>
                         <div>
                             数量 { this.state.ct}   *   价格 { this.state.price } =
                             { this.state.total }
                         </div>
                         <button onClick={ ()=>{
                            this.setState({
                                ct: this.state.ct+1
                            })
                          } }> 让ct递增</button>
                    </div>
                )
             }
        }
        ReactDOM.render( <Cp1></Cp1>, document.getElementById("app") )
    </script>

    <style>
        .kfc{
            color:red
        }
    </style>

</body>
</html>
```

# 卸载阶段

## componetWillUnmount 组件即将卸载之前

这里是清除定时器或者解除绑定监听的的最好时机

```

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/react/16.14.0/umd/react.development.js" type="application/javascript"></script>
    <script src="https://lf26-cdn-tos.bytecdntp.com/cdn/expire-1-M/react-dom/16.14.0/umd/react-dom.development.js" type="application/javascript"></script>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/babel-standalone/7.0.0-alpha.20/babel.min.js" type="application/javascript"></script>
</head>
<body>
    <div id="app"></div>
    <script type="text/babel">

        class Cp2  extends React.Component {
            //  component 组件 Will 即将  Unmount 卸载 => 组件即将卸载（vue2 当中的 beforeDestroy / vue3当中的 beforeUnmount)
            componentWillUnmount(){
                console.log('cp2组件的 componentWillUnmount 执行')
            }
            render(){
                return (
                    <div> 我是Cp2组件  </div>
                )
            }
        }
        class Cp1 extends React.Component {
            constructor(){
                super();
                console.log('constructor 执行')
                // beforeCreate 对应vue的生命周期
                this.state  = {
                    ct: 1,
                    showCp2: true
                }
                // created 对应vue的生命周期
            }
            // component 组件 did 完成  mount 挂载 => 挂载完成之后  ( vue 当中的mounted)
            componentDidMount(){
                //console.log('componentDidMount 执行')
            }
            // component 组件 did 完成  Update 更新 => 组件更新完成之后  （vue 当中的 updated)
            componentDidUpdate(){
               console.log('componentDidUpdate 执行')
            }
            // component 组件 will 即将  Update 更新 => 组件更新之前执行 ( vue 当中的beforeUpdate )
            //  发生在 SCU 之后  在render之前
            componentWillUpdate(){
                console.log('componentWillUpdate  即将更新 执行')
            }
            // should是否允许 Component 组件 Update 更新=> 是否允许组件更新
            // SCU
            shouldComponentUpdate(props,state){
                // shouldComponentUpdate 第一个 ....
                // 第二个参数是修改了之后的 state
                console.log('state in the scu',state)

                // react通过shouldComponentUpdate 让我们具备控制是否更新视图的能力
                // 因为有些时候有些值变化并不会直接体现在视图上面 这个时候如果不控制那么 react的组件也会走完一整个更新渲染的流程非常的浪费性能
                if(state.ct < 5) return false; // 不更新
                return true; // 更新
            }

            UNSAFE_componentWillMount(){
                console.log('componentWillMount  执行')
            }
            render(){
                // 挂载阶段当中 类似于 beforeMount 对应vue的生命周期
                // 更新阶段当中  类似于 beforeUpdate(但这个并不完全一样)
               console.log('render执行')
                return (
                    <div>
                        我是cp1
                        <div> ct: { this.state.ct } </div>
                        <button onClick={ ()=> this.setState({ ct: this.state.ct+1 })  }>让ct递增</button>
                        <hr />
                       { this.state.showCp2 && <Cp2></Cp2> }
                       <button onClick={()=>{
                            this.setState({
                                showCp2: !this.state.showCp2
                            })
                       }}>
                             切换渲染 cp2组件
                       </button>
                    </div>
                )
             }
        }

        ReactDOM.render( <Cp1></Cp1>, document.getElementById("app") )
    </script>
    <style>
        .kfc{
            color:red
        }
    </style>

</body>
</html>
```

## 被抛弃的的生命周期

下面的三个生命周期 都是在 18 版本当中被移除的 api 官方是不推荐我们使用的。所以特地在前面加上了 UNSAFE 的前缀 标识不安全(以为你写了 以后的兼容性就会出现问题 不能快速的移植到新的 react 版本当中)

( 这个三个生命周期实际使用的时候可以不加上 UNSAFE\_ 在 16.x 的版本当中 )

- [UNSAFE_componentWillUpdate()](https://react.yubolun.com/docs/react-component.html#unsafe_componentwillupdate)  发生在组件即将更新 -> 建议使用 SCU 替代
- [UNSAFE_componentWillReceiveProps()](https://react.yubolun.com/docs/react-component.html#unsafe_componentwillreceiveprops)  接受的自定义属性或者是状态变化-> xxxx
- [UNSAFE_componentWillMount()](https://react.yubolun.com/docs/react-component.html#unsafe_componentwillmount)  发生在组件即将挂载之前 -> 建议在 render return 之前

![](http://192.168.196.197:8000/data/img/1666337343058-362265e2-fb3b-4b18-a586-a3c3a61f0517.png)

![](http://192.168.196.197:8000/data/img/1666337371919-f16a7a2c-da69-47c5-b3bf-8aefcb5f8fb8.png)

```

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/react/16.14.0/umd/react.development.js" type="application/javascript"></script>
    <script src="https://lf26-cdn-tos.bytecdntp.com/cdn/expire-1-M/react-dom/16.14.0/umd/react-dom.development.js" type="application/javascript"></script>
    <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/babel-standalone/7.0.0-alpha.20/babel.min.js" type="application/javascript"></script>
</head>
<body>
    <div id="app"></div>
    <script type="text/babel">

        class Cp1 extends React.Component {
            constructor(){
                super();
                this.state = {
                    ct:1,
                    price: 20,
                    a: 5,
                    b: 6,
                    total : 20
                }
            }
            componentDidUpdate(){
                console.log('更新完成之后执行');
                // 我希望是在更新完成之后 就主动的去修改total的值
                // setState 是不是修改状态？ yes => 那么会不会引起视图的更新？ yes => 会不会执行 componentDidUpdate?
                // componentDidUpdate 会不会让里面的setState再执行？？？ 死循环了没？
                // 官方的说辞 尽量不要在 componentDidUpdate里面调用setState
                // 如果非要使用 一定要是有条件的调用不然就会引起内存溢出(死循环)
                this.setState({
                    total: this.state.ct * this.state.price
                })
            }

            render(){
                return (
                    <div>
                         <div>
                             数量 { this.state.ct}   *   价格 { this.state.price } =
                             { this.state.total }
                         </div>
                         <button onClick={ ()=>{
                            this.setState({
                                ct: this.state.ct+1
                            })
                          } }> 让ct递增</button>
                    </div>
                )
             }
        }
        ReactDOM.render( <Cp1></Cp1>, document.getElementById("app") )
    </script>

    <style>
        .kfc{
            color:red
        }
    </style>

</body>
</html>
```

## 官方的生命周期函数执行示意图

[React lifecycle methods diagram](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)
