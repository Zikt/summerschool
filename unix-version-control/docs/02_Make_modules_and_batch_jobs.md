---
title:  Make, modules and batch jobs
author: CSC Summerschool 
date:   2019
lang:   en
---
# Make {.section}

# Compiling and linking: a reminder

<div class=column>
- A compiler turns a source code file into an object file of machine code that 
  the processor can understand
- A linker combines several compiled object files into one executable file
- Together, compiling and linking can be called building
</div>
<div class=column>
![](images/building.svg){.center}
</div>

# Compiling and linking: possible problems

- Programs should usually be separated into several files
    - $\Rightarrow$ complicated dependency structures
- Building large programs takes time
    - rebuilding only the parts we changed? 
- Having different options when building 
    - debug versions, enabling/disabling features, etc.


# Make: Purpose 

- Make allows you to define how to build parts of a program and how they 
  depend on other parts. This enables:
    - Building parts of a program and only rebuilding nessecsary parts
    - Building different version and configurations of a program 

 ![](images/depend.png){.center width=40%}

# Make rules

<div class=column>
Make **rules** define how some part of your program is built.

- **Target**: The output or aim of your rule  
- **Dependency**: What your target depends on
- **Recipe**: How you produce your target
- Rules are defined in a file which is normally named **Makefile**

</div>

<div class=column>

 ![](images/rule.png){.center width=100%}
 
_A make rule_
</div>



# Simple Makefile example
<div class=column>
- Dependencies can be files or other rules  
- Recipes consist of one or more shell commands
    - Recipe lines start with **tabulator**
- If the dependencies are newer then the target, make runs the recipe
- Run make with ` make target`
	* Without an argument, make runs the first rule in the file
</div>

<div class=column>
```makefile
main: main.c functions.o
	gcc -c main.c
	gcc -o main main.o functions.o

functions.o: functions.c
	gcc -c functions.c

clean:
	rm -f main.o functions.o main

```
</div>

# Variables and patterns in rules

<div class=column>
- It is possible to define variables, for example compiler and link commands 
  and flags
- Targets, dependencies and recipes can contain special wild cards
</div>

<div class=column>
```makefile
CC=cc
CCFLAGS=-O3 
...

# Files of the form filename.o depend on 
# filename.c
%.o: %.c
        $(CC) $(CCFLAGS) -c $< -o $@

```
</div>


# Modules and batch job system {.section}

# Module environment on Sisu

- Supercomputers have a large number of users with different needs for development environments and applications
- _Environment modules_ provide a convenient way to dynamically change the user's environment 
- In this way, different compiler suites and application versions can be used more easily
	* They basically just change where things are "pointing". So when you run `gcc` the loaded module decides 
whether you are using version 4.9 or 5.3 or 6.0 and so on
	* Most programs requires loading a module to be accessible

# Most common Module commands in Sisu
<div class="column">
`module load mod`  
`module unload mod`  
`module show mod`  
`module avail`  
`module list`  
`module switch mod1 mod2`  
`module help mod`  
</div>

<div class="column">
<small>
Load module **mod** in shell environment  
Remove module **mod** from environment  
Get information about module **mod**  
List all available modules  
List loaded modules  
Switch loaded **mod1** to **mod2**    
Get information about a module or, if no argument is given, about the module sub-commands  
</small>
</div>

# Batch queue system

- On a cluster you can't run programs instantly. Instead you submit your program/simulation (i.e. job) to a queue. 
	* The queue enables effective and fair resource usage.
	* CSC uses SLURM as the queue system



- When running a job on a supercomputer you need to:
	- Describe how you want to run the job and what resources you need
	- Add a command that launches your program: 
	- Submit your job to a queue
- This is done with a batch job file

# Batch job file on Sisu


my_job.sh
```bash
#!/bin/bash                      | Invokes bash shell
#SBATCH -J my_job_name           | Defines a name for your job
#SBATCH –o %J.out                | Standard output file
#SBATCH –e %J.err                | Standard error file
#SBATCH –N 1                     | Number of nodes to use (each has 24 cores)
#SBATCH –t 1:00:00               | Maximum time to run the program (hh:mm:ss)
#SBATCH –p test                  | Batch job partition (initially use ’test’)
#SBATCH --mem=5G                 | Memory requirement
aprun –n 24 path_to_program
```

# Running batch jobs
- Submit your batch job script to the queue using sbatch  
		`$ sbatch my_job.sh`
- You can follow the status of your jobs with squeue:  
		`$ squeue -u my_username`
- If something goes wrong, you can cancel your job with scancel:  
		`$ scancel jobid`  
		(here the jobid is the numeric id of the job)
- Show job resource usage:  
		`$ sacct jobid`
