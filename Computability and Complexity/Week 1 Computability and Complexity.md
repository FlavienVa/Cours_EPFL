
# Course 1 

## Grades 
3 test that can be retake in January 

## Topics of the year
- Turing machines, halting problem, decidability
- Finite  
- Circuits 
- Complexity classes : P, NP, PSPACE
- Grammars
- Reduction
- Diagonalization 
- Other stuff :)

## Turing machines 
One of the first model of "computer" 

For that we need data, which is the input and/or output of the *computer* 

<u>Definition</u> : 
- Alphabet : $\Sigma$
- Word : w,x,y
- Words (example) : a, b, ab, aaa
- $\Sigma^*$ = **set of all words** 
- $a^n$ is a word with n a's 

<u>Definition (more)</u> : 
- Language $L$, $L \subseteq \Sigma^*$
	Example : 
	$L = \{\epsilon, a, b, aa, ab, ba, bb\}$ =$\{w *|w| <= 2\}$

### Turing machine begin 
For our machine we need to define some constant to work with them. 

<u>Definition of the symbols</u>
-> $(Q, k, \Sigma, \Gamma, \delta, q_0, q_a, q_r)$
 $Q$ : set of states 
 $k>0$ : number of work tapes 
 $\Sigma$ : input/output alphabet
 $\Gamma$ : working alphabet >= $\Sigma$ U {w}
 $q_0$ : starting state
 $q_a$ : acceptance state
 $q_r$ : rejecting state 

<u>The transition function</u>
$\delta : Q \times \Gamma^{k+1} \rightarrow Q \times \Gamma^{k+1} \times \{L, R, N\}^{k+2}$

The + 1 is because we need one tape for the input at least. It can be + 2 if we consider that we always need an input AND an output. 

<u>Variants of the Turing machine</u> 
- 1- tape (like every thing in one tape input/output/working)
- bidirectional tapes 
- Read-only input tape 
- Write-only output tape 
- etc...

#### <u>Examples</u>
VERY simple 
1-tape Turing machine, $\Sigma$ = {0,1} adds a 0 at the end of its input 

| -   | 0   | 0   | 1   | 0   | -   | -   |
| --- | --- | --- | --- | --- | --- | --- |
to

| -   | 0   | 0   | 1   | 0   | 0   | -   |
| --- | --- | --- | --- | --- | --- | --- |


So what is the transition function for that ?? 

<table>
    <thead>
        <tr>
            <th>State</th>
            <th>Symbol Read</th>
            <th>New State</th>
            <th>Symbol Write</th>
            <th>Movement</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>q_0</td>
            <td>0</td>
            <td>q_0</td>
            <td>0</td>
            <td>R</td>
        </tr>
        <tr>
            <td>q_0</td>
            <td>1</td>
            <td>q_0</td>
            <td>1</td>
            <td>R</td>
        </tr>
        <!-- Add more rows as needed -->
    </tbody>
</table> 

Or translated to a graph (more easy to understand)

```mehrmaid
graph TD 
X1 & X2 -> X3 -> X4
```
```mehrmaid
stateDiagram-v2
    [*] --> q0
    q0 --> q4 : ⊔ / L
    q4 --> qa : ⊔ / N
    qa --> qa : ⊔ / N
    state qa <<accept>>
```
```mehrmaid 
graph LR
X1 -->|"$\  ⊔ \rightarrow N$"| X2 
X2 -->|"$\ ⊔ \rightarrow N$"| X3  
X1(("$q_0$"))
X2(("$q_1$"))
X3(("$q_a$"))
```
He has shown way more complex examples that are way harder to understand and/or find on our own. *See slides*


#### Mathematical definition Turing machine

##### Set
<u>Definition</u>
Reclusively enumerable  / Semi-decidable 
$\exists M pourToutw apartenant \Sigma^* w apartient A iff M accepts an input w$
A language A is said to be recursively enumerable or re
recognizable if there exists a Turing machine M such that the
computation of M on the input w ends in the accepting configu-
if and only if w ∈ A.

<u>Definition</u> Recursive / decidable

##### Function
<u>Definition</u> Computable function $f : \ \Sigma^* \rightarrow \Sigma^*$


#### Representation/Encoding of $N X N$
Is finite because trivial merci 

And then we can use a pretty code to encode it as $T(x+y)+y$
$T(n) = n*(n+1)/2$

#### Representation/Encoding of $\{0,1\}$
Use ***BINARY REPRESENTATION***

Okey no his one is a bit weird and we need to do more operation to just get to binary representation, because 000 is not the same as 00, because there are <u>words</u> and not just numbers.

| $\Sigma$   | $N$ |
| ---------- | --- |
| $\epsilon$ | 0   |
| 0          | 1   |
| 1          | 2   |
| 00         | 3   |
| 01         | 4   |
| 10         | 5   |
| 11         | 6   |


