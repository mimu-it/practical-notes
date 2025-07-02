# 避免el-dialog下的input出现两个clearable 

```css
.main-content {
​    ::v-deep .el-input__validateIcon {
​        display: none;
​    }
}
```

其中在dialog上增加css类“main-content”

```vue
<el-dialog :visible.sync="showDialog" :title="dialogTitle" :width="width + 'px'" :before-close="handleClose" append-to-body  :close-on-click-modal="false" class="main-content">
```

