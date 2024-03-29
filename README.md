# Lambda表达式 #

## 为什么使用`Lambda`表达式 ##

`Lambda`是一个匿名函数,我们可以把`Lambda`表达式理解为是一段可以传递的代码(将代码像数据一样进行传递)。可以写出更简洁、更灵活的代码。作为一种更紧凑的代码风格.使Java的语言表达能力得到了提升

## 代码对比 ##

```java
public class TestLamabda1 {

    public static void main(String[] args) {
        test1();
    }

    //原来的匿名内部类
    public static void test1(){
        //方法一
        Comparator<String> comparator = new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return Integer.compare(o1.length(), o2.length());
            }
        };
        TreeSet<String> tree1 = new TreeSet<>(comparator);
        
         //方法二
        TreeSet<String> tree2 = new TreeSet<>(new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return Integer.compare(o1.length(), o2.length());
            }
        });
    }

    //现在的 Lambda 表达式
    public static void test2(){
        Comparator<String> com = (x, y) -> Integer.compare(x.length(), y.length());
        TreeSet<String> ts = new TreeSet<>(com);
    }	
}
```

> 对比可以看出原来的匿名内部类所需的代码量比较多
>

新增一个`Employee`类根据不同需求取出结果

```java
public class TestLambda2 {
   static List<Employee> emps = Arrays.asList(
            new Employee(101, "张三", 18, 9999.99),
            new Employee(102, "李四", 59, 6666.66),
            new Employee(103, "王五", 28, 3333.33),
            new Employee(104, "赵六", 8, 7777.77),
            new Employee(105, "田七", 38, 5555.55)
    );

    public static void main(String[] args) {
        List<Employee> employees1 = filterEmployeeForAge(emps);
        System.out.println(employees1);
        System.out.println("============");
        List<Employee> employees2 = filterEmployeeForSalary(emps);
        System.out.println(employees2);
    }

    //需求1：获取公司中年龄小于 35 的员工信息
    private static List<Employee> filterEmployeeForAge(List<Employee> emps){
        ArrayList<Employee> list = new ArrayList<>();
        for(Employee employee:emps){
            if (employee.getAge()<35){
                list.add(employee);
            }
        }
        return list;
    }

    //需求2：获取公司中工资大于 5000 的员工信息
    private static List<Employee> filterEmployeeForSalary(List<Employee> emps)
    {
        ArrayList<Employee> list = new ArrayList<>();
        for(Employee employee:emps){
            if (employee.getSalary()>5000){
                list.add(employee);
            }
        }
        return list;
    }
}
```

如果每次根据不同需求新增方法过滤不同的结果，其代码的重复量比较多,实际的就只是判断的方式不同

### 优化方式一：策略设计模式 ###

```java
	//定义接口
	public interface MyPredicate<T> {
	    boolean filter(T t);
	}

	//根据要过滤的需求新增两个实现类
	public class FilterEmployeeForAge implements MyPredicate<Employee> {
	    @Override
	    public boolean filter(Employee employee) {
	        if (employee.getAge()<35){
	            return true;
	        }
	        return false;
	    }
	}

	public class FilterEmployeeForSalary implements MyPredicate<Employee> {
	    @Override
	    public boolean filter(Employee employee) {
	        if (employee.getSalary()>5000){
	            return true;
	        }
	        return false;
	    }
	}

	//在实际调用中新增方法把参数进去
	private static List<Employee> filterEmployee1(List<Employee> emps, MyPredicate<Employee> filter)
    {
        List<Employee> list = new ArrayList<>();
        for (Employee employee:emps){
            if(filter.filter(employee))
            {
                list.add(employee);
            }
        }

        return list;
    }
```

### 优化方式二:匿名内部类 ###

```java
	//根据上面的方式只定义接口和方法，具体实现在内部类里实现
	private static List<Employee> filterEmployee2(List<Employee> emps)
    {

        List<Employee> list = new ArrayList<>();

        list = filterEmployee1(emps, new MyPredicate<Employee>() {
            @Override
            public boolean filter(Employee employee) {
               if (employee.getSalary() >5000){
                   return true;
               }
               return false;
            }
        });
        return list;
    }
```

### 优化方式三:`Lambda `表达式 ###

```java
	//根据优化里定义的接口和方法，用Lambda 表达式
    private static void filterEmployee3(List<Employee> emps)
    {
        List<Employee> employees1 = filterEmployee1(emps, (e) -> e.getAge() < 35);
        employees1.forEach(System.out::println);
        System.out.println("=======================");
        List<Employee> employees2 = filterEmployee1(emps, (e) -> e.getSalary() > 5000);
        employees2.forEach(System.out::println);
    }
```

### 优化方式四：`Stream API` ###

```java
    private static void filterEmployee4(List<Employee> emps){
        emps.stream().filter((e)->e.getSalary()>5000).forEach(System.out::println);
        System.out.println("==================");
        emps.stream().filter((e)->e.getAge()<35).forEach(System.out::println);
    }
```

## `Lambda`表达式的基础语法 ##

Java8中引入了一个新的操作符 `->` 该操作符称为箭头操作符或 `Lambda `操作符

箭头操作符将 Lambda 表达式拆分成两部分：

- 左侧：`Lambda `表达式的参数列表
- 右侧：`Lambda `表达式中所需执行的功能， 即 `Lambda `体

### 语法格式一 ###

无参数，无返回值() ->` System.out.println("Hello Lambda!");`

```java
/**Lambda表达式相关于实现接口类的方法**/
private static void test1()
{
    int num = 0;//jdk 1.7 前，必须是 final

	//注意:内部类在传递局部变量在1.7前必须是final属性的,因此不能变更,
    Runnable runnable1 = new Runnable() {
        @Override
        public void run() {
            System.out.println("Hello world!"+num);
        }
    };
    runnable1.run();

	//因此，Lambda表达式也一样在传局部变量时,相当于final属性
    Runnable runnable2 = () -> System.out.println("Hello world!");
    runnable2.run();

}
```

### 语法格式二 ###

有一个参数，并且无返回值`(x) -> System.out.println(x)`

```java
 private static void test2()
{
	//相当于实现了接口Consumer.accept()方法
    Consumer<String> consumer = (x) -> System.out.println(x);
    consumer.accept("hello world!");

}
```

### 语法格式三 ###

若只有一个参数，小括号可以省略不写`x -> System.out.println(x)`

```java
  private static void test2()
{
    Consumer<String> consumer = x -> System.out.println(x);
    consumer.accept("hello world!");

}
```

### 语法格式四 ###

有两个以上的参数，有返回值，并且 `Lambda `体中有多条语句

```java
 private static void test3()
{
    Comparator<Integer> comparator = (x, y) -> {
        System.out.println("函数式接口");
        return Integer.compare(x, y);
    };
}
```

### 语法格式五 ###

若 `Lambda `体中只有一条语句， `return `和 大括号 都可以省略不写

```java
private static void test4()
{
    Comparator<Integer> com = (x , y) -> Integer.compare(x , y);

}
```

### 语法格式六 ###

`Lambda` 表达式的参数列表的数据类型可以省略不写，因为`JVM`编译器通过上下文推断出，数据类型，即“类型推断”

```java
  private static void test4()
{
	//这里的类型可以忽略不写，就像下面的定义数组和方法一样
    Comparator<Integer> com = (Integer x , Integer y) -> Integer.compare(x , y);
}

 private static void test5()
{
	//数组后面的字符串不需要定义类型，JVM会推断出
    String[] strs = {"aaa","bbb","ccc"};

	//后ArrayList不需要定义类型
    List<String> list = new ArrayList<>();

	//方法的调用不需要写具体的类型
    show(new HashMap<>());
}

private static void show(Map<String,Integer> map){

}
```

> **类型推断:上述`Lambda`表达式中的参数类型都是由编译器推断得出的,`Lambda`表达式中无需指定类型，程序依然可以编译,这是因为`javac`根据程序的上下文,在后台推断出了参数的类型.`Lambda`表达式的类型依赖于上下文环境，是由编译器推断出来的。这就是所谓的"类型推断"**
>
> - 上联：左右遇一括号省
> - 下联：左侧推断类型省
> - 横批：能省则省
>
> `Lambda `表达式需要“函数式接口”的支持

# 函数式接口 #

## 什么是函数式接口 ##

- 只包含一个抽象方法的接口,称为**函数式接口**
- 你可以通过`Lambda`表达式来创建该接口的对象(若`Lambda`表达式抛出一个受检异常,那么该异常需要在目标接口的抽象方法上进行声明)
- 我们可以在任意函数式接口上使用**`@FunctionalInterface`**注解,这样做可以检查它是否是一个函数式接口,同时`javadoc`也会包含一条声明，说明这个接口是一个函数式接口

