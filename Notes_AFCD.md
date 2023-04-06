# Chapter 2: The Relational Model of Data

## 2.1 An Overview of Data Models

### 2.1.1 What is a Data Model?

数据模型是一个描述数据和信息的概念，这种描述通常包含三个部分：

1. *Structure of the data*.  这里说的数据结构和算法中的数据结构不太一样。在数据库的讨论中，数据结构常常指的是一个物理意义上的数据模型。
2. Operations on the data. 
3. Constraints on the data.

### 2.1.2 Important Data Models

数据库系统中最重要的两种数据模型是：

1. The relational model, including object-relational extensions.
2. The semistructured-data model, including XML and related standards.

## 2.2 Basics of the Relational Model

The relational model gives us a single way to represent data: as a two-dimentional table called *a relation*.  Now let’s introduce some terminology of relational model.

1. Attributes: The columns of a relation are named by attributes.

2. Schemas: The name of a relation and the set of attributes for a relation is called the *schema* for that relation.  For example:

   >Movies(title, year, length, genre)

- In a relational model, a database consists of one or more relations. The set of schemas for the relations of a database is called a *relational database schema*, or just a *database schema*.

3. Tuples: The rows of a relation, other that the header row containing the attribute names, are called tuples. For example:

   >(Gone with the wind,  1939, 231, drama)

   ==Note:== the order of a tuple must be consistent with the order in schema.

4. Domains: 首先，关系模型需要每个元组的每个部分都是原子性的(atomic), 即，他们必须都是原始类型如integer或者string. 而不允许是set，array这种类型。   In relational database terminology, ==a domain defines the permitted range of values for an attribute of an entity.==

   我们可以表示 **Movies** relation 的schema如下：

   Movies(title:string,  year:integer,  length:integer,  genre:string)

5. Equivalent Representations of a Relation:

   - Relations are sets of tuples, not lists of tuples. 因此，在一个relation中tuple的顺序不重要。不同顺序的tuple组成的relation是同一个relation。
   - 此外，属性的顺序也不重要。我们可以在定义schema的时候将属性的顺序打乱，得到的依然是同一个schema。

6. Relation Instance: We shall call a set of tuples for a given relation an *instance* of that relation.

7. Keys of Relations: ==A set of attributes== forms a key for a relation of we do not allow two tuples in a relation instance to have the same values in all the attributes of the key.

## 2.3 Defining a Relation Schema in SQL

SQL is the **principal** language used to describe and manipulate **relational databases**. 

There are two aspects to SQL:

1. The Data-Defination sublanguage for declaring database schemas and
2. The Data-Manipulation sublanguage for *querying* databases and for modifying the database.

### 2.3.1 Relations in SQL

SQL makes a dictinction between three kinds of relations:

1. Stored relations, which are called tables, a relation that exists in the database and that can be modified bt changing its tuples.
2. Views, which are relations defined by a computation.These relations are not stored, but are constructed.
3. Temporary tables, which are constructed by the SQL language processor when it performs ots job of executing queries and data modifications. 子查询中的子表。

### 2.3.2 Data Types

The following are the primitive data types that are supported by SQL systems. All attributes must have a data type.

1. CHAR(n): a ==fixed-length== string of up to n characters.

   VARCHAR(n): a string of up to n characters.

   区别：CHAR会以一些短字符来填充后面未满的空间来构成n个字符，而VARCHAR会使用一个结束符或字符长度值来标志字符串的结束，后面未满的空间不会做填充。

   >注意：SQL中的字符串使用单引号

2. Boolean：可以取 TRUE, FALSE, UNKNOWN.

3. INT

4. FLOAT和DECIMAL(n, d): DECIMAL(n, d)允许可以有n位有效数字的十进制数，小数点在右数第d位。例如$0123.45$ 是DECIMAL(6, 2) 定义的数值。

5. DATA

### 2.3.3 Simple Table Declarations

```sql
CREATE TABLE MovieStar(
    name      CHAR(30),
    address   VARCHAR(255),
    gender    CHAR(1),
    birthdata DATE
);
```

### 2.3.4 Modifying Relation Schemas

1. Delete a relation R :  `DROP TABLE R;`

2. Modify the schema of an existing relation: begin with **ALTER TABLE** and the name of the relation. We then have several options, the most important of which are

   1. **ADD** followed by an attribute name and its data type.
   2. **DROP** followed by an attribute name.

   example: `ALTER TABLE MovieStar ADD phone CHAR(16);`

