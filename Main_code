import random
import math
import matplotlib.pyplot as plt

def split_customer_demand(customer_id, demand, capacity):
    splits = []
    while demand > capacity:
        splits.append((f"{customer_id}p{len(splits)+1}", capacity))
        demand -= capacity
    if demand > 0:
        splits.append((f"{customer_id}p{len(splits)+1}", demand))
    return splits

def get_customer_parts(customers, demands, capacity):
    customer_parts = []
    demand_parts = []
    for c, d in zip(customers, demands):
        if d > capacity:
            splits = split_customer_demand(c, d, capacity)
            for label, part_demand in splits:
                customer_parts.append(label)
                demand_parts.append(part_demand)
        else:
            customer_parts.append(str(c))
            demand_parts.append(d)
    return customer_parts, demand_parts

def build_demands_dict(customers, demands, capacity):
    demands_dict = {}
    for c, d in zip(customers, demands):
        if d > capacity:
            splits = split_customer_demand(c, d, capacity)
            for label, part_demand in splits:
                demands_dict[label] = part_demand
        else:
            demands_dict[str(c)] = d
    return demands_dict

def generate_random_chromosome(customer_parts, demand_parts, vehicles, capacity):
    unassigned = list(zip(customer_parts, demand_parts))
    random.shuffle(unassigned)
    vehicles_left = vehicles[:]
    random.shuffle(vehicles_left)
    chromosome = []
    used_vehicles = set()
    i = 0
    n = len(unassigned)
    while i < n and vehicles_left:
        v = vehicles_left.pop()
        used_vehicles.add(v)
        group = []
        load = 0
        while i < n and load + unassigned[i][1] <= capacity:
            group.append(unassigned[i][0])
            load += unassigned[i][1]
            i += 1
        if group:
            chromosome.extend(group)
            chromosome.append(str(v))
    if i < n:
        return None
    for v in vehicles:
        if v not in used_vehicles:
            chromosome.append(str(v))
    return chromosome

def parse_chromosome(chromosome, vehicles):
    vehicle_set = set(str(v) for v in vehicles)
    assignments = {v: [] for v in vehicles}
    i = 0
    n = len(chromosome)
    while i < n:
        group = []
        while i < n and chromosome[i] not in vehicle_set:
            group.append(chromosome[i])
            i += 1
        if i < n and chromosome[i] in vehicle_set:
            v = int(chromosome[i])
            assignments[v].extend(group)
            i += 1
    return assignments

def is_legitimate_chromosome(chrom, customer_parts, vehicles, demands_dict, capacity):
    assigned_customers = set()
    assignments = parse_chromosome(chrom, vehicles)
    for v, cust_list in assignments.items():
        total_load = 0
        for cust in cust_list:
            if cust not in customer_parts or cust in assigned_customers:
                return False
            assigned_customers.add(cust)
            total_load += demands_dict[cust]
        if total_load > capacity:
            return False
    if assigned_customers != set(customer_parts):
        return False
    if len(chrom) != len(set(chrom)):
        return False
    if not all(str(v) in chrom for v in vehicles):
        return False
    return True

def calculate_distance(coord1, coord2):
    return math.sqrt((coord1[0] - coord2[0])**2 + (coord1[1] - coord2[1])**2)

def calculate_vehicle_distances(assignments, customers, demands, vehicles, coordinates, depot=(0, 0)):
    cust_part_coords = {}
    for i, c in enumerate(customers):
        cust_part_coords[str(c)] = coordinates[i]
    for i, c in enumerate(customers):
        for part_num in range(2, 20):
            cust_part_coords[f"{c}p{part_num}"] = coordinates[i]
    vehicle_distances = {}
    for v, cust_list in assignments.items():
        if not cust_list:
            vehicle_distances[v] = 0.0
            continue
        total_dist = 0.0
        prev_point = depot
        for cust in cust_list:
            total_dist += calculate_distance(prev_point, cust_part_coords[cust])
            prev_point = cust_part_coords[cust]
        total_dist += calculate_distance(prev_point, depot)
        vehicle_distances[v] = total_dist
    return vehicle_distances

