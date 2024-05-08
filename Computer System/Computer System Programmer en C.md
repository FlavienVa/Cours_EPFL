
# Boot Camp

## Semaine 1/3  vidéos 

- Langage très proche de la machine,
- pas d'orienté objet
- moins de vérification par le compiler
- l'allocation de la mémoire doit être faite par nous
- Même chose pour la dé-allocation

### structure
````C
#include des trucs utiles

declaration de fonctions

int main(void){
corps du programme
return un int
}
````
Le return de int nous sert pas pour l'instant juste on return 0

#### conventions
pas trop
SoitDesMajusclues
soit_des_underscores

### variables

Types élémentaires
- int
- char
- double

on peut rajouter des **booléens** en important la bibliothèque (stdbool.h)

PAS de STRING !!
``const`` veut dire **read-only**

#### sémantique de l'opérateur =
si on fait b = a fait une **copie** et pas juste changer la référence ce qui est plus come b.clone(a)


#### opérateurs et expressions
Très très similaire à JAVA

**Erreur** très fréquente x = 3 (qui est une affectation de valeur) et x == 3 (renvoie un booléen)

**Attention** à la virgule **,** exemple :  x =(3,4) x vaut 4  

### structure de contrôle
modifie l'ordre dans le quel le code est exécuté (if-else, while, switch, do-while,etc...)

! mettre les accolades {} pour éviter des erreurs

La portée des variables **est limité au bloc {}** dans le quel elles sont définies à part si on les mets à l'extérieur de notre block ``main`` et dans ce cas ce sont des constantes
Dans le cas d'ambiguity on résout **à la portée la plus proche**

**Ne pas utiliser les breaks et continue**

### functions 
NE JAMAIS FAIRE DE COPIER COLLER

comme en java juste y a pas d'objets donc... pas de méthodes 

