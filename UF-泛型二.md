# 泛型二

## 一、类型绑定

### 1、引入
```
class Point<T> {
    private T x;       // 表示X坐标
    private T y;       // 表示Y坐标

    public void setX(T x) {
        this.x = x;
    }

    public void setY(T y) {
        this.y = y;
    }

    public T getX() {
        return this.x;
    }

    public T getY() {
        return this.y;
    }
}

//使用
Point<Integer> p1 = new Point<Integer>();
p1.setX(new Integer(100));
System.out.println(p1.getX());
```

首先，我们要知道一点，任何的泛型变量（比如这里的`T`）都是派生自`Object`，所以我们在填充泛型变量时，只能使用派生自`Object`的类，比如`String,Integer,Double`，等而不能使用原始的变量类型，比如`int,double,float`等。
<br>
那在泛型类`Point<T>`内部，利用泛型定义的变量`T x`能调用哪些函数呢？
<br>
当然只能调用`Object`所具有的函数，因为编译器根本不知道`T`具体是什么类型，只有在运行时，用户给什么类型，才知道是什么类型。编译器唯一能确定的是，无论什么类型，都是派生自`Object`的，所以`T`肯定是`Object`的子类，所以`T`是可以调用`Object`的方法的。
<br>
那么问题又来了，如果我想写一个找到最小值的泛型类；由于不知道用户会传什么类型，所以要写一个接口, 让用户实现这个接口来自已对比他所传递的类型的大小。
```
public interface Comparable<T>{
    public boolean compareTo(T i);
}
```

### 2、类型绑定：extends

#### 2.1、定义
有时候，你会希望泛型类型只能是某一部分类型，比如操作数据的时候，你会希望是`Number`或其子类类型。这个想法其实就是给泛型参数添加一个界限。其定义形式为：
```
<T extends BoundingType>
```
此定义表示T应该是BoundingType的子类型（subtype）。T和BoundingType可以是类，也可以是接口。
<br>
另外注意的是，此处**extend**表示的是子类型，不等同于继承。
<br>
在这里extends后的BoundingType可以是类，也可以是接口，意思是说，T是在BoundingType基础上创建的，具有BoundingType的功能。

#### 2.2、实例：绑定接口
```
public interface Comparable<T> {
    public boolean compareTo(T i);
}

//添加上extends Comparable之后，就可以Comparable里的函数了
public static <T extends Comparable> T min(T...a) {
    T smallest = a[0];
    for(T item:a){
        if (smallest.compareTo(item)){
            smallest = item;
        }
    }
    return smallest;
}
```
这段代码的意思就是根据传进去的T类型数组a，然后调用其中item的compareTo()函数，跟每一项做对比，最终找到最小值。
<br>

>从这段代码也可以看出，类型绑定有两个作用：
>1、对填充的泛型加以限定 
>2、使用泛型变量T时，可以使用BoundingType内部的函数。

这里有一点非常要注意的是，在这句中smallest.compareTo(item)，smallest和item全部都是T类型的，也就是说，compareTo对比的是同一种类型。
然后我们实现一个派生自Comparable接口的类：



#### 参考资料：
[夯实JAVA基本之一——泛型详解(2)：高级进阶](https://blog.csdn.net/harvic880925/article/details/49883589)
