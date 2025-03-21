- 类中调用类方法(static)可以忽略类名
- 实例方法传参省略this

# 类
## 继承

![[250310-192313724-Pasted image 20250310192311.png]]

## 方法重写

- @override 校验
![[250310-194512756-Pasted image 20250310194510.png]]
![[250310-194950376-Pasted image 20250310194949.png]]

- 私有的继承不了，静态的也不需要继承，所以可以解释为什么不重写
## 构造器

![[250310-195408655-Pasted image 20250310195407.png]]

- 没有会报错

![[250310-195609015-Pasted image 20250310195607.png]]

### 调用兄弟构造器

就是同一类的其他不同参数的构造器

super(), this()都必须写在构造器第一行且不能同时出现

![[250310-200438416-Pasted image 20250310200436.png]]

## 多态

对象多态、行为多态

- 方法：编译看左，运行看右
- 变量：都看左
解释：只规定了行为多态，变量没设计

- 多态下不能调用独有功能
解释：编译不能通过

- 可以进行强制类型转换解决，但是只要有继承关系就能编译通过，不一定能运行
- 建议用instanceof进行检查
```java
if(a1 instanceof Wolf){...}
```

## final

- 修饰类：不能被继承
- 修饰方法：不能被重写
- 修饰变量：有且仅能被赋值一次（称常量）
-

## 单例类

1. 把构造器私有
2. 定义一个类变量记住类的一个对象
3. 定义一个类方法返回该对象

"饿汉式"——早已创建
```java
public class A{
	private static A a = new A();
	private A(){}
	public static A getInstance(){return a;}
}
```

“懒汉式”——用时才开始创建
```java
public class B{
	private static B b;
	private B(){}
	public static B getInstance(){
		if(b == null){
			b = new B();
		}
		return b;
	}
}
```

## 枚举类

```java
public enum A{
	X,Y,Z;
}
```
switch 可以省略A.X的A.，写作X


## 抽象类

```java
public abstract class A{
	public abstract void show();
}
```

![[250311-204857175-Pasted image 20250311204855.png]]
![[250311-205110698-Pasted image 20250311205110.png]]

抽象方法实践

模板方法{
--相同内容--
--不同内容--
--相同内容--
}
不同内容设置为抽象函数去实现即可实现

## 接口

```java
public interface 接口名{
	//成员变量(都是常量)
	//成员方法(都是抽象方法)
}
```

- 用implement，事项的类必须重写接口的全部抽象方法，或是抽象类
- 接口只规范

jdk9之后

```java
public interface A{
	//普通实例方法，必须加default修饰默认用public
	//只能用接口实现类对象调用
	default void go(){...}
	//使用接口中的其他实例方法调用它
	private void run(){...}
	//默认会用public修饰，只能用当前接口名调用
	static void show(){...}
}
```

- 接口可以继承多个接口
```java
interface A{...}
interface B{...}
interface C extends A, B{...}
```
- 方法签名（同名）冲突时不支持多继承，也不支持实现（报错）
- 一个类实现了多个接口，多个接口存在同名默认方法，可以不冲突，重写即可

- 一个类继承了父类，又实现了接口，如果有同名默认方法，实现类优先用父类的

## 代码块

### 静态代码块

- 类加载时自动执行，自动执行一次
- 完成类的初始化，例如：对静态变量的初始化赋值

```java
static{...}
```

### 实例代码块


- 每次创建对象时，执行实例代码块，并在构造器前执行
- 和构造器一样，都完成对象的初始化，例如：对实例变量进行初始化
```java
{...}
```

## 内部类

定义在类的内部

### 成员内部类

- 无static修饰，属于外部类对象持有的
```java
public class Outer{
	public static String name="H";
	private int age=11;
	public class Inner{
		...
		public void show(){
			sout(name)
			sout(Outer.this.age)
		}
		...
	}
}
```

```java
Outer.Inner oi = new Outer().new Inner();
```

特点:
1. 成员内部类中可以直接访问外部类的静态成员，也可以直接访问外部类的实例成员
2. 成员内部类的实例方法中，可以直接拿到当前寄生的外部类对象： 外部类名.this（创建时已经创建有一个匿名的外部类对象）

### 静态内部类

- 有static修饰的内部类，属于外部类自己持有

```java
public class Outer{
	public static class Inner{...}
}
```


```java
Outer.Inner oi = new Outer().Inner();
```

1. 静态内部类可以直接访问外部类的静态成员
2. 静态内部类不可以直接访问外部类的实例成员（静态内部类不属于外部类的对象）

### 局部内部类

定义在方法中

### 匿名内部类

