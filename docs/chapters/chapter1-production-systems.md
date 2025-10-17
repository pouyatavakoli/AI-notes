# Chapter 1 – PRODUCTION SYSTEMS AND AI

## Overview

This foundational chapter introduces the **production system** as the fundamental architectural model for artificial intelligence systems. The core thesis is that most AI systems can be understood through a clean separation between **data**, **operations**, and **control**. The chapter systematically explores:

- The three core components of production systems (global database, production rules, control system)
- Different control strategies (irrevocable, backtracking, graph-search)
- Problem representation techniques
- Specialized production systems (commutative and decomposable)
- Real-world applications across various domains
- Historical context and bibliographical references

The production system model provides a unified framework for understanding how knowledge is organized and utilized in AI systems, distinguishing between declarative, procedural, and control knowledge.

## Core Concepts

### 1.1 Production Systems Fundamentals

**Global Database:**
- The central data structure representing the current state of the problem-solving process
- Can range from simple (3×3 matrix for 8-puzzle) to complex (relational, indexed file structures)
- Contains the "declarative knowledge" - facts about the current problem state
- Should not be confused with traditional database management systems
- Various data structures can be used: symbol strings, vectors, sets, arrays, trees, lists

**Production Rules:**
- Condition-action pairs that operate on the global database
- Each rule has a **precondition** that must be satisfied by the database for the rule to be applicable
- Rules change the database when applied
- **Key features:**
  - All rules can access the entire global database
  - Rules don't "call" other rules directly
  - Communication between rules occurs only through the global database
  - Highly modular - changes can be made relatively independently

**Control System:**
- Selects which applicable rule to apply at each step
- Determines when computation should cease (when termination condition is satisfied)
- Responsible for keeping track of rules applied to compose the solution sequence
- Operates as a **search process** when perfect knowledge isn't available

### 1.2 Problem Representation

**The Representation Problem:**
- Transforming a problem statement into the three production system components
- Multiple possible representations for any given problem
- Selection of good representation is crucial for efficient problem solving

**Key Elements for Problem Solving:**
- **Problem States:** Different configurations in the problem space
- **Moves:** Operations that transform one state to another
- **Goal:** The target state or condition to be achieved

**State Space Considerations:**
- Size of problem space greatly affects solution difficulty
- Some representations can collapse large spaces into manageable ones
- Reformulation can dramatically reduce search complexity

### 1.3 Control Strategy Taxonomy

**Irrevocable Control:**
- Selected rules are applied permanently without reconsideration
- Appropriate when local knowledge reliably leads to global solution
- Example: Hill-climbing in function optimization

**Tentative Control:**
- Rules are applied provisionally with ability to reconsider
- **Two subtypes:**
  - **Backtracking:** Establish backtracking points; revert to previous point when difficulties arise
  - **Graph-Search:** Maintain multiple paths simultaneously using graph structures

### 1.4 Search Direction

**Forward Production Systems:**
- Work from initial state toward goal state
- Use F-rules applied to state descriptions
- Efficient when few initial states, many goal states

**Backward Production Systems:**
- Work from goal state toward initial state
- Use B-rules applied to goal descriptions
- Efficient when few goal states, many initial states

**Bidirectional Systems:**
- Search from both directions simultaneously
- Require matching condition between state and goal descriptions
- Control must decide whether to apply F-rules or B-rules at each step

### 1.5 Specialized Production Systems

**Commutative Production Systems:**
- **Properties:**
  1. Rules applicable to database D remain applicable to any database produced by applying applicable rules to D
  2. Goal condition satisfied by D remains satisfied after applying any applicable rule
  3. Database resulting from applying any sequence of applicable rules is invariant under sequence permutations
- Eliminates redundant path exploration
- Enables irrevocable control even in search contexts

**Decomposable Production Systems:**
- Global database can be split into independent components
- Components can be processed separately (possibly in parallel)
- Use AND/OR graphs for representation:
  - **AND nodes:** All components must be processed (circular arc connection)
  - **OR nodes:** Only one path needed for solution
- Terminal nodes satisfy termination condition

## Key Mechanisms

### 2.1 Basic Production System Algorithm

**Procedure PRODUCTION:**
```
1 DATA ← initial database
2 until DATA satisfies the termination condition, do:
3 begin
4   select some rule, R, in the set of rules that can be applied to DATA
5   DATA ← result of applying R to DATA
6 end
```

