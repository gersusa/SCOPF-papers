
```
              Copyright 2019 / GersUSA
```

# Instructions of SCOPF algortihm with MatLab (Matpower)


This code can achieve quick answers to the SCOPF problem.

# Quick start :

First, you can download the code files in the internal folder: 
D:\Google Drive\GERS\ARPA-E\FINAL-FINAL
Or contact to e-mails:
- diego.rodriguez@gers.com.co
- dario.arango@gers.com.co
- daniel.agudelo@gers.com.co


**Requirements**
Remember previously you must have installed:
- MATPOWER7.0. You can download it at: https://matpower.org/download/  .
- IPOPT compilated (mexa file). The instructions for installation can be found: https://coin-or.github.io/Ipopt/ for linux or windows      distributions.
- Download the repository files (FINAL_FINAL-master.zip). Unzip the folder and after add it at the MatLab PATH.
Verify that you have the **matLab parallel computing toolBox** on the contrary some modification must be realized in differents scripts.




**Input data format:**
 
 The input data network (case) must contain 4 files in format of PSSE:

- case.raw:  It file has the power flow data : Bus Data, Load Data, Generator Data etc.
- case.inl:  It file has the data of generators with  nonzero generator participacion factors. (Power limits, inertia data).
- case.rop:  It file has the data of generators cost.
- case.con:  It file has the set of contingencies of lines, transformers and generators.



To call myMatLab1 function, first make sure to locate the **.raw, .rop, .inl and .con files** in the  working directory.

**Input variables :**
- TimeLimit:  Is the available time in seconds to get a solution of SCOPF in the base case.
- Scoring Method: 1 for a real time approach, 2 for a extended time approach.

A parpool must be created to improve the algorithm performance.  The number of workers is a fundamental factor tho achieve a  good solution in the time "TimeLimit". If you need a good response and you have few workers and a great contingencies number you should consider a good TimeLimit.

```
                           _ _ _ _ _ _ _ _ _ _ _ _ _ _  _ 
                        |                                  |    
  case.raw              |                                  |        
  case.inl              |                                  |        
  case.rop  --------->  |          MyMatLab1.m             | -----> mpcOPF  
  case.con  --------->  |                                  | -----> solution1.txt
  TimeLimit             |                                  | -----> mpc.mat
  ScoringMethod         |                                  |   
                        |  _ _ _ _ _ _ _ _ _ _ _ _ _ _  _  | 
                        
                        
```


For example, with a timeLimit of 600 seconds  and a scoringMethod=1 (Real-Time approach) execute:

```
%%%%%% MATLAB CODE  %%%%%%%%%%

//Create a parallel pool
if isempty(gcp('nocreate'))
    parpool;
end
InFile2 = 'case.inl';
InFile4 = 'case.rop';
InFile1 = 'case.con';
InFile3 = 'case.raw';
MyMatlab1(InFile1, InFile2, InFile3, InFile4, 600, 1, '')

%%%%%%%  WAIT TIME   %%%%%%%%%%%

```

As output a mpc.mat file save the base case solution (mpcOPF). To get some results you can
apply the MatPower syntax, for example:

```
%%%%%%% MATLAB CODE %%%%%%%%
load('mpc.mat')
///mpcOPF will appear in you workspace
minCost=mpcOPF.      // objective fucntion in the base case
Pg=mpcOPF.gen(:,2)   // active power generated
Qg=mpcOPF.gen(:,3)   // reactive power generated
Vm=mpcOPF.bus(:,9)   // voltage magnitudes
VA=mpcOPF.bus(:,10)  // voltages angles
%%%%%%%  WAIT TIME   %%%%%%%%%

```

"solution1.txt" must be appear in your current folder. It contains the base case solution information.
If you want the post-contingency solution for each contingency, you must execute myMatLab2.m file.

```
                         _ _ _ _ _ _ _ _ _ _ _ _ _ _ _  _ _
                        |                                  | 
                        |                                  |    
  case.raw              |                                  |        
  case.inl  --------->  |                                  | -----> mpc.mat
  case.rop  --------->  |           MyMatLab2.m            | -----> mpcOPF  
  case.con  --------->  |                                  | -----> solution1.txt
                        |                                  | 
                        |                                  |   
                        | _ _ _ _ _ _ _ _ _ _ _ _ _ _ _  _ | 
```

```
%%%%%%% MATLAB CODE %%%%%%%%
//Create a parallel pool
if isempty(gcp('nocreate'))
    parpool;
end
InFile2 = 'case.inl';
InFile4 = 'case.rop';
InFile1 = 'case.con';
InFile3 = 'case.raw';
// create solution1.txt : Solution in the base case
MyMatlab1(InFile1, InFile2, InFile3, InFile4, 600, 1, '')
// create solution2.txt : Set a solutions in postContingencies scenarios. 
MyMatlab2(InFile1, InFile2, InFile3, InFile4, TimeLimitInSeconds, ScoringMethod, NetworkModel)
%%%%%%%  WAIT TIME   %%%%%%%%%
```
"solution2.txt" must be appear in your current folder. It contains the solution for  each contingency.


# Main functions descriptions:

- **MyMatLab1.m** : This script generates solution1.txt. Internally it calls the functions **convert2mpc**,**solveSCOPF** and **createSolution1.m** functions.
- **convert2mpc.m** : This script take the  **.raw, .rop, .inl** and **.con**  as input files and create a matpower case mpc as output file. 
- **solveSCOPF.m** : This script take a mpc file as input and execute the main algorithm. for the base case execute a extended OPF and after call the **runALLCONS.m** function to evaluate the set of contingencies. With the results update the constraints bounds for reduce the violation number in the next iteration.
- **runALLCONS.m** : Evaluate the base case in each contingency according active and reactive andredispatch of  rules. After detect constraints violations, non convergences and penalizations cost. With this values the  base case constraints are updated.   
- **MyMatLab2.m** : This script generates solution2.txt taking as input the solution1.



Each function has its own description., you can see it writing:  

help **function_name** 

In the MatLab command window.



# Contact 
For more information about the code contact:
E-mail:
- diego.rodriguez@gers.com.co
- dario.arango@gers.com.co
- daniel.agudelo@gers.com.co


 
 
                        

