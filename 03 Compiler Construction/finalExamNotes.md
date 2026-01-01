## 1. Shift Reduce Parser 
A Shift-Reduce Parser is a bottom-up parsing technique used in syntax analysis to check whether a given input string follows the rules of a grammar.

In simple words, it builds the parse tree from the input symbols up to the start symbol by repeatedly shifting symbols and reducing them using grammar rules. 


### Core idea 

**Shift** → move the next input symbol onto the stack.  

**Reduce** → replace a group of symbols on the stack with a grammar rule’s left side.

**Input Buffer:** → This stores the string or sequence of tokens that needs to be parsed.

**Stack:** → The parser uses a stack to keep track of which symbols or parts of the parse it has already processed. As it processes input, symbols are pushed onto and popped off the stack.

**Parsing Table:** → Similar to a predictive parser, a parsing table helps the parser decide what action to take next.  

**Accept:** → Successfully complete parsing when the entire input is processed and the stack contains only the start symbol.  
**Error:** → Handle unexpected or invalid input when no shift or reduce action is possible   

## Working of Shift Reduce Parser
Shift-reduce parsers use a **Deterministic Finite Automaton (DFA)** to help recognize these handles. The DFA helps track what symbols are on the stack and decides when to shift or reduce by following a set of rules. Instead of directly analyzing the structure, the DFA helps the parser determine when reductions should occur based on the stack's contents.    

## Difference Between Shift Reduce Parser and Predictive Parser 

### Predictive Parser
A Predictive Parser is a top-down parser. It starts parsing from the start symbol and moves step by step towards the input string. It uses leftmost derivation, which means it always expands the leftmost non-terminal first. The parser predicts which grammar rule to apply by looking at the next input symbol, so it does not need backtracking. Predictive parsers work only for LL(1) grammars, so left recursion is not allowed.  

The parsing decisions are made using a table created from FIRST and FOLLOW sets. This parser is easy to implement but it is less powerful because it can handle only simple grammars.

### Shift Reduce Parser 
A Shift-Reduce Parser is a bottom-up parser. It starts parsing from the input string and tries to reduce it until it reaches the start symbol. It uses rightmost derivation in reverse order. This parser works using two main actions: shift, where input symbols are pushed onto a stack, and reduce, where grammar rules are applied to replace symbols on the stack.  

Shift-reduce parsers are used for LR grammars, and left recursion is allowed in them. They use ACTION and GOTO tables to decide parsing steps. These parsers are more powerful and are commonly used in real compilers, but they are more difficult to implement compared to predictive parsers.   



## 2. Operator precedence parser   

An **Operator Precedence Parser** is a **bottom-up parser** used in **compiler construction** to parse expressions involving operators (like `+`, `-`, `*`, `/`) in programming languages.

* It **only works on operator grammars**, i.e., grammars where the main constructs are operators and operands.
* **Ambiguous grammars** are not allowed (except in special cases handled by operator precedence).
* The parser decides **how to group operators and operands** based on their **precedence and associativity**.  

**Operator Grammar** have the restriction that no production has either an empty right-hand side (null productions) or two adjacent non-terminals in its right-hand side.

* **Example**
1) This is the operator grammar   E->E+E/E*E/id 
2) This is not operator grammar    
S->SAS/a   
A->bSb/b   

We can convert it into an operator grammar, though:

S->SbSbS/SbS/a  
A->bSb/b 

<br>

### Why do we use it?

We use an operator precedence parser because:

1. **Expressions are common in programming languages**, e.g., `a + b * c`.
2. It helps **resolve ambiguities** like which operator to evaluate first (multiplication vs addition).
3. It is **efficient for arithmetic and logical expressions**.
4. Works automatically based on **operator precedence rules**, without writing a lot of parsing code.


<br>

### How it works (basic idea)

The parser uses **precedence relations** between operators:

* **`a ⋖ b`** → `a` **yields precedence to** `b`. (Next operator has higher precedence)
* **`a ≐ b`** → `a` and `b` **have the same precedence** (used for associativity)
* **`a ⋗ b`** → `a` **takes precedence over** `b`. (Reduce happens here)

**Example:**

For `a + b * c`:

