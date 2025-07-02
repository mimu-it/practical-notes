# 让子组件能够触发父组件form的校验的方法

父组件代码片段如下：

```vue
<el-form-item :label="$t('message.term.customer_name')" :label-width="formLabelWidth" prop="customer_name">
    <CustomerPicker v-model="formIU.customer_name">  
    </CustomerPicker>
</el-form-item>
```



这里CustomerPicker是自定义组件，CustomerPicker组件中又包含了TablePicker子组件

```vue
<TablePicker v-model="formIU.name" @change="onPick" :columnsDef="columnsDef" placeholder="请选择产品" value-field="name" display-field="name" :fetch="query"></TablePicker>
```

CustomerPicker务必要把"formIU.name"的值 $emit上去

```js
watch: {
	value: {
​   	immediate: true,
		handler(newVal) {
​           this.formIU.name = newVal;
​       }
​   },
	"formIU.name"(newVal) {
		this.$emit("input", newVal)
	}
},
```

TablePicker中有两个组件，一个是input，其自带触发@Blur事件，会向父组件广播，从而触发校验，我们不要处理，另外一个是弹出框选择值确定后赋值给input，这种赋值不会触发@Blur事件，我们需要手动执行

```js
confirmSelection() {
	if (this.selectedRows.length === 0) {
		this.$message.warning('请选择数据')
		return
	}

	const selectedRow = this.selectedRows[0]
	this.$emit('input', this.returnObject ? selectedRow : selectedRow[this.valueField]);
	// 因为此操作没有触发input的blur，为了触发外层form的校验，需要手动触发el.form.blur
	this.triggerElFormValidate(selectedRow);
	this.handleClose(() => {
		this.showDialog = false;
	});
},
```

triggerElFormValidate就是手动触发的代码

```js
/**
 * 因为此操作没有触发input的blur，为了触发外层form的校验，需要手动触发el.form.blur
 * 记得务必要一直this.$emit('input', xx) 到父组件，中间千万不要中断
 */
triggerElFormValidate(inputValue) {
	this.$nextTick(() => {
		this.parent && this.parent.$emit("el.form.blur", inputValue);
		this.parent && this.parent.$emit("el.form.change", inputValue);
	})
},
```

这里的parent是在加载页面时，通过循环向上查找获得form的对象，从而触发事件

```js
/**
 * 为了让深层子组件能够触发父层的form校验，需要循环找form的父组件
 */
getParent() {
	let parent = this.$parent;
    while(parent) {
    	if(parent.$options.name === "ElFormItem") {
    		this.parent = parent;
       		break;
    	}
    
    	parent = parent.$parent;
    }
},
```

```js
mounted() {
	this.getParent();
	// 组件挂载时初始化数据
	this.fetchData()
},
```