---
title: java中集合使用中遇到的-坑
date: 2017-04-25 14:27:21
categories: java
tags: java
---
平时开发过程中用的最多的莫过于基本类型、包装类、集合等等了，但是最近在使用集合时，偶尔使用不当会出现一些想不到的问题(bug)；为什么使用不当呢，因为我们对集合的操作都是使用JDK原生的API方法进行操作的，那么之所以使用不当，是因为对jdk源码中的api方法的不了解所致，下面就具体罗列一下具体的坑吧。

**1、数组之间的拷贝-Arrays.copyOf()**

>我们直接用java.util.Arrays工具类的copyOf方法进行数组拷贝

```java
	
class Student implements Cloneable{
	
	private String studentId;
	private String studentName;
	
	
	public Student(String studentId, String studentName) {
		super();
		this.studentId = studentId;
		this.studentName = studentName;
	}
	@Override
	public String toString() {
		return "Student [studentId=" + studentId + ", studentName=" + studentName + "]";
	}
	public String getStudentId() {
		return studentId;
	}
	public void setStudentId(String studentId) {
		this.studentId = studentId;
	}
	public String getStudentName() {
		return studentName;
	}
	public void setStudentName(String studentName) {
		this.studentName = studentName;
	}
	
	@Override
	protected Student clone() throws CloneNotSupportedException {
		Student stu = null;
		try{
			stu = (Student) super.clone();
		}catch(Exception e){
			e.printStackTrace();
		}
		return stu;
	}
	
}

```

```java

	int[] intArray = {1,2,3,4,5};
	int[] intTempArray = Arrays.copyOf(intArray, intArray.length);
	intTempArray[4] = 6;
	System.out.println("intArray:"+Arrays.toString(intArray));
	System.out.println("intTempArray:"+Arrays.toString(intTempArray));
	
	double[] doubleArray = {1.0,2.0,3.0,4.0,5.0};
	double[] doubleTempArray = Arrays.copyOf(doubleArray, doubleArray.length);
	doubleTempArray[4] = 6.0;
	System.out.println("doubleArray:"+Arrays.toString(doubleArray));
	System.out.println("doubleTempArray:"+Arrays.toString(doubleTempArray));
	
	String[] strArray = {"a","b","c","d","e"};
	String[] strTempArray = Arrays.copyOf(strArray, strArray.length);
	strTempArray[4] = "F";
	System.out.println("strArray:"+Arrays.toString(strArray));
	System.out.println("strTempArray:"+Arrays.toString(strTempArray));
	
	Student[] studentArray = {new Student("1", "A"),new Student("2", "B"),new Student("3", "C")};
	Student[] studentTempArray = Arrays.copyOf(studentArray, studentArray.length);
	studentTempArray[2].setStudentName("D");
	System.out.println("studentArray:"+Arrays.toString(studentArray));
	System.out.println("studentTempArray:"+Arrays.toString(studentTempArray));

```

以上代码只是最基本的数组实例，我们直接看最后一段代码，声明了Student类型的数组，然后通过Arrays.copyOf方法进行拷贝处理，得到一个新的Student类型的数组，然后我们对新得到的Student类型的数组中的对象进行重新赋值操作，然后我们查看以下输出结果：

```java

	intArray:[1, 2, 3, 4, 5]
	intTempArray:[1, 2, 3, 4, 6]
	doubleArray:[1.0, 2.0, 3.0, 4.0, 5.0]
	doubleTempArray:[1.0, 2.0, 3.0, 4.0, 6.0]
	strArray:[a, b, c, d, e]
	strTempArray:[a, b, c, d, F]
	str1Array:[a, b, c, d, e]
	str1TempArray:[a, b, c, d, F]
	studentArray:[Student [studentId=1, studentName=A], Student [studentId=2, studentName=B], Student [studentId=3, studentName=D]]
	studentTempArray:[Student [studentId=1, studentName=A], Student [studentId=2, studentName=B], Student [studentId=3, studentName=D]]

```

可以看到上面的数组拷贝示例中，不管对老数组中的值进行更改还是对新数组中的值进行更改，都不会相互影响；而最后的Student类型的数组示例中，不管对老数组(studentArray)中的值进行更改还是对新数组(studentTempArray)中的值进行更改，都会相互影响；比如我们对拷贝生成的新数组studentTempArray中的索引为2的对象的学生姓名重新赋值，然后老数组中对应的索引的对象的学生姓名也被修改了；其实这就是所谓的数组浅拷贝，在使用Arrays.copyOf方法时要特别注意。

**2、基本类型的数组转换为集合会出现数据类型未知错误-Arrays.asList()**

平时开发中，也经常会遇到把一个Array数组转换成List集合，Arrays.asList(data)；但是这个方法如果使用不当，也会出现问题

```java

	int[] intArray = {1,2,3};
	List list= Arrays.asList(intArray);
	System.out.println("size:"+list.size()+" list:"+list.toString());
	
```
上面的代码很简单，就是把intArray数组转换成List，其输出结果如下：