**函数式接口：接口中只有一个抽象方法的接口，称为函数式接口。 可以使用注解`@FunctionalInterface` 修饰可以检查是否是函数式接口**

```java
@FunctionalInterface
public interface MyFun {

    public Integer getValue(Integer num);
    
	//当定义多了一个方法时会提示错误
   //public Integer getNum(Integer num);
}
```

1. 需求1：对一个数进行运算

    ```java
    private static void test6()
    {
        Integer num = operation(100, x -> x * x);
        System.out.println(num);
        System.out.println(operation(100, x -> 200 + x));
    }
    
    private static Integer operation(Integer num, MyFun myFun)
    {
        return myFun.getValue(num);
    }
    ```

    调用`Collections.sort()`方法,通过定制排序比较两个`Employee`(先按年龄比,年龄相同按姓名比)，使用`Lamabda`作为参数传递

    ```java
    static List<Employee> emps = Arrays.asList(
            new Employee(101, "张三", 18, 9999.99),
            new Employee(102, "李四", 59, 6666.66),
            new Employee(103, "王五", 28, 3333.33),
            new Employee(104, "赵六", 8, 7777.77),
            new Employee(105, "田七", 38, 5555.55)
    );
    
    private static void test1(){
        Collections.sort(emps,(e1,e2) -> {
            if(e1.getAge() == e2.getAge())
            {
                return e1.getName().compareTo(e2.getName());
            }
            return Integer.compare(e1.getAge(),e2.getAge());
        });
    
        for (Employee employee:emps)
        {
            System.out.println(employee);
        }
    }
    /*
        Employee{id=104, name='赵六', age=8, salary=7777.77}
        Employee{id=101, name='张三', age=18, salary=9999.99}
        Employee{id=103, name='王五', age=28, salary=3333.33}
        Employee{id=105, name='田七', age=38, salary=5555.55}
        Employee{id=102, name='李四', age=59, salary=6666.66}
    */
    ```

2. 需求2
	1. 声明函数式接口,接口中声明抽象方法，`public String getValue(String str);`
	2. 声明类`TestLamabda`,类中编写方法使用接口作为参数,将一个字符串转成大写,并作为方法的返回值.
	3. 再将一个字符串的第2个和第4个索引位置进行截取子串
	
	```java
	@FunctionalInterface
	public interface MyFunction {
	
	    public String getValue(String str);
	}
	
	private static void test2()
	{
	    String str = show("Hello World", x -> x.toUpperCase());
	    System.out.println(str);
	    String substr = show("Hello World", x -> x.substring(2, 4));
	    System.out.println(substr);
	}
	
	private static String show(String str, MyFunction myFunction){
		return myFunction.getValue(str);
	}
	```

3. 需求3
	1. 声明一个带两个泛型的函数式接口,泛型类型为`<T,R> T`为参数,R为返回值
	2. 接口中声明对应抽象方法
	3. 在`TestLambda`类中声明方法,使用接口作为参数,计算两个`long`型参数的和
	4. 再计算两个`long`型参数的乘积	

		```java
		@FunctionalInterface
		public interface MyFunction2<T,R> {
		    public R getValue(T value1,T value2);
		}
		
		private static void test3()
		{
		    operation(100L,200L,(x,y) -> x+y);
		    operation(300L,5L,(x,y) -> x*y);
		}
		
		private static void operation(Long l1, Long l2, MyFunction2<Long,Long> myFunction2){
		    System.out.println(myFunction2.getValue(l1, l2));
		}
		```

> **作为参数传递`Lambda`表达式：为了将`Lambda`表达式作为参数传递,接收`Lambda`表达式的参数类型必须是与该`Lambda`表达式兼容的函数式接口的类型**

## 内置的四大核心函数式接口 ##

### `Consumer<T> ` ###

消费型接口` void accept(T t) `对类型为`T`的对象应用操作

```java
private static void test1()
{
    show("hello world!",x -> System.out.println(x));
}

private static void show(String str, Consumer<String> consumer)
{
    consumer.accept(str);
}
```

### `Supplier<T> ` ###

供给型接口 `T get()` 返回类型为T的对象

```java
private static void test2()
{
    List<Integer> numList = getNumList(10, () -> (int) (Math.random() * 100));
    System.out.println(numList);
}

private static List<Integer> getNumList(Integer num, Supplier<Integer> supplier)
{
    List<Integer> list = new ArrayList<>();
    for (int i = 0; i < num; i++) {
        Integer integer = supplier.get();
        list.add(integer);
    }
    return list;

}
```

### `Function<T, R> ` ###

函数型接口 `R apply(T t) `对类型为`T`的对象应用操作,并返回结果.结果是R类型的对象

```java
private static void test3()
{
    String s1 = handleStr("\t\t\t hello world!   ", x -> x.trim());
    System.out.println(s1);

    String s2 = handleStr("hello world!", x -> x.substring(2,6));
    System.out.println(s2);
}

private static String handleStr(String str , Function<String,String> function)
{
    return function.apply(str);
}
```

### `Predicate<T> ` ###

断言型接口 `boolean test(T t)` 确定类型为`T`的对象是否满足某约束,并返回`boolean`值

```java
    private static void test4()
    {
        List<String> list = Arrays.asList("aaa", "bbbb", "cccccc", "ddddddd");
        List<String> stringList = filterStr(list, x -> x.length() > 4);
        System.out.println(stringList);
    }

    private static List<String> filterStr(List<String> list, Predicate<String> predicate)
    {
        List<String> strList = new ArrayList<>();

        for (String str : list) {
            if(predicate.test(str)){
                strList.add(str);
            }
        }
        return strList;

    }
```

### `Runnable`

无参数,无返回值

```java
@FunctionalInterface
public interface Runnable {

    public abstract void run();
}
```

### 其它接口 ###

```java
BiFunction<T,U,R> 	params:T,U	return：R	//对类型为T,U参数应用操作,返回R类型的结果.包含方法为R apply<T t,U u>

//Function子接口
UnaryOperator<T> 	params:T	return T	//对类型为T的对象进行一元运算,并返回T类型的结果.包含方法为T apply(T t)

//BiFunction子接口
BinaryOperator<T>	params:T T	return T	//对类型为T的对象进行二元运算,并返回T类型的结果.包含方法为 T apply(T t1,T t2);

BiConsumer<T,U>		params:T,U	return void	//对类型为T,U参数应用操作.包含方法为void accept(T t,U u)

ToIntFunction<T>|ToLongFunction<T>|ToDoubleFunction<T>|		params:T	return int、long、doublue 	//分别计算为int\long\double值的函数

IntFunction<R>|LongFunction<R>|DoubleFunction<R> 	params：int|long|double	return：R	//参数分别int\long\double类型的函数
```

| 函数式接口名称 | 方法名称 |   参数   |  返回值  |
| :------------: | :------: | :------: | :------: |
|   `Runnable`   |  `run`   |  无参数  | 无返回值 |
|   `Function`   | `apply`  | 1个参数  | 有返回值 |
|   `Consume`    | `accept` | 1个参数  | 无返回值 |
|   `Supplier`   |  `get`   | 没有参数 | 有返回值 |
|  `BiConsumer`  | `accept` | 2个参数  | 无返回值 |

# 方法引用和构造器引用 #

当要传递给`Lambda`体的操作,已经有实现的方法了，可以使用方法引用！(实现抽象方法的参数列表，必须与方法引用的参数列表保持一致！)

方法引用:使用操作符`::`将方法名和对象 类的名字分隔开来

## 方法引用 ##

若 `Lambda `体中的功能，已经有方法提供了实现，可以使用方法引用 （可以将方法引用理解为 `Lambda` 表达式的另外一种表现形式）

- 对象的引用 `::` 实例方法名

  ```java
  /**System.out::println()方法的参数和返回类型必须与函数式接口Consumer的方法accept一样才可以这样使用**/
  public static void test1()
  {
  	/*新使用*/
      PrintStream printStream = System.out;
      Consumer<String> consumer = (str) -> printStream.println(str);
      consumer.accept("hello world!");
  
      System.out.println("==========");
  
      Consumer<String> consumer2 = printStream::println;
      consumer2.accept("hello java8");
  
  	/*原有使用*/
      Consumer<String> consumer3 = System.out::println;
  }
  ```

- 对象的引用 :: 实例方法名

  ```java
  /**Employee.getName()方法的参数和返回类型与Supplier.get()一致**/
  public static void test2()
  {
      Employee employee = new Employee(101, "李四", 18, 9999.99);
  
      /*原有使用*/
      Supplier<String> sup = () -> employee.getName();
      System.out.println(sup.get());
  
      System.out.println("=============");
  
      /*新使用*/
      Supplier<String> sup2 = employee::getName;
      System.out.println(sup2.get());
  }
  ```