Le **prototypage** est la *déclaration* de la fonction sans en définir le corps, son en-tête quoi
	syntaxe : *type nom (liste d'arguments) ;*
	Conseil : nommer les arguments 

En C il n'y a que **EXCLUSIVEMENT** des passages par valeur. 
Pour faire des passages par référence on va devoir utiliser <u>des pointeurs</u> (passage par valeur de pointeur)

**Pointeur**: (on verra plus plus tard)
Je mets des étoiles *  partout devant le nom de l'argument et je mets un & quand je fait un appel : 
![[Pasted image 20240221104423.png]]

### Types de données avancées 

#### Les tableaux 
Les tableaux fixes (les seuls disponibles en C)
	Ils sont FIXES ! 
	On est obligé de connaitre la **taille** au moment de la création du tableau. 
	Ce n'est pas un objet 
	Déclaration``int age[longeur] = {10,20,30,40}`` 
		Si au moins un element du tableau est initialisé, le reste du tableau est initialisé à 0. 
		Une façon simple d'initialiser un gros tableau à 0 est donc de faire : ``int age[BigInt] = {0}``
		Encore mieux ``int tablo[N] = {[2] = 0.5}; // {0,0,0.5,0,0,0,...}`` 
	.
	Pas de vérification de si on est bien dans le tableau (il n'a pas connaissance de sa taille)
	.
	**REGLE ABSOLUE** quand on passe un tableau en argument d'un fonction on **doit** aussi passer sa taille en argument 
	. 
	Pour les tableau de 2 dimensions ça ne change rien, mais on doit passer la taille dans les arguments dans les []

<u>Les enums</u> 
	``enum Cantons {Vaud, Valais, ...}`` 
	On peut carrément les utiliser comme des int, en gros ``enum Vaud + 1 == Valais`` 
   
<u>Les Alias de Types </u>
	Nous permets de définir un nouveau ***nom*** de type
	 commande :  ``typedef``
	 . 
	 Nous sert à bien définir des types et les <u>différencier</u>
	 . 
	 Syntaxe : ``typedef type alias`` 
	 Exemple : ``typedef double Matrice [3][3]`` 

<u>Les Données Structurées </u>
	Nous permets de représenter les données qui ne sont pas de même type (donc on peut pas les mettre dans un tableau)
	.
	Syntaxe : ``struct Nom_du_type{ type1 nom1; type2 nom2; ...}`` 
	Exemple: 
	![[Pasted image 20240221222338.png]]
	. 
	Il est **fortement** recommandé d'initialisé toute la structure 
	.
	Pour accéder à un champs on utilise ``structure.champs`` Ex: ``Personne.taille`` 

<u>Unions</U>
	Union c'est un truc OU un autre 
	Comparé au struct qui est un ET
	Mais ça a de gros inconvénients et du coup on s'en fous


### I/O

<u>Printf</u>
	Pour imprimer des valeurs on doit utiliser des places holder % 
	``printf("texte %nombre", nbr1);`` 
	Ex : ``printf("le premier %d et le deuxième %d, 1, 2);`` 
	Il y a bien sûr beaucoup plus de modificateur de formats (ce qui vient après le %)

<u>Scanf</u>
	Sert a lire les entrées claviers sur le terminal
	Syntaxe : ``int scanf("FORMAT", pointeur1, pointeur2, ...);``
	EX: ``scanf("%d", &nbr1);`` 
	. 
	Lorsque plusieurs valeurs sont lues à la suite, le caractère séparateur de ces valeurs est le **un blanc**
	. 
	Il y a plus de formats disponibles encore que pour printf tel que ``%[A-Z]`` qui ne lis que les lettre majuscules ou ``%lf`` pour les doubles. 

### File I/O

<u>Utilisation des fichiers</u>
	Le type permettant de représenter (le flot associé à) un fichier dans un programme C est FILE défini dans stdio.h
	L'association d'un flot d'entrée-sortie avec le fichier se fait par le biais de la fonction spécifique ``fopen`` 
		Syntaxe : ``FILE* fopen(const char* nom, const char* mode);`` 
	Les modes sont : 
	- "r" en lecture
	- "w" en écriture 
	- "a" en écriture (à la fin)
	Quand on ouvre un fichier il faut directement vérifier si le truc est pas NULL
	. 
	Pour savoir si on est arrivé à la fin d'un fichier on utilise la fonction ``feof(FILE*)`` et pour savoir si il y a une erreur ``ferror(FILE*)`` et pour fermer ``fclose(FILE*)`` 
	!! NE PAS oublier de fermer les fichiers ! 
	.
	.
	***!!!!!!!!!!!!!!!!!!*** Se référer au exercices de la première semaine ***!!!!!!!!!!!!!!!!!***



<u>Fichier binaire</u>
	On utilise le mode "wb" (on a rajouté le petit b)

<u>Complément fichier</u>
	Pour repositionner la tête de lecture on utilise la fonction ``fseek`` ou savoir où on est grâce à ``ftell`` 
	Syntaxe : ``int fseek(FILE* fichier, long offset, int depart)`` 
	. 
	

## Semaine 1/3 lecture 2

### The need of a process abstraction 
We need abstraction from what **really happens** in the hardware and what (we mortal) we can see.  So we don't want to see all that happens in the memory/CPU when we do a simple request (COMPARCH). 
That said it helps us to : 
- hide undesirable property 
- add new capabilities 
- organize information 

Even more, it is important to give the impression of unlimited memory and parallel running of programs. (encore comparch) 
### What is a process ?
An **executable** file contains <u>CPU instructions</u> and the <u>data needed</u> 
	It is a obtained by compiling a program (juste le truc a être exécuté quoi)

"Running a program" is equivalent to "creating a **process**"

#### What constitues a process ?
- A unique identifier : Process ID (PID)
- Memory image (instructions and data)
	- Code and data (static)
	- Stack and heap (dynamic)
- CPU context: registers 
	- Program counter, current operands, stack pointer
- File descriptors : pointers to open files and descriptors   

![[Pasted image 20240221085051.png]]
### How does the OS creates a process? 

We got 4 main steps to this :
- **Loading** : OS loads the static code and data into memory
- **Memory allocation** :  Allocate process memory regions (heap and stack)
- **Initialization** : Initialize tasks related to IO (setting up STDIN, STDOUT, STDERR)
- **Ready** : OS sets the stage for running the process by transferring the CPU control at beginning of the program’s entry point (e.g., main() function)

!! A Process is NOT a Program 
	A program consist of static code and data, the program is **passive**
	A process is an instance of a program, the process is **alive** : mutable data + register + open files....

<u>Sharing of ressources</u>
Multiples programs can run at the same time, so we must determine how to allow the ressources (memory, computation power ...)
We have to approches : 
- Time sharing, running one task at time and quickly switching among other tasks
- Space sharing, each task gets a portion of the available space

### The process state transition diagram 
A process can be one of several states during its life cycle: 
- **Running** : A process is running on a CPU. This means it is executing instructions
- **Ready** : A process is ready to run, but for some reason the OS has chosen not to run the process at this moment.
- **Blocked** : A process has performed some kind of operation that makes it not ready to run until some other event takes place.

![[Pasted image 20240221091946.png]]
<u>Possibles way of blocking </U> 
- Loading the program (from disk)
- Potentially all system calls of the program 
	- Opening the file 
	- Reading the file 
	- Writing to STDOUT 
- Useful tool : ``Strace`` 
	- Lists all system calls of program 

### OS mechanisms for process management 

#### OS data structure to manage process (in Linux)

The OS keeps a tree of all processes and all processes have a parent (except "init")

The OS scheduler maintains the set of schedulables processes. 

Information for each process is stored in a **process control block** (on Linux, this is called task_struct) that contains: 
- PID 
- Process state / context 
- Pointers to parents process 
- CPU context 
- Pointer to address space
- IO status information 
- Pointer to a list of open files

#### Process API

![[Pasted image 20240221093348.png]]

OS provides a set of interfaces to create and manage processes : 
- ``fork()`` executes a child process (a copy of the parent process)
	- The new process is the **child process** with a different process ID (PID)
	- fork returns -1 if there is a error, 0 if executing the new child process, >0 if executing in the parent process
- ``exec()`` executes a new program 
	- It loads a new program in the context of an already running process (child), **replacing** the previous **executable program**
	- NO new PID allocation
- ``exit()`` terminates the current process 
	- When a process terminates, it executes exit(), either directly on its own, or indirectly via library code
	- The exit() call resumes the execution of a **waiting parent process**
- ``wait()`` blocks the current process until the child terminates
	- A parent process uses wait() to suspend its execution until one of its children terminates. The parent process then gets the exit status of the terminated child
	- Return the PIS of the terminated child process 


<u><b>Why do we need ``fork()`` and ``exec()``</u> </b>
Multiple programs can run simultaneously 
User can perform various operation between fork() and exec() calls to enable various use case : 
- To redirect standard intput/output
		fork, close/open file descriptors, exec
- To switch users
		fork, setuid, exec
- To start a process with a different current directory 
		fork, chdir, exec


<u>Waiting for children to die...</u>
Scenarios under which a process terminates
- By calling exit() itself 
- OS terminates a misbehaving process 

Terminated process exists as **a zombie**

When a parent process calls wait(), the zombie child is cleaned up or "reaped"

If a parent terminates before child, the child becomes a **orphan**
- init (pid: 1) process adopts orphans and reaps them

So we could add a state to our state transition the **zombie** 
The 4 states : 
- Running
- Ready 
- Blocked 
- Zombie


### How the shell execs programs 
<u>How does a shell work?</u>
In a basic OS : the init process is created after hardware initialization 
The init process spawns a shell like bash, sh, zsh, fish (all are **terminals**)

**Shell is a UNIX term for the interactive user interface with OS** 

Shell reads user commands, forks a child, execs the command executable, waits for it to finish, and reads the next command.

Common commands are executable that are simply exec’ed by the shell: 
- Exemple : cat, ls, mkdir, cd



## Semaine 2/3 vidéos

### Les pointeurs !
A quoi ça sert??
- Partage d'objet sans duplication (référence)
- Généricité 
- Allocation dynamique 

#### Définition
Un pointeur est une variable qui contient l'adresse d'un autre objet informatique  
	Une "variable de variable"

<u>Déclarer</u> un pointeur c'est juste : ajouter une page dans notre carnet de référence 
<u>Allouer</u> un pointeur c'est juste : aller construire une maison quelque part et noter son adresse sur la page p. 
<u>Libérer</u> un pointeur c'est juste : aller **détruire** la maison dont l'adresse est écrite en page p.
	ATTENTION l'adresse existe toujours il n'y a juste plus de valeur correspondante
p1 = p2 c'est juste : Recopier à la page p1 l'adresse écrite à la page p2
p1 = NULL c'est juste : On gomme la page p1

#### En pratique 
On rajoute l'étoile * 
Exemple : `int* px = 2` 

& est l'opérateur qui retourne l'adresse mémoire de la valeur d'une variable 
.* est l'opérateur qui retourne la valeur pointée par une variable pointeur  

`( * p).x` est équivalent à ` p->x` (pour les structures)

Pour les passages d'argument dans les fonctions il faut utiliser le "const" 

<u>const</u>
"Const s'applique directement à ce qui précède sauf s'il n'y a rien qui derrière, il s'applique à ce qui suit."

Par exemple : 
![[Pasted image 20240226132723.png]]


### Allocation dynamique 
De base quand on déclare des variables, elles sont allouées automatiquement par c. 

Mais nous on peut aussi faire de l'<u>allocation dynamique</u> qui nous permets d'utiliser des données **en dehors** du block dans le quel elle était déclarée. + si on a des gros gros truc faut les allouer dynamiquement parce que le stockage de la pile est **limité**.

Mémoire virtuelle d'un processus : 
![[Pasted image 20240228103558.png]]
Pile ("stack"): stockage pour les variables locales 

Tas ("Heap"): stockage pour l'allocation dynamique, le tas est en général limité par l'espace d'adressage. 

<u>Comment faire??</u>
Pour allouer de la mémoire on utilise les fonctions `malloc` ou `calloc` 

Syntaxe : `void* malloc(size_t size);` 
Example : `pointeur = malloc(sizeof(double))` 

Si on veut allouer plusieurs elements à la suite, on utilise `calloc` (qui est préférable!!)

Syntaxe : `void* calloc(size_t nb_element, size_t taille_element);` 
Exemple (allouer de la place pour 3 doubles ) : `pointeur = calloc(3, size(double))` 

<u>Différence entre calloc et malloc</u>
`calloc` est protégé contre l'erreur de débordement de la multiplication. 
Mais en plus en plus `calloc` initialise à 0 (le contenu de) la zone allouée. 

***Conseil*** : initialisez **toujours toute** la mémoire utilisée. 
Grâce à la fonction `memset(pointeur, valeur, taille)` 

On doit gérer les erreurs, si `malloc` ou `calloc` n'arrivent pas à faire l'allocation il retourne NULL. 

<u>Libérer la mémoire</u> 
On ***doit*** libérer la mémoire quand on n'utilise plus car sinon on n'aura plus d'espace disponible. 
On le fait grâce à la méthode : `free(pointeur);` 
Par contre le pointeur existe toujours donc on doit aussi réinitialiser le pointeur avec `pointeur = NULL` 

***Conseil*** : Initialisez **toujours** vos pointeurs. Utilisez NULL si vous ne connaissez pas encore la mémoire pointée au moment de l'initialisation. 


<u>Realloc</u>
Syntaxe : `pointeurnew = realloc(pointeurold, newsize)` 
Nous permet de recopier les valeur qui étaient pointé par l'ancien pointeur et les mettre dans un nouveau pointeur mais avec une taille différente. 
Cela déplace le pointeur <u>si nécessaire</u>

#### Résumé joli des pointeurs : 
![[Pasted image 20240228124815.png]]


## Semaine 2/3 lecture 1

We still have the same problem, we want to run a lot of process but we **don't want to trust** any of them. This is the responsibility of the OS ! --> PROTECT (security, privacy, unfavorable resources utilization)

To execute *untrusted* processes : 
![[Pasted image 20240226094323.png]]

#### CPU virtualization 
The **goal** here is to give each process the *illusion* of **exclusive CPU access** !

The 2 approches for <u>virtualization</u> :
- Time sharing 
- Space sharing 

There are different strategies for the component of the computer :
- CPU : time sharing
- Memory : space sharing
- Disk : space sharing 

Today we will talk about the **CPU** 

### Limited Direct Execution 

<u>Basic technique</u> 
Limited direct execution enables programs to execute as fast as possible with some restrictions.
The direct execution is simple: Run the program directly on the CPU without any restrictions!

Problems: 
- **Restricted operations** : How does the OS ensure that a process does not execute/run a privileged code, while running it efficiently?
- **Control process execution on a CPU** : How does the OS stop running a process and switch to another one, which is required for virtualizing the CPU?

The most important slide :
![[Pasted image 20240226094937.png]]
### System calls

How can a process request (from the OS) for operations that are only possible in the kernel mode ?? 

#### Requesting OS services (user m. -> kernel m.)
Processes can request OS services through the system call API (example: fork/exec/wait)

System calls transfer execution to the OS, meanwhile the execution of the process is suspended
![[Pasted image 20240226101352.png]]

So the <u>systems calls</u> go to kernel mode and give us the data/operation that we need. 
Here are some of the key functionalities :
- Creating and destroying processes
- Accessing the file system
- Communicating with other processes 
- Allocating memory 

<u>Going to kernel mode</u> 
	A process executes a special **trap** instruction 
	CPU jumps into the kernel mode and it raises the privilege level at the same time 
	Now , privileged operations can be performed 
	.
	When finished, the OS calls a special **return-from-trap** instruction 
	Return to the calling process and lowers the privilege 
	Now, privileged operations cannot be performed 	![[Pasted image 20240226102247.png]]

### Traps and Interrupts 

<u>Traps/Exceptions</u> (the same thing)
Handle internal program errors 
	Overflow, division by zero, accessing not allowed memory region
Exceptions are produced by the CPU while execution instructions 
Exceptions are **synchronous** : CPU invokes them only after terminating the invocation of an instruction 

#### OS configures hardware at boot time 
![[Pasted image 20240226103044.png]]

#### Requesting OS services using system call numbers
![[Pasted image 20240226103414.png]]

#### Interrupts 
The OS can take control with this approche !

<u>Interrupts</u> : Hardware provides signaling mechanism for the OS to **regain control**
(the concept is exactly the same as in COMPARCH)

Timeline of an interrupts:
1. CPU detects it 
2. Suspends the user process and switches to the kernel mode 
3. Executes the appropriate interrupt handling code 
4. Restores the user process 

Interrupts to the rescue for OS ensuring control
- OS configures the **timer interrupt** to regain control 
- The interrupt is fired every **certain milliseconds** 
- With every interrupt, CPU switches from user mode --> kernel mode

#### Concurrent interrupts and system calls 

**Question**: What happens when an interrupt happens while processing another interrupt or processing a system call on the same CPU?

**Answer**: Hardware provides instructions to <u>temporarily delay</u> the delivery of an interrupt (disable interrupt), and re-enable them when safe (enable interrupt)

- Interrupts are disabled when an interrupt handler is running 
- Locking schemes to protect concurrent access to OS data structures 



## Semaine 2/3 Lecture 2 : The OS scheduler 

### Mechanism : Context switch
For these 2 cases: 
The OS can be in the kernel mode, it cannot return back to the same process.
	Process is finished or must be terminated (e.g., invalid operations)
	Process did a system call and it is waiting for it to complete (IO operation)
The OS does not want to run the same process
	The process has run for too long
	There are other processes present and they should be scheduled

The OS performs a **context switch** to stop running one process and start running another, i.e., switch from one process to another

<u>Context switch</u> 
A context switch is a mechanism that allows the OS to store the current process state and switch to some other, previously stored context.
- The context of the process is represented in the process control block (PCB)
- The OS maintains the PCB for each process
- The process control block (PCB) includes hardware  registers 
	- All registers available to user code 
	- All process-specific registers 
	- Stored in the PCB when the process is not currently running 

<u>The context switch procedure</u>
The OS does the following operations during the context switch 
1. **Saves** the running process execution state in the PCB
2. **Selects** the next thread
3. **Restores** the execution state of the next process 
4. **Passes** the control using return from trap to resume next process
![[Pasted image 20240228083944.png]]
### Scheduling internals and metrics 

#### Scheduler implementation 
The simplest form is to maintains a **queue** for each process state

#### The idle process
When we have **not process to run** the next schedule process is <u>the idle process</u>. 
It is used when we dont have anything more to do. 
It never blocks or executes any I/O.

The idle process issues special instructions to save power

#### Scheduling metrics 
2 metrics : **utilization and trunaround time** 
1. Utilization : What fraction of the time is the CPU executing a job.
2. Trunaround time : Total time from job arrival to job completion.

#### Scheduling assumptions 
To start to understand how the scheduling works we will first see it with this assumptions: 
1. Each job runs for the same amount of time
2. All jobs arrive at the same time
3. Once started, each job runs to completion
4. All jobs only use the CPU (no IO)
5. Run-time of jobs is known
6. For now, we assume a single CPU

### CPU-only scheduling policies 

#### First in, First Out (FIFO)
Pretty logic, the first in will run first, the second : second, etc...

**But** if we relax the first assumption "1. Each job runs for the same amount of time". 
With this FIFO policies if a bigggggg process get there first it blocks all the others processes ....

#### Shortest Job First (SJF)
Choose ready jobs with shortest running time. 

**But** if we delete the second assumption "2. All jobs arrive at the same time", we can still be blocked by biggggg processes if the shorter processes arrive just after the big one. 

#### Shortest Time to Completion First (STCF)
If we relaxe the 3th assumption "3. Once started, each job runs to completion", it allows us to interrupt jobs.

<u>Preemptive scheduling</u> 
Previous schedulers (FIFO, SJF) are non-preemptive
	Non-preemptive schedulers only switch to other jobs once the current jobs is finished (run-to-completion)
	OR 
	Other way: Non-preemptive schedulers only switch to other process if the current process gives up the CPU voluntarily
Preemptive schedulers can take the control of CPU at any time, switching to another process according to the scheduling policy 

STCF extends the SJF by **adding** preemption
Any time a new job enters the system : 
	STCF scheduler determines which of the remaining jobs has the least time left
	STCF then schedules the shortest job first 

But if we think about a new metric (see below) the STCF is not that good..
#### Scheduling metrics: the comeback
We had : 
1. Utilization : What fraction of the time is the CPU executing a job.
2. Turnaround time : Total time from job arrival to job completion.
But now we also want to know :
3. **Response time** : How long it takes until a task/job is scheduled

#### Round Robin (RR)
Instead of running jobs to completion, RR runs a job for a **fixed interval** (or a time-slice) and then switches to the next job in run queue. 
**Alternate** ready processes every fixed-length time slice 

The good and the bad : 
![[Pasted image 20240228093440.png]]

### CPU and I/O scheduling 
Let's now relax one more assumption : "4. All jobs only use the CPU (no IO)", and so USE I/O.

<u>I/O awareness</u>
Now, schedulers need to incorporate both CPU and IO
	If IO request is sent to the disk, the process might be blocked for few milliseconds based on the current IO load of the disk

Scheduler should schedule another job during IO requests: Sometimes, it can lead to many short CPU bursts (based on the workload)

-->Scheduler needs to account for both IO and CPU for better resource utilization<--
	For example : 
![[Pasted image 20240228094351.png]]

#### Multi-level Feedback Queue (MLFQ)
![[Pasted image 20240228094502.png]]

Relaxing assumption 5  "Run-time of jobs is known" that does not have runtime information of jobs.

<u>MLFQ</u>
**Goal**: Supporting general purpose scheduling
**Challenge**: The scheduler must support both long running background tasks (batch processing) and low latency foreground tasks (interactive processes)

In MLFQ we will try to optimize 2 types of processes :
- **Batch process** : Response time is not important; cares for long run times (reduce the cost of context switch, cares for lots of CPUs) --> MLFQ optimise ***turnarournd time*** 
- **Interactive process** : Response time is critical, short bursts (context switch cost is not important, not a lot of CPU is required but used frequently) --> MLFQ optimise ***response time*** 

<u>Approach of the MLFQ</u> 
Multiple levels of round robin (RR) !!
- Each level has higher priority and preempts lower level
- Process at a higher level will always be scheduled first
- High levels have short time slices, lower levels run longer

Basic rules of MLFQ 
Rule 1 : if priority (A) > priority (B) then A runs
Rule 2 : if priority (A) == priority (B) then A, B run in RR
Rule 3 : Processes start at top priority
Rule 4 : If a process A uses up total time slice, the scheduler lowers A’s priority
Rule 5 : Periodically moves all jobs to the topmost queue (priority **boosting**)

Boosting : At some time, every processes are put back to the same priority (see boost in the example)

example : 
![[Pasted image 20240228095526.png]]

## Semaine 3/3 vidéos
### String 
Il n'existe pas de string en C !!!

En C, une chaîne de caractères est codée dans <u>un tableau de caractères.</u>

Pour <u>déclarer</u> une chaîne de caractère on peut : 
1. Par une constante 
	"Bonjour"
2. Par une variable de taille fixe 
	char nom[25];
	char nom_fichier[FILENAME_MAX];
	char const welcome[] = "Bonjour";
3. Par une allocation dynamique (POINTEUR)
	`char* nom;` 
	Il faut faire l'allocation avec `malloc/calloc` 
	Il faut de la place pour n+1 (pour stocker le '\0' en fin de mot)
	.
	**SYNTAXE**
	Si s doit être modifié on utilise : `strncpy(s, "bonjour", TAILLE);` 
	Si on veut que la chaîne de caractères soit constante :`char* s = calloc(TAILLE+1, 1)` puis -->  `const char* s = "bonjour";` 

Plein de fonctions disponible -> voir `$man 3 string` 
-> vaut mieux prendre les fonctions avec les n 

<u>lecture/ecriture</u>
`printf("....%s", s);` 
`scanf("%s", s);` 

### Pointeur sur fonctions
En C on peut en fait pointer n'importe où et donc sur les **fonctions** aussi 

<u>Syntaxe</u> : mettre (* ptr) à la place du nom de la fonction : 
`double sum(int i)` --> `double(*g)(int i)`  

Grâce à cela on peut passer une fonction dans les paramètres d'autre fonctions.

#### Pointeur/Fonctions génériques
Pour pointer vers un espace de mémoire sans spécifier le type de pointeur grâce à : `void*` 

Et du coup pour faire des fonctions génériques, on va utiliser des pointeurs génériques en argument de la fonction. 
	EX: `int(*compar)(const void*, const void*);` 

***Toutes les fonctions sont des pointeurs*** 

Pour plus de compréhension du code -> utiliser des <u>Typedef</u> <- 


### Casting (conversion)
On peut convertir un type simplement grâce à `(type) expression` 
EX: 
```C
double x = 5.4;
int i = (int) x; //i = 5
```

Mais !! Attention !! dans le cas des pointeurs, cela ne va pas changer la zone mémoire mais son **interprétation**.... (ce qui lead a des big problèmes)

Mais le casting est intéressant pour écrire du code générique en castant les types dans l'appel de la fonction. 

Les casts avec des void* sont plus simples: 
	L'affectation vers void* est permise sans cast 
	EX: `int* ptr1; void* ptr2; ... ptr2 = ptr1` 
	


## Semaine 3/3 lecture 1
rien
## Semaine 3/3 lecture 2
### Address space as an abstraction 
The address space is a **set of memory addresses** that are only **accessible** to a **program**
	The process resides somewhere else in the physical memory

--------------------------------------------------
#### Concept of Indirection
We can solve any problem by introducing an extra level of indirection.

**Indirection**: Practice of using an intermediate layer or mechanism to access / manipulate data or resources instead of directly interacting with them. --> we use that in memory!

--------------------------------
<u>Adresses</u>
A memory address refers to the location of a **byte** in memory. 
	Most machines are *byte-addressable*

**Recall** : Logical view of a process' memory layout
![[Pasted image 20240306084721.png]]
	The memory used depends on the program (which is logic...)

#### Dynamic data structure : Stack
The stack follows a first-in-last-out policy (FIFO)

It has two operations : (super logique merci)
	Push
	Pop

Memory allocated in function *prologue*, freed when returned
	Prologue is a few lines of code at the beginning of a function 
	Prepares register and the stack to be used within the function 

What happens to the data when function returns?
--> Data from previous functions gets **overwritten** when the new function initializes !!

#### Dynamic data structure : Heap
The heap of randomly allocated memory objects with statically unknown site and statically unknown allocation patterns. The size and lifetime of each allocated object is unknown !

It is very practical to have this type of storage, but it is harder to manipulate

API : 
- **alloc** creates an object (endroit en memoire) 
- **free** indicates it is no longer used 

<u>Heap: free list</u> 
**Idea** : Abstract heap into a list of free blocks 
- Keep track of free space, program handles allocated space
- Keep a list of all available memory objects and their size
**Implementation** : 
- alloc : take a free block, split, put the remaining back to the free list
- add : add block to the free list
![[Pasted image 20240306093043.png]]

<u>Heap : better implementation</u>
Allocation : find a fitting objet 
- first fit : find the first object in the list and split it 
- best fit : find the object that is closest to the size 
- worst fit : find the largest object and split it 

Free : merge adjacent blocks 
	If the adjacent region is free, merge two blocks

### The case for virtual memory 
In the beginning we didn't need any virtual memory because only **one program** was running on a machine. But now with our systems many many program run simultaneously and we need to share the memory space without compromising any data, it also help to protect programs from one another. 

<u>Virtual address space</u> 
- Virtual address space is the address at which a memory block / cell appears to reside from the perspective of an executing program (process)
- It maps the **virtual** to the **physical** address
- SEE COMPARCH COURSE

<u>Memmory management unit (MMU): base and bounds</u>
![[Pasted image 20240306094418.png]]
Keep two values (in registers) for every process: base and bounds 
- Base register sets the minimum address
- Bounds register sets (virtual) limit of the address space, highest physical address that is accessible becomes base + bound

Pro and cons : 
+Achieves security
+Achieves performance

-No memory sharing 
-Waste of physical memory 
-Results in memory fragmentation 

### Fragmentation & Segmentation 

***PAGING (SEE COMPARCH)***

<u>A MMU : segmentation</u>
![[Pasted image 20240306095423.png]]
OS can place segments independently anywhere in the physical memory
	Unlike for base+bound: the whole process memory should be contiguous

**Sharing**: Segmentation introduces **protection** **bits** (read/write/execute bits)

Issues with segmentation :
	space : Each segment must be backed by the physical memory
	space : Although external fragmentation is decreased, it still happens since the
	hardware needs to allocate physical memory for every segment