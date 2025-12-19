# Vehicle_Routing_Optimization_SCM_Project
A Python project that uses a Genetic Algorithm to solve the Vehicle Routing Problem by minimizing travel distance and balancing customer demand with vehicle capacity. It outputs optimized routes, total distance, and fitness evolution.
# 🚚 Vehicle Routing Optimization using Genetic Algorithm (VRP-GA)

This repository contains an implementation of the **Vehicle Routing Problem (VRP)** using a **Genetic Algorithm**. The objective is to minimize the total distance traveled by multiple vehicles while serving customers with varying demands, subject to vehicle capacity constraints.

---

## 📌 Key Features

- Genetic Algorithm based optimization
- Customer demand splitting support
- Chromosome representation for routes
- Legitimacy validation of chromosomes
- Fitness evaluation using total route distance
- Single-point crossover operator
- Swap mutation operator
- Elitism for population preservation
- Automatic repair for invalid chromosomes
- Termination based on fitness stagnation
- Route distance calculation with depot return
- Graph plotting of **Fitness vs Generations**

---

## 🧠 Problem Overview

Given:
- A set of customers with coordinates and demand
- A fleet of vehicles with limited capacity

Goal:
- Assign and route vehicles to serve all customers
- Minimize total travel distance
- Ensure no vehicle exceeds load capacity
- Avoid duplicate or missing customer assignments

This project addresses the NP-hard VRP using evolutionary computation.

---

## 🏗️ Algorithm Approach

### Genetic Algorithm Components

| Stage | Description |
|-------|-------------|
| Initialization | Random valid chromosome generation |
| Fitness | `1 / total_distance` |
| Selection | Elitism + random parent selection |
| Crossover | Single point |
| Mutation | Swap mutation |
| Repair | Correct invalid or duplicate assignments |
| Termination | Stagnation based (`max_unchanged`) |

---

## 📂 Project Structure