- 对象的引用 :: 实例方法名

   ```java
   /**double max(double a, double b)与R apply(T t, U u)一致**/
    public static void test3()
   {
   	/*原有使用*/
       BiFunction<Double,Double,Double> biFunction = (x, y) -> Math.max(x, y);
       System.out.println(biFunction.apply(1.5, 22.2));
   
       System.out.println("==============");
   
   	/*新使用*/
       BiFunction<Double, Double, Double> biFunction2 = Math::max;
       System.out.println(biFunction.apply(1.2, 1.5));
   }
   ```

- 类名 :: 静态方法名

   ```java
   	/**Comparator int compare(T o1, T o2) |Integer static int compare(int x, int y)**/
   	 public static void test4()
       {
   		/*原有使用*/
           Comparator<Integer> comparator = (x, y) -> Integer.compare(x, y);
           System.out.println(comparator.compare(1, 2));
   
           System.out.println("===========");
   
   		/*新使用*/
           Comparator<Integer> comparator2 = Integer::compare;
           System.out.println(comparator2.compare(3, 4));
   
       }
   ```

- 类名 :: 实例方法名

	```java
	public static void test5()
	{
		/*原有使用*/
	    BiPredicate<String,String> biPredicate1 = (x, y) -> x.equals(y);
	    System.out.println(biPredicate1.test("abc", "abc"));
	
	    System.out.println("=============");
	
		/*新使用*/
	    BiPredicate<String, String> biPredicate2 = String::equals;
	    System.out.println(biPredicate2.test("efg", "efg"));
	
	    System.out.println("=============");
	
		/*原有使用*/
	    Function<Employee,String> function1 = (e) -> e.show();
	    System.out.println(function1.apply(new Employee()));
	
	    System.out.println("===============");
	
		/*新使用*/
	    Function<Employee, String> function2 = Employee::show;
	    System.out.println(function2.apply(new Employee()));
	}
	```

> 注意：
>
> 1. 方法引用所引用的方法的参数列表与返回值类型，需要与函数式接口中抽象方法的参数列表和返回值类型保持一致！
> 2. 若`Lambda `的参数列表的第一个参数，是实例方法的调用者，第二个参数(或无参)是实例方法的参数时，格式：` ClassName::MethodName`
>

## 构造器引用 ##

构造器的参数列表，需要与函数式接口中参数列表保持一致！

格式:**`ClassName::new`**

与函数式接口相结合,自动与函数式接口中方法兼容.可以把构造器引用赋值给定义的方法,与构造器参数列表要与接口中抽象方法的参数列表一致

-  **`类名 :: new`**

	```java
	/**构造函数的参数必须与函数式接口的参数一致**/
	public static void test6()
	{
		 /*新使用*/
	    Supplier<Employee> supplier1 = () -> new Employee();
	    System.out.println(supplier1.get());
	
	    System.out.println("===============");
	
		 /*新使用*/
	    Supplier<Employee> supplier2 = Employee::new;
	    System.out.println(supplier2.get());
	}
	
	public static void test7()
	{
	    /*新使用*/
	    Function<String, Employee> function = Employee::new;
	    /*新使用*/
	    BiFunction<String, Integer, Employee> biFunction = Employee::new;
	}
	```
	


## 数组引用 `type[]::new` ##

- `类型[] :: new;`

	```java
	public static void test8()
	{
		/*原有使用*/
	    Function<Integer,String[]> function1 = (length) -> new String[length];
	    String[] strs = function1.apply(10);
	    System.out.println(strs.length);
	    System.out.println("================");
	
		/*新使用*/
	    Function<Integer,Employee[]> function2 = Employee[]::new;
	    Employee[] employees = function2.apply(20);
	    System.out.println(employees.length);
	}
	```

# `Stream API` #

Java8中有两大最为重要的改变.第一个是`Lambda`;另外一个则是`Stream Api(java.util.strea.*)`

`Stream`是Java8中处理集合的关键抽象概念,它可以指定你希望对集合进行的操作,可以执行非常复杂的查找、过滤和映射数据等操作.使用`Stream API`对集合数据进行操作,就类拟于使用SQL执行的数据库查询.也可以使用`Stream API`来并行执行操作.简而言之,`Stream API`提供了一种高效且易于使用的处理数据的方式

## `Stream`  ##

流(`Stream`)到底是什么?

是数据渠道,用于操作数据源(集合、数组等)所生成的元素序列.“**集合讲的是数据，流讲的是计算**”

> **注意:**
>
> 1. `Stream`自己不会存储元素
> 2. `Stream`不会改变源对象.相反，他们会返回一个持有结果的新`Stream`
> 3. `Stream`操作是延迟执行的.这意味着它们会等到需要结果的时候才执行
>

## `Stream API` 的操作步骤 ##

1. **创建 `Stream`**：一个数据源(如：集合、数组)，获取一个流
2. **中间操作**:一个中间操作链,对数据源的数据进行处理
3. **终止操作(终端操作)**:一个终止操作,执行中间操作链,并产生结果

## 创建`Stream` ##

1. Java8中的`Collection`接中被扩展,提供了两个获取流的方法:

	```java
	default Stream<E> stream()	//返回一个顺序流
	default Stream<E> parallelStream()	//返回一个并行流
	```
2. 由数组创建流Java8中的`Arrays`的静态方法`stream()`可以获取数组流:

	```java
	static <T> Stream<T> stream(T[] array)	//返回一个流
	
	//重载形式,能够处理对应基本类型的数组:
	public static IntStream stream(int[] array)
	public static LongStream stream(long[] array)
	public static DoubleStream stream(double[] array)
	```
3. 由值创建流可以使用静态方法`Stream.of()`,通过显示值创建一个流.它可以接收任意数量的参数。

   ```java
   public static<T> Stream<T> of(T...values)	//返回一个流
   ```

4. 由函数创建流:创建无限流可以使用静态方法`Stream.iterate(`)和`Stream.generate()`,创建无限流

	```java
	//迭代
	public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)
	//生成
	public static<T> Stream<T> generate(Supplier<T> s)
	```

## 实际操作

```java
  private static List<Employee> emps = Arrays.asList(
          new Employee(102, "李四", 59, 6666.66),
          new Employee(101, "张三", 18, 9999.99),
          new Employee(103, "王五", 28, 3333.33),
          new Employee(104, "赵六", 8, 7777.77),
          new Employee(104, "赵六", 8, 7777.77),
          new Employee(104, "赵六", 8, 7777.77),
          new Employee(105, "田七", 38, 5555.55)
  );
```

```java
private static void test1()
{
    //1. Collection 提供了两个方法  stream() 与 parallelStream()
    List<String> list = new ArrayList<>();
    Stream<String> stream = list.stream();      //获取一个顺序流
    Stream<String> parallelStream = list.parallelStream();      //获取一个并行流

    //2. 通过 Arrays 中的 stream() 获取一个数组流
    Integer[] integers = new Integer[10];
    Stream<Integer> integerStream = Arrays.stream(integers);

    //3. 通过 Stream 类中静态方法 of()
    Stream<Integer> strem2 = Stream.of(1, 2, 3, 4, 5, 6, 7);

    //4. 创建无限流
    Stream<Integer> stream3 = Stream.iterate(0, x -> x + 2).limit(10);
    stream3.forEach(System.out::println);

    //生成
    Stream<Double> doubleStream = Stream.generate(Math::random).limit(10);
    doubleStream.forEach(System.out::println);
}
```

## `Stream`的中间操作 ##

多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作,否则中间操作不会执行任何的处理！而在终止操作时一次性全部处理,称为"惰性求值"

### 筛选与切片 ###

```java
	filter(Predicate p)		//接收Lambda,从流中排除某些元素
	distinct()				//筛选，通过流所生成元素的hashCode()和equals()去除重复元素
	limit(long maxSize)		//截断流,使其元素不超过给定数量
	skip(long n)			//跳过元素，返回一个扔掉了前n个元素的流.若流中元素不足n个，则返回一个空流.与limit(n)互补
```

例

