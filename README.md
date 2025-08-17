Budget Optimization in Healthcare Scenario
Abstract

This report presents a mixed-integer programming approach to optimize staff assignments, patient scheduling, and resource allocation in a healthcare setting under budget constraints. We formulate a binary linear model minimizing total costs (staff wages, patient scheduling costs, resource usage, and penalties) subject to meeting time-slot demand, staffing limits, and a budget cap. The model is implemented in Python using the Pyomo framework and solved with the GLPK solver. The optimal solution achieves a total cost of 5452.0 and assigns all patients and resources while satisfying staffing demand. Key results and tables are provided, and the performance and implications of the solution are discussed. The methodology and findings illustrate the efficacy of mathematical optimization for healthcare scheduling.

Introduction

Healthcare systems must efficiently allocate limited resources (personnel, equipment, time) while maintaining high-quality patient care
mdpi.com
mdpi.com
. In particular, scheduling staff and allocating resources are critical challenges: staff shortages, complex shift requirements, and patient demand fluctuations all contribute to increasing costs and waiting times
mdpi.com
mdpi.com
. Optimization methods, especially mixed-integer linear programming (MILP), have been widely proposed to address such scheduling problems
mdpi.com
mdpi.com
. For example, Yinusa and Faezipour (2023) developed a MILP model including staff assignments, patient assignments, and resource allocation to minimize healthcare expenditures while satisfying demand
mdpi.com
mdpi.com
. Our work follows this paradigm, formulating an MILP that simultaneously schedules employees, patients, and resources under a budget constraint.

Problem Description

The problem considers a healthcare facility with a set of employees, patients, resources, and discrete time slots. Key input data include:

Employees (j=1…30) with cost 
𝑈
𝑗
,
𝑡
U
j,t
	​

 for assigning employee j in time slot t, maximum hours 
𝐻
𝑗
H
j
	​

, and overtime cost weight 
𝑌
𝑗
Y
j
	​

.

Patients (q=1…200) with cost 
𝑉
𝑞
,
𝑡
V
q,t
	​

 for scheduling patient q at time t and penalty 
𝑋
𝑞
,
𝑡
X
q,t
	​

 if not scheduled.

Resources (s=1…15) with cost 
𝑊
𝑠
,
𝑡
W
s,t
	​

 for allocating resource s at time t.

Time slots (t=1…8) with a staff demand 
𝑍
𝑡
Z
t
	​

 per slot.

Budget (B) limiting total staff and resource expenditure.

The goal is to assign employees and resources to time slots and to schedule each patient to one time slot such that all staff demands 
𝑍
𝑡
Z
t
	​

 are exactly met, each patient is served exactly once, and total staffing/resource cost does not exceed the budget. The objective is to minimize total cost, including staff assignment costs (
𝑈
𝑗
,
𝑡
U
j,t
	​

), patient scheduling costs (
𝑉
𝑞
,
𝑡
V
q,t
	​

), resource allocation costs (
𝑊
𝑠
,
𝑡
W
s,t
	​

), penalties (
𝑋
𝑞
,
𝑡
X
q,t
	​

), and overtime costs (
𝑌
𝑗
Y
j
	​

). All employees and resources are binary-decision (assigned or not in a slot), and patients are assigned exactly once. This problem is a combinatorial optimization (MILP) that incorporates operational constraints and a global budget limit.

Methodology

We formulate a binary integer programming model using the Pyomo optimization library
optimization-online.org
. The decision variables are:

𝑎
𝑗
,
𝑡
∈
{
0
,
1
}
a
j,t
	​

∈{0,1} = 1 if employee j works in time slot t,

𝑏
𝑞
,
𝑡
∈
{
0
,
1
}
b
q,t
	​

∈{0,1} = 1 if patient q is scheduled at time t,

𝑐
𝑠
,
𝑡
∈
{
0
,
1
}
c
s,t
	​

∈{0,1} = 1 if resource s is allocated at time t,

𝑑
𝑗
∈
{
0
,
1
}
d
j
	​

∈{0,1} = 1 if employee j works overtime (exceeds 
𝐻
𝑗
H
j
	​

).

The objective function minimizes total cost:

min
⁡
∑
𝑗
,
𝑡
𝑈
𝑗
,
𝑡
 
