# Scalable Computing in Genomics
## Lower costs → More data

The reduced cost of sequencing has lead to a huge growth in sequencing data. The more samples the more we care about scalability. The less refined the data the more we care about scalability. There are two components of scalability, time and space. Time as in how long it takes to process the data; space as in how we store the data, how we can compress it such that we can store and retrieve. 

## Typical Epigenome Project
A typical epigenome project consists of many independent samples such as patients, cell lines, conditions, etc,. We define our analysis in two stages: ‘pipeline’ is where we process raw data, i.e. same procedures done on every sample; then we get to a stage where we perform ‘analysis’, which is taking our processed data, looking across samples and analyze them. 

## Approaches for Scalability
There are four approaches for scalability. Parallelization and optimization are more relevant to the time question, i.e. how to execute more efficiently computationally, whereas compression and databases are more relevant to the space complexity question. 
Parallelization, where we split a compute task, and then compute each split simultaneously. One of the framework to perform this is ‘split-apply-combine.’ This is useful because many problems call for a similar type of computing architecture. Here we have our data analogous to a puzzle, where we split them into chunks.  For each chunk we apply some function, then take the results from chunks and combine them as one. Existing frameworks are MapReduce and Hadoop. These approaches are applicable at different levels, sample level or within sample level. If we think of these puzzle pieces as different samples, then we can view this as splitting across different samples, and then applying our function to each sample. We can also apply this to one sample, say if we have an aligned reads file, and now we can split by chromosomes, then we apply our function to each chromosomes. So our combination is now summarizing those results across chromosomes. 

## Scopes of Parallelism 
We have a sample and a sequence of steps we will apply to that sample. We can run our data naively in a serial way, or we can parallelize it. There are three different ways to parallelize. The first way is to parallelize by process, where we use more than one CPU on that process. Another way to parallelize is by sample, where we run many samples simultaneously, but at each single step we only use one CPU. The third way is to parallelize by dependence, the idea is to identify the independence and dependence between steps and parallelize the independent parts. 
There are advantages, disadvantages to each of these way of parallelization. 
Parallelize by process is straightforward using tools. Its disadvantage is that it is a node-threaded parallelism, meaning we cannot parallelize across computers/nodes in a cluster. We are also limited by tool capacity. Sometime the aligner does not have the capacity to parallelize. Aside, in the cluster hardware, we have nodes, and each node is a computer. Each node has 16 cores times 4 different units, so if we are running a process on this cluster, we cannot use more than 64 cores to parallelize by process. 
Now we look at parallelize by sample/job. We have no shared memory so we cannot parallelize the same process across nodes, but we can parallelize by sample across nodes independently. HPC clusters are intended for parallelize by sample, where the job is restricted by the size of HPC instead of the size of the node. Another advantage is we no longer depend the tool’s capacity to parallelize.
The third one is parallel by dependency, where we take the different tasks in the pipeline, and run different tasks simultaneously. This is not necessarily node-threaded because we can run different tasks on different nodes. The tasks should be independent hence requires no shared memory. This is the benefit over the process level. The cons are from the dependency.  1) we may have shared file-system requirements 2) requires a dependency graph of workflow steps 3) requires a layer of task management (such as task synchronization) above typical HPC usage 4) limited to independent workflow elements 5) requires (partial) independence of jobs
In summary, parallelizing by process is very easy. Parallelizing by sample is not as easy as parallelizing by process, since we need to submit different jobs, but these jobs are independent of pipeline. Parallelizing by dependency is not independent of the pipeline. For this, we have to design a dependency graph for each task. (See graphs for time benefit versus sample resources ratio in the slides)
There are two ways to think about parallelization. One way is to parallelize jobs (BatchJobs and snow), and the other is parallelizing within one interactive environment (parallel package in base R.) The mclapply is node parallelism. In python, there are two way of parallelizing. The first one is using the subprocess module, which spawns a new process through a shell-like system (see code in the slides) from within python. The other way to do it is multiprocessing (package), which creates a new pool of processors and provides a map function that operates similar to lapply functions in R.

