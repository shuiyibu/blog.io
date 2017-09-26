# [深入理解Java：注解（Annotation）基本概念](http://www.cnblogs.com/peida/archive/2013/04/23/3036035.html)

**什么是注解（Annotation）：**

　　**Annotation（注解）就是Java提供了一种元程序中的元素关联任何信息和着任何元数据（metadata）的途径和方法。**Annotion(注解)是一个接口，程序可以通过反射来获取指定程序元素的Annotion对象，然后通过Annotion对象来获取注解里面的元数据。

　　Annotation(注解)是JDK5.0及以后版本引入的。它可以用于创建文档，跟踪代码中的依赖性，甚至执行基本编译时检查。从某些方面看，annotation就像修饰符一样被使用，并应用于包、类 型、构造方法、方法、成员变量、参数、本地变量的声明中。这些信息被存储在Annotation的“name=value”结构对中。

　　**Annotation的成员**在Annotation类型中以无参数的方法的形式被声明。其方法名和返回值定义了该成员的名字和类型。在此有一个特定的默认语法：允许声明任何Annotation成员的默认值：一个Annotation可以将name=value对作为没有定义默认值的Annotation成员的值，当然也可以使用name=value对来覆盖其它成员默认值。这一点有些近似类的继承特性，父类的构造函数可以作为子类的默认构造函数，但是也可以被子类覆盖。

　　Annotation能被用来为某个程序元素（类、方法、成员变量等）关联任何的信息。需要注意的是，这里存在着一个**基本的规则：Annotation不能影响程序代码的执行，无论增加、删除 Annotation，代码都始终如一的执行。**另外，尽管一些annotation通过java的反射api方法在运行时被访问，而java语言解释器在工作时忽略了这些annotation。正是由于java虚拟机忽略了Annotation，导致了annotation类型在代码中是“不起作用”的； 只有通过某种配套的工具才会对annotation类型中的信息进行访问和处理。本文中将涵盖标准的Annotation和meta-annotation类型，陪伴这些annotation类型的工具是java编译器（当然要以某种特殊的方式处理它们）。

------

**什么是metadata（元数据）：**

　　元数据从metadata一词译来，就是“关于数据的数据”的意思。
　　元数据的功能作用有很多，比如：你可能用过Javadoc的注释自动生成文档。这就是元数据功能的一种。总的来说，元数据可以用来创建文档，跟踪代码的依赖性，执行编译时格式检查，代替已有的配置文件。如果要对于元数据的作用进行分类，目前还没有明确的定义，不过我们可以根据它所起的作用，大致可分为三类： 
　　　　1. 编写文档：通过代码里标识的元数据生成文档
　　　　2. 代码分析：通过代码里标识的元数据对代码进行分析
　　　　3. 编译检查：通过代码里标识的元数据让编译器能实现基本的编译检查
　　在Java中元数据以标签的形式存在于Java代码中，元数据标签的存在并不影响程序代码的编译和执行，它只是被用来生成其它的文件或针在运行时知道被运行代码的描述信息。
　　综上所述：
　　　　第一，元数据以标签的形式存在于Java代码中。
　　　　第二，元数据描述的信息是**==类型安全==**的，即元数据内部的字段都是有明确类型的。
　　　　第三，元数据需要编译器之外的工具额外的处理用来生成其它的程序部件。
　　　　第四，元数据可以只存在于Java源代码级别，也可以存在于编译之后的Class文件内部。

------

 **Annotation和Annotation类型：**

- **Annotation：**

  Annotation使用了在java5.0所带来的新语法，它的行为十分类似public、final这样的修饰符。每个Annotation具有一个名字和成员个数>=0。每个Annotation的成员具有被称为name=value对的名字和值（就像javabean一样），name=value装载了Annotation的信息。


