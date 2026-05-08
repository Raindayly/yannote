1. 一个组件里一堆状态比如表单：账号、密码、手机号、邮箱、协议勾选拆七八个 useState 很乱，不如统一一个对象管理。
	写表单时可以使用
2. 多个状态互相依赖、联动变化A 变了要跟着改 B、C，用 useState 会散得到处都是，reducer 收拢在一起好维护。
3. 同一个更新逻辑要复用多次多处都要「重置、清空、赋值默认」，写到 reducer 里统一 type，不用重复写代码。
4. 状态是嵌套对象 / 复杂结构多层对象、嵌套数组，用 reducer 集中修改更清晰，不会到处写解构。

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

1. 配合 useContext 做局部 / 全局状态子组件要改状态，不用一层层传 set 函数，直接传 dispatch 就行。
	
``` jsx


```
