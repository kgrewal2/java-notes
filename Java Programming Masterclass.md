# Java Basics

## Numeric Literals

> Java 7 or later

```java
int i = 123456;
int i = 123_456;
```

> Float and Double are good for general calculations. High Precision calculations should be done using class like BigDecimal

## Logical Operator

Logical AND has higher precedence than Logical OR.

## Input

```java
Scanner scanner = new Scanner(System.in);
String name = scanner.nextLine();
int year = scanner.nextInt();
scanner.nextLine(); // Handles the next line character after integer input
// Code
scanner.close();
```

## Arrays

```java
int[] array1 = new int[10];
int[] array2 = {1, 2, 3, 4};
```

## Autoboxing, Unboxing

Autoboxing
Primitive type to class type
int --> Integer

Unboxing
Opposite of Autoboxing
Integer --> int

----

# Class Basics

## Generic Classes

```java
class MyClass <T> {
    // Code
}
```

Here T was a generic type and any class can be used as T at compile time.

```java
class Team <T extends Player> {
    // Code
}
```

Here only the classes extending from Player can be used as T.

## Static Initializers

> Initialize Static Data Members.
> Executed before the main method at the time of class loading.

```java
static {
    // Code
}
```

## Immutable Classes

> Object state cannot be changed after creation.

- No setter methods.
- Fields must be final and private.
- Declare class as final to avoid further inheritance.
- Create copies of reference variables passed in constructor.
- Getters should not return references to the field variables.
- Private constructors and factory methods can be used.
- Methods should not make changes to the class fields.

## Unmodifiable Collections

> Restricts to make changes to the collection but the existing data can be changes.
>
> Example: You cannot add new elements to Unmodifiable Map but can change the values in the existing <K, V> pairs.

---

# Exception Handling

## LBYL - Look before you leap

Check if something is possible before taking any actions.

```java
private int divide(int x, int y) {
    if(y==0) {
        System.out.println("Division by zero: Detected");
        return 0;
    }
    return x/y;
}
```

## EAFP - Easier to ask forgiveness than permission

If something goes wrong, fix it.

```java
private int divide(int x, int y) {
    int z;
    try {
        z = x/y;
    } catch(ArithmeticException e) {
        System.out.println("Division by zero: Detected\n" 
                           + e);
        return 0;
    }
    return z;
}
```

---

# File Operations - Standard IO

## Writing to File (Defensive Programming)

> Finally block always run irrespective of the fact that exception occurs or not.

