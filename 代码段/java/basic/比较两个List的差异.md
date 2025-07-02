# 比较两个List的差异



```java
/**
 * @author xiao.hu
 * @date 2025-02-14
 * @apiNote
 */
public class ListDiffUtil {

    /**
     * 比较两个List的差异，该新增新增，该删除删除
     * list1: [1, 2, 3, 4]
     * list2: [1, 3, 3, 4]
     *
     * step1: 1 == 1  --> same: [1]
     *        list1: [2, 3, 4]
     *        list2: [3, 3, 4]
     *
     * step2: 2 找不到  --> del: [2]
     *        list1: [3, 4]
     *        list2: [3, 3, 4]
     *
     * step3: 3 == 3  --> same: [1, 3]
     *        list1: [4]
     *        list2: [3, 4]
     *
     * step4: 4 == 4  --> same: [1, 3, 4]
     *        list1: []
     *        list2: [3]
     *
     * step5: 剩下3   --> add: [3]
     *
     * @param oldList
     * @param newList
     * @return
     */
    public static DiffResult compute(List<Long> oldList, List<Long> newList) {
        List<Long> itemsForAdd = new ArrayList<>();
        List<Long> itemsForDel = new ArrayList<>();
        List<Long> sameItems = new ArrayList<>();

        Iterator<Long> iteratorOfOldList = oldList.iterator();
        while (iteratorOfOldList.hasNext()) {
            long oldItem = iteratorOfOldList.next();

            boolean found = false;
            Iterator<Long> iteratorOfNewList = newList.iterator();
            while (iteratorOfNewList.hasNext()) {
                long newItem = iteratorOfNewList.next();
                if(oldItem == newItem) {
                    // 遇到一样的就删除，剩下的就是需要新增的
                    iteratorOfNewList.remove();
                    iteratorOfOldList.remove();
                    sameItems.add(newItem);
                    found = true;
                    break;
                }
            }

            if(!found) {
                itemsForDel.add(oldItem);
            }
        }

        if(!newList.isEmpty()) {
            for(Long item : newList) {
                itemsForAdd.add(item);
            }
        }

        DiffResult diffResult = new DiffResult();
        diffResult.setItemsForAdd(itemsForAdd);
        diffResult.setItemsForDel(itemsForDel);
        diffResult.setSameItems(sameItems);
        return diffResult;
    }
}
```