This nondeterministic algorithm requires specification of rule selection in step 4.

### 2.2 Hill-Climbing Control

**Mechanism:**
- Use real-valued evaluation function on global databases
- Function should attain highest value for goal states
- Select applicable rule that produces largest increase in function value
- If no increase possible, select rule that doesn't decrease value

**Limitations:**
- Local maxima: Process gets stuck at non-optimal points
- Plateaus: No improvement possible from current state
- Ridges: Sequence of local maxima preventing progress

### 2.3 Backtracking Control

**Implementation Rules:**
- Backup occurs when:
  - State description repeats on current path
  - Depth bound exceeded (fixed number of rules applied without reaching goal)
  - No applicable rules remain
- Uses arbitrary or informed rule selection schemes
- More efficient with good rule-selection knowledge

### 2.4 Graph-Search Control

**Search Trees:**
- Root node: Initial state description
- Arcs: Rule applications
- Descendant nodes: States reachable in one move
- Grows until termination condition satisfied
- Intelligent control focuses tree growth toward goal

### 2.5 Decomposable System Procedure

**Procedure SPLIT:**
```
1 DATA ← initial database
2 {Di} ← decomposition of DATA
3 until all {Di} satisfy termination condition, do:
4 begin
5   select D* from among {Di} not satisfying termination condition
6   remove D* from {Di}
7   select rule R applicable to D*
8   D ← result of applying R to D*
9   {di} ← decomposition of D
10  append {di} to {Di}
11 end
```

**Component Processing Strategies:**
- Fixed order: Process components in predetermined sequence
- Dynamic reordering: Reorder components during processing
- AND/OR graph representation tracks dependencies

## Examples

### 3.1 The 8-Puzzle

**Problem Specification:**
- 8 numbered tiles in 3×3 frame with one empty cell
- Move adjacent tiles into empty space (equivalent to moving blank)

**Production System Setup:**
- **Global Database:** 3×3 array representing tile configuration
- **Rules:** Move blank left/up/right/down (with preconditions for boundary cases)
- **Termination Condition:** Specific goal configuration achieved

**Control Strategy Examples:**
- **Hill-climbing:** Use negative of tiles out of place as evaluation function
- **Backtracking:** Try moves in fixed order (left, up, right, down) with depth bound
- **Graph-search:** Build complete search tree of possible moves

### 3.2 Traveling Salesman Problem

**Problem:** Visit 5 cities with minimal total distance, starting and ending at A

**Production System Setup:**
- **Global Database:** List of cities visited so far (initial: (A))
- **Rules:** Go to city X next (if legal - no repeats except final return to A)
- **Termination Condition:** List begins/ends with A, contains all other cities once

**Search Tree Characteristics:**
- Edges labeled with distance increments
- Multiple paths to same intermediate state
- Requires finding minimal cost path

### 3.3 Syntax Analysis Problem

**Problem:** Determine if symbol string is valid sentence in given grammar

**Production System Setup:**
- **Global Database:** String of terminal and non-terminal symbols
- **Rules:** Grammar rewrite rules (replace left-hand side with right-hand side)
- **Termination Condition:** Database contains single symbol S

**Grammar Example:**
- Terminal symbols: of, approves, new, president, company, sale, the
- Non-terminal symbols: S, NP, VP, PP, P, V, DNP, DET, A, N
- Rewrite rules: DNP VP → S, V DNP → VP, etc.

### 3.4 Chemical Structure Generation (DENDRAL)

**Problem:** Propose plausible chemical structures given formula

**Production System Setup:**
- **Global Database:** Partially structured compound representation
- **Rules:** Structure-proposing rules converting unstructured formulas to partial structures
- **Termination Condition:** No unstructured formulas remain

**Example: C₅H₁₂**
- Initial database: unstructured formula C₅H₁₂
- Rules propose partial structures with structured and unstructured components
- AND/OR tree represents candidate structures
- Solution trees correspond to complete structures (e.g., pentane)

### 3.5 Symbolic Integration (SAINT)

**Problem:** Find indefinite integral of given expression

**Production System Setup:**
- **Global Database:** Integral expression to be solved
- **Rules:** Integration techniques (by parts, substitution, decomposition, etc.)
- **Termination Condition:** Expression matches basic integral form in table

