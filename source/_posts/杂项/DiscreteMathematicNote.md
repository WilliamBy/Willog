---
categories:
  - 杂项
abbrlink: b8e16674
toc: true
tags:
  - Math
---
# Discrete MatheMatic Note 离散数学笔记
Author: BAIDI
Date: from 2020-2-19

## 1 Logic and Proofs 逻辑和证明

### 1.1 Propositional Logic
- connectives 逻辑联结词
negation
conjunction(合取) disjunction(析取) --inclusive/exclusive ..
conditional statement/biconditional statement

### 1.2 Applications of Propositional Logic
1. System Specification 系统规范说明
2. Boolean Logic 布尔逻辑
3. Logic Puzzles 逻辑谜题
4. Logic Circuits 逻辑电路

### 1.3 Propositional Equivalences
1. tautology, contradiction, contingency
2. logically equivalent ≡ ⇔
3. Law
- De Morgan's Law
¬(p ∧ q) ≡ ¬p ∨ ¬q
¬(p ∨ q) ≡ ¬p ∧ ¬q
- other Law(check relative table)
¬p ∨ q and p → q
- Distribute Law
 p ∨ (q ∧ r) ≡ (p ∨ q) ∧ (p ∨ r)
4. Law's extension
5. Law's application
6. Proposition's Satisfiability and Problem's Solution
7. Soduka Puzzle's solution

### 1.4 Predicates and Quantifiers 谓词和量词
#### Predicates 谓词
> basic notion:Propositional function P (命题函数P)
> n-place / n-ary predicates (n位/元谓词) 包含多个变量的谓词
> P(x1, x2...xn) --谓词+变量=命题
> Application--verify the correctness of a programme:
> Precondition(input) & Postcondition(output expected) (前置条件 & 后置条件）
#### Quantifiers 量词
Notions:
1. Quantification 量化
2. Universal quantifier 全称量词
3. domain: domain of discourse 论域(universe of discouse 全体域)
4. counterexample 反例
5. Existential quantifier 存在量词
6. Precedence 优先级
7. Resticted Domains 约束域 (约束域和条件，合取的等价关系)
8. bound (被赋值或被量词联结）-绑定 scope -作用域 free -自由(未绑定的变量)
#### Logic Equivalences Involving Quantifiers
Notions:
1. comma 逗号
2. Prolog --programming in logic
3. Prolog fact / Prolog rule
3. semicolon 分号
### Nested Quantifiers 嵌套变量
### Rules of inference 推理规则
- argument 论证
- valid 有效性
- premise 前提
- fallacy 谬误
- argument form 推理形式
- modus ponens 假言推理 （law of detachment 分离规则）
- hypothesis 假设
- syllogism 三段论
- contrapositive 逆否命题
- instantiation 实例
- arbitrary 任意的
### Itroduction to Proofs 
- theorem 定理 axiom 公理 lemmma 引理
- corollary 推论 conjecture 猜想
- vacuous proof 空证明
- trivial proof 平凡证明