- **Annotation类型：**

  Annotation类型定义了Annotation的名字、类型、成员默认值。一个Annotation类型可以说是一个特殊的java接口，它的成员变量是受限制的，而声明Annotation类型时需要使用新语法。当我们通过java反射api访问Annotation时，返回值将是一个实现了该 annotation类型接口的对象，通过访问这个对象我们能方便的访问到其Annotation成员。后面的章节将提到在java5.0的 java.lang包里包含的3个标准Annotation类型。

------

**注解的分类：**

根据注解参数的个数，我们可以将注解分为三类：

- **标记注**解：一个没有成员定义的Annotation类型被称为标记注解。这种Annotation类型仅使用自身的存在与否来为我们提供信息。比如后面的系统注解@Override;
- **单值注解**
- **完整注解**　　

根据注解使用方法和用途，我们可以将Annotation分为三类：

- JDK内置系统注解
- 元注解
- 自定义注解

------

 **系统内置标准注解：**

注解的语法比较简单，除了@符号的使用外，他基本与Java固有的语法一致，JavaSE中内置三个标准注解，定义在java.lang中：

- @Override：用于修饰此方法覆盖了父类的方法;
- @Deprecated：用于修饰已经过时的方法;
- @SuppressWarnnings：用于通知java编译器禁止特定的编译警告。

下面我们依次看看三个内置标准注解的作用和使用场景。

------

- **@Override，限定重写父类方法**：

  @Override 是一个标记注解类型，它被用作标注方法。它说明了被标注的方法重载了父类的方法，起到了断言的作用。如果我们使用了这种Annotation在一个没有覆盖父类方法的方法时，java编译器将以一个编译错误来警示。这个annotaton常常在我们试图覆盖父类方法而又写错了方法名时发挥威力。使用方法极其简单：在使用此annotation时只要在被修饰的方法前面加上@Override即可。下面的代码是一个使用@Override修饰一个企图重载父类的displayName()方法，而又存在拼写错误的实例：

  ```java
  public class Fruit {

      public void displayName(){
          System.out.println("水果的名字是：*****");
      }
  }

  class Orange extends Fruit {
      @Override
      public void displayName(){
          System.out.println("水果的名字是：桔子");
      }
  }

  class Apple extends Fruit {
      @Override
      public void displayname(){
          System.out.println("水果的名字是：苹果");
      }
  }
  ```

  Orange 类编译不会有任何问题，Apple 类在编译的时候会提示相应的错误。@Override注解只能用于方法，不能用于其他程序元素。

------

- **@Deprecated，标记已过时：**

  同样Deprecated也是一个标记注解。当一个类型或者类型成员使用@Deprecated修饰的话，编译器将不鼓励使用这个被标注的程序元素。而且这种修饰具有一定的 “延续性”：如果我们在代码中通过继承或者覆盖的方式使用了这个过时的类型或者成员，虽然继承或者覆盖后的类型或者成员并不是被声明为 @Deprecated，但编译器仍然要报警。

  值得注意，@Deprecated这个annotation类型和javadoc中的 @deprecated这个tag是有区别的：前者是java编译器识别的，而后者是被javadoc工具所识别用来生成文档（包含程序成员为什么已经过时、它应当如何被禁止或者替代的描述）。

  在java5.0，java编译器仍然象其从前版本那样寻找@deprecated这个javadoc tag，并使用它们产生警告信息。但是这种状况将在后续版本中改变，我们应在现在就开始使用@Deprecated来修饰过时的方法而不是 @deprecated javadoc tag。

  下面一段程序中使用了@Deprecated注解标示方法过期，同时在方法注释中用@deprecated tag 标示该方法已经过时，代码如下：

  ```java
  class AppleService {
      public void displayName(){
          System.out.println("水果的名字是：苹果");
      }
      
      /**
       * @deprecated 该方法已经过期，不推荐使用
       */
      @Deprecated
      public void showTaste(){
          System.out.println("水果的苹果的口感是：脆甜");
      }
      
      public void showTaste(int typeId){
          if(typeId==1){
              System.out.println("水果的苹果的口感是：酸涩");
          }
          else if(typeId==2){
              System.out.println("水果的苹果的口感是：绵甜");
          }
          else{
              System.out.println("水果的苹果的口感是：脆甜");
          }
      }
  }

  public class FruitRun {

      /**
       * @param args
       */
      public static void main(String[] args) {
          Apple apple=new Apple();
          apple.displayName();    
          
          AppleService appleService=new AppleService();
          appleService.showTaste();
          appleService.showTaste(0);
          appleService.showTaste(2);
      }

  }
  ```

  AppleService类的showTaste() 方法被@Deprecated标注为过时方法，在FruitRun类中使用的时候，编译器会给出该方法已过期，不推荐使用的提示。

