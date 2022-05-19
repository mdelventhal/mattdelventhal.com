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