**Rule Types:**
- Algebraic substitutions
- Trigonometric substitutions
- Numerator/denominator division
- Completing the square
- Sum decomposition

**AND/OR Tree Features:**
- Nodes represent expressions to integrate
- Terminal nodes match integral table forms
- Solution tree provides integration path
- Final answer computed from solution tree

## Study Notes

### 4.1 Critical Distinctions

**Production Systems vs. Conventional Systems:**
- No hierarchical program organization
- Global database accessibility
- Rule modularity enables evolutionary development
- Changes can be made relatively independently

**Knowledge Organization:**
- **Declarative Knowledge:** Facts in global database
- **Procedural Knowledge:** Operations in production rules
- **Control Knowledge:** Strategy in control system

**Problem vs. System Types:**
- Distinctions drawn between system types, not problem types
- Same problem can be solved by different system types
- Choice depends on problem characteristics and available knowledge

### 4.2 Efficiency Considerations

**Representation Impact:**
- Good representations have small state spaces
- May recognize symmetries or useful rule sequences
- Example: 8-puzzle "move blank" vs. "move tile" representations

**Control Strategy Selection:**
- Irrevocable: When wrong moves don't foreclose solutions
- Backtracking: When wrong moves cause dead ends
- Graph-search: When multiple paths need simultaneous exploration

**Commutative Systems:**
- Avoid redundant path exploration
- Only initially applicable rules can be arbitrarily reordered
- Newly applicable rules after rule application cannot necessarily be reordered

### 4.3 Historical Context

**Theoretical Foundations:**
- Post (1943): String replacement rules
- Markov algorithms: Ordered replacement rules
- Newell & Simon: Human problem-solving modeling

**Evolution of Terminology:**
- Production systems → Rule-based systems → Blackboard systems → Pattern-directed inference systems

**Key Systems:**
- DENDRAL: Chemical structure elucidation
- SAINT/SIN: Symbolic integration
- UNDERSTAND: Natural language to problem representation

## Exercises

### 5.1 Missionaries and Cannibals Problem
**Task:** Specify production system components
- **Global Database:** State representation (missionaries/cannibals on each side, boat position)
- **Rules:** Legal boat moves ensuring cannibals never outnumber missionaries
- **Termination Condition:** All on opposite side
- **Hill-climbing function:** Maximize people on destination side
- Compare irrevocable vs. backtracking control using this function

### 5.2 Water-Jug Problem
**Task:** Represent 5-liter/2-liter jug problem
- **Global Database:** Current water amounts in both jugs
- **Rules:** Pour between jugs, fill, empty operations
- **Termination Condition:** Precisely 1 liter in 2-liter jug

### 5.3 Sentence Generation
**Task:** Use grammar rules to generate sentences
- **Global Database:** Evolving symbol string
- **Rules:** Grammar rewrite rules in generative direction
- **Termination Condition:** Complete sentence with only terminal symbols
- Generate five grammatical sentences

### 5.4 Ancestry Verification
**Task:** Compare search directions for ancestry proof
- **Analysis:** Consider branching factors in family trees
- Typically easier to verify ancestry by working from descendant to ancestor (fewer ancestors than descendants)

### 5.5 Commutative System Proof
**Task:** Show rule set invariance under invertible rules
- If rule R has inverse R⁻¹, then applicable rules to D and R(D) are identical
- Proof involves showing applicability conditions preserved

### 5.6 Integer Set Production System
**Task:** Prove commutativity of integer set system
- Rules: Add product of any two elements to set
- Show all three commutative properties hold

### 5.7 Decimal to Binary Conversion
**Task:** Design production system for base conversion
- **Global Database:** Number in decimal and/or binary representation
- **Rules:** Division by 2, remainder handling, digit placement
- Demonstrate conversion of 141

### 5.8 Backtracking Discussion
**Task:** Critically analyze backtracking with multiple paths
- Discuss when backtracking avoids vs. duplicates path exploration
- Consider graph vs. tree structures

### 5.9 Backtracking in SPLIT Procedure
**Task:** Analyze step 5 as backtracking point
- Discuss implications for component selection
- Compare SPLIT with backtracking vs. PRODUCTION with backtracking
- Consider component processing order effects