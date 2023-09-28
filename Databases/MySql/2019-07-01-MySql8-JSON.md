* 单个json根据字段搜索

select * from user where all_area ->> '$areaId' = 440304;

* json数组根据字段进行搜索

SELECT * FROM user WHERE JSON_CONTAINS(all_area, JSON_OBJECT('areaId', 440305));