def single_point_crossover(parent1, parent2):
    length = len(parent1)
    if length < 2:
        return parent1[:], parent2[:]
    point = random.randint(1, length - 2)
    child1 = parent1[:point] + parent2[point:]
    child2 = parent2[:point] + parent1[point:]
    return child1, child2

def swap_mutation(chrom):
    c = chrom[:]
    idx1, idx2 = random.sample(range(len(c)), 2)
    c[idx1], c[idx2] = c[idx2], c[idx1]
    return c

def repair_chromosome(chrom, customer_parts, vehicles):
    seen = set()
    new_chrom = []
    for gene in chrom:
        if gene not in seen:
            new_chrom.append(gene)
            seen.add(gene)
    all_numbers = set(customer_parts + [str(v) for v in vehicles])
    missing = all_numbers - set(new_chrom)
    new_chrom += list(missing)
    return new_chrom[:len(customer_parts) + len(vehicles)]

def fitness_function(total_distance):
    if total_distance == 0:
        return float('inf')  # Best possible
    return 1 / total_distance

def store_best_chromosomes(generation, chromosome_fitness, best_chromosomes):
    best = chromosome_fitness[0]
    best_chromosomes.append({
        'generation': generation,
        'chromosome': best['chromosome'],
        'fitness': best['fitness']
    })

def plot_fitness_vs_generation(best_chromosomes):
    generations = [entry['generation'] for entry in best_chromosomes]
    fitness_values = [entry['fitness'] for entry in best_chromosomes]
    plt.figure(figsize=(10, 6))
    plt.plot(generations, fitness_values, marker='o', linestyle='-', color='b')
    plt.title('Best Fitness Value vs Generation')
    plt.xlabel('Generation')
    plt.ylabel('Best Fitness Value')
    plt.grid(True)
    plt.tight_layout()
    plt.show()

