# java8新特性

java8新特性 | 说明
-|-
1、Lambda表达式 | -
2、接口的默认方法与静态方法|-
3、方法引用|-
4、重复注解|-
5、扩展注解的支持|
6、Optional|-
7、Stream|-
8、Date/Time API (JSR 310)|-
9、JavaScript引擎Nashorn|
10、Base64 | java8对Base64进行优化后，放到java.util包下


## Java Lambda
Lambda是Java8的新特性 ，是一个语法糖

```java
// 语法格式
(params) -> expression
(params) -> statement
(params) ->{ statements; }

// List循环
List<String> list = ...;
list.forEach( reslut -> {
    reslut +="_修改过" ;
});
// 循环输出
list.forEach(System.out::println);

//Map循环
Map<String, Integer> map =...;
map.forEach( (key ,value)-> {
    value +=100 ;
});
map.forEach( ( key ,value ) -> System.out.println( "key="+key +"  value="+value)  );

// List 条件过滤
List<String> result1 =
        // 将list转为 流
        list.stream()
        // 将符合条件的元素过滤出来
        .filter( line -> line.contains("aa"))
        // 收集并将输出的流转为 list
        .collect(Collectors.toList());
result1.forEach(System.out::println);

// 对象排序
List<Student> listSort= ... ;
listSort.sort( (Student stu1 , Student stu2) -> stu1.age- stu2.age);
listSort.forEach( (sutdent) -> {
    System.out.println(sutdent.name+" : "+sutdent.age);
});
```

<br>
#### 接口的默认方法与静态方法
```java
public interface MyMathTool {
    // 被 default关键字 修饰过的 sqrt 方法 将在子类上可以直接使用.
    // 可以有多个 默认方法
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}

// 被 static修饰的方法 ，跟普通类的静态方法一样可以直接调用
// 可以有多个 静态方法
static double cacle(int a, int b){
    return  a+b ;
}
```

## 函数式编程
```xml
函数式接口是Java支持函数式编程的基础。
"函数式编程"是一种"编程范式"
```

<br>

## 方法引用
```xml
方法引用是lambda表达式的一种语法糖, 为了简化代码和加速开发。

当我们想要实现一个函数式接口的那个抽象方法，但是已经有类实现了我们想要的功能，
这个时候我们就可以用方法引用来直接使用现有类的功能去实现。（至于为什么可以这样，我暂时还不知道，没看源码。）

方法引用规定：实现抽象方法的参数列表，必须与方法引用方法的参数列表保持一致。返回值就不作要求。
```

### 引用分类

分类 | 说明
-|-
引用方法 |类名::静态方法名<br> 类名::实例方法名 <br> 实例::实例方法名
引用构造器|
引用数组|

```java
// 实例::实例方法名
Consumer<String> consumer = System.out::println;
consumer.accept("This is Major Tom");

// 类名::静态方法名
Function<Long, Long> f = Math::abs;
Long result = f.apply(-3L);

// 类名::实例方法名
// 这个地方有点不太好理解。
// 可以暂时这样理解： 生成了一个 BiPredicate 实例b ，
// 并且 b中的 方法 test里面的内容和 String类的静态方法 equals 一模一样
BiPredicate<String, String> b = String::equals;
b.test("abc", "abcd");

// 引用构造器
Function<Integer, StringBuffer> fun = StringBuffer::new;
StringBuffer buffer = fun.apply(10);

//  引用数组
Function<Integer, Integer[]> fun2 = Integer[]::new;
Integer[] arr2 = fun2.apply(10);

```


<br>

## 函数式接口
为了更友好的支持Lambda表达式，Java 8引入了函数式接口的概念。
所谓的函数式接口，当然首先是一个接口，然后就是在这个接口里面只能有一个抽象方法。
```java
1、Java 8为函数式接口引入了一个新注解 @FunctionalInterface，主要用于编译级错误检查，
加上该注解，当你写的接口不符合函数式接口定义的时候，编译器会报错。

2、加不加@FunctionalInterface对于接口是不是函数式接口没有影响，
该注解知识提醒编译器去检查该接口是否仅包含一个抽象方法

3、函数式接口里是可以包含默认方法 、静态方法、Object里的public方法
```

