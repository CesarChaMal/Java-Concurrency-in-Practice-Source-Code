# CLAUDE.md - AI Assistant Guide for Java Concurrency in Practice Source Code

## Repository Overview

This repository contains **143 Java source files** organized into **16 topic-based directories**, representing example implementations from the book **"Java Concurrency in Practice"** by Brian Goetz and Tim Peierls.

### Purpose
- **Educational reference code** demonstrating Java concurrency concepts
- **Progressive examples** showing evolution from simple/incorrect to complex/correct implementations
- **Anti-patterns and solutions** with both thread-safe and unsafe examples
- **Best practices** for concurrent programming in Java

### Key Metadata
- **Package**: All classes use `net.jcip.examples`
- **Authors**: Brian Goetz and Tim Peierls
- **Java Version**: JDK 5.0+ (heavily uses `java.util.concurrent` package)
- **Total Size**: ~508KB, 143 files
- **Build System**: None (standalone educational examples)
- **Source Origin**: Downloaded from http://jcip.net.s3-website-us-east-1.amazonaws.com/

---

## Directory Structure and Organization

The repository is organized by book chapters/topics:

### 1. **Introduction/** (2 files)
Basic thread-safe vs unsafe implementations.
- `Sequence.java` - Thread-safe counter
- `UnsafeSequence.java` - Race condition example

### 2. **Thread_Safety/** (8 files)
Servlet examples demonstrating various levels of thread safety.
- `StatelessFactorizer.java` - Thread-safe stateless servlet
- `UnsafeCountingFactorizer.java` - Race condition in servlet
- `SynchronizedFactorizer.java` - Using synchronization
- `CachedFactorizer.java` - Optimized with caching
- `LazyInitRace.java` - Lazy initialization problems

### 3. **Sharing_Objects/** (15 files)
Visibility issues, safe publication, immutability.
- `NoVisibility.java` - Visibility problems
- `ThisEscape.java` - Constructor escape hazard
- `VolatileCachedFactorizer.java` - Using volatile
- `SafeListener.java` - Safe construction patterns
- `Holder.java` - Improper publication

### 4. **Composing_Objects/** (14 files)
Building thread-safe classes through composition.
- `MonitorVehicleTracker.java` - Monitor-based synchronization
- `DelegatingVehicleTracker.java` - Delegation pattern
- `PublishingVehicleTracker.java` - Safe publication
- `ImprovedList.java` - Thread-safe list wrapper
- `ListHelpers.java` - Client-side locking examples

### 5. **Building_Blocks/** (14 files)
Concurrent collections, synchronizers, memoization.
- `Memoizer.java` and variants (1-3) - Progressive memoization
- `BoundedHashSet.java` - Using semaphores
- `TestHarness.java` - CountDownLatch example
- `Preloader.java` - FutureTask usage

### 6. **Task_Execution/** (12 files + 3 HTML)
Executor framework and web server examples.
- `LifecycleWebServer.java` - Executor lifecycle management
- `TaskExecutionWebServer.java` - Task-based web server
- `FutureRenderer.java` - Async rendering with Future
- `Renderer.java` - Sequential rendering

### 7. **Cancellation_and_Shutdown/** (16 files)
Thread interruption and graceful shutdown patterns.
- `PrimeGenerator.java` - Cancellation via flag
- `LogService.java` - Shutdown with producer-consumer
- `WebCrawler.java` - Coordinated shutdown
- `TrackingExecutor.java` - Tracking cancelled tasks

### 8. **Applying_Thread_Pools/** (12 files)
Thread pool configuration and custom factories.
- `MyThreadFactory.java` - Custom thread factory
- `PuzzleSolver.java` - Parallel puzzle solving
- `TimingThreadPool.java` - ThreadPoolExecutor extension
- `BoundedExecutor.java` - Bounded task submission

### 9. **GUI_Applications/** (4 files)
Swing threading and background tasks.
- `GuiExecutor.java` - GUI event thread executor
- `BackgroundTask.java` - Async GUI updates
- `ListenerExamples.java` - Event listener patterns

### 10. **Explicit_Locks/** (4 files)
Lock interface usage and deadlock avoidance.
- `DeadlockAvoidance.java` - Using tryLock
- `ReadWriteMap.java` - ReadWriteLock example
- `InterruptibleLocking.java` - Interruptible locks

### 11. **Avoiding_Liveness_Hazards/** (6 files)
Deadlock examples and solutions.
- `LeftRightDeadlock.java` - Classic deadlock
- `DynamicOrderDeadlock.java` - Dynamic lock ordering
- `InduceLockOrder.java` - Lock ordering solution
- `CooperatingDeadlock.java` - Cooperating objects deadlock

