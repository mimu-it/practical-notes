# Vue 监听表数据变化

下面是我们需要关心的数据

```javascript
tableData: [{
    region: [],
    first_piece_quantity: 1,
    first_amount: 1,
    subsequent_piece_quantity: 1,
    subsequent_amount: 1
}],
```

使用deep参数，可以监听数组内的变化，哪怕是其中仍有其他数组

```javascript
watch: {
    tableData: {
        deep: true,
        handler(newVal, oldVal) {
            console.log("tableData changed:", JSON.stringify(newVal))
        }
    }
},
```

el-table 使用这个数组

```javascript
<el-table
        :data="tableData"
        border
        :header-cell-style="{background:'#f5f7fa'}"
        style="width: 100%;">
    <el-table-column ...
</el-table>    
```

检测到变化后打印如下：

```javascript
tableData changed: [{"region":[{"code":1,"label":"东南","children":[{"code":7,"label":"江苏","children":[{"code":8,"label":"南京"},{"code":9,"label":"苏州"},{"code":10,"label":"无锡"}]}]},{"code":17,"label":"西北","children":[{"code":18,"label":"陕西","children":[{"code":19,"label":"西安"},{"code":20,"label":"延安"}]}]}],"first_piece_quantity":2,"first_amount":3,"subsequent_piece_quantity":1,"subsequent_amount":1.4}]
```

