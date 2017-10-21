+++
date = "2017-05-08T08:22:07+01:00"
draft = false
title = "Matrices and Machine Learning"
image = "/portfolio/matrixAlgebra/MMprinciple.svg.png"
+++

The ubiquity of matrix manipulation in machine learning means that languages like R and Python, which have been optimised for linear algebra, are very well suited to the task of making robots think. It is less clear to me whether Skynet could be written in Golang? 

There are a number of machine learning packages in development but none have the naturalness of an R implementation. To that end I decided to create some of the functionality I need. This is simulataneously a test of creativity and the limits of Go's static typing restriction. To begin with we shall utilise Go's native slice data-structure to implement a matrix multiplication function. This is a little tricky since Go's slice data structure doesn't allow indexing by column, but takes only the row index. So in addition to standard dotproduct operation, we also need a function to extract the column values from each matrix. Consequently, a matrix is an array of row-arrays.

``` go
func dotproduct(input []float64, weights []float64) float64 {
	sum := 0.0
	for i := 0; i < len(input); i++ {
		sum = sum + (input[i] * weights[i])
	}
	return sum
}

func returnCol(array [][]float64, column int) []float64 {
	rowNum := len(array)
	var col []float64
	for i := 0; i < rowNum; i++ {
		col = append(col, array[i][column])
	}
	return col
}


```
We now have what we need to define matrix multiplication in Go.Using the above function we can extract the ith column from any given matrix.

``` go
func mtrxMult(array1 [][]float64, array2 [][]float64) [][]float64 {
	colNum := len(array1[0])
	//fmt.Println("colNum", colNum)
	rowNum := len(array2)
	//fmt.Println("rowNum", rowNum)
	if colNum != rowNum {
		log.Fatal(fmt.Println("Error, column, row dimensions do not match"))
	}

	newRowNum := len(array1)
	newMtrx := make([][]float64, newRowNum)

	if len(array2) == 1 { // dealing with the dotproduct special case
		var row []float64
		row = append(row, dotproduct(returnCol(array1, 0), array2[0]))
		newMtrx[0] = row
		newMtrx = newMtrx[:1]
	} else {
		for i := 0; i < len(array1); i++ {
			var row []float64
			for j := 0; j < len(array2[0]); j++ {
				//fmt.Println(returnCol(array2, j))
				//fmt.Println(array1[i])
				row = append(row, dotproduct(array1[i], returnCol(array2, j)))
				//fmt.Println(dotproduct(returnCol(array1, j), array2[i]))
			}
			newMtrx[i] = row
		}
	}
	return newMtrx
}



```
We can see here that this function constructs the new matrix by (a) ensuring the two input matrices have conformable arguments (b) building the new matrix row by row. We hold the row value fixed and iterate along populating the values until we hit the end, at which point we move to the next row. To populate each element we take the simple dotproduct of the a column from the first matrix and row from the second, so as long as the two matrices have the correct number of dimensions this procedure will work.

Next we make use of this operation to define the LU decomposition algorithm. 

``` go
// MakeZero creates a square zero matrix
func MakeZero(number int) [][]float64 {
	m := make([][]float64, number)
	for i := range m {
		r := make([]float64, number)
		m[i] = r
		for j := range m[i] {
			m[i][j] = 0.0
		}
	}
	return m
}

// Eye creates the identity matrix for a square matrix of size n
func Eye(number int) [][]float64 {
	m := MakeZero(number)
	for i := 0; i < number; i++ {
		m[i][i] = 1.0
	}
	return m
}

// LuDecomp performs the L U decomposition of a square matrix using Crout's method.
func LuDecomp(A [][]float64) (L [][]float64, U [][]float64) {
	n := len(A)
	l := MakeZero(n) // primes l
	u := Eye(n)      // primes u

	l[0][0] = A[0][0]
	for j := 1; j < n; j++ {
		l[j][0] = A[j][0]           // copies remainder of first column into L
		u[0][j] = A[0][j] / l[0][0] // scales first row in U, barring 1st entry
	}
	//fmt.Println(l, u)
	for j := 1; j < n-1; j++ { //encompasses both l and u

		for i := j; i < n; i++ { // populates l from second column onwards
			l[i][j] = A[i][j]
			for k := 0; k <= j-1; k++ {
				l[i][j] = l[i][j] - l[i][k]*u[k][j] //makes use of definition of mtrx multiplication and structure of l

			}
		}

		for k := j + 1; k < n; k++ { // populates u from second row onwards
			u[j][k] = A[j][k]
			for i := 0; i < j; i++ {
				u[j][k] = u[j][k] - l[j][i]*u[i][k] //makes use of definition of mtrx multiplication and structure of u
			}
			u[j][k] = u[j][k] / l[j][j] //scales u
		}
	}
	l[n-1][n-1] = A[n-1][n-1] // sets the nth value of l
	for k := 0; k < n-1; k++ {
		l[n-1][n-1] -= l[n-1][k] * u[k][n-1]
	}

	return l, u
}

```
This algorithm works by decomposing a given matrix into two triangular matrices which can be used to solve a system of linear equations. For the moment we demonstrate the decomposition. 

``` go

func main() {
	A := make([][]float64, 3)
	A[0] = []float64{3, -0.1, -0.2}
	A[1] = []float64{0.1, 7, -0.3}
	A[2] = []float64{0.3, -0.2, 10}

	B := make([][]float64, 3)
	B[0] = []float64{4, 0, 1}
	B[1] = []float64{2, 1, 0}
	B[2] = []float64{2, 2, 3}

	C := make([][]float64, 3)
	C[0] = []float64{1, 3, 4}
	C[1] = []float64{3, 15, 20}
	C[2] = []float64{2, 18, 26}

	D := make([][]float64, 3)
	D[0] = []float64{1, 2, 4}
	D[1] = []float64{3, 8, 14}
	D[2] = []float64{2, 6, 13}

	E := make([][]float64, 2)
	E[0] = []float64{3, 1}
	E[1] = []float64{-6, -4}

	a, b := LuDecomp(C)
	fmt.Println("Lower", a, "Upper", b)
	fmt.Println("Same?", C, MtrxMult(a, b))

	fmt.Println("_____________________")

	l, u := LuDecomp(B)
	fmt.Println("Lower", l, "Upper", u)
	fmt.Println("Same?", B, MtrxMult(l, u))

	fmt.Println("_____________________")

	L, U := LuDecomp(A)
	fmt.Println("Lower", L, "Upper", U)
	fmt.Println("Same?", A, MtrxMult(L, U))

	fmt.Println("_____________________")

	c, d := LuDecomp(D)
	fmt.Println("Lower", c, "Upper", d)
	fmt.Println("Same?", D, MtrxMult(c, d))

	fmt.Println("_____________________")

	e, f := LuDecomp(E)
	fmt.Println("Lower", e, "Upper", f)
	fmt.Println("Same?", E, MtrxMult(e, f))

}


```
