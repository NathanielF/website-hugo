+++
date = "2017-05-08T08:22:07+01:00"
draft = false
title = "Matrices and Machine Learning"
image = "/portfolio/matrixAlgebra/MMprinciple.svg.png"
+++

The ubiquity of matrix manipulation in machine learning means that languages like R and Python, which have been optimised for linear algebra, are very well suited to the task of making robots think. It is less clear to me whether Skynet could be written in Golang? There are a number of machine learning packages in development but none have the naturalness of an R implementation. To that end I decided to create some of the functionality I need. This is simulataneously a test of creativity and the limits of Go's static typing restriction.

To begin with we shall utilise Go's native slice data-structure to implement a matrix multiplication function. This is a little tricky since Go's slice data structure doesn't allow indexing by column, but takes only the row index. So in addition to standard dotproduct operation, we also need a function to extract the column values from each matrix. Consequently, a matrix is an array of row-arrays.

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
We can see here that this function constructs the new matrix by (a) ensuring the two input matrices have conformable arguments (b) building the new matrix row by row. We hold the row value fixed and iterate along populating the values until we hit the end, at which point we move to the next row. To populate each element we take the simple dotproduct of the a column from the first matrix and row from the second, so as long as the two matrices have the correct number of dimensions this procedure will work. Consider:

``` go

	A := make([][]float64, 3) // 3 X 2
	A[0] = []float64{3, 2}
	A[1] = []float64{7, 6}
	A[2] = []float64{7, 6}

	B := make([][]float64, 2) //2 X 3
	B[0] = []float64{1, 2, 3}
	B[1] = []float64{3, 4, 1}

	C := make([][]float64, 1)
	C[0] = []float64{2, 2, 2} // 1 X 3

	D := make([][]float64, 3) //  3 X 2
	D[0] = []float64{1, 1}
	D[1] = []float64{1, 2}
	D[2] = []float64{1, 1}


func printMatrix(matrix [][]float64) {
	for i := 0; i < len(matrix); i++ {
		fmt.Println("m", i, ":", matrix[i], "\n")
	}
	return
}

printMatrix(mtrxMult(B, D))   //   m 0: [6 8]
                              //   m 1: [8 12]

printMatrix(mtrxMult(C, A))   // m 0: [34, 28]

```
