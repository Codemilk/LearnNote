

# 内部类

* 可以将一个类定义在另一个类的定义内部，这就是内部类

## 创建内部类

```java
package Internal;

/**
 * @author lenovo
 * 连接到外部类
 */
class CiteinternalClass{

    CiteinternalClass(int i){
        System.out.println("CiteinternalClass构造器："+i);
    }

    void say(int i){
        System.out.println("CiteinternalClass成员方法"+i);
    }
}

public class LinkExternal {

    private int i;

    /**组合的引用类*/

    CiteinternalClass internalClass;

    class internalClass{

        internalClass(int i){
            System.out.println("internalClass构造器："+i);
        }
    }

    public LinkExternal(int i) {
        this.i = i;

    }

    public  void link(){
        //引用类创建对象
        internalClass=new CiteinternalClass(i);
        //引用类
        internalClass.say(i);
        //内部类
        new internalClass(i);
    }

    public static void main(String[] args) {
        new LinkExternal(1).link();

    }

}

```

从上面代码我们可以知道：当你生成一个内部类对象的时候，此对象与制造它的外围类会形成一种联系，内部类可以**直接**访问外围类的所有元素

## 内部类与向上转型

```java
package Internal;

interface BookingFactory{
    public void book();
}

interface BookingService{
    public void bookService();
}
public class internal_tranform {

    private  class localBookingFactory implements BookingFactory{
        @Override
        public void book() {
            System.out.println("localBookingFactory");
        }
    }

    protected class RemoteBookingFactory implements BookingService{

        @Override
        public void bookService() {
            System.out.println("RemoteBookingFactory");
        }
    }

    public localBookingFactory getLocalBookingFactory(){
        return new localBookingFactory();
    }

    public RemoteBookingFactory getRemoteBookingFactory(){
        return new RemoteBookingFactory();
    }

    public static void main(String[] args) {
        //我们使用了接口，我们封装了内部类，也实现了多态，这里可以声明这个私有类localBookingFactory，因为main方法本身就是类的方法,不要把private搞乱哦
        internal_tranform.localBookingFactory e = new internal_tranform().getLocalBookingFactory();
        e.book();
        BookingFactory example=new internal_tranform().getLocalBookingFactory();
        example.book();

    }
}

class test{
    public static void main(String[] args) {
//        这里会出现报错你，因为本身localBookingFactory类就是私有的内部类，只有在外部(内部类的外部类)类下才可以访问到
//        internal_tranform.localBookingFactory e = new internal_tranform().getLocalBookingFactory();
//        e.book();
//        但是你可以通过向上转型为接口来获取
        BookingFactory bookingFactory=new internal_tranform().getLocalBookingFactory();
        bookingFactory.book();
    }
}
```

我们从上面的代码可以看出当我们将内部类定义为private，protected的时候，除非在我们的类中，别的地方不可以被使用，这就造成很好的封装性，隐藏了具体实现的细节（我们并不知道实现的是那一个类，只知道接口类型），更加根本性的不依赖于类型（只要有接口就可以，通过调用接口，向上转型），类的内部互相调用非常安全且可扩展

**注意：**

对main方法而言，虽然写在类中，它是游离于任何类之外的，因此某类的非静态内部类对它而言是不直接可见的，也就无法直接访问 。

1：非静态内部类，必须有一个外部类的引用才能创建。

2：在外部类的非静态方法中，因为有隐含的外部类引用this，所以可以直接创建非静态内部类。

3：在外部类的静态方法中，因为没有this，所以必须先获得外部类引用，然后创建非静态内部类。

4：静态内部类，不需要外部类引用就可以直接创建。

5：同时静态的内部类，也不能直接访问外部类的非静态方法。

6：由此可以推测，非静态内部类之所以可以直接访问外部类的方法，是因为创建非静态内部类时，有一个隐含的外部类引用被传递进来。

```java
public class test {

     int i=1;

     class Human{

    }

    class man extends Human{

    }

    class women extends Human{

    }

    public women giveWomen(){
        return new women();
    }

    void sayHello(Human human){
        System.out.println("human");
    }

    void sayHello(man man){
        System.out.println("man");
    }
    void sayHello(women women){
        System.out.println("women");
    }


    public void s(){
        women women = new women();
    }

    public static void main(String[] args) {

        test test=new test();
//        这两种方法
        Human human = test.new Human();
        women women = test.giveWomen();


    }
}

```



## 方法和作用域内的内部类

### 1.方法中的内部类

```java
package Internal;

interface Des{ }

public class Internal {

    /**在方法里面的内部类，只有方法方法的作用域可以访问到这个类*/
      public Des getPDes(){
          class PDes implements Des {
              
              private String label;
              PDes(String label){
                  this.label = label;
              }
              
              public void readLabel() {
                  System.out.println(label);
              }
          }
          return new PDes("PDes");
  
      }
}
```

​                                                                                                                                                                                                                                                                                                                                                                                                                                        

### 2.匿名内部类

```java
package Internal;

interface Des{
    default String say(){
        System.out.println("hehe");
        return "Des";
    }
}
/**匿名内部类*/
class Utils{

    public Des  getNonameDes(){
        return new Des() {
            @Override
            public String say() {
                return "InternalDes";
            }
        };

    }

    public static void main(String[] args) {
        Des nonameDes = new Utils().getNonameDes();
        System.out.println(nonameDes.say());
    }
}

```

这个过程其实就是“创建一个继承自X的匿名类对象，通过new 的方式返回引用”

#### 匿名内部类构造器

* 匿名内部类可以有构造器，但一定没有命名构造器，因为根本就没名字

* 当你匿名内部类需要引用外部定义的成员，他必须是final修饰的。

* 匿名内部类不可有有静态成员 ，我觉得是，本身他就是一个具体的实现过程了你大可以在你的类里面定义，但是匿名内部类本身就是一个没有名字的具体实现，所以不可以加入静态修饰

  

```java
/**匿名内部类构造器*/
abstract class Base{

    public Base(int x) {
        System.out.println(x);
    }

   abstract public String readBase();

}
class Utils{

    public Base getNonameBase(final int a,int x){
    //这里就相当于将参数传给父类的构造器，他不会在当前内部类使用，而是传回基类Base中
        return new Base(x) {

            private int b=a;

            @Override
            public String readBase() {
                return ""+a;
            }
        };
    }



    public static void main(String[] args) {
        Base nonameBase = new Utils().getNonameBase(1, 1);

        System.out.println(nonameBase.readBase());
    }
}
```

**注意：实例初始化的实际效果就是构造器。当然受到了限制--你不能重载实例初始化的方法，也就是说它在充当匿名内部类的时候构造器只能有一个**

所以对于匿名内部类，你可以有两个选择，扩展类（类也是一个类）还是实现接口，就算实现了接口，也只能是一个接口

https://www.jianshu.com/p/df18858d0378