This is an exercise in performance optimization on heterogeneous Intel
architecture systems based on multi-core processors and manycore (MIC)
coprocessors.

NOTE: this  lab follows the discussion  in Section 4.7.1 and  4.7.2 in
the book  "Parallel Programming and  Optimization with Intel  Xeon Phi
Coprocessors",  second edition  (2015). The  book can  be obtained  at
xeonphi.com/book

In  this  step,  we  will  look  at how  to  load-balance  in  an  MPI
application running  on a  heterogeneous cluster. The  provided source
code is  a Monte-Carlo  simulation on Asian  Options Pricing.  For the
purposes of this exercise, the actual implementation of the simulation
is not important, however those if you are interested in learning more
about the simulation itself refer to the Colfax Research website.

0.  Study  "workdistribution.cc" and  compile  it.  Then run  the  MPI
   application across all the nodes available to you (including MICs),
   with one process on each node.
   
   You  should  see  that  there is  load-imbalance,  where  one  node
   finished faster than others.

1.  A simple  solution  to this  load balance  is  to distribute  work
   unevenly  depending  on  the  target  system.  Implement  a  tuning
   variable "alpha" (should be typefloat or double) where the workload
   MIC receives  is alpha times  the workload the CPU  receives.  Each
   node shpould calculate which options to work on. To do this use the
   function input "rankTypes",  which stores the type (CPU  or MIC) of
   all nodes in the world. "rankTypes[i]"  is "1" if the rank "i" node
   is on  a coprocessor, and "0"  if it is  on a CPU. Make  sure every
   option is accounted for.

   Compile and run the application. Then try to find the "alpha" value
   that provides the best performance.

2. The previous implementation, although  simple to implement, has the
   drawback that the alpha value will  be dependent on the cluster. To
   make  the  application  independent  of the  cluster  it  runs  on,
   implement boss-worker model  in which the boss assigns  work to the
   workers as the workers completes them.
   
   Compile and run the code to see the performance. Remember that node
   that has the boss proccess should have 2 processes.

   Hint:  To implement  th  boss worker  model, you  will  need an  if
         statement with two while loops  in it. The worker loop should
         send it's  rank to the  host, and  receive the index  that it
         needs to  calculate.  The  host should use  MPI_ANY_SOURCE in
         it's receive  for the rank,  and send  the next index  to the
         worker rank that it received.  When there are no more options
         to be  simulated, the  boss should  send a  "terminate" index
         (say index of -1). When  the worker receives this "terminate"
         index it  should exit the  while loop.  The host  should exit
         the while loop when "terminate"  has been sent to every other
         process. Finally, don't forget  to have MPI_Barrier in before
         the MPI_Reduce to make sure all processes are done before the
         reduction happens.
   
    



