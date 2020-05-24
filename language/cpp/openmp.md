# Openmp



## enviroment variable

1. export OMP\_NUM\_THREADS=4: will run for four thread
2. OMP\_STACKSIZE
3. OMP\_WAIT\_POLICY
4. OMP\_PROC\_BIND

## include file

1. include  : include file that needed

## functions:

1. omp\_get\_thread\_num\(\) : get the thread id num
2. omp\_set\_num\_threads\(int\) : set threads number

## pragma

### pragma omp parallel

under block will doing parallel process

### pragma omp barrier

Each thread waits until all threads arrive.

```text
#pragma omp parallel
{
  int id=omp_get_thread_num();
  A[id] = big_calc1(id);
#pragma omp barrier
  B[id] = big_calc2(id, A);
}
```

### pragma omp critical

Mutual exclusion: Only one thread at a time can enter a critical region.

```text
float res;
#pragma omp parallel
{ 
  float B; int i, id, nthrds;
  id = omp_get_thread_num();
  nthrds = omp_get_num_threads();
  for(i=id;i<niters;i+=nthrds){
    B = big_job(i);
#pragma omp critical
    res += consume (B);
  }
}
```

### pragma omp atomic

provides mutual exclusion but only applies to the update of a memory location

```text
#pragma omp parallel
{
  double tmp, B;
  B = DOIT();
  tmp = big_ugly(B);
#pragma omp atomic
  X += tmp;
}
```

The statement inside the atomic must be one of the following forms:

* x binop= expr
* x++
* ++x
* x—
* --x

  X is an lvalue of scalar type

  and binop is a non-overloaded

  built in operator.

  **pragma omp matser**

  ```text
  #pragma omp parallel
  {
  do_many_things();
  #pragma omp master
  { exchange_boundaries(); }
  #pragma omp barrier
  do_many_other_things();
  }
  ```

  **pargma omp single**

  ```text
  #pragma omp parallel
  {
  do_many_things();
  #pragma omp single
  { exchange_boundaries(); }
  do_many_other_things();
  }
  ```

  **pragma omp sections**

  ```text
  #pragma omp parallel
  {
  #pragma omp sections
  {
  #pragma omp section
    X_calculation();
  #pragma omp section
    y_calculation();
  #pragma omp section
    z_calculation();
  }
  }
  ```

  **simple locks**

  ```text
  #pragma omp parallel for
  for(i=0;i<NBUCKETS; i++){
  omp_init_lock(&hist_locks[i]); hist[i] = 0;
  }
  #pragma omp parallel for
  for(i=0;i<NVALS;i++){
  ival = (int) sample(arr[i]);
  omp_set_lock(&hist_locks[ival]);
  hist[ival]++;
  omp_unset_lock(&hist_locks[ival]);
  }
  for(i=0;i<NBUCKETS; i++)
  omp_destroy_lock(&hist_locks[i]);
  ```

  **runtime library routines**

  ```text
  #include <omp.h>
  void main()
  { int num_threads;
  omp_set_dynamic( 0 );
  omp_set_num_threads( omp_num_procs() );
  #pragma omp parallel
  { int id=omp_get_thread_num();
  #pragma omp single
  num_threads = omp_get_num_threads();
  do_lots_of_stuff(id);
  }
  }
  ```

## Data envitoment

### Shared Memory programming model:

Most variables are shared by default

### Global variables are SHRED among thresds

### But not everything is shared

```text
double A[10];
int main() {
int index[10];
#pragma omp parallel
work(index);
printf(“%d\n”, index[0]);;
}
extern double A[10];
void work(int *index) {
double temp[10];
static int count;
...
}
```

A, index, count are sharedm but temp is not shared.

### Data sharing: private clause

* private\(var\) creates a new local copy of var for each thread.
  * The value of the private copies is uninitialized
  * The value of the original variable is unchanged after the region

```text
void wrong() {
int tmp = 0;
#pragma omp parallel for private(tmp)
for (int j = 0; j < 1000; ++j)
tmp += j;
printf("%d\n", tmp);
}
```

### Firstprivate Clause

* Variables initialized from shared variable
* C++ objects are copy-constructed

  ```text
  incr = 0;
  #pragma omp parallel for firstprivate(incr)
  for (i = 0; i <= MAX; i++) {
  if ((i%2)==0) incr++;
  A[i] = incr;
  }
  ```

  **Lastprivate Clause**

* Variables update shared variable using value from last iteration
* C++ objects are updated as if by assignment

  ```text
  void sq2(int n, double *lastterm)
  {
  double x; int i;
  #pragma omp parallel for lastprivate(x)
  for (i = 0; i < n; i++){
  x = a[i]*a[i] + b[i]*b[i];
  b[i] = sqrt(x);
  }
  *lastterm = x;
  }
  ```

## eliminate False ng problem

1. Use sum\[NUM\_THREADS\]\[PAD\] : the second arguments will eliminate the cache problem.

