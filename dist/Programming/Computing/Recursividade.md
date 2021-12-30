# Estrutura

Você pega um problema, reduz ele até chegar em sua menor forma (caso base) e a partir disso trata o pré e pós recursão.

````c
// without recursion
int sum(int a[],int n) {	
	int sum = 0;
	for(int i =0; i<n; i++) {
		sum+= a[n];
	}
	return sum;
}

// with recursion
int sum(int a[],int n) {
	// caso base
	if(n == 0 ) {
		return 0;
	}
	return a[n-1] + sum(a,n-2);
}

````

Porém iterar sobre um vetor e fazer a soma dos valores é trivial, quando tentamos passar um método de ordenação para ser recursivo as coisas complicam.

````C
// without recursion
void selection_sort(int num[], int tam)
{
    int i, j, min, aux;
    for (i = 0; i < (tam - 1); i++)
    {
        min = i;
        for (j = (i + 1); j < tam; j++)
        {
            if (num[j] < num[min])
                min = j;
        }
        if (i != min)
        {
            aux = num[i];
            num[i] = num[min];
            num[min] = aux;
        }
    }
}

//with recursion
int minValueOfVector(int a[], int length) {
	if(length== 1) {
		return 0;
	}
	// a+1 represents an array omiting the first value

	minValueOfVector(a+1, n-1);

	if(a[0] < a[m]){
		return 0;
	}

	return m;
}

void insertion(int a[], int length) {
	if(length <= 1) {
		return; 
	}
	int minValue = minValueOfVector(a, length);

	int swap = a[0];
	a[0] = a[m];
	a[m] = tmp;

	selection( a+1, length - 1);
}
````

A implementação com a recursão talvez seja um pouco maior, porém ela serve para mostrar que é possível resolver o mesmo problema de uma outra maneira que talvez seja até mais intuitiva que a sem recursão.