### 2.3.5 Default Values

`gender CHAR(1) DEFAULT '?',`

### 2.3.6 Declaring Keys

There are two ways to declares an attributes or set of attributes to be a key in the CREATE TABLE statement that defines a stored relation.

1. We may declare **one** attribute to be a key when that attributes is listed in the relation schema.

   ```sql
   CREATE TABLE MovieStar(
       name CHAR(30) PRIMARY KEY,
       address VARCHAR(255)
   );
   ```

   

2. We may add to the list of items declared in the schema an additional declaration that says a particular attribute or set of attributes forms a key.

   ```sql
   CREATE TABLE MovieStar(
       address VARCHAR(255)，
       PRIMARY KEY (name) //注意，这里可以写多个属性，用逗号隔开
   );
   ```

## 2.4 An Algebraic Query Language

The operands of relational algebra are:

1. Variables that stand for relations.
2. Constants, which are finite relations.

### 2.4.3 Overview of Relational Algebra

The operations of the traditional relational algebra fall into four broad classes:

1. The usual set operations — union, intersection, and difference — applied to relations.
2. Operations that remove parts of a relation: “selection” eliminates some rows(tuples), and “projection” eliminates some columns.
3. Operations that combine the tuples of two relations, including “Cartesian product” and various kinds of “join” operations.
4. An operation called “renaming” that does not affect the tuples of a relation, but changes the relation schema, i.e., the names of the atrributes and/or the name of the relation itself.

### 2.4.4 Set Operations on Relations

When we put set operations to relations, we need to put some conditions on R and S:

1. R and S must have schemas with identical sets of attributes, and the types (domains) for each attributes must be the same in R and S.
2. Before we compute the set-theoretic union, intersection, or difference of sets of tuples, the columns of R and S must be ordered so that the order of atrributes is the same for both relations.

### 2.4.5 Projection

The *projection* operator is used to produce from a relation R a new relation that has only some of R’s columns.

$\pi_{A_1, A_2, \dots, A_n}(R)$ is a relation that has only the columns for attributes $A_1, A_2, \dots, A_n$  of R.

>注意：在关系代数中，不允许存在重复的元组。所以如果projection的结果中含有重复的元组，会将其删除。

### 2.4.6 Selection

The selections operator, applied to a relation R, produces a new relation with a subset of R’s tuples. The tuples in the resulting relation are those that satisfy some condition $C$ that involves the attributes of R. We denote this operation $\sigma_C (R)$. The schema for the resulting relation is the same as R’s schema, and we conventionally show the attributes in the same order as we used for R.

Example:

>$\sigma_{length \ge 100 \: AND \: studioName='Fox'}(Movies)$

### 2.4.7 Cartesian Product

你懂得

### 2.4.8 Natural Joins

Denote: $R \bowtie S$

表示：我们将公共属性相同的tuples组合在一起。也就是说，R和S的自然连接的结果中只会出现在R和S共有属性上相同的元组组合。

举例：

![image-20230403222322300](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230403222322300.png)

笛卡尔积：

![image-20230403222358438](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230403222358438.png)

自然连接：

![image-20230403222414435](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230403222414435.png)

### 2.4.9 Theta-Joins

The notion for a thata-join of relations R and S based on condition $C$ is $R \bowtie_{C} S$. The result of this operation is constructed as follows:

1. Take the product of R and S.
2. Select from the product only tuples that satisfy the condition C.

### 2.4.11 Naming and Renaming

- 重命名Relation以及各个属性名：$\rho_{S(A_1, A_2, \dots, A_n)}(R)$ . 该操作将R重命名为S， 将R的各个属性按顺序重命名为$A_1, A_2 \dots$ .
- 只重命名Relation：$\rho_S(R)$.

### 2.4.12 Relations Among Operations

1. $R \cap S = R - (R - S)$

2. $R\bowtie_C S = \sigma_C (R \times S)$

3. The natural join of R and S can be expressed by starting with the product $R \times S$. We then apply the selcection operator with a condition C of the form

   $R.A_1 = S.A_1 \: AND R.A_2 = S.A_2 AND \dots AND \: R.A_n = S.A_n$

   where A_1, A_2 … are all the attributes appearing in the schemas of both R and S. Finally, we must project out one copy of each of the equated attributes. 

   Let $L$ be the list of attirbutes in the schema of R followed by those attributes in the schema of S that are not in the schema of R. Then:

   $R\bowtie S = \pi_L (\sigma_C (R \times S))$

   

