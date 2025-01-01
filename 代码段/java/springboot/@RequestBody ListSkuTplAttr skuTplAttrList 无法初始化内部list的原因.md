# @RequestBody List<SkuTplAttr> skuTplAttrList 无法初始化内部list的原因

为什么id能正常初始化，而 attrKey 和 attrValues 不行？

可能的原因是浏览器传入的参数不是 "attr_key" 和 "attr_values", 我们在配置springboot的时候，使用的是下划线模式



传入的参数结构如下：

```json
[{"id":1,"attrKey":"1","attrValues":[{"val":"11"},{"val":"12"}]},{"id":2,"attrKey":"2","attrValues":[{"val":"21"},{"val":"22"}]},{"id":3,"attrKey":"3"}]
```



SkuTplAttr.class 的结构如下：

```java
public class SkuTplAttr {
    private Long id;

    /**
     * JsonProperty 用于 @RequestBody 初始化
     * 为什么id能正常初始化，而 attrKey 和 attrValues 不行？
     * 可能的原因是浏览器传入的参数不是 "attr_key" 和 "attr_values",
     * 我们在配置springboot的时候，使用的是下划线模式
     */
    @JsonProperty("attrKey")
    private String attrKey;

    @JsonProperty("attrValues")
    private List<SkuTplAttrValue> attrValues;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getAttrKey() {
        return attrKey;
    }

    public void setAttrKey(String attrKey) {
        this.attrKey = attrKey;
    }

    public List<SkuTplAttrValue> getAttrValues() {
        return attrValues;
    }

    public void setAttrValues(List<SkuTplAttrValue> attrValues) {
        this.attrValues = attrValues;
    }
}
```