------

- **SuppressWarnnings，抑制编译器警告：**

  @SuppressWarnings 被用于有选择的关闭编译器对类、方法、成员变量、变量初始化的警告。在java5.0，sun提供的javac编译器为我们提供了-Xlint选项来使编译器对合法的程序代码提出警告，此种警告从某种程度上代表了程序错误。例如当我们使用一个generic collection类而又没有提供它的类型时，编译器将提示出"unchecked warning"的警告。通常当这种情况发生时，我们就需要查找引起警告的代码。如果它真的表示错误，我们就需要纠正它。例如如果警告信息表明我们代码中的switch语句没有覆盖所有可能的case，那么我们就应增加一个默认的case来避免这种警告。

  有时我们无法避免这种警告，例如，我们使用必须和非generic的旧代码交互的generic collection类时，我们不能避免这个unchecked warning。此时@SuppressWarning就要派上用场了，在调用的方法前增加@SuppressWarnings修饰，告诉编译器停止对此方法的警告。

  SuppressWarning不是一个标记注解。它有一个类型为String[]的成员，这个成员的值为被禁止的警告名。对于javac编译器来讲，被-Xlint选项有效的警告 名也同样对@SuppressWarings有效，同时编译器忽略掉无法识别的警告名。

  annotation语法允许在annotation名后跟括号，括号中是使用逗号分割的name=value对用于为annotation的成员赋值。实例如下：

  ```java
  public class FruitService {
      
      @SuppressWarnings(value={ "rawtypes", "unchecked" })
      public static  List<Fruit> getFruitList(){
          List<Fruit> fruitList=new ArrayList();
          return fruitList;
      }
      
      @SuppressWarnings({ "rawtypes", "unchecked" })
      public static  List<Fruit> getFruit(){
          List<Fruit> fruitList=new ArrayList();
          return fruitList;
      }

      @SuppressWarnings("unused")
      public static void main(String[] args){
          List<String> strList=new ArrayList<String>();
      }
  }
  ```

  在这个例子中SuppressWarnings annotation类型只定义了一个单一的成员，所以只有一个简单的value={...}作为name=value对。又由于成员值是一个数组，故使用大括号来声明数组值。注意：我们可以在下面的情况中缩写annotation：当annotation只有单一成员，并成员命名为"value="。这时可以省去"value="。比如将上面方法getFruit()的SuppressWarnings annotation就是缩写的。

- SuppressWarnings注解的常见参数值的简单说明：

  - deprecation：使用了不赞成使用的类或方法时的警告；
  - unchecked：执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型; 
  - fallthrough：当 Switch 程序块直接通往下一种情况而没有 Break 时的警告;
  - path：在类路径、源文件路径等中有不存在的路径时的警告; 
  - serial：当在可序列化的类上缺少 serialVersionUID 定义时的警告; 
  - finally：任何 finally 子句不能正常完成时的警告; 
  - all：关于以上所有情况的警告。





从JDK5开始，Java增加了Annotation(注解)，Annotation是代码里的特殊标记，这些标记可以在编译、类加载、运行时被读取，并执行相应的处理。通过使用Annotation，开发人员可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充的信息。代码分析工具、开发工具和部署工具可以通过这些补充信息进行验证、处理或者进行部署。

