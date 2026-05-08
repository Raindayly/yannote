### useState
参数1 variable
返回值 数组 第一项为state 第二项为setState函数
将变量包装为状态，通过setState更新视图
### useEffect
参数1 function 参数2 array
返回值 无
特定的时刻调用回调
根据依赖数组的不同决定回调方式和时机
1. 依赖数组为空数组
	组件挂载时执行，适合请求接口、初始化数据、注册全局事件
2. 依赖数组不传
	每次渲染完组件都要执行，场景极少使用
3. 依赖数组有值
	依赖数组中的值改变会触发
4. 回调函数返回函数
	可作为清除副作用使用，返回函数在组件卸载前执行，组件重新渲染时触发上次渲染时的返回函数
### useLayoutEffect
参数1 function 参数2 array
useLayoutEffect 语法和用法跟 useEffect 一模一样，只是执行时机不同，而且effect是<font color="#c00000">异步执行</font>，layoutEffect是<font color="#c00000">同步执行</font><font color="#c00000">阻塞页面渲染</font>
dom更新后函数同步执行完之后才开始渲染dom
应用场景，适合在函数执行开始的时候操作dom的情况使用，比如弹窗，需要在计算好位置大小之后再根据状态渲染页面
### useRef
参数1 any
返回值 dom | state
1. 获取dom
``` js
const inputRef = useRef(null);

// 挂载后聚焦
useEffect(() => {
  inputRef.current.focus();
}, []);

return <input ref={inputRef} placeholder="我是输入框" />;
```
2. 存放一个修改<font color="#c00000">不触发渲染</font>的<font color="#c00000">永久对象</font>
	由于ref包装的对象在整个组件生命周期内地址不变，不会因为函数重新执行导致原引用丢失，因此可以存放一个比函数执行周期更长的变量，并且该变量不会因为改变而触发重新渲染

### useMemo
参数1 function 参数2 array
返回值 计算结果
缓存计算结果，避免每次组件重渲染都重复执行耗时计算。
1. 只在依赖项变了，才重新计算
2. 依赖不变 → 直接拿上次缓存好的值，不重新执行函数
3. 同步执行，渲染阶段就算出结果
4. 不能放异步、不能发请求
### useCallback
参数1 function 参数2 array
返回值 函数
缓存函数本身，存的是函数引用
依赖不变 → 永远复用同一个函数引用
### useContext
createContext 造容器
外层用 xxx.Provider 传值
内层任意组件用 useContext 取值
provider的value变了，所有调用useContext的组件自动渲染
### useReducer
useReducer 是 useState 的升级版
适合：状态逻辑复杂、多个状态互相依赖、更新逻辑可复用 的场景。

``` jsx
const [state, dispatch] = useReducer(reducer, 初始值);
```
1. state：当前状态，和 useState 的 state 一样
2. dispatch：派发动作，触发状态更新
3. reducer：纯函数，接收 (旧state, action)，返回新 state
### useImperativeHandle
### useId
### useTransition
### useDeferredValue