```java
 //内部迭代：迭代操作 Stream API 内部完成
private static void test2()
{
    //所有的中间操作不会做任何的处理(这里不会有任何输出打印)
    Stream<Employee> stream = emps.stream().filter((e) -> {
        System.out.println("测试中间操作");
        return e.getAge() <= 35;
    });
    //只有当做终止操作时，所有的中间操作会一次性的全部执行，称为“惰性求值”
    stream.forEach(System.out::println);
}

	/*
	测试中间操作
	测试中间操作
	Employee{id=101, name='张三', age=18, salary=9999.99}
	测试中间操作
	Employee{id=103, name='王五', age=28, salary=3333.33}
	测试中间操作
	Employee{id=104, name='赵六', age=8, salary=7777.77}
	测试中间操作
	Employee{id=104, name='赵六', age=8, salary=7777.77}
	测试中间操作
	Employee{id=104, name='赵六', age=8, salary=7777.77}
	测试中间操作
	*/

 //外部迭代
private static void test3()
{
    Iterator<Employee> employeeIterator = emps.iterator();
    while (employeeIterator.hasNext())
    {
        System.out.println(employeeIterator.next());
    }
}

 private static void test4()
{
    emps.stream().filter((e) -> {
        System.out.println("短路!");
        return e.getSalary() >= 5000;
    }).limit(3).forEach(System.out::println);
}
/*
短路!
Employee{id=102, name='李四', age=59, salary=6666.66}
短路!
Employee{id=101, name='张三', age=18, salary=9999.99}
短路!
短路!
Employee{id=104, name='赵六', age=8, salary=7777.77}
*/

 private static void test5()
{
    emps.parallelStream().filter(e -> e.getSalary() >= 5000).skip(2).forEach(System.out::println);
}
/*
Employee{id=104, name='赵六', age=8, salary=7777.77}
Employee{id=105, name='田七', age=38, salary=5555.55}
Employee{id=104, name='赵六', age=8, salary=7777.77}
Employee{id=104, name='赵六', age=8, salary=7777.77}
*/
```

### 映射 ###

```java
map(Function f)		//接收一个函数作为参数,该函数会被应用到每个元素上,并将其映射成一个新的元素
mapToDouble(ToDoubleFunction f)		//接收一个函数作为参数,该函数会被应用到每个元素上,产生一个新的DoubleStream
mapToInt(ToIntFunction f)		//接收一个函数作为参数,该函数会被应用到每个元素上,产生一个新的IntStream
mapToLong(ToLongFunction f)		//接收一个函数作为参数,该函数会被应用到每个元素上，产生一个新的LongStream
flatMap(Function f)		//接收一个函数作为参数,将流中的每个值都换成另一个流,然后把所有流连接成一个流
```

例:

```java
	private static void test7()
    {
        Stream<String> stringStream = emps.stream().map(e -> e.getName());
        stringStream.forEach(System.out::println);

		/*
		李四
		张三
		王五
		赵六
		赵六
		赵六
		田七
		*/
        System.out.println("===========");

        List<String> stringList = Arrays.asList("aaa", "bbb", "ccc", "ddd");
        Stream<String> stream = stringList.stream().map(String::toUpperCase);
        stream.forEach(System.out::println);
		/*
		AAA
		BBB
		CCC
		DDD
		*/

        System.out.println("=============");
        Stream<Stream<Character>> strem2 = stringList.stream().map(TestStreamApi::filterCharacter);
        strem2.forEach(sm -> sm.forEach(System.out::println));
		/*
		a
		a
		a
		b
		b
		b
		c
		c
		c
		d
		d
		d
		*/

        System.out.println("===========");
        Stream<Character> strem3 = stringList.stream().flatMap(TestStreamApi::filterCharacter);
        strem3.forEach(System.out::println);
		/*
		a
		a
		a
		b
		b
		b
		c
		c
		c
		d
		d
		d
		*/
    }

    private static Stream<Character> filterCharacter(String str)
    {
        List<Character> list = new ArrayList<>();

        for(Character chr:str.toCharArray())
        {
            list.add(chr);
        }
        return list.stream();
    }
```

> **注意:`map()`和`flatMap()`的使用相当于集合的`add()`和`addAll()`操作一样**
>
> `map()`是把流中的各个集合元素传到新的流中,而`flatMap()`是把流中集合里各个元素传到新的流里,所以`map()`返回的是`Stream<Stream<Object>>`,而`flatMap()`直接是`Stream<Object>`
>
> ```java
>  private static void test8()
> {
>         List<String> stringList = Arrays.asList("aaa", "bbb", "ccc", "ddd");
>         ArrayList<Object> list = new ArrayList<>();
>         list.add("11");
>         list.add("22");
>         list.add(stringList);
>         System.out.println(list);
>         ArrayList<Object> list2 = new ArrayList<>();
>         list2.add("11");
>         list2.add("22");
>         list2.addAll(stringList);
>         System.out.println(list2);
> 
> 	//add()集合中还有集合,addAll()集合中只有各个元素
> 	/*
> 	[11, 22, [aaa, bbb, ccc, ddd]]
> 	[11, 22, aaa, bbb, ccc, ddd]
> 	*/
> }
> ```

### 排序 ###

```java
	sorted()	//产生一个新流，其中按自然顺序排序
	sorted(Comparator comp)		//产生一个新流,其中按比较器顺序排序
```

```java

   	/*
	sorted()——自然排序(Comparable)：按调用对象的实现好的compareTo()排序方法进行排序(例如：String)
	sorted(Comparator com)——定制排序(Comparator):按自定义的方法进行排序
	*/
    private static void test9()
    {
        List<String> stringList = Arrays.asList("aaa", "ddd", "bbb", "ccc");
        stringList.stream().sorted().forEach(System.out::println);

        System.out.println("==============");

        emps.stream().sorted((e1,e2) -> {
            if(e1.getAge() == e2.getAge())
            {
                return e1.getName().compareTo(e2.getName());
            }
            return Integer.compare(e1.getAge(),e2.getAge());
        }).forEach(System.out::println);
    }
```

## `Stream`终止操作 ##

终端操作会从流的流水线生成结果.其结果可以是任何不是流的值,例如:List、Integer,甚至是void

### 查找与匹配 ###

```java
	allMatch(Predicate p)		//检查是否匹配所有元素
	anyMatch(Predicate p)		//检查是否至少匹配一个元素
	noneMatch(Predicate p)		//检查是否没有匹配所有元素
	findFirst()				//返回第一个元素
	findAny()				//返回当前流中的任意元素
	count()					//返回流中元素总数
	max(Comparator c)		//返回流中最大值
	min(Comparator c)		//返回流中最小值
	forEach(Consumer c)		//内部迭代(Collection接口需要用户去做迭代,称为外部迭代.相反,Stream API使用内部迭代--它帮你把迭代做了)
```

**allMatch\anyMatch\noneMatch**

```java
  private static List<Employee> emps = Arrays.asList(
        new Employee(102, "李四", 59, 6666.66, Status.BUSY),
        new Employee(101, "张三", 18, 9999.99, Status.FREE),
        new Employee(103, "王五", 28, 3333.33, Status.VOCATION),
        new Employee(104, "赵六", 8, 7777.77, Status.BUSY),
        new Employee(104, "赵六", 8, 7777.77, Status.FREE),
        new Employee(104, "赵六", 8, 7777.77, Status.FREE),
        new Employee(105, "田七", 38, 5555.55, Status.BUSY)
	);

	private static void test1()
    {
        boolean b1 = emps.stream().allMatch(e -> e.getStatus().equals(Status.BUSY));
        System.out.println(b1);
        System.out.println("=============");
        boolean b2 = emps.stream().anyMatch(e -> e.getStatus().equals(Status.BUSY));
        System.out.println(b2);
        System.out.println("===============");
        boolean b3 = emps.stream().noneMatch(e -> e.getStatus().equals(Status.BUSY));
        System.out.println(b3);
        System.out.println("===============");
    }
	/*
	false
	=============
	true
	===============
	false
	===============
	*/

	 private static void test2()
    {
		//如果Double前加-是取反，取最大值
        Optional<Employee> op1 = emps.stream().sorted((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary())).findFirst();
        System.out.println(op1);
        System.out.println("==============");

		//这里使用的是并行流,因此是多条线程同时取最快唯一值
        Optional<Employee> op2 = emps.parallelStream().filter(e -> e.getStatus().equals(Status.FREE)).findAny();
        System.out.println(op2);
    }

	/*
	Optional[Employee{id=103, name='王五', age=28, salary=3333.33, status=VOCATION}]
	==============
	Optional[Employee{id=101, name='张三', age=18, salary=9999.99, status=FREE}]
	*/

   private static void test3()
	{	
		//计算个数
        long count = emps.stream().filter(e -> e.getStatus().equals(Status.FREE)).count();
        System.out.println(count);

        System.out.println("=================");

        Optional<Double> max = emps.stream().map(Employee::getSalary).max(Double::compare);
        System.out.println(max);

        System.out.println();

		//如果Double前加-是取反，取最大值
        Optional<Employee> min = emps.stream().min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
        System.out.println(min);
		
    }
	/*
	3
	=================
	Optional[9999.99]
	============
	Optional[Employee{id=103, name='王五', age=28, salary=3333.33, status=VOCATION}]
	*/
```

### 累计操作 ###

