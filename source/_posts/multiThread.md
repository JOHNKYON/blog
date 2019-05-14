---
title: ArrayBlockingQueue in Java
tags:
  - Engineer
  - Java
  - Multi Thread
categories:
  - Engineer
date: 2019-05-14 23:43:54
---


My recent phone interview encountered with an OOD question about multi-thread safe queue (and I'm a new grad).
I didn't do well so I read into the source code of ArrayBlockingQueue, which is a widely used multi-thread-safe queue class in openJDK.

For full source code of openJDK, check [here](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/util/concurrent/ArrayBlockingQueue.java).

## Class Members

```Java
public class ArrayBlockingQueue<E> extends AbstractQueue<E>
        implements BlockingQueue<E>, java.io.Serializable {
    
    /** The queued items */
    final Object[] items; // Fixed size;
    
    /** items index for next take, poll, peek or remove */
    int takeIndex;

    /** items index for next put, offer, or add */
    int putIndex;

    /** Number of elements in the queue */
    int count;

    /*
     * Concurrency control uses the classic two-condition algorithm
     * found in any textbook.
     */

     /** Main lock guarding all access */
    final ReentrantLock lock;
    /** Condition for waiting takes */
    private final Condition notEmpty;
    /** Condition for waiting puts */
    private final Condition notFull;
}
```

With these members, ArrayBlockingQueue implemented a fixed-size, multi-thread safe queue.

## Main methods
For a queue, there're two main write operations: put one element into the queue, and get one element out from the queue. In multi-thread safe programs all write operations should share a Mutex. Here we're implementing our own version of three different adding method and three different polling method. They should be like

Name | Behavior
------|--------
add() | Add an element to the queue. Return true if succeed; Throw IllegalStateException if the queue is full.
offer() | Add an element to the queue. Return true if succeed; Return false if failed.
put() | Add an element to the queue. Return true if succeed; Waiting if the queue is full until it's not full.
poll() | Retrieves and removes the head of this queue, or returns null if this queue is empty.
take() | Retrieves and removes the head of this queue, waiting if necessary until an element becomes available.

The key point here to keep the queue safe is by using a main lock and two conditions. Since all the operations are write operations, they share a Mutex.

Let's look into these methods one by one.

### add()
In fact, add simply inherited it's super class's implementation.
```Java
public boolean add(E e) {
    if (offer(e)) {
        return true;
    } else {
        throw new IllegalStateException("Queue full");
    }
}
```

### offer()
```Java
    /**
     * Inserts the specified element at the tail of this queue if it is
     * possible to do so immediately without exceeding the queue's capacity,
     * returning {@code true} upon success and {@code false} if this queue
     * is full.  This method is generally preferable to method {@link #add},
     * which can fail to insert an element only by throwing an exception.
     *
     * @throws NullPointerException if the specified element is null
     */
    public boolean offer(E e) {
        checkNotNull(e);
        final ReentrantLock lock = this.lock;
        lock.lock();    // Make sure only one thread can write to the queue
        try {
            if (count == items.length)
                return false;
            else {
                insert(e);
                return true;
            }
        } finally {
            lock.unlock();          // Release the lock
        }
    }
```

### put()
```Java
    /**
     * Inserts the specified element at the tail of this queue, waiting
     * for space to become available if the queue is full.
     *
     * @throws InterruptedException {@inheritDoc}
     * @throws NullPointerException {@inheritDoc}
     */
    public void put(E e) throws InterruptedException {
        checkNotNull(e);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();   // Make sure only one thread can write to the queue
        try {
            while (count == items.length)   // If the queue is full, current thread would get hang up.
                notFull.await();            // And the condition turns to await status.
            insert(e);
        } finally {
            lock.unlock();          // Release the lock
        }
    }
```

### lock() and lockInterruptibly()

As we can see from the code above, in `offer()`, we used `lock.lock()`. However in `put()`, we used `lock.lockInterruptibly()` instead. What is the difference between them?

`lock.lock()` would continuously trying to get the lock. 
`lock.lockInterruptibly()` behaves almost the same as `lock()`, however, if it is already interrupted or is interrupted while trying to get the lock, it would throw an exception, which should be handled by it's invoker.

### poll()
```Java
    public E poll() {
        final ReentrantLock lock = this.lock;   // Make sure only one thread can write to the queue
        lock.lock();
        try {
            return (count == 0) ? null : extract();
        } finally {
            lock.unlock();      // Release the lock
        }
    }
```

### take()
```Java
    public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;   // Make sure only one thread can write to the queue
        lock.lockInterruptibly();
        try {
            while (count == 0)      // If current queue is empty, current thread get hang up.
                notEmpty.await();   // And the empty condition changes its status.
            return extract();
        } finally {
            lock.unlock();      // Release the lock
        }
    }
```

### insert() and extract()
These two methods basically does two thing: do the operation and notify the awaiting process.

```Java
    /**
     * Inserts element at current put position, advances, and signals.
     * Call only when holding lock.
     */
    private void insert(E x) {
        items[putIndex] = x;
        putIndex = inc(putIndex);       // Circularly decrement i.
        ++count;
        notEmpty.signal();              // Notify the notEmpty condition.
    }

    /**
     * Extracts element at current take position, advances, and signals.
     * Call only when holding lock.
     */
    private E extract() {
        final Object[] items = this.items;
        E x = this.<E>cast(items[takeIndex]);
        items[takeIndex] = null;
        takeIndex = inc(takeIndex);     // Circularly decrement i.
        --count;
        notFull.signal();               // Nofity the notEmpty condition.
        return x;
    }
```

Above is the main part of the ArrayBlockingQueue in Java (openJDK). It's easy to understand, and a good review of two-condition algorithm.