# 遍历树并构造基于code的字典通用方法

```javascript
//将树结构变成基于元素code的字典
makeTreeToDict(tree) {
    const dict = {};
    // level 和 maxLevel 用于规范最大遍历层级，避免错误数据导致溢出
    const context = {
        level: 0,
        maxLevel: null,
        beforeChildrenWalk: (item, parentCode) => {
            const code = item.code;
            dict[code] = {
                parent: parentCode,
                label: item.label
            }
        },
        afterChildrenWalk: null
    }
    this.walkTreeChildren(context, tree, null);
    return dict;
},
//递归遍历所有子节点
walkTreeChildren(context, children, parentCode) {
    context.level++;
    //先判断level有没有到达最大层级
    if(context.maxLevel != null && (context.level > context.maxLevel)) {
        context.level--;
        return;
    }

    //判断有没有子节点，没有子节点就不用遍历了
    if(children != undefined && children != null) {
        //遍历子节点
        for(const item of children) {
            // 遍历子节点前进行处理
            const beforeCallback = context.beforeChildrenWalk;
            if(Object.prototype.toString.call(beforeCallback) === '[object Function]') {
                beforeCallback(item, parentCode);
            }

            //继续遍历子节点的子节点
            this.walkTreeChildren(context, item.children, item.code)

            // 子节点遍历完后进行的处理
            const afterCallback = context.afterChildrenWalk;
            if(Object.prototype.toString.call(afterCallback) === '[object Function]') {
                afterCallback(item, parentCode);
            }
        }
    }

    context.level--;
},
```

传入的参数是：

```javascript
options: [{
    code: 1,
    label: '东南',
    checked: false,
    children: [{
        code: 2,
        label: '上海',
        checked: false,
        children: [
            { code: 3, label: '普陀', checked: false },
            { code: 4, label: '黄埔', checked: false },
            { code: 5, label: '徐汇', checked: false }
        ]
    }, {
        code: 7,
        label: '江苏',
        checked: false,
        children: [
            { code: 8, label: '南京', checked: false },
            { code: 9, label: '苏州', checked: false },
            { code: 10, label: '无锡', checked: false }
        ]
    }, {
        code: 12,
        label: '浙江',
        checked: false,
        children: [
            { code: 13, label: '杭州', checked: false },
            { code: 14, label: '宁波', checked: false },
            { code: 15, label: '嘉兴', checked: false }
        ]
    }]
}, {
    code: 17,
    label: '西北',
    checked: false,
    children: [{
        code: 18,
        label: '陕西',
        checked: false,
        children: [
            { code: 19, label: '西安', checked: false },
            { code: 20, label: '延安', checked: false }
        ]
    }, {
        code: 21,
        label: '新疆维吾尔族自治区',
        checked: false,
        children: [
            { code: 22, label: '乌鲁木齐', checked: false },
            { code: 23, label: '克拉玛依', checked: false }
        ]
    }]
}],
```



执行的结果是：

```javascript
{
    "1":
    {
        "parent": null,
        "label": "东南"
    },
    "2":
    {
        "parent": 1,
        "label": "上海"
    },
    "3":
    {
        "parent": 2,
        "label": "普陀"
    },
    "4":
    {
        "parent": 2,
        "label": "黄埔"
    },
    "5":
    {
        "parent": 2,
        "label": "徐汇"
    },
    "7":
    {
        "parent": 1,
        "label": "江苏"
    },
    "8":
    {
        "parent": 7,
        "label": "南京"
    },
    "9":
    {
        "parent": 7,
        "label": "苏州"
    },
    "10":
    {
        "parent": 7,
        "label": "无锡"
    },
    "12":
    {
        "parent": 1,
        "label": "浙江"
    },
    "13":
    {
        "parent": 12,
        "label": "杭州"
    },
    "14":
    {
        "parent": 12,
        "label": "宁波"
    },
    "15":
    {
        "parent": 12,
        "label": "嘉兴"
    },
    "17":
    {
        "parent": null,
        "label": "西北"
    },
    "18":
    {
        "parent": 17,
        "label": "陕西"
    },
    "19":
    {
        "parent": 18,
        "label": "西安"
    },
    "20":
    {
        "parent": 18,
        "label": "延安"
    },
    "21":
    {
        "parent": 17,
        "label": "新疆维吾尔族自治区"
    },
    "22":
    {
        "parent": 21,
        "label": "乌鲁木齐"
    },
    "23":
    {
        "parent": 21,
        "label": "克拉玛依"
    }
}
```

