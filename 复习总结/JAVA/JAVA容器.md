## JAVA容器

主要总结Map,List,Set

![avatar](容器.png)

### transient

在JDK源代码中经常会发现容器的内部变量使用transient修饰，这个关键字的作用是使被修饰的变量不被序列化，主要目的是为了节约磁盘空间，因为容器中的数组长度通常是大于实际存储的值的长度。并且JDK重写了writeObject来进行序列化，只保存实际存储的内容。

### List

在JAVA中常被问到的List类型右Vector,ArrayList和LinkedList

#### ArrayList

arrayList底层是普通数组，默认容量为 10,每次扩容会变成原来的1.5倍。为实现泛型/多态，底层使用Object作为数组类型。手动实现代码如下：

~~~java
public class myArrayList<E>{
    private Object []elements;
    private int size;
    private static final int defaultSize = 10;
    private static final Object[] defaultArray={}
    public myArrayList(int initialSize){
        if(initialSize<0){
            throw new Exception("List长度不能是负数");
        }else if(initialSize==0){
            this.element=defaultArray;
        }else{
            this.element= new Object[initialSize]
        }
    }
    public myArrayList(){
        this.elements = defaultArray;
    }
    public boolean add(Object o){
        if(this.size == elements.length){
            Object[] newElements=new Object[1.5*this.size];
            System.arraycopy(this.elements,0,newElements,0,size);
            this.elements = newElements;
        }
        elements[size++]=o;
        return true;
    }
    public E remove(int index){
        rangeCheck(indedx);
        Object oldValue = elements[index];
        int numMoved = size-index-1;
        if(numMoved>0){
            System.arraycopy(elements,index+1,elements,index,numMoved);
        }
        elements[--size]=null;
        return (E)oldValue;
    }
    public boolean remove(Object o){
        if(null==o){
            for(int i=0;i<size;i++){
                if(null==elements[i]){
                    remove(i);
                    return true;
                }
            }
        }else{
            for(int i=0;i<size;i++){
                if(elements[i].equals(o)){
                    remove(i);
                    return true;
                }
            }
        }
        return false;
    }
    public void rangeCheck(int index){
        if(index<0||index>=size){
            throw new IndexOutOfBoundsException(""+index);
        }
    }
} 
~~~

#### LinkedList

LinkedList基于双向链表适用于增删频繁且查询不频繁的场景，线程不安全的且适用于单线程。

~~~java

public class LinkedList<E>{
    private int size=0;
    private Node<E> first;
    private Node<E> last;
    private static class Node<E>{
        E item;
        Node<E> next;
        Node<E> prev;
        //构造函数略，就是三个属性都赋值
    }
    public void checkPositionIndex(int index){
        if (!(index>=0 && index<size)){
            throw new IndexOutOfBoundsException(index.toString());
        }
    }
    public boolean add(E e){
        linkLast(e);
        return true;
    }
    void linkLast(E e){
        final Node<E> l =last;
        final Node<E> newNode = new Node<>(l,e,null);
        last = newNode;
        if(l==null)
            first = newNode;
        else
            l.next=newNode;
        size++;
    }
    Node<E> node(int index){
        //当index大于size的一半时，从后向前查找，否则从前向后
        if(index<(size>>1)){
            Node<E> x =first;
            for(int i=0;i<index,i++)
                x =x.next;
            return x;
        }else{
            Node<E> x = last;
            for(int i=size-1;i>index;i--)
                x=x.prev;
            return x;
        }
    }
    void linkBefore(E e,Node<E> succ){
        
    }
}
~~~

