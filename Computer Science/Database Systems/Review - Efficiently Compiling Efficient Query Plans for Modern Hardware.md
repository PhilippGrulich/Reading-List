
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



###### THE QUERY COMPILER
Query Processing Architecture

Compiling Algebraic Expressions

###### Compiling Algebraic Expressions

Generating Machine Code

Complex Operators

Performance Tuning

###### ADVANCED PARALLELIZATION TECHNIQUES


###### EVALUATION
Systems Comparison
Code Quality

###### CONCLUSION

###### APPENDIX
OPERATOR TRANSLATION

MICROBENCHMARKS

QUERIES

OPTIMIZATION SETTINGS


Further Links:

[Implementation of this idea in Spark 2.0](https://databricks.com/blog/2016/05/23/apache-spark-as-a-compiler-joining-a-billion-rows-per-second-on-a-laptop.html)

[Review on Morning Paper](https://blog.acolyer.org/2016/05/23/efficiently-compiling-efficient-query-plans-for-modern-hardware/)
