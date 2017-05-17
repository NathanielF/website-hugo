+++
date = "2017-05-08T08:22:07+01:00"
draft = false
title = "Decision Boundaries"
image = "/portfolio/perceptron/perceptron_schematic.png"
+++

Despite sounding like the protagonist of rejected sci-fi script, the perceptron is just a lowly algorithm. Inspired by the model of neuronal triggering patterns, this simple linear classifier was designed to enact a pseudo-biological processing mechanism. As our brain receives and processes different inputs it unconsciously corrects for infelicities in rendering based on a weighting derived from past experience. The thought is that we are progressively conditioned for behaviour that expedites performance of perceptual and physical reflex. At a more gross level our body learns not to waste resources digesting food it can't process, and optimises the processes that extract nutrients from food where it can. This operational pattern is assumed to hold even down to the level of the neuron.
While the brain-model analogy is far from perfect and breaks down somewhat in the details, it should be sufficient to lend context to our elaboration of the algorithm below.

We will build the perceptron algorithm from the basic buildings blocks of the Golang programming language.The exercise is intended as much to showcase the Go language as it is to explore the perceptron algorithm.  

In an image the processing model follows this trajectory:
![The perceptron](/portfolio/perceptron/perceptron_schematic.png)

``` go
import "fmt"

func dotproduct(input []float64, weights []float64) float64 {
    sum := 0.0
    for i := 0; i < len(input); i++ {
        sum = sum + (input[i] * weights[i])
    }
    return sum
}
```

This is the dot product function which will be used to combine the disparate inputs and their learned weights. The dotproduct function is versatile in that it can take numeric weights which represent continuous data or integers which are coded classifiers of categories. However this range of possibilities is then processed and reduced to a single number which (given a large range of inputs) can often appear like a meaningless summary figure.

Yet given some domain knowledge and reasonable expectations about function output we can set a threshold figure to define a binary classification function known as the heaviside activation function. It is often reasonable to set the threshold to 0 in which case the proportion of positive (negative) inputs will force the output over (below) the threshold. This allows for a concise summary of our triggering event as being of one kind or another.


We will code the process in stages of function composition. We take each stage in turn.

``` go
// Our activation function
func heaviside(x float64) float64 {
    if x >= 0.0 {
        return 1.0
    }
    return -1.0
}

```
Putting these details together allows us to make a simple prediction informed by the kind of inputs received.

``` go
// Function which runs the dotproduct through an activation function

func predict(input []float64, weights []float64) float64 {
    sum := dotproduct(input, weights)
    return heaviside(sum)
}

```
In the case where we aim to refine our predictive capacity we can train our algorithm on the historic dataset. Given a set of historic observations (e.g. the length and width of a petal or sepal in a given iris), we can identfy those combinations of length and width which are associated with different species of iris. In this context this means that we run our prediction algorithm on the training data and observe the difference (delta) between the prediction and the actual species as noted in the historical record. Just as we would get better at recognising the flowers if we were penalised for every incorrect identification.
Ultimately the idea is that we use this delta to refine the weights we assign to each feature upon encountering a false prediction. So long as the dataset is linearly seperable, if we refine each weight as we iterate this process across each observation we will ultimately converge on a set of weights which result in no prediction errors.

![The Cycle](/portfolio/perceptron/Cycle.png)

This raises the question of how we should update the weights with respect to the discovered delta?

The choice is informed by the degree to which we want refine our weights given an observed error. We don't want to over-correct since this means we might fail to converge and if we under-correct then convergence might take an age. While the choice of a correction is parameter which ought to be specified with knowledge of the project at hand, we can in general specify a learning rate that implements a change in the features weight proportional to input.

``` go
// update function for an individual observation vector i.e. the input array

func update(input []float64, delta float64, weights []float64, rate float64) []float64 {
    for i := 1; i < len(weights); i++ {
        weights[i] = round(weights[i], .5, 3) + delta*rate*input[i]
    }
    weights[0] = round(weights[0], .5, 3) + delta*rate
    return weights
}

```
Once we've updated the weights for one flawed prediction we continue the process and loop through our entire set of training observations.

