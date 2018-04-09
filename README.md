# Thinking-In-Java-Read-Record
## Java 编程思想读书笔记
### Chapter 2 一切都是对象

* 方法名和参数列表被称为“方法签名”，他们可以唯一地标识一个方法。唯一性与方法的返回值无关。因为当调用方法不考虑方法的返回值时（只是为了调用方法而产生的效果），两个不同返回值的方法在代码层面是完全相同的。
    ```
    # 方法定义
    void methodA() {// 1
      // do something
    }

    int methodA() {// 2
      // dosomething diff
    }
    # 方法调用时，代码完全相同
      object.methodA();// invoke "1"
      object.methodA();// invoke "2"
    ```
* 为了避免类名称重复，使用小写、倒叙的域名作为包名前缀。
* `java.lang` 是默认导入每个`java`文件中的，它的所有类可以被直接使用。
* `main()`方法中的`args`参数用于存储命令行参数。

### Chapter 5 初始化与清理
* 在使用`new`关键字穿件对象时，会为对象分配存储空间，并调用相应构造器。构造器用于对象的创建和初始化。
* 成员变量的初始化总是在构造器初始化之前，不管成员变量和构造器声明的顺序如何。
* 与静态代码块对应的，有非静态代码块，只是非静态代码块在对象的每一次初始化时都会被调用！
* 当存在多个可变参数方法重载时，无法调用0个参数的该方法
    ```
    # 存在两个可变参方法的重载
    public void method(int... ints) {
        // do something
    }
    public void method(float... floats) {
        // do something
    }
    # 调用时，不可调0个参数的情况（尽管可变参数方法可以接受0个参数）
    obj.method();// not allowed! ambiguous call
    ```
* 创建 enum（枚举）类时，编译器会自动添加一些有用的特性（方法）：
    ```
    * toString(): 获得该枚举的名称;
    * ordinal(): 获得当前枚举对象在枚举类中的顺序（位置）,基于0;
    * static values(): 获得该枚举类中所有枚举对象的数组.
    ```
### Chapter 6 访问权限控制

### Chapter 7 复用类

### Chapter 9 接口
* 任何抽象性都应该是应真正的需求而产生的。当必须时，应该重构接口而不是到处添加额外级别的间接性，并由此带来的额外的复杂性。恰当的原则应该是优先选择类而不是接口。从类开始，如果接口的必须性变得非常明确，那么就进行重构。接口是一种重要的工具，但是它们容易被滥用。

### Chapter 10 内部类
* 当某个外部类的对象创建了一个内部类对象时，此内部类对象会秘密地捕获一个指向那个外部类对象的引用。在访问外部类的成员时，就是用那个引用来选择外部类的成员。
* 内部类的分类
    * **成员内部类**：处于外部类成员变量位置，该类可访问外部类的所有成员；外部类的方法中也可以创建内部类的对象。
    * **局部内部类**：处于外部类的方法中，属于方法的一部分，只能在该方法中访问内部类；该内部类也无法访问方法之外的外部类成员。
    * **作用域中的内部类**：处于某个方法中的某个作用域中（如if, while等），它会被一同编译，但是，出了作用域将无法被访问。
    * **匿名内部类**：以特定形式创建一个类的子类，它没有具体的名字。
    * **嵌套内部类**：用 static 修饰的内部类。它的作用类似于一个静态方法。
* **为什么需要内部类？**
    最主要的原因在于：内部类可以解决多重继承的问题。但我们通常不是说实现多个接口可以解决多重继承问题吗？个人认为，使用多个接口实现，如果存在相同的方法名，那么对于该重名方法，类在实现该方法后，这个重名方法只会存在一种实现，如下：

        public class SubClass implements Interface1, interface2 {
            public void method() {
                // 在本类中只能提供一种实现
            }
        }
        interface1 {
            void method();
        }
        interface2 {
            void method();
        }

    那么如果使用内部类来实现不同接口的重名方法，方式如下：

        public class SubClass implements Interface1 {
            public void method() {
                // 这里提供一种实现方式
            }
            public innerClass implements Interface2 {
                public void method() {
                    // 这里提供另一种实现方式
                }     
            }
        }
    可以说，内部类更完美地实现了“多重继承”。\
    内部类在事件驱动中可以扮演重要角色，不同的内部类继承“基础事件”后实现不同的功能。\

### Chapter 11 持有对象
* Collection 的构造方法中可以接受另一个 Collection，用它进行初始化，那么我们可以使用 Arrays.List() 方法生成 Collection 构造的参数。**但是**，Collection.addAll() 方法性能更好，可以通过构建一个空的 Collection, 然后调用 Collections.addAll() 进行填充, 为什么不调用 Collection.addAll() 方法呢？ 因为该方法只接受一个 Collection 参数，而 Collections.addAll() 接受可变长度的参数列表。
* ```Arrays.asList(T... a)```返回一个由数组支持的固定长度的```List```,对于该```List```的操作将直接修改底层数组,因此无法支持对集合元素的添加和删除。同时，在使用该方法时，可变参数列表中的类型需要注意：如果参数列表中的所有参数都是泛型类子类的子类，则需要使用“显示类型参数说明”提示编译器：

        // 定义类
        class Fa {}
        class Son1 {}
        class GraSon1 extends Son1 {}
        class GraSon2 extends Son1 {}

        public class AsListTest {
            public static void main(String[] args) {
                // 编译不通过，提示 Required List<Fa>, Found List<Son1>
                // List<Fa> list = Arrays.AsList(new GraSon1(), new GranSon2());
                // 需要提供“显示类型参数说明”
                List<Fa> list = Arrays.<Fa>AsList(new GraSon1(), new GranSon2());
            }
        }
