```java
	T reduce(T identity, BinaryOperator<T> accumulator);		//可以将流中元素反复结合起来,得到一个值.返回T
	Optional<T> reduce(BinaryOperator<T> accumulator);			//可以将流中元素反复结合起来,得到一个值.返回Optional<T>
	/**备注::map和reduce的连接通常称为map-reduce模式,因Google用它来进行网络搜索而出名**/


```

```java

	private static void test1()
    {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7,8,9,10);
		//第一次把0放到x，数组值1放到y，计算完把结果放到,值2放到y......进行累加
        Integer reduce = list.stream().reduce(0, (x, y) -> x + y);
        System.out.println(reduce);		//55

        System.out.println("===============");

        Optional<Double> op = emps.stream().map(Employee::getSalary).reduce(Double::sum);	//48888.84000000001
        System.out.println(op.get());
    }
```

> **注意:上面的返回类型不是`Optional`,是因为在`reduce`已经设置了初始值为0，结果是不会为`Null`值的,而下面的对象统计,因为对象有可能为空，固会以返回`Optional`类型处理`Null`值**

### `Collections` ###

在java stream中，通常需要将处理后的`stream`转换成集合类，这个时候就需要用到`stream.collect`方法。`collect`方法需要传入一个`Collector`类型，要实现`Collector`还是很麻烦的，需要实现好几个接口。

于是java提供了更简单的`Collectors`工具类来方便我们构建`Collector`。

```java
collect(Collector c)		//将流转换为其他形式.接收一个Collector接口的实现,用于给Stream中元素做汇总的方法
```

#### 聚合,分组

`Collector`接口中方法的实现决定了如何对流执行收集操作(如收集到`List`、`Set`、`Map`).但是`Collectors`实用类提供了很多静态方法,可以方便地创建常见收集器实例

```java
toList	List<T>		//把流中元素收集到List
```

```java
List<String> list = emps.stream().map(Employee::getName).collect(Collectors.toList());
list.forEach(System.out::println);
```

```
李四
张三
王五
赵六
赵六
赵六
田七
```

```java
toSet	Set<T>		//把流中元素收集到Set
```

```java
 emps.stream().map(Employee::getName).collect(Collectors.toSet()).forEach(System.out::println);
```

```
李四
张三
王五
赵六
田七
```

```java

public static <T, K, U>
    Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
                                    Function<? super T, ? extends U> valueMapper) {
        return toMap(keyMapper, valueMapper, throwingMerger(), HashMap::new);
    }

public static <T, K, U>
    Collector<T, ?, ConcurrentMap<K,U>>
    toConcurrentMap(Function<? super T, ? extends K> keyMapper,
                    Function<? super T, ? extends U> valueMapper,
                    BinaryOperator<U> mergeFunction) {
        return toConcurrentMap(keyMapper, valueMapper, mergeFunction, ConcurrentHashMap::new);
    }
```

```java
    List<String> list = Arrays.asList("jack", "bob", "alice", "mark");
   list.stream().collect(Collectors.toMap(Function.identity(), String::length)).entrySet().forEach(System.out::println);
```

```
bob=3
alice=5
mark=4
jack=4
```

```java
toCollection	Collection<T>	//把流中元素收集到创建的集合
```

```java
HashSet<String> hashSet = emps.stream().map(Employee::getName).collect(Collectors.toCollection(HashSet::new));
hashSet.forEach(System.out::println);
```

```
李四
张三
王五
赵六
田七
```

#### 数据统计

```java
	counting	Long		//计算流中元素的个数
	summingInt	Integer		//对流中元素的整数属性求和
	averagingInt	Double	//计算流中元素Integer属性的平均值
	summarizingInt		IntSummaryStatistics		//收集流中Integer属性的统计值.如:平均值
	maxBy		Optional<T>		//根据比较器选择最大值
	minBy		Optional<T>		//根据比较器选择最小值
```

```java
	 private static void test4()
    {
		//maxBy
        Optional<Double> max = emps.stream().map(Employee::getSalary).collect(Collectors.maxBy(Double::compareTo));
        System.out.println(max.get());	//9999.99

        System.out.println("==============");
	
		//minBy
        Optional<Employee> op = emps.stream().collect(Collectors.minBy((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary())));
        System.out.println(op.get());	//Employee(id=103, name=王五, age=28, salary=3333.33)

        System.out.println("================");

		//summingDouble
        Double sum = emps.stream().collect(Collectors.summingDouble(Employee::getSalary));
        System.out.println(sum);
        //DoubleSummaryStatistics{count=7, sum=48888.840000, min=3333.330000, average=6984.120000, max=9999.990000}

        System.out.println("================");

		//averagingDouble
        Double avg = emps.stream().collect(Collectors.averagingDouble(Employee::getSalary));
        System.out.println(avg);

        System.out.println("==================");

		//counting
        Long count = emps.stream().collect(Collectors.counting());
        System.out.println(count);

        System.out.println("==============");

		//summarizingDouble
        DoubleSummaryStatistics dss = emps.stream().collect(Collectors.summarizingDouble(Employee::getSalary));
        System.out.println(dss.getMax());
    }
```

#### 分组

```java
	groupingBy		Map<K,List<T>>		//根据某属性值对流分组,属性为K,结果为V
```

```java
    //分组
    private static void test5()
    {
        Map<Status, List<Employee>> map = emps.stream().collect(Collectors.groupingBy(Employee::getStatus));
        System.out.println(map);
    }

    //多级分组(可以按多个属性无限级分下去)
    private static void test6()
    {
        Map<Status, Map<String, List<Employee>>> map = emps.stream().collect(Collectors.groupingBy(Employee::getStatus, Collectors.groupingBy(e -> {
            if (e.getAge() >= 60) {
                return "老年";
            } else if (e.getAge() >= 35) {
                return "中年";
            } else {
                return "成年";
            }
        })));
        System.out.println(map);
    }
```

```java
partitioningBy		Map<Boolean,List<T>>		//根据true或false进行分区
```

```java
	 //分区
    private static void test7()
    {
        Map<Boolean, List<Employee>> map = emps.stream().collect(Collectors.partitioningBy(e -> e.getSalary()>= 5000));
        System.out.println(map);
    }
```

#### 链接数据

```java
joining		//把对象里的字符串取出进行拼接
```

```java
	 private static void test8()
    {
        String collect = emps.stream().map(Employee::getName).collect(Collectors.joining(",", "---", "==="));
        System.out.println(collect);/**---李四,张三,王五,赵六,赵六,赵六,田七===**/
    }
```

#### 聚合后操作

```java
reducing	归约产生的类型		//从一个作为累加器的初始值开始,利用BinaryOperator与流中元素逐个结合,从而归约成单个值
```

```java
	 private static void test9()
    {
        Optional<Double> sum = emps.stream().map(Employee::getSalary).collect(Collectors.reducing(Double::sum));
        System.out.println(sum);	//Optional[48888.84000000001]
         
         
         
       emps.stream()
        .collect(
            Collectors.groupingBy(
                Employee::getStatus, Collectors.reducing(BinaryOperator.minBy(comparingDouble))))
        .entrySet()
        .forEach(System.out::println);
         
         /**
         
         VOCATION=Optional[Employee(id=103, name=王五, age=28, salary=3333.33, status=VOCATION)]
         BUSY=Optional[Employee(id=105, name=田七, age=38, salary=5555.55, status=BUSY)]
         FREE=Optional[Employee(id=104, name=赵六, age=8, salary=7777.77, status=FREE)]
         
         **/
         
         
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
      System.out.println(
        list.stream()
            .collect(
                Collectors.reducing(
                    1,
                    x -> x + 1,
                    (result, b) -> {
                      System.out.println(result + "---" + b);
                      return result * b;
                    })));
         
         /**
                1---2
                2---3
                6---4
                24---5
                120---6
                720---7
                5040---8
                40320---9
                362880---10
                3628800---11
                39916800
         **/
    }
```

#### 操作链

```java
/**利用这方法也可以得到上面summingDouble之类所有方法的效果**/
collectiogAndThen		转换函数返回的类型		//包裹另一个收集器,对其结果转换函数
```

```java
  public void test9()
  {
    Double average = emps.stream().collect(Collectors.collectingAndThen(Collectors.averagingDouble(Employee::getSalary), Double::new));

    System.out.println(average);	//6984.120000000001

    Double sum = emps.stream().collect(Collectors.collectingAndThen(Collectors.summingDouble(Employee::getSalary), Double::new));
    System.out.println(sum);		//48888.840000000004

  }
```

## 举例1

```java
/*给定一个数字列表，如何返回一个由每个数的平方构成的列表呢？
	，给定【1，2，3，4，5】， 应该返回【1，4，9，16，25】。*/
private static void test1()
{
    Integer[] integers = {1, 2, 3, 4, 5};
   Arrays.stream(integers).map(x -> x * x).forEach(System.out::println);
}

/*怎样用 map 和 reduce 方法数一数流中有多少个Employee呢？*/
private static void test2()
{
    Optional<Integer> count = emps.stream().map(e -> 1).reduce(Integer::sum);
    System.out.println(count);
}
```

