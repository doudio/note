> ### 将 map 根据 value 排序

```java
    @Test
    public void contextLoads() {
        HashMap<String, String> dataMap = new HashMap<>();
        dataMap.put("3", "c");
        dataMap.put("2", "b");
        dataMap.put("1", "z");
        dataMap.put("5", "e");
        dataMap.put("4", "d");

        dataMap.forEach((k,v) -> System.out.println(String.format("key: %s, value: %s", k, v)));

        Map<String, String> sort = sort(dataMap, Comparator.comparing(Map.Entry::getValue));

        System.out.println("===========================;");

        sort.forEach((k,v) -> System.out.println(String.format("key: %s, value: %s", k, v)));

    }

   /**
     * 给 map 通过 value 排序
     *
     * @param map 被排序的 map
     * @param comparator<Map.Entry<K, V>> map 中的值可以自定义排序的字段
     * @return LinkedHashMap 被排序的 map
     */
    public static <K, V> Map<K, V> sort(Map<K, V> map, Comparator<Map.Entry<K, V>> comparator) {
        Map<K, V> sortedMap = new LinkedHashMap<>();
        ArrayList<Map.Entry<K, V>> entryList = new ArrayList<>(map.entrySet());
        Collections.sort(entryList, comparator);
        Iterator<Map.Entry<K, V>> iter = entryList.iterator();
        Map.Entry<K, V> tmpEntry = null;
        while (iter.hasNext()) {
            tmpEntry = iter.next();
            sortedMap.put(tmpEntry.getKey(), tmpEntry.getValue());
        }
        return sortedMap;
    }
```