![[250312-163605479-Pasted image 20250312163603.png]]

- 用于更方便地创建一个子类对象

![[250312-164119823-Pasted image 20250312164117.png]]
被编译成一个子类

![[250312-164415125-Pasted image 20250312164413.png]]

# 函数式编程

## Lambda

![[250312-171339625-Pasted image 20250312171337.png]]

- 只能简化函数式接口的匿名内部类——只有一个抽象方法的接口
- 一般有@FuntionalInterface 注解
![[250312-172124215-Pasted image 20250312172122.png]]

![[250312-172930051-Pasted image 20250312172928.png]]

## 方法引用
### 静态方法引用

![[250312-174005158-Pasted image 20250312174004.png]]

### 实例方法引用

![[250312-193411945-Pasted image 20250312193411.png]]

### 特定类的方法引用

![[250312-195055331-Pasted image 20250312195054.png]]
### 构造器引用

![[250312-195747518-Pasted image 20250312195745.png]]

# 常用API

## String

### “xxx” 与 new String("xxx")

- "xxx"方式创建的字符串会放进常量池，相同的字符串会共用，地址相同
- new方式的会创建新的，每次都创建新的，地址不共用
- 实际上除了“”方式在常量池中，其他都会创建新的，用 == 对比都不相同，用eqauls()方法


### 常用方法

![[250312-201515911-Pasted image 20250312201515.png]]

## ArrayList

集合

![[250312-203335500-Pasted image 20250312203333.png]]

# 异常

- 方法可以抛异常，把方法内部的异常抛出
- 可以主动使用throws抛异常
```java
public static void main(){
	try{
		show();
	}
	catch (Exception e){
		e.printStackTrace()
	}

}


public static void show() throws Exception{
	...
	throw new ...;
	...
}
```

# 泛型

## 类
```java
修饰符 class 类名<类型变量, 类型变量, ...>{...}
```

## 接口

```java
public interface Data<T>{...}
```
## 方法

定义E的才是泛型方法
```java
public <E> E show(E e){...}
```

## 通配符

就是"?"

```java
ArrayList<?> cars
ArrayList<Xiaomi> cars
ArrayList<BYD> cars
ArrayList<Car>
```
- 可以在使用泛型时表示一切类型
- 即使Xiaomi 和 BYD都是Cars 的子类，但是使用泛型时不同


```java
? extends Car
? super Car
```
- 泛型上限必须是Car或其子类
- 泛型下限必须是Car或其父类

## 泛型支持的类型

- 泛型不支持基本数据类型，只能支持对象类型（引用数据类型）
不支持int double 这些基本类型
解释：泛型工作在编译阶段，编译后都会被擦除，所有类型会恢复成Object类型，而int不是类也就不是Object子类


## 包装类

- 把基本类型的数据包装成对象的类型

![[250313-172611448-Screenshot_20250313_172525_tv.danmaku.bili.png]]

### 手动包装

![[250313-173028628-Screenshot_20250313_172949_tv.danmaku.bili.png]]
- valueOf把-128到127的自动返回不会创建新的，所以地址相同，而范围外不同

### 自动装箱，自动拆箱

```java
Integer it = 121;
ArrayList<Integer> list = new ArrayList<>();
list.add(120);
list.add(130);
list.get(1)
```

### 包装类其他方法

![[250313-173557616-Capture_20250313_173454.jpg]]

# 集合体系结构

### Collection


![[250314-101917260-Pasted image 20250314101915.png]]

![[250314-102526917-Pasted image 20250314102524.png]]
- toArray()可以放参数，参数是数组，数组的类型指定转换后的类型，结果存储到该数组中

#### 遍历

![[250314-104158295-Pasted image 20250314104156.png]]


![[250314-105856064-Pasted image 20250314105854.png]]

- 本质是迭代器遍历

![[250314-110132853-Pasted image 20250314110131.png]]

#### 并发异常

#####  for
例如，for遍历的时删除元素会导致删不干净，索引指不对
- 删除后索引i--
- 倒着遍历，从list.size()-1 到 0

##### 迭代器

要用迭代器的remove方法，不要用集合的remove方法即可

##### 增强for和 forEach(lambda)

都不能解决
只适合做遍历，不适合修改

### List

![[250315-161614345-Capture_20250315_161600.jpg]]
对于collection来说多了索引

### ArrayList&LinkedList

两者的数据结构不同
- ArrayList基于数组存储数据（第一次创建长度默认10）
- LinkedList基于链表存储数据（双链表）

- 数组
	- 查询速度快（根据索引）
	- 增删数据效率低（后面数据要移动）
