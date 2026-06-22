# Java Foundations — Placement Edition
### Notes from a "been-there, interviewed-that" professor's lens

---

## A Word Before We Start

I've sat on both sides of the interview table — and I'll tell you a secret: nobody fails an interview because they didn't know what a `for` loop is. They fail because they treat these basics as "done and dusted" too early, and then freeze when an interviewer twists the question slightly — "what if `n` is negative?", "what's the time complexity if `b` is a `BigInteger`?", "why `String` and not `char[]`?"

So this isn't just a syntax dump. Every section now has three layers:
1. **What it is** (you already half-know this)
2. **Why it's asked / where it bites you** (interview reality)
3. **How to not embarrass yourself** (the professor's red flags)

Treat this as your revision spine, not your first read.

---

## 1. Input & Output

### Concept
Input = data flowing **into** your program. Output = data flowing **out**.

### Syntax
```java
import java.util.Scanner;

Scanner sc = new Scanner(System.in);
int n = sc.nextInt();
System.out.println(n);
```

### Professor's Note — Scanner vs BufferedReader
In DSA practice and most interview platforms, `Scanner` is fine. But the moment you're solving 10⁵+ inputs (competitive programming, some online assessments), `Scanner` is noticeably slower than `BufferedReader` + `StringTokenizer`, because `Scanner` does regex-based parsing internally.

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
int n = Integer.parseInt(br.readLine().trim());
```

**Interview trap:** "Why is your program TLE-ing (Time Limit Exceeded) on large input?" — 90% of the time, the honest answer for a beginner is "I used `Scanner` in a tight loop." Know this trade-off; it shows maturity beyond syntax.

### Complexity
- Reading a single token: O(1) amortized
- `System.out.println` in a loop: each call has overhead (flushes); for bulk output, prefer `StringBuilder` + one final print.

### Memory Tip
`Scanner` → take it in. `println` → push it out. `BufferedReader` → take it in *fast*.

---

## 2. Variables

### Concept
A variable is a named, typed storage location. In Java, **every variable must be declared with a type** — this is what makes Java statically typed, unlike Python or JS.

```java
int age = 20;
double salary = 50000.5;
char grade = 'A';
String name = "Harsh";
```

### Professor's Note — Stack vs Heap
This is the single most under-tested-but-frequently-asked concept for freshers:
- Primitive variables (`int`, `char`, `double`...) live on the **stack** (when local).
- Objects (`String`, arrays, custom classes) — the **reference** lives on the stack, the **actual object** lives on the **heap**.

**Interview trap:** "If I pass an `int` to a method and modify it, does the caller see the change? What about an array?" → Java is "pass by value" *always*, but for objects, the value being passed is the *reference* (address). This single question filters out people who memorized syntax from people who understand the model.

### Memory Tip
`datatype variableName = value;`
Primitive → stack. Object → reference on stack, data on heap.

---

## 3. Data Types

### Concept
Decides storage size, range, and the operations allowed.

| Type | Size | Example | Range (approx) |
|---|---|---|---|
| `byte` | 1 byte | `10` | -128 to 127 |
| `short` | 2 bytes | `1000` | -32,768 to 32,767 |
| `int` | 4 bytes | `10` | ~-2.1B to 2.1B |
| `long` | 8 bytes | `10000000000L` | ~±9.2 × 10¹⁸ |
| `float` | 4 bytes | `10.5f` | ~7 decimal digits precision |
| `double` | 8 bytes | `10.5` | ~15 decimal digits precision |
| `char` | 2 bytes | `'A'` | 0 to 65,535 (Unicode) |
| `boolean` | 1 bit (JVM-dependent) | `true` | true/false |

### Professor's Note — The Overflow Trap
This is **the** classic DSA bug that even decent students make under pressure:

```java
int a = 1_000_000_000;
int b = 1_000_000_000;
int sum = a + b;        // overflow! int max ~2.1B, this wraps to negative
long sum2 = (long) a + b; // correct: cast BEFORE the addition
```

**Interview trap:** "Find the product of two large integers in an array." If you don't reason about overflow out loud, you lose points even if your logic is otherwise correct. Always ask yourself: *what's the maximum possible value here, and does it fit in an `int`?*

Also know: `char` in Java is **Unicode (2 bytes)**, not ASCII (1 byte) like in C — this surprises a lot of people coming from C/C++ backgrounds.

### Memory Tip
Whole numbers → `int`/`long`. Decimals → `double` (default; `float` only if memory-constrained). One character → `char`. Text → `String`. True/false → `boolean`.

---

## 4. Operators

### Arithmetic
```java
+  -  *  /  %
```
```java
10 % 3   // 1
-10 % 3  // -1  (sign follows the dividend in Java, NOT like Python)
```

**Interview trap:** Java's `%` with negative numbers behaves differently from Python's. If you've practiced on LeetCode (Java) vs a Python tutorial, this WILL trip you up in a live coding round. Always verbalize this if negatives are in play.

### Comparison
```java
== != > < >= <=
```

**Critical interview trap:** `==` on objects (like `String`) compares **references**, not content, unless it's a primitive.
```java
String a = new String("hi");
String b = new String("hi");
System.out.println(a == b);        // false
System.out.println(a.equals(b));   // true
```
This is one of the most commonly asked "gotcha" questions in fresher interviews — know it cold.

### Logical
```java
&& || !
```
Both `&&` and `||` are **short-circuit** operators — the second operand isn't evaluated if the first already decides the result. This matters when the second operand has side effects (e.g., a method call that could throw an exception).

```java
if (arr != null && arr.length > 0) // safe: short-circuit prevents NullPointerException
```

### Memory Tip
`%` → remainder (sign follows dividend). `&&`/`||` → short-circuit. `==` on objects → reference check, use `.equals()` for content.

---

## 5. If-Else

### Concept
Decision-making based on boolean conditions.

```java
if (condition) {
    // ...
} else {
    // ...
}
```

```java
int age = 18;
if (age >= 18) {
    System.out.println("Eligible");
} else {
    System.out.println("Not Eligible");
}
```

### Professor's Note — Readability Under Pressure
In a live interview, *always* use braces `{}` even for single-line blocks. It costs you nothing and prevents the classic "dangling else" bug — interviewers notice this discipline.

### Complexity
O(1) — but if conditions involve function calls or comparisons on large data structures, factor that cost in separately.

### Memory Tip
True → `if` block. False → `else` block. Brace everything.

---

## 6. Switch

### Concept
A cleaner alternative to a long if-else-if chain, when comparing one variable against multiple discrete values.

```java
switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    default:
        System.out.println("Invalid");
}
```

### Professor's Note — Modern Java Syntax
Since Java 14+, switch expressions are cleaner and remove the fall-through bug entirely:

```java
String result = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    default -> "Invalid";
};
```

**Interview trap:** "What happens if you forget `break`?" → execution **falls through** to the next case, silently. This single missing keyword has caused real production bugs — interviewers love asking this because it tests attention to detail, not cleverness.

### Memory Tip
Forget `break` → fall-through bug. New Java → use `->` arrow syntax to avoid the problem entirely.

---

## 7. Loops

### Why Loops Exist
Computation is repetition. Mastering loop *patterns* (not just syntax) is 50% of solving DSA problems.

### For Loop
```java
for (int i = 0; i < n; i++) {
    System.out.println(i);
}
```
Time Complexity: **O(n)**

### While Loop
```java
int n = 5;
while (n > 0) {
    System.out.println(n);
    n--;
}
```
Use when the number of iterations is **not known in advance** (e.g., processing digits of a number, traversing until a condition flips).

### Do-While Loop
```java
do {
    // ...
} while (condition);
```
Guarantees the body runs **at least once** — useful for menu-driven programs, input validation loops.

### Professor's Note — Loop Patterns You MUST Recognize Instantly
These four patterns cover a disproportionate share of fresher DSA questions:

1. **Digit extraction**: `while(n > 0) { digit = n % 10; n = n / 10; }`
2. **Two-pointer**: `while(left < right) { ... left++; right--; }`
3. **Sliding window**: expanding/contracting a window with two indices
4. **Nested loop for pairs/triplets**: O(n²)/O(n³) brute-force comparisons

When you see a problem, your first instinct shouldn't be "let me write a loop" — it should be "which of these four patterns does this resemble?"

### Memory Tip
`for` → known iteration count. `while` → unknown, condition-driven. `do-while` → run-then-check.

---

## 8. Functions / Methods

### Concept
A reusable, named block of logic — the single biggest tool for writing maintainable, testable code (and yes, interviewers grade on this).

```java
public static int add(int a, int b) {
    return a + b;
}
```

```java
int ans = add(5, 3); // 8
```

### Professor's Note — Why "static" Matters Here
You'll write `public static` a hundred times before truly understanding it. `static` means the method belongs to the **class**, not an instance — that's why you can call `add(5,3)` directly from `main` (which is itself static) without creating an object first.

**Interview trap:** "Why can't a static method access a non-static (instance) variable directly?" → because a static method exists *before* any object is created; instance variables belong to objects. This question separates rote memorizers from people who get the model.

Also — practice writing **recursive** functions early. Recursion (factorial, Fibonacci, tree traversal, backtracking) is asked far more than people expect at the fresher level, and a shaky base-case instinct shows immediately.

### Memory Tip
Method = machine: input → processing → output.
`static` = belongs to the class, not an object.

---

## 9. Time Complexity Basics

### O(1) — Constant
```java
int sum = a + b;
```

### O(n) — Single Loop
```java
for (int i = 0; i < n; i++) { ... }
```

### O(n²) — Nested Loop
```java
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) { ... }
}
```

### Professor's Note — Beyond the Cheat Sheet
Knowing "single loop = O(n)" is table stakes. What separates a placement-ready candidate is being able to answer:

- "What's the time complexity if the loop's bound depends on a **changing** variable?" (e.g., `for(int i=1; i<n; i*=2)` → O(log n))
- "What's the **space** complexity, not just time?" (recursion uses stack space — O(n) for n recursive calls, even if there's no explicit array)
- "Can you trade space for time here?" (hashmaps to convert O(n²) brute force into O(n))

Always state **both** time and space complexity out loud in an interview, even if not asked. It signals you think about trade-offs by default, not as an afterthought.

### Memory Tip
Single loop → O(n). Nested loop → O(n²). Halving each step → O(log n). Recursion depth → O(n) space, not "free."

---

## Quick Revision Cheatsheet

| Need | Tool |
|---|---|
| Input | `Scanner` (or `BufferedReader` for speed) |
| Output | `println` (or `StringBuilder` for bulk) |
| Remainder | `%` (sign follows dividend) |
| Decision | `if-else` / `switch` |
| Repetition | `for` / `while` / `do-while` |
| Reusable logic | methods (`static` vs instance) |
| Content comparison | `.equals()`, never `==` for objects |
| Single loop | O(n) |
| Nested loop | O(n²) |
| Halving loop | O(log n) |

---

## Interview Quick-Fire Notes

1. `n % 10` → last digit. `n / 10` → strip last digit.
2. `while(n > 0)` → standard digit-processing skeleton.
3. `==` on objects checks reference, not value — always `.equals()` for `String`/objects.
4. Java's `%` keeps the sign of the dividend, unlike Python.
5. `int` overflows around ±2.1 billion — cast to `long` *before* the operation that risks overflow, not after.
6. Always state time **and** space complexity, unprompted.
7. Forgetting `break` in `switch` → silent fall-through bug.
8. Recursion has a hidden space cost: the call stack.
9. Pass-by-value in Java — but for objects, the "value" is the reference.

---

## Problems Solved — Worked Through Properly

### ✅ Count Digits
**Pattern used:** digit-extraction loop
**Key learnings:**
- `n / 10` removes the last digit each iteration.
- Loop condition `while(n != 0)` rather than `n > 0` if negative numbers are possible.
- **Edge case interviewers always probe:** what does your code output for `n = 0`? (Should be 1 digit, not 0 — a common off-by-one bug.)

### ✅ Reverse a Number
**Pattern used:** digit-extraction + reconstruction
**Key learnings:**
- `digit = n % 10`, then `rev = rev * 10 + digit`, then `n = n / 10`.
- **Edge case interviewers always probe:** what if the reversed number overflows `int`? (e.g., reversing `1534236469` overflows 32-bit `int`.) The mature answer: use `long` for `rev`, then check bounds before returning.

---

## Closing Thought from the Professor's Chair

At the fresher level, interviewers are rarely testing whether you *know* a `for` loop. They're testing whether you can **reason about edge cases, complexity trade-offs, and the underlying model** (stack/heap, pass-by-value, fall-through) under mild pressure. Treat every "basic" topic above as a launchpad for a "but what if...?" question — because that's exactly how it'll be asked.

Next logical step in your prep: take each of the "Interview trap" callouts above and write a 2–3 line spoken answer for it, out loud, as if explaining to an interviewer. That rehearsal — not re-reading syntax — is what converts "I know this" into "I can defend this."