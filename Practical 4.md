#include<stdio.h>
#include<omp.h>
double fibbo_serial(int n) { 
    double a = 0, b = 1, temp;
    
    double start = omp_get_wtime();
    for (int i = 0; i < n; i++) {
        temp = a + b;
        a = b;
        b = temp;
    }
    double end = omp_get_wtime();
    
    printf("Execution Time Serial: %f seconds\n", end - start);
	return end-start;
}
double fibbo(int n){
	double a = 0;
	double b = 1;
	 double temp;
	double start = omp_get_wtime();
	#pragma omp parallel
	{
	#pragma omp for private(temp)
	for(int i = 0;i<n;i++){
	
	#pragma omp critical
	{	
	temp = a+b;
	a=b;
	b=temp;
	}

	}

	}
	double end = omp_get_wtime();
	printf("Executuion time Parallel: %f Seconds\n",end-start);

	return end-start;
}

int main(){
	double tserial,tparallel;
	tparallel = fibbo(10000000);
	tserial = fibbo_serial(10000000);
	printf("Speedup = %f", tserial/tparallel);


return 0;
}