```java
FileWriter writer = null;
try {
    writer = new FileWriter("2.txt");
    writer.write("Bye");
    writer.close();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    try {
        if (writer!=null)
            writer.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

This code is a defensive way of programming in which each exception is tracked and `finally` block is used to clean up the stuff. But it is difficult to handle multiple exceptions.
Solution is to use `try` resources. It automatically does the cleaning tasks.

```java
try (FileWriter writer = new FileWriter("2.txt")) {
	// Code
} catch (FileNotFoundException exception) {
	// Exception Code
}
```

### BufferedReader

Used to read file as a buffer. It is not bound to just files like FileReader. Default Buffer Size is 8K.

### BufferedWriter

Used to write to a buffer.

## Writing Primitive Data

`DataOutputStream` is used to write primitive Java variables like int or double to output stream in a portable way. (Also, see `DataInputStream`).

```java
DataOutputStream dos = new DataOutputStream(new BufferedOutputStream(new FileOutputStream("Output")));
dos.writeDouble(12.3d);
dos.writeBoolean(true);
dos.close();
DataInputStream dataInputStream = new DataInputStream(new BufferedInputStream(new FileInputStream("Output")));
System.out.println(dataInputStream.readDouble());
System.out.println(dataInputStream.readBoolean());
dataInputStream.close();
```

## Writing and Reading Java Objects

```java
List<String> list = new ArrayList<>();
list.add("Hello World");
list.add("Bye World");
try (ObjectOutputStream os = new ObjectOutputStream(new FileOutputStream("Output"))) {
    os.writeObject(list);
}
try (ObjectInputStream is = new ObjectInputStream(new FileInputStream("Output"))){
	List<String> newList = (List<String>)is.readObject();
	System.out.println(newList);
}
```

## RandomAccessFile

Uses pointer to read/write in the file and takes random access time. Gives performance but the file size is fixed.

---

# NIO - Non Blocking IO

Path: Object used to operate on files/directories.

FileChannel: NIO Alternative for Standard File Reader/Writer.

FileSystems: Used to work on different filesystems in a general manner. (Copying, renaming, etc.)

Files: Working with file attributes.

DirectoryStream: Content of an existing directory.

File.Separator: Used to avoid hardcoding the separator.

## Creating Temporary Files

```java
Files.createTempFile("text-file-", ".txt");
Files.write(tempFile, "Hello from Temp File", getBytes(StandardCharsets.UTF_8));
```

## Simple File Visitor

`SimpleFileVisitor<Path>` is used to visit each file in a file tree and do some XYZ operations on them. 
Methods to override: `preVisitDirectory`, `visitFile`, `visitFileFailed`, and `postVisitDirectory`.

## Sync Parallelism

When multiple cores of the computer are used to achieve parallelism. Different tasks run as different threads or processes.

## Async Parellelism

This is a multitasking that can be achieved on a single core using loop. This uses tasks to divide the work. Example: Task 1 is waiting for database's response. The loop can check database's response regularly and can work on other tasks till then. Once the database responds back, the loop can start working on the task 1.

---

# Concurrency

Process: Self contained execution environment. Owns runtime resources and memory space.

Thread: Like a light-weight process. Requires fewer resources than a process.

## Definining/Starting Thread

Runnable should be the preferred way of defining threads unless you want to change the default behaviour of the thread class.

### Runnable Interface (Preferred)

```java
class ThreadName implements Runnable {
	@Override
	public void run() {
		...
	}
}
...
new Thread(new ThreadName()).start();
```

### Thread Class

```java
Thread Class
class ThreadName extends Thread {
	@Override
	public void run() {
		...
	}
}
...
Thread t1 = new ThreadName();
t1.start();
```

### Synchronized

#### Code Block

Used to synchronize a block inside the method.

```java
synchronized(variable) {
    ...
}
```

#### Method

Used to synchronize a complete method.

```java
synchronized void methodName(int n) {
    ...
}
```

## Volatile Variable

Example: A thread class has a boolean variable `isRunning`.
Another method in the same class depends on `isRunning` to run a loop. (`true`: keep running, `false`: stop).

In general case, `isRunning` is cached to memory and method will use the cached variable. If `isRunning` changes while the loop inside method is running, it's not definite that the change will be reflected on the cached value immediately (OS Dependent).

In such cases, `volatile` keyword is used to avoid caching the variable.

## Wait for thread

```java
threadName.wait();
```

waits for the thread to die and then start the next work.

## Immutable Objects

Used in concurrent programs because they do not have any effect from interference.

- No setter methods.
- Fields must be final and private.
- Declare class as final to avoid further inheritance.
- Create copies of reference variables passed in constructor.
- Getters should not return references to the field variables.
- Private constructors and factory methods can be used.
- Methods should not make changes to the class fields.

## Starvation

A thread is unable to gain regular access to the shared resources.
Example: A method is invoking a synchronized method very frequently. Other methods trying to access synchronized methods may fall into starvation.

## Livelock

Like a deadlock caused by multiple threads.

## Executors

For large scale applications, it is suggested to keep the thread management/creation separate from rest of the application.

```java
(new Thread(r)).start();
```

becomes -->

```java
e.execute(r); // r is a runnable object
```

Other options/extensions:

- ExecutorService
- ScheduledExecutorService

## Thread Pools

Instead of creating new threads with requirement, create a thread pool (consisting of reusable worker threads).

## Atomic Variables

Variables that provide atomic operations and work like volatile variables. Using atomic variables is an alternative for unwanted synchronization.

Example: AtomicInteger

## Concurrent Collections

Java Concurrent Package also provides collections to avoid consistency errors.

### Blocking Queue

Defines a FIFO that blocks or times out when attempted to add to full queue or remove from empty queue.

Other Concurrent Collections: ConcurrentMap, NavigableConcurrentMap.
