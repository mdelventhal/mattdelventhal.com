---
title: Optimal Routing with Mixed Integer Programming
summary: 'An interactive solution to a tricky Mixed Integer Programming problem, with a comparison to a Gurobi-based solution.'
tags:
- 
date: "2022-05-19T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: ""

image:
  caption: Photo by rawpixel on Unsplash
  focal_point: Smart
  preview_only: true

links:
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
# slides: example
---

The problem seems simple: a telecommunications firm has jobs that must be completed, and must assign them to its available technicians. Not all technicians are qualified to do all jobs, they are based out of depots a certain distance away from the jobs, and each job has a deadline.

Below, we will formulate this as a Mixed Integer Programming problem with the objective of minimizing lateness and, if possible, completing all the jobs. We will then demonstrate both custom, hand-coded solution, and a solution designed to use industry-leading MIP package Gurobi. Finally, we will present the hand-coded version as a live, interactive app, which will allow you to edit the list of jobs and see how the algorithm performs.

The original version of this problem is borrowed from a Gurobi example application, which you can find [here](https://gurobi.github.io/modeling-examples/technician_routing_scheduling/technician_routing_scheduling.html "Technician Routing and Scheduling Problem").

## Problem details

A telecom firm has a list jobs to be completed in a given day. The jobs are of different types, and each type has a priority: urgent equipment repairs are higher priority than routine repairs. Each customer is located in a particular city, has a windown of time in which they would prefer the job to be started, and deadline for the job to be completed.

To complete each job, one qualified technician must travel to the customer location. Each technician is qualified to do some types of jobs and not others. They must begin and end their day in their home depot which is located in a particular city. Each technician has a maximum number of allocated work hours for the day. The total time spent traveling and working on jobs cannot exceed this number.

The company would like to start all jobs in the specified window and complete all jobs on time, if possible. If this is not possible, the company prefers to "triage" the situation in the following order:
 1. First, complete as many jobs as possible. Skip low-priority jobs before high-priority jobs.
 2. Second, deviate as little as possible from the customer's preferred arrival time, especially for higher-priority jobs.
 3. Third, minimize late completions of jobs, especially for higher-priority jobs.

## Formal specification

### Time and Space

Suppose that the planning day starts at time 0 and ends after $T$ minutes. There are a set of $N$ locations which are the sites of customers and technician depots. Let $\tau_{mn}$ represent the travel time in minutes from location $m$ to location $n$ for $m,n \in \\{1,2,..., N\\}$.

### Jobs

There are $J$ jobs to be completed. Each job $j \in \\{1, 2, ..., J\\}$ has a priority level $\pi_j$ and requires $p_j$ minutes to be completed. Each customer has specified that they would prefer work to begin between time $a_j$ and $b_j$, and that the job should be fully complete by time $c_j$. Let the location of each job be given by $L_j \in \\{1,2,...,N\\}$.

### Technicians

There are $K$ technicians available for work. Each must begin and end his or her work day at their "home" depot, located at $O_k \in \\{1,2,...,N\\}$ for $k \in \\{1,2,...,K\\}$. Total time spent traveling and working for technician $k$ may not exceed $W_k$ minutes. Each technician is qualified to do some jobs, and may be unqualified to do others: let $q_{jk}$ be an indicator function, taking a value of 1 if technician $k$ is qualified to do job $j$, and taking a value of 0 otherwise.

### Company's problem

The company must choose which technicians will do which jobs and in which order they will do them. The order of the jobs defines a route that the technician will travel, from the depot to the first job, then to the second and so on, and finally returning from the last job to the depot. Additinally, the company must specify a start time for each job.

The company wishes to minimize total weighted lateness, out-of-bounds start times, and no-shows. The weight the company places on each minute of lateness is normalized to 1, and multiplied by the priority $\pi_j$ for each job. The company places a weight $C_0 \times \pi_j$ on each minute that a job $j$'s start time is outside the customer's preferred window, and values the cost of not completing job $j$ at all at $C_1 \times \pi_j$.

Let $x_{jkr}$ be an indicator variable taking a value of 1 if job $j$ is the $r^{\text{th}}$ job which the company chooses to assign to technician $k$, and zero otherwise. For example, if technician 1 is assigned two jobs, first job $7$, and then job $3$, then $x_{711} = 1$ and $x_{312} = 1$, while $x_{j1r} = 0$ for all other combinations of $j$ and $r$. Let $t_j \in \[0,T\]$ represent the start time that the company chooses for job $j$.

For ease of presentation, let us also define $z_j \equiv \sum\limits_{r = 1}^J \sum\limits_{k = 1}^{K} x_{jkr}$. Notice that if the job $j$ is assigned in any order to any technician, $z_j$ will take a value of 1; but it will take a value of 0 otherwise. Thus, $z_j$ serves as an indicator of whether the job will be completed or not.

The company's problem can then formally be given as $$ \min\limits_{x_{jkr} ~ , ~ t_j} \left \\{   \sum\limits_{j=1}^J \pi_j \times \left \[ \begin{array}{l} \left ( 1 - z_j  \right ) ~ \times \overbrace{C_1}^{\begin{array}{c}\text{penalty} \\\\ \text{for} \\\\ \text{no-show} \end{array}} + \\\\ \\\\   \quad + ~ z_j \times \left (  \begin{array}{l} \small \quad \quad \underbrace{\max \\{ 0 ~ , ~ t_j + p_j - c_j \\}}\_{\begin{array}{c} \text{lateness} \end{array}} \\\\ \small + ~ C_0 \cdot \underbrace{\max \\{ 0 ~ , ~ a_j - t_j \\}}\_{\begin{array}{c} \text{start} \\\\ \text{too early} \end{array}} \\\\ \small + ~ C_0 \cdot \underbrace{ \max \\{ 0 ~ , ~ t_j - b_j \\}}\_{\begin{array}{c} \text{start} \\\\ \text{too late} \end{array}}   \end{array} \right )  \end{array} \right \] \right \\}, $$

subject to the following constraints:

1. **At most one technician per job:** $$ \begin{flalign}\sum\limits_{r = 1}^J \sum\limits_{k = 1}^{K} & x_{jkr}  \leq 1  \\\\ & \quad \quad {\small  \text{ for }  j \in \\{1,2,...,J\\}} \end{flalign} $$
2. **Technicians not assigned if not qualified:** $$\begin{flalign} \left (\sum\limits_{r = 1}^J x_{jkr} \right )& \cdot \left ( 1 - q_{jk} \right ) = 0 \\\\ & \quad \quad {\small \text{ for } j,k \in \\{1,2,...,J \\} \times \\{1,2,...,K \\} } \end{flalign} $$
3. **For each technician, order of jobs is assigned sequentially with no gaps:** $$ \sum\limits_{j=1}^J x_{jkr} \leq \left \\{ \begin{array}{l l} 1 & {\small \text{for }  r = 1 , k \in \\{ 1,2,...,K \\} } \\\\ \\\\ \sum\limits_{j=1}^J x_{jk,r-1} & {\small \text{for } r \in \\{2, 3, ..., J\\}, k \in \\{ 1,2,...,K \\} } \end{array} \right. $$
4. **Job starts allow sufficient time to arrive at job site from previous location:** $$ \begin{flalign}t_j &\leq \sum\limits_{k = 1}^K x_{jk0} \tau_{ \small O_k,L_j} + \sum\limits_{r=2}^J \sum\limits_{i = 1}^J \sum\limits_{k=1}^K x_{jkr} x_{ik,r-1} \left \[ t_i + p_i + \tau_{\small L_i,L_j}  \right \] \\\\ \\\\ & \quad \quad {\small \text{ for } j \in \\{1,2,...,J \\} } \end{flalign}$$
5. **No technician works longer than time allocation:** $$ \begin{flalign}\sum\limits_{r = 1}^J \sum\limits_{j = 1}^{J} & x_{jkr} p_j + \sum\limits_{j = 1}^{J}  x_{jko} \tau_{\small O_k, L_j }  + \sum\limits_{r=2}^{J} \sum\limits_{i=1}^J \sum\limits_{j=1}^J x_{ik,r-1} x_{jk,r} \tau_{\small L_i, L_j} \\\\
&  \quad + \sum\limits_{r=1}^{J-1} \sum\limits_{j = 1}^J \left ( \sum\limits_{i=1}^J  x_{ikr} \right ) \cdot \left (1 - \sum\limits_{i=1}^J x_{ik,r+1} \right ) \cdot x_{jkr} \tau_{\small L_j, O_k}  \\\\ & \quad + \sum\limits_{j=1}^J x_{jkJ} \tau_{\small L_j,O_k}   \leq w_k \\\\ \\\\ & {\small \text{ for } k \in \\{1, 2, ..., K \\} } \end{flalign}$$

## Solution approaches

### Features of this problem that make it tricky

This problem contains a mixture of discrete decision variables (whom to send to each job, in which order) with continuous variables (what exact time to start each job). The presence of discrete decisions is what makes this and other Mixed-Integer-Programming problems relatively difficult to solve: the number of permutations of possible combinations of discrete choices will be massive even for a relatively small-scale problem. This means that a brute-force approach which blindly explores all the permutations will probably be computationally infeasible even for a toy problem.

We should also note that the objective function is not continuously differentiable in the continuous choice variables. This means that any simple derivative-based approach to dealing with this part of the problem will also likely fail.

There are two practical approaches that may be useful in solving this kind of problem:
1. Formulate a customized approach that exploits simplifying features specific to the structure of this problem.
2. Formulate the problem in a way that it can be fed into a general MIP solver such as Gurobi, which has been optimized to automatically exploit features that commonly occur in the structure of problems of this general type.

In what follows, we will try both.

### Hand-coded solution

In our custom algorithm, the decision process will be explicitly broken down into three stages:
1. The company chooses which technicians do which jobs.
2. For each technician assigned more than one job, the company chooses which order to do the jobs in.
3. Given the assignment and order of the jobs, the compnay chooses the start time for each.

We will solve the problem starting at stage 3 and working backwards. When determining the choices or stage 1, we will use a classic "branch and bound" approach, in which entire branches of possible combinations of decisions are iteratively ruled out based on the current "best-yet" objective function value.

Three key feature we will exploit are:
1. For a given ordering of jobs, there are only a limited number of start times for each job that are likely to be optimal. This allows us to treat each continuous time choice as just another discrete choice with a limited number of possible values, and avoid possibly unreliable non-derivative-based continuous optimization algorithms.
2. For a given allocation of jobs, the contribution of each technician to the objective function is independent of each other. This will mean that while working backwards through the problem, we only have to solve each stage once.
3. The objective function has a hard minimum: zero, when all jobs are completed with no lateness or start window misses. Therefore, if our algorithm finds at least one optimal solution, we will tell it to stop--no need to look further. 

The following are the specific steps the algorithm takes:
1. **Calculate the partial independent contribution of each possible assignment of jobs, in each possible ordering, for each technician.** 
  - **a.** For each combination and ordering of jobs, check first if it will satisfy the total work time constraint for the technician. If not, skip it.
  - **b.** If a combination and ordering satisfies the total work time constraint, cycle through the relevant permutations of possible starting times in order.
  - **c.** For each job, try at most 7 possible starting times: (1) as early as possible, (2) at the beginning of the "start window", (3) at the tail end of the "start window," (4) just in time to finish the job at the due time, (5) just in time for the following job to start at the beginning of its "start window," (6) just in time for the following job to start at the end of its "start window," (7) just in time for the following job to finish just in time, at its due time. There are many cases where some of these are not relevant and are skipped. For example, if a technician is only assigned one job, it is unnecessary to try more than one start time: that which allows her to finish right on time, or as close to it as possible.
  - **d.** For each combination of assigned jobs, discard all but the one with the lowest partial contribution to the objective function. If there is more than one such ordering, keep only the first one found--ordering is irrelevant to the optimality of the assignments of other technicians.
2. **Perform an undirected search through the possible combinations of job assignments to technicians:**
  - **a.** Cycle through technicians one at a time making assignments.
  - **b.** Use the already-prepared dictionary of partial contributions to the objective function to calculate total objective function value.
  - **c.** If at some point when only some technicians have received an assignment, the accumulated objective value exceeds the best-yet objective function value, abort, and skip all permutations involving the already-made assignments. This is the "branch and bound" part of the algorithm.
  - **d.** Count any left-over jobs as unfilled, and add to objective function accordingly.

The part of this algorithm which probably has the most room for improvement is step 2: specifically, the undirected nature of the search. However, for a problem instance like the one we will see below, which is relatively small-scale and has plenty of slack in the technician roster, it performs quite well.

### Gurobi-based solution

The problem as specified above is already nearly ready to be introduced to the Gurobi solver. All that remains is to define some auxiliary variables:
  - $X_{ijkr}^0 \equiv x_{ikr} x_{jkr}$
  - $X_{ijkr}^1 \equiv x_{ik,r-1} x_{jkr}$
  - $\theta_{ikr} \equiv \left \\{ \begin{array}{l l} \sum\limits_{j = 1}^J \left ( X_{ijk,r+1}^0  \right )  \left (x_{ikr}  \sum\limits_{j=1}^J X_{ijkr}^1  \right ) & {\small \text{ for } r \in \\{1, 2, ..., R-1\\} \\\\ x_{ikr} & {\small \text{ for } r = R  }}  \end{array} \right.$

This is necessary because Gurobi does not allow for more than two decision variables to be multiplied together in any constraint or in the objective function.

The main complication in implementing it is that some auxiliary variables must be defined to make the constraints compatible with Gurobi's particular requirements. For example, it is not possible to pass any constraint in which more than two decision variables are multiplied together

## An example problem instance

The same one given in the original Gurobi demonstration problem.
### Hand-coded performance

### Gurobi performance

## Discussion

## Live interactive problem instance

Modify the job list and see how it works for yourself!

### We're testing out markdown.

And some more text.

<iframe height="1100" width="95%" frameborder="no" src="https://share.streamlit.io/mdelventhal/telecommute_viz/main/telecommute_viz.py"> </iframe>
