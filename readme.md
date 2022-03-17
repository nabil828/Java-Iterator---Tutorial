# Iterator

>  An Iterator is an object that can be used to loop through collections, like ArrayList and HashSet. It is called an "iterator" because "iterating" is the technical term for looping.

Here is an example of the built in iterator in Java

```Java
// Import the ArrayList class and the Iterator class
import java.util.ArrayList;
import java.util.Iterator;

public class Main {
  public static void main(String[] args) {

    // Make a collection
    ArrayList<String> cars = new ArrayList<String>();
    cars.add("Volvo");
    cars.add("BMW");
    cars.add("Ford");
    cars.add("Mazda");

    // Get the iterator
    Iterator<String> it = cars.iterator();

    // Print the first item
    System.out.println(it.next());
  }
}


```


## Looping Through a Collection
To loop through a collection, use the hasNext() and next() methods of the Iterator:

Example

```Java
while(it.hasNext()) {
  System.out.println(it.next());
}
```

## Removing Items from a Collection
Iterators are designed to easily change the collections that they loop through. The remove() method can remove items from a collection while looping.

Example
Use an iterator to remove numbers less than 10 from a collection:

```Java
import java.util.ArrayList;
import java.util.Iterator;

public class Main {
  public static void main(String[] args) {
    ArrayList<Integer> numbers = new ArrayList<Integer>();
    numbers.add(12);
    numbers.add(8);
    numbers.add(2);
    numbers.add(23);
    Iterator<Integer> it = numbers.iterator();
    while(it.hasNext()) {
      Integer i = it.next();
      if(i < 10) {
        it.remove();
      }
    }
    System.out.println(numbers);
  }
}

```

**Note**: Trying to remove items using a for loop or a for-each loop would not work correctly because the collection is changing size at the same time that the code is trying to loop.


---
Now, let us try to **implement** a Stack using an iterator.
We have three options
---
## Option 1 - implement `next()`, `hasNext()`, and `remove()` inside the class

```Java
public class QueueUsingLinkedListWithIterator {

    Node front;
    Node back;

    // for iterator
    int nextPosition = 0; // first item is item #1
    int size = 0;

    void insert( int a ){
        Node new_node = new Node();
        new_node.data = a;

        if(front == null && back == null ){ // Q is empty
            front = new_node;
            back = new_node;
        }else{
            // Q is not empty
            back.next = new_node;
            back = new_node;
        }
        size++;
    }

    boolean hasNext(){
        if(nextPosition < size)
            return true;
        else
            return false;
    }

    Node getNextNode(){
        int count = 0;
        Node nextNode = front;
        while (nextNode != null){
            if(count == nextPosition)
                break;
            count++;
            nextNode = nextNode.next;
        }
        return nextNode;
    }

    Node getPrevNode(){
        int count = 0;
        Node tmpNode = front;
        Node prevNode = null;
        while (tmpNode != null){
            if(count == nextPosition)
                break;
            count++;
            prevNode = tmpNode;
            tmpNode = tmpNode.next;
        }
        return prevNode;
    }

    boolean wasNextCalled = false;
    int next(){
        if(hasNext()){
            Node iteratorCurrentNode = getNextNode();
            nextPosition++;


            wasNextCalled = true;
            return iteratorCurrentNode.data;
        }
        else
            throw new RuntimeException("No more!");

    }

    void iteratorRemove(){
    if(wasNextCalled){
        wasNextCalled = false;

        Node prevNode = getPrevNode();

        Node tmpNode = front;
        Node prevTmp = new Node();
        prevTmp.next = front;
        while(tmpNode != prevNode){
            prevTmp = tmpNode;
            tmpNode = tmpNode.next;
        }

        // change the front if the it is one to be removed
        if(prevTmp.next == front)
            front = prevTmp.next.next;
        else//removal
            prevTmp.next = prevTmp.next.next;

        nextPosition--;

    }else
        throw new RuntimeException("Next() should be called first");

    }

    void remove(){
        front = front.next;
        size--;
    }

    int peek(){
        return front.data;
    }
    public static void main(String[] args) {
        QueueUsingLinkedListWithIterator obj = new QueueUsingLinkedListWithIterator();
//        Queue<Integer> obj = new LinkedList<>();

        obj.insert(6);
        obj.insert(4);
        obj.insert(17);
        obj.insert(3);
//        obj.remove();
       // System.out.println(obj.peek()); // 4

//        while(obj.hasNext()) {
//            System.out.println(obj.next());
//        }

        while(obj.hasNext()) {
            Integer i = obj.next();
            if(i < 10) {
                obj.iteratorRemove();;
            }
        }
    }
}


```

---

## Option 2 - Separate Class Iterator
Implement next(), hasNext(), and remove() outside the class in a separate class

