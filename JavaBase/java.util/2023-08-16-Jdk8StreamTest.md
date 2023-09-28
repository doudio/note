```java
@Test
public void templateValidationTest() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "张三");
    map.put("uid", "xxxxx");

    List<String> template = ListUtil.of(
            "恭喜{name:20}中奖了!!!",
            null,
            "",
            "{name:22}",
            "xxxxxxx",
            "请点击链接领取奖品 https://xxx.com/{uid:50}"
    );

    assert isTemplate(template, map);
}


public static final Pattern PATTERN_PLACEHOLDER = Pattern.compile("\\{[a-z]+:[0-9]+\\}");

public static boolean isTemplate(List<String> contentList, Map<String, String> templateParam) {
    try {
        Map<String, Integer> dynamicParam = contentList.stream()
                .filter(StrUtil::isNotBlank)
                .map(itm -> ReUtil.findAllGroup0(PATTERN_PLACEHOLDER, itm))
                .flatMap(List::stream)
                .distinct()
                .map(itm -> StrUtil.subBetween(itm, "{", "}").split(":"))
                .collect(Collectors.toMap(a -> a[0], a -> Integer.valueOf(a[1]), (oldValue, newValue) -> newValue));
        if (CollUtil.isEmpty(dynamicParam)) {
            if (CollUtil.isNotEmpty(templateParam)) return false;
            return true;
        }
        for (Map.Entry<String, Integer> entry : dynamicParam.entrySet()) {
            String paraVal = templateParam.get(entry.getKey());
            if (StrUtil.isBlank(paraVal) || paraVal.length() > entry.getValue()) {
                return false;
            }
        }
    } catch (Exception e) {
        System.out.println(e);
        return false;
    }
    return true;
}

@Data
@AllArgsConstructor
class Stu {
    private String name;
    private Integer age;
    private String rename;
}

@Test
public void streamGroupBy() {
    List<Stu> of = ListUtil.of(
            new Stu("zs", 10, "ddd"),
            new Stu("ls", 20, "bbb"),
            new Stu("zs", 15, "dddx"),
            new Stu("ww", 30, "wawa")
    );

    Map<String, List<Stu>> ofMap = of.stream().collect(Collectors.groupingBy(Stu::getName));

    Console.log(JSONUtil.toJsonPrettyStr(ofMap));
}

@Test
public void streamSorted() {
    List<Stu> of = ListUtil.of(
            new Stu("zs", 10, "ddd"),
            new Stu("ls", 20, "bbb"),
            new Stu("zs", 15, "dddx"),
            new Stu("ww", 30, "wawa")
    );

    List<Stu> ofList = of.stream().sorted(Comparator.comparing(Stu::getName).reversed()).collect(Collectors.toList());

    Console.log(JSONUtil.toJsonPrettyStr(ofList));
}

@Test
public void streamDistinct() {
    List<Stu> of = ListUtil.of(
            new Stu("zs", 10, "ddd"),
            new Stu("ls", 20, "bbb"),
            new Stu("zs", 15, "dddx"),
            new Stu("ww", 30, "wawa")
    );

    List<Stu> ofDistinct = of
            .stream()
            .distinct()
            .collect(Collectors.collectingAndThen(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Stu::getName))), ArrayList::new));

    Console.log(JSONUtil.toJsonPrettyStr(ofDistinct));
}
```