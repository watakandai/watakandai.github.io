---
title: "Multiclass SVM using SGD in Theano"
categories:
  - statistics
tags:
  - MNIST
  - SVM
  - classifier
use_math: true
published: false
---


## Linear multiclass support vector machine for MNIST classification in Theano with SGD

The Support Vector Machine is a linear classifier designed to maximize the _margin of separability_ for linearly separable datasets. Let $(x_i, y_i)$ describe the labeled categorical data we wish to build a classifier for, where $x_i \in \mathbb{R}^N$ and $y_i \in \{1,\dots,M\}$. The SVM finds the _maximum margin_ hyperplane. This is described first by 

$$
w\dot x + b = -1
$$

and

$$
w\dot x + b = 1
$$

We begin with some frontmatter:


```python
"""
MNIST classification using multiclass support vector machines
"""

from __future__ import print_function

import os 
import sys
import timeit

import numpy as np 

import theano
import theano.tensor as T

#import matplotlib.plot as plt
from load_mnist import *

```

Next we define a class to perform the classification


```python
class SVM(object):
    """Multi-class SVM Class"""

    def __init__(self, input, output, n_in, n_out):
        """ Initialize the parameters of the logistic regression

        :type input: theano.tensor.TensorType
        :param input: symbolic variable that describes the input of the
                      architecture (one minibatch)

        :type output: theano.tensor.TensorType
        :param output: symbolic variable that describes the input of the
                      architecture (one minibatch)

        :type n_in: int
        :param n_in: number of input units, the dimension of the space in
                     which the datapoints lie

        :type n_out: int
        :param n_out: number of output units, the dimension of the space in
                      which the labels lie
        """

        # initialize with 0 the weights W as a matrix of shape (n_in, n_out)
        self.W = theano.shared(
            value=np.zeros(
                (n_in, n_out),
                dtype=theano.config.floatX
            ),
            name='W',
            borrow=True
        )

        # initialize the biases b as a vector of n_out 0s
        self.b = theano.shared(
            value=np.zeros(
                (n_out,),
                dtype=theano.config.floatX
            ),
            name='b',
            borrow=True
        )

        self.Lambda = .0001

        i = 1-output*T.dot(input, self.W) - output*T.tile(self.b.T, (input.shape[0],1))
        self.loss = (i + T.abs_(i))/2

        self.y_pred = T.argmax(T.dot(input, self.W)+self.b, axis=1)

        self.params = [self.W, self.b]
        self.input = input

    def cost(self, y):
        """Return the mean of the negative log-likelihood of the prediction
        of this model under a given target distribution.

        .. math::

            \frac{1}{|\mathcal{D}|} \mathcal{L} (\theta=\{W,b\}, \mathcal{D}) =
            \frac{1}{|\mathcal{D}|} \sum_{i=0}^{|\mathcal{D}|}
                \log(P(Y=y^{(i)}|x^{(i)}, W,b)) \\
            \ell (\theta=\{W,b\}, \mathcal{D})

        :type y: theano.tensor.TensorType
        :param y: corresponds to a vector that gives for each example the
                  correct label

        Note: we use the mean instead of the sum so that
              the learning rate is less dependent on the batch size
        """

        return T.mean(self.loss)+self.Lambda*T.sum(self.W*self.W)

    def errors(self, y):
        """Return a float representing the number of errors in the minibatch
        over the total number of examples of the minibatch ; zero one
        loss over the size of the minibatch

        :type y: theano.tensor.TensorType
        :param y: corresponds to a vector that gives for each example the
                  correct label
        """

        if y.dtype.startswith('int'):
            return T.mean(T.neq(self.y_pred, T.argmax(y, axis=1)))
        else:
            raise NotImplementedError()

```

Then set some parameters


```python
learning_rate=0.13
n_epochs=1000
dataset='mnist.pkl.gz'
batch_size=600
```

Load the data


```python
datasets = load_data(dataset)

train_set_x, train_set_y = datasets[0]
valid_set_x, valid_set_y = datasets[1]
test_set_x, test_set_y = datasets[2]

# compute number of minibatches for training, validation and testing
n_train_batches = train_set_x.get_value(borrow=True).shape[0] // batch_size
n_valid_batches = valid_set_x.get_value(borrow=True).shape[0] // batch_size
n_test_batches = test_set_x.get_value(borrow=True).shape[0] // batch_size
```

    ... loading data