## 2.5 Constraints on Relations

### 2.5.1 Relation Algebra as a Constraint Language

在关系代数中有两种方法表达constraints：

1. If R is an expression of relational algebra, then R = $\empty$ is a constraint that says “The value of R must be empty”. 
2. If R and S are expressions of relational algebra, then $R \subseteq S$ is a constraint that says “Every tuple in the result of R must also be in the result of S.” 

### 2.5.2 Referential Integrity Constraints

# 3. Design Theory for Relational Databases

## 3.1 Functional Dependencies

### 3.1.1 Definition of Functional Dependency

A *functional dependency (FD)* on a relation $R$ is a statement of the form “If two tuples of R agree on all of the attributes $A_1, A_2, \dots, A_n$ , then they must also agree on ==all== of another list of attributes $B_1, B_2, \dots, B_m$.  We write this FD formally as $A_1 A_2 \dots A_n \rightarrow B_1 B_2 \dots B_m$ and say that ”

$A_1, A_2, \dots, A_n $  functional determine $B_1, B_2, \dots, B_m$ 

![image-20230404114616302](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230404114616302.png)    

### 3.1.2 Keys of Relations

We say a set of one or more attributes {$A_1, ... A_n$} is a key for a relation R if:

1. Those attributes functionally determine all other attributes of the relation. That is, it is impossible for two distinct tuples of R to agree on all of $A_1 ... A_n$ .
2. No proper subset of {$A_1, ... A_n$} functionally determines all other attributes of R.

### 3.1.2 Superkeys

A set of attributes that contains a key is called $superkey$. Thus, every key is a super key.

## 3.2 Rules About Functional Denpendencies

### 3.2.1 Reasoning About Functional Dependencies

### 3.2.2 The Splitting/Combining Rule

$A_1A_2 \dots A_n \rightarrow B_1 B_2 \dots B_m $   

was equivalent to the set of FD’s:

$A_1A_2 \dots A_n \rightarrow B_1, $  $A_1A_2 \dots A_n \rightarrow B_2$,  … , $A_1A_2 \dots A_n \rightarrow B_m$

vice versa.

### 3.2.3 Trivial Functional Dependencies(平凡函数依赖)

A trivial FD has a right side that is a subset of its left side. That is:

The FD’s $A_1A_2 \dots A_n \rightarrow B_1 B_2 \dots B_m $   such that

${B_1 B_2 \dots B_m} \subseteq {A_1A_2 \dots A_n}$ .

For example : $title \: \: year \rightarrow title$   is a trival FD.

### 3.2.4 Computing the Closure (闭包) of Attributes

- {$A_1, A_2, \dots, A_n$} is a set of attributes 
- $S$ is a set of FD’s
- The $closure$ of {$A_1, A_2, \dots, A_n$} under the FD’s in $S$ is the set of attributes B such that every relation that satisfies all the FD’s in set $S$ also satisfies $A_1, A_2, \dots, A_n \rightarrow B$. 
- We denote the closure of a set of attributes  $A_1, A_2, \dots, A_n$ by $\{A_1, A_2, \dots, A_n\}^+$ 

### 3.2.6 The Transitive Rule

- If $A_1A_2 \dots A_n \rightarrow B_1 B_2 \dots B_m$  and $B_1 B_2 \dots B_m \rightarrow C_1 C_2 \dots C_k$  hold in relation R, then $A_1A_2 \dots A_n \rightarrow C_1 C_2 \dots C_k$  also holds in R.

### 3.2.7 Closing Sets of Functional Dependencies

### 3.2.8 Projecting Functional Dependencies

假设我们有关系R和函数依赖集S，当我们计算R的投影$R_1 = \pi_L(R)$后，哪些函数依赖在$R_1$中成立呢？

满足条件的函数依赖需要满足如下性质：

1. Follow from S, and
2. Involve only attributes of $R_1$.

计算 *projection of functional dependencies* 的算法如下：

![image-20230404142024433](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230404142024433.png) 

![image-20230404142034990](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230404142034990.png)

==说明==：对于算法中的*For each set of attributes X that is a subset of $R_1$*, 我们可以对其做以下说明：