* `+ ⋖ *` → `+` yields to `*` → multiply first.
* `* ⋗ +` → `*` takes precedence over `+` → then add.

<br>

### How we determine precedence

There are **two methods**:

1. **Conventional method**: Use standard operator precedence and associativity, e.g.,

   * `*` and `/` have higher precedence than `+` and `-`
   * `+` and `-` are left-associative

2. **Grammar-based method**: Construct an **unambiguous grammar** where the parse tree naturally reflects the correct precedence.

<br>

### Advantages

1. **Efficient for expressions**: Good for arithmetic and logical operators.
2. **Simple to implement**: Only needs operator tables and a stack.
3. **Handles associativity**: Correctly resolves left/right associativity.
4. **Bottom-up parsing**: Can be used for real-time expression evaluation.



### Disadvantages

1. **Limited to operator grammars**: Cannot parse general context-free grammars.
2. **Cannot handle ambiguity** in general (except in operators).
3. **Precedence table construction** can be tricky for many operators.
4. **Not suitable for complex languages** with nested statements, loops, etc.



### What is the Left and Right Associativity  
- Left-associative → Go left to right
- Right-associative → Go right to left

**Example** 

| Operator | Associativity | Example     | Evaluated as  |
| -------- | ------------- | ----------- | ------------- |
| `+`      | Left          | `a + b + c` | `(a + b) + c` |
| `-`      | Left          | `a - b - c` | `(a - b) - c` |
| `^`      | Right         | `a ^ b ^ c` | `a ^ (b ^ c)` |


___

<br>

## 3. What is Ambiguous Grammar?

A **grammar is said to be ambiguous** if **one string (sentence) can have more than one parse tree** or **more than one leftmost/rightmost derivation**.

👉 This creates **confusion for the compiler** because it cannot decide **which structure or meaning is correct**.

### **Example of Ambiguous Grammar**

```
E → E + E
E → E * E
E → id
```

For the input:

```
id + id * id
```

This grammar can generate **two different parse trees**:

* `(id + id) * id`
* `id + (id * id)`

So this grammar is **ambiguous**.



###  Why is Ambiguity a Problem?

* Compiler **cannot decide precedence** of operators
* Leads to **incorrect program interpretation**
* Different parse trees → **different meanings**
* Makes **code generation unreliable**


###  Ambiguous Grammar Resolution

Since ambiguity causes problems, we **resolve it** using the following methods:

#### **Method 1: Rewrite the Grammar (Best Method)**

Rewrite grammar to make it **unambiguous** by **separating precedence levels**.

**Unambiguous Grammar Example**

```
E → E + T | T
T → T * F | F
F → id
```

Now:

* `*` has higher precedence than `+`
* Only **one parse tree** exists for each string



#### **Method 2: Operator Precedence & Associativity Rules**

Used in **operator precedence parsers**:

* Define precedence:

  * `*` > `+`
* Define associativity:

  * `+`, `-` → left associative
  * `^` → right associative

⚠️ Grammar may remain ambiguous, but **parser behavior is fixed**.



#### **Method 3: Disambiguation Rules (Parser-level)**

Some parsers use rules like:

* **“Shift over reduce”**
* **“Reduce using higher precedence operator”**

👉 Common in **YACC / Bison**.



### Advantages of Resolving Ambiguity
* Correct syntax tree generation
* Accurate semantic analysis
* Proper code generation
* Reliable compiler behavior



###  Disadvantages / Limitations
* Detecting ambiguity is **not always possible**
* Rewriting grammar can make it **long and complex**
* Operator precedence rules are **parser-dependent**


## Semantic Analysis

**Semantic analysis** is the **phase of a compiler** that checks whether a program is **meaningful and logically correct**, not just syntactically correct.

When a compiler processes a program, the **syntax analysis (parsing)** phase checks whether the program follows grammatical rules. However, a program can be syntactically correct and still be **wrong in meaning**. Semantic analysis is responsible for catching these kinds of errors.

**For example,** the statement

```
int x;
x = "hello";
```

is syntactically correct, but it is **semantically incorrect** because a string value is being assigned to an integer variable. Detecting such mistakes is the job of semantic analysis.

