# A Differential Evolution Algorithm with side-objective function

<p align="center">
  <img width="700" src="assets/download.jpg" >
</p>

In this Kernel we modify the DE algorithm codes developed previously [here](https://pablormier.github.io/2017/09/05/a-tutorial-on-differential-evolution-with-python/#). 
The modified DE Algorithm now can be used by taking multi-objectve function into account.

Steps taken are as follow:

* The main objective function (fobj) and side objective function are defined.
* The DE Algorithm written by Pablo R. Mier is modified and includes the new finction called generate candidate.
* The generate candidate function takes the main objective function (fobj) and side objective function and the boundries into account to generates candidates.
* The generate candidate function consideres the side objective function along with maximum and minimum limitations to filter the candidates 
(See this following line in the generates function).
```python 
if side_obj_1_fitness[0] < 1000 and side_obj_1_fitness[0] > 900: 
```  

* Evaluate the side objective function and filter those candidates that are not satisfied the side_objective function. 
* Here we use side objective function and we want to make sure that the DE performs evolution on those candidates that is between 900 and 1000 values generated by side objective function.

```python
def generate_candidate(fobj, side_obj_1, bounds):
    
        cond = True
        while cond == True:

            dimensions = len(bounds)
            pop = np.random.rand(1, dimensions)
            min_b, max_b = np.asarray(bounds).T
            diff = np.fabs(min_b - max_b)
            pop_denorm = min_b + pop * diff
            # calculate the fitness using fitness function and save it into the fitness parameter
            fitness = np.asarray([fobj(ind) for ind in pop_denorm])
            # calculate the side objective function and save it into the side object_1 parameter
            side_obj_1_fitness = np.asarray([side_obj_1(ind) for ind in pop_denorm])
            #evaluate the side objective function and filter those candidates
            #that are not satisfied the side_objective function.
            # Here we use side objective function and we want to make sure that the DE performs evolution on those candidates 
            # that is between 900 and 1000 values generated by side objective function.
            if side_obj_1_fitness[0] < 1000 and side_obj_1_fitness[0] > 900:
                cond = False
    #             print('candidate = ', pop_denorm)

    #     print('fitness = ', fitness[0]), print('side_obj_1_fitness = ', side_obj_1_fitness[0])
        return pop[0], pop_denorm[0] 
  ```
  
  * The De Algorithm modified evaluate the new candidates fitness taking both main and side objective functions into account. 
  Check out the line  in the main code.
  ```python 
  fitness = np.asarray([fobj(ind) for ind in pop_denorm])
  #Sub_Objective function are evaluated at the following.
  side_obj_1_fitness = np.asarray([side_obj_1(ind) for ind in pop_denorm])
  ```
 * A new funtion (gene_trial_in_range) is defined which generates the trial candidates in predeterimend ranges as follow.
 ```python
 def gene_trial_in_range(popsize, j, mut, dimensions, crossp, pop, min_b, diff):
    
    cond = True
    while cond == True:
        
        idxs = [idx for idx in range(popsize) if idx != j]
        a, b, c = pop[np.random.choice(idxs, 3, replace = False)]
        mutant = np.clip(a + mut * (b - c), 0, 1)
        cross_points = np.random.rand(dimensions) < crossp
        if not np.any(cross_points):
            cross_points[np.random.randint(0, dimensions)] = True

        trial = np.where(cross_points, mutant, pop[j])
        trial_denorm = min_b + trial * diff
        cond = False

    return trial_denorm, trial
  ```
 
 * You may run the program and see the results (res) as follow).
 ```
 (array([[9.71347573],
        [9.69980814],
        [9.72554979],
        [9.69181503],
        [9.67379587]]),
 array([47.1758054 , 47.04313899, 47.29315934, 46.96563926, 46.79116331]),
 4)
 ```
  
  




