---
title: "The KNN algorithm"
author: "Nathaniel Forde"
date: "2017-05-26T08:22:07+01:00"
draft: no
type: post
image: "/portfolio/knn/knn.png"
---
In this post we will implement the K-Nearest Neighbour classification algorithm. The idea is simply stated that "we are the company we keep". The algorithm surveys an entire known population and compares each candidate to that population to determine where (along a series of metrics) that candidate best fits. Once we have ascertained a measure of fit we further identify the candidate with the most common class of the members of the population nearest to them in the population. At the crudest level of interpretation you can think of this algorithm as making a judgement based on stereotype. If you live in the wealthy neighborhood, you're probably wealthy and conversely if you're from the poor neighborhood you're probably poor. 

One of the challenges implementing this reasoning in Golang comes from the fact that our functions are staticly typed and in principle the algorithm is data-type agnostic. We can compare different populations using different distance measures. So for instance if we wanted to compare text snippets we could use hamming's distance measure, while if we're comparing numeric data euclid's measure of distance is more appropriate. However I have yet to find an elegant way to write a data-type agnostic knn algorithm without recycling code. We elaborate below: 

As normal we begin with defining a series of helper functions:

``` go
package main

import (
	"encoding/csv"
	"fmt"
	"log"
	"math"
	"math/rand"
	"os"
	"sort"
	"strconv"
)

func dotproduct(input []float64, weights []float64) float64 {
	sum := 0.0
	for i := 0; i < len(input); i++ {
		sum = sum + (input[i] * weights[i])
	}
	return sum
}

func norm(input []float64) float64 { //analogue of line length in multi-dimensional setting
	sumOfsquare := 0.0
	for i := 0; i < len(input); i++ {
		sumOfsquare += input[i] * input[i]
	}
	return math.Sqrt(sumOfsquare)
}

func euclidistance(input []float64, input2 []float64) float64 {
	p := norm(input2) * norm(input2)
	q := norm(input) * norm(input)
	l := p + q - 2*dotproduct(input2, input)
	return math.Sqrt(l)
}

func hamming(string1 []string, string2 []string) float64 {
	dist := 0.0
	for i := 0; i < len(string1); i++ {
		if string1[i] != string2[i] {
			dist++
		}
	}
	return dist
}

```
Now since we will be comparing each new datapoint to an entire population we will define a structure on which to record our collected measures. Crucially, we will need to rank and sort these measurements so we need to define a comparison measurement on this data structure. Fortunately we can use the fact that Golang's sort functions are well defined on any structure which exhibits methods of swapping, ordering and length measurement. For these are the fundamental operations required to sort any list. 


``` go
type ordererdPair struct {
	number float64
	class  string
}
type sliceOfOrderedPair []ordererdPair

func (s sliceOfOrderedPair) Len() int      { return len(s) }
func (s sliceOfOrderedPair) Swap(i, j int) { s[i], s[j] = s[j], s[i] }
func (s sliceOfOrderedPair) Less(i, j int) bool {
	return s[i].number < s[j].number
}

```
With this in place we have ensured that our sliceofOrderedPair data-type satisfies the sort-interface. 


``` go
func comparison(candidate []float64, population [][]float64, popClass []string) sliceOfOrderedPair {
	//fmt.Println(candidate)
	//fmt.Println(population)
	var unSorted sliceOfOrderedPair
	//fmt.Println(unSorted)
	for j := 0; j < len(population); j++ {
		dist := euclidistance(candidate, population[j])
		class := popClass[j]
		pair := ordererdPair{dist, class}
		unSorted = append(unSorted, pair)
	}
	//fmt.Println(unSorted)
	sort.Sort(unSorted)
	sorted := unSorted
	return sorted
}
```
Here we implement a comparison function for a given candidate against the entire population. We have chosen to use the euclidean distance measure and as such we need to specify our function such that it only accepts inputs of numeric data-type. We return a complete sorted list of distance measures and class labels for each member of the population. The next step allows us to predict a class for our candidate based of a majority rule decision examinging the class labels of the k-nearest neighbors. 