During semantic analysis, the compiler uses information stored in the **symbol table**, which contains details about variables, functions, and their properties. The compiler checks whether variables are **declared before use**, whether operations are applied to **compatible data types**, and whether function calls match their **definitions** in terms of number and type of arguments.

Semantic analysis also ensures **type consistency** in expressions. For example, it checks whether operands of an operator are of valid types and whether implicit or explicit type conversions are allowed. It verifies rules such as “you cannot add a number to a boolean” or “a function must return a value of the declared return type.”

Another important task of semantic analysis is **scope checking**. The compiler ensures that variables are accessed only within their valid scope and that there are no illegal redeclarations or conflicts between variable names.

Semantic analysis also prepares the program for later phases by **annotating the parse tree** with type information and other attributes. This annotated structure is often guided by **Syntax Directed Definitions**, which specify how semantic information should be computed and attached to grammar symbols.

In many compilers, semantic analysis is also responsible for producing an **intermediate representation** or for helping generate it, using the verified semantic information to ensure correctness.

### Snytax Directed Definition

**Syntax Directed Definition (SDD)** is a **formal method used in compiler construction** to attach **meaning (semantics)** to the grammatical structure of a programming language.

In simple words, a Syntax Directed Definition tells the compiler **what actions to perform** and **what values to compute** when certain grammar rules are recognized during parsing.

When a compiler checks syntax using a grammar, it only verifies whether the program is written correctly. But a compiler must also understand **what the program means**. This meaning is handled during **semantic analysis**, and Syntax Directed Definitions are used for this purpose.

An SDD consists of two main parts. The first part is a **context-free grammar**, which defines the syntax of the language. The second part is a set of **semantic rules** attached to each grammar production. These rules specify how to compute **attributes** associated with grammar symbols.

Attributes are values associated with grammar symbols, such as data types, values of expressions, or memory locations. When a production rule is used during parsing, its semantic rules are executed to compute these attribute values.

For example, consider a simple grammar for expressions:

```
E → E + T
E → T
T → num
```

In a Syntax Directed Definition, we can attach semantic rules like:

```
E.val = E1.val + T.val
T.val = num.lexval
```

Here, `val` is an attribute that stores the value of the expression. When the parser reduces `E → E + T`, it computes the value of `E` by adding the values of `E` and `T`.

SDDs are important because they allow the compiler to perform tasks such as **type checking**, **expression evaluation**, **symbol table construction**, and **intermediate code generation** while parsing the program.

A Syntax Directed Definition describes **what should be computed**, but not **when** it should be computed. The actual execution order of these semantic rules is handled by **Syntax Directed Translation**, which implements the SDD during parsing.

#### Two Types of Attributes in SDD 

When a compiler uses a **Syntax Directed Definition (SDD)**, it attaches **attributes** to grammar symbols.
You can think of an attribute as **extra information** stored at a node of the parse tree, such as:

* the value of an expression
* the data type of a variable
* the memory location of a variable

Now, there are **two ways this information can move inside the parse tree**, and that is why we have **two types of attributes**.


1) **synthesized attribute** is an attribute whose value is **calculated using information from the children of a node** in the parse tree. This means the information is built **from bottom to top**.

For example, imagine the expression:

```
3 + 4
```

The numbers `3` and `4` are **leaf nodes** in the parse tree. Each leaf already knows its value.
When the compiler reaches the `+` node, it looks at its children (`3` and `4`), takes their values, and computes a new value `7`.

This value `7` is a **synthesized attribute** because:

* it is calculated **after** the children are processed
* it moves **upward** from children to parent

So, synthesized attributes answer this question:

> “What value can I compute from my children?”

That is why we say **synthesized attributes flow upward** in the parse tree.


2) **inherited attributes**.

An **inherited attribute** is an attribute whose value is **passed from a parent or a sibling to a child**. This means the information flows **from top to bottom or sideways** in the parse tree.

Inherited attributes are used when a child node needs **context information** that it cannot compute by itself.

For example, consider this code:

```
int x, y;
```

Here, the type `int` applies to **both `x` and `y`**.
The variable `x` does not know its type on its own. The type information comes from the declaration part (`int`).

So the compiler:

* determines the type `int` at a higher-level node
* **passes this type information down** to `x` and `y`

This passing of information from parent to child is called an **inherited attribute**.

Inherited attributes answer this question:

