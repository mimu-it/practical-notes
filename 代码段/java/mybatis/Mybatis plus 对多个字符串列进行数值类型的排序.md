# Mybatis plus 对多个字符串列进行数值类型的排序

```java
@Override
public List<QyPurchase> listByEstateId(long estateId, String[] columns) {
    String buildNumberOrderText = String.format("CAST(%s AS SIGNED INTEGER)", QyPurchase.BUILDING_NUMBER);
    String roomNumberOrderText = String.format("CAST(%s AS SIGNED INTEGER)", QyPurchase.ROOM_NUMBER);
    return this.query().select(columns)
            .eq(QyPurchase.ESTATE_ID, estateId)
            .eq(BaseEntity.IS_DELETED, Const.IsDeleted.NO)
            .orderByAsc(buildNumberOrderText, roomNumberOrderText).list();
}
```