```java
@FunctionalInterface
public interface Consumer <String>{
    void accept(String msg);
}
```

### 内置的函数式接口
为了方便开发者，java8内置了一批函数式接口。

Java8内置的函数式接口 |  	参数类型 |	返回类型 |	用途
-|-
Consumer|T |void  | 对类型T参数操作，无返回结果，包含方法 void accept(T t)
Supplier|无 | T	 | 返回T类型参数，方法时 T get()
Function<T,R>|	T|	R	|对类型T参数操作，返回R类型参数，包含方法 R apply（T t）
Predicate	|T|	boolean|	断言型接口，对类型T进行条件筛选操作，返回boolean，包含方法 boolean test（T t）
BinaryOperator|T,T|	T	|对类型T对象进行二元运算，并返回T类型的结果，包含方法T apply(T t1,T t2)
BigConsumer	|T,U	|void	|对类型T，u参数进行操作，包含方法void accept（T t,U u）
BiConsumer<T,U> | || 代表了一个接受两个输入参数的操作，并且不返回任何结果
BiFunction<T,U,R>|||代表了一个接受两个输入参数的方法，并且返回一个结果
BinaryOperator<T>|||代表了一个作用于于两个同类型操作符的操作，并且返回了操作符同类型的结果
BiPredicate<T,U>|||代表了一个两个参数的boolean值方法
BooleanSupplier|||代表了boolean值结果的提供方
Consumer<T>|||代表了接受一个输入参数并且无返回的操作
DoubleBinaryOperator||| 代表了作用于两个double值操作符的操作，并且返回了一个double值的结果。
DoubleConsumer|||代表一个接受double值参数的操作，并且不返回结果。
DoubleFunction<R>|||代表接受一个double值参数的方法，并且返回结果
DoublePredicate ||| 代表一个拥有double值参数的boolean值方法
DoubleSupplier|||代表一个double值结构的提供方
DoubleToIntFunction|||接受一个double类型输入，返回一个int类型结果。
DoubleToLongFunction||| 接受一个double类型输入，返回一个long类型结果
DoubleUnaryOperator|||接受一个参数同为类型double,返回值类型也为double 。
IntBinaryOperator|||接受两个参数同为类型int,返回值类型也为int 。
IntConsumer|||接受一个int类型的输入参数，无返回值 。
IntFunction<R>|||接受一个int类型输入参数，返回一个结果 。
IntPredicate|||接受一个int输入参数，返回一个布尔值的结果。
IntSupplier|||无参数，返回一个int类型结果。
IntToDoubleFunction|||接受一个int类型输入，返回一个double类型结果 。
IntToLongFunction|||接受一个int类型输入，返回一个long类型结果。
IntUnaryOperator||接受一个参数同为类型int,返回值类型也为int 。
LongBinaryOperator|||接受两个参数同为类型long,返回值类型也为long。
LongConsumer|||接受一个long类型的输入参数，无返回值。
LongFunction<R>|||接受一个long类型输入参数，返回一个结果。
LongPredicate|||R接受一个long输入参数，返回一个布尔值类型结果。
LongSupplier|||无参数，返回一个结果long类型的值。
LongToDoubleFunction|||接受一个long类型输入，返回一个double类型结果。
LongToIntFunction|||接受一个long类型输入，返回一个int类型结果。
LongUnaryOperator|||接受一个参数同为类型long,返回值类型也为long。
ObjDoubleConsumer<T>|||接受一个object类型和一个double类型的输入参数，无返回值。
ObjIntConsumer<T>|||接受一个object类型和一个int类型的输入参数，无返回值。
ObjLongConsumer<T>|||接受一个object类型和一个long类型的输入参数，无返回值。
Predicate<T>|||接受一个输入参数，返回一个布尔值结果。
Supplier<T>|||无参数，返回一个结果。
ToDoubleBiFunction<T,U>|||接受两个输入参数，返回一个double类型结果
ToDoubleFunction<T>|||接受一个输入参数，返回一个double类型结果
ToIntBiFunction<T,U>|||接受两个输入参数，返回一个int类型结果。
ToIntFunction<T>|||接受一个输入参数，返回一个int类型结果。
ToLongBiFunction<T,U>|||接受两个输入参数，返回一个long类型结果。
ToLongFunction<T>|||接受一个输入参数，返回一个long类型结果。
UnaryOperator<T>|||接受一个参数为类型T,返回值类型也为T。