### 12. **Performance_and_Scalability/** (7 files)
Lock striping and reducing contention.
- `StripedMap.java` - Lock striping pattern
- `AttributeStore.java` - Contention examples
- `ServerStatusAfterSplit.java` - Reducing lock scope

### 13. **Testing_Concurrent_Programs/** (7 files)
Testing strategies for concurrent code.
- `BoundedBuffer.java` - Testing bounded buffers
- `PutTakeTest.java` - Producer-consumer testing
- `BarrierTimer.java` - Throughput testing

### 14. **Building_Custom_Synchronizers/** (8 files)
Condition queues and custom synchronizers.
- `BoundedBuffer.java` variants - Progressive refinement
- `ConditionBoundedBuffer.java` - Using Condition
- `OneShotLatch.java` - Custom latch
- `ThreadGate.java` - Thread gating

### 15. **Atomic_Variables_and_Nonblocking_Synchronization/** (7 files)
CAS operations and lock-free data structures.
- `ConcurrentStack.java` - Treiber stack
- `LinkedQueue.java` - Michael-Scott queue
- `CasCounter.java` - CAS-based counter
- `AtomicPseudoRandom.java` - Lock-free PRNG

### 16. **The_Java_Memory_Model/** (7 files)
Memory model issues and initialization patterns.
- `DoubleCheckedLocking.java` - DCL anti-pattern
- `SafeLazyInitialization.java` - Correct lazy init
- `PossibleReordering.java` - Reordering examples

---

## Code Conventions and Patterns

### Package Structure
```java
package net.jcip.examples;
```
**All classes use this single package.** No sub-packages.

### Concurrency Annotations
The codebase uses custom annotations from `net.jcip.annotations`:

```java
@ThreadSafe        // Marks thread-safe classes (66+ files)
@NotThreadSafe     // Marks classes that are NOT thread-safe
@GuardedBy("lock") // Documents which lock guards which field
@Immutable         // Marks immutable classes
```

**Important**: These annotations are for **documentation only** (not enforced at runtime).

### Standard File Header
```java
/**
 * ClassName
 * <p/>
 * Brief description of what this class demonstrates
 *
 * @author Brian Goetz and Tim Peierls
 */
@ThreadSafe  // or @NotThreadSafe
public class ClassName {
    @GuardedBy("this") private int counter;
    // ...
}
```

### Common Patterns

#### 1. **Servlet Examples**
Many examples extend `GenericServlet`:
```java
@ThreadSafe
public class StatelessFactorizer extends GenericServlet implements Servlet {
    public void service(ServletRequest req, ServletResponse resp) {
        // Thread-safe implementation
    }
}
```

#### 2. **Progressive Refinement**
Multiple versions showing evolution:
- `Memoizer1.java` → `Memoizer2.java` → `Memoizer3.java` → `Memoizer.java`
- Each version fixes issues from the previous

#### 3. **Unsafe/Safe Pairs**
Many concepts have both incorrect and correct versions:
- `UnsafeSequence.java` vs `Sequence.java`
- `UnsafeCountingFactorizer.java` vs `StatelessFactorizer.java`

#### 4. **Cancellation Pattern**
```java
private volatile boolean cancelled;

public void run() {
    while (!cancelled && !Thread.currentThread().isInterrupted()) {
        // Work
    }
}

public void cancel() { cancelled = true; }
```

#### 5. **Lock-Free Algorithms**
Using `AtomicReference` and CAS:
```java
public void push(E item) {
    Node<E> newHead = new Node<E>(item);
    Node<E> oldHead;
    do {
        oldHead = top.get();
        newHead.next = oldHead;
    } while (!top.compareAndSet(oldHead, newHead));
}
```

#### 6. **Memoization Pattern**
```java
public class Memoizer<A, V> implements Computable<A, V> {
    private final ConcurrentMap<A, Future<V>> cache = new ConcurrentHashMap<>();

    public V compute(final A arg) throws InterruptedException {
        Future<V> f = cache.get(arg);
        if (f == null) {
            Callable<V> eval = new Callable<V>() {
                public V call() throws InterruptedException {
                    return c.compute(arg);
                }
            };
            FutureTask<V> ft = new FutureTask<V>(eval);
            f = cache.putIfAbsent(arg, ft);
            if (f == null) { f = ft; ft.run(); }
        }
        return f.get();
    }
}
```

---

## Dependencies and External Libraries

