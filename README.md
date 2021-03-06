# CONTACT PROCESS
### GIADA PALMA - VR471280
Project for the probability and statistics course (Master's Degree Data Science)

___

The contact process is a discrete Markov chain with state space *S := {0,1}*. *x ∈ S*. 
So if *xi = 1* we say vertex *i* is *infected*, if *xi = 0* we say vertex *i* is *susceptible*.

The function below returns the index of the neighbours of the current vertex.
```python

def return_neighbour_index(target_index, population):   
    if target_index == 0:
        target_neighbour_index = 1

    elif target_index == len(population) - 1:
        target_neighbour_index = target_index  - 1

    else:
        if np.random.rand() < 0.5:
            target_neighbour_index = target_index + 1
        else:
            target_neighbour_index = target_index - 1
    
    return target_neighbour_index 
```



The function below transforms all infected vertices of a population to susceptible. 
It returns the *T* (i.e. the time steps needed to reach the absorbing state were all vertices are suceptible).
```python

def return_single_time_value(population_graph, lambda_input):
    time_to_completion = 0

    while sum(population_graph) > 0:
        target_index = randrange(len(population_graph))
        target_neighbour_index = None
        
        target_neighbour_index = return_neighbour_index(target_index, population_graph)
        
        if population_graph[target_index] == 1:
            probability = lambda_input / (1 + lambda_input)
            if np.random.rand() < probability:
                population_graph[target_neighbour_index] = 1
            else:
                population_graph[target_index] = 0
        time_to_completion += 1
    #print("MC",time_to_completion) #Debugging
    return time_to_completion 
```



By the law of large numbers *T^* is an estimate for the expected value of *T*.
The function below returns *T^*.
```python
def return_average_time_steps(lambda_input, number_of_runs):    
    total_time_steps = 0

    for i in range(0, number_of_runs):
        total_time_steps += return_single_time_value(np.array([1,1,1,1,1,1,1,1,1,1]), lambda_input)
    #print("Total",total_time_steps) #Debugging
    
    average = total_time_steps / number_of_runs
    return average
```


___

## SIMULATING WITH INCREASING λ

The function below simulates the process by running it a specified number of times, with *λ* increasing.
It returns two arrays:
- of *λ* values to be plotted on the x axis  
- of *T* value for each *λ* calculated from the runs of the simulation
```python
def simulate(number_of_runs):

    simulations = np.array([])
    array_of_lambdas = np.array([])
    lambda_index = 0.1

    while lambda_index <= 1:
        simulations = np.append(simulations, return_average_time_steps(lambda_index, number_of_runs))
        lambda_index += 0.1
        array_of_lambdas = np.append(array_of_lambdas, lambda_index)

    return simulations, array_of_lambdas
```


The result is the plot below


![Graph for increasing lambda](Increasing_lambda.png)

As we can see *T^* depends on *λ*:
- for *λ* small (i.e. 0.1 < *λ* < 0.9) *T^* is also small
- as soon as *λ* crosses the critical value *λc* (i.e. roughly 0.9) *T^* starts growing faster.



___

## SIMULATING WITH INCREASING NUMBER OF VERTICES

The function below simulates the process by running it a specified number of times, with the number of vertices increasing.
It returns two arrays:
- of population sizes (i.e. number of vertices) to be plotted on the x axis
- of T value for each run with a different population size
```python
def simulate_pop_size(lambda_input, number_of_runs, pop_size):
    simulations = np.array([])
    individuals = np.array([])

    population_size = return_population(pop_size)

    while pop_size > 2:
        simulations = np.append(simulations, return_average_time_steps(lambda_input, number_of_runs, population_size))
        individuals = np.append(individuals, pop_size)
        pop_size -= 250
        population_size = return_population(pop_size)
        
    return simulations, individuals
```

The result can be seen in the plots below

![Graph for increasing population size](Population_size_100.png)

![Graph for increasing population size](Population_size_1000.png)

![Graph for increasing population size](Population_size_5000.png)

As we can see *T^* depends not only on *λ* being larger or smaller than the critical value, but also on the number of vertices.
In fact with a large number of vertices, also a larger number of *T^* is needed to reach the absorbing state.





