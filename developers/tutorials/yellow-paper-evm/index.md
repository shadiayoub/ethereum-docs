---
title: Understanding the Yellow Paper's EVM Specifications
author: qbzzt
tags:
  - evm
skill: intermediate
lang: en
published: 2022-05-15T00:00:00.000Z
description: >-
  Understanding the part of the Yellow Paper, the formal specifications for
  Ethereum, that explains the Ethereum virtual machine (EVM).
---

# Understanding the Yellow Paper's EVM Specifications

[The Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf) is the formal specification for Ethereum. Except where amended by [the EIP process](../../../eips/), it contains the exact description of how everything works. It is written as a mathematical paper, which includes terminology programmers may not find familiar. In this paper you learn how to read it, and by extension other related mathematical papers.

### Which Yellow Paper? <a href="#which-yellow-paper" id="which-yellow-paper"></a>

Like almost everything else in Ethereum, the Yellow Paper evolves over time. To be able to refer to a specific version, I uploaded [the current version at writing](../../../public/content/developers/tutorials/yellow-paper-evm/yellow-paper-berlin.pdf). The section, page, and equation numbers I use will refer to that version. It is a good idea to have it open in a different window while reading this document.

#### Why the EVM? <a href="#why-the-evm" id="why-the-evm"></a>

The original yellow paper was written right at the start of Ethereum's development. It describes the original proof-of-work based consensus mechanism that was originally used to secure the network. However, Ethereum switched off proof-of-work and started using proof-of-stake based consensus in September 2022. This tutorial will focus on the parts of the yellow paper defining the Ethereum Virtual Machine. The EVM was unchanged by the transition to proof-of-stake (except for the return value of the DIFFICULTY opcode).

### 9 Execution model <a href="#id-9-execution-model" id="id-9-execution-model"></a>

This section (p. 12-14) includes most of the definition of the EVM.

The term _system state_ includes everything you need to know about the system to run it. In a typical computer, this means the memory, content of registers, etc.

