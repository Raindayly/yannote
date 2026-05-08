1. 一个组件里一堆状态比如表单：账号、密码、手机号、邮箱、协议勾选拆七八个 useState 很乱，不如统一一个对象管理。
	写表单时可以使用

``` jsx
import { useReducer } from 'react';

// 模拟省市区数据源
const cityMap = {
  陕西: ['西安', '咸阳', '宝鸡'],
  广东: ['深圳', '广州', '东莞'],
  浙江: ['杭州', '宁波', '温州']
};

// 初始表单状态
const initialState = {
  username: '',
  province: '',
  city: '',
  price: 100,
  discount: 0.8, // 折扣固定8折
  finalPrice: 0  // 联动算出折后价
};

// 所有联动、状态修改逻辑 全部收拢在 reducer
function formReducer(state, action) {
  switch (action.type) {
    // 修改普通输入框
    case 'SET_FIELD':
      return {
        ...state,
        [action.key]: action.value
      };

    // 选省份：联动自动清空城市
    case 'SET_PROVINCE':
      return {
        ...state,
        province: action.value,
        city: '' // 省份变了，城市重置
      };

    // 修改原价：自动联动计算折后价
    case 'SET_PRICE': {
      const newPrice = Number(action.value);
      return {
        ...state,
        price: newPrice,
        finalPrice: newPrice * state.discount // 自动联动计算
      };
    }

    // 重置整个表单
    case 'RESET_FORM':
      return initialState;

    default:
      return state;
  }
}

export default function LinkageForm() {
  const [form, dispatch] = useReducer(formReducer, initialState);

  return (
    <div style={{ padding: 30, width: 400 }}>
      <h3>表单多字段联动示例</h3>

      {/* 用户名 */}
      <div style={{ margin: '10px 0' }}>
        <label>用户名：</label>
        <input
          value={form.username}
          onChange={(e) => dispatch({
            type: 'SET_FIELD',
            key: 'username',
            value: e.target.value
          })}
          placeholder="请输入用户名"
        />
      </div>

      {/* 省份选择 */}
      <div style={{ margin: '10px 0' }}>
        <label>省份：</label>
        <select
          value={form.province}
          onChange={(e) => dispatch({
            type: 'SET_PROVINCE',
            value: e.target.value
          })}
        >
          <option value="">请选择省份</option>
          {Object.keys(cityMap).map(p => (
            <option key={p} value={p}>{p}</option>
          ))}
        </select>
      </div>

      {/* 城市（跟随省份联动） */}
      <div style={{ margin: '10px 0' }}>
        <label>城市：</label>
        <select
          value={form.city}
          onChange={(e) => dispatch({
            type: 'SET_FIELD',
            key: 'city',
            value: e.target.value
          })}
          disabled={!form.province}
        >
          <option value="">请选择城市</option>
          {form.province && cityMap[form.province].map(c => (
            <option key={c} value={c}>{c}</option>
          ))}
        </select>
      </div>

      {/* 原价 + 自动算折后价 */}
      <div style={{ margin: '10px 0' }}>
        <label>商品原价：</label>
        <input
          type="number"
          value={form.price}
          onChange={(e) => dispatch({
            type: 'SET_PRICE',
            value: e.target.value
          })}
        />
      </div>

      <div style={{ margin: '10px 0', color: 'red' }}>
        8折后价格：{form.finalPrice}
      </div>

      <div style={{ marginTop: 20 }}>
        <button onClick={() => dispatch({ type: 'RESET_FORM' })}>
          重置表单
        </button>
      </div>

      <pre style={{ marginTop: 20 }}>
        表单全部数据：{JSON.stringify(form, null, 2)}
      </pre>
    </div>
  );
}
```

1. 多个状态互相依赖、联动变化A 变了要跟着改 B、C，用 useState 会散得到处都是，reducer 收拢在一起好维护。