Compile Theano functions


```python
    print('... building the model')

    index = T.lscalar()  # index to a [mini]batch

    # generate symbolic variables for input (x and y represent a
    # minibatch)
    x = T.matrix('x')  # data, presented as rasterized images
    y = T.imatrix('y')  # labels, presented as matrix

    # construct the logistic regression class
    # Each MNIST image has size 28*28
    classifier = SVM(input=x, output = y, n_in= 28*28, n_out=10)
    cost = classifier.cost(y)

    # compiling a Theano function that computes the mistakes that are made by
    # the model on a minibatch
    test_model = theano.function(
        inputs=[index],
        outputs=classifier.errors(y),
        givens={
            x: test_set_x[index * batch_size: (index + 1) * batch_size],
            y: test_set_y[index * batch_size: (index + 1) * batch_size]
        }
    )

    validate_model = theano.function(
        inputs=[index],
        outputs=classifier.errors(y),
        givens={
            x: valid_set_x[index * batch_size: (index + 1) * batch_size],
            y: valid_set_y[index * batch_size: (index + 1) * batch_size]
        }
    )

    # compute the gradient of cost with respect to theta = (W,b)
    g_W = T.grad(cost=cost, wrt=classifier.W)
    g_b = T.grad(cost=cost, wrt=classifier.b)

    # specify how to update the parameters of the model as a list of
    # (variable, update expression) pairs.
    updates = [(classifier.W, classifier.W - learning_rate * g_W),
               (classifier.b, classifier.b - learning_rate * g_b)]

    # compiling a Theano function `train_model` that returns the cost, but in
    # the same time updates the parameter of the model based on the rules
    # defined in `updates`
    train_model = theano.function(
        inputs=[index],
        outputs=cost,
        updates=updates,
        givens={
            x: train_set_x[index * batch_size: (index + 1) * batch_size],
            y: train_set_y[index * batch_size: (index + 1) * batch_size]
        }
    )
```

    ... building the model


Setup main loop


```python
    print('... training the model')
    # early-stopping parameters
    patience = 5000  # look as this many examples regardless
    patience_increase = 2  # wait this much longer when a new best is
                                  # found
    improvement_threshold = 0.995  # a relative improvement of this much is
                                  # considered significant
    validation_frequency = min(n_train_batches, patience // 2)
                                  # go through this many
                                  # minibatche before checking the network
                                  # on the validation set; in this case we
                                  # check every epoch

    best_validation_loss = np.inf
    test_score = 0.
    start_time = timeit.default_timer()

    done_looping = False
    epoch = 0
```

    ... training the model


Run main loop