> “What information do I need from my parent or sibling to do my work?”

That is why we say **inherited attributes flow downward or sideways**.


#### S and L attributes 
In **Syntax Directed Definitions (SDD)**, attributes are classified not only as synthesized and inherited, but also as **S-attributes** and **L-attributes**. These terms describe **how attributes are evaluated during parsing**, and they are very important for understanding **when and how semantic rules can be implemented**.


##### S-attributes

An **S-attribute grammar** is a Syntax Directed Definition in which **all attributes are synthesized attributes**. This means that every attribute value is computed **only from the attributes of the children** in the parse tree.

Because synthesized attributes flow **from bottom to top**, they are very easy to evaluate. The compiler can compute them naturally during **bottom-up parsing** (such as LR parsing), without needing any special handling.

For example, when evaluating an arithmetic expression like `3 + 4`, the values of `3` and `4` are known first. Then, the value of `3 + 4` is computed at the parent node. No information needs to be passed downward.

S-attributed definitions are simple, clean, and widely used for tasks like:

* expression evaluation
* building syntax trees
* generating intermediate code

In short, **S-attributes mean: only synthesized attributes, no inherited attributes**.



##### L-attributes

An **L-attribute grammar** allows the use of **both synthesized and inherited attributes**, but with a restriction that makes evaluation possible during **left-to-right traversal** of the parse tree.

In an L-attributed grammar, an inherited attribute of a symbol can depend only on:

* attributes of the **parent**, and
* attributes of the **symbols to the left** of that symbol in the production

It **cannot depend on attributes of symbols to the right**, because those have not been processed yet during left-to-right parsing.

For example, in a declaration like:

```
int x, y;
```

The type `int` is determined first and then passed to `x` and `y` from left to right. This is allowed in an L-attributed grammar because the information flows from the parent and left siblings.

L-attributed grammars are powerful enough to handle:

* type declarations
* scope rules
* many real programming language constructs

They can be implemented using **top-down parsing (like LL parsing)** with syntax-directed translation.




---


<br>  

## Three Address Code (TAC)  

Three Address Code, usually called **TAC**, is an **intermediate representation** used by a compiler to represent a program in a simple, low-level, and structured form before generating machine code. It is called *three address* because **each instruction contains at most three addresses**: two for operands and one for the result.

When a compiler translates a high-level language like C or Java, it does not directly convert it into machine code. Instead, it first converts the program into an intermediate form that is easier to analyze and optimize. Three Address Code plays this role. It acts as a bridge between the source program and the final machine code.

In Three Address Code, complex expressions are **broken down into very small steps**. Each step performs only **one operation**. This makes the control flow and data flow of the program very clear to the compiler.

**For example, consider the expression:**

```
a = b + c * d
```

In a high-level language, this is written in one line, but it contains multiple operations. In Three Address Code, it is written as:

```
t1 = c * d
t2 = b + t1
a  = t2
```

Here, `t1` and `t2` are **temporary variables** created by the compiler. Each line performs only one operation and uses at most three addresses.

The name “three address” comes from the general format:

```
result = operand1 op operand2
```

Sometimes fewer than three addresses are used, for example in assignments or jumps, but never more than three.

Three Address Code is very important because it is **machine-independent**. It does not depend on the architecture of the computer. This allows the same compiler front end to work for different machines, and only the back end needs to change.

Another important role of Three Address Code is **optimization**. Since TAC is simple and explicit, the compiler can easily apply optimizations such as common subexpression elimination, dead code elimination, constant folding, and loop optimizations. Structures like **DAGs are often built using Three Address Code** inside basic blocks to remove redundant calculations.

Three Address Code also makes **control flow** very clear. Conditional statements, loops, and jumps are represented using labels and goto-like instructions. For example:

```
if a < b goto L1
goto L2
L1: c = a
L2: c = b
```

This form is easy for the compiler to analyze and later convert into assembly instructions.

### Common Types of TAC Statements

|Type|Example|
|----|-------|
|Assignment|x = y op z or x = op y (unary minus)|
|Copy|x = y|
|Unconditional Jump|goto L (move to label L)|
|Conditional Jump|if x < y goto L|
|Procedural Call|"param x, call p, return y"|
|Indexed Assignment|x = y[i] or x[i] = y|