### Standard Java Libraries (JDK 5.0+)
- `java.util.concurrent.*` - ExecutorService, Future, ConcurrentHashMap, Semaphore
- `java.util.concurrent.atomic.*` - AtomicReference, AtomicInteger
- `java.util.concurrent.locks.*` - Lock, ReentrantLock, Condition
- `javax.servlet.*` - For servlet examples (requires servlet API JAR)
- `java.math.BigInteger` - Used in computation examples
- `java.net.*` - For web server examples
- `javax.swing.*` - For GUI examples

### External Dependencies
- **jcip-annotations.jar** - Contains `@ThreadSafe`, `@GuardedBy`, etc.
  - Not included in repository
  - Available from book's website or Maven Central
  - Group ID: `net.jcip`, Artifact ID: `jcip-annotations`

---

## Build and Compilation

### No Build System
This repository has **no Maven, Gradle, or Ant configuration**. It's designed as standalone educational examples.

### Compiling Individual Files

**Simple compilation:**
```bash
javac -cp /path/to/jcip-annotations.jar Sharing_Objects/NoVisibility.java
```

**For servlet examples:**
```bash
javac -cp /path/to/jcip-annotations.jar:/path/to/servlet-api.jar Thread_Safety/StatelessFactorizer.java
```

### Stub Implementations
Many files contain **stub methods** for demonstration:
```java
BigInteger extractFromRequest(ServletRequest req) {
    return new BigInteger("7");  // Simplified stub
}

BigInteger[] factor(BigInteger i) {
    return new BigInteger[] { i };  // Stub implementation
}
```

**These are not production code** - they exist to keep examples focused on concurrency concepts.

---

## Development Workflows

### Working with This Repository

#### 1. **Analyzing a Concurrency Pattern**
When asked to explain or analyze a pattern:
- Identify the relevant directory (e.g., `Sharing_Objects` for visibility)
- Read related files (often there are multiple examples)
- Look for progression (unsafe → safe, or v1 → v2 → v3)
- Check annotations (`@GuardedBy`, `@ThreadSafe`) for documentation

#### 2. **Finding Examples of a Technique**
Use grep to find usage patterns:
```bash
# Find all uses of volatile
grep -r "volatile" . --include="*.java"

# Find all @GuardedBy annotations
grep -r "@GuardedBy" . --include="*.java"

# Find all uses of ConcurrentHashMap
grep -r "ConcurrentHashMap" . --include="*.java"
```

#### 3. **Comparing Implementations**
Many concepts have multiple implementations:
- Look for numbered versions (`Memoizer1`, `Memoizer2`, etc.)
- Compare "Unsafe" vs safe versions
- Check "Before" vs "After" variants

#### 4. **Understanding Context**
- Read JavaDoc comments for concept explanation
- Check related files in the same directory
- Look at the directory name for the chapter/topic context

---

## Key Concepts by Directory

### Thread Safety Spectrum
1. **Stateless** - `StatelessFactorizer.java` (inherently thread-safe)
2. **Immutable** - `ThreeStooges.java`, `ImmutablePoint.java`
3. **Synchronized** - `SynchronizedFactorizer.java`
4. **Concurrent collections** - `CachedFactorizer.java`

### Visibility Mechanisms
- **Volatile** - `NoVisibility.java`, `VolatileCachedFactorizer.java`
- **Synchronization** - Guarantees both atomicity and visibility
- **Final fields** - `SafeListener.java`

### Synchronizers
- **Latches** - `TestHarness.java` (CountDownLatch)
- **Semaphores** - `BoundedHashSet.java`
- **Barriers** - `CellularAutomata.java` (CyclicBarrier)
- **FutureTask** - `Preloader.java`

### Lock Patterns
- **Intrinsic locks** - synchronized methods/blocks
- **Explicit locks** - `ReadWriteMap.java`, `InterruptibleLocking.java`
- **Lock striping** - `StripedMap.java`
- **Try-lock** - `DeadlockAvoidance.java`

### Cancellation Strategies
- **Volatile flag** - `PrimeGenerator.java`
- **Interruption** - `BrokenPrimeProducer.java`
- **ExecutorService shutdown** - `LifecycleWebServer.java`
- **Poison pills** - Producer-consumer patterns

---

## Common Tasks for AI Assistants

### Task 1: Explain a Concurrency Concept
**Example**: "Explain the happens-before relationship"

**Approach**:
1. Look in `The_Java_Memory_Model/` directory
2. Read `PossibleReordering.java` and related files
3. Check JavaDoc comments
4. Reference the book chapter context