Annotation提供了一种为程序元素（包、类、构造器、方法、成员变量、参数、局域变量）设置元数据的方法。Annotation不能运行，它只有成员变量，没有方法。Annotation跟public、final等修饰符的地位一样，都是程序元素的一部分，Annotation不能作为一个程序元素使用。

**1 定义Annotation**

定义新的Annotation类型使用@interface关键字（在原有interface关键字前增加@符号）。定义一个新的Annotation类型与定义一个接口很像，例如：

```
public @interface Test{
}
```

定义完该Annotation后，就可以在程序中使用该Annotation。使用Annotation，非常类似于public、final这样的修饰符，通常，会把Annotation另放一行，并且放在所有修饰符之前。例如：

```
@Test
public class MyClass{
....
}
```

**1.1 成员变量**

Annotation只有成员变量，没有方法。**==Annotation的成员变量在Annotation定义中以“无形参的方法”形式来声明==**，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。例如：

```
public @interface MyTag{
    string name();
    int age();
}
```

示例中定义了2个成员变量，这2个成员变量以方法的形式来定义。

一旦在Annotation里定义了成员变量后，使用该Annotation时就应该为该Annotation的成员变量指定值。例如：

```
public class Test{
    @MyTag(name="红薯"，age=30)
    public void info(){
    ......
    }
}
```

也可以在定义Annotation的成员变量时，为其**==指定默认值==**，指定成员变量默认值使用default关键字。示例：

```
public @interface MyTag{
    string name() default "我兰";
    int age() default 18;
}
```

如果Annotation的成员变量已经指定了默认值，使用该Annotation时可以不为这些成员变量指定值，而是直接使用默认值。例如：

```
public class Test{
    @MyTag
    public void info(){
    ......
    }
}
```

根据Annotation是否包含成员变量，可以把Annotation分为如下两类：

- 标记Annotation：没有成员变量的Annotation被称为标记。这种Annotation仅用自身的存在与否来为我们提供信息，例如@override等。
- 元数据Annotation：包含成员变量的Annotation。因为它们可以接受更多的元数据，因此被称为元数据Annotation。

**1.2 元注解**

在定义Annotation时，也可以使用JDK提供的元注解来修饰Annotation定义。JDK提供了如下4个元注解（注解的注解，不是上述的”元数据Annotation“）：

- **@Retention**
- **@Target**
- **@Documented**
- **@Inherited**

**1.2.1 @Retention**

@Retention用于指定Annotation可以保留多长时间。

@Retention包含一个名为“value”的成员变量，该value成员变量是RetentionPolicy枚举类型。使用@Retention时，必须为其value指定值。value成员变量的值只能是如下3个：

- RetentionPolicy.SOURCE：Annotation只保留在源代码中，编译器编译时，直接丢弃这种Annotation。
- RetentionPolicy.CLASS：编译器把Annotation记录在class文件中。当运行Java程序时，JVM中不再保留该Annotation。
- RetentionPolicy.RUNTIME：编译器把Annotation记录在class文件中。当运行Java程序时，JVM会保留该Annotation，程序可以通过反射获取该Annotation的信息。

示例：

```java
package com.demo1;

import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

//name=value形式
//@Retention(value=RetentionPolicy.RUNTIME)

//直接指定
@Retention(RetentionPolicy.RUNTIME)
public @interface MyTag{
	String name() default "我兰";
}
```

如果Annotation里有一个名为“value“的成员变量，使用该Annotation时，可以直接使用XXX(val)形式为value成员变量赋值，无须使用name=val形式。

**1.2.2 @Target**

@Target指定Annotation用于修饰哪些程序元素。@Target也包含一个名为”value“的成员变量，该value成员变量类型为ElementType[ ]，ElementType为枚举类型，值有如下几个：

