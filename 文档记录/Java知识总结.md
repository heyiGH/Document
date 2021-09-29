

# Java知识总结

由于这套总结涉及的范围很泛，很广，很杂，大家不可能一天两天就看完和学完这套面试宝典，即使你已经学过了有关的技术，那么至少也需要一个月的时间才能消化和掌握这套面试宝典，所以，大家应该早作准备，从拿到这套面试宝典之日起，就要坚持在每天闲暇之余学习其中几道题目，日积月累，等到出去面试时，一切都水到渠成，面试时就自然会游刃有余了。

答题时，先答是什么，再答有什么作用和要注意什么（这部分最重要，展现自己的心得）

答案的段落分别，层次分明，条理清晰都非常重要，从这些表面的东西也可以看出一个人的习惯、办事风格、条理等。

---

## 一、Java基础

​		基础部分的顺序：基本语法，类相关的语法，内部类的语法，继承相关的语法，异常的语法，线程的语法，集合的语法，io 的语法，虚拟机方面的语法。

### 1、一个".java"源文件中是否可以包括多个类（不是内部类）？有什么限制？ 

> 可以有多个类，但只能有一个`public`的类，并且`public`的类名必须与文件名相一致。

### 2、Java有没有goto?

> `java`中的保留字，现在没有在`java`中使用。

### 3、说说&和&&的区别。

> `&`和`&&`都可以用作逻辑与的运算符，表示逻辑与（`and`），当运算符两边的表达式的结果都为`true`时，整个运算结果才为`true`，否则，只要有一方为`false`，则结果为`false`。
>
> `&&`还具有短路的功能，即如果第一个表达式为`false`，则不再计算第二个表达式，例如，对于`if(str != null && !str.equals(“”))`表达式，当`str`为`null`时，后面的表达式不会执行，所以不会出现`NullPointerException`如果将`&&`改为`&`，则会抛出`NullPointerException`异常。`If(x==33 & ++y>0) `y会增长，`If(x==33 && ++y>0)`不会增长
>
> `&`还可以用作位运算符，当`&`操作符两边的表达式不是`boolean`类型时，`&`表示按位与操作，我们通常使用`0x0f`来与一个整数进行`&`运算，来获取该整数的最低4个bit位，例如，`0x31 & 0x0f`的结果为`0x01`。
>
> 备注：这道题先说两者的共同点，再说出&&和&的特殊之处，并列举一些经典的例子来表明自己理解透彻深入、实际经验丰富。

### 4、在JAVA中如何跳出当前的多重嵌套循环？

> 在`Java`中，要想跳出多重循环，可以在外面的循环语句前定义一个标号，然后在里层循环体的代码中使用带有标号的`break `语句，即可跳出外层循环。
>
> ``` java
> ok:
> for(int i=0;i<10;i++)	{
>        for(int j=0;j<10;j++){
>            System.out.println("i=" + i + ",j=" + j);
>            if(j == 5) break ok;
>        }
> } 
> ```
>
> 另外，我个人通常并不使用标号这种方式，而是让外层的循环条件表达式的结果可以受到里层循环体代码的控制，例如，要在二维数组中查找到某个数字。
>
> ``` java
> int arr[][] = {{1,2,3},{4,5,6,7},{9}};
> boolean found = false;
> for(int i=0;i<arr.length && !found;i++)	{
>        for(int j=0;j<arr[i].length;j++){
>            System.out.println("i=" + i + ",j=" + j);
>            if(arr[i][j]  == 5) {
>                found = true;
>                break;
>            }
>        }
> } 
> ```

### 5、switch语句能否作用在byte上，能否作用在long上，能否作用在String上? 

> 在`switch（expr1）`中，`expr1`只能是一个整数表达式或者枚举常量（更大字体），整数表达式可以是`int`基本类型或`Integer`包装类型，由于，`byte`,`short`,`char`都可以隐含转换为`int`，所以，这些类型以及这些类型的包装类型也是可以的。显然，`long`和`String`类型都不符合`switch`的语法规定，并且不能被隐式转换成`int`类型，所以，它们不能作用于`swtich`语句中。

### 6、short s1 = 1; s1 = s1 + 1;有什么错? short s1 = 1; s1 += 1;有什么错?

> 对于`short s1 = 1; s1 = s1 + 1; `由于`s1+1`运算时会自动提升表达式的类型，所以结果是`int`型，再赋值给`short`类型`s1`时，编译器将报告需要强制转换类型的错误。
>
> 对于`short s1 = 1; s1 += 1;`由于` += `是`java`语言规定的运算符，`java`编译器会对它进行特殊处理，因此可以正确编译。

### 7、char型变量中能不能存贮一个中文汉字?为什么? 

> `char`型变量是用来存储`Unicode`编码的字符的，`unicode`编码字符集中包含了汉字，所以，`char`型变量中当然可以存储汉字啦。不过，如果某个特殊的汉字没有被包含在`unicode`编码字符集中，那么，这个`char`型变量中就不能存储这个特殊汉字。补充说明：`unicode`编码占用两个字节，所以，`char`类型的变量也是占用两个字节。
>
> 备注：后面一部分回答虽然不是在正面回答题目，但是，为了展现自己的学识和表现自己对问题理解的透彻深入，可以回答一些相关的知识，做到知无不言，言无不尽。

### 8、用最有效率的方法算出2乘以8等于几? 

> `2 << 3`，因为将一个数左移n位，就相当于乘以了2的n次方，那么，一个数乘以8只要将其左移3位即可，而位运算`cpu`直接支持的，效率最高，所以，2乘以8等于几的最效率的方法是`2 << 3`。

### 9、请设计一个一百亿的计算器

> 首先要明白这道题目的考查点是什么，一是大家首先要对计算机原理的底层细节要清楚、要知道加减法的位运算原理和知道计算机中的算术运算会发生越界的情况，二是要具备一定的面向对象的设计思想。
>
> 首先，计算机中用固定数量的几个字节来存储的数值，所以计算机中能够表示的数值是有一定的范围的，为了便于讲解和理解，我们先以byte 类型的整数为例，它用1个字节进行存储，表示的最大数值范围为`-128`到`+127`。`-1`在内存中对应的二进制数据为`11111111`，如果两个`-1`相加，不考虑`Java`运算时的类型提升，运算后会产生进位，二进制结果为`1,11111110`，由于进位后超过了`byte`类型的存储空间，所以进位部分被舍弃，即最终的结果为`11111110`，也就是`-2`，这正好利用溢位的方式实现了负数的运算。`-128`在内存中对应的二进制数据为`10000000`，如果两个`-128`相加，不考虑`Java`运算时的类型提升，运算后会产生进位，二进制结果为`1,00000000`，由于进位后超过了`byte`类型的存储空间，所以进位部分被舍弃，即最终的结果为`00000000`，也就是0，这样的结果显然不是我们期望的，这说明 **计算机中的算术运算是会发生越界情况的，两个数值的运算结果不能超过计算机中的该类型的数值范围。**由于Java中涉及表达式运算时的类型自动提升，我们无法用byte类型来做演示这种问题和现象的实验，大家可以用下面一个使用整数做实验的例子程序体验一下：
>
> ``` java
> int a = Integer.MAX_VALUE;
> int b = Integer.MAX_VALUE;
> int sum = a + b;
> System.out.println("a="+a+",b="+b+",sum="+sum);
> ```
>
> 先不考虑`long`类型，由于int的正数范围为2的31次方，表示的最大数值约等于`2*1000*1000*1000`，也就是20亿的大小，所以，要实现一个一百亿的计算器，我们得自己设计一个类可以用于表示很大的整数，并且提供了与另外一个整数进行加减乘除的功能，大概功能如下：
>
> - 这个类内部有两个成员变量，一个表示符号，另一个用字节数组表示数值的二进制数。
>
> - 有一个构造方法，把一个包含有多位数值的字符串转换到内部的符号和字节数组中。
>
> - 提供加减乘除的功能。
>
> ``` java
> public class BigInteger{
>     int sign;
>     byte[] val;
>     public Biginteger(String val)	{
>         sign = ;
>         val = ;
>     }
>     public BigInteger add(BigInteger other)	{    }
>     public BigInteger subtract(BigInteger other)	{    }
>     public BigInteger multiply(BigInteger other){    }
>     public BigInteger divide(BigInteger other){    }
> }
> ```
>
> 备注：要想写出这个类的完整代码，是非常复杂的，如果有兴趣的话，可以参看`jdk`中自带的`java.math.BigInteger`类的源码。面试的人也知道谁都不可能在短时间内写出这个类的完整代码的，他要的是你是否有这方面的概念和意识，他最重要的还是考查你的能力，所以，你不要因为自己无法写出完整的最终结果就放弃答这道题，你要做的就是你比别人写得多，证明你比别人强，你有这方面的思想意识就可以了，毕竟别人可能连题目的意思都看不懂，什么都没写，你要敢于答这道题，即使只答了一部分，那也与那些什么都不懂的人区别出来，拉开了距离，算是矮子中的高个，机会当然就属于你了。另外，答案中的框架代码也很重要，体现了一些面向对象设计的功底，特别是其中的方法命名很专业，用的英文单词很精准，这也是能力、经验、专业性、英语水平等多个方面的体现，会给人留下很好的印象，在编程能力和其他方面条件差不多的情况下，英语好除了可以使你获得更多机会外，薪水可以高出一千元。 

### 10、使用final关键字修饰一个变量时，是引用不能变，还是引用的对象不能变？ 

> 使用final关键字修饰一个变量时，是指引用变量不能变，引用变量所指向的对象中的内容还是可以改变的。例如，对于如下语句：
>
> ``` java
> final StringBuffer a=new StringBuffer("immutable");
> ```
>
> 执行如下语句将报告编译期错误：
>
> ```java
> a=new StringBuffer("");
> ```
>
> 但是，执行如下语句则可以通过编译：
>
> ```java
> a.append(" broken!"); 
> ```
>
> 有人在定义方法的参数时，可能想采用如下形式来阻止方法内部修改传进来的参数对象：
>
> ```java
> public void method(final  StringBuffer  param){} 
> ```
>
> 实际上，这是办不到的，在该方法内部仍然可以增加如下代码来修改参数对象：
>
> ``` java
> public void method(final  StringBuffer  param){
> 	param.append("a");  
> } 
> ```

### 11、"=="和equals方法究竟有什么区别？

> `==`操作符专门用来比较两个变量的值是否相等，也就是用于比较变量所对应的内存中所存储的数值是否相同，要比较两个基本类型的数据或两个引用变量是否相等，只能用`==`操作符。
>
> 如果一个变量指向的数据是对象类型的，那么，这时候涉及了两块内存，对象本身占用一块内存（堆内存），变量也占用一块内存，例如`Objet obj = new Object();`变量`obj`是一个内存，`new Object()`是另一个内存，此时，变量`obj`所对应的内存中存储的数值就是对象占用的那块内存的首地址。对于指向对象类型的变量，如果要比较两个变量是否指向同一个对象，即要看这两个变量所对应的内存中的数值是否相等，这时候就需要用`==`操作符进行比较。
>
> `equals`方法是用于比较两个独立对象的内容是否相同，就好比去比较两个人的长相是否相同，它比较的两个对象是独立的。例如，对于下面的代码：
>
> ``` java
> String a=new String("foo");
> String b=new String("foo");
> ```
>
> 两条`new`语句创建了两个对象，然后用a,b这两个变量分别指向了其中一个对象，这是两个不同的对象，它们的首地址是不同的，即a和b中存储的数值是不相同的，所以，表达式`a==b`将返回`false`，而这两个对象中的内容是相同的，所以，表达式`a.equals(b)`将返回`true`。
>
> 在实际开发中，我们经常要比较传递进行来的字符串内容是否等，例如，`String input = …;input.equals(“quit”)`，许多人稍不注意就使用`==`进行比较了，这是错误的，随便从网上找几个项目实战的教学视频看看，里面就有大量这样的错误。记住，字符串的比较基本上都是使用equals方法。
>
> 如果一个类没有自己定义`equals`方法，那么它将继承`Object`类的`equals`方法，`Object`类的`equals`方法的实现代码如下：
>
> ```java
> boolean equals(Object o){
> 	return this==o;
> }
> ```
>
> 这说明，如果一个类没有自己定义`equals`方法，它默认的`equals`方法（从`Object `类继承的）就是使用`==`操作符，也是在比较两个变量指向的对象是否是同一对象，这时候使用`equals`和使用`==`会得到同样的结果，如果比较的是两个独立的对象则总返回`false`。如果你编写的类希望能够比较该类创建的两个实例对象的内容是否相同，那么你必须覆盖`equals`方法，由你自己写代码来决定在什么情况即可认为两个对象的内容是相同的。

### 12、静态变量和实例变量的区别？ 

>在语法定义上的区别：静态变量前要加`static`关键字，而实例变量前则不加。
>
>在程序运行时的区别：**实例变量属于某个对象的属性，必须创建了实例对象，其中的实例变量才会被分配空间，才能使用这个实例变量。静态变量不属于某个实例对象，而是属于类，所以也称为类变量，只要程序加载了类的字节码，不用创建任何实例对象，静态变量就会被分配空间，静态变量就可以被使用了。**总之，实例变量必须创建对象后才可以通过这个对象来使用，静态变量则可以直接使用类名来引用。
>
>例如，对于下面的程序，无论创建多少个实例对象，永远都只分配了一个`staticVar`变量，并且每创建一个实例对象，这个`staticVar`就会加1；但是，每创建一个实例对象，就会分配一个`instanceVar`，即可能分配多个`instanceVar`，并且每个`instanceVar`的值都只自加了1次。
>
>```java
>public class VariantTest{
>    public static int staticVar = 0; 
>    public int instanceVar = 0; 
>    public VariantTest(){
>        staticVar++;
>        instanceVar++;
>        System.out.println("staticVar=" + staticVar + ",instanceVar=" + instanceVar);
>    }
>}
>```
>
>备注：这个解答除了说清楚两者的区别外，最后还用一个具体的应用例子来说明两者的差异，体现了自己有很好的解说问题和设计案例的能力，思维敏捷，超过一般程序员，有写作能力！

### 13、是否可以从一个static方法内部发出对非static方法的调用？ 

> 不可以。`不可以。因为非static方法是要与对象关联在一起的，必须创建一个对象后，才可以在该对象上进行方法调用，而static方法调用时不需要创建对象，可以直接调用。`也就是说，当一个static方法被调用时，可能还没有创建任何实例对象，如果从一个static方法中发出对非static方法的调用，那个非static方法是关联到哪个对象上的呢？这个逻辑无法成立，所以，一个static方法内部发出对非static方法的调用。也就是说，当一个static方法被调用时，可能还没有创建任何实例对象，如果从一个static方法中发出对非static方法的调用，那个非static方法是关联到哪个对象上的呢？这个逻辑无法成立，所以，一个static方法内部发出对非static方法的调用。

### 14、Integer与int的区别