A [Turing machine](https://en.wikipedia.org/wiki/Turing\_machine) is a computational model. Essentially, it is a simplified version of a computer, which is proved to have the same ability to run computations that a normal computer can (everything that a computer can calculate a Turing machine can calculate and vice versa). This model makes it easier to prove various theorems about what is and what isn't computable.

The term [Turing-complete](https://en.wikipedia.org/wiki/Turing\_completeness) means a computer that can run the same calculations as a Turing machine. Turing machines can get into infinite loops, and the EVM cannot because it would run out of gas, so it's only quasi-Turing-complete.

### 9.1 Basics <a href="#id-91-basics" id="id-91-basics"></a>

This section gives the basics of the EVM and how it compares with other computational models.

A [stack machine](https://en.wikipedia.org/wiki/Stack\_machine) is a computer that stores intermediate data not in registers, but in a [**stack**](https://en.wikipedia.org/wiki/Stack\_\(abstract\_data\_type\)). This is the preferred architecture for virtual machines because it is easy to implement meaning that bugs, and security vulnerabilities, are a lot less likely. The memory in the stack is divided into 256-bit words. This was chosen because it is convenient for Ethereum's core cryptographic operations such as Keccak-256 hashing and elliptic curve computations. The maximum size of the stack is 1024 items (1024 x 256 bits). When opcodes are executed they are usually getting their parameters from the stack. There are opcodes specifically for reorganizing elements in the stack such as `POP` (removes item from top of stack), `DUP_N` (duplicated N'th item in stack), etc.

The EVM also has a volatile space called **memory** which is used to store data during execution. This memory is organized into 32-byte words. All memory locations are initialized to zero. If you execute this [Yul](https://docs.soliditylang.org/en/latest/yul.html) code to add a word to memory, it will fill 32 bytes of memory by padding the empty space in the word with zeros, i.e. it creates one word - with zeros in locations 0-29, 0x60 to 30, and 0xA7 to 31.

```yul
mstore(0, 0x60A7)
```

`mstore` is one of three opcodes the EVM provides for interacting with memory - it loads a word into memory. The other two are `mstore8` which loads a single byte into memory, and `mload` which moves a word from memory to stack.

The EVM also has a separate non-volatile **storage** model that is maintained as part of the system state - this memory is organized into word arrays (as opposed to word-addressable byte arrays in the stack). This storage is where contracts keep persistent data - a contract can only interact with its own storage. Storage is organized in key-value mappings.

Although it is not mentioned in this section of the Yellow Paper, it is also useful to know there is a fourth type of memory. **Calldata** is byte-addressable read-only memory used to store the value passed with the `data` parameter of a transaction. The EVM has specific opcodes for managing `calldata`. `calldatasize` returns the size of the data. `calldataload` loads the data into the stack. `calldatacopy` copies the data into memory.

The standard [Von Neumann architecture](https://en.wikipedia.org/wiki/Von\_Neumann\_architecture) stores code and data in the same memory. The EVM does not follow this standard for security reasons - sharing volatile memory makes it possible to change program code. Instead, code is saved to storage.

There are only two cases in which code is executed from memory:

* When a contract creates another contract (using [`CREATE`](https://www.evm.codes/#f0) or [`CREATE2`](https://www.evm.codes/#f5)), the code for the contract constructor comes from memory.
* During the creation of _any_ contract, the constructor code runs and then returns with the code of the actual contract, also from memory.

The term exceptional execution means an exception that causes the execution of the current contract to halt.

### 9.2 Fees overview <a href="#id-92-fees-overview" id="id-92-fees-overview"></a>

This section explains how the gas fees are calculated. There are three costs:

#### Opcode cost <a href="#opcode-cost" id="opcode-cost"></a>

The inherent cost of the specific opcode. To get this value, find the cost group of the opcode in Appendix H (p. 28, under equation (327)), and find the cost group in equation (324). This gives you a cost function, which in most cases uses parameters from Appendix G (p. 27).

For example, the opcode [`CALLDATACOPY`](https://www.evm.codes/#37) is a member of group _Wcopy_. The opcode cost for that group is _Gverylow+Gcopy×⌈μs\[2]÷32⌉_. Looking at Appendix G, we see that both constants are 3, which gives us _3+3×⌈μs\[2]÷32⌉_.

We still need to decipher the expression _⌈μs\[2]÷32⌉_. The outmost part, _⌈ \<value> ⌉_ is the ceiling function, a function that given a value returns the smallest integer that is still not smaller than the value. For example, _⌈2.5⌉ = ⌈3⌉ = 3_. The inner part is _μs\[2]÷32_. Looking at section 3 (Conventions) on p. 3, _μ_ is the machine state. The machine state is defined in section 9.4.1 on p. 13. According to that section, one of the machine state parameters is _s_ for the stack. Putting it all together, it seems that _μs\[2]_ is location #2 in the stack. Looking at [the opcode](https://www.evm.codes/#37), location #2 in the stack is the size of the data in bytes. Looking at the other opcodes in group Wcopy, [`CODECOPY`](https://www.evm.codes/#39) and [`RETURNDATACOPY`](https://www.evm.codes/#3e), they also have a size of data in the same location. So _⌈μs\[2]÷32⌉_ is the number of 32 byte words required to store the data being copied. Putting everything together, the inherent cost of [`CALLDATACOPY`](https://www.evm.codes/#37) is 3 gas plus 3 per word of data being copied.

#### Running cost <a href="#running-cost" id="running-cost"></a>

The cost of running the code we're calling.

* In the case of [`CREATE`](https://www.evm.codes/#f0) and [`CREATE2`](https://www.evm.codes/#f5), the constructor for the new contract.
* In the case of [`CALL`](https://www.evm.codes/#f1), [`CALLCODE`](https://www.evm.codes/#f2), [`STATICCALL`](https://www.evm.codes/#fa), or [`DELEGATECALL`](https://www.evm.codes/#f4), the contract we call.

#### Expanding memory cost <a href="#expanding-memory-cost" id="expanding-memory-cost"></a>

The cost of expanding memory (if necessary).

In equation 324, this value is written as _Cmem(μi')-Cmem(μi)_. Looking at section 9.4.1 again, we see that _μi_ is the number of words in memory. So _μi_ is the number of words in memory before the opcode and _μi'_ is the number of words in memory after the opcode.

The function _Cmem_ is defined in equation 326: _Cmem(a) = Gmemory × a + ⌊a2 ÷ 512⌋_. _⌊x⌋_ is the floor function, a function that given a value returns the largest integer that is still not larger than the value. For example, _⌊2.5⌋ = ⌊2⌋ = 2._ When _a < √512_, _a2 < 512_, and the result of the floor function is zero. So for the first 22 words (704 bytes), the cost rises linearly with the number of memory words required. Beyond that point _⌊a2 ÷ 512⌋_ is positive. When the memory required is high enough the gas cost is proportional to the square of the amount of memory.

**Note** that these factors only influence the _inherent_ gas cost - it does not take into account the fee market or tips to validators that determine how much an end user is required to pay - this is just the raw cost of running a particular operation on the EVM.

[Read more about gas](../../docs/gas/).

### 9.3 Execution environment <a href="#id-93-execution-env" id="id-93-execution-env"></a>

The execution environment is a tuple, _I_, that includes information that isn't part of the blockchain state or the EVM.

| Parameter | Opcode to access the data                                                                                        | Solidity code to access the data         |
| --------- | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| _Ia_      | [`ADDRESS`](https://www.evm.codes/#30)                                                                           | `address(this)`                          |
| _Io_      | [`ORIGIN`](https://www.evm.codes/#32)                                                                            | `tx.origin`                              |
| _Ip_      | [`GASPRICE`](https://www.evm.codes/#3a)                                                                          | `tx.gasprice`                            |
| _Id_      | [`CALLDATALOAD`](https://www.evm.codes/#35), etc.                                                                | `msg.data`                               |
| _Is_      | [`CALLER`](https://www.evm.codes/#33)                                                                            | `msg.sender`                             |
| _Iv_      | [`CALLVALUE`](https://www.evm.codes/#34)                                                                         | `msg.value`                              |
| _Ib_      | [`CODECOPY`](https://www.evm.codes/#39)                                                                          | `address(this).code`                     |
| _IH_      | Block header fields, such as [`NUMBER`](https://www.evm.codes/#43) and [`DIFFICULTY`](https://www.evm.codes/#44) | `block.number`, `block.difficulty`, etc. |
| _Ie_      | Depth of the call stack for calls between contracts (including contract creation)                                |                                          |
| _Iw_      | Is the EVM allowed to change state, or is it running statically                                                  |                                          |

A few other parameters are necessary to understand the rest of section 9:

| Parameter | Defined in section   | Meaning                                                                                                                                                                                                                  |
| --------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| _σ_       | 2 (p. 2, equation 1) | The state of the blockchain                                                                                                                                                                                              |
| _g_       | 9.3 (p. 13)          | Remaining gas                                                                                                                                                                                                            |
| _A_       | 6.1 (p. 8)           | Accrued substate (changes scheduled for when the transaction ends)                                                                                                                                                       |
| _o_       | 9.3 (p. 13)          | Output - the returned result in the case of internal transaction (when one contract calls another) and calls to view functions (when you are just asking for information, so there is no need to wait for a transaction) |

### 9.4 Execution overview <a href="#id-94-execution-overview" id="id-94-execution-overview"></a>

Now that have all the preliminaries, we can finally start working on how the EVM works.

Equations 137-142 give us the initial conditions for running the EVM:

| Symbol | Initial value | Meaning                                                                                                                                                                                                                                                     |
| ------ | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _μg_   | _g_           | Gas remaining                                                                                                                                                                                                                                               |
| _μpc_  | _0_           | Program counter, the address of the next instruction to execute                                                                                                                                                                                             |
| _μm_   | _(0, 0, ...)_ | Memory, initialized to all zeros                                                                                                                                                                                                                            |
| _μi_   | _0_           | Highest memory location used                                                                                                                                                                                                                                |
| _μs_   | _()_          | The stack, initially empty                                                                                                                                                                                                                                  |
| _μo_   | _∅_           | The output, empty set until and unless we stop either with return data ([`RETURN`](https://www.evm.codes/#f3) or [`REVERT`](https://www.evm.codes/#fd)) or without it ([`STOP`](https://www.evm.codes/#00) or [`SELFDESTRUCT`](https://www.evm.codes/#ff)). |

Equation 143 tells us there are four possible conditions at each point in time during execution, and what to do with them:

1. `Z(σ,μ,A,I)`. Z represents a function that tests whether an operation creates an invalid state transition (see [exceptional halting](index.md#942-exceptional-halting)). If it evaluates to True, the new state is identical to the old one (except gas gets burned) because the changes have not been implemented.
2. If the opcode being executed is [`REVERT`](https://www.evm.codes/#fd), the new state is the same as the old state, some gas is lost.
3. If the sequence of operations is finished, as signified by a [`RETURN`](https://www.evm.codes/#f3)), the state is updated to the new state.
4. If we aren't at one of the end conditions 1-3, continue running.

### 9.4.1 Machine State <a href="#id-941-machine-state" id="id-941-machine-state"></a>

This section explains the machine state in greater detail. It specifies that _w_ is the current opcode. If _μpc_ is less than _||Ib||_, the length of the code, then that byte (_Ib\[μpc]_) is the opcode. Otherwise, the opcode is defined as [`STOP`](https://www.evm.codes/#00).

As this is a [stack machine](https://en.wikipedia.org/wiki/Stack\_machine), we need to keep track of the number of items popped out (_δ_) and pushed in (_α_) by each opcode.

### 9.4.2 Exceptional Halting <a href="#id-942-exceptional-halt" id="id-942-exceptional-halt"></a>

This section defines the _Z_ function, which specifies when we have an abnormal termination. This is a [Boolean](https://en.wikipedia.org/wiki/Boolean\_data\_type) function, so it uses [_∨_ for a logical or](https://en.wikipedia.org/wiki/Logical\_disjunction) and [_∧_ for a logical and](https://en.wikipedia.org/wiki/Logical\_conjunction).

We have an exceptional halt if any of these conditions is true:

* _**μg < C(σ,μ,A,I)**_ As we saw in section 9.2, _C_ is the function that specifies the gas cost. There isn't enough gas left to cover the next opcode.
* _**δw=∅**_ If the number of items popped for an opcode is undefined, then the opcode itself is undefined.
* _**|| μs || < δw**_ Stack underflow, not enough items in the stack for the current opcode.
* _**w = JUMP ∧ μs\[0]∉D(Ib)**_ The opcode is [`JUMP`](https://www.evm.codes/#56) and the address is not a [`JUMPDEST`](https://www.evm.codes/#5b). Jumps are _only_ valid when the destination is a [`JUMPDEST`](https://www.evm.codes/#5b).
* _**w = JUMPI ∧ μs\[1]≠0 ∧ μs\[0] ∉ D(Ib)**_ The opcode is [`JUMPI`](https://www.evm.codes/#57), the condition is true (non zero) so the jump should happen, and the address is not a [`JUMPDEST`](https://www.evm.codes/#5b). Jumps are _only_ valid when the destination is a [`JUMPDEST`](https://www.evm.codes/#5b).
* _**w = RETURNDATACOPY ∧ μs\[1]+μs\[2]>|| μo ||**_ The opcode is [`RETURNDATACOPY`](https://www.evm.codes/#3e). In this opcode stack element _μs\[1]_ is the offset to read from in the return data buffer, and stack element _μs\[2]_ is the length of data. This condition occurs when you try to read beyond the end of the return data buffer. Note that there isn't a similar condition for the calldata or for the code itself. When you try to read beyond the end of those buffers you just get zeros.
*   _**|| μs || - δw + αw > 1024**_

    Stack overflow. If running the opcode will result in a stack of over 1024 items, abort.
*   _**¬Iw ∧ W(w,μ)**_ Are we running statically ([¬ is negation](https://en.wikipedia.org/wiki/Negation) and _Iw_ is true when we are allowed to change the blockchain state)? If so, and we're trying a state changing operation, it can't happen.

    The function _W(w,μ)_ is defined later in equation 150. _W(w,μ)_ is true if one of these conditions is true:

    * _**w ∈ {CREATE, CREATE2, SSTORE, SELFDESTRUCT}**_ These opcodes change the state, either by creating a new contract, storing a value, or destroying the current contract.
    * _**LOG0≤w ∧ w≤LOG4**_ If we are called statically we cannot emit log entries. The log opcodes are all in the range between [`LOG0` (A0)](https://www.evm.codes/#a0) and [`LOG4` (A4)](https://www.evm.codes/#a4). The number after the log opcode specifies how many topics the log entry contains.
    * _**w=CALL ∧ μs\[2]≠0**_ You can call another contract when you're static, but if you do you cannot transfer ETH to it.
* _**w = SSTORE ∧ μg ≤ Gcallstipend**_ You cannot run [`SSTORE`](https://www.evm.codes/#55) unless you have more than Gcallstipend (defined as 2300 in Appendix G) gas.

### 9.4.3 Jump Destination Validity <a href="#id-943-jump-dest-valid" id="id-943-jump-dest-valid"></a>

Here we formally define what are the [`JUMPDEST`](https://www.evm.codes/#5b) opcodes. We cannot just look for byte value 0x5B, because it might be inside a PUSH (and therefore data and not an opcode).

In equation (153) we define a function, _N(i,w)_. The first parameter, _i_, is the opcode's location. The second, _w_, is the opcode itself. If _w∈\[PUSH1, PUSH32]_ that means the opcode is a PUSH (square brackets define a range that includes the endpoints). If that case the next opcode is at _i+2+(w−PUSH1)_. For [`PUSH1`](https://www.evm.codes/#60) we need to advance by two bytes (the PUSH itself and the one byte value), for [`PUSH2`](https://www.evm.codes/#61) we need to advance by three bytes because it's a two byte value, etc. All other EVM opcodes are just one byte long, so in all other cases _N(i,w)=i+1_.

This function is used in equation (152) to define _DJ(c,i)_, which is the [set](https://en.wikipedia.org/wiki/Set\_\(mathematics\)) of all valid jump destinations in code _c_, starting with opcode location _i_. This function is defined recursively. If _i≥||c||_, that means that we're at or after the end of the code. We are not going to find any more jump destinations, so just return the empty set.

In all other cases we look at the rest of the code by going to the next opcode and getting the set starting from it. _c\[i]_ is the current opcode, so _N(i,c\[i])_ is the location of the next opcode. _DJ(c,N(i,c\[i]))_ is therefore the set of valid jump destinations that starts at the next opcode. If the current opcode isn't a `JUMPDEST`, just return that set. If it is `JUMPDEST`, include it in the result set and return that.

### 9.4.4 Normal halting <a href="#id-944-normal-halt" id="id-944-normal-halt"></a>

The halting function _H_, can return three types of values.

* If we aren't in a halt opcode, return _∅_, the empty set. By convention, this value is interpreted as Boolean false.
* If we have a halt opcode that doesn't produce output (either [`STOP`](https://www.evm.codes/#00) or [`SELFDESTRUCT`](https://www.evm.codes/#ff)), return a sequence of size zero bytes as the return value. Note that this is very different from the empty set. This value means that the EVM really did halt, just there's no return data to read.
* If we have a halt opcode that does produce output (either [`RETURN`](https://www.evm.codes/#f3) or [`REVERT`](https://www.evm.codes/#fd)), return the sequence of bytes specified by that opcode. This sequence is taken from memory, the value at the top of the stack (_μs\[0]_) is the first byte, and the value after it (_μs\[1]_) is the length.

### H.2 Instruction set <a href="#h2-instruction-set" id="h2-instruction-set"></a>

Before we go to the final subsection of the EVM, 9.5, let's look at the instructions themselves. They are defined in Appendix H.2 which starts on p. 29. Anything that is not specified as changing with that specific opcode is expected to stay the same. Variables that do change are specified with as \<something>′.

For example, let's look at the [`ADD`](https://www.evm.codes/#01) opcode.

| Value | Mnemonic | δ | α | Description                 |
| ----: | -------- | - | - | --------------------------- |
|  0x01 | ADD      | 2 | 1 | Addition operation.         |
|       |          |   |   | _μ′s\[0] ≡ μs\[0] + μs\[1]_ |

_δ_ is the number of values we pop from the stack. In this case two, because we are adding the top two values.

_α_ is the number of values we push back. In this case one, the sum.

So the new stack top (_μ′s\[0]_) is the sum of the old stack top (_μs\[0]_) and the old value below it (_μs\[1]_).

Instead of going over all the opcodes with an "eyes glaze over list", This article explains only those opcodes that introduce something new.

| Value | Mnemonic  | δ | α | Description                                              |
| ----: | --------- | - | - | -------------------------------------------------------- |
|  0x20 | KECCAK256 | 2 | 1 | Compute Keccak-256 hash.                                 |
|       |           |   |   | _μ′s\[0] ≡ KEC(μm\[μs\[0] . . . (μs\[0] + μs\[1] − 1)])_ |
|       |           |   |   | _μ′i ≡ M(μi,μs\[0],μs\[1])_                              |

This is the first opcode that accesses memory (in this case, read only). However, it might expand beyond the current limits of the memory, so we need to update _μi._ We do this using the _M_ function defined in equation 328 on p. 29.

| Value | Mnemonic | δ | α | Description                       |
| ----: | -------- | - | - | --------------------------------- |
|  0x31 | BALANCE  | 1 | 1 | Get balance of the given account. |
|       |          |   |   | ...                               |

The address whose balance we need to find is _μs\[0] mod 2160_. The top of the stack is the address, but because addresses are only 160 bits, we calculate the value [modulo](https://en.wikipedia.org/wiki/Modulo\_operation) 2160.

If _σ\[μs\[0] mod 2160] ≠ ∅_, it means that there is information about this address. In that case, _σ\[μs\[0] mod 2160]b_ is the balance for that address. If _σ\[μs\[0] mod 2160] = ∅_, it means that this address is uninitialized and the balance is zero. You can see the list of account information fields in section 4.1 on p. 4.

The second equation, _A'a ≡ Aa ∪ {μs\[0] mod 2160}_, is related to the difference in cost between access to warm storage (storage that has recently been accessed and is likely to be cached) and cold storage (storage that hasn't been accessed and is likely to be in slower storage that is more expensive to retrieve). _Aa_ is the list of addresses previously accessed by the transaction, which should therefore be cheaper to access, as defined in section 6.1 on p. 8. You can read more about this subject in [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929).

| Value | Mnemonic | δ  | α  | Description                |
| ----: | -------- | -- | -- | -------------------------- |
|  0x8F | DUP16    | 16 | 17 | Duplicate 16th stack item. |
|       |          |    |    | _μ′s\[0] ≡ μs\[15]_        |

Note that to use any stack item, we need to pop it, which means we also need to pop all the stack items on top of it. In the case of [`DUP<n>`](https://www.evm.codes/#8f) and [`SWAP<n>`](https://www.evm.codes/#9f), this means having to pop and then push up to sixteen values.

### 9.5 The execution cycle <a href="#id-95-exec-cycle" id="id-95-exec-cycle"></a>

Now that we have all the parts, we can finally understand how the execution cycle of the EVM is documented.

Equation (155) says that given the state:

* _σ_ (global blockchain state)
* _μ_ (EVM state)
* _A_ (substate, changes to happen when the transaction ends)
* _I_ (execution environment)

The new state is _(σ', μ', A', I')_.

Equations (156)-(158) define the stack and the change in it due to an opcode (_μs_). Equation (159) is the change in gas (_μg_). Equation (160) is the change in the program counter (_μpc_). Finally, equations (161)-(164) specify that the other parameters stay the same, unless explicitly changed by the opcode.

With this the EVM is fully defined.

### Conclusion <a href="#conclusion" id="conclusion"></a>

Mathematical notation is precise and has allowed the Yellow Paper to specify every detail of Ethereum. However, it does have some drawbacks:

* It can only be understood by humans, which means that [compliance tests](https://github.com/ethereum/tests) must be written manually.
* Programmers understand computer code. They may or may not understand mathematical notation.

Maybe for these reasons, the newer [consensus layer specs](https://github.com/ethereum/consensus-specs/blob/dev/tests/core/pyspec/README.md) are written in Python. There are [execution layer specs in Python](https://ethereum.github.io/execution-specs), but they are not complete. Until and unless the entire Yellow Paper is also translated to Python or a similar language, the Yellow Paper will continue in service, and it is helpful to be able to read it.