``` jsx
import { useReducer } from 'react';

// 初始状态 三个联动字段
const initState = {
  count: 0,
  double: 0,
  tip: '正常'
};

// 所有联动逻辑 全部收拢在这一个地方
function reducer(state, action) {
  let newCount = state.count;

  switch (action.type) {
    case 'ADD':
      newCount = state.count + 1;
      break;
    case 'MINUS':
      newCount = state.count - 1;
      break;
    case 'RESET':
      return initState;
    default:
      return state;
  }

  // ✅ 【核心联动】只要 count 变，自动算 double、自动判 tip
  return {
    count: newCount,
    double: newCount * 2,
    tip: newCount > 5 ? '超出上限' : '正常'
  };
}

export default function ReducerDemo() {
  const [state, dispatch] = useReducer(reducer, initState);

  return (
    <div style={{padding:20}}>
      <p>count: {state.count}</p>
      <p>double(自动联动2倍): {state.double}</p>
      <p>提示(自动联动文案): {state.tip}</p>

      {/* 组件只负责发指令，完全不用管联动逻辑 */}
      <button onClick={() => dispatch({type:'ADD'})}>+1</button>
      &nbsp;&nbsp;
      <button onClick={() => dispatch({type:'MINUS'})}>-1</button>
      &nbsp;&nbsp;
      <button onClick={() => dispatch({type:'RESET'})}>重置</button>
    </div>
  );
}

```


3. 同一个更新逻辑要复用多次多处都要「重置、清空、赋值默认」，写到 reducer 里统一 type，不用重复写代码。

``` jsx
import { useReducer } from 'react';

// 初始状态
const initState = {
  username: '',
  password: '',
  age: 18,
  list: [1, 2, 3]
};

// 所有复用逻辑统一写在 reducer 里
function reducer(state, action) {
  switch (action.type) {
    // 1. 清空表单字段（复用）
    case 'CLEAR_FORM':
      return {
        ...state,
        username: '',
        password: ''
      };

    // 2. 重置整个 state 到初始值（全局重置，到处能用）
    case 'RESET_ALL':
      return initState;

    // 3. 清空数组列表（复用）
    case 'CLEAR_LIST':
      return {
        ...state,
        list: []
      };

    // 4. 给年龄赋默认值
    case 'RESET_AGE':
      return {
        ...state,
        age: 18
      };

    // 普通修改
    case 'SET_USERNAME':
      return { ...state, username: action.payload };

    default:
      return state;
  }
}

export default function Demo() {
  const [state, dispatch] = useReducer(reducer, initState);

  return (
    <div style={{ padding: 20 }}>
      <div>用户名：{state.username}</div>
      <div>密码：{state.password}</div>
      <div>年龄：{state.age}</div>
      <div>列表：{state.list.join(',')}</div>

      <br />
      {/* 普通修改 */}
      <button onClick={() => dispatch({ type: 'SET_USERNAME', payload: '小明' })}>
        设置用户名
      </button>

      <br /><br />
      {/* 复用逻辑：清空表单，多处都可以直接调 */}
      <button onClick={() => dispatch({ type: 'CLEAR_FORM' })}>
        清空账号密码
      </button>

      <br /><br />
      {/* 复用逻辑：清空列表 */}
      <button onClick={() => dispatch({ type: 'CLEAR_LIST' })}>
        清空数组列表
      </button>

      <br /><br />
      {/* 复用逻辑：重置年龄默认值 */}
      <button onClick={() => dispatch({ type: 'RESET_AGE' })}>
        重置年龄为默认
      </button>

      <br /><br />
      {/* 复用逻辑：整体全部重置 */}
      <button onClick={() => dispatch({ type: 'RESET_ALL' })}>
        重置整个状态
      </button>
    </div>
  );
}
```