### Task 2: Find Best Practice Examples
**Example**: "Show me the right way to use volatile"

**Approach**:
1. Search for `volatile` usage: `grep -r "volatile" . --include="*.java"`
2. Focus on files with `@ThreadSafe` annotation
3. Compare with unsafe examples
4. Recommend `VolatileCachedFactorizer.java` or `SafeListener.java`

### Task 3: Debug Concurrency Issues
**Example**: "Why does this code have a race condition?"

**Approach**:
1. Look for similar anti-patterns in "Unsafe" files
2. Check for missing synchronization
3. Verify proper use of `@GuardedBy` patterns
4. Find the corresponding safe implementation

### Task 4: Implement a Pattern
**Example**: "Implement a thread-safe cache"

**Approach**:
1. Reference `Memoizer.java` (final version)
2. Note the progression through `Memoizer1-3.java`
3. Use `ConcurrentHashMap` and `FutureTask`
4. Follow the `@ThreadSafe` + `@GuardedBy` annotation style

### Task 5: Compare Approaches
**Example**: "Compare monitor-based vs delegation approaches"

**Approach**:
1. Read `MonitorVehicleTracker.java` (monitor pattern)
2. Read `DelegatingVehicleTracker.java` (delegation pattern)
3. Read `PublishingVehicleTracker.java` (alternative)
4. Explain trade-offs in comments

---

## Important Gotchas and Notes

### 1. **Not Production Code**
- Examples are simplified for teaching
- Many methods are stubs returning dummy values
- Some examples intentionally show **broken code** (Unsafe* files)

### 2. **Annotation Dependencies**
- Code won't compile without `jcip-annotations.jar`
- Annotations are documentation only (no runtime enforcement)

### 3. **Servlet API Required**
- Many examples need `servlet-api.jar` on classpath
- Servlets are used as concrete examples of shared state

### 4. **Java Version**
- Code targets **Java 5.0+**
- Uses generics, enhanced for-loops, java.util.concurrent
- Some patterns may have modern alternatives (e.g., CompletableFuture)

### 5. **Progressive Examples**
- Always check for numbered versions (1, 2, 3)
- The final version (without number) is usually the correct one
- Read them in sequence to understand evolution

### 6. **HTML Reference Files**
- `Task_Execution/` contains 3 HTML files
- These reference external annotations and JavaDoc
- Not source code - metadata about dependencies

### 7. **downloader.py**
- Python script used to scrape original source
- Not part of the Java codebase
- Useful for understanding repository origin

---

## File Naming Conventions

### Prefixes
- **Unsafe*** - Deliberately broken examples (race conditions, etc.)
- **Safe*** - Correct implementations
- **Broken*** - Subtly incorrect (e.g., `BrokenPrimeProducer.java`)

### Suffixes/Numbers
- **Number suffixes (1, 2, 3)** - Progressive refinement
  - Example: `Memoizer1`, `Memoizer2`, `Memoizer3`, `Memoizer`
  - Read in numerical order

### Descriptive Names
- **What it demonstrates**: `LeftRightDeadlock.java`, `NoVisibility.java`
- **Pattern name**: `StripedMap.java`, `OneShotLatch.java`
- **Use case**: `AttributeStore.java`, `WebCrawler.java`

---

## Testing Approach

The `Testing_Concurrent_Programs/` directory shows testing strategies:

1. **Sequential tests** - Test logic in single thread
2. **Concurrent stress tests** - Multiple threads hammering the code
3. **Timing tests** - Measure throughput under load
4. **Barrier-based tests** - Synchronize test threads with `CyclicBarrier`

**Example test pattern** from `PutTakeTest.java`:
```java
class PutTakeTest {
    private static final ExecutorService pool = Executors.newCachedThreadPool();
    private final CyclicBarrier barrier;
    private final BoundedBuffer<Integer> bb;

    void test() {
        // Start all threads
        barrier.await();  // Synchronize start
        // All threads run concurrently
        // Verify invariants after completion
    }
}
```

---

## Recommended Reading Order

For AI assistants learning from this codebase:

