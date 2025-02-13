#include <stdio.h>
#include <omp.h>

#define N 40  // Change for testing

int fibonacci_serial(int n) {
    if (n <= 1) return n;
    return fibonacci_serial(n - 1) + fibonacci_serial(n - 2);
}

int fibonacci_parallel(int n) {
    if (n <= 1) return n;

    int x, y;

    #pragma omp task shared(x)
    { x = fibonacci_parallel(n - 1); }

    #pragma omp task shared(y)
    { y = fibonacci_parallel(n - 2); }

    #pragma omp taskwait
    return x + y;
}

int main() {
    double start, end;

    // Serial Execution
    start = omp_get_wtime();
    int fib_serial = fibonacci_serial(N);
    end = omp_get_wtime();
    printf("Serial Fibonacci(%d) = %d\n", N, fib_serial);
    printf("Serial Time: %f sec\n\n", end - start);

    // Parallel Execution
    start = omp_get_wtime();
    int fib_parallel;

    #pragma omp parallel
    {
        #pragma omp single
        fib_parallel = fibonacci_parallel(N);
    }

    end = omp_get_wtime();
    printf("Parallel Fibonacci(%d) = %d\n", N, fib_parallel);
    printf("Parallel Time: %f sec\n", end - start);

    return 0;
}
=======================================================================================================================================


#include <stdio.h>
#include <omp.h>

#define BUFFER_SIZE 5
#define NUM_ITEMS 10

int buffer[BUFFER_SIZE];
int count = 0;  // Number of items in buffer

void producer() {
    for (int i = 0; i < NUM_ITEMS; i++) {
        while (count == BUFFER_SIZE);  // Wait if buffer is full

        #pragma omp critical  // Ensure only one thread modifies buffer
        {
            buffer[count] = i;
            printf("Produced: %d\n", i);
            count++;
        }
    }
}

void consumer() {
    for (int i = 0; i < NUM_ITEMS; i++) {
        while (count == 0);  // Wait if buffer is empty

        #pragma omp critical
        {
            count--;
            printf("\tConsumed: %d\n", buffer[count]);
        }
    }
}

int main() {
    #pragma omp parallel sections
    {
        #pragma omp section
        producer();

        #pragma omp section
        consumer();
    }

    return 0;
}

