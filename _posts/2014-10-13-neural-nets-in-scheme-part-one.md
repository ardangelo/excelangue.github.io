---
layout: post
title: Neural Nets in Scheme Part 1
categories: [code]
---

I have been getting into functional programming, specifically Scheme and tend to learn by doing. After being linked to [this](http://neuralnetworksanddeeplearning.com/chap1.html) online course in neural networks using Python, I decided to follow along not in Python but in Scheme. I especially liked how the course uses no specific libraries besides NumPy, as I'd like to keep reimplementation to a minimum.

I recommend reading at least the first part of the article to get a more thorough understanding of the mathematics and terms.

## Matrix functions

Keeping with the theme of "making it from scratch", here are the the necessary matrix operations.

	(define (matrix-multiply x y)
		(map (lambda (row) (apply map 
			(lambda column (apply + (map * row column))) y)) x))

	(define (matrix-add x y) (map (lambda (x y) (map + x y)) x y))

	(define (transpose m) 
		(apply map list m))

	(define (magnitude v) (sqrt (apply + (map square v))))

## Adding two bits

In the first section, we'll just do some basic addition of two bits. Two inputs, a sum bit output, and a carry bit output. Pretty simple.

![1-bit binary adder]({{ site.url }}/assets/images/neural-nets-in-scheme-part-one/adder.png)

### Neural networking basics

The basic unit of decision making is the "neuron". Given any number of inputs it will return the result of a singular output based on a bias and per-input weights and fed into an "activation function".

	(define (neuron activation-function input weight bias)
		(activation-function (+ (* input weight) bias)))

The simplest neuron, the perceptron, uses the step function as its activation function. If the input is less than zero, it outputs a `1`. Otherwise, it outputs a `0`. 

	(define (step-function z)
		(if (< 0 z) 1 0))

Later, we'll use the most common type of neuron, the sigmoid neuron. This neuron's activation function is a smooth curve instead of a sharp step, allowing for a "learning" approach where weights and balances are tuned to slightly vary a decimal output as opposed to a strictly binary output.

Thus a perceptron can be implemented as 

	(lambda (input weight bias)
		(step-function (+ (* input weight) bias)))

### The NAND gate

We can implement a NAND gate in a perceptron by using two inputs, each with weight `-2` and the perceptron having a bias of `3`. 

To do this, we need to first adapt the genereralized neuron function to accept multiple inputs and weights. To do this, we'll use a `1x2` matrix for the weights and `2x1` matrix for the input. That way, they'll multiply nicely. 

	(define neuron (activation-function inputs weights bias)
		(activation-function (+ 
			(caar (matrix-multiply weights inputs))
			bias)))

The caar is to turn the `1x1` matrix into a number for the step function.

### Encoding the neural network

Now that we have a NAND gate implementation, we can form a 1-bit binary adder! Well, almost. We need to adapt the single-neuron function into a procedure that returns the result of a lot of linked neurons. It will be extremely helpful to refer again to the article on neural networking in Python, as I prefer to not duplicate the general explanation. 

The main idea is that we will have multiple layers of neurons, each receiving input from the layer before it (unless it is the input layer) and outputting a value to the layer after it (unless it is the output layer). Each input to a neuron must come from the layer before it (again, unless it is the input layer). This will greatly simplify the implementation, allowing us to code the network as a set of vectors and to calculate the result using little more than simple matrix operations. 

![Neural net without pass neurons]({{ site.url }}/assets/images/neural-nets-in-scheme-part-one/net-no-pass.png)

In examining the design of a 1-bit binary adder, the inputs feed into neuron `A`, `B`, and `C`. However, `A` also feeds into `B` and `C`, which means that we need three layers, one for inputs, one for `A`, and one for `B` and `C`. How, then, will be pass the input across two layers? We just need to insert a neuron into the second layer that passes along whatever input it gets to the next neuron. This means a weight of `1` for all inputs and a bias of `0`.

![Neural net with pass neurons]({{ site.url }}/assets/images/neural-nets-in-scheme-part-one/net.png)

Notice how the weight of `A` to the carry bit output (techincally to the "pass" neuron and then to the output) has a weight of `-4`. This is because the output of `A` feeds into both inputs of the NAND gate.

	-2 * i1 + -2 * i1 = -4 * i1

Now that the layering problem is solved, we can begin to encode the network as a bunch of matricies. Again, taking from the original article, we will encode weights as a list of `MxN` matricies where `M` is the number of receiving neurons and `N` is the number of sending neurons. For example, the weight of the connection between input neuron `x1` and the `A` would be row `2`, column `1`. If there is no connection between neurons, the weight is simply `0`. Going from input `x2` to "pass neuron" `p2` would correspond to row `3`, column `2` and have a weight of `0`.

![Weights of the connections]({{ site.url }}/assets/images/neural-nets-in-scheme-part-one/weights.png)

Biases are encoded as a list of `Nx1` matricies, one for each layer, where `N` is the number of neurons in each layer. We exclude the input biases, as they should be `0`. In our matrix calculations, we skip the input layer anyway. 

![Biases of the neurons]({{ site.url }}/assets/images/neural-nets-in-scheme-part-one/biases.png)

Finally, the inputs. This is the easy part. Simply a `Nx1` matrix where `N` is the number of inputs.

For the 1-bit binary adder, the weights and biases are encoded as such:

	(define weights '(
		((1 0) (-2 -2) (0 1)) 
		((-2 -2 0) (0 -2 -2) (0 1 0)) 
		((-2 -2 0) (0 0 -4))))
	(define biases '(
		((0) (3) (0)) 
		((3) (3) (0)) 
		((3) (3))))

