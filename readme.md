# Java Collection Memory Usage Estimation

This repository provides various approaches for estimating the memory usage of Java collections and data structures like `LinkedList`, `ArrayList`, `HashMap`, and more. These methods will help you understand how much memory your collections consume in different scenarios.

## Approaches to Estimate Memory Usage

### 1. **Using `java.lang.instrument` API**
   - The `java.lang.instrument` package allows you to get the memory size of an object using the `getObjectSize` method.
   - To use this, you need to create a Java Agent. The agent is responsible for instrumenting the classes and calculating the memory size at runtime.
   - This method can give you precise memory usage for individual objects, but requires setup of a Java Agent.

   **Steps to use:**
   - Create an agent class that implements the `premain` method.
   - Use `java.lang.instrument.Instrumentation` to get object size.
   - Run your application with the Java Agent attached.

### 2. **Manual Memory Estimation**
   - You can estimate the memory used by each collection or node manually by considering the size of the object references and overheads in memory.
   - In general:
     - An object reference takes **8 bytes** (on a 64-bit JVM).
     - The object header typically consumes **12-16 bytes**.
     - Each `LinkedList` node contains two references: one for the data and one for the next node.
     - For example, a single `LinkedList` node can take **16-24 bytes** for references.

   **Steps to use:**
   - Calculate the memory usage of an object based on reference types and overheads.
   - Estimate for common data structures like `LinkedList`, `HashMap`, `ArrayList`, etc.