𝑎
𝑗
,
𝑡
+
∑
𝑞
,
𝑡
𝑉
𝑞
,
𝑡
 
𝑏
𝑞
,
𝑡
+
∑
𝑠
,
𝑡
𝑊
𝑠
,
𝑡
 
𝑐
𝑠
,
𝑡
+
∑
𝑞
,
𝑡
𝑋
𝑞
,
𝑡
 
𝑏
𝑞
,
𝑡
+
∑
𝑗
𝑌
𝑗
 
𝑑
𝑗
.
min
j,t
∑
	​

U
j,t
	​

a
j,t
	​

+
q,t
∑
	​

V
q,t
	​

b
q,t
	​

+
s,t
∑
	​

W
s,t
	​

c
s,t
	​

+
q,t
∑
	​

X
q,t
	​

b
q,t
	​

+
j
∑
	​

Y
j
	​

d
j
	​

.

This sums staff wages, patient assignment costs, resource costs, penalties (for scheduling patients) and any overtime costs. The rationale for this linear objective is that it captures all costs linearly, enabling use of efficient MILP solvers.

Subject to this objective, the model imposes the following linear constraints:

Staff Demand: For each time slot t, the total employees assigned equals demand: 
∑
𝑗
𝑎
𝑗
,
𝑡
=
𝑍
𝑡
∑
j
	​

a
j,t
	​

=Z
t
	​

.

Working Hours: Each employee j works at most 
𝐻
𝑗
H
j
	​

 slots: 
∑
𝑡
𝑎
𝑗
,
𝑡
≤
𝐻
𝑗
∑
t
	​

a
j,t
	​

≤H
j
	​

.

Patient Scheduling: Each patient q is assigned exactly once: 
∑
𝑡
𝑏
𝑞
,
𝑡
=
1
∑
t
	​

b
q,t
	​

=1.

Resource Allocation: Each resource s is allocated exactly once: 
∑
𝑡
𝑐
𝑠
,
𝑡
=
1
∑
t
	​

c
s,t
	​

=1.

Overtime Definition: Overtime flag for employee j enforces 
𝑑
𝑗
≥
∑
𝑡
𝑎
𝑗
,
𝑡
−
𝐻
𝑗
d
j
	​

≥∑
t
	​

a
j,t
	​

−H
j
	​

. This forces 
𝑑
𝑗
=
1
d
j
	​

=1 whenever hours exceed the maximum.

Budget Constraint: Total staff wage, overtime, and resource costs do not exceed the budget:

∑
𝑗
,
𝑡
𝑈
𝑗
,
𝑡
𝑎
𝑗
,
𝑡
+
∑
𝑗
𝑌
𝑗
𝑑
𝑗
+
∑
𝑠
,
𝑡
𝑊
𝑠
,
𝑡
𝑐
𝑠
,
𝑡
  
≤
  
𝐵
.
j,t
∑
	​

U
j,t
	​

a
j,t
	​

+
j
∑
	​

Y
j
	​

d
j
	​

+
s,t
∑
	​

W
s,t
	​

c
s,t
	​

≤B.

The optimization model is implemented with Pyomo
optimization-online.org
, which allows symbolic definition of variables, objective, and constraints in Python. We use the open-source GLPK solver to find an optimal solution. GLPK is designed for large-scale LP/MIP problems and employs methods such as the simplex algorithm and branch-and-bound with cutting planes
en.wikipedia.org
, making it suitable for this MILP. In practice, solving is straightforward: the model is instantiated with the data (from CSV files) and solved via SolverFactory('glpk'). The solver returns the optimal binary assignments 
𝑎
,
𝑏
,
𝑐
,
𝑑
a,b,c,d if one exists.

Experimental Setup

The model uses empirical data for costs and constraints, loaded from provided CSV files. Specifically, matrices of dimensions 30×8 for 
𝑈
𝑗
,
𝑡
U
j,t
	​

 (employee costs), 200×8 for 
𝑉
𝑞
,
𝑡
V
q,t
	​

 (patient costs) and 
𝑋
𝑞
,
𝑡
X
q,t
	​

 (penalties), and 15×8 for 
𝑊
𝑠
,
𝑡
W
s,t
	​

 (resource costs) are read in. Vectors of length 30 define overtime weights 
𝑌
𝑗
Y
j
	​

 and hour limits 
𝐻
𝑗
H
j
	​

