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

The company's problem can then formally be given as $$ a \times b $$


## Hand-coded solution

## Gurobi-based solution

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
