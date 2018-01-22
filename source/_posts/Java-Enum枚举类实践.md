---
title: Java Enum枚举类实践
date: 2018-01-22 17:31:42
tags: Java Enum 枚举
---
## 应用场景
业务系统中的业务对象，一般都会涉及到业务状态，比如：草稿、待审核、发布。为了保证状态的统计管理和使用，我们一般都会先去定义好这些状态，业务逻辑中引用这些定义。
在这里，数据结构就值得我们斟酌一下。
## 应用场景
### 静态变量
有很多人可能会这么去写：
``` java
//状态(0-草稿，1-发布)
public static final int RELEASED_STATUS = 1;
public static final int DRAFT_STATUS  = 0;
```
组织结构上，可能是写在业务对象内或单独一个类或集中写在一个类。
这么写，基本能满足要求。但不是最好的：
- 不够清晰，不易读懂，静态变量多了就需要你花费时间去分辨对应关系
- 类型不安全，设置时，如果直接设置int值，编译过程中不会被检查出来
- 如果你还需要解析对应有名称，如 0:"草稿"，还需要额外编写方法，使代码更加难以维护

### 枚举（enum）
可以很完美地解决问题：
``` java
public enum MyStatus{
    DRAFT("待修订", 0), PENDING("待审核", 1), ACCEPTED("正常", 2);

    private String name;
    private int index;

    /**
     * 构造函数
     * @param name
     * @param index
     */
    MyStatus(String name, int index) {
        this.name = name;
        this.index = index;
    }
    /**
     * 取index
     * @return
     */
    public int getIndex() {
        return this.index;
    }

    /**
     * 取name
     * @return
     */
    public String getName() {
        return this.name;
    }
    /**
     * 获取包含所有成员的map
     */
    public static Map getAll() {
        HashMap<Integer, String> all = new HashMap<>();
        for (MyStatus i : MyStatus.values()) {
            all.put(i.index, i.name);
        }
        return all;
    }

    public static void main(String[] args){
        System.out.println(JSON.toJSONString(MyStatus.values()));
        System.out.println(JSON.toJSONString(MyStatus.getAll()));
    }
}
```
注：enum内部其实就是一个特别的类，你可以定义自己的各种方法去满足业务需求。