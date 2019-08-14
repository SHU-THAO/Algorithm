# Comparator 接口排序用法

java的比较器有两类，分别是Comparable接口和Comparator接口。

在为***对象数组***进行排序时，比较器的作用非常明显，首先来讲解Comparable接口。

让需要进行排序的对象实现Comparable接口，重写其中的compareTo(T o)方法，在其中定义排序规则，那么就可以直接调用java.util.Arrays.sort()来排序对象数组，实例如下：

``` java
class Student implements Comparable<Student>{  
    private String name;  
    private int age;  
    private float score;  
      
    public Student(String name, int age, float score) {  
        this.name = name;  
        this.age = age;  
        this.score = score;  
    }  
      
    public String toString()  
    {  
        return name+"\t\t"+age+"\t\t"+score;  
    }  
  
    @Override  
    public int compareTo(Student o) {  
        // TODO Auto-generated method stub  
        if(this.score>o.score)//score是private的，为什么能够直接调用,这是因为在Student类内部  
            return -1;//由高到底排序  
        else if(this.score<o.score)  
            return 1;  
        else{  
            if(this.age>o.age)  
                return 1;//由底到高排序  
            else if(this.age<o.age)  
                return -1;  
            else  
                return 0;  
        }  
    }  
}  
  
public class ComparableDemo01 {  
  
    /** 
     * @param args 
     */  
    public static void main(String[] args) {  
        // TODO Auto-generated method stub  
        Student stu[]={new Student("zhangsan",20,90.0f),  
                new Student("lisi",22,90.0f),  
                new Student("wangwu",20,99.0f),  
                new Student("sunliu",22,100.0f)};  
        java.util.Arrays.sort(stu);  
        for(Student s:stu)  
        {  
            System.out.println(s);  
        }  
    }  
}  
```

在上面的程序中，实现了Comparable接口，并重写了compareTo方法，将学生先按成绩由大到小排名，成绩相同时候按照年龄由低到高排序。

执行的结果是

``` java
sunliu 22 100.0
wangwu 20 99.0
zhangsan 20 90.0
lisi 22 90.0
```

但是在设计类的时候，可能没有考虑到让类实现Comparable接口，那么就需要用到另外的一个比较器接口Comparator。

从上面的实例我们可以发现，compareTo(T o)只有一个参数，而Comparator接口中必须要实现的compare(T o1,T o2)就有两个参数。

``` java
package edu.sjtu.ist.comutil;  
  
import java.util.Comparator;  
  
class Student {  
    private String name;  
    private int age;  
    private float score;  
      
    public Student(String name, int age, float score) {  
        this.name = name;  
        this.age = age;  
        this.score = score;  
    }  
  
    public String getName() {  
        return name;  
    }  
    public void setName(String name) {  
        this.name = name;  
    }  
    public int getAge() {  
        return age;  
    }  
    public void setAge(int age) {  
        this.age = age;  
    }  
    public float getScore() {  
        return score;  
    }  
    public void setScore(float score) {  
        this.score = score;  
    }  
  
    public String toString()  
    {  
        return name+"\t\t"+age+"\t\t"+score;  
    }  
  
}  
  
class StudentComparator implements Comparator<Student>{  
  
    @Override  
    public int compare(Student o1, Student o2) {  
        // TODO Auto-generated method stub  
        if(o1.getScore()>o2.getScore())  
            return -1;  
        else if(o1.getScore()<o2.getScore())  
            return 1;  
        else{  
            if(o1.getAge()>o2.getAge())  
                return 1;  
            else if(o1.getAge()<o2.getAge())  
                return -1;  
            else   
                return 0;  
        }  
    }  
      
} 
public class ComparableDemo02 {  
  
    /** 
     * @param args 
     */  
    public static void main(String[] args) {  
        // TODO Auto-generated method stub  
  
        Student stu[]={new Student("zhangsan",20,90.0f),  
                new Student("lisi",22,90.0f),  
                new Student("wangwu",20,99.0f),  
                new Student("sunliu",22,100.0f)};  
        java.util.Arrays.sort(stu,new StudentComparator());  
        for(Student s:stu)  
        {  
            System.out.println(s);  
        }  
    }  
  
}  
```

上面依然是对student对象数组进行排序，用的都是Array.sort方法，不同的是实现comparator接口时，sort方法需要传进来两个参数，即stu对象数组，以及重写的实现了comparator比较方法类。

程序运行的结果和上面是一样的

如果想对不同的同期来进行排序，根据构造函数传入比较器的参数即可。









