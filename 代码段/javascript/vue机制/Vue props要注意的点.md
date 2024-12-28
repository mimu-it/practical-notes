# Vue props要注意的点

## 不要直接修改props的参数

```javascript
props: {
    visible: {
        type: Boolean,
        default() {
            return false;
        }
    },
    title: {
        type: String
    }
},
```

this.visible = false  这个操作是不允许的



## v-model不应直接绑定props

```sql
<input v-model="title"> 
```

上面这个操作也不建议



## 正确方法

在子组件中创建一个局部状态来存储 prop 的值，并在需要时更新这个局部状态。

使用 watch 来监听 prop 的变化。

需要将子组件中的更改通知给父组件，可以使用 $emit 发送事件，让父组件来更新数据