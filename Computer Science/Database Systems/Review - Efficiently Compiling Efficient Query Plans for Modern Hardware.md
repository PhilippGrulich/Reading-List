
# ... > Hyper

## Fast Serializable Multi-Version Concurrency Control for Main-Memory Database Systems: 

#### _Thomas Neumann - Technische Universitat Munchen_

```  
@article{neumann2011efficiently,
  title={Efficiently compiling efficient query plans for modern hardware},
  author={Neumann, Thomas},
  journal={Proceedings of the VLDB Endowment},
  volume={4},
  number={9},
  pages={539--550},
  year={2011},
  publisher={VLDB Endowment}
}
```  

### Review:

#### Outline:
- Summary
- Reflection
- Related work
- Current state of the paper


#### Summary:


__Problem:__ 
There is still a huge performance gap between handwritten query code and the query execution in main memory databases. The reason for this are function calls in the Iterator model, which cause bad branch prediction, poor code locality, and complex book-keeping. 

__Solution:__
A novel query compilation strategy that:

1. Operator boundaries are blurred to keep data in registers as long as possible.
2. Data is pushed to operators, which result in better code and data locality
3. Queries are compiled into native code.

Outperform hand-written code

###### THE QUERY COMPILER
**Query Processing Architecture:**   
The proposed Query Processing Architecture is built on top of a more restrictive definition of *pipeline breakers*:
In this context, an algebraic operator is a *pipeline breaker* if it takes an incoming tuple out of the CPU registers and it is a *full pipeline breaker* if it materialized all incoming tuples before it continues to processing.
Therefore are all operations which have to transfer data back to main memory are *pipeline breaker*.

When we apply this definition to the classical *iterator model* or *the block-oriented* query processing model, we easily see the disadvantages of these models. The *iterator model* suffers from the high number of function calls (which always result in evicting the register content) and the *the block-oriented* which produce blocks which are larger the register capacity.

Therefore a novel query processing architecture is proposed, which reserves the direction of the data flow between operators.
Instead of pulling new tuples, with the *next()* function in iterator based processing models,
the proposed technique pushes them towards the consuming operators until they reach the next pipeline breaker [Figure 2]. On this way, the data can stay in the CPU registers. To achieve this every pipeline is compiled into a custom code fragment [Figure 3]. 

**Compiling Algebraic Expressions:**   
Each of the four code fragments in figure 3 represents one pipeline. We see that each fragment contains operations from multiple Operators, therefore we can say that the boundary's between them are *blurred*.  
To model this approach in a similarly simple way as the iterator model, the authors provide a novel mental abstraction for defining operators.  
Conceptual each operator offers two functions:
- produce() -> call produce on child operator.
- consume(attribute, source) -> return code template for operator.

The *produce* ask an operator to start producing result tuples, which are then pushed to the consuming operator, by calling there *consume* function.
It is important to notice that this functions only exist in the code generation process. Therefore the resulting machine code only contains the necessary code fragments which are returned by the *consume* function.

###### Code Generation   
**Generating Machine Code**   
For generating the machine code the Low-Level Virtual Machine (LLVM) compiler framework[1] is used. It provides a portable and strongly typed assembler code, which can be executed directly using an optimizing JIT compiler and it also solves a range of tedious problems which writing assembly code. For example, LLVM offers an unbound number of registers, therefore it can pretend that the CPU have enough registers available for every attribute of a tuple.
Beside this, the LLVM framework can directly interact with c++ code. Therefore it is possible to write more complex operations (eg. Memory management, Indexes) in C++. Therefore C++ is only called from time to time and the generated LLVM code dose the most processing. This is a huge benefit for the developer's productivity.

**Complex Operators**

**Performance Tuning**
As a result of using the *consume/produce* model and LLVM for the code generation extremely fast query execution is achieved. It interesting to see that in some cases (Query 1 of TPC-H) now more time is spent on hashing. This highlights that the performance bottleneck has now shifted to parts of the execution pipeline which have been negligible before. Also, the branch prediction has now a huge performance impact and has to be taken into account by the query compiler. 

###### ADVANCED PARALLELIZATION TECHNIQUES
By organizing the dataflow in this data-centric way we achieve excellent data locality.
Which also enables data parallelism to improve the query performance even more.
First, we can use SIMD vector instruction, which allows us to compute multiple tuples with one CPU instruction. 
Second, the definition of pipelines helps us to model intra-query parallelism nearly without any code changes.

###### EVALUATION
The proposed query compilation technique was implemented in the main memory database system hyper.
Which was then benchmarked with the TPC-C and TPC-H workload.
In a comparison with MoneteDB, VectorWise, and an unknown DBs System X it is shown that in all cases the Hyper + LLVM query engine beats the other systems in order of magnitudes. It is also interesting that even if the compilation time of the query takes up to the halfe of the whole query run time it is still two times faster than the second system (TPC-H Q1)

Besides this simple benchmark also the code quality of monetDB and hyper is compared. This comparison highlights that the custom code generation result in a reduction of branches, miss predictions and cache misses in the orders of magnitudes.  

###### CONCLUSION


###### Strong and weak points:

###### In reason history: 

Further Links:

[Implementation of this idea in Spark 2.0](https://databricks.com/blog/2016/05/23/apache-spark-as-a-compiler-joining-a-billion-rows-per-second-on-a-laptop.html)

[Review on Morning Paper](https://blog.acolyer.org/2016/05/23/efficiently-compiling-efficient-query-plans-for-modern-hardware/)