<br>

## 匿名函数
参考匿名类。 匿名函数就是一个没有名字的函数。
常与 Lambda 搭配使用
```java
// 新建了一个Runnable 对象。省略了 Runnable 的run函数的名字，并在函数里面打印了一句话
Runnable r = () -> System.out.println("Thread start...");
// 使用这个新建的对象
new Thread(r).start();
```

<br>

## Optional

```xml
是一个封装了一个元素的容器，而这个元素可以是null也可以是其它任意类型的。
```
Optional 的方法 | 说明
-|-
Optional.of(T t) | 创建一个 Optional 实例
 Optional.ofNullable(T t) | 若 t 不为 null,创建 Optional 实例,否则创建空实例
Optional.empty() | 创建一个空的 Optional 实例
optional.get() | 获取Optional中的值
optional.isPresent() | 判断optional对象是否有值


<br>
## Stream
```xml
这个Stream并不是我们以前认识的IO流，
而是一个数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。
Stream API 提供了一种高效且易于使用的处理数据的方式 ，例如 非常复杂的查找、过滤和映射数据等 。

注意：Stream 不会改变源对象。它会返回一个持有结果的新Stream（这样可以链式编程）。
```
## Stream 延迟执行
只要Stream的方法返回的对象是Stream，这些方法就是延迟执行的方法 。
一般在Stream流中，一个方法返回的不是Stream，基本就是迫切方法 。
```java
// 由集合创建 Stream
List<String> list = new ArrayList<>();
//获取一个顺序流
Stream<String> stream = list.stream();
//获取一个并行流
Stream<String> parallelStream = list.parallelStream();

// 由数组创建流
Integer[] nums = new Integer[8];
Stream<Integer> stream3 = Arrays.stream(nums);

// 由值创建流
Stream<Integer> stream4 = Stream.of(1,2,3,4,5);

// 由函数创建流 , 会不停打印一个随机数
Stream.generate(() -> Math.random()).forEach(System.out::println);
Stream.iterate(1, (x) -> x + 1).forEach(System.out::println);

// 筛选 ,自定义
List<Integer> list2 = Arrays.asList(2, 3, 5, 4, 4,4, 1, 8, 10, 9, 7, 6);
list2.stream() .filter((e) -> e > 5).forEach(System.out::println);
//  筛选 ，去重
list2.stream()  .distinct()  .forEach(System.out::println);
// 切割 ，限定个数
list2.stream().limit(3).forEach(System.out::println);
// 返回一个去掉了前 n 个元素的流。若元素不足 n 个，则返回一个空流。
list.stream().skip(3).forEach(System.out::println);
// 映射 , 将 参数 经过自定义处理后 再返回
list2.stream() .map(x -> x*x) .forEach(System.out::println);
// 特殊的 映射操作 ， 结果一定要是 double类型
list2.stream() .mapToDouble(x -> x+0.1)  .forEach(System.out::println);

// 特殊的映射 ， 将流中的每个元素映射成一个流
list2.stream()  .flatMap( xxx ) .forEach(System.out::println);

// 按自然顺序排序
list2.stream()   .sorted()  .forEach(System.out::println);
// 自定义排序
list.stream()  .sorted( (x,y) -> -x.compareTo(y) )  .forEach(System.out::println);

// 检查是否匹配所有元素 ,(是否全部元素都大于2)
boolean allMatch = list2.stream() .allMatch(x -> x > 2);

// 匹配元素位置
Optional<Integer> first = list2.stream() .findFirst();
int val = first.get();

// 求最大值 最小值
Optional<Integer> first1 = list2.stream() .min(Integer::compareTo);
Optional<Integer> first2 = list2.stream() .max(Integer::compareTo);
Integer val1 = first.get();

// 将流中元素收集到List中
List<Integer> resultList = list2.stream() .collect(Collectors.toList());
// 将流中元素收集到Set中
Set<Integer> resultSet = list2.stream() .collect(Collectors.toSet());
```