>  int是java提供的8种原始数据类型之一。Java为每个原始类型提供了封装类，Integer是java为int提供的封装类。`int的默认值为0，而Integer的默认值为null，即Integer可以区分出未赋值和值为0的区别`，int则无法表达出未赋值的情况，例如，要想表达出没有参加考试和考试成绩为0的区别，则只能使用Integer。在JSP开发中，Integer的默认为null，所以用el表达式在文本框中显示时，值为空白字符串，而int默认的默认值为0，所以用el表达式在文本框中显示时，结果为0，所以，int不适合作为web层的表单数据的类型。
>
> 在Hibernate中，如果将OID定义为Integer类型，那么Hibernate就可以根据其值是否为null而判断一个对象是否是临时的，如果将OID定义为了int类型，还需要在hbm映射文件中设置其unsaved-value属性为0。
>
> 另外，Integer提供了多个与整数相关的操作方法，例如，将一个字符串转换成整数，Integer中还定义了表示整数的最大值和最小值的常量。

### 15、Math.round(11.5)等于多少? Math.round(-11.5)等于多少?

> Math类中提供了三个与取整有关的方法：ceil、floor、round，这些方法的作用与它们的英文名称的含义相对应，例如，ceil的英文意义是天花板，该方法就表示向上取整，Math.ceil(11.3)的结果为12,Math.ceil(-11.3)的结果是-11；floor的英文意义是地板，该方法就表示向下取整，Math.ceil(11.6)的结果为11,Math.ceil(-11.6)的结果是-12；最难掌握的是round方法，它表示“四舍五入”，算法为Math.floor(x+0.5)，即将原来的数字加上0.5后再向下取整，所以，Math.round(11.5)的结果为12，Math.round(-11.5)的结果为-11。

### 16、请说出作用域public，private，protected，以及不写时的区别

> 这四个作用域的可见范围如下表所示。
>
> 说明：如果在修饰的元素上面没有写任何访问修饰符，则表示friendly。
>
> | 作用域    | 当前类 | 同一package | 子孙类 | 其他package |
> | :-------- | :----: | :---------: | :----: | :---------: |
> | public    |   √    |      √      |   √    |      √      |
> | protected |   √    |      √      |   √    |      ×      |
> | friendly  |   √    |      √      |   ×    |      ×      |
> | private   |   √    |      ×      |   ×    |      ×      |
>
> 备注：只要记住了有4种访问权限，4个访问范围，然后将全选和范围在水平和垂直方向上分别按排从小到大或从大到小的顺序排列，就很容易画出上面的图了。

### 17、Overload和Override的区别。Overloaded的方法是否可以改变返回值的类型? 

> Overload是重载的意思，Override是覆盖的意思，也就是重写。
>
> 重载Overload表示同一个类中可以有多个名称相同的方法，但这些方法的参数列表各不相同（即参数个数或类型不同）。
>
> 重写Override表示子类中的方法可以与父类中的某个方法的名称和参数完全相同，通过子类创建的实例对象调用这个方法时，将调用子类中的定义方法，这相当于把父类中定义的那个完全相同的方法给覆盖了，这也是面向对象编程的多态性的一种表现。子类覆盖父类的方法时，只能比父类抛出更少的异常，或者是抛出父类抛出的异常的子异常，因为子类可以解决父类的一些问题，不能比父类有更多的问题。子类方法的访问权限只能比父类的更大，不能更小。如果父类的方法是private类型，那么，子类则不存在覆盖的限制，相当于子类中增加了一个全新的方法。
>
> 至于Overloaded的方法是否可以改变返回值的类型这个问题，要看你倒底想问什么呢？这个题目很模糊。如果几个Overloaded的方法的参数列表不一样，它们的返回者类型当然也可以不一样。但我估计你想问的问题是：如果两个方法的参数列表完全一样，是否可以让它们的返回值不同来实现重载Overload。这是不行的，我们可以用反证法来说明这个问题，因为我们有时候调用一个方法时也可以不定义返回结果变量，即不要关心其返回结果，例如，我们调用map.remove(key)方法时，虽然remove方法有返回值，但是我们通常都不会定义接收返回结果的变量，这时候假设该类中有两个名称和参数列表完全相同的方法，仅仅是返回类型不同，java就无法确定编程者倒底是想调用哪个方法了，因为它无法通过返回结果类型来判断。 
>
> override可以翻译为覆盖，从字面就可以知道，它是覆盖了一个方法并且对其重写，以求达到不同的作用。对我们来说最熟悉的覆盖就是对接口方法的实现，在接口中一般只是对方法进行了声明，而我们在实现时，就需要实现接口声明的所有方法。除了这个典型的用法以外，我们在继承中也可能会在子类覆盖父类中的方法。在覆盖要注意以下的几点：
>
> > 1. 覆盖的方法的标志必须要和被覆盖的方法的标志完全匹配，才能达到覆盖的效果；
> > 2. 覆盖的方法的返回值必须和被覆盖的方法的返回一致；
> > 3. 覆盖的方法所抛出的异常必须和被覆盖方法的所抛出的异常一致，或者是其子类；
> > 4. 被覆盖的方法不能为private，否则在其子类中只是新定义了一个方法，并没有对其进行覆盖。
>
> overload对我们来说可能比较熟悉，可以翻译为重载，它是指我们可以定义一些名称相同的方法，通过定义不同的输入参数来区分这些方法，然后再调用时，VM就会根据不同的参数样式，来选择合适的方法执行。在使用重载要注意以下的几点：
>
> > 1. 在使用重载时只能通过不同的参数样式。例如，不同的参数类型，不同的参数个数，不同的参数顺序（当然，同一方法内的几个参数类型必须不一样，例如可以是fun(int,float)，但是不能为fun(int,int)）；
> >
> > 2. 不能通过访问权限、返回类型、抛出的异常进行重载；
> >
> > 3. 方法的异常类型和数目不会对重载造成影响；
> >
> > 4. 对于继承来说，如果某一方法在父类中是访问权限是priavte，那么就不能在子类对其进行重载，如果定义的话，也只是定义了一个新方法，而不会达到重载的效果。

### 18、构造器Constructor是否可被override? 

> 构造器Constructor不能被继承，因此不能重写Override，但可以被重载Overload。 

### 20、接口是否可继承接口? 抽象类是否可实现(implements)接口? 抽象类是否可继承具体类(concrete class)? 抽象类中是否可以有静态的main方法？

> 接口可以继承接口。抽象类可以实现(implements)接口，抽象类可继承具体类。抽象类中可以有静态的main方法。
>
> 备注：只要明白了接口和抽象类的本质和作用，这些问题都很好回答，你想想，如果你是java语言的设计者，你是否会提供这样的支持，如果不提供的话，有什么理由吗？如果你没有道理不提供，那答案就是肯定的了。
>
>  `只有记住抽象类与普通类的唯一区别就是不能创建实例对象和允许有abstract方法。` 

### 21、写clone()方法时，通常都有一行代码，是什么？ 

> clone 有缺省行为，`super.clone();`因为首先要把父类中的成员复制到位，然后才是复制自己的成员。 

### 22、面向对象的特征有哪些方面

> 计算机软件系统是现实生活中的业务在计算机中的映射，而现实生活中的业务其实就是一个个对象协作的过程。面向对象编程就是按现实业务一样的方式将程序代码按一个个对象进行组织和编写，让计算机系统能够识别和理解用对象方式组织和编写的程序代码，这样就可以把现实生活中的业务对象映射到计算机系统中。
>
> 面向对象的编程语言有4个主要的特征:
>
> **1 封装**
>
> 封装是保证软件部件具有优良的模块性的基础，封装的目标就是要实现软件部件的“高内聚、低耦合”，防止程序相互依赖性而带来的变动影响。在面向对象的编程语言中，对象是封装的最基本单位，面向对象的封装比传统语言的封装更为清晰、更为有力。面向对象的封装就是把描述一个对象的属性和行为的代码封装在一个“模块”中，也就是一个类中，属性用变量定义，行为用方法进行定义，方法可以直接访问同一个对象中的属性。通常情况下，`只要记住让变量和访问这个变量的方法放在一起，将一个类中的成员变量全部定义成私有的，只有这个类自己的方法才可以访问到这些成员变量，这就基本上实现对象的封装，就很容易找出要分配到这个类上的方法了，就基本上算是会面向对象的编程了。把握一个原则：把对同一事物进行操作的方法和相关的方法放在同一个类中，把方法和它操作的数据放在同一个类中。`
>
> 例如，人要在黑板上画圆，这一共涉及三个对象：人、黑板、圆，画圆的方法要分配给哪个对象呢？由于画圆需要使用到圆心和半径，圆心和半径显然是圆的属性，如果将它们在类中定义成了私有的成员变量，那么，画圆的方法必须分配给圆，它才能访问到圆心和半径这两个属性，人以后只是调用圆的画圆方法、表示给圆发给消息而已，画圆这个方法不应该分配在人这个对象上，`这就是面向对象的封装性，即将对象封装成一个高度自治和相对封闭的个体，对象状态（属性）由这个对象自己的行为（方法）来读取和改变。`一个更便于理解的例子就是，司机将火车刹住了，刹车的动作是分配给司机，还是分配给火车，显然，应该分配给火车，因为司机自身是不可能有那么大的力气将一个火车给停下来的，只有火车自己才能完成这一动作，火车需要调用内部的离合器和刹车片等多个器件协作才能完成刹车这个动作，司机刹车的过程只是给火车发了一个消息，通知火车要执行刹车动作而已。
>
> **2 抽象**
>
> 抽象就是找出一些事物的相似和共性之处，然后将这些事物归为一个类，这个类只考虑这些事物的相似和共性之处，并且会忽略与当前主题和目标无关的那些方面，将注意力集中在与当前目标有关的方面。例如，看到一只蚂蚁和大象，你能够想象出它们的相同之处，那就是抽象。抽象包括行为抽象和状态抽象两个方面。例如，定义一个Person类，如下：
>
> ```java
> class Person{
>     String name;
>     int age;
> }
> ```
>
> 人本来是很复杂的事物，有很多方面，但因为当前系统只需要了解人的姓名和年龄，所以上面定义的类中只包含姓名和年龄这两个属性，这就是一种抽像，使用抽象可以避免考虑一些与目标无关的细节。我对抽象的理解就是不要用显微镜去看一个事物的所有方面，这样涉及的内容就太多了，而是要善于划分问题的边界，当前系统需要什么，就只考虑什么。
>
> **3 继承**
>
> 在定义和实现一个类的时候，可以在一个已经存在的类的基础之上来进行，把这个已经存在的类所定义的内容作为自己的内容，并可以加入若干新的内容，或修改原来的方法使之更适合特殊的需要，这就是继承。继承是子类自动共享父类数据和方法的机制，这是类之间的一种关系，提高了软件的可重用性和可扩展性。
>
> **4 多态**
>
> 多态是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，而是在程序运行期间才确定，即一个引用变量倒底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定。因为在程序运行时才确定具体的类，这样，不用修改源程序代码，就可以让引用变量绑定到各种不同的类实现上，从而导致该引用调用的具体方法随之改变，即不修改程序代码就可以改变程序运行时所绑定的具体代码，让程序可以选择多个运行状态，这就是多态性。多态性增强了软件的灵活性和扩展性。例如，下面代码中的`UserDao`是一个接口，它定义引用变量`userDao`指向的实例对象由`daofactory.getDao()`在执行的时候返回，有时候指向的是`UserJdbcDao`这个实现，有时候指向的是`UserHibernateDao`这个实现，这样，不用修改源代码，就可以改变`userDao`指向的具体类实现，从而导致`userDao.insertUser()`方法调用的具体代码也随之改变，即有时候调用的是`UserJdbcDao的insertUser`方法，有时候调用的是`UserHibernateDao的insertUser`方法：
>
> ```java
> UserDao userDao = daofactory.getDao();  
> userDao.insertUser(user);
> ```
>
> 比喻：人吃饭，你看到的是左手，还是右手？

### 23、java中实现多态的机制是什么？ 

> 靠的是父类或接口定义的引用变量可以指向子类或具体实现类的实例对象，而程序调用的方法在运行期才动态绑定，就是引用变量所指向的具体实例对象的方法，也就是内存里正在运行的那个对象的方法，而不是引用变量的类型中定义的方法。 

### 24、abstract class和interface有什么区别? 	

> 含有abstract修饰符的class即为抽象类，abstract 类不能创建的实例对象。含有abstract方法的类必须定义为abstract class，abstract class类中的方法不必是抽象的。`abstract class类中定义抽象方法必须在具体(Concrete)子类中实现`，所以，不能有抽象构造方法或抽象静态方法。如果的子类没有实现抽象父类中的所有抽象方法，那么子类也必须定义为abstract类型。
>
> `接口（interface）可以说成是抽象类的一种特例，接口中的所有方法都必须是抽象的。`接口中的方法定义默认为public abstract类型，`接口中的成员变量类型默认为public static final`。下面比较一下两者的语法区别：
>
> 1. 抽象类可以有构造方法，接口中不能有构造方法。
> 2. 抽象类中可以有普通成员变量，接口中没有普通成员变量。
> 3. 抽象类中可以包含非抽象的普通方法，接口中的所有方法必须都是抽象的，不能有非抽象的普通方法。
> 4. 抽象类中的抽象方法的访问类型可以是`public`，`protected`和（默认类型,虽然`eclipse`下不报错，但应该也不行），但接口中的抽象方法只能是`public`类型的，并且默认即为`public abstract`类型。
> 5. 抽象类中可以包含静态方法，接口中不能包含静态方法。
> 6. 抽象类和接口中都可以包含静态成员变量，抽象类中的静态成员变量的访问类型可以任意，但接口中定义的变量只能是public static final类型，并且默认即为public static final类型。
> 7. 一个类可以实现多个接口，但只能继承一个抽象类。
>
> 下面接着再说说两者在应用上的区别：
>
> 接口更多的是在系统架构设计方法发挥作用，主要用于定义模块之间的通信契约。而抽象类在代码实现方面发挥作用，可以实现代码的重用，例如，模板方法设计模式是抽象类的一个典型应用，假设某个项目的所有`Servlet`类都要用相同的方式进行权限判断、记录访问日志和处理异常，那么就可以定义一个抽象的基类，让所有的`Servlet`都继承这个抽象基类，在抽象基类的`service`方法中完成权限判断、记录访问日志和处理异常的代码，在各个子类中只是完成各自的业务逻辑代码，伪代码如下：
>
> ```java
> public abstract class BaseServlet extends HttpServlet{
>     public final void service(HttpServletRequest request, HttpServletResponse response) throws IOExcetion,ServletException{
>         //记录访问日志
>         //进行权限判断
>         if(具有权限){
>             try{
>                 doService(request,response);
>             }
>             catch(Excetpion e)	{
>                 //记录异常信息
>             }
>         }
> 	} 
>     protected abstract void doService(HttpServletRequest request, HttpServletResponse response) throws IOExcetion,ServletException{ 
> 		//注意访问权限定义成protected，显得既专业，又严谨，因为它是专门给子类用的
> 	}
> 
> public class MyServlet1 extends BaseServlet{
>     protected void doService(HttpServletRequest request, HttpServletResponse response) throws IOExcetion,ServletException{
>         //本Servlet只处理的具体业务逻辑代码
>     } 
> 
> }
> ```
>
> 父类方法中间的某段代码不确定，留给子类干，就用模板方法设计模式。
>
> 备注：这道题的思路是先从总体解释抽象类和接口的基本概念，然后再比较两者的语法细节，最后再说两者的应用区别。比较两者语法细节区别的条理是：先从一个类中的构造方法、普通成员变量和方法（包括抽象方法），静态变量和方法，继承性等6个方面逐一去比较回答，接着从第三者继承的角度的回答，特别是最后用了一个典型的例子来展现自己深厚的技术功底。