- ElementType.TYPE：能修饰类、接口或枚举类型
- ElementType.FIELD：能修饰成员变量
- ElementType.METHOD：能修饰方法
- ElementType.PARAMETER：能修饰参数
- ElementType.CONSTRUCTOR：能修饰构造器
- ElementType.LOCAL_VARIABLE：能修饰局部变量
- ElementType.ANNOTATION_TYPE：能修饰注解
- ElementType.PACKAGE：能修饰包

示例1（单个ElementType）：

```java
package com.demo1;

import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

@Target(ElementType.FIELD)
public @interface AnnTest {
	String name() default "sunchp";
}
```

示例2（多个ElementType）：

```java
package com.demo1;

import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

@Target({ ElementType.FIELD, ElementType.METHOD })
public @interface AnnTest {
	String name() default "sunchp";
}
```

**1.2.3 @Documented**

如果定义注解A时，使用了@Documented修饰定义，则在用javadoc命令生成API文档后，所有使用注解A修饰的程序元素，将会包含注解A的说明。

示例：

```java
@Documented
public @interface Testable {
}
```

```java
public class Test {
	@Testable
	public void info() {
	}
}
```

![img](http://static.oschina.net/uploads/space/2015/0206/163800_YAow_820500.png)

**1.2.4 @Inherited**

@Inherited指定Annotation具有继承性。

示例：

```java
package com.demo2;

import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Inherited
public @interface MyTag{

}
```

```
package com.demo2;

@MyTag
public class Base {

}
```

```java
package com.demo2;

//SubClass只是继承了Base类
//并未直接使用@MyTag注解修饰
public class SubClass extends Base {
	public static void main(String[] args) {
		System.out.println(SubClass.class.isAnnotationPresent(MyTag.class));
	}
}
```

示例中Base使用@MyTag修饰，SubClass继承Base，而且没有直接使用@MyTag修饰，但是因为MyTag定义时，使用了@Inherited修饰，具有了继承性，所以运行结果为true。

如果MyTag注解没有被@Inherited修饰，则运行结果为：false。

**1.3 基本Annotation**

JDK默认提供了如下几个基本Annotation：

- **@Override **

限定重写父类方法。对于子类中被@Override 修饰的方法，如果存在对应的被重写的父类方法，则正确；如果不存在，则报错。@Override 只能作用于方法，不能作用于其他程序元素。

- **@Deprecated**

用于表示某个程序元素（类、方法等）已过时。如果使用被@Deprecated修饰的类或方法等，编译器会发出警告。

- **@SuppressWarning**

抑制编译器警告。指示被@SuppressWarning修饰的程序元素（以及该程序元素中的所有子元素，例如类以及该类中的方法.....）取消显示指定的编译器警告。例如，常见的@SuppressWarning（value="unchecked"）

- **@SafeVarargs**

@SafeVarargs是JDK 7 专门为抑制“堆污染”警告提供的。

**2 提取Annotation信息（反射）**

当开发者使用了Annotation修饰了类、方法、Field等成员之后，这些Annotation不会自己生效，必须由开发者提供相应的代码来提取并处理Annotation信息。这些处理提取和处理Annotation的代码统称为APT（Annotation Processing Tool）。

JDK主要提供了两个类，来完成Annotation的提取：

- `java.lang.annotation.Annotation`接口：这个接口是所有Annotation类型的父接口（后面会分析Annotation的本质，Annotation本质是接口，而`java.lang.annotation.Annotation`接口是这些接口的父接口）。
- `java.lang.reflect.AnnotatedElement`接口：该接口代表程序中可以被注解的程序元素。

**2.1 java.lang.annotation.Annotation**

java.lang.annotation.Annotation接口源码:

```java
package java.lang.annotation;

public interface Annotation {

    boolean equals(Object obj);

    int hashCode();

    String toString();

    Class<? extends Annotation> annotationType();
}
```

`java.lang.annotation.Annotation`接口的主要方法是annotationType( )，用于返回该注解的java.lang.Class。

**2.2 java.lang.reflect.AnnotatedElement**

`java.lang.reflect.AnnotatedElement`接口源码：

```java
package java.lang.reflect;

import java.lang.annotation.Annotation;

public interface AnnotatedElement {

     boolean isAnnotationPresent(Class<? extends Annotation> annotationClass);

    <T extends Annotation> T getAnnotation(Class<T> annotationClass);

    Annotation[] getAnnotations();

    Annotation[] getDeclaredAnnotations();
}
```

主要方法有：

- **isAnnotationPresent(Class<? extends Annotation> annotationClass)：**判断该程序元素上是否存在指定类型的注解，如果存在则返回true，否则返回false。
- **getAnnotation(Class<T> annotationClass)：**返回该程序元素上存在的指定类型的注解，如果该类型的注解不存在，则返回null
- **Annotation[] getAnnotations()：**返回该程序元素上存在的所有注解。

`java.lang.reflect.AnnotatedElement`接口是所有程序元素（例如`java.lang.Class`、`java.lang.reflect.Method`、`java.lang.reflect.Constructor`等）的父接口。类图结构如下：

![img](http://static.oschina.net/uploads/space/2015/0209/112234_SBK3_820500.png)

所以程序通过**反射**获取了某个类的AnnotatedElement对象（例如，A类method1()方法的java.lang.reflect.Method对象）后，就可以调用该对象的isAnnotationPresent( )、getAnnotation( )等方法来访问注解信息。

**为了获取注解信息，必须使用反射知识。**

**\*PS：如果想要在运行时提取注解信息，在定义注解时，该注解必须使用@Retention(RetentionPolicy.RUNTIME)修饰。***

**2.3 示例**

**2.3.1 标记Annotation**

给定一个类的全额限定名，加载类，并列出该类中被注解@MyTag修饰的方法和没被修饰的方法。

注解定义：

```java
package com.demo1;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyTag {

}
```

注解处理：

```java
package com.demo1;

import java.lang.reflect.Method;

public class ProcessTool {
	public static void process(String clazz) {
		Class targetClass = null;

		try {
			targetClass = Class.forName(clazz);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}

		for (Method m : targetClass.getMethods()) {
			if (m.isAnnotationPresent(MyTag.class)) {
				System.out.println("被MyTag注解修饰的方法名：" + m.getName());
			} else {
				System.out.println("没被MyTag注解修饰的方法名：" + m.getName());
			}
		}
	}
}
```

测试类：

```
package com.demo1;

public class Demo {
	public static void m1() {

	}

	@MyTag
	public static void m2() {

	}
}
```

```
package com.demo1;

public class Test {

	public static void main(String[] args) {
		ProcessTool.process("com.demo1.Demo");
	}
}
```

运行结果：

```
没被MyTag注解修饰的方法名：m1
被MyTag注解修饰的方法名：m2
没被MyTag注解修饰的方法名：wait
没被MyTag注解修饰的方法名：wait
没被MyTag注解修饰的方法名：wait
没被MyTag注解修饰的方法名：equals
没被MyTag注解修饰的方法名：toString
没被MyTag注解修饰的方法名：hashCode
没被MyTag注解修饰的方法名：getClass
没被MyTag注解修饰的方法名：notify
没被MyTag注解修饰的方法名：notifyAll
```

**2.3.2 元数据Annotation**

给定一个类的全额限定名，加载类，找出被注解MyTag修饰的方法，并输出每个方法的MyTag注解的属性。

注解定义：

```java
package com.demo1;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyTag {
	String name() default "我兰";

	int age() default 18;
}
```

注解处理：

```java
package com.demo1;

import java.lang.reflect.Method;

public class ProcessTool {
	public static void process(String clazz) {
		Class targetClass = null;

		try {
			targetClass = Class.forName(clazz);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}

		for (Method m : targetClass.getMethods()) {
			if (m.isAnnotationPresent(MyTag.class)) {
				MyTag tag = m.getAnnotation(MyTag.class);
				System.out.println("方法" + m.getName() + "的MyTag注解内容为：" + tag.name() + "，" + tag.age());
			}
		}
	}
}
```

测试类：

```java
package com.demo1;

public class Demo {
	public static void m1() {

	}

	@MyTag
	public static void m2() {

	}

	@MyTag(name = "红薯")
	public static void m3() {

	}

	@MyTag(name = "红薯", age = 30)
	public static void m4() {

	}
}
```

```java
package com.demo1;

public class Test {

	public static void main(String[] args) {
		ProcessTool.process("com.demo1.Demo");
	}
}
```

运行结果：

```
方法m2的MyTag注解内容为：我兰，18
方法m3的MyTag注解内容为：红薯，18
方法m4的MyTag注解内容为：红薯，30
```

若要获取注解中的成员变量值，直接调用注解对象的"成员变量名( )"形式的方法就行，例如示例中的tag.name()等。

PS：在编译器编译注解定义时，自动在class文件中，添加与成员变量同名的抽象方法，用于反射时获取成员变量的值。

通过上面的示例可以看出，其实Annotation十分简单，它是对源代码增加的一些特殊标记，这些特殊标记可通过反射获取，当程序获取这些特殊标记后，程序可以做出相应的处理（当然也可以完全忽略这些Annotation）。

**3 注解本质**

对于示例”2.3.2 元数据Annotation“中的MyTag注解，在编译后，生成一个MyTag.class文件。反编译该class文件：

```
javap -verbose -c MyTag.class > m.txt
```

MyTag注解的字节码为：

![img](http://static.oschina.net/uploads/space/2015/0209/125910_U8RD_820500.png)

通过分析字节码可知：

- **==注解实质上会被编译器编译为接口==**，并且继承java.lang.annotation.Annotation接口。
- **==注解的成员变量会被编译器编译为同名的抽象方法==**。
- 根据Java的class文件规范，class文件中会在程序元素的属性位置记录注解信息。例如，RuntimeVisibleAnnotations属性位置，记录修饰该类的注解有哪些；flags属性位置，记录该类是不是注解；在方法的AnnotationDefault属性位置，记录注解的成员变量默认值是多少。

我们再反编译下示例”2.3.2 元数据Annotation“中的Demo测试类，查看下”被注解修饰的方法是怎样记录自己被注解修饰的“：

```
javap -verbose -c Demo.class > d.txt
```

反编译结果如下：

![img](http://static.oschina.net/uploads/space/2015/0209/131810_8dMx_820500.png)

通过字节码可知：

在字节码文件中，每个方法都有RuntimeVisibleAnnotations属性位置，用来放置注解和注解的成员变量赋值。JVM在解析class文件时，会解析RuntimeVisibleAnnotations属性，并新建相应类型的注解对象，并将成员变量赋值。

如果要明白JVM对注解的运行机制，需要对class文件的格式规范有一定了解。（[资料](http://www.blogjava.net/DLevin/archive/2011/09/05/358035.html)）

**4 注解的意义**

为编译器提供辅助信息 — Annotations可以为编译器提供而外信息，以便于检测错误，抑制警告等.

编译源代码时进行而外操作 — 软件工具可以通过处理Annotation信息来生成原代码，xml文件等等.

运行时处理 — 有一些annotation甚至可以在程序运行时被检测，使用.

总之，注解是一种元数据，起到了”描述，配置“的作用。

 

　　　

出处链接：

http://www.open-open.com/lib/view/open1423558996951.html





## **扩展阅读**

Java 注解Annotation 

Java Annotation 及几个常用开源项目注解原理简析

Java自定义Annotation,通过反射解析Annotation

Java Annotation 学习笔记

Annotation实战【自定义AbstractProcessor】 