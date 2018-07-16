#### 复用的reducer
```js
// prefix类似于命名空间
export default function keducer(prefix, actionMutationMap = {}) {
  return (state = {}, action) => {
    return actionMutationMap[action.type]
      ? actionMutationMap[action.type](state, action.payload)
      : action.type.indexOf(`${prefix}.`) === 0
        ? { ...state, ...action.payload }
        : state
  }
}
```
