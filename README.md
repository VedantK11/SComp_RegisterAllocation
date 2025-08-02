# Optimizing Register Allocation in Compilers

## Overview

This project implements an optimized register allocation mechanism using the **Linear Scan Register Allocation (LSRA)** algorithm. The implementation uses JavaCC and JTB to perform compiler optimizations that efficiently assign registers to variables or spill them to memory based on live range analysis.


## Project Description

Register allocation is a critical optimization in compiler design that determines how to map program variables to a limited number of CPU registers. This project implements the Linear Scan Register Allocation algorithm, which:

- **Analyzes live ranges** of variables to determine their usage intervals
- **Assigns registers efficiently** based on register pressure analysis
- **Spills variables to memory** when registers are unavailable
- **Generates optimized code** with reduced memory access overhead

## Key Features

**Live Range Analysis**: Computes variable lifetimes using depth-first AST traversal  
**Register Pressure Management**: Efficiently handles limited register availability  
**Memory Spilling**: Automatically spills variables when registers are exhausted  
**Code Generation**: Produces optimized intermediate representation with register assignments  
**Type Safety**: Maintains proper type casting for register loads and memory operations

## Algorithm Implementation

### 1. Liveness Analysis
The system performs liveness analysis to determine which variables are live at each program point, providing:
- IN sets for each statement node
- Live variable information at method return points
- Support for various statement types (assignments, loops, conditionals)

### 2. Live Interval Computation
- Uses depth-first visitor pattern to traverse the AST
- Computes live ranges for each variable
- Provides program point mapping for register allocation decisions

### 3. Linear Scan Register Allocation
- Sorts variables by their live range start points
- Assigns available registers to variables
- Spills variables to memory when no registers are available
- Optimizes register reuse after variable lifetimes end

## Input/Output Specification

### Input Format
```java
/*<number_of_registers>*/
// Java program with variable declarations and assignments
```

### Output Format
The generated code includes:
- **Static import**: `import static a3.Memory.*;`
- **Register declarations**: `Object Rx;` (generic Object type)
- **Memory allocation**: `alloca(SIZE);` for spilled variables
- **Register operations**: Direct assignments `Rx = Expression;`
- **Memory operations**: `store(INDEX, Expression)` and `((TYPE) load(INDEX))`
- **Type casting**: `((TYPE) Rx)` for register loads

## Example

### Input (TC01.java)
```java
/*1*/
class TC01 {
  public static void main(String[] args) {
    TestTC01 o;
    int res;
    o = new TestTC01();
    res = o.foo();
    System.out.println(res);
  }
}
class TestTC01 {
  public int foo() {
    int a, b, x;
    boolean e;
    a = 1;
    b = 2;
    e = a <= b;
    while(e) {
      b = a + b;
      e = b <= a;
    }
    b = b;
    x = 2;
    b = x;
    return b;
  }
}
```

### Live Range Analysis
```
TC01.main:
  o:   [4, 4]
  res: [5, 5]

TC01.foo:
  a: [11, 15]
  b: [12, 19] 
  e: [13, 13]
  x: [18, 18]
```

### Register Allocation (1 register available)
```
TC01.main:  o: R0, res: R0
TC01.foo:   a: spilled(1), b: spilled(0), e: R0, x: R0
```

### Output
```java
/*1*/
import static a3.Memory.*;

class TC01 {
  public static void main(String[] args) {
    Object R0;
    alloca(0);
    R0 = new TestTC01();
    R0 = ((TestTC01) R0).foo();
    System.out.println(((int) R0));
  }
}

class TestTC01 {
  public int foo() {
    Object R0;
    alloca(2);
    store(1, 1);                    // a = 1 (spilled to index 1)
    store(0, 2);                    // b = 2 (spilled to index 0)  
    R0 = ((int) load(1)) <= ((int) load(0)); // e = a <= b
    while(((boolean) R0)) {
      store(0, ((int) load(1)) + ((int) load(0))); // b = a + b
      R0 = ((int) load(0)) <= ((int) load(1));     // e = b <= a
    }
    store(0, ((int) load(0)));      // b = b
    R0 = 2;                         // x = 2
    store(0, ((int) R0));           // b = x
    return ((int) load(0));         // return b
  }
}
```


## Build and Run

### Prerequisites
- Java Development Kit (JDK 8+)
- JavaCC parser generator
- JTB (Java Tree Builder)

### Compilation
```bash
javac Main.java
```

### Execution
```bash
java Main <inputfile >outputfile
```

### Example Usage
```bash
java Main <testcase/TC01.java >output/TC01_output.java
```

## Contributing

This project was developed as part of the CS614 Advanced Compilers course assignment at IIT Bombay. The implementation demonstrates practical compiler optimization techniques used in modern compiler infrastructures.

## Acknowledgments

- **Prof. Manas Thakur** for course guidance and compiler design principles
- **CS614 Course Materials** for theoretical foundations and algorithm specifications
- **JavaCC/JTB Communities** for parser generation tools and documentation

---

*This project showcases advanced compiler optimization techniques and demonstrates the practical implementation of register allocation algorithms in real-world compiler design.*