```python
    while (epoch < n_epochs) and (not done_looping):
        epoch = epoch + 1
        for minibatch_index in range(n_train_batches):

            minibatch_avg_cost = train_model(minibatch_index)
            # iteration number
            iter = (epoch - 1) * n_train_batches + minibatch_index

            if (iter + 1) % validation_frequency == 0:
                # compute zero-one loss on validation set
                validation_losses = [validate_model(i)
                                     for i in range(n_valid_batches)]
                this_validation_loss = np.mean(validation_losses)

                print(
                    'epoch %i, minibatch %i/%i, validation error %f %%' %
                    (
                        epoch,
                        minibatch_index + 1,
                        n_train_batches,
                        this_validation_loss * 100.
                    )
                )

                # if we got the best validation score until now
                if this_validation_loss < best_validation_loss:
                    #improve patience if loss improvement is good enough
                    if this_validation_loss < best_validation_loss *  \
                       improvement_threshold:
                        patience = max(patience, iter * patience_increase)

                    best_validation_loss = this_validation_loss
                    # test it on the test set

                    test_losses = [test_model(i)
                                   for i in range(n_test_batches)]
                    test_score = np.mean(test_losses)

                    print(
                        (
                            '     epoch %i, minibatch %i/%i, test error of'
                            ' best model %f %%'
                        ) %
                        (
                            epoch,
                            minibatch_index + 1,
                            n_train_batches,
                            test_score * 100.
                        )
                    )

                    # save the best model
                    with open('best_model.pkl', 'wb') as f:
                        pickle.dump(classifier, f)

            if patience <= iter:
                done_looping = True
                break

    end_time = timeit.default_timer()
    print(
        (
            'Optimization complete with best validation score of %f %%,'
            'with test performance %f %%'
        )
        % (best_validation_loss * 100., test_score * 100.)
    )
    print('The code run for %d epochs, with %f epochs/sec' % (
        epoch, 1. * epoch / (end_time - start_time)))
    print(('The code for file ' +
           os.path.split(__file__)[1] +
           ' ran for %.1fs' % ((end_time - start_time))), file=sys.stderr)

```

    epoch 1, minibatch 83/83, validation error 21.281250 %
         epoch 1, minibatch 83/83, test error of best model 22.135417 %
    epoch 2, minibatch 83/83, validation error 17.322917 %
         epoch 2, minibatch 83/83, test error of best model 18.572917 %
    epoch 3, minibatch 83/83, validation error 15.270833 %
         epoch 3, minibatch 83/83, test error of best model 16.020833 %
    epoch 4, minibatch 83/83, validation error 13.958333 %
         epoch 4, minibatch 83/83, test error of best model 14.427083 %
    epoch 5, minibatch 83/83, validation error 12.937500 %
         epoch 5, minibatch 83/83, test error of best model 13.500000 %
    epoch 6, minibatch 83/83, validation error 12.270833 %
         epoch 6, minibatch 83/83, test error of best model 12.625000 %
    epoch 7, minibatch 83/83, validation error 11.906250 %
         epoch 7, minibatch 83/83, test error of best model 12.197917 %
    epoch 8, minibatch 83/83, validation error 11.666667 %
         epoch 8, minibatch 83/83, test error of best model 11.864583 %
    epoch 9, minibatch 83/83, validation error 11.427083 %
         epoch 9, minibatch 83/83, test error of best model 11.635417 %
    epoch 10, minibatch 83/83, validation error 11.291667 %
         epoch 10, minibatch 83/83, test error of best model 11.385417 %
    epoch 11, minibatch 83/83, validation error 11.062500 %
         epoch 11, minibatch 83/83, test error of best model 11.229167 %
    epoch 12, minibatch 83/83, validation error 10.937500 %
         epoch 12, minibatch 83/83, test error of best model 11.104167 %
    epoch 13, minibatch 83/83, validation error 10.822917 %
         epoch 13, minibatch 83/83, test error of best model 10.947917 %
    epoch 14, minibatch 83/83, validation error 10.677083 %
         epoch 14, minibatch 83/83, test error of best model 10.812500 %
    epoch 15, minibatch 83/83, validation error 10.593750 %
         epoch 15, minibatch 83/83, test error of best model 10.729167 %
    epoch 16, minibatch 83/83, validation error 10.458333 %
         epoch 16, minibatch 83/83, test error of best model 10.677083 %
    epoch 17, minibatch 83/83, validation error 10.333333 %
         epoch 17, minibatch 83/83, test error of best model 10.531250 %
    epoch 18, minibatch 83/83, validation error 10.218750 %
         epoch 18, minibatch 83/83, test error of best model 10.406250 %
    epoch 19, minibatch 83/83, validation error 10.072917 %
         epoch 19, minibatch 83/83, test error of best model 10.270833 %
    epoch 20, minibatch 83/83, validation error 10.010417 %
         epoch 20, minibatch 83/83, test error of best model 10.187500 %
    epoch 21, minibatch 83/83, validation error 9.968750 %
         epoch 21, minibatch 83/83, test error of best model 10.104167 %
    epoch 22, minibatch 83/83, validation error 9.968750 %
    epoch 23, minibatch 83/83, validation error 9.927083 %
         epoch 23, minibatch 83/83, test error of best model 9.968750 %
    epoch 24, minibatch 83/83, validation error 9.864583 %
         epoch 24, minibatch 83/83, test error of best model 9.906250 %
    epoch 25, minibatch 83/83, validation error 9.833333 %
         epoch 25, minibatch 83/83, test error of best model 9.885417 %
    epoch 26, minibatch 83/83, validation error 9.760417 %
         epoch 26, minibatch 83/83, test error of best model 9.781250 %
    epoch 27, minibatch 83/83, validation error 9.718750 %
         epoch 27, minibatch 83/83, test error of best model 9.770833 %
    epoch 28, minibatch 83/83, validation error 9.708333 %
         epoch 28, minibatch 83/83, test error of best model 9.708333 %
    epoch 29, minibatch 83/83, validation error 9.656250 %
         epoch 29, minibatch 83/83, test error of best model 9.697917 %
    epoch 30, minibatch 83/83, validation error 9.562500 %
         epoch 30, minibatch 83/83, test error of best model 9.677083 %
    epoch 31, minibatch 83/83, validation error 9.510417 %
         epoch 31, minibatch 83/83, test error of best model 9.625000 %
    epoch 32, minibatch 83/83, validation error 9.489583 %
         epoch 32, minibatch 83/83, test error of best model 9.562500 %
    epoch 33, minibatch 83/83, validation error 9.479167 %
         epoch 33, minibatch 83/83, test error of best model 9.500000 %
    epoch 34, minibatch 83/83, validation error 9.447917 %
         epoch 34, minibatch 83/83, test error of best model 9.479167 %
    epoch 35, minibatch 83/83, validation error 9.395833 %
         epoch 35, minibatch 83/83, test error of best model 9.447917 %
    epoch 36, minibatch 83/83, validation error 9.427083 %
    epoch 37, minibatch 83/83, validation error 9.416667 %
    epoch 38, minibatch 83/83, validation error 9.375000 %
         epoch 38, minibatch 83/83, test error of best model 9.395833 %
    epoch 39, minibatch 83/83, validation error 9.375000 %
    epoch 40, minibatch 83/83, validation error 9.354167 %
         epoch 40, minibatch 83/83, test error of best model 9.333333 %
    epoch 41, minibatch 83/83, validation error 9.385417 %
    epoch 42, minibatch 83/83, validation error 9.312500 %
         epoch 42, minibatch 83/83, test error of best model 9.322917 %
    epoch 43, minibatch 83/83, validation error 9.302083 %
         epoch 43, minibatch 83/83, test error of best model 9.322917 %
    epoch 44, minibatch 83/83, validation error 9.281250 %
         epoch 44, minibatch 83/83, test error of best model 9.322917 %
    epoch 45, minibatch 83/83, validation error 9.250000 %
         epoch 45, minibatch 83/83, test error of best model 9.281250 %
    epoch 46, minibatch 83/83, validation error 9.197917 %
         epoch 46, minibatch 83/83, test error of best model 9.281250 %
    epoch 47, minibatch 83/83, validation error 9.187500 %
         epoch 47, minibatch 83/83, test error of best model 9.239583 %
    epoch 48, minibatch 83/83, validation error 9.135417 %
         epoch 48, minibatch 83/83, test error of best model 9.187500 %
    epoch 49, minibatch 83/83, validation error 9.145833 %
    epoch 50, minibatch 83/83, validation error 9.145833 %
    epoch 51, minibatch 83/83, validation error 9.135417 %
    epoch 52, minibatch 83/83, validation error 9.104167 %
         epoch 52, minibatch 83/83, test error of best model 9.083333 %
    epoch 53, minibatch 83/83, validation error 9.062500 %
         epoch 53, minibatch 83/83, test error of best model 9.041667 %
    epoch 54, minibatch 83/83, validation error 9.031250 %
         epoch 54, minibatch 83/83, test error of best model 9.031250 %
    epoch 55, minibatch 83/83, validation error 9.052083 %
    epoch 56, minibatch 83/83, validation error 9.062500 %
    epoch 57, minibatch 83/83, validation error 9.052083 %
    epoch 58, minibatch 83/83, validation error 9.031250 %
    epoch 59, minibatch 83/83, validation error 9.031250 %
    epoch 60, minibatch 83/83, validation error 9.010417 %
         epoch 60, minibatch 83/83, test error of best model 8.989583 %
    epoch 61, minibatch 83/83, validation error 8.979167 %
         epoch 61, minibatch 83/83, test error of best model 8.968750 %
    epoch 62, minibatch 83/83, validation error 8.968750 %
         epoch 62, minibatch 83/83, test error of best model 8.958333 %
    epoch 63, minibatch 83/83, validation error 8.968750 %
    epoch 64, minibatch 83/83, validation error 8.947917 %
         epoch 64, minibatch 83/83, test error of best model 8.927083 %
    epoch 65, minibatch 83/83, validation error 8.927083 %
         epoch 65, minibatch 83/83, test error of best model 8.906250 %
    epoch 66, minibatch 83/83, validation error 8.927083 %
    epoch 67, minibatch 83/83, validation error 8.916667 %
         epoch 67, minibatch 83/83, test error of best model 8.895833 %
    epoch 68, minibatch 83/83, validation error 8.906250 %
         epoch 68, minibatch 83/83, test error of best model 8.895833 %
    epoch 69, minibatch 83/83, validation error 8.885417 %
         epoch 69, minibatch 83/83, test error of best model 8.895833 %
    epoch 70, minibatch 83/83, validation error 8.875000 %
         epoch 70, minibatch 83/83, test error of best model 8.916667 %
    epoch 71, minibatch 83/83, validation error 8.885417 %
    epoch 72, minibatch 83/83, validation error 8.895833 %
    epoch 73, minibatch 83/83, validation error 8.906250 %
    epoch 74, minibatch 83/83, validation error 8.885417 %
    epoch 75, minibatch 83/83, validation error 8.822917 %
         epoch 75, minibatch 83/83, test error of best model 8.875000 %
    epoch 76, minibatch 83/83, validation error 8.781250 %
         epoch 76, minibatch 83/83, test error of best model 8.864583 %
    epoch 77, minibatch 83/83, validation error 8.750000 %
         epoch 77, minibatch 83/83, test error of best model 8.854167 %
    epoch 78, minibatch 83/83, validation error 8.739583 %
         epoch 78, minibatch 83/83, test error of best model 8.833333 %
    epoch 79, minibatch 83/83, validation error 8.739583 %
    epoch 80, minibatch 83/83, validation error 8.729167 %
         epoch 80, minibatch 83/83, test error of best model 8.833333 %
    epoch 81, minibatch 83/83, validation error 8.729167 %
    epoch 82, minibatch 83/83, validation error 8.750000 %
    epoch 83, minibatch 83/83, validation error 8.729167 %
    epoch 84, minibatch 83/83, validation error 8.750000 %
    epoch 85, minibatch 83/83, validation error 8.729167 %
    epoch 86, minibatch 83/83, validation error 8.718750 %
         epoch 86, minibatch 83/83, test error of best model 8.687500 %
    epoch 87, minibatch 83/83, validation error 8.687500 %
         epoch 87, minibatch 83/83, test error of best model 8.697917 %
    epoch 88, minibatch 83/83, validation error 8.687500 %
    epoch 89, minibatch 83/83, validation error 8.687500 %
    epoch 90, minibatch 83/83, validation error 8.687500 %
    epoch 91, minibatch 83/83, validation error 8.645833 %
         epoch 91, minibatch 83/83, test error of best model 8.656250 %
    epoch 92, minibatch 83/83, validation error 8.635417 %
         epoch 92, minibatch 83/83, test error of best model 8.635417 %
    epoch 93, minibatch 83/83, validation error 8.635417 %
    epoch 94, minibatch 83/83, validation error 8.635417 %
    epoch 95, minibatch 83/83, validation error 8.625000 %
         epoch 95, minibatch 83/83, test error of best model 8.604167 %
    epoch 96, minibatch 83/83, validation error 8.614583 %
         epoch 96, minibatch 83/83, test error of best model 8.593750 %
    epoch 97, minibatch 83/83, validation error 8.604167 %
         epoch 97, minibatch 83/83, test error of best model 8.583333 %
    epoch 98, minibatch 83/83, validation error 8.604167 %
    epoch 99, minibatch 83/83, validation error 8.593750 %
         epoch 99, minibatch 83/83, test error of best model 8.593750 %
    epoch 100, minibatch 83/83, validation error 8.604167 %
    epoch 101, minibatch 83/83, validation error 8.593750 %
    epoch 102, minibatch 83/83, validation error 8.583333 %
         epoch 102, minibatch 83/83, test error of best model 8.562500 %
    epoch 103, minibatch 83/83, validation error 8.572917 %
         epoch 103, minibatch 83/83, test error of best model 8.552083 %
    epoch 104, minibatch 83/83, validation error 8.531250 %
         epoch 104, minibatch 83/83, test error of best model 8.520833 %
    epoch 105, minibatch 83/83, validation error 8.520833 %
         epoch 105, minibatch 83/83, test error of best model 8.531250 %
    epoch 106, minibatch 83/83, validation error 8.510417 %
         epoch 106, minibatch 83/83, test error of best model 8.541667 %
    epoch 107, minibatch 83/83, validation error 8.500000 %
         epoch 107, minibatch 83/83, test error of best model 8.520833 %
    epoch 108, minibatch 83/83, validation error 8.489583 %
         epoch 108, minibatch 83/83, test error of best model 8.531250 %
    epoch 109, minibatch 83/83, validation error 8.489583 %
    epoch 110, minibatch 83/83, validation error 8.479167 %
         epoch 110, minibatch 83/83, test error of best model 8.531250 %
    epoch 111, minibatch 83/83, validation error 8.468750 %
         epoch 111, minibatch 83/83, test error of best model 8.510417 %
    epoch 112, minibatch 83/83, validation error 8.447917 %
         epoch 112, minibatch 83/83, test error of best model 8.510417 %
    epoch 113, minibatch 83/83, validation error 8.447917 %
    epoch 114, minibatch 83/83, validation error 8.437500 %
         epoch 114, minibatch 83/83, test error of best model 8.520833 %
    epoch 115, minibatch 83/83, validation error 8.447917 %
    epoch 116, minibatch 83/83, validation error 8.437500 %
    epoch 117, minibatch 83/83, validation error 8.427083 %
         epoch 117, minibatch 83/83, test error of best model 8.500000 %
    epoch 118, minibatch 83/83, validation error 8.416667 %
         epoch 118, minibatch 83/83, test error of best model 8.489583 %
    epoch 119, minibatch 83/83, validation error 8.427083 %
    epoch 120, minibatch 83/83, validation error 8.427083 %
    epoch 121, minibatch 83/83, validation error 8.416667 %
    epoch 122, minibatch 83/83, validation error 8.416667 %
    epoch 123, minibatch 83/83, validation error 8.416667 %
    epoch 124, minibatch 83/83, validation error 8.416667 %
    epoch 125, minibatch 83/83, validation error 8.427083 %
    epoch 126, minibatch 83/83, validation error 8.416667 %
    epoch 127, minibatch 83/83, validation error 8.416667 %
    epoch 128, minibatch 83/83, validation error 8.395833 %
         epoch 128, minibatch 83/83, test error of best model 8.479167 %
    epoch 129, minibatch 83/83, validation error 8.395833 %
    epoch 130, minibatch 83/83, validation error 8.385417 %
         epoch 130, minibatch 83/83, test error of best model 8.479167 %
    epoch 131, minibatch 83/83, validation error 8.364583 %
         epoch 131, minibatch 83/83, test error of best model 8.468750 %
    epoch 132, minibatch 83/83, validation error 8.364583 %
    epoch 133, minibatch 83/83, validation error 8.375000 %
    epoch 134, minibatch 83/83, validation error 8.364583 %
    epoch 135, minibatch 83/83, validation error 8.364583 %
    epoch 136, minibatch 83/83, validation error 8.375000 %
    epoch 137, minibatch 83/83, validation error 8.375000 %
    epoch 138, minibatch 83/83, validation error 8.375000 %
    epoch 139, minibatch 83/83, validation error 8.364583 %
    epoch 140, minibatch 83/83, validation error 8.364583 %
    epoch 141, minibatch 83/83, validation error 8.385417 %
    epoch 142, minibatch 83/83, validation error 8.385417 %
    epoch 143, minibatch 83/83, validation error 8.406250 %
    epoch 144, minibatch 83/83, validation error 8.395833 %
    epoch 145, minibatch 83/83, validation error 8.385417 %
    epoch 146, minibatch 83/83, validation error 8.385417 %
    epoch 147, minibatch 83/83, validation error 8.385417 %
    epoch 148, minibatch 83/83, validation error 8.385417 %
    epoch 149, minibatch 83/83, validation error 8.375000 %
    Optimization complete with best validation score of 8.364583 %,with test performance 8.468750 %
    The code run for 150 epochs, with 1.618632 epochs/sec



    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-8-e487e1ed5739> in <module>()
         68     epoch, 1. * epoch / (end_time - start_time)))
         69 print(('The code for file ' +
    ---> 70        os.path.split(__file__)[1] +
         71        ' ran for %.1fs' % ((end_time - start_time))), file=sys.stderr)


    NameError: name '__file__' is not defined



```python

```