1. 状态是嵌套对象 / 复杂结构多层对象、嵌套数组，用 reducer 集中修改更清晰，不会到处写解构。
``` jsx
import { useReducer } from 'react';

// 初始状态：多层嵌套对象 + 嵌套数组
const initialState = {
  base: {
    name: '张三',
    age: 18
  },
  address: {
    province: '陕西',
    city: '西安'
  },
  hobbyList: [
    { id: 1, name: '听歌' },
    { id: 2, name: '打球' }
  ]
};

// 👉 所有修改逻辑全部收拢在 reducer，组件只负责 dispatch
function reducer(state, action) {
  switch (action.type) {
    // 1. 修改基础信息 name/age
    case 'SET_BASE':
      return {
        ...state,
        base: {
          ...state.base,
          ...action.payload
        }
      };

    // 2. 修改地址省市
    case 'SET_ADDRESS':
      return {
        ...state,
        address: {
          ...state.address,
          ...action.payload
        }
      };

    // 3. 新增爱好（嵌套数组）
    case 'ADD_HOBBY':
      return {
        ...state,
        hobbyList: [...state.hobbyList, action.payload]
      };

    // 4. 根据id删除爱好
    case 'DEL_HOBBY':
      return {
        ...state,
        hobbyList: state.hobbyList.filter(item => item.id !== action.payload.id)
      };

    // 重置整个嵌套状态
    case 'RESET':
      return initialState;

    default:
      return state;
  }
}

export default function NestedForm() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div style={{ padding: 20 }}>
      <h4>当前嵌套状态</h4>
      <pre>{JSON.stringify(state, null, 2)}</pre>

      <hr />

      {/* 修改姓名年龄 */}
      <div>
        <button
          onClick={() => dispatch({
            type: 'SET_BASE',
            payload: { name: '李四', age: 25 }
          })}
        >
          修改姓名年龄
        </button>
      </div>

      {/* 修改地址 */}
      <div style={{ margin: '10px 0' }}>
        <button
          onClick={() => dispatch({
            type: 'SET_ADDRESS',
            payload: { province: '广东', city: '深圳' }
          })}
        >
          修改省市
        </button>
      </div>

      {/* 新增数组项 */}
      <div>
        <button
          onClick={() => dispatch({
            type: 'ADD_HOBBY',
            payload: { id: 3, name: '游泳' }
          })}
        >
          新增爱好：游泳
        </button>
      </div>

      {/* 删除数组项 */}
      <div style={{ margin: '10px 0' }}>
        <button
          onClick={() => dispatch({
            type: 'DEL_HOBBY',
            payload: { id: 2 }
          })}
        >
          删除 打球 爱好
        </button>
      </div>

      <div style={{ marginTop: 10 }}>
        <button onClick={() => dispatch({ type: 'RESET' })}>重置全部</button>
      </div>
    </div>
  );
}
```

5. 配合 useContext 做局部 / 全局状态子组件要改状态，不用一层层传 set 函数，直接传 dispatch 就行。
``` jsx
import { createContext, useContext, useReducer } from 'react';

// 1. 创建全局上下文
const StoreContext = createContext();

// 2. 初始全局状态
const initialState = {
  count: 0,
  name: '默认名称'
};

// 3. 统一所有状态修改逻辑：reducer
function reducer(state, action) {
  switch (action.type) {
    case 'ADD_COUNT':
      return { ...state, count: state.count + 1 };
    case 'SUB_COUNT':
      return { ...state, count: state.count - 1 };
    case 'SET_NAME':
      return { ...state, name: action.payload };
    case 'RESET_ALL':
      return initialState;
    default:
      return state;
  }
}

// 4. 中间层级组件（啥都不用传，纯占位）
function Father() {
  return <Son />;
}

// 5. 深层子组件：直接拿全局state和dispatch，不用props透传
function Son() {
  // 直接消费全局上下文
  const { state, dispatch } = useContext(StoreContext);

  return (
    <div style={{ padding: 20, border: '1px solid #ccc' }}>
      <h4>深层子组件</h4>
      <p>计数：{state.count}</p>
      <p>名称：{state.name}</p>

      <button onClick={() => dispatch({ type: 'ADD_COUNT' })} style={{ marginRight: 8 }}>
        计数+1
      </button>
      <button onClick={() => dispatch({ type: 'SUB_COUNT' })} style={{ marginRight: 8 }}>
        计数-1
      </button>
      <br /><br />
      <button
        onClick={() => dispatch({ type: 'SET_NAME', payload: '子组件直接改名字' })}
        style={{ marginRight: 8 }}
      >
        修改名称
      </button>
      <button onClick={() => dispatch({ type: 'RESET_ALL' })}>
        重置所有状态
      </button>
    </div>
  );
}

// 6. 根组件：提供全局状态
export default function App() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <StoreContext.Provider value={{ state, dispatch }}>
      <h3>根组件</h3>
      <p>根组件看计数：{state.count}</p>
      {/* 中间组件直接写标签，不用传任何 props / set 函数 */}
      <Father />
    </StoreContext.Provider>
  );
}

```
