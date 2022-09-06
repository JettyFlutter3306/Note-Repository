# EasyExcel

# 一. 关于

EasyExcel是一个基于Java的简单、省内存的读写Excel的开源项目。在尽可能节约内存的情况下支持读写百M的Excel。 github地址:https://github.com/alibaba/easyexcel 开源项目不容易，如果觉得本项目对您的工作还是有帮助的话，请在帮忙在[![github star](https://img.shields.io/github/stars/alibaba/easyexcel.svg?style=flat-square&label=Stars&logo=github)](https://github.com/alibaba/easyexcel)点个★Star。

官方网站：https://easyexcel.opensource.alibaba.com/

Maven 依赖坐标：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>3.0.5</version>
</dependency>
```

# 二. 读Excel

## 2.1 简单的读

### 2.1.1 表格数据

| 标题    | 日期                | 数值 |
| ------- | ------------------- | ---- |
| 字符串0 | 2020-01-01 10:00:00 | 1    |
| 字符串1 | 2020-01-02 10:00:00 | 2    |
| 字符串2 | 2020-01-03 10:00:00 | 3    |

### 2.1.2 数据Pojo

```java
@Data
public class DemoData {
    private String string;
    private Date date;
    private Double doubleData;
}
```

### 2.1.3 简单读的监听器

```java
// 有个很重要的点 DemoDataListener 不能被spring管理，要每次读取excel都要new,
// 然后里面用到spring可以构造方法传进去
@Slf4j
public class DemoDataListener implements ReadListener<DemoData> {

    /**
     * 每隔5条存储数据库，实际使用中可以100条，然后清理list，方便内存回收
     */
    private static final int BATCH_COUNT = 100;
    /**
     * 缓存的数据
     */
    private List<DemoData> cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
    /**
     * 假设这个是一个DAO，当然有业务逻辑这个也可以是一个service。当然如果不用存储这个对象没用。
     */
    private DemoDAO demoDAO;

    public DemoDataListener() {
        // 这里是demo，所以随便new一个。实际使用如果到了spring,请使用下面的有参构造函数
        demoDAO = new DemoDAO();
    }

    /**
     * 如果使用了spring,请使用这个构造方法。每次创建Listener的时候需要把spring管理的类传进来
     *
     * @param demoDAO
     */
    public DemoDataListener(DemoDAO demoDAO) {
        this.demoDAO = demoDAO;
    }

    /**
     * 这个每一条数据解析都会来调用
     *
     * @param data    one row value. Is is same as {@link AnalysisContext#readRowHolder()}
     * @param context
     */
    @Override
    public void invoke(DemoData data, AnalysisContext context) {
        log.info("解析到一条数据:{}", JSON.toJSONString(data));
        cachedDataList.add(data);
        // 达到BATCH_COUNT了，需要去存储一次数据库，防止数据几万条数据在内存，容易OOM
        if (cachedDataList.size() >= BATCH_COUNT) {
            saveData();
            // 存储完成清理 list
            cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
        }
    }

    /**
     * 所有数据解析完成了 都会来调用
     *
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        // 这里也要保存数据，确保最后遗留的数据也存储到数据库
        saveData();
        log.info("所有数据解析完成！");
    }

    /**
     * 加上存储数据库
     */
    private void saveData() {
        log.info("{}条数据，开始存储数据库！", cachedDataList.size());
        demoDAO.save(cachedDataList);
        log.info("存储数据库成功！");
    }
}
```

### 2.1.4 持久层

```java
/**
 * 假设这个是你的DAO存储。当然还要这个类让spring管理，当然你不用需要存储，也不需要这个类。
 **/
public class DemoDAO {
    public void save(List<DemoData> list) {
        // 如果是mybatis,尽量别直接调用多次insert,
        // 自己写一个mapper里面新增一个方法batchInsert,所有数据一次性插入
    }
}
```

### 2.1.5 代码

写法1：JDK8+ ,不用额外写一个 DemoDataListener

```java
/**
 * 最简单的读
 * <p>
 * 1. 创建excel对应的实体对象 参照{@link DemoData}
 * <p>
 * 2. 由于默认一行行的读取excel，所以需要创建excel一行一行的回调监听器，
 * 	  参照{@link DemoDataListener}
 * <p>
 * 3. 直接读即可
 */
