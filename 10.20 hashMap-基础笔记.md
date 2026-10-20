# Java HashMap 基础笔记

> 覆盖主题：遍历方式 · 字符频次统计 · containsKey() · Integer 包装类

---

## 一、HashMap 的 6 种遍历方式

### 1. keySet 遍历（遍历 key，再取 value）

```java
for (String key : map.keySet()) {
    System.out.println(key + "-->" + map.get(key));
}
```

- 每取一个 value 都要对 Map 做二次查找，数据量大时性能差
- 适用：只需要 key、或偶尔取值的场景

### 2. entrySet 遍历（最推荐）

```java
for (Map.Entry<String, Object> entry : map.entrySet()) {
    String key = entry.getKey();
    Object value = entry.getValue();
}
```

- 一次遍历同时拿到 key 和 value，只查一遍，性能最好
- 适用：日常全量键值对遍历，通用首选

### 3. Iterator 迭代器遍历（可安全删除）

```java
Iterator<Map.Entry<String, Object>> it = map.entrySet().iterator();
while (it.hasNext()) {
    Map.Entry<String, Object> entry = it.next();
    it.remove(); // 可安全删除当前元素
}
```

- foreach 遍历中删除元素会抛 `ConcurrentModificationException`，迭代器不会
- 适用：遍历过程中需要删除元素

### 4. values() 遍历（只需 value）

```java
for (Object value : map.values()) { ... }
```

- 拿不到 key
- 适用：只遍历值、不需要键

### 5. Lambda forEach（Java 8+，最简洁）

```java
map.forEach((key, value) -> { ... });
```

- 底层基于 entrySet，性能相当
- 注意：Lambda 内不能直接用非 final 外部变量，不能用 break/return 中断

### 6. Stream 流遍历（Java 8+，可链式操作）

```java
map.entrySet().stream()
    .filter(e -> e.getValue() != null)
    .forEach(e -> System.out.println(e.getKey()));
```

- 可结合过滤、排序、聚合
- 适用：需要对键值对做二次处理

### 选型速查

| 场景 | 选型 |
|------|------|
| 日常全量遍历 | entrySet 增强 for |
| 追求代码简洁（Java 8+） | forEach Lambda |
| 遍历中删除元素 | Iterator |
| 只需 value | values() |
| 需过滤/排序/聚合 | Stream |

> ⚠️ 不推荐用 `keySet + get` 做完整键值遍历，二次查找性能开销更高。

---

## 二、经典应用：字符频次统计

利用 HashMap **key 不可重复** + `put` 覆盖旧值的特性实现计数。

```java
import java.util.HashMap;
import java.util.Map;

public class CharCountDemo {
    public static void main(String[] args) {
        String content = "hello java hashmap";

        // 键 = 字符，值 = 出现次数
        Map<Character, Integer> countMap = new HashMap<>();

        for (int i = 0; i < content.length(); i++) {
            char c = content.charAt(i);

            if (countMap.containsKey(c)) {      // 已存在 → 次数 +1 覆盖
                countMap.put(c, countMap.get(c) + 1);
            } else {                             // 首次出现 → 次数置 1
                countMap.put(c, 1);
            }
        }

        for (Map.Entry<Character, Integer> entry : countMap.entrySet()) {
            System.out.println(entry.getKey() + " --> " + entry.getValue() + " 次");
        }
    }
}
```

**等价写法**（增强 for 遍历字符数组）：

```java
for (char c : content.toCharArray()) {
    // 内部 if-else 逻辑不变
}
```

**跳过空格**（按需加）：

```java
if (c == ' ') continue;
```

---

## 三、核心 API：containsKey()

**作用**：判断 Map 中是否存在指定 key，返回 `boolean`。

```java
boolean exists = map.containsKey("张三"); // true / false
```

- HashMap 基于哈希定位，时间复杂度接近 **O(1)**
- 支持 `null` 键判断：`containsKey(null)` 正常生效

> ⚠️ 判断键是否存在**必须用 containsKey()**，不要用 `map.get(key) != null` 替代：
> HashMap 允许 value 为 null，键存在但值为 null 时，`get()` 返回 null，`!= null` 会误判为"键不存在"。

---

## 四、包装类 Integer

### 为什么集合里要用 Integer，不能写 int？

集合（HashMap、ArrayList 等）只能存**引用类型（对象）**，不能存基本数据类型。

- ❌ `Map<Character, int>` → 编译报错
- ✅ `Map<Character, Integer>` → Integer 是 int 的包装类

Java 8 种基本类型都有对应包装类：

| 基本类型 | 包装类 |
|----------|--------|
| int | Integer |
| char | Character |
| boolean | Boolean |
| byte / short / long | Byte / Short / Long |
| float / double | Float / Double |

### 自动装箱与拆箱（Java 5+）

```java
countMap.put(c, 1);                  // int 1 自动装箱成 Integer 存入
int oldNum = countMap.get(c);        // Integer 自动拆箱成 int
```

写代码时几乎感知不到差异，可以像 int 一样直接运算。

### int vs Integer

| 特性 | int | Integer |
|------|-----|---------|
| 类型 | 基本数据类型 | 引用类型（类） |
| 默认值 | 0 | null |
| 能否存 null | 不能 | 能 |
| 能否放入集合 | 不能 | 能 |
| 自带方法 | 无 | 有 |

> 呼应上一节：Integer 可以为 null，所以 `get() != null` 不能替代 `containsKey()`。

### 常用工具方法

```java
Integer.parseInt("123");   // 字符串 → int
Integer.valueOf(123);      // int → Integer 对象
Integer.MAX_VALUE;         // int 最大值 2147483647
Integer.MIN_VALUE;         // int 最小值 -2147483648
```

---

## 五、易错点速查

| 场景 | 正确做法 |
|------|----------|
| 遍历中删除元素 | 用 Iterator，勿用 foreach |
| 判断键是否存在 | `containsKey()`，勿用 `get() != null` |
| 集合泛型 | 用包装类 `Integer`/`Character`，勿用基本类型 |
| 全量键值遍历 | 用 entrySet，勿用 keySet + get |