### 3. **Using Java Profilers (VisualVM, YourKit)**
   - Profilers like [VisualVM](https://visualvm.github.io/) and [YourKit](https://www.yourkit.com/) can be used to analyze heap usage and inspect the memory footprint of objects at runtime.
   - These profilers provide detailed memory breakdowns and let you monitor how memory usage changes during execution.

   **Steps to use:**
   - Download and install VisualVM or YourKit.
   - Attach the profiler to your running Java process.
   - Analyze the memory consumption and object allocation by class type.

### 4. **Using `sun.misc.Unsafe` (Advanced)**
   - The `sun.misc.Unsafe` class provides low-level methods for working with memory directly. It is typically not recommended due to its unsafe nature, but it allows you to calculate memory usage at a very granular level.
   - With `Unsafe`, you can manually allocate memory and inspect the sizes of objects in memory.

   **Steps to use:**
   - Use reflection to access `Unsafe` methods.
   - Call `getAddress` or `allocateMemory` to inspect or allocate memory.

### 5. **Benchmarking and Estimation**
   - For a rough estimation, you can benchmark your application before and after adding specific collections or nodes. By comparing memory usage in a profiler, you can infer the size of the collections.
   - This approach uses a combination of memory monitoring tools and logical reasoning to estimate memory consumption.

   **Steps to use:**
   - Use a profiler or memory monitoring tool to capture heap usage before and after adding elements to a collection.
   - Compare the memory usage to estimate the size of the collection.

## Example Code for Memory Estimation in Collections

Below is a basic example of how you can use some of these methods to estimate memory usage for a `LinkedList` node:

### Example 1: Using `java.lang.instrument`

```java
import java.lang.instrument.Instrumentation;

public class ObjectSizeFetcher {
    private static Instrumentation globalInstrumentation;

    public static void premain(String agentArgs, Instrumentation inst) {
        globalInstrumentation = inst;
    }

    public static long getObjectSize(Object object) {
        return globalInstrumentation.getObjectSize(object);
    }

    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>();
        list.add("Test");
        System.out.println("Size of list node: " + getObjectSize(list));
    }
}
```
### Example 2: Manual Estimation

// For a LinkedList node, let's estimate manually:
public class LinkedListNode {
    Object data;  // 8 bytes for reference
    LinkedListNode next;  // 8 bytes for reference

    public LinkedListNode(Object data, LinkedListNode next) {
        this.data = data;
        this.next = next;
    }
}

### Example 3: Using VisualVM

Run your Java program with VisualVM attached.

Use the profiler to monitor memory usage.

Analyze the heap dumps and object memory consumption.

### Example 4: Using Unsafe (Advanced)

import sun.misc.Unsafe;

public class UnsafeMemoryExample {
    private static final Unsafe unsafe = getUnsafe();

    private static Unsafe getUnsafe() {
        // Accessing Unsafe using reflection
        try {
            Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
            theUnsafe.setAccessible(true);
            return (Unsafe) theUnsafe.get(null);
        } catch (Exception e) {
            throw new RuntimeException("Unable to access Unsafe", e);
        }
    }

    public static void main(String[] args) {
        // Example of using Unsafe methods (advanced)
        System.out.println(unsafe.getAddress(0L));  // Low-level memory access
    }
}
Conclusion
These methods provide different ways to estimate and measure the memory usage of Java collections and data structures. Each approach has its own advantages and trade-offs, so you can choose the one that best fits your use case.

For further exploration:

Explore Java profilers like VisualVM or YourKit for real-time memory profiling.

Use java.lang.instrument for more precise memory size measurement in production environments.

Use manual estimation for quick memory analysis without needing external tools.

# Memory Usage of Java Collections and Primitive Types

## 1. Primitive Types
The size of each primitive type is fixed:

| **Primitive Type** | **Size (32-bit JVM)** | **Size (64-bit JVM)** |
|--------------------|-----------------------|-----------------------|
| `byte`             | 1 byte                | 1 byte                |
| `short`            | 2 bytes               | 2 bytes               |
| `int`              | 4 bytes               | 4 bytes               |
| `long`             | 8 bytes               | 8 bytes               |
| `float`            | 4 bytes               | 4 bytes               |
| `double`           | 8 bytes               | 8 bytes               |
| `char`             | 2 bytes               | 2 bytes               |
| `boolean`          | 1 byte (JVM-specific) | 1 byte (JVM-specific) |

## 2. Wrapper Objects
Wrapper objects in Java typically take more space due to additional overhead (header, object alignment, etc.).

| **Wrapper Type**   | **Size (32-bit JVM)** | **Size (64-bit JVM)** |
|--------------------|-----------------------|-----------------------|
| `Byte`             | 16 bytes              | 24 bytes              |
| `Short`            | 16 bytes              | 24 bytes              |
| `Integer`          | 16 bytes              | 24 bytes              |
| `Long`             | 16 bytes              | 24 bytes              |
| `Float`            | 16 bytes              | 24 bytes              |
| `Double`           | 16 bytes              | 24 bytes              |
| `Character`        | 16 bytes              | 24 bytes              |
| `Boolean`          | 16 bytes              | 24 bytes              |

## 3. Collections Memory Usage

# Memory Calculation for Storing a byte Primitive in a LinkedList

### Structure of a LinkedList Node:
A LinkedList in Java uses a doubly linked list where each node contains:

- A reference to the data item (`E item`).
- A reference to the next node (`Node<E> next`).
- A reference to the previous node (`Node<E> prev`).

For a `byte` primitive, Java uses the `Byte` object, which wraps the primitive value. The `Node` object also contains overhead due to its structure.

### 32-bit JVM Memory Calculation:
- Each reference (i.e., `item`, `next`, `prev`) is 4 bytes (32 bits).
- The header of the `Node` object (due to the internal object structure) takes 8 bytes.

**Total size for the Node:**
- 4 (next) + 4 (prev) + 4 (item reference) + 8 (object header) = 20 bytes.

Since object sizes in Java are aligned to 8 bytes, the `Node` object size is 24 bytes.

The `Byte` wrapper object for a `byte` primitive contains:
- 8 bytes for the object header.
- 1 byte for the actual byte value.
- 7 bytes of padding for alignment.

**Total memory used for the `Byte` object:**
- 16 bytes.

Thus, in a 32-bit JVM, a LinkedList node storing a `byte` primitive uses:

- 24 bytes for the node.
- 16 bytes for the `Byte` object.

**Total: 40 bytes.**

### 64-bit JVM Memory Calculation:
- Each reference is 8 bytes (64 bits).
- The header of the `Node` object is 16 bytes (two machine words).

**Total size for the Node:**
- 8 (next) + 8 (prev) + 8 (item reference) + 16 (object header) = 40 bytes.

The `Byte` wrapper object still uses 24 bytes (8 for the header, 1 for the byte value, and 7 for padding).

Thus, in a 64-bit JVM, a LinkedList node storing a `byte` primitive uses:

- 40 bytes for the node.
- 24 bytes for the `Byte` object.

**Total: 64 bytes.**

---

# Memory Calculation for Storing a byte Primitive in an ArrayList

An `ArrayList` uses an underlying array to store elements. When a primitive `byte` is added, it is automatically boxed into a `Byte` object.

### 32-bit JVM Memory Calculation:
The `Byte` object takes 16 bytes (8 bytes for the object header, 1 byte for the byte value, 7 bytes of padding).

The array storing the `Byte` object has a reference to the `Byte` object, which takes 4 bytes (since it's a 32-bit JVM).

**Total memory used for storing one byte in an ArrayList:**
- 16 bytes for the `Byte` object.
- 4 bytes for the reference in the array.

**Total: 20 bytes.**

### 64-bit JVM Memory Calculation:
The `Byte` object still takes 24 bytes (8 bytes for the object header, 1 byte for the byte value, and 7 bytes of padding).

The reference in the array takes 8 bytes (since it's a 64-bit JVM).

**Total memory used for storing one byte in an ArrayList:**
- 24 bytes for the `Byte` object.
- 8 bytes for the reference in the array.

**Total: 32 bytes.**


### LinkedList
A `LinkedList` is implemented as a doubly linked list, where each node contains a reference to the data (`item`), a reference to the next node (`next`), and a reference to the previous node (`prev`).

| **Collection**     | **Size per Element (32-bit JVM)** | **Size per Element (64-bit JVM)** |
|--------------------|-----------------------------------|-----------------------------------|
| `LinkedList` (Node) | `24 bytes (Node) + 16 bytes (Byte)` = **40 bytes** | `40 bytes (Node) + 24 bytes (Byte)` = **64 bytes** |

### ArrayList
An `ArrayList` stores elements in an array. It has a reference array to store objects, and each object stored in the array is boxed into a wrapper object.

| **Collection**     | **Size per Element (32-bit JVM)** | **Size per Element (64-bit JVM)** |
|--------------------|-----------------------------------|-----------------------------------|
| `ArrayList` (Element) | `16 bytes (Byte) + 4 bytes (reference)` = **20 bytes** | `24 bytes (Byte) + 8 bytes (reference)` = **32 bytes** |

### HashMap
A `HashMap` stores key-value pairs, where each entry is represented as an object (a `Map.Entry`). For each key and value, it stores references to the objects.

| **Collection**     | **Size per Entry (32-bit JVM)** | **Size per Entry (64-bit JVM)** |
|--------------------|---------------------------------|---------------------------------|
| `HashMap` (Entry)  | `16 bytes (key) + 16 bytes (value) + 8 bytes (reference)` = **40 bytes** | `24 bytes (key) + 24 bytes (value) + 16 bytes (reference)` = **64 bytes** |

### HashSet
A `HashSet` is backed by a `HashMap`, where the elements are stored as keys with `Boolean.TRUE` as their values.

| **Collection**     | **Size per Element (32-bit JVM)** | **Size per Element (64-bit JVM)** |
|--------------------|-----------------------------------|-----------------------------------|
| `HashSet` (Element) | `16 bytes (key) + 8 bytes (reference)` = **24 bytes** | `24 bytes (key) + 16 bytes (reference)` = **40 bytes** |

## 4. Full Cross-Referenced Summary Table

This table provides the memory usage across different collection types (e.g., `LinkedList`, `ArrayList`, `HashMap`, `HashSet`) for all primitive and wrapper types:

| **Collection**            | **Data Type**   | **Size (32-bit JVM)** | **Size (64-bit JVM)** |
|---------------------------|-----------------|-----------------------|-----------------------|
| `LinkedList` (Node)       | `byte`          | 40 bytes              | 64 bytes              |
|                           | `short`         | 40 bytes              | 64 bytes              |
|                           | `int`           | 40 bytes              | 64 bytes              |
|                           | `long`          | 40 bytes              | 64 bytes              |
|                           | `float`         | 40 bytes              | 64 bytes              |
|                           | `double`        | 40 bytes              | 64 bytes              |
|                           | `char`          | 40 bytes              | 64 bytes              |
|                           | `boolean`       | 40 bytes              | 64 bytes              |
|                           | `Byte`          | 40 bytes              | 64 bytes              |
|                           | `Short`         | 40 bytes              | 64 bytes              |
|                           | `Integer`       | 40 bytes              | 64 bytes              |
|                           | `Long`          | 40 bytes              | 64 bytes              |
|                           | `Float`         | 40 bytes              | 64 bytes              |
|                           | `Double`        | 40 bytes              | 64 bytes              |
|                           | `Character`     | 40 bytes              | 64 bytes              |
|                           | `Boolean`       | 40 bytes              | 64 bytes              |
|                           | `Object`        | 40 bytes              | 64 bytes              |
| `ArrayList` (Element)     | `byte`          | 20 bytes              | 32 bytes              |
|                           | `short`         | 20 bytes              | 32 bytes              |
|                           | `int`           | 20 bytes              | 32 bytes              |
|                           | `long`          | 20 bytes              | 32 bytes              |
|                           | `float`         | 20 bytes              | 32 bytes              |
|                           | `double`        | 20 bytes              | 32 bytes              |
|                           | `char`          | 20 bytes              | 32 bytes              |
|                           | `boolean`       | 20 bytes              | 32 bytes              |
|                           | `Byte`          | 20 bytes              | 32 bytes              |
|                           | `Short`         | 20 bytes              | 32 bytes              |
|                           | `Integer`       | 20 bytes              | 32 bytes              |
|                           | `Long`          | 20 bytes              | 32 bytes              |
|                           | `Float`         | 20 bytes              | 32 bytes              |
|                           | `Double`        | 20 bytes              | 32 bytes              |
|                           | `Character`     | 20 bytes              | 32 bytes              |
|                           | `Boolean`       | 20 bytes              | 32 bytes              |
|                           | `Object`        | 20 bytes              | 32 bytes              |
| `HashMap` (Entry)         | `byte`          | 40 bytes              | 64 bytes              |
|                           | `short`         | 40 bytes              | 64 bytes              |
|                           | `int`           | 40 bytes              | 64 bytes              |
|                           | `long`          | 40 bytes              | 64 bytes              |
|                           | `float`         | 40 bytes              | 64 bytes              |
|                           | `double`        | 40 bytes              | 64 bytes              |
|                           | `char`          | 40 bytes              | 64 bytes              |
|                           | `boolean`       | 40 bytes              | 64 bytes              |
|                           | `Byte`          | 40 bytes              | 64 bytes              |
|                           | `Short`         | 40 bytes              | 64 bytes              |
|                           | `Integer`       | 40 bytes              | 64 bytes              |
|                           | `Long`          | 40 bytes              | 64 bytes              |
|                           | `Float`         | 40 bytes              | 64 bytes              |
|                           | `Double`        | 40 bytes              | 64 bytes              |
|                           | `Character`     | 40 bytes              | 64 bytes              |
|                           | `Boolean`       | 40 bytes              | 64 bytes              |
|                           | `Object`        | 40 bytes              | 64 bytes              |
| `HashSet` (Element)       | `byte`          | 24 bytes              | 40 bytes              |
|                           | `short`         | 24 bytes              | 40 bytes              |
|                           | `int`           | 24 bytes              | 40 bytes              |
|                           | `long`          | 24 bytes              | 40 bytes              |
|                           | `float`         | 24 bytes              | 40 bytes              |
|                           | `double`        | 24 bytes              | 40 bytes              |
|                           | `char`          | 24 bytes              | 40 bytes              |
|                           | `boolean`       | 24 bytes              | 40 bytes              |
|                           | `Byte`          | 24 bytes              | 40 bytes              |
|                           | `Short`         | 24 bytes              | 40 bytes              |
|                           | `Integer`       | 24 bytes              | 40 bytes              |
|                           | `Long`          | 24 bytes              | 40 bytes              |
|                           | `Float`         | 24 bytes              | 40 bytes              |
|                           | `Double`        | 24 bytes              | 40 bytes              |
|                           | `Character`     | 24 bytes              | 40 bytes              |
|                           | `Boolean`       | 24 bytes              | 40 bytes              |
|                           | `Object`        | 24 bytes              | 40 bytes              |


## Additional Notes
- The size of each collection element varies depending on whether the collection is storing primitive types directly (which Java automatically boxes into wrapper objects) or whether the collection stores references to objects.
- Memory usage can also differ depending on JVM-specific implementation details and whether the object is cached (as in the case of small integers and booleans).
- The size of the object may also depend on internal padding and alignment to satisfy JVM requirements for optimal memory access.

This table should give you a good estimate of memory usage for various data types and collections in Java.