; a length-8 vector gives staff demand 
𝑍
𝑡
Z
t
	​

; and the budget 
𝐵
B is a scalar. We set up these parameters in a Pyomo ConcreteModel and define decision variables and constraints as above.

The computations were carried out in a Python environment (e.g. Google Colab) with Pyomo and the glpsol executable. After solving, the optimal value of the objective function and the selected assignments were extracted. Solution output includes the total cost (5452.0) and lists of assignments for employees, patients, and resources. For clarity, we record the key outputs (assignments) in CSV tables. Each employee’s schedule, each patient’s slot, and each resource’s slot are obtained from the binary solutions.

Results

The MILP solver found an optimal schedule meeting all constraints. The total objective cost is 5452.0. Key summary statistics of the solution are:

Metric	Value
Employees	30
Time slots	8
Patients	200
Resources	15
Employee assignments	126
Patient assignments	200
Resource allocations	15
Total cost (min)	5452.0

Each patient is scheduled exactly once (200 patient assignments), and each of the 15 resources is allocated to one slot. All 8 time slots have their staffing demand exactly met with a total of 126 employee-slot assignments across all employees. For example, Employee 1 is assigned to slots {2,3,5,6,7}, while Employee 30 works slots {2,6,7,8} (as seen in the solution output). No employee was required to exceed their maximum hours: the overtime indicator 
𝑑
𝑗
d
j
	​

 is 0 for all j, so no overtime cost was incurred.

The budget constraint is respected, meaning the sum of wages and resource costs remains within the specified limit. Because each patient is required to be scheduled exactly once, no “unserved patient” penalties are incurred; the penalty matrix 
𝑋
𝑞
,
𝑡
X
q,t
	​

 thus effectively contributes only to scheduling costs rather than penalizing any unscheduled patient. The solver output confirms that under the chosen budget, all staffing demands are met and all patients are served in the cost-minimal way.

Discussion

The optimization produced a feasible schedule that precisely satisfies staffing requirements while minimizing cost. By construction, the number of staff in each slot equals the demand 
𝑍
𝑡
Z
t
	​

. The model’s linear objective guided the solution to use the cheapest combination of assignments: it likely placed patients in their lowest-cost slots 
𝑉
𝑞
,
𝑡
V
q,t
	​

 subject to staff availability, and allocated each resource to the cheapest slot 
𝑊
𝑠
,
𝑡
W
s,t
	​

. Because the model forces each patient to be scheduled, the solution avoids any unsatisfied demand for patient service (so the penalty costs 
𝑋
𝑞
,
𝑡
X
q,t
	​

 did not actively contribute to extra expense).

From a budget perspective, the constraint ensured cost control: any higher-cost assignment (such as hiring additional staff or using overtime) was only allowed if within budget. In this solution, the budget appears to have been sufficient to meet all demands without triggering overtime. If the budget were tighter, the model might force trade-offs (e.g. serving fewer patients or reducing staffed slots), but in our case, full service was achieved.

The use of Pyomo and GLPK effectively handled this moderate-size MILP (approximately 240 employee-slot variables, 1600 patient-slot variables, etc.). GLPK’s branch-and-bound algorithm with cuts
en.wikipedia.org
 solved the model to optimality. The zero optimality gap (reported by GLPK) indicates an exact solution. In practice, this demonstrates that even for several thousand binary variables, modern MILP solvers can compute optimal schedules efficiently.

As a limitation, this model assumes every patient must be served exactly once; in reality, some patients might be optional or have multiple potential time slots, which would change the formulation. Moreover, parameters like costs and demands were assumed deterministic and known. Future work could extend this framework to include stochastic arrivals, variable patient demand, or preferences.

Conclusion

We developed and solved an MILP to optimize healthcare scheduling under budget limits. Using Pyomo
optimization-online.org
 for model formulation and GLPK
en.wikipedia.org
 as the solver, we obtained a cost-minimal assignment: all patient appointments and resource allocations were made while meeting staff demand and not exceeding the budget. The model’s solution had total cost 5452.0 and no overtime usage. This confirms that integer programming is a viable tool for such allocation problems. Future extensions could relax the “all patients served” assumption or integrate uncertainty, but the present model provides a clear and exact solution under the given data and constraints. The results highlight the effectiveness of formal optimization in strategic healthcare resource planning.