```Java
import java.util.Iterator;

public class SeparateClassIterator implements Iterator<Integer> {

    // for iterator
    int nextPosition = 0; // first item is item #1
    QueueUsingLinkedList aQueue;


    public SeparateClassIterator(QueueUsingLinkedList a){
        this.aQueue = a;
    }

    public boolean hasNext(){
        if(nextPosition < aQueue.getSize())
            return true;
        else
            return false;
    }

    Node getNextNode(){
        int count = 0;
        Node nextNode = aQueue.front;
        while (nextNode != null){
            if(count == nextPosition)
                break;
            count++;
            nextNode = nextNode.next;
        }
        return nextNode;
    }

    Node getPrevNode(){
        int count = 0;
        Node tmpNode = aQueue.front;
        Node prevNode = null;
        while (tmpNode != null){
            if(count == nextPosition)
                break;
            count++;
            prevNode = tmpNode;
            tmpNode = tmpNode.next;
        }
        return prevNode;
    }

    boolean wasNextCalled = false;

    public Integer next(){
        if(hasNext()){
            Node iteratorCurrentNode = getNextNode();
            nextPosition++;


            wasNextCalled = true;
            return iteratorCurrentNode.data;
        }
        else
            throw new RuntimeException("No more!");

    }

    public void remove(){
        if(wasNextCalled){
            wasNextCalled = false;

            Node prevNode = getPrevNode();

            Node tmpNode = aQueue.front;
            Node prevTmp = new Node();
            prevTmp.next = aQueue.front;
            while(tmpNode != prevNode){
                prevTmp = tmpNode;
                tmpNode = tmpNode.next;
            }

            // change the front if the it is one to be removed
            if(prevTmp.next == aQueue.front)
                aQueue.front = prevTmp.next.next;
            else//removal
                prevTmp.next = prevTmp.next.next;

            nextPosition--;

        }else
            throw new RuntimeException("Next() should be called first");

    }

    public static void main(String[] args) {
        QueueUsingLinkedList aQueue = new QueueUsingLinkedList();
        SeparateClassIterator iteratorObject = new SeparateClassIterator(aQueue);
//        Queue<Integer> obj = new LinkedList<>();

        aQueue.insert(6);
        aQueue.insert(4);
        aQueue.insert(17);
        aQueue.insert(3);
//        obj.remove();
        // System.out.println(obj.peek()); // 4

//        while(obj.hasNext()) {
//            System.out.println(obj.next());
//        }

        while(iteratorObject.hasNext()) {
            Integer i = iteratorObject.next();
            if(i < 10) {
                iteratorObject.remove();;
            }
        }
    }
}

```

---

## Option 3 - Inner Class Iterator
Implement next(), hasNext(), and remove() outside the classin a separate classin

```Java
public class QueueUsingLinkedListWithIteratorInnerClass {

    class SeparateClassInnerIterator{
        // for iterator
        int nextPosition = 0; // first item is item #1
        QueueUsingLinkedListWithIteratorInnerClass aQueue;
        SeparateClassInnerIterator(QueueUsingLinkedListWithIteratorInnerClass aQueue){
            this.aQueue = aQueue;
        }
        boolean hasNext(){
            if(nextPosition < aQueue.get_size())
                return true;
            else
                return false;
        }

        Node getNextNode(){
            int count = 0;
            Node nextNode = front;
            while (nextNode != null){
                if(count == nextPosition)
                    break;
                count++;
                nextNode = nextNode.next;
            }
            return nextNode;
        }

        Node getPrevNode(){
            int count = 0;
            Node tmpNode = front;
            Node prevNode = null;
            while (tmpNode != null){
                if(count == nextPosition)
                    break;
                count++;
                prevNode = tmpNode;
                tmpNode = tmpNode.next;
            }
            return prevNode;
        }

        boolean wasNextCalled = false;
        int next(){
            if(hasNext()){
                Node iteratorCurrentNode = getNextNode();
                nextPosition++;


                wasNextCalled = true;
                return iteratorCurrentNode.data;
            }
            else
                throw new RuntimeException("No more!");

        }

        void iteratorRemove(){
            if(wasNextCalled){
                wasNextCalled = false;

                Node prevNode = getPrevNode();

                Node tmpNode = front;
                Node prevTmp = new Node();
                prevTmp.next = front;
                while(tmpNode != prevNode){
                    prevTmp = tmpNode;
                    tmpNode = tmpNode.next;
                }

                // change the front if the it is one to be removed
                if(prevTmp.next == front)
                    front = prevTmp.next.next;
                else//removal
                    prevTmp.next = prevTmp.next.next;

                nextPosition--;

            }else
                throw new RuntimeException("Next() should be called first");

        }
    };

    Node front;
    Node back;


    SeparateClassInnerIterator get_iterator(){
        return  new SeparateClassInnerIterator(this);
    }

    int get_size(){
        int size = 0;
        Node tmpNode = front;
        while(tmpNode != null){
            size++;
            tmpNode = tmpNode.next;
        }
        return  size;
    }

    void insert( int a ){
        Node new_node = new Node();
        new_node.data = a;

        if(front == null && back == null ){ // Q is empty
            front = new_node;
            back = new_node;
        }else{
            // Q is not empty
            back.next = new_node;
            back = new_node;
        }
//        size++;
    }


    void remove(){
        front = front.next;
//        size--;
    }

    int peek(){
        return front.data;
    }
    public static void main(String[] args) {
        QueueUsingLinkedListWithIteratorInnerClass aQueue = new QueueUsingLinkedListWithIteratorInnerClass();
//        Queue<Integer> obj = new LinkedList<>();

        aQueue.insert(6);
        aQueue.insert(4);
        aQueue.insert(17);
        aQueue.insert(3);
//        obj.remove();
       // System.out.println(obj.peek()); // 4
        SeparateClassInnerIterator innerIterator = aQueue.get_iterator();
//        while(innerIterator.hasNext()) {
//            System.out.println(innerIterator.next());
//        }

        while(innerIterator.hasNext()) {
            Integer i = innerIterator.next();
            if(i < 10) {
                innerIterator.iteratorRemove();;
            }
        }
    }
}
```