@Test
public void simpleRead() {
    String fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 这里 需要指定读用哪个class去读，然后读取第一个sheet 文件流会自动关闭
    // 这里每次会读取100条数据 然后返回过来 直接调用使用数据就行
    EasyExcel.read(fileName, DemoData.class, new PageReadListener<DemoData>(dataList -> {
        for (DemoData demoData : dataList) {
            log.info("读取到一条数据{}", JSON.toJSONString(demoData));
        }
    })).sheet().doRead();
}
```

写法2： 匿名内部类 不用额外写一个 DemoDataListener

```java
@Test
public void simpleRead() {
    fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 这里 需要指定读用哪个class去读，然后读取第一个sheet 文件流会自动关闭
    EasyExcel.read(fileName, DemoData.class, new ReadListener<DemoData>() {
        /**
         * 单次缓存的数据量
         */
        public static final int BATCH_COUNT = 100;
        /**
         * 临时存储
         */
        private List<DemoData> cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);

        @Override
        public void invoke(DemoData data, AnalysisContext context) {
            cachedDataList.add(data);
            if (cachedDataList.size() >= BATCH_COUNT) {
                saveData();
                // 存储完成清理 list
                cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
            }
        }

        @Override
        public void doAfterAllAnalysed(AnalysisContext context) {
            saveData();
        }

        /**
         * 加上存储数据库
         */
        private void saveData() {
            log.info("{}条数据，开始存储数据库！", cachedDataList.size());
            log.info("存储数据库成功！");
        }
    }).sheet().doRead();
}
```

写法3：

```java
@Test
public void simpleRead() {
    fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 这里 需要指定读用哪个class去读，然后读取第一个sheet 文件流会自动关闭
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet().doRead();
}
```

写法4：

```java
@Test
public void simpleRead() {
    fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 一个文件一个reader
    try (ExcelReader excelReader = EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).build()) {
        // 构建一个sheet 这里可以指定名字或者no
        ReadSheet readSheet = EasyExcel.readSheet(0).build();
        // 读取一个sheet
        excelReader.read(readSheet);
    }
}
```

## 2.2 属性和列名对应

使用 `@ExcelProperty` 注解，指定属性和列名对应起来，推荐使用 name 属性的方式进行映射。

```java
@Data
public class NameData {
    /**
     * 强制读取第三个 这里不建议 index 和 name 同时用，要么一个对象只用index，
     * 要么一个对象只用name去匹配
     */
    // @ExcelProperty(index = 2)
    @ExcelProperty("数值")
    private Double doubleData;
    /**
     * 用名字去匹配，这里需要注意，如果名字重复，会导致只有一个字段读取到数据
     */
    @ExcelProperty("字符串标题")
    private String string;
    @ExcelProperty("日期标题")
    private Date date;
}
```

然后代码和上面的一样，只是比原来多了注解指定了列名。

## 2.3 读取多个sheet

读多个或者全部sheet，这里注意一个sheet不能读取多次，多次读取需要重新读取文件。

```java
@Test
public void repeatedRead() {
    String fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 读取全部sheet
    // 这里需要注意 DemoDataListener的doAfterAllAnalysed 会在每个sheet读取完毕后调用一次。
    // 然后所有sheet都会往同一个DemoDataListener里面写
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).doReadAll();

    // 读取部分sheet
    fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";

    try (ExcelReader excelReader = EasyExcel.read(fileName).build()) {
        // 这里为了简单 所以注册了 同样的head 和Listener 自己使用功能必须不同的Listener
        ReadSheet readSheet1 =
            EasyExcel.readSheet(0).head(DemoData.class).registerReadListener(new DemoDataListener()).build();
        ReadSheet readSheet2 =
            EasyExcel.readSheet(1).head(DemoData.class).registerReadListener(new DemoDataListener()).build();
        // 这里注意 一定要把sheet1 sheet2 一起传进去，不然有个问题就是03版的excel 会读取多次，浪费性能
        excelReader.read(readSheet1, readSheet2);
    }
}
```

## 2.4 格式转换

只需要使用 `@DateTimeFormat` 和 `@NumberFormat` 注解指定转换格式即可。

```java
@Data
public class ConverterData {
    /**
     * 我自定义 转换器，不管数据库传过来什么 。我给他加上“自定义：”
     */
    @ExcelProperty(converter = CustomStringStringConverter.class)
    private String string;
    /**
     * 这里用string 去接日期才能格式化。我想接收年月日格式
     */
    @DateTimeFormat("yyyy年MM月dd日HH时mm分ss秒")
    private String date;
    /**
     * 我想接收百分比的数字
     */
    @NumberFormat("#.##%")
    private String doubleData;
}
```

### 2.4.1 自定义转换器

```java
public class CustomStringStringConverter implements Converter<String> {
    @Override
    public Class<?> supportJavaTypeKey() {
        return String.class;
    }

    @Override
    public CellDataTypeEnum supportExcelTypeKey() {
        return CellDataTypeEnum.STRING;
    }

    /**
     * 这里读的时候会调用
     */
    @Override
    public String convertToJavaData(ReadConverterContext<?> context) {
        return "自定义：" + context.getReadCellData().getStringValue();
    }

    /**
     * 这里是写的时候会调用 不用管
     */
    @Override
    public WriteCellData<?> convertToExcelData(WriteConverterContext<String> context) {
        return new WriteCellData<>(context.getValue());
    }
}
```

## 2.5 多行头

```java
/**
 * 多行头
 * 设置headRowNumber参数，然后读。 这里要注意headRowNumber如果不指定，
 * 会根据你传入的class的@ExcelProperty里面的表头的数量来决定行数，
 * 如果不传入class则默认为1.当然你指定了headRowNumber不管是否传入class都是以你传入的为准。
 */
@Test
public void complexHeaderRead() {
    String fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 这里 需要指定读用哪个class去读，然后读取第一个sheet 
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet()
        // 这里可以设置1，因为头就是一行。如果多行头，可以设置其他值。不传入也可以，
        // 因为默认会根据DemoData 来解析，他没有指定头，也就是默认1行
        .headRowNumber(1).doRead();
}
```