## 举例2 ##

```java
public class TestStreamAPI2 {

    private static List<Transaction> transactions = Arrays.asList(
            new Transaction(new Trader("Brian", "Cambridge"), 2011, 300),
            new Transaction(new Trader("Raoul", "Cambridge"), 2012, 1000),
            new Transaction(new Trader("Raoul", "Cambridge"), 2011, 400),
            new Transaction(new Trader("Mario", "Milan"), 2012, 710),
            new Transaction(new Trader("Mario", "Milan"), 2012, 700),
            new Transaction(new Trader("Alan", "Cambridge"), 2012, 950)

    );

    //1. 找出2011年发生的所有交易， 并按交易额排序（从低到高）
    private static void test1()
    {
        transactions.stream().filter(t -> t.getYear() == 2011).sorted((t1,t2) -> Integer.compare(t1.getValue(),t2.getValue())).forEach(System.out::println);
    }

    //2. 交易员都在哪些不同的城市工作过？
    private static void test2()
    {
        transactions.stream().map(t -> t.getTrader().getCity()).distinct().forEach(System.out::println);
    }

    //3. 查找所有来自剑桥的交易员，并按姓名排序
    private static void test3()
    {
        transactions.stream()
            .map(Transaction::getTrader)
            .filter(trader -> trader.getCity().equals("Cambridge"))
            .sorted((t1, t2) -> t1.getName().compareTo(t2.getCity()))
            .distinct()
            .forEach(System.out::println);
    }

    //4. 返回所有交易员的姓名字符串，按字母顺序排序
    private static void test4()
    {
        /*方法一*/
        transactions.stream()
                .map(t -> t.getTrader().getName())
                .sorted()
                .forEach(System.out::println);

        System.out.println("====================");

        /*方法二*/
        String str = transactions.stream()
                .map(t -> t.getTrader().getName())
                .sorted()
                .reduce("", String::concat);
        System.out.println(str);

        System.out.println("===============");

        /*方法三(不区分大小写排序)*/
        transactions.stream()
                .map(t -> t.getTrader().getName())
                .flatMap(TestStreamAPI2::filterCharacter)
                .sorted((s1,s2) -> s1.compareToIgnoreCase(s2))
                .forEach(System.out::println);
    }
    
  private static Stream<String> filterCharacter(String str) {
    ArrayList<String> list = new ArrayList<>();
    for (Character ch : str.toCharArray()) {
      list.add(ch.toString());
    }
    return list.stream();
  }

      // 5. 有没有交易员是在米兰工作的？
      private static void test5() {
        boolean b = transactions.stream().anyMatch(x -> x.getTrader().getCity().equals("Milan"));
        System.out.println(b);
      }

      // 6. 打印生活在剑桥的交易员的所有交易额
      private static void test6() {
        transactions.stream()
            .filter(x -> x.getTrader().getCity().equals("Cambridge"))
            .map(Transaction::getValue)
            .forEach(System.out::println);
      }

      // 7. 所有交易中，最高的交易额是多少
      private static void test7() {
        Optional<Integer> max = transactions.stream().map(x -> x.getValue()).max(Integer::compareTo);
        System.out.println(max.get());
      }

      // 8. 找到交易额最小的交易
      private static void test8() {
        Optional<Transaction> min =
            transactions.stream().min((t1, t2) -> Integer.compare(t1.getValue(), t2.getValue()));
        System.out.println(min);
      }
}
```

## 并行流与串行流 ##

并行流就是把一个内容分成多个数据块,并用不同的线程分别处理每个数据块的流。

Java8中将并行进行了优化,我们可以很容易的对数据进行并行操作.Stream API可以声明性地通过parallel()与sequential()在并行流与顺序流之间进行切换

## Fork/Join框架 ##

了解Fork/Join框架:就是在必要的情况下,将一个大任务,进行拆分(fork)成若干个小任务(拆到不可再拆时),再将一个个的小任务运算的结果进行join汇总

