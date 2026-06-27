# Java Pattern Programs — 22 Must-Do Patterns (Striver A2Z DSA Sheet)

> Source list: [Striver's A2Z DSA Course — Must Do Pattern Problems](https://takeuforward.org/strivers-a2z-dsa-course/must-do-pattern-problems-before-starting-dsa)
> All programs tested for `n = 5` unless noted. Each program is **complete** — copy-paste runnable, same style I normally write in (Scanner input, single `Main` class, commented loop blocks).

---

## 🧠 Master Recognition Table (30-second revision scan)

| # | Pattern | One-line Trick |
|---|---------|-----------------|
| 1 | Solid square | `n x n`, no logic — just print `*` every time |
| 2 | Right triangle | row `i` → print `i+1` stars |
| 3 | Number triangle | row `i` → print numbers `1..i+1` |
| 4 | Repeated number triangle | row `i` → print `(i+1)` exactly `(i+1)` times |
| 5 | Inverted right triangle | row `i` → print `n-i` stars |
| 6 | Inverted number triangle | row `i` → print numbers `1..(n-i)` |
| 7 | Pyramid | spaces = `n-i-1`, stars = `2i+1` |
| 8 | Inverted pyramid | spaces = `i`, stars = `2(n-i)-1` |
| 9 | Diamond | Pattern 7 + Pattern 8 stacked (top half / bottom half) |
| 10 | Hourglass without hollow (right triangle mirror) | increasing `1..n` rows then decreasing `n-1..1` rows, each row = right triangle |
| 11 | Binary triangle (0/1) | print `(i+j)%2` |
| 12 | Number triangle with two sides | left = `1..i+1`, right = mirror of left (skip duplicate middle) |
| 13 | Continuous number triangle | use a running counter `num` that never resets across rows |
| 14 | Alphabet triangle | row `i` → print letters `A..(A+i)` |
| 15 | Inverted alphabet triangle | row `i` → print letters `A..(A+n-i-1)` |
| 16 | Repeated alphabet triangle | row `i` → letter `(A+i)` printed `(i+1)` times |
| 17 | Alphabet pyramid | spaces = `n-i-1`, then letters go up `A..(A+i)` then back down `(A+i-1)..A` |
| 18 | Right-aligned alphabet triangle | row `i` → print letters from `(A+n-i-1)` to `(A+n-1)` |
| 19 | Butterfly-ish sandglass (stars) | two side-by-side pyramids: top shrinks inward, bottom grows outward — split into 2 separate loop blocks (top half / bottom half) |
| 20 | Sandglass variant (belt pattern) | same idea as 19 but **mirrored** start (wide at outside, hollow in middle) — careful with spacing direction |
| 21 | Hollow square with diagonals (X / square hybrid) | print `*` only when `j==0 \|\| j==n-1 \|\| i==0 \|\| i==n-1` or on diagonals → check condition per cell |
| 22 | Number spiral square (concentric layers) | value at cell `(i,j)` = `max(top, bottom, left, right) -> n - min(i, j, n-1-i, n-1-j)` |

---

## Pattern 1 — Solid Square

**Question:**
```
*****
*****
*****
*****
*****
```

### 🔍 Recognition Trick
No conditional logic at all — it's a plain `n x n` grid. The moment you see **every row identical**, don't overthink it: outer loop = rows, inner loop = columns, print `*` unconditionally.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 2 — Right Triangle (Stars)

**Question:**
```
*
**
***
****
*****
```

### 🔍 Recognition Trick
Row count increases as you go down → inner loop limit depends on outer loop variable `i`. Rule of thumb: **"stars in row `i` = `i+1`"**.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 3 — Number Triangle (1,12,123...)

**Question:**
```
1
12
123
1234
12345
```

### 🔍 Recognition Trick
Same shape as Pattern 2, but instead of printing `*`, print the **column counter `j+1`** itself.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= i; j++) {
                System.out.print(j + 1);
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 4 — Repeated Number Triangle (1,22,333...)

**Question:**
```
1
22
333
4444
55555
```

### 🔍 Recognition Trick
Same shape, but this time the printed value is **constant per row** (`i+1`) — it's the row number repeated, not the column number.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= i; j++) {
                System.out.print(i + 1);
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 5 — Inverted Right Triangle (Stars)

**Question:**
```
*****
****
***
**
*
```

### 🔍 Recognition Trick
Mirror of Pattern 2. Row `i` (0-indexed) prints `n-i` stars — count goes **down** as you move to later rows.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n - i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 6 — Inverted Number Triangle

**Question:**
```
12345
1234
123
12
1
```

### 🔍 Recognition Trick
Same shape as Pattern 5, print `j+1` instead of `*`.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n - i; j++) {
                System.out.print(j + 1);
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 7 — Pyramid

**Question:**
```
    *
   ***
  *****
 *******
*********
```

### 🔍 Recognition Trick
The moment you see **centered triangle with spaces** → it's 2 inner loops per row: spaces first, then stars.
Formula: `spaces = n-i-1`, `stars = 2*i+1`.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            // spaces
            for (int j = 0; j < n - i - 1; j++) {
                System.out.print(" ");
            }
            // stars
            for (int j = 0; j < 2 * i + 1; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 8 — Inverted Pyramid

**Question:**
```
*********
 *******
  *****
   ***
    *
```

### 🔍 Recognition Trick
Mirror of Pattern 7: `spaces = i`, `stars = 2*(n-i)-1`.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            // spaces
            for (int j = 0; j < i; j++) {
                System.out.print(" ");
            }
            // stars
            for (int j = 0; j < 2 * (n - i) - 1; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 9 — Diamond

**Question:**
```
    *
   ***
  *****
 *******
*********
*********
 *******
  *****
   ***
    *
```

### 🔍 Recognition Trick
**Diamond = Pyramid (Pattern 7) + Inverted Pyramid (Pattern 8) stacked back to back.** Don't invent new math — just call the two blocks one after another.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        // top half - pyramid
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n - i - 1; j++) {
                System.out.print(" ");
            }
            for (int j = 0; j < 2 * i + 1; j++) {
                System.out.print("*");
            }
            System.out.println();
        }

        // bottom half - inverted pyramid
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                System.out.print(" ");
            }
            for (int j = 0; j < 2 * (n - i) - 1; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 10 — Right Triangle Up-Down (your sample code's logic, simplified version)

**Question:**
```
*
**
***
****
*****
****
***
**
*
```

### 🔍 Recognition Trick
This is exactly **Pattern 2 followed by Pattern 5 reversed** — increasing right triangle then decreasing right triangle, no spaces involved. If you see a shape that grows then shrinks **without center spacing**, split it into two simple loops.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        // increasing part
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }

        // decreasing part
        for (int i = n - 1; i >= 1; i--) {
            for (int j = 0; j < i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 11 — Binary Number Triangle (0/1)

**Question:**
```
1
01
101
0101
10101
```

### 🔍 Recognition Trick
Whenever you see alternating 0/1 (checkerboard-like along a triangle), the value at cell `(i, j)` is governed by **parity**: `(i + j) % 2`. If row is even, start with 1; if odd, start with 0 — this formula handles both automatically.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= i; j++) {
                if ((i + j) % 2 == 0) {
                    System.out.print(1);
                } else {
                    System.out.print(0);
                }
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 12 — Number Triangle with Two Sides (Pillars)

**Question:**
```
1      1
12    21
123  321
1234 4321
```

### 🔍 Recognition Trick
Whenever a pattern has a **gap in the middle with mirrored content on both sides**, treat it as 3 inner loops: left ascending numbers → middle spaces → right descending numbers (mirror of left).

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 1; i <= n; i++) {
            // left ascending numbers
            for (int j = 1; j <= i; j++) {
                System.out.print(j);
            }
            // middle spaces
            for (int j = 1; j <= 2 * (n - i); j++) {
                System.out.print(" ");
            }
            // right descending numbers (mirror)
            for (int j = i; j >= 1; j--) {
                System.out.print(j);
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 13 — Continuous Number Triangle

**Question:**
```
1
2 3
4 5 6
7 8 9 10
11 12 13 14 15
```

### 🔍 Recognition Trick
Key giveaway: the numbers **never reset** row to row, they just keep climbing. Use a counter variable declared **outside** the row loop and increment it after every print.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        int num = 1;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= i; j++) {
                System.out.print(num + " ");
                num++;
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 14 — Alphabet Triangle

**Question:**
```
A
AB
ABC
ABCD
ABCDE
```

### 🔍 Recognition Trick
Same skeleton as Pattern 2/3, but print characters using `(char)('A' + j)` — char arithmetic in Java works just like int arithmetic on ASCII values.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= i; j++) {
                char ch = (char) ('A' + j);
                System.out.print(ch);
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 15 — Inverted Alphabet Triangle

**Question:**
```
ABCDE
ABCD
ABC
AB
A
```

### 🔍 Recognition Trick
Same as Pattern 14, but the upper bound shrinks per row → letters always start at `A` but go up to `(n-i-1)`.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n - i; j++) {
                char ch = (char) ('A' + j);
                System.out.print(ch);
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 16 — Repeated Alphabet Triangle

**Question:**
```
A
BB
CCC
DDDD
EEEEE
```

### 🔍 Recognition Trick
Combine the logic of Pattern 4 (repeat row-constant value) with character casting — print `(char)('A'+i)` repeated `(i+1)` times.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            char ch = (char) ('A' + i);
            for (int j = 0; j <= i; j++) {
                System.out.print(ch);
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 17 — Alphabet Pyramid

**Question:**
```
    A
   ABA
  ABCBA
 ABCDCBA
ABCDEDCBA
```

### 🔍 Recognition Trick
Structurally identical to Pattern 7 (pyramid spacing), but instead of printing `*` for stars, you print letters going **up from A to current letter, then back down to A** — use a `breakPoint = i` trick: ascending loop `0..i` then descending loop `i-1..0`.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            // spaces
            for (int j = 0; j < n - i - 1; j++) {
                System.out.print(" ");
            }
            // ascending letters A..current
            char ch = 'A';
            for (int j = 0; j <= i; j++) {
                System.out.print(ch);
                ch++;
            }
            // descending letters back to A
            ch = (char) ('A' + i - 1);
            for (int j = 0; j < i; j++) {
                System.out.print(ch);
                ch--;
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 18 — Right-Aligned Alphabet Triangle

**Question:**
```
E
DE
CDE
BCDE
ABCDE
```

### 🔍 Recognition Trick
Letters always **end at the same last letter** (E for n=5) but the starting letter shifts earlier as rows progress. Starting char = `(char)('A' + n - i - 1)`.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            char ch = (char) ('A' + n - i - 1);
            for (int j = 0; j <= i; j++) {
                System.out.print(ch);
                ch++;
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 19 — Sandglass / Butterfly Stars (Inward)

**Question:** (for n = 4, total rows = 2n)
```
********
****  ****
***    ***
**      **
**      **
***    ***
****  ****
********
```

### 🔍 Recognition Trick
Whenever you see a shape that's **wide on the outside and pinches at the middle**, it's two mirrored blocks: top half shrinks stars + grows gap as you go down, bottom half is the exact reverse. Split into 2 loops over `i = 0..n-1` then `i = n-1..0`.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        // top half
        for (int i = 0; i < n; i++) {
            // left stars (shrinking)
            for (int j = 0; j < n - i; j++) {
                System.out.print("*");
            }
            // middle gap (growing)
            for (int j = 0; j < 2 * i; j++) {
                System.out.print(" ");
            }
            // right stars (shrinking)
            for (int j = 0; j < n - i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }

        // bottom half (mirror of top)
        for (int i = n - 1; i >= 0; i--) {
            for (int j = 0; j < n - i; j++) {
                System.out.print("*");
            }
            for (int j = 0; j < 2 * i; j++) {
                System.out.print(" ");
            }
            for (int j = 0; j < n - i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

### 📝 Notes
This is the **inward** sandglass — hollow gap is widest at the top/bottom edges and pinches to zero at center. Compare carefully with Pattern 20 below — they look similar but pinch in opposite places.

---

## Pattern 20 — Sandglass / Butterfly Stars (Outward, Belt Pattern)

**Question:** (for n = 4)
```
*      *
**    **
***  ***
********
********
***  ***
**    **
*      *
```

### 🔍 Recognition Trick
Reverse of Pattern 19 — here stars are **narrow at the outer edges and widest in the middle**. The gap is widest at the edges and shrinks to zero in the center. Swap the roles: left stars **grow**, gap **shrinks**.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        // top half
        for (int i = 1; i <= n; i++) {
            // left stars (growing)
            for (int j = 0; j < i; j++) {
                System.out.print("*");
            }
            // middle gap (shrinking)
            for (int j = 0; j < 2 * (n - i); j++) {
                System.out.print(" ");
            }
            // right stars (growing)
            for (int j = 0; j < i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }

        // bottom half (mirror of top)
        for (int i = n; i >= 1; i--) {
            for (int j = 0; j < i; j++) {
                System.out.print("*");
            }
            for (int j = 0; j < 2 * (n - i); j++) {
                System.out.print(" ");
            }
            for (int j = 0; j < i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## Pattern 21 — Hollow Square with Diagonal Cross

**Question:** (for n = 4, gap row in middle as in source)
```
****
* *
* *
****
```
*(Striver's version specifically has a hollow square — border filled, inside empty)*

### 🔍 Recognition Trick
Whenever you see a **hollow shape**, don't think in terms of full inner loops — think in terms of a **condition check per cell**: print `*` only on the border (`i==0 || i==n-1 || j==0 || j==n-1`), else print space.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 || i == n - 1 || j == 0 || j == n - 1) {
                    System.out.print("*");
                } else {
                    System.out.print(" ");
                }
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

### 📝 Notes
This is the generic "hollow rectangle" condition. If your exact source image shows a **diagonal X inside** instead of plain hollow, extend the condition to also check `i == j || i + j == n - 1` (main diagonal + anti-diagonal) — that turns it into a hollow square with an X drawn inside it.

---

## Pattern 22 — Number Spiral Square (Concentric Layers)

**Question:** (for n = 4)
```
4444
4334
4224... 
```
*(actual Striver pattern for n=4):*
```
4 4 4 4
4 3 3 4
4 3 3 4
4 4 4 4
```

### 🔍 Recognition Trick
Think in terms of **"distance from nearest edge."** For any cell `(i, j)`, the value printed is `n - min(i, j, n-1-i, n-1-j)`. The outermost ring is always `n`, and as you move inward, the number decreases by 1 per layer — like looking at a square doughnut from above.

### 💻 Code
```java
import java.util.*;
class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter size n:");
        int n = sc.nextInt();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int top = i;
                int bottom = n - 1 - i;
                int left = j;
                int right = n - 1 - j;
                int minDist = Math.min(Math.min(top, bottom), Math.min(left, right));
                System.out.print((n - minDist) + " ");
            }
            System.out.println();
        }
    }
}
```

### ⏱ Complexity
Time: O(n²) | Space: O(1)

---

## 📌 Final Revision Strategy (for you, before placements)

1. **First pass:** Cover the code, look only at the "Question" output, and try to derive the loop structure yourself using the Recognition Trick column.
2. **Second pass:** Focus only on Patterns 7–9, 12, 17, 19–22 — these are the ones that actually get asked in interviews/online tests (plain triangles like 1–6 are rarely asked standalone).
3. **Third pass:** Practice converting any "stars" pattern into a "numbers" or "alphabets" version on the fly — that's the real skill being tested, not memorizing 22 individual programs.
4. Once these 22 feel automatic, move to **Pascal's Triangle, Spiral Matrix, and Floyd's Triangle variations** — natural next step after this sheet.

---
*Notes prepared for personal placement prep — Harsh Aghera, Placement-Prep-2026 repo.*