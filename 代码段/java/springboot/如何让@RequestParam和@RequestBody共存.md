# 如何让@RequestParam和@RequestBody共存

axios请求时，url需要构造get模式使用的queryParams 模式，也就是url附带问号传参数，然后post中使用header采用 {'Content-Type': 'application/json'}

重写构造一个post函数，名为postMix

```javascript
export function postMix(url, data = {}, queryParams = {}, headers = {'Content-Type': 'application/json'}) {
    return new Promise((resolve, reject) => {
        NProgress.start();

        // 将查询参数添加到URL
        const queryString = new URLSearchParams(queryParams).toString();
        const urlWithQuery = `${url}?${queryString}`;

        Vue.axios({
            url: urlWithQuery,
            method: 'POST',
            headers: headers,
            data: data, //post方式必须是data参数而不是params
            responseType: 'json'
        }).then(response => {
            /* 因为在响应拦截器中已经处理了isOK为false的情况，如果在拦截器中直接return，会导致这里的response为undefined */
            if (response != undefined) {
                resolve(response.data);
            }

            NProgress.done();
        }, err => {
            /* 超时会调用这里 */
            reject(err);
            NProgress.done();
        })
    })
}
```



使用方式

```javascript
API.saveSkuConfigAsTemplate(list, {id: 1}).then((responseData) => {

}).catch((res) => {
    Common.showErrorMsgBox(this, res);
});
```

api.js中代码如下

```javascript
saveSkuConfigAsTemplate: (params, queryParams) => postMix('/admin/goods-sku-tpl/save', params, queryParams),
```



服务端代码如下：

```java
@PostMapping(Url.SAVE)
public RespResult saveAsTemplate(
    @RequestParam(name = BsGoodsSkuTpl.ID, required = false) String idStr,
    @RequestParam(name = BsGoodsSkuTpl.NAME, required = false) String skuTplName,
    @RequestBody List<SkuTplAttr> skuTplAttrList) {
    if(StrUtil.isNumeric(idStr)) {
        Long skuTplId = Long.parseLong(idStr);
        goodsSkuTplSrv.saveAsTemplate(skuTplId, skuTplAttrList);
        return RespResult.success(skuTplId);
    }
    else if(StrUtil.isBlank(skuTplName)){
        return RespResult.failure(ErrorCode.illegal_argument.name());
    }

    long skuTplId = goodsSkuTplSrv.addTemplate(skuTplName, skuTplAttrList);
    return RespResult.success(skuTplId);
}
```

