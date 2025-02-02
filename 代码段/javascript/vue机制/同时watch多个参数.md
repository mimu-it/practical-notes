# 同时监控多个参数

```javascript
computed: {
   attr() {
     return {
       id: this.id,
       attrKey: this.attrKey,
       attrValues: this.attrValues
     }
   }
 },
 watch: {
   attr(newVal) {
     console.log("attr: %s", JSON.stringify(newVal))
     this.$emit("input", newVal);
   }
 },
```

