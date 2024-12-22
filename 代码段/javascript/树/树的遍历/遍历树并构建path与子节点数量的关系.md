# 遍历树并构建path与子节点数量的关系



```javascript
hasChildren(children) {
    return (children != undefined && children != null);
},
//将树结构变成基于元素code的字典
makeTreeToStatDict(tree) {
    const dict = {};
    // level 和 maxLevel 用于规范最大遍历层级，避免错误数据导致溢出
    const context = {
        level: 0,
        maxLevel: null,
        // 用于记录走过的节点所在的path
        path: [],
        beforeChildrenWalk: (context, item, parentCode) => {
            if(this.hasChildren(item.children)) {
                // 栈顶的值就是所在的节点
                let key = context.path[context.path.length - 1];
                if(key != undefined) {
                    dict[key] = {
                        childrenCount: item.children.length
                    }
                }
            }
        },
        afterChildrenWalk: null
    }

    this.walkTreeChildren(context, tree, null);
    return dict;
},
```



```javascript
//递归遍历所有子节点
walkTreeChildren(context, children, parentCode) {
    context.level++;
    //先判断level有没有到达最大层级
    if(context.maxLevel != null && (context.level > context.maxLevel)) {
        context.level--;
        return;
    }

    //判断有没有子节点，没有子节点就不用遍历了
    if(this.hasChildren(children)) {
        //遍历子节点
        for(const item of children) {
            let latestPath = context.path[context.path.length - 1]
            if(latestPath === undefined) {
                latestPath = "";
            }
            // 为什么需要加括号？ /17/21 和 /1 有共同的匹配值 "/1"
            // 但/(17)/(21) 和 /(1) 不会
            context.path.push(latestPath + "/(" + item.code + ")");

            // 遍历子节点前进行处理
            const beforeCallback = context.beforeChildrenWalk;
            if(Object.prototype.toString.call(beforeCallback) === '[object Function]') {
                beforeCallback(context, item, parentCode);
            }

            //继续遍历子节点的子节点
            this.walkTreeChildren(context, item.children, item.code)

            // 子节点遍历完后进行的处理
            const afterCallback = context.afterChildrenWalk;
            if(Object.prototype.toString.call(afterCallback) === '[object Function]') {
                afterCallback(context, item, parentCode);
            }

            context.path.pop();
        }
    }

    context.level--;
},
```



统计某个节点下的子节点数量

```javascript
statDescendantCount(code) {
    let count = 0;
    for(let key in this.treeStatDict) {
        if(key.indexOf("(" + code + ")") != -1) {
            count += this.treeStatDict[key].childrenCount;
        }
    }

    return count;
}
```



传入Tree的参数：

```javascript
options: [{
    code: 1,
    label: '东南',
    children: [{
        code: 2,
        label: '上海',
        children: [
            { code: 3, label: '普陀'},
            { code: 4, label: '黄埔'},
            { code: 5, label: '徐汇'}
        ]
    }, {
        code: 7,
        label: '江苏',
        children: [
            { code: 8, label: '南京'},
            { code: 9, label: '苏州'},
            { code: 10, label: '无锡'}
        ]
    }, {
        code: 12,
        label: '浙江',
        children: [
            { code: 13, label: '杭州' },
            { code: 14, label: '宁波'},
            { code: 15, label: '嘉兴'}
        ]
    }]
}, {
    code: 17,
    label: '西北',
    children: [{
        code: 18,
        label: '陕西',
        children: [
            { code: 19, label: '西安'},
            { code: 20, label: '延安'}
        ]
    }, {
        code: 21,
        label: '新疆维吾尔族自治区',
        children: [
            { code: 22, label: '乌鲁木齐'},
            { code: 23, label: '克拉玛依'}
        ]
    }]
}]
```



得到的statDict为：

```javascript
{
    "/(1)":
    {
        "childrenCount": 3
    },
    "/(1)/(2)":
    {
        "childrenCount": 3
    },
    "/(1)/(7)":
    {
        "childrenCount": 3
    },
    "/(1)/(12)":
    {
        "childrenCount": 3
    },
    "/(17)":
    {
        "childrenCount": 2
    },
    "/(17)/(18)":
    {
        "childrenCount": 2
    },
    "/(17)/(21)":
    {
        "childrenCount": 2
    }
}
```

