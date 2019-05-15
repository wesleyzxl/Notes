1. 按值传递和按引用传递

   按值传递是指在方法调用时，传递的参数是实参值的副本。按引用传递是指在方法调用时，传递的参数是实参的引用，也可以理解为实参所对应的内存空间的地址

   ```java
   public class Test{
       public static void testPassParameter(StringBuffer ss1, int n) {
           ss1.append(" world");
           n = 10;
       }
       
       public static void main(String... args) {
           int i = 1;
           StringBuffer s1 = new StringBuffer("hello");
           testPassParameter(s1, i);
           
           System.out.println(s1); // hello world
           System.out.println(i); // 1
       }
   }
   ```

   这里的StringBuffer是按引用传递，int是按值传递。

   引用传递本质上还是值传递，StringBuffer按引用传递时传递时s1的地址值，方法调用时是调用s1的地址值的副本（设为ss1），ss1的地址值和s1一样。

   s1的值为地址值（可以理解为"hello"的地址值，或者"hello"的引用）

   如果在方法的调用过程中通过ss1（字符串的引用或地址）来修改字符串的内容，因为s1与ss1指向同一个字符串，所以通过ss1的字符串的修改对s1也是可见的。但是方法中对ss1的值修改对s1没有影响，例如：

   ```java
   public class Test{
       public static void testPassParameter(StringBuffer ss1) {
           ss1 = "world";
       }
       
       public static void main(String... args) {
           StringBuffer s1 = new StringBuffer("hello");
           testPassParameter(s1);
           
           System.out.println(s1); // hello
       }
   }
   ```

   

2. Java语言中的方法属于类中的成员。——这种说法是错的。Java中的方法有两种：静态方法（类的方法）和非静态方法（实例的方法）。所以这种说法错误，因为方法有可能属于实例成员，而不属于类成员。

   另外，方法必定隶属于某一类的说法是正确的。

   

3. ```java
   public class Test{
       public static void main(String... args) {
           class A {
               public int i = 3;
           }
           
           Object o = (Object) new A();
           A a = (A) o;
           
           System.out.println("i = " + a.i);
       }
   }
   ```

   输出结果为i = 1，不会报错

4. Math类中的取整方法

   ```java
   static double ceil(double a); // 返回大于等于a的最小整数
   static double floor(double a); // 返回小于等于a的最大整数
   static double rint(double a); // 四舍五入
   static long round(double a); // 四舍五入
   static int round(float a); // 四舍五入
   ```

5. Java中goto是保留关键字，但是没有goto语句，也没有任何使用goto关键字的地方。在特定情况下，通过特定手段可以实现goto的功能。

6. 

