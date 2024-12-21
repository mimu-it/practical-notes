

# 构建基于树的checkbox组并更新祖先和子孙状态的逻辑

ui组件上某个checkbox触发了点击事件

```javascript
checkboxChange(item) {
    //item为触发点击事件的checkbox关联的数据
    //item.checked是触发点击事件的checkbox的目标状态
    this.updateChildrenState(item, item.checked);

    // 先处理item的父节点状态
    this.updateParentState(item);
	
    this.$forceUpdate();
    this.$emit("input", this.computeResult())
},
```



更新所有祖先节点的状态

```javascript
/**
 * 一直找父元素，来更新父元素，祖先元素的状态
 * @param item
 */
updateParentState(item) {
    let watchItem = item;
    while(watchItem !== undefined) {
        //计算当前节点应该怎么显示checkbox
        const result = this.computeCheckboxState(watchItem);
        if(result !== null) {
            watchItem.isIndeterminate = result.isIndeterminate;
            watchItem.checked = result.checked;
        }
        //让 watchItem 指向父节点
        watchItem = this.parentMap[watchItem.code];
    }
},
```



更新所有的子孙节点的状态

```javascript
updateChildrenState(item, checkboxState) {
    let watchItem = item;
    if(watchItem !== undefined) {
        const children = watchItem.children;
        //先更改item的子节点状态
        if(children !== undefined && children !== null) {
            if(checkboxState === true) {
                // 如果点击的checkbox的状态更新为选中，那么所有子孙节点都要被选中
                for(let it of children) {
                    it.checked = true;
                }
            }
            else {
                // 如果点击的checkbox的状态更新为非选中，那么所有子孙节点都要被非选中
                for(let it of children) {
                    it.checked = false;
                }
            }

            // 不管是选中还是不选中，isIndeterminate 都是false，因为不是中间状态
            watchItem.isIndeterminate = false;

            // 递归更新所有的孙子节点
            for(const child of children) {
                watchItem = child;
                this.updateChildrenState(watchItem, checkboxState);
            }
        }
    }
},
```



计算checkbox应该如何显示

```javascript
/**
 * 根据子节点的选中状态计算该节点的checkbox状态
 */
computeCheckboxState(parent) {
    // 子节点不存在，则无法进行计算该节点的状态
    if(parent.children == undefined || parent.children == null) {
        return null;
    }

    let count = 0;
    for (let it of parent.children) {
        if (it.checked === true) {
            // 如果checked为true, 评3分
            count = count + 3;
        }

        if (it.isIndeterminate === true) {
            // 如果 isIndeterminate 为true, 需要减 1 分
            count = count - 1;
        }
    }

    console.log("count:" + count)
    const result = {
        checked: false,
        isIndeterminate: false
    }

    if (count === 0) {
        result.checked = false;
        result.isIndeterminate = false;
    } else if (count < 3 * parent.children.length) {
        result.checked = true;
        result.isIndeterminate = true;
    } else {
        result.checked = true;
        result.isIndeterminate = false;
    }

    return result;
},
```