>size:1 list:[[I@2a139a55]

看到输出结果是不是感觉有点茫然，list的长度应该为3才对啊，并且应该直接输出其中的元素才对啊。

首先我们看看asList的源码：

```java

	public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
    }
	
```

>asList方法输入的是一个泛型变长参数，我们知道基本类型是不能泛型化的，也就是说8个基本类型不能作为泛型参数，要想作为泛型参数就必须使用其所对应的包装类型，那前面的例子传递了一个int类型的数组,在asList方法中，直接把这个数组当作了一个参数进行处理；然后打印的集合内容是一个对象串，并不是其中的元素，这是为什么呢，我们点击上面的asList方法中的new ArrayList这个类源码中看一下：

```java

	/**
     * @serial include
     */
    private static class ArrayList<E> extends AbstractList<E>
        implements RandomAccess, java.io.Serializable
    {
        private static final long serialVersionUID = -2764017481108945198L;
        private final E[] a;

        ArrayList(E[] array) {
            a = Objects.requireNonNull(array);
        }

        @Override
        public int size() {
            return a.length;
        }

		.......省略其他代码		

```

查看ArrayList源码可以看到，其是Arrays类的一个静态内部类，并不是我们平时声明List集合时的ArrayList类，只不过他们都继承了AbstractList这个类，所以List list= Arrays.asList(intArray);这句代码把Arrays的静态内部类赋值给Lits实例变量才不至于报异常。并且我们可以看到ArrayList内部类中有一个泛型的数组常量:
>private final E[] a；

所以我们也可以通过下面代码获取转换后的list集合中对应索引的数据。

>System.out.println("size:"+list.size()+" list:"+((int[])list.get(0))[1]);
>
>size:3 list:1

好了，重新回到刚才那个问题，已经看到asList(T... a) 方法的入参是一个泛型参数，所以我们需要传递对应的基本类型的包装类或者其他对象参数，示例如下：

```java

	Integer[] integerArray = {1,2,3};
	List list= Arrays.asList(integerArray);
	System.out.println("size:"+list.size()+" list:"+list.toString());

```
我们把上面的数组基本类型转换为其对应的包装类之后，就正常了！输出如下：

>size:3 list:[1, 2, 3]

总结：使用Arrays.asList方法转换数组为list时，入参的类型一定不能是基本数据类型，需要传递包装类或者其它非基本数据类型

```java

	String[] StringArray = {"a","b","c"};
	List list= Arrays.asList(StringArray);
	System.out.println("size:"+list.size()+" list:"+list.toString());
		
	Student[] studentArray = {new Student("1","A"),new Student("2","B")};
	List listStu = Arrays.asList(studentArray);
	System.out.println("size:"+listStu.size()+" list:"+listStu.toString());

```

上面传递的非基本数组类型输出如下：

>size:3 list:[a, b, c]
>
>size:2 list:[Student [studentId=1, studentName=A], Student [studentId=2, studentName=B]]


**3、数组转换为集合后不能调用add方法新增元素-Arrays.asList()**

接着第2个坑的另一个使用方法进行说明，一般来讲我们把一个数组转换为集合之后，肯定要对集合进行增、删、改、查操作，那么我首先演示一下集合的增加操作：

```java
	
	String[] StringArray = {"a","b","c"};
	List list= Arrays.asList(StringArray);
	list.add("d");
	System.out.println("size:"+list.size()+" list:"+list.toString());

```

上面代码输出结果其实抛异常了，是下面这样：

>Exception in thread "main" java.lang.UnsupportedOperationException

是不是很纳闷，既然可以正常把数组转换为集合，那么为什么不能向集合中添加数据呢？其实上面已经说过了，调用Arrays.asList方法后，返回的其实是Arrays的内部类，并不是我们平时声明集合时(new ArrayList() )的真正意义上的ArrayList类；之所以可以把Arrays的内部类ArrayList赋值给List类变量，是因为他们有同一个父类-AbstractList，然而List list变量指向的并不是真正意义上的集合。

所以我们继续再看看Arrays的内部类ArrayList源码会发现，其中只有以下几个方法：

>public int size()  #元素数量

>public Object[] toArray() #转化为数组，实现了数组的浅拷贝

>public <T> T[] toArray(T[] a) 

>public E get(int index) #获得指定索引的元素

>public E set(int index, E element) #设置制定索引元素

>public int indexOf(Object o) #查找元素的索引值

>public boolean contains(Object o) #查找元素是否存在

>public Spliterator<E> spliterator() #定义分割器

>public void forEach(Consumer<? super E> action) #遍历

>public void replaceAll(UnaryOperator<E> operator)

>public void sort(Comparator<? super E> c)

是不是没有发现add方法，add方法只不过是抽象类-AbstractList的方法,我们再进入该抽象类的方法中可以看到直接抛出了一个异常，刚好就是刚才我们调用add方法时的异常，OK，一切真相大白了。

```java

	public void add(int index, E element) {
        throw new UnsupportedOperationException();
    }

```

总结，我们通过调用Arrays.asList方法把数组转换为集合后，返回的集合并不是真正意义上的ArrayList，而是Arrays的静态内部类，所以并不能调用add方法向集合中添加元素，但是可以调用内部类自己的方法从集合中获取集合元素，设置集合中的元素等操作


**4、对生成的子列表修改元素后源集合也会被修改-subList()**

平时开发中，将一个大的list集合截取成符合条件的集合也是很常见的事情，那么我们一般直接通过subList方法去截取，只要api有的方法我们就直接用嘛，避免重复造轮子。

```java

	List list1 = Lists.newArrayList("a","b","c");
	List list2 = list1.subList(0, list1.size());
	list2.add("d");
		
	System.out.println("list1长度:"+list1.size()+" list1元素:"+list1);
	System.out.println("list2长度:"+list2.size()+" list2元素:"+list2);
		
	List list3 = Lists.newArrayList(1,2,3);
	List list4 = list3.subList(0, list3.size());
	list4.add(4);
	System.out.println("list3长度:"+list3.size()+" list3元素:"+list3);
	System.out.println("list4长度:"+list4.size()+" list4元素:"+list4);

```

上面的代码输出为如下：

>list1长度:4 list1元素:[a, b, c, d]
>
>list2长度:4 list2元素:[a, b, c, d]
>
>list3长度:4 list3元素:[1, 2, 3, 4]
>
>list4长度:4 list4元素:[1, 2, 3, 4]

可以看到一个现象，我们向通过subList方法生成的新集合中添加元素后，新集合和老集合都发生了变化，分析一下subList方法，可以发现SubList其实是ArrayList类的内部类，并且我们可以看到其定义的add方法如下：

```java

	private class SubList extends AbstractList<E> implements RandomAccess {
        private final AbstractList<E> parent;
        private final int parentOffset;
        private final int offset;
        int size;

        SubList(AbstractList<E> parent,
                int offset, int fromIndex, int toIndex) {
            this.parent = parent;
            this.parentOffset = fromIndex;
            this.offset = offset + fromIndex;
            this.size = toIndex - fromIndex;
            this.modCount = ArrayList.this.modCount;
        }

        public E set(int index, E e) {
            rangeCheck(index);
            checkForComodification();
            E oldValue = ArrayList.this.elementData(offset + index);
            ArrayList.this.elementData[offset + index] = e;
            return oldValue;
        }

        public E get(int index) {
            rangeCheck(index);
            checkForComodification();
            return ArrayList.this.elementData(offset + index);
        }

        public int size() {
            checkForComodification();
            return this.size;
        }

        public void add(int index, E e) {
            rangeCheckForAdd(index);
            checkForComodification();
            parent.add(parentOffset + index, e);
            this.modCount = parent.modCount;
            this.size++;
        }

```

可以看到是针对parent进行的add操作，其实parent就是调用者(源集合)，并且它返回的SubList类也是AbstractList的子类，其所有的get、set、add、remove等都是在原始列表上的操作，并没有重新定义集合，从而可以说明对元素的操作其实是在源集合上进行的，所以对新截取的集合中元素的操作也就直接同步反映到源集合中。

总结：subList产生的列表只是一个源集合的视图概念，所有对生成的子集合中元素的修改操作都会直接作用于源集合


**5、对生成子列表添加元素操作会抛并发修改异常-subList()**

```java

	List list1 = Lists.newArrayList("a","b","c");
	List list2 = list1.subList(0, list1.size());
	list1.add("d");
		
	System.out.println("list1长度:"+list1.size()+" list1元素:"+list1);
	System.out.println("list2长度:"+list2.size()+" list2元素:"+list2);

```

上面的示例中，我们通过subList方法获取子集合list2后，对源集合list1进行元素添加，然后输出如下（报异常）：

>list1长度:4 list1元素:[a, b, c, d]
>
>Exception in thread "main" java.util.ConcurrentModificationException

怎么报了一个并发修改异常呢，我们点击subList类可以看到其有一个add方法

```java

	 public void add(int index, E e) {
        rangeCheckForAdd(index); 
        checkForComodification(); //检测判断当前修改计数器是否与子列表生成时一致
        parent.add(parentOffset + index, e);
        this.modCount = parent.modCount;
        this.size++;
    }

```

进入checkForComodification方法源码可以看：

```java
	
	private void checkForComodification() {
       if (ArrayList.this.modCount != this.modCount)
         throw new ConcurrentModificationException();
    }

```
上面的方法主要看if分支判断条件，其实subList中的add方法中添加了修改次数比较判断，这个modCount变量是ArrayList中的一个变量，即每次调用arrayList类的add方法时，该变量都会自增操作；当我们调用subList方法时，subList对应的内部类中的构造方法就自己初始化一个自己的modeCount值，并且是直接把源集合的modeCount变量赋值给自己的，所以调用subList构造方法后，再次针对源集合list调用add方法添加元素时，就必然会使modCount自增操作，那么自增后，再看上面的代码checkForComodification()方法中的if判断条件就为true了，那么就抛出了并发修改异常了。

注：再使用subList生成子列表后，不要再修改源集合列表数据，最好保持原列表的只读状态(通过Collections.unmodifiableList)。