### Computing the result of the neural network

From a high level, we need to multiply the input matrix `A` by weight matrix `W[0]` and add bias matrix `B[0]`, yielding `A'`. Repeat again with `A'` as the input for the next layer of neurons (weight matrix `W[1]` and bias matrix `B[1]`), until the network is done "feeding the results forward".

	feed-forward (A, W, B) {
		if (w is null) return a
		A' = map(activation-function, car(W) * transpose(A) + car(B))
		feed-forward(A', cdr(W), cdr(B))
	}

Because the list of weight and bias matricies are the same length, they'll run out at the same time. translating into Scheme we get:

	(define (feed-forward a-f a w b)
		(if (null? w) a
			(feed-forward 
				a-f 
				(map a-f (map car 
					(matrix-add 
						(matrix-multiply 
							(car w) (transpose (list a))) 
							(car b)))) 
						(cdr w) 
				(cdr b))))

We need to `(map car ... )` and `(transpose (list a))` to provide a numerical input to the activation function and to get everything in the proper form for matrix multiplication respectively.

### Putting it all together

Now that we have a `feed-forward` procedure, activation function, and some weights and biases, let's put it all together:

	;matrix & vector functions
	(define (matrix-multiply m n)
		(map (lambda (row) (apply map 
			(lambda column (apply + (map * row column))) n)) m))

	(define (matrix-add x y) (map (lambda (x y) (map + x y)) x y))

	(define (transpose m) 
		(apply map list m))
	 
	;activation functions
	(define (step-function z)
		(if (< 0 z) 1 0))

	;get result of neural net computation
	(define (feed-forward a-f a w b)
		(if (null? w) a
			(feed-forward 
				a-f 
				(map a-f (map car 
					(matrix-add 
						(matrix-multiply 
							(car w) (transpose (list a))) 
							(car b)))) 
						(cdr w) 
				(cdr b))))

	;example: add two bits X and Y
	;output is list (SUM CARRY-BIT)
	(define (add-two-bits x y)
		(define weights '(
			((1 0) (-2 -2) (0 1)) 
			((-2 -2 0) (0 -2 -2) (0 1 0)) 
			((-2 -2 0) (0 0 -4))))
		(define biases '(
			((0) (3) (0)) 
			((3) (3) (0)) 
			((3) (3))))

		(feed-forward step-function (list x y) weights biases))

Testing it out:

	1 ]=> (add-two-bits 0 1)

	;Value 1: (1 0)

	1 ]=> (add-two-bits 1 1)

	;Value 2: (0 1)

Tune in next time for some real machine learning using these foundations.

Please send any suggestions and corrections via e-mail!