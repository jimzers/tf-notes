# TENSORFLOW notes

## welcome to my tensor flow notes


### additional notes

https://chromium.googlesource.com/external/github.com/tensorflow/tensorflow/+/r0.7/tensorflow/g3doc/how_tos/variables/index.md

https://chromium.googlesource.com/external/github.com/tensorflow/tensorflow/+/r0.7/tensorflow/g3doc/how_tos/variable_scope/index.md

## notes

tf.data - prefeered data streaming

flow to get tf.Tensor from dataset:
convert dataset to tf.data.Iterator, then call Iterator.get_next method

To make ITERATOR:

tf.data.Dataset.make_one_shot_iterator

tf.data.Dataset.from_tensor_slices - take array of stuff you wanna tensors with

my_data = [
    [0, 1,],
    [2, 3,],
    [4, 5,],
    [6, 7,],
]
slices = tf.data.Dataset.from_tensor_slices(my_data)
next_item = slices.make_one_shot_iterator().get_next()


reaching end of data stream causes Dataset to throw an tf.errors.OutOfRangeError - catch this error in try catch

if Dataset depends on stateful operations (eg. random), you need to init the iterator before using it

eg. sess.run(iterator.initializer)

VARIABLES
creating a variable

use the tf.get_variable function, provide name and shape
my_variable = tf.get_variable('my_variable', [1, 2, 3])

you can optionally specify the dtype and initializer to tf.get_variable as well.

my_int_variable = tf.get_variable("my_int_variable", [1, 2, 3], dtype=tf.int32,
  initializer=tf.zeros_initializer)
by default, dtype is tf.float32 and initial value is randomized via tf.glorot_uniform_initializer
alternatively, you may init a tf.Variable to have the value of a tf.Tensor
eg.
other_variable = tf.get_variable('other_variable', dtype=tf.int32, initializer=tf.constant([23, 42])

Variable collections

collections are named lists of tensors or other objects, such as tf.Variable instances
by default, any tf.Variable gets put in two collections:
tf.GraphKeys.GLOBAL_VARIABLES - can be shared across multiple devices
tf.GraphKeys.TRAINABLE_VARIABLES - tensorflow uses these to calculate gradients

if you don't want a variable to be trainable, add it to tf.GraphKeys.LOCAL_VARIABLES collection
eg.

my_local = tf.get_variable('my_local', shape=(), collections=[tf.GraphKeys.LOCAL_VARIABLES])

alternatively, you can specify trainable=False as a parameter into tf.get_variable instead of putting it into the collection.

my_local = tf.get_variable('my_local', shape=(), collections=[tf.GraphKeys.LOCAL_VARIABLES])


you can make your own collection with any string.
to add a variable to a collection, just call tf.add_to_collection
eg.
tf.add_to_collection('my_collection', my_tf_var)


before using a variable, it must be initialized
eg.
session.run(tf.global_variables_initializer())

if you just want one single variable to be initted, use this:
eg.
session.run(my_variable.initializer)


print names of all variables which have not yet been initted:
print(session.run(tf.report_uninitialized_variables()))
