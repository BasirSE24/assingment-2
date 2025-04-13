# assingment-2
// Interface: MyList.java
public interface MyList<E> extends Iterable<E> {
    void add(E element);
    E get(int index);
    E remove(int index);
    int size();
    boolean isEmpty();
    void clear();
}

// Class: MyArrayList.java
public class MyArrayList<E> implements MyList<E> {
    private Object[] data;
    private int size;
    private static final int INITIAL_CAPACITY = 10;

    public MyArrayList() {
        data = new Object[INITIAL_CAPACITY];
        size = 0;
    }

    public void add(E element) {
        if (size == data.length) resize();
        data[size++] = element;
    }

    private void resize() {
        Object[] newData = new Object[data.length * 2];
        for (int i = 0; i < data.length; i++) newData[i] = data[i];
        data = newData;
    }

    public E get(int index) {
        checkIndex(index);
        return (E) data[index];
    }

    public E remove(int index) {
        checkIndex(index);
        E removed = (E) data[index];
        for (int i = index; i < size - 1; i++) data[i] = data[i + 1];
        data[--size] = null;
        return removed;
    }

    public int size() { return size; }

    public boolean isEmpty() { return size == 0; }

    public void clear() {
        data = new Object[INITIAL_CAPACITY];
        size = 0;
    }

    private void checkIndex(int index) {
        if (index < 0 || index >= size) throw new IndexOutOfBoundsException();
    }

    public java.util.Iterator<E> iterator() {
        return new java.util.Iterator<>() {
            int cursor = 0;
            public boolean hasNext() { return cursor < size; }
            public E next() { return (E) data[cursor++]; }
        };
    }
}

// Class: MyLinkedList.java
public class MyLinkedList<E> implements MyList<E> {
    private class MyNode {
        E data;
        MyNode next, prev;
        MyNode(E data) { this.data = data; }
    }

    private MyNode head, tail;
    private int size;

    public void add(E element) {
        MyNode newNode = new MyNode(element);
        if (head == null) {
            head = tail = newNode;
        } else {
            tail.next = newNode;
            newNode.prev = tail;
            tail = newNode;
        }
        size++;
    }

    public E get(int index) {
        checkIndex(index);
        MyNode current = nodeAt(index);
        return current.data;
    }

    public E remove(int index) {
        checkIndex(index);
        MyNode toRemove = nodeAt(index);
        if (toRemove.prev != null) toRemove.prev.next = toRemove.next;
        else head = toRemove.next;
        if (toRemove.next != null) toRemove.next.prev = toRemove.prev;
        else tail = toRemove.prev;
        size--;
        return toRemove.data;
    }

    private MyNode nodeAt(int index) {
        MyNode current = head;
        for (int i = 0; i < index; i++) current = current.next;
        return current;
    }

    public int size() { return size; }

    public boolean isEmpty() { return size == 0; }

    public void clear() {
        head = tail = null;
        size = 0;
    }

    private void checkIndex(int index) {
        if (index < 0 || index >= size) throw new IndexOutOfBoundsException();
    }

    public java.util.Iterator<E> iterator() {
        return new java.util.Iterator<>() {
            MyNode current = head;
            public boolean hasNext() { return current != null; }
            public E next() {
                E value = current.data;
                current = current.next;
                return value;
            }
        };
    }
}

// Class: MyStack.java
public class MyStack<E> {
    private MyArrayList<E> list = new MyArrayList<>();

    public void push(E value) { list.add(value); }
    public E pop() { return list.remove(list.size() - 1); }
    public E peek() { return list.get(list.size() - 1); }
    public boolean isEmpty() { return list.isEmpty(); }
    public int size() { return list.size(); }
}

// Class: MyQueue.java
public class MyQueue<E> {
    private MyLinkedList<E> list = new MyLinkedList<>();

    public void enqueue(E value) { list.add(value); }
    public E dequeue() { return list.remove(0); }
    public E peek() { return list.get(0); }
    public boolean isEmpty() { return list.isEmpty(); }
    public int size() { return list.size(); }
}

// Class: MyMinHeap.java
public class MyMinHeap<E extends Comparable<E>> {
    private MyArrayList<E> heap = new MyArrayList<>();

    public void add(E value) {
        heap.add(value);
        heapifyUp();
    }

    public E removeMin() {
        if (heap.isEmpty()) return null;
        E min = heap.get(0);
        heap.remove(0);
        heapifyDown();
        return min;
    }

    public E peek() {
        return heap.isEmpty() ? null : heap.get(0);
    }

    private void heapifyUp() {
        for (int i = heap.size() / 2 - 1; i >= 0; i--) heapify(i);
    }

    private void heapifyDown() {
        for (int i = 0; i < heap.size() / 2; i++) heapify(i);
    }

    private void heapify(int i) {
        int left = 2 * i + 1, right = 2 * i + 2, smallest = i;
        if (left < heap.size() && heap.get(left).compareTo(heap.get(smallest)) < 0) smallest = left;
        if (right < heap.size() && heap.get(right).compareTo(heap.get(smallest)) < 0) smallest = right;
        if (smallest != i) {
            E temp = heap.get(i);
            heap.remove(i);
            heap.add(i, heap.get(smallest));
            heap.remove(smallest);
            heap.add(smallest, temp);
            heapify(smallest);
        }
    }

    public int size() { return heap.size(); }
    public boolean isEmpty() { return heap.isEmpty(); }
}