1. 计算空集和包含$R_1$ 所有元素的集合的闭包，不会得到任何非平凡的函数依赖
2. 如果我们已经知道一些集合X的闭包是所有$R_1$中的元素，那么我们就不能再找到任何新的FD通过计算X的父集的闭包。

### 3.2.9 Some ==Important== Rules for deducing FD

- *Augmenting left sides*: If $A_1 A_2 \dots A_n \rightarrow B$  is an FD, and $C$ is another attribute, then $A_1 A_2 \dots A_n C \rightarrow B$ follows.

- *Full augmentation.* If $A_1 A_2 \dots A_n \rightarrow B$  is an FD, then *$A_1 A_2 \dots A_n C \rightarrow BC$* follows.

- *Pseudotransitivity.* 

  ![image-20230405184700518](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230405184700518.png)

- Addition.

  ![image-20230405184711568](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230405184711568.png)

## 3.3 Design of Relational Database Schemas

**Careless selection of a relational database schema can lead to redundancy and related anomalies.**

### 3.3.1 Anomalies

1. Redundancy.
2. Update Anomalies.
3. Deletion Anomalies.

### 3.3.2 Decomposing Relations

**The accepted way to eliminate these anomalies is to decompose relations.**

### 3.3.3 Boyce-Codd Normal Form (BCNF)

在BCNF的条件下，上面讨论的三个异常都会被消除。

- A relation R is in BCNF if and only if: whenever there is a nontrivial FD $A_1 A_2 \dots A_n \rightarrow B_1 B_2 \dots B_m$ for R, it is the case that {$A_1 A_2 \dots A_n $} is a superkey for R.

An equivalent statement of the BCNF condition is that the left side of every nontrivial FD must contain a key.

==We can claim that any two-attribute relation is in BCNF.==

### 3.3.4 Decomposition into BCNF

By repeatedly choosing suitable decompositions, we can break any relation schema into a collection of subsets of its attributes with the following important properties:

1. These subsets are the schemas of relations in BCNF.
2. 原来关系中的数据可以从子表中恢复(lossless)

![image-20230405211101356](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230405211101356.png)

## 3.4 Decomposition: The Good, Bad, and Ugly

In this section, we shall consider three distinct properties we would like a decomposition to have.

1. *Elimination of Anomalies* by decomposition.
2. *Recoverability of Information.* Can we recover the original relation from the tuples in its decomposition?
3. *Preservation of Dependencies.*  If we check the projected FD’s in the relations of the decomposition, can we be sure that when we reconstruct the original relation from the decomposition by joining, the result will satisfy the original FD’s?

3.3.4中的算法，BCNF decomposition satisfy (1) and (2), but not necessarily (3).

There is no way to get all three at once.

### 3.4.1 Recovering Information from a Decomposition

每一个two-attribute relation 都处于 BCNF， 那为啥我们不直接把所有的relation都decompose成two-attribute relation 呢？ 因为这样我们不能保证能从分解的relation中还原原来的relation的数据。

If we can get R back, then we say the decomposition has a **lossless join**. 

- If we decompose a relation according to Algorithm in 3.3.4, then the original relation can be recovered exactly by the natural join.

### 3.4.2 The Chase Test for Lossless Join

![image-20230406112831822](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230406112831822.png)

![image-20230406112859450](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230406112859450.png)

![image-20230406112917005](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230406112917005.png)

![image-20230406112933826](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230406112933826.png)

### 3.4.4 Dependency Preservation

## 3.5 Third Normal Form

### 3.5.1 Definition of Third Normal Form

A relation R is in *third normal form(3NF)* if:

- Whenever $A_1 A_2 \dots A_n \rightarrow B_1 B_2 \dots B_m$ is a nontrivial FD, either

  {$A_1 A_2 \dots A_n$}

  is a superkey, or those of $B_1 B_2 \dots B_m$  that are not among the A’s, are each a member of some key (not necessarily the same key).

  An attribute that is a member of some key is often said to be *prime*.



**The 3NF condition can be stated as “for each nontrivial FD, either the left side is a superkey, or the right side consists of prime attributes only.”**

### 3.5.2 The Synthesis Algorithm for 3NF Schemas

We can now explain and justify how we decompose a relation R into a set of relaiotns such that:

1. The relations of the decomposition are all in 3NF.
2. The decomposition has a lossless join.
3. The decomposition has the dependency-preservation property.



![image-20230406132410419](C:\Users\ming\AppData\Roaming\Typora\typora-user-images\image-20230406132410419.png)





