![](http://www.dxb02.top/photos/java8/01.png)

### Fork/Join框架与传统线程池的区别 ###

采用"工作窃取"模式(work-stealing)：当执行新的任务时它可以将其拆分分成更小的任务执行,并将小任务加到线程队列中，然后再从一个随机线程的队列中偷一个并把它放在自己的队列中.

相对于一般的线程池实现,fork/join框架的优势体面在对其中包含的任务的处理方式上.在一般的线程池中，如果一个线程正在搪行的任务由于某些原因无法继续运行,那么该线程会处于等待状态。而在fork/join框架实现中，如果某个子问题由于等待另外一个子问题的完成而无法继续运行.那么处理该子问题的线程会主动寻找其它尚未运行的子问题.这种试减少了线程的等待时间,提高了性能

```java
/*RecursiveAction没返回值,RecursiveTask有返回值*/
public class ForkJoinCalculate extends RecursiveTask<Long> {
    private long start;
    private long end;

    private static final long THRESHOLD = 10000L;   //临界值

    public ForkJoinCalculate(long start, long end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        long lenght = end - start;

        if (lenght <= THRESHOLD)
        {
            long sum = 0;
            for (long i = start; i <= end; i++) {
                sum+=i;
            }
            return sum;
        } else {
			/*左右拆分计算后再合并*/
           long middle =  (start + end) /2;
            ForkJoinCalculate left = new ForkJoinCalculate(start, middle);
            left.fork();
            ForkJoinCalculate right = new ForkJoinCalculate(middle + 1, end);
            right.fork();
            return left.join()+right.join();
        }

    }
}
```


```java
    private static void test1()
    {
        long start = System.currentTimeMillis();

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Long> task = new ForkJoinCalculate(0L, 10000000000L);
        Long sum = forkJoinPool.invoke(task);
        System.out.println(sum);

        long end = System.currentTimeMillis();
        System.out.println("耗费的时间为: " + (end - start));

		/*
			-5340232216128654848
			耗费的时间为: 2627
		*/
    }

    private static void test2()
    {
        long start = System.currentTimeMillis();

        long sum = 0L;

        for (long i = 0L; i < 10000000000L; i++) {
            sum+=i;
        }
        System.out.println(sum);
        long end = System.currentTimeMillis();
        System.out.println("耗费的时间为: " + (end - start));

		/*
			-5340232216128654848
			耗费的时间为: 3221
		*/
    }

    private static void test3()
    {
        long start = System.currentTimeMillis();

        long sum = LongStream.rangeClosed(0L, 10000000000L)
                .parallel()
                .sum();
        System.out.println(sum);

        long end = System.currentTimeMillis();
        System.out.println("耗费的时间为: " + (end - start));
		/*
			-5340232216128654848
			耗费的时间为: 1665
		*/
    }
```

# `Optional`类 #

`Optional<T>`类(`java.util.Optional`)是一个容器类，代表一个值存在或不存在,原来用`null`表示一个值不存在,现在`Optional`可以更好的表达这个概念.并且可以避免空指针异常

```java
Optional 容器类：用于尽量避免空指针异常
Optional.of(T t) : 创建一个 Optional 实例
Optional.empty() : 创建一个空的 Optional 实例
Optional.ofNullable(T t):若 t 不为 null,创建 Optional 实例,否则创建空实例
isPresent() : 判断是否包含值
orElse(T t) :  如果调用对象包含值，返回该值，否则返回t
orElseGet(Supplier s) :如果调用对象包含值，返回该值，否则返回 s 获取的值
map(Function f): 如果有值对其处理，并返回处理后的Optional，否则返回 Optional.empty()
flatMap(Function mapper):与 map 类似，要求返回值必须是Optional
```

```java

private static void test1()
{
    /*创建Optional实例的Employee泛型*/
  Op  Optional<Employee> optional = Optional.of(new Employee());
    Employee employee = optional.get();
    //Employee(id=null, name=null, age=null, salary=null, status=null)
    System.out.println(employee);	

    /*传入参数为null，抛出NullPointerException*/
    Optional<Employee> optional = Optional.of(null);
    System.out.println(optional.get());

  /*抛出NoSuchElementException*/
   Optional<Employee> op = Optional.empty();
    System.out.println(op.get());

  /*
  public static <T> Optional<T> ofNullable(T value) {
    return value == null ? empty() : of(value);
   }
    */
    Optional<Employee> op2 = Optional.ofNullable(null);
    System.out.println(op2.get());

}

private static void test2()
{
    /*创建一个空的Employee对象*/
    Optional<Employee> optional = Optional.ofNullable(new Employee());
    /*判断是否有值*/
    if(optional.isPresent()) {
        System.out.println(optional.get());
    }

    /*创建一个空对象*/
    Optional<Employee> op = Optional.empty();
    /*如果Optional没值则取新值*/
    Employee employee = op.orElse(new Employee("李四"));
    //Employee(id=null, name=李四, age=null, salary=null, status=null)
    System.out.println(employee);

    /*参数是函数式接口Supplier的供给型接口:接口不接受返回值,可以在里面创建任意对象*/
    Employee employee2 = op.orElseGet(() -> new Employee("张三"));
    //Employee(id=null, name=张三, age=null, salary=null, status=null)
    System.out.println(employee2);
}

private static void test3()
{

    Optional<Employee> optional = Optional.of(new Employee(101, "张三", 18, 9999.99));

    /*参数是函数式接口Function（类名 :: 实例方法名）*/
    Optional<String> op = optional.map(Employee::getName);
    System.out.println(op.get());	//张三

    /*参数是函数式接口Function,但要求使用Optional进行包装*/
    Optional<Integer> op2 = optional.flatMap(e -> Optional.of(e.getId()));
    System.out.println(op2.get());	//101
}
```

举例

```java
/*用以前的方法需要在方法里多重判断*/
private static void test4()
{
    Man man = new Man();
    String teacherName = getTeacherName(man);
    System.out.println(teacherName);

}

private static String getTeacherName(Man man)
{
    if (man != null)
    {
        Teacher teacher = man.getTeacher();
        if (teacher != null)
        {
            return teacher.getName();
        }
    }
    return "苍老师";
}

/*使用Optional进行包装传值,如果为null值则返回定义默认的*/
private static void test5()
{
    Optional<Teacher> optionalTeacher = Optional.ofNullable(new Teacher("三上老师"));
    Optional<NewMan> optionalNewMan = Optional.ofNullable(new NewMan(optionalTeacher));
    String teacherName = getTeacherName2(optionalNewMan);
    System.out.println(teacherName);
}

private static String getTeacherName2(Optional<NewMan> man){
    return man.orElse(new NewMan())
            .getTeacher()
            .orElse(new Teacher("苍老师"))
            .getName();
}
```

# 接口中的默认方法与静态方法 #

Java8中允许接口中包含具有具体实现的方法,该方法称为"默认方法"，默认方法使用`default`关键字修饰

## 接口中的默认方法 ##

接口默认方法的"类优先"原则

若一个接口定义了一个默认方法,而另外一个父类或接口中又定义了一个同名的方法时

- 选择父类中的方法.如果一个父类提供了具体的实现,那么接口中具有相同名称和参数的默认方法会被忽略
- 接口冲突.如果一个父接口提供一个默认方法,而另一个接口也提供了一个具有相同名称和参数列表的方法(不管方法是否是默认方法)，那么必须覆盖该方法来解决冲突

	```java
	/**在接口里定义default默认方法**/
	public interface MyInterface {
	    default String getName()
	    {
	        return "this is myinterface";
	    }
	
	/**Java8中,接口中允许添加静态方法**/
	    public static void show()
	    {
	        System.out.println("this is static method in interface");
	    }
	
	}
	
	/**定义另外一个接口和默认方法**/
	public interface MyFun {
	    default String getName()
	    {
	        return "this is myfun";
	    }
	}
	
	/**定义一个父类**/
	public class MyClass {
	    public String getName()
	    {
	        return "this is myclass";
	    }
	}
	
	/**
	接口默认方法的"类优先"原则,子类继承的父类已经实现了接口里的方法,会优先使用父类的实现方法
	**/
	public class SubClass extends MyClass implements MyFun,MyInterface{
	}
	
	public class TestDefaultInterface {
	    public static void main(String[] args) {
	        SubClass subClass = new SubClass();
	        System.out.println(subClass.getName());	//this is myclass
			
			/**接口里的静态方法也可以直接调用**/
		   MyInterface.show();	//this is static method in interface
	    }
	}
	```


```java
	/**如果实现多个接口都有同一个默认方法,使用super选择优先使用**/
	public class SubClass /*extends MyClass*/ implements MyFun,MyInterface{
	    @Override
	    public String getName() {
	        return MyInterface.super.getName();		//this is myinterface
	    }
	}
```

# 新时间日期API #

```java
	/**旧的时间日期API会有线程安全问题，执行会报错**/
   public static void main(String[] args) throws Exception{
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyyMMdd");

        Callable<Date> callable = new Callable<Date>() {
            @Override
            public Date call() throws Exception {
                return simpleDateFormat.parse("20191009");
            }
        };

        ExecutorService executorService = Executors.newFixedThreadPool(10);
        List<Future<Date>> results = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            results.add(executorService.submit(callable));
        }

        for (Future<Date> future:results)
        {
            System.out.println(future.get());
        }
        executorService.shutdown();
    }

	  //旧版本解决多线程安全问题
   public static void main(String[] args) throws Exception {

       Callable<Date> callable = new Callable<Date>() {
           @Override
           public Date call() throws Exception {
               return DateFormatThreadLocal.convert("20191009");	//??groovy.json.DateFormatThreadLocal???
           }
       };
       ExecutorService executorService = Executors.newFixedThreadPool(10);
       List<Future<Date>> results = new ArrayList<>();
       for (int i = 0; i < 10; i++) {
           results.add(executorService.submit(callable));
       }

       for (Future<Date> future:results)
       {
           System.out.println(future.get());
       }
       executorService.shutdown();

   }

	/**关于ThreadLocal解决线程安全问题不在这里讨论,另行查看JUC**/
	public class DateFormatThreadLocal {

	    private static final ThreadLocal<DateFormat> df = new ThreadLocal<DateFormat>(){
	        @Override
	        protected DateFormat initialValue() {
	            return new SimpleDateFormat("yyyyMMdd");
	        }
	    };
	    
      public static final Date convert(String source) throws Exception {
        return df.get().parse(source);
      }
    }
    
  /** JAVA8解决办法* */
  public static void main(String[] args) throws Exception {
    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyyMMdd");
    Callable<LocalDate> callable =
        new Callable<LocalDate>() {
          @Override
          public LocalDate call() throws Exception {
            return LocalDate.parse("20191009", dateTimeFormatter);
          }
        };

    ExecutorService executorService = Executors.newFixedThreadPool(10);
    List<Future<LocalDate>> results = new ArrayList<>();
    for (int i = 0; i < 10; i++) {
      results.add(executorService.submit(callable));
    }

    for (Future<LocalDate> future : results) {
      System.out.println(future.get());
    }
    executorService.shutdown();
  }
​
```

## `LocalDate`、`LocalTime`、`LocalDateTime` ##

**`LocalDate`**、**`LocalTime`**、**`LocalDateTime`**类的实例是不可变的对象,分别表示使用ISO-8601日历系统的日期、时间、日期和时间。它们提供了简单的日期或时间，并不包含当前的时间信息.也不包含与时区相关的信息

```java
now()	//静态方法,根据当前时间创建对象
of()	//静态方法,根据指宝日期/时间创建对象
plusDays,plusWeeks,plusMonths,plusYears		//向当前LocalDate对象添加几天、几周、几个月、几年
minusDays,minusWeeks,minusMonths,minusYears		//从当前LocalDate对象减去几天、几周、几个月、几年
plus,minus		//添加或减少一个Duration或Period
withDayOfMonth,withDayOfYear,withMonth,withYear		//将月份天数、年份天数、月份、年份改为指定的值并返回新的LocalDate对象
getDayOfMonth		//获得月份天数(1-31)
getDayOfYear		//获得年份天数(1-366)
getDayOfWeek		//获得星期几(返回一个DayOfWeek枚举值)
getMonth			//获得月份,返回一个Month枚举值
getMonthValue		//获得月份(1-12)
getYear				//获得年份
until				//获得两个日期之间的Period对象,或者指定ChronUnits的数字
isBefore,isAfter	//比较两个LocalDate
isLeapYear			//判断是否闰年
```

```java
/**LocalDate,LocalTime,LocalDateTime之间的部分方法共有**/
 private static void test1()
{
    LocalDate now = LocalDate.now();
    System.out.println(now);	//2019-10-09

    LocalDateTime localDateTime = LocalDateTime.of(2019, 10, 9, 14, 31, 00);
    System.out.println(localDateTime);		//2019-10-09T14:31

    LocalDateTime localDateTime2 = localDateTime.plusYears(1);
    System.out.println(localDateTime2);		//2020-10-09T14:31

    LocalDateTime localDateTime3 = localDateTime.minusMonths(2);
    System.out.println(localDateTime3);		//2019-08-09T14:31

    System.out.println(localDateTime.getYear());	//2019
    System.out.println(localDateTime.getMonthValue());	//10
    System.out.println(localDateTime.getDayOfMonth());	//9
    System.out.println(localDateTime.getHour());		//14
    System.out.println(localDateTime.getMinute());		//31
    System.out.println(localDateTime.getSecond());		//0
}
```

## `Instant`时间戳 ##

用于"时间戳"的运算。它是以Unix元年(传统的设定为UTC时区1970年1月1日午夜时分)开始所经历的描述进行运算

```java
 private static void test2()
{
    Instant instant = Instant.now();
    System.out.println(instant);	//2019-10-09T06:44:05.737Z

	/**时区加8小时**/
    OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
    System.out.println(offsetDateTime);	//2019-10-09T14:44:05.737+08:00

	/**打印当前纳秒**/
    System.out.println(instant.getNano());	//737000000

	/**从1970-01-01 00:00:00加5秒**/
    Instant instant2 = Instant.ofEpochSecond(5);
    System.out.println(instant2);			//1970-01-01T00:00:05Z
}
```

## `Duration`和`Period` ##

- `Duration`:用于计算两个"时间"间隔
- `Period`:用于计算两个"日期"间隔

	```java
	private static void test3()
	{
	    Instant instant = Instant.now();
	
	    try {
	        Thread.sleep(1000);
	    } catch (InterruptedException e) {
	        e.printStackTrace();
	    }
	
	    Instant instant2 = Instant.now();
	
	    Duration between = Duration.between(instant, instant2);
	    System.out.println(between);		//PT1S
	    System.out.println(between.getSeconds());	//1
	
	    System.out.println("===============");
	
	    LocalDate localDate = LocalDate.of(2015, 10, 7);
	    LocalDate now = LocalDate.now();
	    Period period = Period.between(localDate, now);
	    System.out.println(period);		//P4Y2D
	    System.out.println(period.getYears());		//4
	    System.out.println(period.getMonths());		//0
	    System.out.println(period.getDays());		//2
	
	}
	```

## 日期的操纵 ##

- `TemporalAdjuster`:时间校正器,有时我们可能需要获取例如:将日期调整到"下个周日"等 操作
- `TemporalAdjusters`:该类通过静态方法提供了大量的常用`TemporalAdjuster`的实现。

	```java
	/**TemporalAdjusters是实现TemporalAdjuster的工具类**/
	private static void test4()
	{
	    LocalDateTime localDateTime = LocalDateTime.now();
	    System.out.println(localDateTime);		//2019-10-09T16:04:11.772
	
	    LocalDateTime localDateTime2 = localDateTime.withDayOfMonth(10);
	    System.out.println(localDateTime2);		//2019-10-10T16:04:11.772
	
	    LocalDateTime localDateTime3 = localDateTime.with(TemporalAdjusters.next(DayOfWeek.SUNDAY));
	    System.out.println(localDateTime3);		//2019-10-13T16:04:11.772
	
		/**TemporalAdjuster是函数式接口，因此可以自定义方法**/
	    //自定义：下一个工作日
	    LocalDateTime localDateTime4 = localDateTime.with(l -> {
	        LocalDateTime dateTime = (LocalDateTime) l;
	        DayOfWeek dayOfWeek = dateTime.getDayOfWeek();
	        if (dayOfWeek.equals(DayOfWeek.FRIDAY)) {
	            return dateTime.plusDays(3);
	        } else if (dayOfWeek.equals(DayOfWeek.SATURDAY)) {
	            return dateTime.plusDays(2);
	        } else {
	            return dateTime.plusDays(1);
	        }
	    });
	
	    System.out.println(localDateTime4);		//2019-10-10T16:04:11.772
	}
	```

## 解析与格式化 ##

`java.time.format.DateTimeFormatter`类:该类提供了三种格式化方法:

- 预定义的标准格式
- 语言环境相关的格式
- 自定义的格式

```java
  public void test12() {
    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ISO_LOCAL_DATE;

    LocalDateTime localDateTime = LocalDateTime.now();

    String date = localDateTime.format(dateTimeFormatter);

    System.out.println(date);	//2022-03-06

    System.out.println("================");

    DateTimeFormatter dateTimeFormatter2 = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");

    String date2 = localDateTime.format(dateTimeFormatter2);

    System.out.println(date2);	//2022年03月06日 16:46:18

    LocalDateTime localDateTime2 = LocalDateTime.parse(date2, dateTimeFormatter2);

    System.out.println(localDateTime2);		//2022-03-06T16:46:18
  }
```

## 时区的处理 ##

Java8中加入了对时区的支持,带时区的时间分别为:`ZonedDate`、`ZonedTime`、`ZonedDateTime`其中每个时区都对应着ID,地区ID都为"{区域}/{城市}"的格式例如:`Asia/Shanghai`等

- `ZoneId`:该类中包含了所有的时区信息
- `getAvailableZonelds()`:可以获取所有时区信息
- `of(id)`:用指定的时区信息获取`ZoneId`对象

与传统日期处理的转换

|                              类                              |                To遗留类                 |                          From遗留类                          |
| :----------------------------------------------------------: | :-------------------------------------: | :----------------------------------------------------------: |
|           `java.time.Instant`互转`java.util.Date`            |          `Date.from(instant)`           |      `Date date = new Date();`<br/>`date.toInstant();`       |
|           `java.util.Date`互转`java.sql.Timestamp`           |        `Timestamp.from(instant)`        | `Timestamp timestamp = new Timestamp();`<br/>`timestamp.toInstant()` |
|  `java.time.ZonedDateTime`互转`java.util.GregorianCalendar`  | `GregorianCalendar.from(zonedDateTime)` | `GregorianCalendar calendar = new GregorianCalendar();`<br/> `calendar.toZonedDateTime()` |
|           `java.time.LocalDate`互转`java.sql.Date`           |   `java.sql.Date.valueOf(localDate)`    | `java.sql.Date date = new java.sql.Date(); `<br/>`date.toLocalDate()` |
|           `java.time.LocalTime`互转`java.sql.Time`           |   `java.sql.Time.valueOf(localTime)`    |     `Time time = new Time(); `<br/>`time.toLocalTime()`      |
|      `java.time.LocalDateTime`互转`java.sql.Timestamp`       |   `Timestamp.valueOf(localDateTime)`    | `Timestamp timestamp = new Timestamp(); `<br/>`timestamp.toLocalDateTime()` |
|          `java.time.ZoneId`互转`java.util.TimeZone`          |       `Timezone.getTimeZone(id)`        | `TimeZone timeZone = new TimeZone();`<br/>` timeZone.toZoneId()` |
| `java.time.format.DateTimeFormatter`互转`java.text.DateFormatwithDayOfMonth`??????? |         `formatter.toFormat()`          |                              无                              |

```java
private static void test6()
{
	/**把支持的所有时区打印**/
    Set<String> set = ZoneId.getAvailableZoneIds();
    set.forEach(System.out::println);
}

/**指定时区获取日期时间**/
private static void test7()
{
    LocalDateTime now = LocalDateTime.now(ZoneId.of("Asia/Shanghai"));
    System.out.println(now);		//2019-10-09T17:06:49.740

    LocalDateTime now2 = LocalDateTime.now(ZoneId.of("US/Pacific"));
    System.out.println(now2);		//2019-10-09T02:06:49.742
}
```


# 重复注解与类型注解 #

Java8对注解处理提供了两点改进:可重复的注解及可用于类型的注解。

```java
/**@Repeatable可定义多个注解，没有这个注释的话定义多个注解会报错**/
@Repeatable(MyAnnotations.class)
/**TYPE_PARAMETER可用于类型的注解**/
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, TYPE_PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
    String value() default "test";
}

/**定义一个容器类，把多个注解装进数组**/
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotations {
    MyAnnotation[] value();
}

public class TestAnnotation {
	/**为防止参数为null值,类型注解在实际应用中的作用(可通过配置第三方插件Checker Framework在编译期进行检测)**/
    private  /*@NotNull*/ Employee employee = null;
    
    public static void main(String[] args) throws Exception{
		/**使用反射获取注解内容**/
        Class<TestAnnotation> testAnnotationClass = TestAnnotation.class;
        Method method = testAnnotationClass.getMethod("show");

        MyAnnotation[] annotationsByType = method.getAnnotationsByType(MyAnnotation.class);

       for (MyAnnotation s:annotationsByType)
        {
            System.out.println(s.value());
        }

    }

	/**要使用重复注解必须定义一个新的容器类(在Java8之前使用会报错)**/
    @MyAnnotation("hello")
    @MyAnnotation("world")
    public void show()	
    {
    }

	public static void get(@MyAnnotation("abc") String str)//通过新增的TYPE_PARAMETER类型，可通过@MyAnnotation("abc")来对参数进行标注
    {
        System.out.println(str);
    }
}
```