``` go
// Process function looping over each observation-vector in our dataset (i.e. the array of input arrays)
//  and updating the weights. Each element of the array  is like: "5.9,3.0,5.1,1.8,Iris-virginica"
// Returns new weights and error count for each prediction based on the observed values.

func process(inputs [][]float64, weights []float64, rate float64) ([]float64, int) {
    errorcount := 0
    for i := 0; i <= len(inputs)-1; i++ {
        target := inputs[i][len(inputs[i])-1]        
        // target is included in our training data
        prediction := predict(inputs[i][0:len(inputs[i])-1], weights)
        // prediction on the dataset minus the target
        delta := target - prediction
        weights = update(inputs[i], delta, weights, rate)
        if delta != 0.0 {
            errorcount = errorcount + 1
        }
    }
return weights, errorcount
}

```
This is sufficient to run through an entire dataset making incremental changes upon encountering each error. But there is no guarantee that such incremental changes will be sufficient to converge to a set of weights which
perfectly predict all our training observations. The perceptron algorithm is simply the repetition of this process for pre-specified number of iterations.

``` go
func perceptron(inputs [][]float64, weights []float64, rate float64, iterations int) ([]float64, []int) {
    var finalweights []float64
    var finalerrors []int
    var finalerror int
    for i := 0; i <= iterations; i++ {
        fmt.Println("Input weight loop", i, ":", finalweights)
        finalweights, finalerror = process(inputs, weights, rate)
        finalerrors = append(finalerrors, finalerror)
    }
    return finalweights, finalerrors
}

```
To run the algorithm on real data we load in the iris data set and encode the strings as numeric values.

``` go
//Loading in the iris data as a slice of row strings
    iris, err := readLines("/iris.data.csv")
    if err != nil {
        return
    }

    str := iris


//   We now extract the various row elements initially as strings,
//   from the row-string ( e.g from  "5.9,3.0,5.1,1.8,Iris-virginica"),
//   we extract "5.9" and convert to force the values into numeric format.
//   We then reconstruct the row andput the details into a slice of slices
//   called inputs. We only need the first hundred observations of iris data.
//   We will use a perceptron to
//  distinguish the Iris-virginica and Iris-setosa based on Sepal and Petal length.

    inputs := make([][]float64, 100) // Declaring the empty slice of slices. Our design matrix.

    for i := 0; i < 100; i++ { // pull out and convert each substring into a number
        var input []float64
    p, _ := strconv.ParseFloat(strings.Split(string(str[i]), ",")[0], 64) // Sepal Length
    //q, _ := strconv.ParseFloat(strings.Split(string(str[i]), ",")[1], 64)
    r, _ := strconv.ParseFloat(strings.Split(string(str[i]), ",")[2], 64) // Petal Length
    //s, _ := strconv.ParseFloat(strings.Split(string(str[i]), ",")[3], 64)
    t := binary(strings.Split(string(str[i]), ",")[4] == "Iris-setosa")
        input = append(input, 1.0) /////// Recreating our row vectors for each observation
        input = append(input, p)
        //input = append(input, q)
        input = append(input, r)
        //input = append(input, s)
        input = append(input, t)
        inputs[i] = input
    // Defining each row-vector observation (e.g [1, 5.9, 5.1, -1])
    // as an instance of our design matrix  ["intercept column", "sepal length", "petal length", target]
    }

    var w = []float64{0, 0, 0} ///////// initialising our weights

fmt.Println(perceptron(inputs, w, .1, 10))

```
Nathaniels-MacBook-Pro:GoLang nathanielforde$ go run Perceptron.go
Input weight loop 0 : []

Input weight loop 1 : [0 0.38 0.66]

Input weight loop 2 : [0 0.76 1.32]

Input weight loop 3 : [-0.2 0.22 1.68]

Input weight loop 4 : [-0.2 0.34 2.1]

Input weight loop 5 : [-0.4 -0.68 1.82]

Input weight loop 6 : [-0.4 -0.68 1.82]

Input weight loop 7 : [-0.4 -0.68 1.82]

Input weight loop 8 : [-0.4 -0.68 1.82]

Input weight loop 9 : [-0.4 -0.68 1.82]

Input weight loop 10 : [-0.4 -0.68 1.82]

[-0.4 -0.68 1.82] [2 2 3 2 1 0 0 0 0 0 0]

Here we can see that the perceptron algorithm converges after five iterations, and indeed we can plot the decision boundary in two dimensions based on the discovered weights. Crucially the decision boundary does indeed seperate the two species of iris.

![The Cycle](/portfolio/perceptron/iris.Rplot.png)