def genetic_algorithm():
    num_customers = int(input("Enter the number of customers: "))
    num_vehicles = int(input("Enter the number of vehicles: "))
    customers = []
    demands = []
    coordinates = []
    for i in range(1, num_customers + 1):
        demand = int(input(f"Enter demand for customer {i}: "))
        x = float(input(f"Enter x-coordinate for customer {i}: "))
        y = float(input(f"Enter y-coordinate for customer {i}: "))
        customers.append(i)
        demands.append(demand)
        coordinates.append((x, y))
    vehicles = [i + num_customers for i in range(1, num_vehicles + 1)]
    truck_capacity = int(input("Enter the load carrying capacity of each truck: "))
    population_size = int(input("Enter the population size: "))
    max_unchanged = int(input("Enter the number of generations with unchanged fitness before stopping: "))

    crossover_prob = float(input("Enter crossover probability (e.g., 0.8): "))
    elitism_rate = float(input("Enter elitism rate (e.g., 0.9): "))
    mutation_prob = float(input("Enter mutation probability (e.g., 0.2): "))

    customer_parts, demand_parts = get_customer_parts(customers, demands, truck_capacity)
    demands_dict = build_demands_dict(customers, demands, truck_capacity)

    population = []
    attempts = 0
    while len(population) < population_size and attempts < population_size * 100:
        chrom = generate_random_chromosome(customer_parts, demand_parts, vehicles, truck_capacity)
        if chrom and is_legitimate_chromosome(chrom, customer_parts, vehicles, demands_dict, truck_capacity):
            if chrom not in population:
                population.append(chrom)
        attempts += 1

    if len(population) < population_size:
        print("Could not generate enough legitimate chromosomes for initial population.")
        return

    best_chromosomes = []
    unchanged_count = 0
    prev_best_fitness = None
    generation = 0

    while unchanged_count < max_unchanged:
        generation += 1
        chromosome_fitness = []
        for chrom in population:
            assignments = parse_chromosome(chrom, vehicles)
            vehicle_distances = calculate_vehicle_distances(
                assignments, customers, demands, vehicles, coordinates
            )
            total_distance = sum(vehicle_distances[v] for v in vehicles)
            fitness = fitness_function(total_distance)
            chromosome_fitness.append({
                'chromosome': chrom,
                'assignments': assignments,
                'vehicle_distances': vehicle_distances,
                'total_distance': total_distance,
                'fitness': fitness
            })
        chromosome_fitness.sort(key=lambda x: x['fitness'], reverse=True)
        population = [cd['chromosome'] for cd in chromosome_fitness]

        store_best_chromosomes(generation, chromosome_fitness, best_chromosomes)

        current_best_fitness = chromosome_fitness[0]['fitness']
        if prev_best_fitness is not None and abs(current_best_fitness - prev_best_fitness) < 1e-10:
            unchanged_count += 1
        else:
            unchanged_count = 1
            prev_best_fitness = current_best_fitness

        elitism_count = int(population_size * elitism_rate)
        next_population = population[:elitism_count]
        prev_generation = population[:]

        while len(next_population) < population_size:
            if random.random() < crossover_prob:
                parent1, parent2 = random.sample(population, 2)
                child1, child2 = single_point_crossover(parent1, parent2)
            else:
                child1, child2 = random.sample(population, 2)
            if random.random() < mutation_prob:
                child1 = swap_mutation(child1)
            if random.random() < mutation_prob:
                child2 = swap_mutation(child2)
            child1 = repair_chromosome(child1, customer_parts, vehicles)
            child2 = repair_chromosome(child2, customer_parts, vehicles)

            if is_legitimate_chromosome(child1, customer_parts, vehicles, demands_dict, truck_capacity):
                if child1 not in next_population:
                    next_population.append(child1)
            if len(next_population) < population_size:
                if is_legitimate_chromosome(child2, customer_parts, vehicles, demands_dict, truck_capacity):
                    if child2 not in next_population:
                        next_population.append(child2)

        seen = set(tuple(chrom) for chrom in next_population[:elitism_count])
        unique_population = next_population[:elitism_count]
        i = elitism_count
        while i < len(next_population):
            chrom_tuple = tuple(next_population[i])
            if chrom_tuple not in seen:
                unique_population.append(next_population[i])
                seen.add(chrom_tuple)
            else:
                replaced = False
                for candidate in prev_generation[elitism_count:]:
                    cand_tuple = tuple(candidate)
                    if cand_tuple not in seen:
                        unique_population.append(candidate)
                        seen.add(cand_tuple)
                        replaced = True
                        break
            i += 1
        while len(unique_population) < population_size:
            candidate = random.choice(prev_generation[elitism_count:])
            cand_tuple = tuple(candidate)
            if cand_tuple not in seen:
                unique_population.append(candidate)
                seen.add(cand_tuple)
        population = unique_population[:population_size]

    chromosome_fitness = []
    for chrom in population:
        assignments = parse_chromosome(chrom, vehicles)
        vehicle_distances = calculate_vehicle_distances(
            assignments, customers, demands, vehicles, coordinates
        )
        total_distance = sum(vehicle_distances[v] for v in vehicles)
        fitness = fitness_function(total_distance)
        chromosome_fitness.append({
            'chromosome': chrom,
            'assignments': assignments,
            'vehicle_distances': vehicle_distances,
            'total_distance': total_distance,
            'fitness': fitness
        })

    chromosome_fitness.sort(key=lambda x: x['fitness'], reverse=True)

    print("\nRanked Chromosomes by Fitness (Reciprocal of Total Distance):")
    for rank, chrom_info in enumerate(chromosome_fitness, 1):
        print(f"\nRank {rank}:")
        print(f"  Chromosome: {chrom_info['chromosome']}")
        for v in vehicles:
            print(f"    Vehicle {v} serves: {chrom_info['assignments'][v]}")
        print(f"  Total Distance: {chrom_info['total_distance']:.2f}")
        print(f"  Fitness Value: {chrom_info['fitness']:.6f}")
        for v in vehicles:
            print(f"    Vehicle {v} distance: {chrom_info['vehicle_distances'][v]:.2f}")

    print("\nCustomer Coordinates:")
    for i, coord in enumerate(coordinates, 1):
        print(f"Customer {i}: {coord}")

    print("\nBest Chromosome and Fitness Value of Each Generation:")
    for entry in best_chromosomes:
        print(f"Generation {entry['generation']}: Chromosome = {entry['chromosome']}, Fitness = {entry['fitness']:.6f}")

    # Plot the fitness vs. generation graph
    plot_fitness_vs_generation(best_chromosomes)

if __name__ == "__main__":
    genetic_algorithm()