### 25、abstract的method是否可同时是static,是否可同时是native，是否可同时是synchronized? 

> `abstract`的`method `不可以是`static`的，因为抽象的方法是要被子类实现的，而`static`与子类扯不上关系！
>
> `native`方法表示该方法要用另外一种依赖平台的编程语言实现的，不存在着被子类实现的问题，所以，它也不能是抽象的，不能与`abstract`混用。例如`FileOutputSteam`类要硬件打交道，底层的实现用的是操作系统相关的`api`实现，例如，在`windows`用c语言实现的，所以，查看`jdk` 的源代码，可以发现`FileOutputStream`的`open`方法的定义如下：
>
> ```java
> private native void open(String name) throws FileNotFoundException;
> ```
>
> 如果我们要用`java`调用别人写的c语言函数，我们是无法直接调用的，我们需要按照`java`的要求写一个c语言的函数，又我们的这个c语言函数去调用别人的c语言函数。由于我们的c语言函数是按java的要求来写的，我们这个c语言函数就可以与`java`对接上，`java`那边的对接方式就是定义出与我们这个c函数相对应的方法，`java`中对应的方法不需要写具体的代码，但需要在前面声明`native`。
>
> 关于`synchronized`与`abstract`合用的问题，我觉得也不行，因为在我几年的学习和开发中，从来没见到过这种情况，并且我觉得`synchronized`应该是作用在一个具体的方法上才有意义。而且，方法上的`synchronized`同步所使用的同步锁对象是`this`，而抽象方法上无法确定`this`是什么。 

### 26、什么是内部类？Static Nested Class 和 Inner Class的不同。

> 内部类就是在一个类的内部定义的类，内部类中不能定义静态成员（静态成员不是对象的特性，只是为了找一个容身之处，所以需要放到一个类中而已，这么一点小事，你还要把它放到类内部的一个类中，过分了啊！提供内部类，不是为让你干这种事情，无聊，不让你干。我想可能是既然静态成员类似c语言的全局变量，而内部类通常是用于创建内部对象用的，所以，把“全局变量”放在内部类中就是毫无意义的事情，既然是毫无意义的事情，就应该被禁止），内部类可以直接访问外部类中的成员变量，内部类可以定义在外部类的方法外面，也可以定义在外部类的方法体中，如下所示：
>
> ```java
> public class Outer{
>     int out_x  = 0;
>     public void method(){
>         Inner1 inner1 = new Inner1();
>         
>         public class Inner2{ //在方法体内部定义的内部类 
>             public method(){
>                 out_x = 3;
>             }
>         }
>         Inner2 inner2 = new Inner2();
>     }
> 
>     public class Inner1{}//在方法体外面定义的内部类
> 		
> }
> ```
>
> 在方法体外面定义的内部类的访问类型可以是`public`,`protecte`,`默认的`,`private`等4种类型，这就好像类中定义的成员变量有4种访问类型一样，它们决定这个内部类的定义对其他类是否可见；对于这种情况，我们也可以在外面创建内部类的实例对象，创建内部类的实例对象时，一定要先创建外部类的实例对象，然后用这个外部类的实例对象去创建内部类的实例对象，代码如下：
>
> ``` java
> Outer outer = new Outer();
> Outer.Inner1 inner1 = outer.new Innner1();
> ```
>
> 在方法内部定义的内部类前面不能有访问类型修饰符，就好像方法中定义的局部变量一样，但这种内部类的前面可以使用`final`或`abstract`修饰符。这种内部类对其他类是不可见的其他类无法引用这种内部类，但是这种内部类创建的实例对象可以传递给其他类访问。这种内部类必须是先定义，后使用，即内部类的定义代码必须出现在使用该类之前，这与方法中的局部变量必须先定义后使用的道理也是一样的。这种内部类可以访问方法体中的局部变量，但是，该局部变量前必须加`final`修饰符。
>
> 对于这些细节，只要在eclipse写代码试试，根据开发工具提示的各类错误信息就可以马上了解到。
>
> 在方法体内部还可以采用如下语法来创建一种匿名内部类，即定义某一接口或类的子类的同时，还创建了该子类的实例对象，无需为该子类定义名称：
>
> ```java
> public class Outer
> {
>     public void start()
>     {
>         new Thread(
>             new Runable(){
>                 public void run(){};
>             }
>         ).start();
>     }
> }
> ```
>
> 最后，在方法外部定义的内部类前面可以加上`static`关键字，从而成为`Static Nested Class`，它不再具有内部类的特性，所有，从狭义上讲，它不是内部类。`Static Nested Class`与普通类在运行时的行为和功能上没有什么区别，只是在编程引用时的语法上有一些差别，它可以定义成`public`、`protected`、`默认的`、`private`等多种类型，而普通类只能定义成`public`和`默认的`这两种类型。在外面引用`Static Nested Class`类的名称为“外部类名.内部类名”。在外面不需要创建外部类的实例对象，就可以直接创建`Static Nested Class`，例如，假设Inner是定义在Outer类中的`Static Nested Class`，那么可以使用如下语句创建Inner类：
>
> ```java
> Outer.Inner inner = new Outer.Inner();
> ```
>
> 由于`static Nested Clas`s不依赖于外部类的实例对象，所以`static Nested Class`能访问外部类的非`static`成员变量。当在外部类中访问`Static Nested Class`时，可以直接使用`Static Nested Class`的名字，而不需要加上外部类的名字了，在`Static Nested Class`中也可以直接引用外部类的`static`的成员变量，不需要加上外部类的名字。
>
> 在静态方法中定义的内部类也是`Static Nested Class`，这时候不能在类前面加`static`关键字，静态方法中的`Static Nested Class`与普通方法中的内部类的应用方式很相似，它除了可以直接访问外部类中的`static`的成员变量，还可以访问静态方法中的局部变量，但是该局部变量前必须加`final`修饰符。
>
> 备注：首先根据你的印象说出你对内部类的总体方面的特点：例如，在两个地方可以定义，可以访问外部类的成员变量，不能定义静态成员，这是大的特点。然后再说一些细节方面的知识，例如，几种定义方式的语法区别，静态内部类，以及匿名内部类。

### 27、内部类可以引用它的包含类的成员吗？有没有什么限制？ 

> 完全可以。如果不是静态内部类，那没有什么限制！ 
>
> 如果你把静态嵌套类当作内部类的一种特例，那在这种情况下不可以访问外部类的普通成员变量，而只能访问外部类中的静态成员，例如，下面的代码：
>
> ```java
> class Outer{
> 	static int x;
> 	static class Inner{
> 		void test(){
> 			syso(x);
> 		}
> 	}
> }
> ```
>
> 答题时，也要能察言观色，揣摩提问者的心思，显然人家希望你说的是静态内部类不能访问外部类的成员，但你一上来就顶牛，这不好，要先顺着人家，让人家满意，然后再说特殊情况，让人家吃惊。

### 28、Anonymous Inner Class (匿名内部类) 是否可以extends(继承)其它类，是否可以implements(实现)interface(接口)? 

> 可以继承其他类或实现其他接口。不仅是可以，而是必须!

### 29、super.getClass()方法调用

> 下面程序的输出结果是多少？
>
> ```java
> import java.util.Date;
> public  class Test extends Date{
> 	public static void main(String[] args) {
> 		new Test().test();
> 	}	
> 	public void test(){
> 		System.out.println(super.getClass().getName());
> 	}
> }
> // 很奇怪，结果是Test
> // 这属于脑筋急转弯的题目，在一个qq群有个网友正好问过这个问题，我觉得挺有趣，就研究了一下，没想到今天还被你面到了，哈哈。
> ```
>
> 在test方法中，直接调用`getClass().getName()`方法，返回的是Test类名由于`getClass()`在`Object`类中定义成了`final`，子类不能覆盖该方法，所以，在test方法中调用`getClass().getName()`方法，其实就是在调用从父类继承的`getClass()`方法，等效于调用`super.getClass().getName()`方法，所以，`super.getClass().getName()`方法返回的也应该是Test。
>
> 如果想得到父类的名称，应该用如下代码：
>
> ```java
> getClass().getSuperClass().getName();
> ```

### 30、String是最基本的数据类型吗? 

> 基本数据类型包括`byte`、`int`、`char`、`long`、`float`、`double`、`boolean`和`short`。 
>
> `java.lang.String`类是`final`类型的，因此不可以继承这个类、不能修改这个类。为了提高效率节省空间，我们应该用`StringBuffer`类 。

### 31、String s = "Hello";s = s + " world!";这两行代码执行后，原始的String对象中的内容到底变了没有？

> 没有。因为`String`被设计成不可变(immutable)类，所以它的所有对象都是不可变对象。在这段代码中，s原先指向一个`String`对象，内容是 `"Hello"`，然后我们对s进行了+操作，那么s所指向的那个对象是否发生了改变呢？答案是没有。这时，s不指向原来那个对象了，而指向了另一个 `String`对象，内容为`"Hello world!"`，原来那个对象还存在于内存之中，只是s这个引用变量不再指向它了。
>
> 通过上面的说明，我们很容易导出另一个结论，如果经常对字符串进行各种各样的修改，或者说，不可预见的修改，那么使用`String`来代表字符串的话会引起很大的内存开销。因为 `String`对象建立之后不能再改变，所以对于每一个不同的字符串，都需要一个`String`对象来表示。这时，应该考虑使用`StringBuffer`类，它允许修改，而不是每个不同的字符串都要生成一个新的对象。并且，这两种类的对象转换十分容易。
> 同时，我们还可以知道，如果要使用内容相同的字符串，不必每次都`new`一个`String`。例如我们要在构造器中对一个名叫s的`String`引用变量进行初始化，把它设置为初始值，应当这样做：
>
> ```java
> public class Demo {
>     private String s;
>     ...
>     public Demo {
>         s = "Initial Value";
>     }
>     ...
> }
> ```
>
> 而非
>
> ```java
> s = new String("Initial Value");
> ```
>
> 后者每次都会调用构造器，生成新对象，性能低下且内存开销大，并且没有意义，因为`String`对象不可改变，所以对于内容相同的字符串，只要一个`String`对象来表示就可以了。也就说，多次调用上面的构造器创建多个对象，他们的`String`类型属性s都指向同一个对象。
>
> 上面的结论还基于这样一个事实：对于字符串常量，如果内容相同，`Java`认为它们代表同一个`String`对象。而用关键字`new`调用构造器，总是会创建一个新的对象，无论内容是否相同。
> 至于为什么要把`String`类设计成不可变类，是它的用途决定的。其实不只`String`，很多`Java`标准类库中的类都是不可变的。在开发一个系统的时候，我们有时候也需要设计不可变类，来传递一组相关的值，这也是面向对象思想的体现。不可变类有一些优点，比如因为它的对象是只读的，所以多线程并发访问也不会有任何问题。当然也有一些缺点，比如每个不同的状态都要一个对象来代表，可能会造成性能上的问题。所以`Java`标准类库还提供了一个可变版本，即 `StringBuffer`。

### 32、是否可以继承String类? 

> String类是final类故不可以继承。 

### 33、String s = new String("xyz");创建了几个String Object? 二者之间有什么区别？

> 两个或一个，`”xyz”`对应一个对象，这个对象放在字符串常量缓冲区，常量`”xyz”`不管出现多少遍，都是缓冲区中的那一个。`New String`每写一遍，就创建一个新的对象，它一句那个常量`”xyz”`对象的内容来创建出一个新`String`对象。如果以前就用过`"xyz"`，这句代表就不会创建`”xyz”`自己了，直接从缓冲区拿。

### 34、String 和StringBuffer的区别

> `JAVA`平台提供了两个类：`String`和`StringBuffer`，它们可以储存和操作字符串，即包含多个字符的字符数据。这个`String`类提供了数值不可改变的字符串。而这个`StringBuffer`类提供的字符串进行修改。当你知道字符数据要改变的时候你就可以使用`StringBuffer`。典型地，你可以使用`StringBuffers`来动态构造字符数据。另外，`String`实现了`equals`方法，`new String(“abc”).equals(new String(“abc”)`的结果为`true`,而`StringBuffer`没有实现`equals`方法，所以`new StringBuffer(“abc”).equals(newStringBuffer(“abc”)`的结果为`false`。
>
> 接着要举一个具体的例子来说明，我们要把1到100的所有数字拼起来，组成一个串。
>
> ```java
> StringBuffer sbf = new StringBuffer();  
> for(int i=0;i<100;i++){
> 	sbf.append(i);
> }
> ```
>
> 上面的代码效率很高，因为只创建了一个`StringBuffer`对象，而下面的代码效率很低，因为创建了101个对象。
>
> ```java
> String str = new String();  
> for(int i=0;i<100;i++){
> 	str = str + i;
> }
> ```
>
> 在讲两者区别时，应把循环的次数搞成`10000`，然后用`endTime-beginTime`来比较两者执行的时间差异，最后还要讲讲`StringBuilder`与`StringBuffer`的区别。 
>
> `String`覆盖了`equals`方法和`hashCode`方法，而`StringBuffer`没有覆盖`equals`方法和`hashCode`方法，所以，将`StringBuffer`对象存储进`Java`集合类中时会出现问题。

### 35、如何把一段逗号分割的字符串转换成一个数组?