## Workflows
Workflows are important in epigenomics and lots of different areas in genomics. A workflow is a repeatable sequence of tasks that process a piece of data. For example, we get raw read $s$ from a sequencer, we align them, then we take those alignments and run MACS or SCICER to call peaks on them, followed by some analysis on these peaks. This is a workflow spectrum, we can approach this in different ways. From interactive analysis to pipeline, say we have a single sample, we type a sequence of commands such as trimmomatic, bowtie2 and the interactive means we are interacting within the shell. This is not efficient enough when we have to enter the same commands for many samples, so we write shell scripts to run these different tasks. One step forward is to wrap the shell scripts into a pipeline framework. Frameworks, or workflow frameworks are basically development toolkits to make it easier to build workflows. 

There are many workflows out there, most of them are domain specific. Here we introduce three mainstream workflows, snakemake, nextflow, and common workflow language. Make is mostly used for compiling software, containing recipes for how to compile source code into the binary output executable. What makes it useful in our case is that we can define output or target, where make automatically computes the dependency (the order of which commands to execute as well as executing the dependent parts of the commands to compute the input.) So instead of writing a sequence of tasks, we actually write rules for the workflow to figure out the dependency for us. Nextflow is similar to snakemake. In common workflow language, we have a separate tool file, making it even more modular so that we can use the same tool file across different workflows. The other difference is that both snakemake and nextflow have their specific excitation method, whereas common workflow language is designed to be a universal/standardized language that can be run on different engines/independent of execution engine. 

## Stop using shellscipts for pipelines!
They are difficult to write, difficult to read. From stack overflow:

> The shell makes common and simple actions really simple, at the expense of making more complex things much more complex. Typically, a small shell script will be shorter and simpler than the corresponding python program, but the python program will tend to gracefully accept modifications, whereas the shell script will tend to get less and less maintainable as code is added. This has the consequence that for optimal day-to-day productivity you need shell-scripting, but you should use it mostly for throwaway scripts, and use python everywhere else. -Anonymous

Still, shell scripts are super useful for small tasks. When the task gets more complex, it is better to move on to something more maintainable.

## Advantages of workflows
- Reproducibility
- Restartability
- Reusability
- Logging
- Provenance (keep track of updates)
- Scaling up compute resources
- Dependency management

## Optimization
The big O notation is a way of simplifying the complexity of an algorithm (by looking at the highest order terms.) By looking at which class an algorithms belongs to, we can see how well it scales (see plot in the slides.) Factorial time O arises in problems involving permutations such as the Traveling Salesman and the shortest common superstring in a brute force way. Exponential time is worse than polynomial time. The exponential time comes from nested subproblems. The polynomial time comes from nested loops. Find overlaps, sequential search can be completed in linear time, binary search can be completed in O(logn) (Manhattan phonebook.) Indexing reduces lookups from linear time to constant time. Tabix indexing allows us to randomly access anywhere in the file.

## Language choice
Existing implementations are often faster than yours; Compiled languages are faster than scripting languages; Loops in R are slow, vectorized way are faster. Programs run faster in machine code. We can also link C code into R or Python. Often in genomics, disk is the bottleneck, i.e. read/write takes more time. We can prevent read/write by loading into memory. Memory lookups/random access is much faster, but we may not have enough memory for a big file. Extract ATAC in consensus, method 2 is faster and uses more memory because we loaded the file into memory thus avoiding reading from disks. We cannot switch peaks and reads because reads are much larger than peaks, and we may not have enough memory to load all the reads. Interconnect is fast between nodes on a cluster. 

## Databases
- Database:Space as Parallelization:Compute
- They offload storage requirements
- They are critical for simultaneous multi-user
- Reduce storage requirements by centralizing
- Data has gravity, it brings compute to it

## Problem with Cloud Platforms
- Platform lock-in
- Difficulty integrating across platforms
- Duplicated effort for both users and developers