### There are different ways to represent Three Address Code internally. 

#### Quadruples

Quadruples are a **way to represent Three Address Code** in a structured table-like form. Each TAC instruction is stored using **four fields**: the operator, the first operand, the second operand, and the result.

Let’s take this expression again:

```
a = b + c * d
```

The Three Address Code is:

```
t1 = c * d
t2 = b + t1
a  = t2
```

In **quadruple form**, it looks like this:

| op | arg1 | arg2 | result |
| -- | ---- | ---- | ------ |
| *  | c    | d    | t1     |
| +  | b    | t1   | t2     |
| =  | t2   | —    | a      |

Here, each row represents **one TAC instruction**. The advantage of quadruples is that the **result is explicitly stored**, which makes it very easy for the compiler to modify or reorder instructions during optimization.


#### Triples

Triples are another way to represent Three Address Code, but here **temporary variables are not stored explicitly**. Instead, the **position (index) of an instruction is used as its result**.

Using the same example:

```
t1 = c * d
t2 = b + t1
a  = t2
```

The **triple representation** becomes:

| index | op | arg1 | arg2 |
| ----- | -- | ---- | ---- |
| 0     | *  | c    | d    |
| 1     | +  | b    | (0)  |
| 2     | =  | (1)  | a    |

Here, `(0)` means the result of instruction 0, and `(1)` means the result of instruction 1. This saves space because we do not need temporary variable names like `t1` and `t2`.

However, triples have a drawback. If instructions are rearranged during optimization, **all index references must be updated**, which is inconvenient.


#### Indirect Triples

Indirect triples are introduced to **solve the reordering problem of triples**.

In indirect triples:

* One table stores the actual triples
* Another table stores **pointers to those triples**

When the compiler wants to reorder instructions, it **changes the pointer table**, not the triples themselves. This makes optimization easier while still avoiding explicit temporary variables.

So, indirect triples combine:

* The **compactness of triples**
* The **flexibility of quadruples**




<br>  


## Directed Acyclic Graph 
A **DAG (Directed Acyclic Graph)** in compiler construction is a way for the compiler to **represent and understand expressions more intelligently** so that it can **optimize the program** before generating machine code.

To understand DAG easily, first remember that when a program is written, the compiler breaks it into smaller parts called **basic blocks**. A basic block is a straight-line piece of code with no jumps inside it. DAG is built **only for one basic block at a time**.

In a DAG, every **node represents a value** that is computed in the program. The **leaf nodes** represent variables or constants such as `a`, `b`, or `5`. The **internal nodes** represent operations such as addition, multiplication, subtraction, etc. The edges in the graph show how values are combined to produce new values. The graph is **directed**, meaning it has a clear flow from operands to results, and it is **acyclic**, meaning no loops are allowed.

1) The main idea of using a DAG is that **if the same expression appears more than once, the compiler should not compute it again**. Instead of creating two separate nodes for the same expression, the DAG creates **one node and connects all uses to it**. This helps the compiler detect **common subexpressions** automatically.

**For example** if a program contains the statements
`t1 = a + b`
`t2 = a + b`
the compiler realizes that `a + b` is exactly the same computation in both cases. In the DAG, there will be **only one node for `a + b`**, and both `t1` and `t2` will point to that same node. This means the expression is evaluated only once, which saves time and resources.

2) Another important use of DAG is **dead code elimination**. If a value is computed but never used later in the basic block, the DAG will show that no variable points to that node. The compiler can safely remove that computation because it has no effect on the final result.

3) DAG also helps the compiler during **code generation**. Since the DAG shows which values are reused, the compiler can decide **which values to keep in registers** and which ones can be recomputed or stored in memory. This results in faster and more efficient machine code.

It is important to understand that a DAG is **not the same as a parse tree**. A parse tree shows the grammatical structure of the source code and may repeat the same expression many times. A **DAG,** on the other hand, is built after parsing and is focused on **optimization**, not syntax. That is why a DAG can share nodes between expressions, while a parse tree cannot.

**Limitations**  
- They are used **only inside basic blocks**, so they cannot directly optimize across loops, function calls, or branches.  
- For larger program structures, other compiler techniques such as control flow graphs are needed.