``` go

func chooseClass(rankedlist map[string]float64) string {
	pl := make(sliceOfOrderedPair, len(rankedlist))
	i := 0
	for k, v := range rankedlist { // counting the number of each class in ranked list
		pl[i] = ordererdPair{v, k}
		i++
	}
	sort.Sort(sort.Reverse(pl))
	//fmt.Println(pl)
	return pl[0].class // choosing majority class.
}

func knn(candidate []float64, population [][]float64, popClass []string, k int) string {
	if len(population) != len(popClass) {
		log.Fatal("Mismatch. All population must have class identifiers")
	}
	sorted := comparison(candidate, population, popClass)

	m := map[string]float64{}
	for i := 0; i < k; i++ {
		m[sorted[i].class] += 1.0
	}
	//fmt.Println(m)
	return chooseClass(m)
}

```
The next two functions work together. The first chooseClass function assumes that we have performed a count aggregation on our ranked list to determine the number of the population falling under each respect class. It unpacks this mapping and places the pairs of numbers and class-labels into our custom structure to apply a sort function. Taking the most prevalent class among the list to be the return result. The knn function plays an intermediary role in that it filters the rankedlist returned by the comparison function to assess only the top k members of the population. It then aggregates to count the population by class before calling the chooseClass function. 

Of course we will often wish to compare more than one candidate, and more importantly we want to know the accuracy of our predictions. The next two functions address this concern:

``` go

func predictionAccuracy(test [][]float64, train [][]float64, trainC []string, actualC []string, k int) float64 {
	result := 0.0
	for i := 0; i < len(test); i++ {
		//fmt.Println("Prediction", knn(test[i], train, trainC, k))
		//fmt.Println("Acutal class", actualC[i])
		if knn(test[i], train, trainC, k) == actualC[i] {
			result++
		}
	}
	return result / float64(len(test))
}

func trainTest(pop [][]float64, class []string) (t [][]float64, tClass []string, tst [][]float64, tstClass []string) {
	train := make([][]float64, 0)
	var trainClass []string
	test := make([][]float64, 0)
	var testClass []string
	s2 := rand.NewSource(21) // setting random number generator
	r2 := rand.New(s2)
	var randomchoice []int
	for i := 0; i < (len(pop) / 5); i++ { // ensures 80/20 split
		randomchoice = append(randomchoice, r2.Intn(len(pop))) // choosing numbers within range of pop size
	}

	set := make(map[int]bool) // for selecting only those randomly generated indexes
	for _, v := range randomchoice {
		set[v] = true
	}

	for i := 0; i < len(pop); i++ {
		if set[i] { // selecting those randomly generated indexes for test set.
			test = append(test, pop[i])
			testClass = append(testClass, class[i])
		} else {
			train = append(train, pop[i])
			trainClass = append(trainClass, class[i])
		}
	}
	return train, trainClass, test, testClass
}
```
The first function assumes the second. We have split our data such that for a randomly selected sub-set of the population we pretend ignorance its class-label and use the rest of the data to (train) develop a prediction for the potential class of the test candidates. We then compare this predicted label to the actual class for a measure of the algorithm's accuracy. 

The second trainTest function simply selects a random fifth of our population to enter into the test set and uses the rest for training data. It returns four elements: (1) slice of slices with the test features for each member of the test population, (2) a slice of strings with the class label for each member of the test population, and the same data for the training population. We are now in a positon to load in some actual data and apply these functions. 


``` go


func main() {
	// read data
	file := "/Users/nathanielforde/Desktop/Data Science/Mathematical Probability/iris.data.csv"
	f, err := os.Open(file)
	defer f.Close()

	r := csv.NewReader(f)
	r.Comma = ','
	r.Comment = '#'

	records, err := r.ReadAll()
	if err != nil {
		log.Fatal(err)
	}

	// Data must be of the form [ num, num, num ...  class identifier]
	// we now parse the data from strings into numbers.

	var population [][]string
	var classifier []string
	for i := 0; i < len(records); i++ {
		classifier = append(classifier, records[i][len(records[i])-1])
		population = append(population, records[i][:len(records[i])-2])
	}
	//fmt.Println(population[1])

	var popNum [][]float64
	for i := 0; i < len(population); i++ {
		var p []float64
		for j := 0; j < len(population[0]); j++ {
			q, err := strconv.ParseFloat(population[i][j], 64)
			if err != nil {
				panic(err)
			}
			p = append(p, q)
		}
		popNum = append(popNum, p)
	}

	train, _, _, _ := trainTest(popNum, classifier)
	_, trainClass, _, _ := trainTest(popNum, classifier)
	_, _, test, _ := trainTest(popNum, classifier)
	_, _, _, testClass := trainTest(popNum, classifier)

	fmt.Println(len(train))
	fmt.Println(len(test))

	fmt.Println(predictionAccuracy(test, train, trainClass, testClass, 3))
```
Our results on the iris data set show:

0.9629629629629629


Finished in  0.585s