> 如果不查`jdk` api，我很难写出来！我可以说说我的思路：
>
> 1. 用正则表达式，代码大概为：`String [] result = orgStr.split(“,”);`
>
> 2.  用 StingTokenizer ,代码为：
>
>    ``` java
>StringTokenizer  tokener = StringTokenizer(orgStr,”,”);
>    String [] result = new String[tokener .countTokens()];
>int i=0;
> while(tokener.hasNext()){
>    result[i++]=toker.nextToken();
> }

### 36、数组有没有length()这个方法? String有没有length()这个方法？ 

> 数组没有`length()`这个方法，有`length`的属性。`String`有`length()`这个方法。

### 37、下面这条语句一共创建了多少个对象：String s="a"+"b"+"c"+"d";

> 答：对于如下代码：
>
> ```java
> String s1 = "a";
> String s2 = s1 + "b";
> String s3 = "a" + "b";
> System.out.println(s2 == "ab");
> System.out.println(s3 == "ab");
> ```
>
> 第一条语句打印的结果为`false`，第二条语句打印的结果为`true`，这说明`javac`编译可以对字符串常量直接相加的表达式进行优化，不必要等到运行期去进行加法运算处理，而是在编译时去掉其中的加号，直接将其编译成一个这些常量相连的结果。
>
> 题目中的第一行代码被编译器在编译时优化后，相当于直接定义了一个`”abcd”`的字符串，所以，上面的代码应该只创建了一个`String`对象。写如下两行代码:
>
> ```java
> String s = "a" + "b" + "c" + "d";
> System.out.println(s == "abcd");
> ```
>
> 最终打印的结果应该为true。 

### 38、try {}里有一个return语句，那么紧跟在这个try后的finally {}里的code会不会被执行，什么时候被执行，在return前还是后? 

> 也许你的答案是在return之前，但往更细地说，我的答案是在return中间执行，请看下面程序代码的运行结果： 
>
> ```java
> public  class Test {
> 	/**
> 	 * @param args add by zxx ,Dec 9, 2008
> 	 */
> 	public static void main(String[] args) {
> 		// TODO Auto-generated method stub
> 		System.out.println(new Test().test());;
> 	}
> 	static int test(){}
> 		int x = 1;
> 		try{
> 			return x;
> 		}
> 		finally{
> 			++x;
> 		}
> 	}
> 	
> }
> 
> //---------执行结果 ---------
> //1
> ```
>
> 运行结果是1，为什么呢？主函数调用子函数并得到结果的过程，好比主函数准备一个空罐子，当子函数要返回结果时，先把结果（对象则是引用）放在罐子里，然后再将程序逻辑返回到主函数。所谓返回，就是子函数说，我不运行了，你主函数继续运行吧，这没什么结果可言，结果是在说这话之前放进罐子里的。

### 39、下面的程序代码输出的结果是多少？

> ```java
> public class  smallT
> {
> 	public static void  main(String args[]){
> 		smallT t  = new  smallT();
> 		int  b  =  t.get();
> 		System.out.println(b);
> 	}
> 	
> 	public int  get(){
> 		try{
> 			return 1 ;
> 		}
> 		finally{
> 			return 2 ;
> 		}
> 	}
> }
> //---------执行结果 ---------
> //2
> ```
>
> 我可以通过下面一个例子程序来帮助我解释这个答案，从下面例子的运行结果中可以发现，try中的return语句调用的函数先于finally中调用的函数执行，也就是说return语句先执行，finally语句后执行，所以，返回的结果是2。Return并不是让函数马上返回，而是return语句执行后，将把返回结果放置进函数栈中，此时函数并不是马上返回，它要执行finally语句后才真正开始返回。
>
> 在讲解答案时可以用下面的程序来帮助分析：
>
> ```java
> public  class Test {
> 	/**
> 	 * @param args add by zxx ,Dec 9, 2008
> 	 */
> 	public static void main(String[] args) {
> 		// TODO Auto-generated method stub
> 		System.out.println(new Test().test());;
> 	}
> 	int test(){
> 		try{
> 			return func1();
> 		}
> 		finally{
> 			return func2();
> 		}
> 	}	
> 	int func1(){
> 		System.out.println("func1");
> 		return 1;
> 	}
> 	int func2(){
> 		System.out.println("func2");
> 		return 2;
> 	}	
> }
> //-----------执行结果-----------------
> //func1
> //func2
> //2
> ```
>
> 结论：finally中的代码比return 和break语句后执行

### 40、final, finally, finalize的区别。 

> **`final `**用于声明属性，方法和类，分别表示属性不可变，方法不可覆盖，类不可继承。 
>
> 内部类要访问局部变量，局部变量必须定义成`final`类型，例如，一段代码…… 
>
> **`finally`**是异常处理语句结构的一部分，表示总是执行。 
>
> **`finalize`**是`Object`类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法，可以覆盖此方法提供垃圾收集时的其他资源回收，例如关闭文件等。`JVM`不保证此方法总被调用

### 41、运行时异常与一般异常有何异同？ 

> 异常表示程序运行过程中可能出现的非正常状态，运行时异常表示虚拟机的通常操作中可能遇到的异常，是一种常见运行错误。`java`编译器要求方法必须声明抛出可能发生的非运行时异常，但是并不要求必须声明抛出未被捕获的运行时异常。

### 42、error和exception有什么区别? 

> `error `表示恢复不是不可能但很困难的情况下的一种严重问题。比如说内存溢出。不可能指望程序能处理这样的情况。 `exception `表示一种设计或实现问题。也就是说，它表示如果程序运行正常，从不会发生的情况。 

### 43、Java中的异常处理机制的简单原理和应用。 

> 异常是指`java`程序运行时（非编译）所发生的非正常情况或错误，与现实生活中的事件很相似，现实生活中的事件可以包含事件发生的时间、地点、人物、情节等信息，可以用一个对象来表示，`Java`使用面向对象的方式来处理异常，它把程序中发生的每个异常也都分别封装到一个对象来表示的，该对象中包含有异常的信息。
>
> Java对异常进行了分类，不同类型的异常分别用不同的Java类表示，所有异常的根类为`java.lang.Throwable`，`Throwable`下面又派生了两个子类：`Error`和`Exception`，`Error `表示应用程序本身无法克服和恢复的一种严重问题，程序只有死的份了，例如，说内存溢出和线程死锁等系统问题。`Exception`表示程序还能够克服和恢复的问题，其中又分为系统异常和普通异常，系统异常是软件本身缺陷所导致的问题，也就是软件开发人员考虑不周所导致的问题，软件使用者无法克服和恢复这种问题，但在这种问题下还可以让软件系统继续运行或者让软件死掉，例如，数组脚本越界（`ArrayIndexOutOfBoundsException`），空指针异常（`NullPointerException`）、类转换异常（`ClassCastException`）；普通异常是运行环境的变化或异常所导致的问题，是用户能够克服的问题，例如，网络断线，硬盘空间不够，发生这样的异常后，程序不应该死掉。
> `java`为系统异常和普通异常提供了不同的解决方案，编译器强制普通异常必须`try..catch`处理或用`throws`声明继续抛给上层调用方法处理，所以普通异常也称为`checked`异常，而系统异常可以处理也可以不处理，所以，编译器不强制用`try..catch`处理或用`throws`声明，所以系统异常也称为`unchecked`异常。
>
> 提示答题者：就按照三个级别去思考：虚拟机必须宕机的错误，程序可以死掉也可以不死掉的错误，程序不应该死掉的错误；

### 44、请写出你最常见到的5个runtime exception。 

> 这道题主要考你的代码量到底多大，如果你长期写代码的，应该经常都看到过一些系统方面的异常，你不一定真要回答出5个具体的系统异常，但你要能够说出什么是系统异常，以及几个系统异常就可以了，当然，这些异常完全用其英文名称来写是最好的，如果实在写不出，那就用中文吧，有总比没有强！ 
>
> 所谓系统异常，就是…..，它们都是`RuntimeException`的子类，在`jdk` doc中查`RuntimeException`类，就可以看到其所有的子类列表，也就是看到了所有的系统异常。我比较有印象的系统异常有：`NullPointerException`、 `ArrayIndexOutOfBoundsException`、`ClassCastException`。

### 45、java中有几种方法可以实现一个线程？用什么关键字修饰同步方法? stop()和suspend()方法为何不推荐使用？ 

> `java5`以前，有如下两种：
> 第一种：
> `new Thread(){}.start();`这表示调用`Thread`子类对象的`run`方法，`new Thread(){}`表示一个`Thread`的匿名子类的实例对象，子类加上`run`方法后的代码如下：
>
> ``` java
> new Thread(){
> 	public void run(){	}
> }.start();
> ```
>
> 第二种：
>
> `new Thread(new Runnable(){}).start();`这表示调用`Thread`对象接受的`Runnable`对象的`run`方法，`new Runnable(){}`表示一个`Runnable`的匿名子类的实例对象,`runnable`的子类加上`run`方法后的代码如下：
>
> ```java
> new Thread(new Runnable(){
>                 public void run(){    }	
>             }).start();
> ```
>
> 从`java5`开始，还有如下一些线程池创建多线程的方式：
>
> ```java
> ExecutorService pool = Executors.newFixedThreadPool(3)
> for(int i=0;i<10;i++){
> 	pool.execute(new Runable(){public void run(){}});
> }
> Executors.newCachedThreadPool().execute(new Runable(){public void run(){}});
> Executors.newSingleThreadExecutor().execute(new Runable(){public void run(){}});
> ```
>
> 有两种实现方法，分别使用`new Thread()`和`new Thread(runnable)`形式，第一种直接调用`thread`的`run方`法，所以，我们往往使用`Thread`子类，即`new SubThread()`。第二种调用`runnable`的`run`方法。
>
> 有两种实现方法，分别是继承`Thread`类与实现`Runnable`接口 
>
> 用`synchronized`关键字修饰同步方法 
>
> 反对使用`stop()`，是因为它不安全。它会解除由线程获取的所有锁定，而且如果对象处于一种不连贯状态，那么其他线程能在那种状态下检查和修改它们。结果很难检查出真正的问题所在。`suspend()`方法容易发生死锁。调用`suspend()`的时候，目标线程会停下来，但却仍然持有在这之前获得的锁定。此时，其他任何线程都不能访问锁定的资源，除非被"挂起"的线程恢复运行。对任何线程来说，如果它们想恢复目标线程，同时又试图使用任何一个锁定的资源，就会造成死锁。所以不应该使用`suspend()`，而应在自己的`Thread`类中置入一个标志，指出线程应该活动还是挂起。若标志指出线程应该挂起，便用`wait()`命其进入等待状态。若标志指出线程应当恢复，则用一个`notify()`重新启动线程。 

### 46、sleep() 和 wait() 有什么区别? 

> （网上的答案：`sleep`是线程类（`Thread`）的方法，导致此线程暂停执行指定时间，给执行机会给其他线程，但是监控状态依然保持，到时后会自动恢复。调用`sleep`不会释放对象锁。 `wait`是`Object`类的方法，对此对象调用`wait`方法导致本线程放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象发出`notify`方法（或`notifyAll`）后本线程才进入对象锁定池准备获得对象锁进入运行状态。） 
>
> `sleep`就是正在执行的线程主动让出`cpu`，`cpu`去执行其他线程，在`sleep`指定的时间过后，`cpu`才会回到这个线程上继续往下执行，如果当前线程进入了同步锁，`sleep`方法并不会释放锁，即使当前线程使用`sleep`方法让出了`cpu`，但其他被同步锁挡住了的线程也无法得到执行。`wait`是指在一个已经进入了同步锁的线程内，让自己暂时让出同步锁，以便其他正在等待此锁的线程可以得到同步锁并运行，只有其他线程调用了`notify`方法（`notify`并不释放锁，只是告诉调用过`wait`方法的线程可以去参与获得锁的竞争了，但不是马上得到锁，因为锁还在别人手里，别人还没释放。如果`notify`方法后面的代码还有很多，需要这些代码执行完后才会释放锁，可以在`notify`方法后增加一个等待和一些代码，看看效果），调用`wait`方法的线程就会解除`wait`状态和程序可以再次得到锁后继续向下运行。对于`wait`的讲解一定要配合例子代码来说明，才显得自己真明白。
>
> ```java
> public class MultiThread {
> 	/**
> 	 * @param args
> 	 */
> 	public static void main(String[] args) {
> 		// TODO Auto-generated method stub
> 		new Thread(new Thread1()).start();
> 		try {
>             Thread.sleep(10);
> 		} catch (InterruptedException e) {
> 			// TODO Auto-generated catch block
> 			e.printStackTrace();
> 		}
> 		new Thread(new Thread2()).start();		
> 	}	
> 	
> 	private static class Thread1 implements Runnable{
> 		@Override
> 		public void run() {
>             /*
>              *由于这里的Thread1和下面的Thread2内部run方法要用同一对象作为监视器，我们这里不能用this，因为在Thread2里面的this和这个Thread1的this不是同一个对象。我们用MultiThread.class这个字节码对象，当前虚拟机里引用这个变量时，指向的都是同一个对象。
>              */
> 			synchronized (MultiThread.class) {
> 				System.out.println("enter thread1...");				
> 				System.out.println("thread1 is waiting");
> 				try {
> 			//释放锁有两种方式，第一种方式是程序自然离开监视器的范围，也就是离开了synchronized关键字管辖的代码范围，另一种方式就是在synchronized关键字管辖的代码内部调用监视器对象的wait方法。这里，使用wait方法释放锁。
> 					MultiThread.class.wait();
> 				} catch (InterruptedException e) {
> 					e.printStackTrace();
> 				}				
> 				System.out.println("thread1 is going on...");
> 				System.out.println("thread1 is being over!");		
> 			}
> 		}		
> 	}
> 	
> 	private static class Thread2 implements Runnable{
> 		@Override
> 		public void run() {
> 			synchronized (MultiThread.class) {			
> 				System.out.println("enter thread2...");				
> 				System.out.println("thread2 notify other thread can release wait status..");
> 				//由于notify方法并不释放锁， 即使thread2调用下面的sleep方法休息了10毫秒，但thread1仍然不会执行，因为thread2没有释放锁，所以Thread1无法得不到锁。
> 				MultiThread.class.notify();				
> 				System.out.println("thread2 is sleeping ten millisecond...");
> 				try {
> 					Thread.sleep(10);
> 				} catch (InterruptedException e) {
> 					// TODO Auto-generated catch block
> 					e.printStackTrace();
> 				}				
> 				System.out.println("thread2 is going on...");
> 				System.out.println("thread2 is being over!");
> 				
> 			}
> 		}
> 		
> 	}	
> 
> }
> ```

### 47、同步和异步有何异同，在什么情况下分别使用他们？举例说明。 

> 如果数据将在线程间共享。例如正在写的数据以后可能被另一个线程读到，或者正在读的数据可能已经被另一个线程写过了，那么这些数据就是共享数据，必须进行同步存取。 
>
> 当应用程序在对象上调用了一个需要花费很长时间来执行的方法，并且不希望让程序等待方法的返回时，就应该使用异步编程，在很多情况下采用异步途径往往更有效率。 

### 48、多线程有几种实现方法?同步有几种实现方法? 

> 多线程有两种实现方法，分别是继承`Thread`类与实现`Runnable`接口 
>
> 同步的实现方面有两种，分别是`synchronized`,`wait`与`notify `
>
> `wait()`使一个线程处于等待状态，并且释放所持有的对象的`lock`。 
>
> `sleep()`使一个正在运行的线程处于睡眠状态，是一个静态方法，调用此方法要捕捉`InterruptedException`异常。 
>
> `notify()`唤醒一个处于等待状态的线程，注意的是在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由`JVM`确定唤醒哪个线程，而且不是按优先级。 
>
> `Allnotity()`唤醒所有处入等待状态的线程，注意并不是给所有唤醒线程一个对象的锁，而是让它们竞争。

### 49、启动一个线程是用run()还是start()? 

> 启动一个线程是调用start()方法，使线程就绪状态，以后可以被调度为运行状态，一个线程必须关联一些具体的执行代码，run()方法是该线程所关联的执行代码。 

### 50、当一个线程进入一个对象的一个synchronized方法后，其它线程是否可进入此对象的其它方法? 

> 分几种情况：
>
> 1. 其他方法前是否加了synchronized关键字，如果没加，则能。
>
> 2. 如果这个方法内部调用了wait，则可以进入其他synchronized方法。
>
> 3. 如果其他个方法都加了synchronized关键字，并且内部没有调用wait，则不能。
>
> 4. 如果其他方法是static，它用的同步锁是当前类的字节码，与非静态的方法不能同步，因为非静态的方法用的是this。

### 51、线程的基本概念、线程的基本状态以及状态之间的关系 

> 一个程序中可以有多条执行线索同时执行，一个线程就是程序中的一条执行线索，每个线程上都关联有要执行的代码，即可以有多段程序代码同时运行，每个程序至少都有一个线程，即`main`方法执行的那个线程。如果只是一个`cpu`，它怎么能够同时执行多段程序呢？这是从宏观上来看的，`cpu`一会执行a线索，一会执行b线索，切换时间很快，给人的感觉是a,b在同时执行，好比大家在同一个办公室上网，只有一条链接到外部网线，其实，这条网线一会为a传数据，一会为b传数据，由于切换时间很短暂，所以，大家感觉都在同时上网。 
>
> 状态：就绪，运行，`synchronize`阻塞，`wait`和`sleep`挂起，结束。
>
> `wait`必须在`synchronized`内部调用。
>
> 调用线程的start方法后线程进入就绪状态，线程调度系统将就绪状态的线程转为运行状态，遇到synchronized语句时，由运行状态转为阻塞，当synchronized获得锁后，由阻塞转为运行，在这种情况可以调用wait方法转为挂起状态，当线程关联的代码执行完后，线程变为结束状态。 

### 52、简述synchronized和java.util.concurrent.locks.Lock的异同 ？ 

> 主要相同点：`Lock`能完成`synchronized`所实现的所有功能 
>
> 主要不同点：`Lock`有比`synchronized`更精确的线程语义和更好的性能。`synchronized`会自动释放锁，而`Lock`一定要求程序员手工释放，并且必须在`finally`从句中释放。`Lock`还有更强大的功能，例如，它的`tryLock`方法可以非阻塞方式去拿锁。 
>
> 举例说明（对下面的题用`lock`进行了改写）：
>
> ```java
> import java.util.concurrent.locks.Lock;
> import java.util.concurrent.locks.ReentrantLock;
> 
> public class ThreadTest {
> 	/**
> 	 * @param args
> 	 */	
> 	private int j;
> 	private Lock lock = new ReentrantLock();
> 	public static void main(String[] args) {
> 		// TODO Auto-generated method stub
> 		ThreadTest tt = new ThreadTest();
> 		for(int i=0;i<2;i++){
> 			new Thread(tt.new Adder()).start();
> 			new Thread(tt.new Subtractor()).start();
> 		}
> 	}
> 
> 	private class Subtractor implements Runnable{
> 		@Override
> 		public void run(){
> 			// TODO Auto-generated method stub
> 			while(true)	{
> 				/*synchronized (ThreadTest.this) {			
> 					System.out.println("j--=" + j--);
> 					//这里抛异常了，锁能释放吗？
> 				}*/
> 				lock.lock();
> 				try{
> 					System.out.println("j--=" + j--);
> 				}finally
> 				{
> 					lock.unlock();
> 				}
> 			}
> 		}		
> 	}
> 	
> 	private class Adder implements Runnable{
> 		@Override
> 		public void run() {
> 			// TODO Auto-generated method stub
> 			while(true){
> 				/*synchronized (ThreadTest.this) {
> 				System.out.println("j++=" + j++);	
> 				}*/
> 				lock.lock();
> 				try{
> 					System.out.println("j++=" + j++);
> 				}finally{
> 					lock.unlock();
> 				}				
> 			}			
> 		}		
> 	}
> }
> ```

### 53、设计4个线程，其中两个线程每次对j增加1，另外两个线程对j每次减少1。写出程序。 

> 以下程序使用内部类实现线程，对j增减的时候没有考虑顺序问题。 
>
> ```java
> public class ThreadTest1{ 
>     private int j; 
>     public static void main(String args[]){ 
>         ThreadTest1 tt=new ThreadTest1(); 
>         Inc inc=tt.new Inc(); 
>         Dec dec=tt.new Dec(); 
>         for(int i=0;i<2;i++){ 
>             Thread t=new Thread(inc); 
>             t.start(); 
>             t=new Thread(dec); 
>             t.start(); 
>         } 
>     } 
>     private synchronized void inc(){ 
>         j++; 
>         System.out.println(Thread.currentThread().getName()+"-inc:"+j); 
>     } 
>     private synchronized void dec(){ 
>         j--; 
>         System.out.println(Thread.currentThread().getName()+"-dec:"+j); 
>     } 
>     class Inc implements Runnable{ 
>         public void run(){ 
>             for(int i=0;i<100;i++){ 
>                 inc(); 
>             } 
>         } 
>     } 
>     class Dec implements Runnable{ 
>         public void run(){ 
>             for(int i=0;i<100;i++){ 
>                 dec(); 
>             } 
>         } 
>     } 
> } 
> ```
>
> ---
>
> 随手再写的一个
>
> ---
>
> ```java
> class A{
>     JManger j =new JManager();
>     main(){
>         new A().call();
>     }
> 
>     void call{
>         for(int i=0;i<2;i++){
>             new Thread(
>                 new Runnable(){ public void run(){while(true){j.accumulate()}}}
>             ).start();
>             new Thread(new Runnable(){ public void run(){while(true){j.sub()}}}).start();
>         }
>     }
> }
> 
> class JManager{
>     private j = 0;
> 
>     public synchronized void subtract(){
>         j--
>     }
> 
>     public synchronized void accumulate(){
>         j++;
>     }
> 
> }
> ```

### 54、子线程循环10次，接着主线程循环100，接着又回到子线程循环10次，接着再回到主线程又循环100，如此循环50次，请写出程序。 

> ```java
> public class ThreadTest {
> 
>     public static void main(String[] args) {
>         new ThreadTest().init();
>     }
> 
>     public void init(){
>         final Business business = new Business();
>         new Thread(
>             new Runnable(){
> 
>                 public void run() {
>                     for(int i=0;i<50;i++){
>                         business.SubThread(i);
>                     }						
>                 }
> 
>             }
> 
>         ).start();
> 
>         for(int i=0;i<50;i++){
>             business.MainThread(i);
>         }		
>     }
> 
>     private class Business{
>         boolean bShouldSub = true;//这里相当于定义了控制该谁执行的一个信号灯
>         public synchronized void MainThread(int i){
>             if(bShouldSub)
>                 try {
>                     this.wait();
>                 } catch (InterruptedException e) {
>                     // TODO Auto-generated catch block
>                     e.printStackTrace();
>                 }		
> 
>             for(int j=0;j<5;j++){
>                 System.out.println(Thread.currentThread().getName() + ":i=" + i +",j=" + j);
>             }
>             bShouldSub = true;
>             this.notify();		
>         }
> 
>         public synchronized void SubThread(int i){
>             if(!bShouldSub)
>                 try {
>                     this.wait();
>                 } catch (InterruptedException e) {
>                     // TODO Auto-generated catch block
>                     e.printStackTrace();
>                 }	
> 
>             for(int j=0;j<10;j++){
>                 System.out.println(Thread.currentThread().getName() + ":i=" + i +",j=" + j);
>             }
>             bShouldSub = false;				
>             this.notify();			
>         }
>     }
> }
> ```
>
> 备注：不可能一上来就写出上面的完整代码，最初写出来的代码如下，问题在于两个线程的代码要参照同一个变量，即这两个线程的代码要共享数据，所以，把这两个线程的执行代码搬到同一个类中去：
>
> ```java
> public class ThreadTest {
>     private static boolean bShouldMain = false;
>     public static void main(String[] args) {
>         // TODO Auto-generated method stub
>         new Thread(
>             new Runnable(){
>                 public void run(){
>                     for(int i=0;i<50;i++){
>                         synchronized (ThreadTest.class) {
>                             if(bShouldMain){
>                                 try {
>                                     ThreadTest.class.wait();} 
>                                 catch (InterruptedException e) {
>                                     e.printStackTrace();
>                                 }
>                             }
>                             for(int j=0;j<10;j++){
>                                 System.out.println(
>                                     Thread.currentThread().getName() + 
>                                     "i=" + i + ",j=" + j);
>                             }
>                             bShouldMain = true;
>                             ThreadTest.class.notify();
>                         }							
>                     }						
>                 }
>             }
>         ).start();
> 
>         for(int i=0;i<50;i++){
>             synchronized (ThreadTest.class) {
>                 if(!bShouldMain){
>                     try {
>                         ThreadTest.class.wait();
>                     } catch (InterruptedException e) {
>                         e.printStackTrace();
>                     }
>                 }				
>                 for(int j=0;j<5;j++){
>                     System.out.println(Thread.currentThread().getName() + 	"i=" + i + ",j=" + j);
>                 }
>                 bShouldMain = false;
>                 ThreadTest.class.notify();				
>             }			
>         }
>     }
> }
> ```

### 55、介绍Collection框架的结构

> 答：随意发挥题，天南海北谁便谈，只要让别觉得你知识渊博，理解透彻即可。

### 56、Collection框架中实现比较要实现什么接口

> `comparable`/`comparator`

### 57、ArrayList和Vector的区别

> 答：
> 这两个类都实现了`List`接口（List接口继承了`Collection`接口），他们都是有序集合，即存储在这两个集合中的元素的位置都是有顺序的，相当于一种动态的数组，我们以后可以按位置索引号取出某个元素，并且其中的数据是允许重复的，这是`HashSet`之类的集合的最大不同处，`HashSet`之类的集合不可以按索引号去检索其中的元素，也不允许有重复的元素（本来题目问的与`hashset`没有任何关系，但为了说清楚`ArrayList`与`Vector`的功能，我们使用对比方式，更有利于说明问题）。接着才说`ArrayList`与`Vector`的区别，这主要包括两个方面：
>
> （1）同步性：
>
> Vector是线程安全的，也就是说是它的方法之间是线程同步的，而`ArrayList`是线程序不安全的，它的方法之间是线程不同步的。如果只有一个线程会访问到集合，那最好是使用`ArrayList`，因为它不考虑线程安全，效率会高些；如果有多个线程会访问到集合，那最好是使用`Vector`，因为不需要我们自己再去考虑和编写线程安全的代码。 
>
> 备注：对于`Vector`&`ArrayList`、`Hashtable`&`HashMap`，要记住线程安全的问题，记住`Vector`与`Hashtable`是旧的，是`java`一诞生就提供了的，它们是线程安全的，`ArrayList与HashMap`是`java2`时才提供的，它们是线程不安全的。所以，我们讲课时先讲老的。
>
> （2）数据增长：
>
> `ArrayList`与`Vector`都有一个初始的容量大小，当存储进它们里面的元素的个数超过了容量时，就需要增加`ArrayList`与`Vector`的存储空间，每次要增加存储空间时，不是只增加一个存储单元，而是增加多个存储单元，每次增加的存储单元的个数在内存空间利用与程序效率之间要取得一定的平衡。`Vector`默认增长为原来两倍，而`ArrayList`的增长策略在文档中没有明确规定（从源代码看到的是增长为原来的1.5倍）。`ArrayList`与`Vector`都可以设置初始的空间大小，`Vector`还可以设置增长的空间大小，而`ArrayList`没有提供设置增长空间的方法。
>
> 总结：即`Vector`增长原来的一倍，`ArrayList`增加原来的0.5倍。

### 58、HashMap和Hashtable的区别

> 条理上还需要整理，也是先说相同点，再说不同点。
> `HashMap`是`Hashtable`的轻量级实现（非线程安全的实现），他们都完成了`Map`接口，主要区别在于`HashMap`允许空（`null`）键值（`key`）,由于非线程安全，在只有一个线程访问的情况下，效率要高于`Hashtable`。 
> `HashMap`允许将`null`作为一个`entry`的`key`或者`value`，而`Hashtable`不允许。 
> `HashMap`把`Hashtable`的`contains`方法去掉了，改成`containsvalue`和`containsKey`。因为`contains`方法容易让人引起误解。 
> `Hashtable`继承自`Dictionary`类，而`HashMap`是`Java1.2`引进的`Map interface`的一个实现。 最大的不同是，`Hashtable`的方法是`Synchronize`的，而`HashMap`不是，在多个线程访问`Hashtable`时，不需要自己为它的方法实现同步，而`HashMap` 就必须为之提供外同步。 
> `Hashtable`和`HashMap`采用的`hash/rehash`算法都大概一样，所以性能不会有很大的差异。
>
> 就`HashMap`与`HashTable`主要从三方面来说。 
>
> 1. 历史原因:`Hashtable`是基于陈旧的Dictionary类的，`HashMap`是`Java 1.2`引进的`Map`接口的一个实现 
> 2. 同步性:`Hashtable`是线程安全的，也就是说是同步的，而`HashMap`是线程序不安全的，不是同步的 
> 3. 值：只有`HashMap`可以让你将空值作为一个表的条目的`key`或`value `

### 59、List 和 Map 区别?

> 一个是存储单列数据的集合，另一个是存储键和值这样的双列数据的集合，`List`中存储的数据是有顺序，并且允许重复；`Map`中存储的数据是没有顺序的，其键是不能重复的，它的值是可以有重复的。

### 60、List, Set, Map是否继承自Collection接口? 

> List，Set是，Map不是 

### 61、List、Map、Set三个接口，存取元素时，各有什么特点？ 

> 这样的题属于随意发挥题：这样的题比较考水平，两个方面的水平：一是要真正明白这些内容，二是要有较强的总结和表述能力。如果你明白，但表述不清楚，在别人那里则等同于不明白。
>
> 首先，`List`与`Set`具有相似性，它们都是单列元素的集合，所以，它们有一个功共同的父接口，叫`Collection`。`Set`里面不允许有重复的元素，所谓重复，即不能有两个相等（注意，不是仅仅是相同）的对象 ，即假设`Set`集合中有了一个A对象，现在我要向`Set`集合再存入一个B对象，但B对象与A对象`equals`相等，则B对象存储不进去，所以，`Set`集合的`add`方法有一个`boolean`的返回值，当集合中没有某个元素，此时`add`方法可成功加入该元素时，则返回`true`，当集合含有与某个元素`equals`相等的元素时，此时`add`方法无法加入该元素，返回结果为`false`。`Set`取元素时，没法说取第几个，只能以`Iterator`接口取得所有的元素，再逐一遍历各个元素。
> `List`表示有先后顺序的集合， 注意，不是那种按年龄、按大小、按价格之类的排序。当我们多次调用`add(Obj e)`方法时，每次加入的对象就像火车站买票有排队顺序一样，按先来后到的顺序排序。有时候，也可以插队，即调用`add(int index,Obj e)`方法，就可以指定当前对象在集合中的存放位置。一个对象可以被反复存储进`List`中，每调用一次`add`方法，这个对象就被插入进集合中一次，其实，并不是把这个对象本身存储进了集合中，而是在集合中用一个索引变量指向这个对象，当这个对象被`add`多次时，即相当于集合中有多个索引指向了这个对象，如图x所示。`List`除了可以以`Iterator`接口取得所有的元素，再逐一遍历各个元素之外，还可以调用`get(index i)`来明确说明取第几个。
> `Map`与`List`和`Set`不同，它是双列的集合，其中有`put`方法，定义如下：`put(obj key,obj value)`，每次存储时，要存储一对`key/value`，不能存储重复的`key`，这个重复的规则也是按`equals`比较相等。取则可以根据`key`获得相应的`value`，即`get(Object key)`返回值为`key `所对应的`value`。另外，也可以获得所有的`key`的结合，还可以获得所有的`value`的结合，还可以获得`key`和`value`组合成的`Map.Entry`对象的集合。
>
> `List `以特定次序来持有元素，可有重复元素。`Set` 无法拥有重复元素,内部排序。`Map `保存`key-value`值，`value`可多值。
>
> `HashSet`按照`hashcode`值的某种运算方式进行存储，而不是直接按`hashCode`值的大小进行存储。例如，`"abc" ---> 78`，`"def" ---> 62`，`"xyz" ---> 65`在`hashSet`中的存储顺序不是`62,65,78`，这些问题感谢以前一个叫崔健的学员提出，最后通过查看源代码给他解释清楚，看本次培训学员当中有多少能看懂源码。`LinkedHashSet`按插入的顺序存储，那被存储对象的`hashcode`方法还有什么作用呢？学员想想!`hashset`集合比较两个对象是否相等，首先看`hashcode`方法是否相等，然后看`equals`方法是否相等。`new` 两个`Student`插入到`HashSet`中，看`HashSet`的`size`，实现`hashcode`和`equals`方法后再看`size`。
>
> 同一个对象可以在`Vector`中加入多次。往集合里面加元素，相当于集合里用一根绳子连接到了目标对象。往`HashSet`中却加不了多次的。 

### 62、说出ArrayList,Vector, LinkedList的存储性能和特性 

> `ArrayList`和`Vector`都是使用数组方式存储数据，此数组元素数大于实际存储的数据以便增加和插入元素，它们都允许直接按序号索引元素，但是插入元素要涉及数组元素移动等内存操作，所以索引数据快而插入数据慢，`Vector`由于使用了`synchronized`方法（线程安全），通常性能上较`ArrayList`差，而`LinkedList`使用双向链表实现存储，按序号索引数据需要进行前向或后向遍历，但是插入数据时只需要记录本项的前后项即可，所以插入速度较快。 
>
> `LinkedList`也是线程不安全的，`LinkedList`提供了一些方法，使得`LinkedList`可以被当作堆栈和队列来使用。

### 63、去掉一个Vector集合中重复的元素 

> ```java
> Vector newVector = new Vector();
> For (int i=0;i<vector.size();i++){
>     Object obj = vector.get(i);
>     if(!newVector.contains(obj))
>        newVector.add(obj);
> }
> ```
>
> 还有一种简单的方式，`HashSet set = new HashSet(vector)`; 

### 64、Collection 和 Collections的区别。 

> `Collection`是集合类的上级接口，继承与他的接口主要有`Set `和`List`. 
> `Collections`是针对集合类的一个帮助类，他提供一系列静态方法实现对各种集合的搜索、排序、线程安全化等操作。 

### 65、Set里的元素是不能重复的，那么用什么方法来区分重复与否呢? 是用==还是equals()? 它们有何区别? 

> `Set`里的元素是不能重复的，元素重复与否是使用`equals()`方法进行判断的。 
> `equals()`和`==`方法决定引用值是否指向同一对象`equals()`在类中被覆盖，为的是当两个分离的对象的内容和类型相配的话，返回真值。

### 66、你所知道的集合类都有哪些？主要方法？ 

> 最常用的集合类是` List `和` Map`。` List` 的具体实现包括 `ArrayList` 和 `Vector`，它们是可变大小的列表，比较适合构建、存储和操作任何类型对象的元素列表。` List `适用于按数值索引访问元素的情形。 
> `Map `提供了一个更通用的元素存储方法。 `Map `集合类用于存储元素对（称作"键"和"值"），其中每个键映射到一个值。 
>
> `ArrayList/Vector` -> `List` -> ` Collection`
>
> `HashSet/TreeSet`   -> `Set`   -> ` Collection`
>
> `Propeties`  ->  `HashTable`   -> `Map`														
>
> `Treemap/HashMap`                  -> `Map`
>
> 我记的不是方法名，而是思想，我知道它们都有增删改查的方法，但这些方法的具体名称，我记得不是很清楚，对于`set`，大概的方法是`add,remove, contains`；对于`map`，大概的方法就是`put,remove，contains`等，因为，我只要在`eclispe`下按点操作符，很自然的这些方法就出来了。我记住的一些思想就是`List`类会有`get(int index)`这样的方法，因为它可以按顺序取元素，而`set`类中没有`get(int index)`这样的方法。`List`和`Set`都可以迭代出所有元素，迭代时先要得到一个`iterator`对象，所以，`set`和`list`类都有一个`iterator`方法，用于返回那个`iterator`对象。`map`可以返回三个集合，一个是返回所有的`key`的集合，另外一个返回的是所有`value`的集合，再一个返回的`key`和`value`组合成的`EntrySet`对象的集合，`map`也有`get`方法，参数是`key`，返回值是`key`对应的`value`。 

### 67、两个对象值相同(x.equals(y) == true)，但却可有不同的hash code，这句话对不对? 

> 对。
> 如果对象要保存在`HashSet`或`HashMap`中，它们的`equals`相等，那么，它们的`hashcode`值就必须相等。如果不是要保存在`HashSet`或`HashMap`，则与`hashcode`没有什么关系了，这时候`hashcode`不等是可以的，例如`arrayList`存储的对象就不用实现`hashcode`，当然，我们没有理由不实现，通常都会去实现的。

### 68、TreeSet里面放对象，如果同时放入了父类和子类的实例对象，那比较时使用的是父类的compareTo方法，还是使用的子类的compareTo方法，还是抛异常！ 

> （应该是没有针对问题的确切的答案，当前的add方法放入的是哪个对象，就调用哪个对象的compareTo方法，至于这个compareTo方法怎么做，就看当前这个对象的类中是如何编写这个方法的）
>
> 实验代码：
>
> ```java
> public class Parent implements Comparable {
>     private int age = 0;
>     public Parent(int age){
>         this.age = age;
>     }
>     public int compareTo(Object o) {
>         System.out.println("method of parent");
>         Parent o1 = (Parent)o;
>         return age>o1.age?1:age<o1.age?-1:0;
>     }
> }
> 
> public class Child extends Parent {
>     public Child(){
>         super(3);
>     }
>     public int compareTo(Object o) {
>         System.out.println("method of child");
>         return 1;
>     }
> }
> 
> public class TreeSetTest {
>     public static void main(String[] args) {
>         TreeSet set = new TreeSet();
>         set.add(new Parent(3));
>         set.add(new Child());
>         set.add(new Parent(4));
>         System.out.println(set.size());
>     }
> }
> ```

### 69、说出一些常用的类，包，接口，请各举5个 

> 要让人家感觉你对`java ee`开发很熟，所以，不能仅仅只列`core java`中的那些东西，要多列你在做`ssh`项目中涉及的那些东西。就写你最近写的那些程序中涉及的那些类。 
>
> 常用的类：`BufferedReader`  `BufferedWriter ` `FileReader`  `FileWirter`  `String`  `Integer ` `java.util.Date`，`System`，`Class`，`List`, `HashMap`
>
>  常用的包：`java.lang`  `java.io`  `java.util`  `java.sql `,`javax.servlet`
>
> 常用的接口：`Remote`  `List`  `Map`  `Document` `NodeList `, `Servlet`, `HttpServletRequest`, `HttpServletResponse`, `HttpSession`

### 70、java中有几种类型的流？JDK为每种类型的流提供了一些抽象类以供继承，请说出他们分别是哪些类？ 

> 字节流，字符流。字节流继承于`InputStream` `OutputStream`，字符流继承于`InputStreamReader` `OutputStreamWriter`。在`java.io`包中还有许多其他的流，主要是为了提高性能和使用方便。 

### 71、字节流与字符流的区别

> 要把一片二进制数据数据逐一输出到某个设备中，或者从某个设备中逐一读取一片二进制数据，不管输入输出设备是什么，我们要用统一的方式来完成这些操作，用一种抽象的方式进行描述，这个抽象描述方式起名为IO流，对应的抽象类为`OutputStream`和`InputStream `，不同的实现类就代表不同的输入和输出设备，它们都是针对字节进行操作的。
>
> 在应用中，经常要完全是字符的一段文本输出去或读进来，用字节流可以吗？计算机中的一切最终都是二进制的字节形式存在。对于“中国”这些字符，首先要得到其对应的字节，然后将字节写入到输出流。读取时，首先读到的是字节，可是我们要把它显示为字符，我们需要将字节转换成字符。由于这样的需求很广泛，人家专门提供了字符流的包装类。
>
> 底层设备永远只接受字节数据，有时候要写字符串到底层设备，需要将字符串转成字节再进行写入。字符流是字节流的包装，字符流则是直接接受字符串，它内部将串转成字节，再写入底层设备，这为我们向IO设别写入或读取字符串提供了一点点方便。
>
> 字符向字节转换时，要注意编码的问题，因为字符串转成字节数组，其实是转成该字符的某种编码的字节形式，读取也是反之的道理。
>
> 讲解字节流与字符流关系的代码案例：
>
> ```java
> import java.io.BufferedReader;
> import java.io.FileInputStream;
> import java.io.FileOutputStream;
> import java.io.FileReader;
> import java.io.FileWriter;
> import java.io.InputStreamReader;
> import java.io.PrintWriter;
> 
> public class IOTest {
>     public static void main(String[] args) throws Exception {
>         String str = "中国人";
>         /*FileOutputStream fos  = new FileOutputStream("1.txt");
> 		fos.write(str.getBytes("UTF-8"));
> 		fos.close();*/
>         /*FileWriter fw = new FileWriter("1.txt");
> 		fw.write(str);
> 		fw.close();*/
>         
>         PrintWriter pw = new PrintWriter("1.txt","utf-8");
>         pw.write(str);
>         pw.close();
> 
>         /*FileReader fr = new FileReader("1.txt");
> 		char[] buf = new char[1024];
> 		int len = fr.read(buf);
> 		String myStr = new String(buf,0,len);
> 		System.out.println(myStr);*/
>         /*FileInputStream fr = new FileInputStream("1.txt");
> 		byte[] buf = new byte[1024];
> 		int len = fr.read(buf);
> 		String myStr = new String(buf,0,len,"UTF-8");
> 		System.out.println(myStr);*/
>         
>         BufferedReader br = new BufferedReader(
>             new InputStreamReader(
>                 new FileInputStream("1.txt"),"UTF-8"	
>             )
>         );
>         String myStr = br.readLine();
>         br.close();
>         System.out.println(myStr);
>     }
> }
> ```

### 72、什么是java序列化，如何实现java序列化？或者请解释Serializable接口的作用。 

> 我们有时候将一个`java`对象变成字节流的形式传出去或者从一个字节流中恢复成一个`java`对象，例如，要将`java`对象存储到硬盘或者传送给网络上的其他计算机，这个过程我们可以自己写代码去把一个`java`对象变成某个格式的字节流再传输，但是，`jre`本身就提供了这种支持，我们可以调用`OutputStream`的`writeObject`方法来做，如果要让`java `帮我们做，要被传输的对象必须实现`serializable`接口，这样，`javac`编译时就会进行特殊处理，编译的类才可以被`writeObject`方法操作，这就是所谓的序列化。需要被序列化的类必须实现`Serializable`接口，该接口是一个`mini`接口，其中没有需要实现的方法，`implements Serializable`只是为了标注该对象是可被序列化的。 
>
>
> 例如，在web开发中，如果对象被保存在了`Session`中，`tomcat`在重启时要把`Session`对象序列化到硬盘，这个对象就必须实现`Serializable`接口。如果对象要经过分布式系统进行网络传输或通过`rmi`等远程调用，这就需要在网络上传输对象，被传输的对象就必须实现`Serializable`接口。

### 73、描述一下JVM加载class文件的原理机制? 

> `JVM`中类的装载是由`ClassLoader`和它的子类来实现的,`Java ClassLoader `是一个重要的`Java`运行时系统组件。它负责在运行时查找和装入类文件的类。 

### 74、heap和stack有什么区别。 

> `java`的内存分为两类，一类是栈内存，一类是堆内存。
>
> 栈内存是指程序进入一个方法时，会为这个方法单独分配一块私属存储空间，用于存储这个方法内部的局部变量，当这个方法结束时，分配给这个方法的栈会释放，这个栈中的变量也将随之释放。
> 堆是与栈作用不同的内存，一般用于存放不放在当前方法栈中的那些数据，例如，使用`new`创建的对象都放在堆里，所以，它不会随方法的结束而消失。方法中的局部变量使用`final`修饰后，放在堆中，而不是栈中。 

### 75、GC是什么? 为什么要有GC?

> `GC`是垃圾收集的意思（`Gabage Collection`）,内存处理是编程人员容易出现问题的地方，忘记或者错误的内存回收会导致程序或系统的不稳定甚至崩溃，`Java` 提供的 `GC` 功能可以自动监测对象是否超过作用域从而达到自动回收内存的目的，Java语言没有提供释放已分配内存的显示操作方法。

### 76、垃圾回收的优点和原理。并考虑2种回收机制。 

> `Java`语言中一个显著的特点就是引入了垃圾回收机制，使`c++`程序员最头疼的内存管理的问题迎刃而解，它使得`Java`程序员在编写程序的时候不再需要考虑内存管理。由于有个垃圾回收机制，`Java`中的对象不再有"作用域"的概念，只有对象的引用才有"作用域"。垃圾回收可以有效的防止内存泄露，有效的使用可以使用的内存。垃圾回收器通常是作为一个单独的低级别的线程运行，不可预知的情况下对内存堆中已经死亡的或者长时间没有使用的对象进行清除和回收，程序员不能实时的调用垃圾回收器对某个对象或所有对象进行垃圾回收。回收机制有分代复制垃圾回收和标记垃圾回收，增量垃圾回收。

### 77、垃圾回收器的基本原理是什么？垃圾回收器可以马上回收内存吗？有什么办法主动通知虚拟机进行垃圾回收？ 

> 对于 `GC` 来说，当程序员创建对象时，`GC` 就开始监控这个对象的地址、大小以及使用情况。通常，`GC` 采用有向图的方式记录和管理堆(`heap`)中的所有对象。通过这种方式确定哪些对象是"可达的"，哪些对象是"不可达的"。当`GC`确定一些对象为"不可达"时，`GC`就有责任回收这些内存空间。可以。程序员可以手动执行`System.gc()`，通知`GC`运行，但是`Java`语言规范并不保证`GC`一定会执行。 

### 78、什么时候用assert。 

> `assertion`(断言)在软件开发中是一种常用的调试方式，很多开发语言中都支持这种机制。在实现中，`assertion`就是在程序中的一条语句，它对一个`boolean`表达式进行检查，一个正确程序必须保证这个`boolean`表达式的值为`true`；如果该值为`false`，说明程序已经处于不正确的状态下，`assert`将给出警告或退出。一般来说，`assertion`用于保证程序最基本、关键的正确性。`assertion`检查通常在开发和测试时开启。为了提高性能，在软件发布后，`assertion`检查通常是关闭的。 
>
> ```java
> public class AssertTest {
> 	public static void main(String[] args) {
> 		int i = 0;
> 		for(i=0;i<5;i++){
> 			System.out.println(i);
> 		}
> 		//假设程序不小心多了一句--i;
> 		--i;
> 		assert i==5;		
> 	}
> }
> ```

### 79、java中会存在内存泄漏吗，请简单描述。 

> 所谓内存泄露就是指一个不再被程序使用的对象或变量一直被占据在内存中。`java`中有垃圾回收机制，它可以保证一对象不再被引用的时候，即对象编程了孤儿的时候，对象将自动被垃圾回收器从内存中清除掉。由于`Java` 使用有向图的方式进行垃圾回收管理，可以消除引用循环的问题，例如有两个对象，相互引用，只要它们和根进程不可达的，那么`GC`也是可以回收它们的，例如下面的代码可以看到这种情况的内存回收：
>
> ```java
> import java.io.IOException;
> 
> public class GarbageTest {
>        public static void main(String[] args) throws IOException {
>            try {
>                gcTest();
>            } catch (IOException e) {
>                e.printStackTrace();
>            }
>            System.out.println("has exited gcTest!");
>            System.in.read();
>            System.in.read();		
>            System.out.println("out begin gc!");		
>            for(int i=0;i<100;i++){
>                System.gc();
>                System.in.read();	
>                System.in.read();	
>            }
>        }
> 
>        private static void gcTest() throws IOException {
>            System.in.read();
>            System.in.read();		
>            Person p1 = new Person();
>            System.in.read();
>            System.in.read();		
>            Person p2 = new Person();
>            p1.setMate(p2);
>            p2.setMate(p1);
>            System.out.println("before exit gctest!");
>            System.in.read();
>            System.in.read();		
>            System.gc();
>            System.out.println("exit gctest!");
>        }
> 
>        private static class Person{
>            byte[] data = new byte[20000000];
>            Person mate = null;
>            public void setMate(Person other){
>                mate = other;
>            }
>        }
> }
> ```
>
> `java`中的 `内存泄露` 的情况：长生命周期的对象持有短生命周期对象的引用就很可能发生内存泄露，尽管短生命周期对象已经不再需要，但是因为长生命周期对象持有它的引用而导致不能被回收，这就是`java`中内存泄露的发生场景，通俗地说，就是程序员可能创建了一个对象，以后一直不再使用这个对象，这个对象却一直被引用，即这个对象无用但是却无法被垃圾回收器回收的，这就是`java`中可能出现内存泄露的情况，例如，缓存系统，我们加载了一个对象放在缓存中(例如放在一个全局`map`对象中)，然后一直不再使用它，这个对象一直被缓存引用，但却不再被使用。
>
> 检查`java`中的内存泄露，一定要让程序将各种分支情况都完整执行到程序结束，然后看某个对象是否被使用过，如果没有，则才能判定这个对象属于内存泄露。 
>
> 如果一个外部类的实例对象的方法返回了一个内部类的实例对象，这个内部类对象被长期引用了，即使那个外部类实例对象不再被使用，但由于内部类持久外部类的实例对象，这个外部类对象将不会被垃圾回收，这也会造成内存泄露。 
>
> 下面内容来自于网上（主要特点就是清空堆栈中的某个元素，并不是彻底把它从数组中拿掉，而是把存储的总数减少，本人写得可以比这个好，在拿掉某个元素时，顺便也让它从数组中消失，将那个元素所在的位置的值设置为`null`即可）：
>
> 我实在想不到比那个堆栈更经典的例子了,以致于我还要引用别人的例子，下面的例子不是我想到的，是书上看到的，当然如果没有在书上看到，可能过一段时间我自己也想的到，可是那时我说是我自己想到的也没有人相信的。
>
> ```java
> public class Stack {
>     private Object[] elements=new Object[10];
>     private int size = 0;
>     public void push(Object e){
>         ensureCapacity();
>         elements[size++] = e;
>     }
>     public Object pop(){
>         if( size == 0)
>             throw new EmptyStackException();
>         return elements[--size];
>     }
>     private void ensureCapacity(){
>         if(elements.length == size){
>             Object[] oldElements = elements;
>             elements = new Object[2 * elements.length+1];
>             System.arraycopy(oldElements,0, elements, 0, size);
>         }
>     }
> }
> ```
>
> 上面的原理应该很简单，假如堆栈加了10个元素，然后全部弹出来，虽然堆栈是空的，没有我们要的东西，但是这是个对象是无法回收的，这个才符合了内存泄露的两个条件：无用，无法回收。
>
> 但是就是存在这样的东西也不一定会导致什么样的后果，如果这个堆栈用的比较少，也就浪费了几个K内存而已，反正我们的内存都上G了，哪里会有什么影响，再说这个东西很快就会被回收的，有什么关系。下面看两个例子。
>
> ```java
> //例子1
> public class Bad{
>        public static Stack s=Stack();
>        static{
>            s.push(new Object());
>            s.pop(); //这里有一个对象发生内存泄露
>            s.push(new Object()); //上面的对象可以被回收了，等于是自愈了
>        }
> }
> ```
>
> 因为是static，就一直存在到程序退出，但是我们也可以看到它有自愈功能，就是说如果你的Stack最多有100个对象，那么最多也就只有100个对象无法被回收其实这个应该很容易理解，Stack内部持有100个引用，最坏的情况就是他们都是无用的，因为我们一旦放新的进取，以前的引用自然消失！
>
> 内存泄露的另外一种情况：当一个对象被存储进`HashSet`集合中以后，就不能修改这个对象中的那些参与计算哈希值的字段了，否则，对象修改后的哈希值与最初存储进`HashSet`集合中时的哈希值就不同了，在这种情况下，即使在`contains`方法使用该对象的当前引用作为的参数去`HashSet`集合中检索对象，也将返回找不到对象的结果，这也会导致无法从`HashSet`集合中单独删除当前对象，造成内存泄露。

### 80、能不能自己写个类，也叫java.lang.String？

> 可以，但在应用的时候，需要用自己的类加载器去加载，否则，系统的类加载器永远只是去加载`jre.jar`包中的那个`java.lang.String`。由于在`tomcat`的`web`应用程序中，都是由`webapp`自己的类加载器先自己加载`WEB-INF/classess`目录中的类，然后才委托上级的类加载器加载，如果我们在`tomcat`的`web`应用程序中写一个`java.lang.String`，这时候`Servlet` 程序加载的就是我们自己写的`java.lang.String`，但是这么干就会出很多潜在的问题，原来所有用了`java.lang.String`类的都将出现问题。
>
> 虽然`java`提供了`endorsed`技术，可以覆盖`jdk`中的某些类，具体做法是….。但是，能够被覆盖的类是有限制范围，反正不包括`java.lang`这样的包中的类。
>
> （下面的例如主要是便于大家学习理解只用，不要作为答案的一部分，否则，人家怀疑是题目泄露了）例如，运行下面的程序：
>
> ```java
> package java.lang;
> public class String {
> 	public static void main(String[] args) {
> 		System.out.println("string");
> 	}
> }
> //报告的错误如下：
> //java.lang.NoSuchMethodError: main
> //Exception in thread "main"
> //这是因为加载了jre自带的java.lang.String，而该类中没有main方法。
> ```

### 81. Java代码查错

> ```java
> abstract class Name {
>    private String name;
>    public abstract boolean isStupidName(String name) {}
> }
> //大侠们，这有何错误?
> ```
>
>
> 答案: 错。abstract method必须以分号结尾，且不带花括号。
>
> ```java
> public class Something {
>    void doSomething () {
>        private String s = "";
>        int l = s.length();
>    }
> }
> //有错吗?
> ```
>
> 答案: 错。局部变量前不能放置任何访问修饰符 (`private`，`public`，和`protected`)。`final`可以用来修饰局部变量
> (`final`如同`abstract`和`strictfp`，都是非访问修饰符，`strictfp`只能修饰`class`和`method`而非`variable`)。
>
> ```java
> abstract class Something {
>    private abstract String doSomething ();
> }
> //这好像没什么错吧?
> ```
>
> 答案: 错。`abstract`的`methods`不能以`private`修饰。`abstract`的`methods`就是让子类`implement`(实现)具体细节的，怎么可以用`private`把`abstract method`封锁起来呢? (同理，`abstract method`前不能加`final`)。
>
> ```java
> public class Something {
>    public int addOne(final int x) {
>        return ++x;
>    }
> }
> ```
>
> 答案: 错。`int x`被修饰成`final`，意味着x不能在`addOne method`中被修改。
>
> ```java
> public class Something {
>    public static void main(String[] args) {
>        Other o = new Other();
>        new Something().addOne(o);
>    }
>    public void addOne(final Other o) {
>        o.i++;
>    }
> }
> class Other {
>    public int i;
> }
> ```
>
> 答案: 正确。在`addOne method`中，参数o被修饰成`final`。如果在`addOne method`里我们修改了o的`reference`
> (比如: `o = new Other();`)，那么如同上例这题也是错的。但这里修改的是o的`member vairable`(成员变量)，而o的`reference`并没有改变。
>
> ```java
> class Something {
>     int i;
>     public void doSomething() {
>         System.out.println("i = " + i);
>     }
> } 
> ```
>
> 答案: 正确。输出的是`"i = 0"`。`int i`属于`instant variable `(实例变量，或叫成员变量)。`instant variable`有`default value`。`int`的`default value`是0。
>
> ```java
> class Something {
>     final int i;
>     public void doSomething() {
>         System.out.println("i = " + i);
>     }
> }
> ```
>
> 答案: 错。`final` `int i`是个`final`的`instant variable` (实例变量，或叫成员变量)。`final`的`instant variable`没有`default value`，必须在`constructor` (构造器)结束之前被赋予一个明确的值。可以修改为`"final int i = 0;"`。
>
> ```java
> public class Something {
>      public static void main(String[] args) {
>         Something s = new Something();
>         System.out.println("s.doSomething() returns " + doSomething());
>     }
>     public String doSomething() {
>         return "Do something ...";
>     }
> }
> ```
>
> 看上去很完美。
> 答案: 错。看上去在`main`里`call doSomething`没有什么问题，毕竟两个`methods`都在同一个`class`里。但仔细看，`main`是`static`的。`static method`不能直接`call non-static methods`。可改成`"System.out.println("s.doSomething() returns " + s.doSomething());"`。同理，`static method`不能访问`non-static instant variable`。
>
> ```java
> //此处，Something类的文件名叫OtherThing.java
> class Something {
>     private static void main(String[] something_to_do) {    
>         System.out.println("Do something ...");
>     }
> }
> ```
>
> 答案: 正确。从来没有人说过Java的Class名字必须和其文件名相同。但public class的名字必须和文件名相同。
>
> ```java
> interface  A{
>     int x = 0;
> }
> class B{
>     int x =1;
> }
> class C extends B implements A {
>     public void pX(){
>         System.out.println(x);
>     }
>     public static void main(String[] args) {
>         new C().pX();
>     }
> }
> ```
>
> 答案：错误。在编译时会发生错误(错误描述不同的`JVM`有不同的信息，意思就是未明确的x调用，两个x都匹配（就象在同时`import java.util`和`java.sql`两个包时直接声明`Date`一样）。对于父类的变量,可以用`super.x`来明确，而接口的属性默认隐含为 `public static final`.所以可以通过`A.x`来明确。
>
> ```java
> interface Playable {
>     void play();
> }
> interface Bounceable {
>     void play();
> }
> interface Rollable extends Playable, Bounceable {
>     Ball ball = new Ball("PingPang");
> }
> class Ball implements Rollable {
>     private String name;
>     public String getName() {
>         return name;
>     }
>     public Ball(String name) {
>         this.name = name;        
>     }
>     public void play() {
>         ball = new Ball("Football");
>         System.out.println(ball.getName());
>     }
> }
> ```
>
> 这个错误不容易发现。
> 答案: 错。`"interface Rollable extends Playable, Bounceable"`没有问题。`interface`可继承多个`interfaces`，所以这里没错。问题出在`interface Rollable`里的`"Ball ball = new Ball("PingPang");"`。任何在`interface`里声明的`interface variable (接口变量，也可称成员变量)`，默认为`public static final`。也就是说`"Ball ball = new Ball("PingPang");"`实际上是`"public static final Ball ball = new Ball("PingPang");"`。在`Ball`类的`Play()`方法中，`"ball = new Ball("Football");"`改变了`ball`的`reference`，而这里的`ball`来自`Rollable interface`，`Rollable interface`里的`ball`是`public static final`的，`final`的`object`是不能被改变`reference`的。因此编译器将在`"ball = new Ball("Football");"`这里显示有错。

## 二. 算法与编程

### 1、编写一个程序，将a.txt文件中的单词与b.txt文件中的单词交替合并到c.txt文件中，a.txt文件中的单词用回车符分隔，b.txt文件中用回车或空格进行分隔。

> ```java
> import java.io.File;
> import java.io.FileReader;
> import java.io.FileWriter;
> 
> public class MainClass{
>     public static void main(String[] args) throws Exception{
>         FileManager a = new FileManager("a.txt",new char[]{'\n'});
>         FileManager b = new FileManager("b.txt",new char[]{'\n',' '});		
>         FileWriter c = new FileWriter("c.txt");
>         String aWord = null;
>         String bWord = null;
>         while((aWord = a.nextWord()) !=null ){
>             c.write(aWord + "\n");
>             bWord = b.nextWord();
>             if(bWord != null)
>                 c.write(bWord + "\n");
>         }
> 
>         while((bWord = b.nextWord()) != null){
>             c.write(bWord + "\n");
>         }	
>         c.close();
>     }
> 
> }
> 
> 
> class FileManager{
> 
>     String[] words = null;
>     int pos = 0;
>     public FileManager(String filename,char[] seperators) throws Exception{
>         File f = new File(filename);
>         FileReader reader = new FileReader(f);
>         char[] buf = new char[(int)f.length()];
>         int len = reader.read(buf);
>         String results = new String(buf,0,len);
>         String regex = null;
>         if(seperators.length >1 ){
>             regex = "" + seperators[0] + "|" + seperators[1];
>         }else{
>             regex = "" + seperators[0];
>         }
>         words = results.split(regex);
>     }
> 
>     public String nextWord(){
>         if(pos == words.length)
>             return null;
>         return words[pos++];
>     }
> 
> }
> ```

### 2、编写一个程序，将d:\java目录下的所有.java文件复制到d:\jad目录下，并将原来文件的扩展名从.java改为.jad。

> （大家正在做上面这道题，网上迟到的朋友也请做做这道题，找工作必须能编写这些简单问题的代码！）
> 答：`listFiles`方法接受一个`FileFilter`对象，这个`FileFilter`对象就是过虑的策略对象，不同的人提供不同的`FileFilter`实现，即提供了不同的过滤策略。
>
> ```java
> import java.io.File;
> import java.io.FileInputStream;
> import java.io.FileOutputStream;
> import java.io.FilenameFilter;
> import java.io.IOException;
> import java.io.InputStream;
> import java.io.OutputStream;
> 
> public class Jad2Java {
> 
>  public static void main(String[] args) throws Exception {
>      File srcDir = new File("java");
>      if(!(srcDir.exists() && srcDir.isDirectory()))
>          throw new Exception("目录不存在");
>      File[] files = srcDir.listFiles(
>          new FilenameFilter(){
> 
>              public boolean accept(File dir, String name) {
>                  return name.endsWith(".java");
>              }
> 
>          }
>      );
> 
>      System.out.println(files.length);
>      File destDir = new File("jad");
>      if(!destDir.exists()) destDir.mkdir();
>      for(File f :files){
>          FileInputStream  fis = new FileInputStream(f);
>          String destFileName = f.getName().replaceAll("\\.java$", ".jad");
>          FileOutputStream fos = new FileOutputStream(new File(destDir,destFileName));
>          copy(fis,fos);
>          fis.close();
>          fos.close();
>      }
>  }
> 
>  private static void copy(InputStream ips,OutputStream ops) throws Exception{
>      int len = 0;
>      byte[] buf = new byte[1024];
>      while((len = ips.read(buf)) != -1){
>          ops.write(buf,0,len);
>      }
> 
>  }
> }
> ```
>
> 由本题总结的思想及策略模式的解析：
>
> 1. 得到某个目录下的所有的`java`文件集合
>
> 2. 得到目录 `File srcDir = new File("d:\\java");`
>
> 3. 得到目录下的所有`java`文件：`File[] files = srcDir.listFiles(new MyFileFilter());`
>
> 4.  只想得到`.java`的文件:
>
>    ```java
>    class MyFileFilter implememyts FileFilter{
>        public boolean accept(File pathname){
>            return pathname.getName().endsWith(".java")
>        }
>    }
>    ```
>
> 5. 将每个文件复制到另外一个目录，并改扩展名
>
> 6. 得到目标目录，如果目标目录不存在，则创建之
>
> 7. 根据源文件名得到目标文件名，注意要用正则表达式，注意.的转义。
>
> 8. 根据表示目录的`File`和目标文件名的字符串，得到表示目标文件的`File`。//要在硬盘中准确地创建出一个文件，需要知道文件名和文件的目录。 
>
> 9. 将源文件的流拷贝成目标文件流，拷贝方法独立成为一个方法，方法的参数采用抽象流的形式。//方法接受的参数类型尽量面向父类，越抽象越好，这样适应面更宽广。	
>
> 分析`listFiles`方法内部的策略模式实现原理
>
> ```java
> File[] listFiles(FileFilter filter){
>     File[] files = listFiles();
>     File[] acceptedFiles = new File[files.length];
>     int pos = 0;
>     for(File file: files){
>         boolean accepted = filter.accept(file);
>         if(accepted){
>             //acceptedFilesList.add(file);
>             acceptedFiles[pos++] = file;
>         }		
>     }
>     Arrays.copyOf(acceptedFiles,pos);
> }
> ```

### 3、编写一个截取字符串的函数，输入为一个字符串和字节数，输出为按字节截取的字符串，但要保证汉字不被截取半个，如“我ABC”，4，应该截取“我AB”，输入“我ABC汉DEF”，6，应该输出“我ABC”，而不是“我ABC+汉的半个”。

> 首先要了解中文字符有多种编码及各种编码的特征。假设n为要截取的字节数。
>
> ```java
> public static void main(String[] args) throws Exception{
>     String str = "我a爱中华abc我爱传智def';
>     String str = "我ABC汉";
>     int num = trimGBK(str.getBytes("GBK"),5);
>     System.out.println(str.substring(0,num));
> }
> 
> public static int  trimGBK(byte[] buf,int n){
>     int num = 0;
>     boolean bChineseFirstHalf = false;
>     for(int i=0;i<n;i++){
>         if(buf[i]<0 && !bChineseFirstHalf){
>             bChineseFirstHalf = true;
>         }else{
>             num++;
>             bChineseFirstHalf = false;				
>         }
>     }
>     return num;
> }
> ```

### 4、有一个字符串，其中包含中文字符、英文字符和数字字符，请统计和打印出各个字符的个数。

> 答：哈哈，其实包含中文字符、英文字符、数字字符原来是出题者放的烟雾弹。
>
> ```java
> String content = “中国aadf的111萨bbb菲的zz萨菲”;
> HashMap map = new HashMap();
> for(int i=0;i<content.length;i++){
>     char c = content.charAt(i);
>     Integer num = map.get(c);
>     if(num == null)
>         num = 1;
>     else
>         num = num + 1;
>     map.put(c,num);
> } 
> for(Map.EntrySet entry : map){
>     system.out.println(entry.getkey() + “:” + entry.getValue());
> }
> ```
>
> 估计是当初面试的那个学员表述不清楚，问题很可能是：
>
> 如果一串字符如"aaaabbc中国1512"要分别统计英文字符的数量，中文字符的数量，和数字字符的数量，假设字符中没有中文字符、英文字符、数字字符之外的其他特殊字符。
>
> ```java
> int engishCount;
> int chineseCount;
> int digitCount;
> for(int i=0;i<str.length;i++){
>     char ch = str.charAt(i);
>     if(ch>=’0’ && ch<=’9’){
>         digitCount++
>     }
>     else if((ch>=’a’ && ch<=’z’) || (ch>=’A’ && ch<=’Z’)){
>         engishCount++;
>     }
>     else{
>         chineseCount++;
>     }
> }
> System.out.println(……………);
> ```
>
> 

### 5、说明生活中遇到的二叉树，用java实现二叉树

> 这是组合设计模式。
>
> 我有很多个(假设10万个)数据要保存起来，以后还需要从保存的这些数据中检索是否存在某个数据，（我想说出二叉树的好处，该怎么说呢？那就是说别人的缺点），假如存在数组中，那么，碰巧要找的数字位于`99999`那个地方，那查找的速度将很慢，因为要从第1个依次往后取，取出来后进行比较。平衡二叉树（构建平衡二叉树需要先排序，我们这里就不作考虑了）可以很好地解决这个问题，但二叉树的遍历（前序，中序，后序）效率要比数组低很多，原理如下图：
>
> ![img](E:\文档记录\图片1.png)
>
> ```java
> public class Node {
>     public int value;
>     public Node left;
>     public Node right;
> 
>     public void store(int value){
>         if(value<this.value){
>             if(left == null){
>                 left = new Node();
>                 left.value=value;
>             }
>             else{
>                 left.store(value);
>             }
>         }
>         else if(value>this.value){
>             if(right == null){
>                 right = new Node();
>                 right.value=value;
>             }
>             else{
>                 right.store(value);
>             }			
>         }
>     }
> 
>     public boolean find(int value){	
>         System.out.println("happen " + this.value);
>         if(value == this.value){
>             return true;
>         }
>         else if(value>this.value){
>             if(right == null) return false;
>             return right.find(value);
>         }else{
>             if(left == null) return false;
>             return left.find(value);
>         }
>     }
> 
>     public  void preList(){
>         System.out.print(this.value + ",");
>         if(left!=null) left.preList();
>         if(right!=null) right.preList();
>     }
> 
>     public void middleList(){
>         if(left!=null) left.preList();
>         System.out.print(this.value + ",");
>         if(right!=null) right.preList();		
>     }
>     public void afterList(){
>         if(left!=null) left.preList();
>         if(right!=null) right.preList();
>         System.out.print(this.value + ",");		
>     }	
>     public static void main(String [] args){
>         int [] data = new int[20];
>         for(int i=0;i<data.length;i++){
>             data[i] = (int)(Math.random()*100) + 1;
>             System.out.print(data[i] + ",");
>         }
>         System.out.println();
>         Node root = new Node();
>         root.value = data[0];
>         for(int i=1;i<data.length;i++){
>             root.store(data[i]);
>         }
>         root.find(data[19]);
>         root.preList();
>         System.out.println();
>         root.middleList();
>         System.out.println();		
>         root.afterList();
>     }
> }
> ```
>
> -----------------又一次临场写的代码---------------------------
>
> ```java
> import java.util.Arrays;
> import java.util.Iterator;
> 
> public class Node {
>     private Node left;
>     private Node right;
>     private int value;
>     //private int num;
> 
>     public Node(int value){
>         this.value = value;
>     }
>     public void add(int value){
> 
>         if(value > this.value){
>             if(right != null)
>                 right.add(value);
>             else{
>                 Node node = new Node(value);				
>                 right = node;
>             }
>         }
>         else{
>             if(left != null)
>                 left.add(value);
>             else{
>                 Node node = new Node(value);				
>                 left = node;
>             }			
>         }
>     }
> 
>     public boolean find(int value){
>         if(value == this.value) return true;
>         else if(value > this.value){
>             if(right == null) return false;
>             else return right.find(value);
>         }else{
>             if(left == null) return false;
>             else return left.find(value);			
>         }
> 
>     }
> 
>     public void display(){
>         System.out.println(value);
>         if(left != null) left.display();
>         if(right != null) right.display();
>     }
> 
>     public static void main(String[] args){
>         int[] values = new int[8];
>         for(int i=0;i<8;i++){
>             int num = (int)(Math.random() * 15);
>             if(!contains(values,num))
>                 values[i] = num;
>             else
>                 i--;
>         }
> 
>         System.out.println(Arrays.toString(values));
> 
>         Node root  = new Node(values[0]);
>         for(int i=1;i<values.length;i++){
>             root.add(values[i]);
>         }
>         System.out.println(root.find(13));
>         root.display();
>     }
> 
>     public static boolean contains(int [] arr, int value){
>         int i = 0;
>         for(;i<arr.length;i++){
>             if(arr[i] == value) return true;
>         }
>         return false;
>     }
> 
> }
> ```

### 6、写一个Singleton出来。

> 第一种：饱汉模式
>
> ```java
> public class SingleTon {
>     private SingleTon(){    }
>     //实例化放在静态代码块里可提高程序的执行效率，但也可能更占用空间	
>     private final static SingleTon instance = new SingleTon();
>     public static SingleTon getInstance(){
>         return instance;
>     }
> }
> ```
>
> 第二种：饥汉模式
>
> ```java
> public class SingleTon {
> 	private SingleTon(){}	
> 	private static instance = null;//new SingleTon();	
> 	public static synchronized SingleTon getInstance(){
> 		if(instance == null)
> 			instance = new SingleTon();
> 		return instance;
> 	}
> }
> ```
>
> 第三种：用枚举
>
> ```java
> public enum SingleTon{
>     ONE;
> }
> ```
>
> 第三：更实际的应用（在什么情况用单例）
>
> ```java
> public class SequenceGenerator{
>     //下面是该类自身的业务功能代码
>     private int count = 0;
>     public synchronized int getSequence(){
>         ++count;
>     }
>     //下面是把该类变成单例的代码
>     private SequenceGenerator(){}
>     private final static instance = new SequenceGenerator();
>     public static SingleTon getInstance(){
>         return instance;
>     }	
> }
> ```
>
> 第四：
>
> ```java
> public class MemoryDao
> {
>     private HashMap map = new HashMap();
>     public void add(Student stu1){ 
>         map.put(SequenceGenerator.getInstance().getSequence(),stu1);
>     }
>     //把MemoryDao变成单例 
> }
> ```
>
> `Singleton`模式主要作用是保证在`Java`应用程序中，一个类`Class`只有一个实例存在。 
>
> 一般`Singleton`模式通常有几种种形式: 
>
> 第一种形式: 定义一个类，它的构造函数为`private`的，它有一个`static`的`private`的该类变量，在类初始化时实例话，通过一个`public`的`getInstance`方法获取对它的引用,继而调用其中的方法。 
>
> ```java
> public class Singleton { 
>     private Singleton(){} 
>     //在自己内部定义自己一个实例，是不是很奇怪？ 
>     //注意这是private 只供内部调用 
>     private static Singleton instance = new Singleton(); 
>     //这里提供了一个供外部访问本class的静态方法，可以直接访问　　 
>     public static Singleton getInstance() { 
>         return instance; 　　 
>     } 
> } 
> ```
>
>  第二种形式: 
>
> ```java
> public class Singleton { 
>     private static Singleton instance = null; 
>     public static synchronized Singleton getInstance() { 
>         //这个方法比上面有所改进，不用每次都进行生成对象，只是第一次　　　 　 
>         //使用时生成实例，提高了效率！ 
>         if (instance==null) 
>             instance＝new Singleton(); 
>         return instance; 　　
>     } 
> } 
> ```
>
> 其他形式: 
>
> 定义一个类，它的构造函数为`private`的，所有方法为`static`的。 
>
> 一般认为第一种形式要更加安全些 

### 7、递归算法题1

> 一个整数，大于0，不用循环和本地变量，按照n，2n，4n，8n的顺序递增，当值大于5000时，把值按照指定顺序输出来。
> 例：n=1237
> 则输出为：
> 1237，
> 2474，
> 4948，
> 9896，
> 9896，
> 4948，
> 2474，
> 1237，
>
> 提示：写程序时，先致谢按递增方式的代码，写好递增的以后，再增加考虑递减部分。
>
> ```java
> public static void doubleNum(int n){
>     System.out.println(n);
>     if(n<=5000)
>         doubleNum(n*2);
>     System.out.println(n);		
> }
> ```

### 8、递归算法题2

> 第1个人10，第2个比第1个人大2岁，依次递推，请用递归方式计算出第8个人多大？
>
> ```java
> import java.util.Date;
> 
> public class A1 {
>     public static void main(String [] args){
>         System.out.println(computeAge(8));
>     }
> 
>     public static int computeAge(int n){
>         if(n==1) return 10;
>         return computeAge(n-1) + 2;
>     }
> }
> ```

### 9、排序都有哪几种方法？请列举。用JAVA实现一个快速排序。 

>  本人只研究过冒泡排序、选择排序和快速排序，下面是快速排序的代码：
>
> ```java
> public class QuickSort {
>     /**
>      * 快速排序
>      * @param strDate
>      * @param left
>      * @param right
>      */
>     public void quickSort(String[] strDate,int left,int right){
>         String middle,tempDate;
>         int i,j;
>         i=left;
>         j=right;
>         middle=strDate[(i+j)/2];
>         do{
>             while(strDate[i].compareTo(middle)<0&& i<right)
>                 i++; //找出左边比中间值大的数
>             while(strDate[j].compareTo(middle)>0&& j>left)
>                 j--; //找出右边比中间值小的数
>             if(i<=j){ //将左边大的数和右边小的数进行替换 
>                 tempDate=strDate[i];
>                 strDate[i]=strDate[j];
>                 strDate[j]=tempDate;
>                 i++;
>                 j--;
>             }
>         }while(i<=j); //当两者交错时停止
>         if(i<right){
>             quickSort(strDate,i,right);//从
>         }
>         if(j>left){
>             quickSort(strDate,left,j);
>         }
>     }
>     public static void main(String[] args){
>         String[] strVoid=new String[]{"11","66","22","0","55","22","0","32"};
>         QuickSort sort=new QuickSort();
>         sort.quickSort(strVoid,0,strVoid.length-1);
>         for(int i=0;i<strVoid.length;i++){
>             System.out.println(strVoid[i]+" ");
>         }
>     }
> }
> ```

### 10、有数组a[n]，用java代码将数组元素顺序颠倒

> ```JAVA
> import java.util.Arrays;
> 
> public class SwapDemo{
>     public static void main(String[] args){
>         int [] a = new int[]{
>             (int)(Math.random() * 1000),
>             (int)(Math.random() * 1000),
>             (int)(Math.random() * 1000),
>             (int)(Math.random() * 1000),						
>             (int)(Math.random() * 1000)};	
>         System.out.println(a);
>         System.out.println(Arrays.toString(a));
>         swap(a);
>         System.out.println(Arrays.toString(a));		
>     }
>     
>     public static void swap(int a[]){
>         int len = a.length;
>         for(int i=0;i<len/2;i++){
>             int tmp = a[i];
>             a[i] = a[len-1-i];
>             a[len-1-i] = tmp;
>         }
>     }
> }
> ```

### 11．金额转换，阿拉伯数字的金额转换成中国传统的形式如：（￥1011）－>（一千零一拾一元整）输出。

> 去零的代码：
>
> ​	`return sb.reverse().toString().replaceAll("零[拾佰仟]","零").replaceAll("零+万","万").replaceAll("零+元","元").replaceAll("零+","零");`
>
> ```java
> public class RenMingBi {
>     private static final char[] data = new char[]{
>         '零','壹','贰','叁','肆','伍','陆','柒','捌','玖'
>     }; 
>     private static final char[] units = new char[]{
>         '元','拾','佰','仟','万','拾','佰','仟','亿'
>     };
>     public static void main(String[] args) {
>         System.out.println(
>             convert(135689123));
>     }
> 
>     public static String convert(int money)
>     {
>         StringBuffer sbf = new StringBuffer();
>         int unit = 0;
>         while(money!=0)
>         {
>             sbf.insert(0,units[unit++]);
>             int number = money%10;
>             sbf.insert(0, data[number]);
>             money /= 10;
>         }
>         return sbf.toString();
>     }
> }
> ```

