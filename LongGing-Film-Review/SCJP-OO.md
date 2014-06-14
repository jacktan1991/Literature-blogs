##以实例变量为引子
在class中，声明了一个变量`String str;`，则`str`成为这个class的实例变量，此时如果再声明一个变量`int str;`，即不符合逻辑，也通不过Java的编译（相信其他语言也不会允许这种丧心病狂的存在）。

```java
class XXX{
 String str;	public String str;
 int str;	private int str;
}
```
任何学过甚至有些没学过Java的人都能一眼看出，上述变量的声明彼此互斥，编译通不过。这些浅显的知识有什么用呢？下面卖弄点玄乎的。

我们知道实例变量和实例方法共同构成了class的实例成员，对实例成员唯一的标识，能保证Java对成员资源的精确定位。其中对于实例变量，其变量名就是其作为实例成员的唯一标识，如`str`，不管它是`int`类型还是`String`类型，是`private`修饰还是`public`修饰，其成员标识只跟变量名有关；同理，对于实例方法，如果能确定其唯一标识，其类型和访问修饰都不会影响标识的唯一性。

我把这种分析方法叫“成员标识”模型，规定成员的声明类型称为“成员类型”，成员的修饰成分称为“成员修饰”，成员类型和成员修饰都不做为对成员唯一标识的组成部分。对于实例变量如`str`，其成员标识是str，成员类型就是变量声明类型`String`（非引用类型），成员修饰就是默认修饰符`default`；那么对于实例方法，是不是也是这样呢？

## 成员标识模型中的实例方法
我们知道在class中，如果去掉方法体，作为class成员，方法和变量长得很像，甚至在一些OO语言如C++中，习惯先把声明变量和声明方法放在一起，在后面才写方法体。这样来看，实例方法的返回类型和实例变量的声明类型类似，所以规定实例方法的返回类型为其成员类型；成员修饰复杂一些，除了和实例变量相似的访问修饰符外，实例方法还有抛错，所以规定访问类型和抛错为实例方法的成员修饰。现在作为对成员唯一标识的组成部分，实例方法的“成员标识”是什么呢？如果以方法名作为标识，则class中就不允许同名方法了，显然不符合OO的多态设计。为了保证同名方法的标识不同，我们规定实例方法的方法名+参数结构共同构成其成员标识，这也是Java能够实现重载又能准确访问调用的原理。

为了演示方便，实例方法的成员标识书写方法可采用`<方法名>[参数1,参数2,..]`的形式。
> `public void doStuff(int size) throws Exception{...}`的成员标识就是
>>`doStuff[int]`
>
> `private void doStuff(String str){...}`~~~~
>>`doStuff[String]`
>
> `String doStuff(String str, int size){...}`
>>`doStuff[String,int]`
>
> `public void doStuff() throws IOE, OutBondE{...}`
>>`doStuff[]`

所以`protected String doStuff(int i){...}`的成员标识是`doStuff[int]`，和第一个方法产生标识冲突，会编译报错，尽管他们的返回类型及访问修饰符、抛错都不同。最后，总结为两句话：

>* 实例成员都有唯一的成员标识，任何尝试声明标识相同（但类型不同或修饰不同）的代码将编译报错
>* 实例方法的成员标识规定为方法名+参数结构，方法名本身并不能唯一确定标识



## 对于分析Overloading有什么意义呢？
现在我们回到《SCJP指南》[^SCJP]中对方法重载的讲解，尝试以**成员标识模型**的思路去理解。*The rules for overloading are simlpe:*

>* Overloaded methods **MUST** change the argements list.
>>这个当然，只有同名方法才能被叫Overload，而方法同名只有改变其参数结构`argements list`才不会产生标识冲突。而这一条也是重载的先决条件。
>* Overloaded methods CAN change the return type.
>* Overloaded methods CAN change the access modifier.
>* Overloaded methods CAN declare new or broader checked exceptions.
>>这三点很好理解，在方法重载先决条件满足情况下，其成员标识跟成员类型`return type`和成员修饰`access modifier`、`checked exceptions`毫无瓜葛。
>* A method can be overloaded in the *sameclass* or in a *subclass*. In other words, 
> if class A defines a `doStuff(int i)`method, the subclass B could define a 
> `doStuff(String s)`method without overriding the superclass version that 
> takes an `int`. So two methods with the same name but in different classes 
> can still be considered overloaded, if the subclass inherits one version of the 
> method and then declares another overloaded version in its class definition.
>>这句话有点复杂，还牵涉到Override。但只要抓住上面两点，不难理解`doStuff(int i)`和`doStuff(String s)`是两个标识不同的成员，且是重载的方法。

总之，**成员标识模型**的意义在于，理解和分析Java中的Overloading更为方便。

-----
[^SCJP]: SCJP Sun Certified Programmer for Java 6 (Exam 310-065)。

## 对于分析Overloading是不是也更方便呢？
