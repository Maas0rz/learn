​		学习 Java 时遇到这样一段代码：

```java
import java.util.*;

public class Experiment{
    public static void main(String[] args){
        ArrayList list=new ArrayList();
        list.add(1);
        list.add(2);
        list.add(3);
        Iterator it=list.itrator();
        while(it.hasNext()){
            Object obj=it.next();
            System.out.println(obj);
        }
    }
}
```

其中 `Object obj=it.next();` 让我感到很疑惑，为什么能将 int 类型的值直接赋给 Object 类的实例？

​		查阅资料发现，实际上此处进行了隐式装箱，即实际上执行的是 `Object obj=Integer.valueOf(it.next());`。因为一个是基本数据类型，一个是引用数据类型，原则上是 obj 是无法直接接收 it.neat() 的值的，所以需先将 it.next() 装箱为包装类，达到与 obj 相同的数据类型，这样 obj 才可直接接收。

​		扩展开来，Object 类实例能直接接收基本数据类型的一维数组，如：

```java
int[] array={1, 2, 3};
Object obj=array;
```

是可以的。Object[] 能直接接收引用数据类型的的一维数组，特别注意的是能直接接收基本数据类型的二维数组（可看成数组元素是一维数组的一维数组），但不能直接接收基本数据类型的一维数组，因为两者的数组元素的数据类型不相同，需手动装箱：

```java
int[] array={1, 2, 3};
Object[] obj=new Object[3];
for(int i=0; i<3; i++){
    obj[i] = array[i];
}
```

