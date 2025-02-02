# mybatisplus 执行原生sql语句

```java
private List<BsDeliveryFeeTplNondeliveryRule> getNondeliveryRule(long templateId, String code) {
    // apply是使用的占位符
    // Preparing: SELECT * FROM bs_delivery_fee_tpl_nondelivery_rule WHERE (JSON_SEARCH(region_rule, 'all', ?) is not null AND is_deleted = ?)
    QueryWrapper<BsDeliveryFeeTplNondeliveryRule> queryWrapper = new QueryWrapper<>();
    queryWrapper.eq(BsDeliveryFeeTplRule.TEMPLATE_ID, templateId);
    //这里是原生sql
    queryWrapper.apply("JSON_SEARCH(" + BsDeliveryFeeTplFreeRule.REGION_RULE + ", 'all', {0}) is not null", code);
    List<BsDeliveryFeeTplNondeliveryRule> list = deliveryFeeTplNondeliveryRuleService.listAll(new String[]{
            BsDeliveryFeeTplNondeliveryRule.ID
    }, queryWrapper);
    return list;
}
```



```java
/**
 * 领取优惠券
 * UPDATE bs_coupon SET remaining_quantity = remaining_quantity - 1 WHERE id = 1001 AND remaining_quantity > 0 是原子操作!
 * @param couponId
 */
public void collectCoupon(long couponId) {
    UpdateWrapper<BsCoupon> updateWrapper = new UpdateWrapper<>();
    updateWrapper.setSql(String.format("%s = %s - 1", BsCoupon.REMAINING_QUANTITY, BsCoupon.REMAINING_QUANTITY))
            .eq(BsCoupon.ID, couponId)
            .eq(BsCoupon.STATUS, CouponStatus.On.getCode())
            .gt(BsCoupon.REMAINING_QUANTITY, 0)
            .eq(BsCoupon.IS_DELETED, Const.IsDeleted.NO);
    int affectRows = couponMapper.update(null, updateWrapper);
    if(affectRows == 0) {
        throw AppException.builder().errorCode(ErrorCode.not_enough_coupon.name())
                .params(BsCoupon.ID, couponId)
                .devLog(logger, "update remaining_quantity - 1 failed when collecting coupon").build();
    }
}
```