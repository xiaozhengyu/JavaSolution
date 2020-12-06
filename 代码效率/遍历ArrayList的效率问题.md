# 遍历ArrayList的效率问题

---

“如果需要遍历List集合元素，对于 ArrayList、Vector 集合，则应该使用随机访问方法（get）来遍历集合元素，这样性能更好。对于 LinkedList 集合，则应该采用迭代器（Iterator）来遍历集合元素”——《疯狂Java讲义》

## 验证

```java
package com.learn.java;

import java.util.ArrayList;
import java.util.List;

/**
 * @author xzy
 * @date 2020-03-25 18:56
 * 说明：测试
 */
public abstract class Main {
    public static void main(String[] args) throws Exception {

        int size = 100000;
        List<Integer> integerList = new ArrayList<>(size);
        for (int i = 0; i < size; i++) { integerList.add(i); }

        // 迭代器访问（为了避免偶然性，取10000次的平均值）
        long count = 0;
        for (int j = 0; j < 10000; j++) {
            long startTime = System.nanoTime();
            integerList.forEach(integer -> {});
            count += System.nanoTime() - startTime;
        }
        System.out.println(count/10000);

        
        // get访问（为了避免偶然性，取10000次的平均值）
        count = 0;
        for (int j = 0; j < 10000; j++) {
            long startTime = System.nanoTime();
            for (int i = 0; i < size; i++) { integerList.get(i); }
            count += System.nanoTime() - startTime;
        }
        System.out.println(count/10000);

        
        // for-each访问（为了避免偶然性，取1000次的平均值）
        count = 0;
        for (int j = 0; j < 10000; j++) {
            long startTime = System.nanoTime();
            for (Integer integer : integerList) {}
            count += System.nanoTime() - startTime;
        }
        System.out.println(count/10000);
    }
}
```

控制台输出：

```
113561
85764
239562
```

## 结论

1. 遍历 ArrayList 时使用随机访问的方式确实会快很多。

    ArrayList 和 Vector 是基于数组进行实现的，它们的 get() 方法其实就是直接通过索引获取数组元素。如果使用迭代器进行遍历，首先需要创建一个迭代器，然后还需要不断的调用hasNext()、next()方法。显然，使用 get() 方法遍历集合中的元素比使用迭代器更直接、更迅速。

    ```java
    public class ArrayList<E> extends AbstractList<E> 
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{
        /**
         * The array buffer into which the elements of the ArrayList are stored.
         * The capacity of the ArrayList is the length of this array buffer. Any
         * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
         * will be expanded to DEFAULT_CAPACITY when the first element is added.
         */
        transient Object[] elementData;
        
        /**
         * Returns the element at the specified position in this list.
         *
         * @param  index index of the element to return
         * @return the element at the specified position in this list
         * @throws IndexOutOfBoundsException {@inheritDoc}
         */
        public E get(int index) {
            Objects.checkIndex(index, size);
            return elementData(index);
        }
        
        /**
         * Returns an iterator over the elements in this list in proper sequence.
         *
         * <p>The returned iterator is <a href="#fail-fast"><i>fail-fast</i></a>.
         *
         * @return an iterator over the elements in this list in proper sequence
         */
        public Iterator<E> iterator() {
            return new Itr();
        }
        
        /**
         * An optimized version of AbstractList.Itr
         */
        private class Itr implements Iterator<E> {
            int cursor;       // index of next element to return
            int lastRet = -1; // index of last element returned; -1 if no such
            int expectedModCount = modCount;
    
            // prevent creating a synthetic constructor
            Itr() {}
    
            public boolean hasNext() {
                return cursor != size;
            }
    
            @SuppressWarnings("unchecked")
            public E next() {
                checkForComodification();
                int i = cursor;
                if (i >= size)
                    throw new NoSuchElementException();
                Object[] elementData = ArrayList.this.elementData;
                if (i >= elementData.length)
                    throw new ConcurrentModificationException();
                cursor = i + 1;
                return (E) elementData[lastRet = i];
            }
    
            public void remove() {
                if (lastRet < 0)
                    throw new IllegalStateException();
                checkForComodification();
    
                try {
                    ArrayList.this.remove(lastRet);
                    cursor = lastRet;
                    lastRet = -1;
                    expectedModCount = modCount;
                } catch (IndexOutOfBoundsException ex) {
                    throw new ConcurrentModificationException();
                }
            }
            
            final void checkForComodification() {
                if (modCount != expectedModCount)
                    throw new ConcurrentModificationException();
            }
        }
    }
    ```

2. 使用 for-each 比使用 forEach() 更慢。

    for-each 其实是一个语法糖，程序编译的时候，编译器会将使用 for-each 的代码转换成使用 Iterator 的代码。

    编译前文的代码，然后使用反编译器对字节码进行反编译：

    ```java
    PS D:\学习笔记\StudyNotes\Java\反编译\CFR反编译> java -jar .\cfr-0.149.jar .\Main.class  --collectioniter false
    /*
     * Decompiled with CFR 0.149.
     */
    package com.learn.java;
    
    import java.util.ArrayList;
    import java.util.Iterator;
    
    public abstract class Main {
        public static void main(String[] args) throws Exception {
            long startTime;
            int j;
            int size = 100000;
            ArrayList<Integer> integerList = new ArrayList<Integer>(size);
            for (int i = 0; i < size; ++i) {
                integerList.add(i);
            }
            long count = 0L;
            for (j = 0; j < 10000; ++j) {
                startTime = System.nanoTime();
                integerList.forEach(integer -> {});
                count += System.nanoTime() - startTime;
            }
            System.out.println(count / 10000L);
            count = 0L;
            for (j = 0; j < 10000; ++j) {
                startTime = System.nanoTime();
                for (int i = 0; i < size; ++i) {
                    integerList.get(i);
                }
                count += System.nanoTime() - startTime;
            }
            System.out.println(count / 10000L);
            // 可以看到，使用 for-each 的代码已经被转换成了使用 Iterator。
            count = 0L;
            for (j = 0; j < 10000; ++j) {
                startTime = System.nanoTime();
                Iterator iterator = integerList.iterator();
                while (iterator.hasNext()) {
                    Integer n = (Integer)iterator.next();
                }
                count += System.nanoTime() - startTime;
            }
            System.out.println(count / 10000L);
        }
    }
    ```