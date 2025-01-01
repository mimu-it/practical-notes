# vue store的使用

## 基本流程

对于 store.getters.dict 这个调用，首要条件是store中暴露了getters方法

```javascript
...
import getters from "./getters";
...

const store = new Vuex.Store({
    modules: {
        settingStore,
        routerStore,
        userStore,
        dictStore
    },
    getters
})

export default store
```

getters的内容是

```javascript
const getters = {
  dict: state => state.dictStore.dict
}
export default getters
```

通常情况下是这样等于getters的, 注意这个state是数据的核心

```javascript
const store = new Vuex.Store({
    state: {
      	name: 'BNTang'  
    },
    modules: {},
    actions; {},
    mutations: {},
    getters: {
        myName(state) {
            return state.name + "888"
        }
    }
})
```

也就是说 store.getters.dict 获取了 state.dictStore.dict

state.dictStore 就是来源于  modules

```javascript
modules: {
        ...
        dictStore
},
```



dictStore的代码如下：

```javascript
const state = {
    dict: {}
}
const mutations = {
    _setDict: (state, data_dictionary) => {
        if (!Util.isNone(data_dictionary)) {
            for(let dictType in data_dictionary){
                state.dict[dictType] = data_dictionary[dictType];
            }
        }
    },
    _removeDict: (state, key) => {
        delete state.dict[key];
    },
    _cleanDict: (state) => {
        state.dict = {};
    }
}

const actions = {
    // 设置字典
    setDict({ commit }, data) {
        commit('_setDict', data);
    },
    // 删除字典
    removeDict({ commit }, key) {
        commit('_removeDict', key);
    },
    // 清空字典
    cleanDict({ commit }) {
        commit('_cleanDict');
    }
}

export default {
    namespaced: true,
    state,
    mutations,
    actions
}
```

namespaced: true 意味着可以使用 Vuex.Store 中的 modules



store.getters.dict 获取了 state.dictStore.dict，就是获取了

dict_store.js中的dict

```javascript
const state = {
    dict: {}
}

export default {
    state
}
```



## 修改dict的方法

```javascript
store.dispatch('dictStore/setDict', this.data_dictionary);
```

上面代码对应于下面代码的 ***commit('_setDict', data);***

```javascript
const actions = {
    // 设置字典
    setDict({ commit }, data) {
        commit('_setDict', data);
    },
    // 删除字典
    removeDict({ commit }, key) {
        commit('_removeDict', key);
    },
    // 清空字典
    cleanDict({ commit }) {
        commit('_cleanDict');
    }
}
```