### Beginner Level
1. **Introduction/** - Thread safety basics
2. **Thread_Safety/** - Servlet examples, atomicity
3. **Sharing_Objects/** - Visibility, publication

### Intermediate Level
4. **Composing_Objects/** - Building thread-safe classes
5. **Building_Blocks/** - Concurrent collections
6. **Task_Execution/** - Executor framework

### Advanced Level
7. **Cancellation_and_Shutdown/** - Lifecycle management
8. **Explicit_Locks/** - Lock interface
9. **Performance_and_Scalability/** - Lock striping, contention

### Expert Level
10. **Atomic_Variables_and_Nonblocking_Synchronization/** - Lock-free algorithms
11. **Building_Custom_Synchronizers/** - Condition queues
12. **The_Java_Memory_Model/** - Memory semantics

### Specialized Topics
13. **GUI_Applications/** - Swing threading
14. **Applying_Thread_Pools/** - Pool configuration
15. **Testing_Concurrent_Programs/** - Testing strategies
16. **Avoiding_Liveness_Hazards/** - Deadlock patterns

---

## Quick Reference: Common Patterns

### Thread-Safe Singleton
```java
// From SafeLazyInitialization.java
@ThreadSafe
public class SafeLazyInitialization {
    private static Resource resource;

    public synchronized static Resource getInstance() {
        if (resource == null)
            resource = new Resource();
        return resource;
    }
}
```

### Guarded By Pattern
```java
@GuardedBy("this")
private final Map<String, String> attributes = new HashMap<>();

public synchronized boolean putIfAbsent(String key, String value) {
    if (!attributes.containsKey(key)) {
        attributes.put(key, value);
        return true;
    }
    return false;
}
```

### Volatile for State Flags
```java
private volatile boolean asleep;

public void run() {
    while (!asleep) {
        // Do work
    }
}
```

### Lock-Free Stack (Treiber)
```java
// From ConcurrentStack.java
public void push(E item) {
    Node<E> newHead = new Node<E>(item);
    Node<E> oldHead;
    do {
        oldHead = top.get();
        newHead.next = oldHead;
    } while (!top.compareAndSet(oldHead, newHead));
}
```

### Executor Lifecycle
```java
// From LifecycleWebServer.java
public void stop() throws InterruptedException {
    exec.shutdown();
    exec.awaitTermination(TIMEOUT, UNIT);
}
```

---

## Git Workflow for This Repository

### Branch Naming
- Feature branches: `claude/claude-md-<session-id>`
- Always develop on designated branch
- Never push to main/master without permission

### Commit Messages
Follow standard conventions:
```
Add CLAUDE.md with comprehensive codebase documentation

- Analyzed 16 directories with 143 Java files
- Documented code patterns and concurrency annotations
- Included development workflows and common tasks
- Added quick reference for common patterns
```

### Making Changes
1. **Read first**: Always use Read tool before editing
2. **Edit existing**: Prefer editing over creating new files
3. **Test compilation**: Verify syntax if adding/modifying Java code
4. **Update docs**: If adding new patterns, update CLAUDE.md

---

## Resources and External References

### Book
- **Title**: Java Concurrency in Practice
- **Authors**: Brian Goetz, Tim Peierls, Joshua Bloch, Joseph Bowbeer, David Holmes, Doug Lea
- **Publisher**: Addison-Wesley Professional
- **ISBN**: 0-321-34960-1

### Official Website
- **Source**: http://jcip.net.s3-website-us-east-1.amazonaws.com/
- Contains all source code listings
- Includes errata and additional resources

### Annotations
- **Maven**: `net.jcip:jcip-annotations:1.0`
- **Purpose**: Documentation annotations for concurrency
- **License**: Creative Commons Attribution

---

## Summary for AI Assistants

When working with this repository:

1. ✅ **DO**:
   - Reference specific files with path and line numbers
   - Compare unsafe vs safe implementations
   - Explain the progression in numbered examples
   - Use annotations as documentation clues
   - Recognize this is teaching code, not production
   - Look for related examples in the same directory

2. ❌ **DON'T**:
   - Assume stub methods are complete implementations
   - Recommend unsafe examples without noting they're broken
   - Ignore the @GuardedBy annotations
   - Try to run examples without proper dependencies
   - Suggest creating new concurrency patterns without checking existing ones
   - Treat this as a production codebase

3. 🎯 **BEST PRACTICES**:
   - Always check for both unsafe and safe versions
   - Read JavaDoc comments for context
   - Use grep to find similar patterns across the codebase
   - Reference the directory name for topic context
   - Recommend the final/safe version after explaining the evolution
   - Note which Java concurrency utilities are used

---

## Version History

- **v1.0** (2025-11-13): Initial comprehensive documentation
  - Complete repository analysis
  - All 16 directories documented
  - Code patterns and conventions established
  - Development workflows defined
  - Quick reference guide added

---

*This guide was created to help AI assistants effectively understand and work with the Java Concurrency in Practice source code repository. For questions or updates, modify this file and commit changes.*