- 链表
	- 查询慢，都要从头找
	- 增删相对快

LinkedList 可以用来设计队列，有很多首尾操作
也可以设计栈


## Set

- 常用的方法基本都是Collection提供的，自己几乎没有新增的常用功能
- 实际上Set系列是基于Map实现的，只要键数据，不要值数据


### HashSet

![[250315-172907975-Capture_20250315_172825.jpg]]

- 基于哈希表存储数据的
- 第一次创建默认长度16
![[250315-173408168-Capture_20250315_173328.jpg]]

![[250315-173706698-Capture_20250315_173700.jpg]]


#### HashSet去重

![[250316-141041240-image.png]]

### LinkedHashSet

![[250316-144732489-image.png]]

### TreeSet

![[250317-133340026-Capture_20250317_133308.jpg]]


- treeset一定要能排序，默认升序
- 为实现排序有两者解决方法
	- 方法一
		- 对于自定义类型要实现Comparable接口，即重写compareTo
		- 一般x.compareTo(y)>0表示排序x>y
	- 方法二
		- 给集合传入 Comparator
- 优先会使用集合定义的Comparator
- 同样，对于Set，排序相等的会排除

### 总结
![[250317-135127690-Capture_20250317_135114.jpg]]

## Map

![[250317-135920178-Capture_20250317_135734.jpg]]


- 即python字典
- 一一对应，键key唯一，值value允许重复

### 常用方法

![[250317-140707742-Capture_20250317_140639.jpg]]

### 遍历方式

#### 键找值

![[250317-142051877-Capture_20250317_142041.jpg]]

![[250317-142327498-Capture_20250317_142316.jpg]]

#### 键值对

![[250317-143058363-Capture_20250317_142855.jpg]]

#### Lambda


![[250317-144517090-Capture_20250317_144505.jpg]]



## Stream流

流只能[[#收集方法|收集]]一次，像迭代器到头了就不能返回，

![[250317-145830067-Capture_20250317_145817.jpg]]

![[250317-151019245-Capture_20250317_151010.jpg]]

### 获取Stream流
![[250317-151358999-Capture_20250317_151349.jpg]]

### 常用方法

![[250317-152345580-Capture_20250317_152325.jpg]]
### 终结方法

![[250317-154205579-Capture_20250317_154145.jpg]]

- max和min可以用get方法获得内容


### 收集方法

![[250317-201512607-image.png]]

collector 和 collection不一样，collector用于指定collect方法用来生成的指定集合

### 可变参数


```java
public void sum(int...nums){...}
```

- nums是数组
- 可变参数只需一个，类型也不行
- 可变参数只能放后面

### Collections 工具类

![[250319-111527903-Capture_20250319_111457.jpg]]

# File, IO流

## File

![[250319-191820312-image.png]]

### 常用方法

![[250319-191847736-image.png]]

- 相对路径是从工程目录开始
- 可以创建不存在的文件路径

![[250319-192937206-image.png]]

![[250319-193215145-image.png]]![[250319-193720460-image.png]]

### 字符集

![[250319-201424598-image.png]]

![[250319-202318250-image.png]]

## IO流

一般都有框架封装，能看懂即可

![[250319-202954737-image.png]]



![[250319-203143941-image.png]]
### IO流体系

![[250319-203413126-image.png]]

### 字节流
#### FileInputStream


![[250319-203504473-image.png]]

>读一个字节

![[250319-204127728-image.png]]
- 编写风格固定
- 读中文一定乱码

>读多个字节

![[250319-204745836-image.png]]


上述两者都不能避免汉字乱码问题，解决方法应该是用字符流，或是定义与文件一样大的buffer数组去读，只适合小文件

![[250319-205135608-image.png]]

#### FileOutputStream

![[250319-205540770-image.png]]

没有append是覆盖，有是追加

![[250319-205748231-image.png]]

流使用完了最好关掉，每个流都有close方法

#### 文件复制

![[250319-210629777-image.png]]

#### 资源释放

出bug时有可能不会按顺序执行到最后，close可能不被执行

![[250319-211154354-image.png]]

- 比较完美的做法
- 在代码块外面创建流，保证finally能访问到
- 最后close也可能报错，也做了try，判断了空指针
- 但是很臃肿，用以下方法

![[250319-211601258-image.png]]


- jdk7后
- 括号中只能放资源

### 字符流

#### FileReader

 ![[250319-215054347-image.png]]


#### FileWriter

![[250319-220109093-image.png]]

![[250319-220438195-image.png]]

- 先写到内存，再写到文件
- 如果没刷新，内存的数据会随程序清空，还没写的数据就丢失了