<br>

## 日期时间 API
```xml
在旧版的 Java 中，日期时间 API 存在很多问题，比如线程安全问题 、命名、设计、分包不合理等问题。
Java 8 提供的日期时间 API都在java.time包下，
这个包涵盖了所有处理日期(date)，时间(time)，日期/时间(datetime)，
时区（zone)，时刻（instant），间隔（duration）与时钟（clock）的操作
```
```java
// 获取当前日期时间
LocalDateTime now = LocalDateTime.now();
int year = now.getYear();
int month = now.getMonthValue() ;
int day = now.getDayOfMonth() ;
// 将当前日期时间减去两天
LocalDateTime dateTime2 = now.minusDays(2);
// 将当前日期时间加上五天
LocalDateTime dateTime3 = now.plusDays(5);
// 构造一个指定日期时间的对象 , 年 月 日 时 分
LocalDateTime dateTime = LocalDateTime.of(2016, 10, 23, 8, 20);


// 获取当前时间的时间戳
Instant instant = Instant.now();
// 结果跟System.currentTimeMillis()一样
long milli = instant.toEpochMilli();
// 时区偏移
OffsetDateTime dateTime4 = instant.atOffset(ZoneOffset.ofHours(8));


//时间间隔
LocalTime start = LocalTime.now();
LocalTime end = LocalTime.now();
Duration duration = Duration.between(start, end);

// 日期间隔
LocalDate startDate = LocalDate.of(2015, 3, 4);
LocalDate endDate = LocalDate.of(2017, 8, 23);
Period period = Period.between(startDate, endDate);

// 日期格式化 为字符串  ， 内置格式
LocalDate now6 = LocalDate.now();
String strNow = now6.format(DateTimeFormatter.BASIC_ISO_DATE);
// 日期格式化 为字符串  ， 自定义格式
String strNow2 = now6.format(DateTimeFormatter.ofPattern("yyyy年MM月dd日"));
// 将字符串转换成日期
LocalDate date = LocalDate.parse(strNow2, DateTimeFormatter.ofPattern("yyyy年MM月dd日"));

// 目前支持的时区
Set<String> set = ZoneId.getAvailableZoneIds();
//获取当前时区的日期时间
ZonedDateTime now3 = ZonedDateTime.now();
//获取美国洛杉矶时区的日期时间
ZonedDateTime USANow = ZonedDateTime.now(ZoneId.of("America/Los_Angeles"));
```

<br>

## 重复注解
```xml
在 java8 以前，同一个程序元素前最多只能有一个相同类型的注解；
如果需要在同一个元素前使用多个相同类型的注解，则必须使用注解“容器”。

java8简化了使用的步骤，虽然仍然要定义“容器” 。但是直接使用的就是注解。
```
```java
// java8 之前的写法
@Retention(RetentionPolicy.RUNTIME)
public @interface Authority {
    String role();
}

@Retention(RetentionPolicy.RUNTIME)
public @interface Authorities {
    Authority[] value();
}

@Authorities({@Authority(role = "Admin"), @Authority(role = "Manager")})
private void test( ) { ... }
```

```java
// java8 及之后的写法
@Retention(RetentionPolicy.RUNTIME)
@Repeatable(Authorities.class)
public @interface Authority {
   String role();
}

@Retention(RetentionPolicy.RUNTIME)
public @interface Authorities {
    Authority[] value();
}

@Authority( role = "Admin" )
@Authority( role = "Manager" )
private void test( ) { ... }
```
