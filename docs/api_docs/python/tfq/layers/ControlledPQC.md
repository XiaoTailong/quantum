<div itemscope itemtype="http://developers.google.com/ReferenceObject">
<meta itemprop="name" content="tfq.layers.ControlledPQC" />
<meta itemprop="path" content="Stable" />
<meta itemprop="property" content="__call__"/>
<meta itemprop="property" content="__init__"/>
<meta itemprop="property" content="build"/>
<meta itemprop="property" content="compute_mask"/>
<meta itemprop="property" content="compute_output_shape"/>
<meta itemprop="property" content="count_params"/>
<meta itemprop="property" content="from_config"/>
<meta itemprop="property" content="get_config"/>
<meta itemprop="property" content="get_input_at"/>
<meta itemprop="property" content="get_input_mask_at"/>
<meta itemprop="property" content="get_input_shape_at"/>
<meta itemprop="property" content="get_losses_for"/>
<meta itemprop="property" content="get_output_at"/>
<meta itemprop="property" content="get_output_mask_at"/>
<meta itemprop="property" content="get_output_shape_at"/>
<meta itemprop="property" content="get_updates_for"/>
<meta itemprop="property" content="get_weights"/>
<meta itemprop="property" content="set_weights"/>
<meta itemprop="property" content="with_name_scope"/>
</div>

# tfq.layers.ControlledPQC

<!-- Insert buttons and diff -->

<table class="tfo-notebook-buttons tfo-api" align="left">

<td>
  <a target="_blank" href="https://github.com/tensorflow/quantum/tree/master/tensorflow_quantum/python/layers/high_level/controlled_pqc.py">
    <img src="https://www.tensorflow.org/images/GitHub-Mark-32px.png" />
    View source on GitHub
  </a>
</td></table>



Controlled Parametrized Quantum Circuit (PQC) Layer.

```python
tfq.layers.ControlledPQC(
    model_circuit, operators, **kwargs
)
```



<!-- Placeholder for "Used in" -->

The `ControlledPQC` layer is very similar to the regular `PQC` layer, but
with one major difference. The `ControlledPQC` layer requires the caller
of the layer to provide the control parameter inputs for `model_circuit`.
You can see how this works through a simple example:


```

>>> bit = cirq.GridQubit(0, 0)
>>> model = cirq.Circuit(
...     cirq.X(bit) ** sympy.Symbol('alpha'),
...     cirq.Z(bit) ** sympy.Symbol('beta')
... )
>>> outputs = tfq.layers.ControlledPQC(model, cirq.Z(bit))
>>> quantum_data = tfq.convert_to_tensor([
...     cirq.Circuit(),
...     cirq.Circuit(cirq.X(bit))
... ])
>>> model_params = tf.convert_to_tensor([[0.5, 0.5], [0.25, 0.75]])
>>> res = outputs([quantum_data, model_params])
>>> res
tf.Tensor(
[[-1.4901161e-08]
 [-7.0710683e-01]], shape=(2, 1), dtype=float32)

```


Just like with the `PQC` it is *very important* that the quantum datapoint
circuits do not contain any `sympy.Symbols` themselves (This can be
supported with advanced usage of the <a href="../../tfq/layers/Expectation.md"><code>tfq.layers.Expectation</code></a> layer). Just
like `PQC` it is possible to specify multiple readout operations and
switch to sample based expectation calculation:


```

>>> bit = cirq.GridQubit(0, 0)
>>> model = cirq.Circuit(
...     cirq.X(bit) ** sympy.Symbol('alpha'),
...     cirq.Z(bit) ** sympy.Symbol('beta')
... )
>>> outputs = tfq.layers.ControlledPQC(
...     model,
...     [cirq.Z(bit), cirq.X(bit), cirq.Y(bit)],
...     repetitions=5000)
>>> quantum_data = tfq.convert_to_tensor([
...     cirq.Circuit(),
...     cirq.Circuit(cirq.X(bit))
... ])
>>> model_params = tf.convert_to_tensor([[0.5, 0.5], [0.25, 0.75]])
>>> res = outputs([quantum_data, model_params])
>>> res
tf.Tensor(
[[-0.0028  1.     -0.0028]
 [-0.6956 -0.498  -0.498 ]], shape=(2, 3), dtype=float32)

```


A value for `backend` can also be supplied in the layer constructor
arguments to indicate which supported backend you would like to use.
A value for `differentiator` can also be supplied in the constructor
to indicate the differentiation scheme this `ControlledPQC` layer
should use. Here's how you would take the gradients of the
above example using a `cirq.Simulator` backend (which is slower
than `backend=None` which uses C++):


```

>>> bit = cirq.GridQubit(0, 0)
>>> model = cirq.Circuit(
...     cirq.X(bit) ** sympy.Symbol('alpha'),
...     cirq.Z(bit) ** sympy.Symbol('beta')
... )
>>> outputs = tfq.layers.ControlledPQC(
...     model,
...     [cirq.Z(bit), cirq.X(bit), cirq.Y(bit)],
...     repetitions=5000,
...     backend=cirq.Simulator(),
...     differentiator=tfq.differentiators.ParameterShift())
>>> quantum_data = tfq.convert_to_tensor([
...     cirq.Circuit(),
...     cirq.Circuit(cirq.X(bit))
... ])
>>> model_params = tf.convert_to_tensor([[0.5, 0.5], [0.25, 0.75]])
>>> with tf.GradientTape() as g:
...     g.watch(model_params)
...     res = outputs([quantum_data, model_params])
>>> grads = g.gradient(res, model_params)
>>> grads
tf.Tensor(
[[-3.1415927   3.1415927 ]
 [-0.9211149   0.02764606]], shape=(2, 2), dtype=float32)]

```


Lastly, like all layers in TensorFlow the `ControlledPQC` layer can be
called on any `tf.Tensor` as long as it is the right shape. This means
you could replace `model_params` in the above example with the outputs
from a `tf.keras.Dense` layer or replace `quantum_data` with values fed
in from a `tf.keras.Input`.

#### Attributes:

* <b>`activity_regularizer`</b>:   Optional regularizer function for the output of this layer.
* <b>`dtype`</b>
* <b>`dynamic`</b>
* <b>`input`</b>:   Retrieves the input tensor(s) of a layer.

  Only applicable if the layer has exactly one input,
  i.e. if it is connected to one incoming layer.

* <b>`input_mask`</b>:   Retrieves the input mask tensor(s) of a layer.

  Only applicable if the layer has exactly one inbound node,
  i.e. if it is connected to one incoming layer.

* <b>`input_shape`</b>:   Retrieves the input shape(s) of a layer.

  Only applicable if the layer has exactly one input,
  i.e. if it is connected to one incoming layer, or if all inputs
  have the same shape.

* <b>`input_spec`</b>
* <b>`losses`</b>:   Losses which are associated with this `Layer`.

  Variable regularization tensors are created when this property is accessed,
  so it is eager safe: accessing `losses` under a `tf.GradientTape` will
  propagate gradients back to the corresponding variables.
* <b>`metrics`</b>
* <b>`name`</b>:   Returns the name of this module as passed or determined in the ctor.

  NOTE: This is not the same as the `self.name_scope.name` which includes
  parent module names.
* <b>`name_scope`</b>:   Returns a `tf.name_scope` instance for this class.
* <b>`non_trainable_variables`</b>
* <b>`non_trainable_weights`</b>
* <b>`output`</b>:   Retrieves the output tensor(s) of a layer.

  Only applicable if the layer has exactly one output,
  i.e. if it is connected to one incoming layer.

* <b>`output_mask`</b>:   Retrieves the output mask tensor(s) of a layer.

  Only applicable if the layer has exactly one inbound node,
  i.e. if it is connected to one incoming layer.

* <b>`output_shape`</b>:   Retrieves the output shape(s) of a layer.

  Only applicable if the layer has one output,
  or if all outputs have the same shape.

* <b>`submodules`</b>:   Sequence of all sub-modules.

  Submodules are modules which are properties of this module, or found as
  properties of modules which are properties of this module (and so on).

  ```
  a = tf.Module()
  b = tf.Module()
  c = tf.Module()
  a.b = b
  b.c = c
  assert list(a.submodules) == [b, c]
  assert list(b.submodules) == [c]
  assert list(c.submodules) == []
  ```
* <b>`trainable`</b>
* <b>`trainable_variables`</b>:   Sequence of trainable variables owned by this module and its submodules.

  Note: this method uses reflection to find variables on the current instance
  and submodules. For performance reasons you may wish to cache the result
  of calling this method if you don't expect the return value to change.
* <b>`trainable_weights`</b>
* <b>`updates`</b>
* <b>`variables`</b>:   Returns the list of all layer variables/weights.

  Alias of `self.weights`.
* <b>`weights`</b>:   Returns the list of all layer variables/weights.



## Methods

<h3 id="__call__"><code>__call__</code></h3>

```python
__call__(
    inputs, *args, **kwargs
)
```

Wraps `call`, applying pre- and post-processing steps.


#### Arguments:


* <b>`inputs`</b>: input tensor(s).
* <b>`*args`</b>: additional positional arguments to be passed to `self.call`.
* <b>`**kwargs`</b>: additional keyword arguments to be passed to `self.call`.


#### Returns:

Output tensor(s).



#### Note:

- The following optional keyword arguments are reserved for specific uses:
  * `training`: Boolean scalar tensor of Python boolean indicating
    whether the `call` is meant for training or inference.
  * `mask`: Boolean input mask.
- If the layer's `call` method takes a `mask` argument (as some Keras
  layers do), its default value will be set to the mask generated
  for `inputs` by the previous layer (if `input` did come from
  a layer that generated a corresponding mask, i.e. if it came from
  a Keras layer with masking support.



#### Raises:


* <b>`ValueError`</b>: if the layer's `call` method returns None (an invalid value).

<h3 id="build"><code>build</code></h3>

```python
build(
    input_shape
)
```

Creates the variables of the layer (optional, for subclass implementers).

This is a method that implementers of subclasses of `Layer` or `Model`
can override if they need a state-creation step in-between
layer instantiation and layer call.

This is typically used to create the weights of `Layer` subclasses.

#### Arguments:


* <b>`input_shape`</b>: Instance of `TensorShape`, or list of instances of
  `TensorShape` if the layer expects a list of inputs
  (one instance per input).

<h3 id="compute_mask"><code>compute_mask</code></h3>

```python
compute_mask(
    inputs, mask=None
)
```

Computes an output mask tensor.


#### Arguments:


* <b>`inputs`</b>: Tensor or list of tensors.
* <b>`mask`</b>: Tensor or list of tensors.


#### Returns:

None or a tensor (or list of tensors,
    one per output tensor of the layer).


<h3 id="compute_output_shape"><code>compute_output_shape</code></h3>

```python
compute_output_shape(
    input_shape
)
```

Computes the output shape of the layer.

If the layer has not been built, this method will call `build` on the
layer. This assumes that the layer will later be used with inputs that
match the input shape provided here.

#### Arguments:


* <b>`input_shape`</b>: Shape tuple (tuple of integers)
    or list of shape tuples (one per output tensor of the layer).
    Shape tuples can include None for free dimensions,
    instead of an integer.


#### Returns:

An input shape tuple.


<h3 id="count_params"><code>count_params</code></h3>

```python
count_params()
```

Count the total number of scalars composing the weights.


#### Returns:

An integer count.



#### Raises:


* <b>`ValueError`</b>: if the layer isn't yet built
  (in which case its weights aren't yet defined).

<h3 id="from_config"><code>from_config</code></h3>

```python
@classmethod
from_config(
    cls, config
)
```

Creates a layer from its config.

This method is the reverse of `get_config`,
capable of instantiating the same layer from the config
dictionary. It does not handle layer connectivity
(handled by Network), nor weights (handled by `set_weights`).

#### Arguments:


* <b>`config`</b>: A Python dictionary, typically the
    output of get_config.


#### Returns:

A layer instance.


<h3 id="get_config"><code>get_config</code></h3>

```python
get_config()
```

Returns the config of the layer.

A layer config is a Python dictionary (serializable)
containing the configuration of a layer.
The same layer can be reinstantiated later
(without its trained weights) from this configuration.

The config of a layer does not include connectivity
information, nor the layer class name. These are handled
by `Network` (one layer of abstraction above).

#### Returns:

Python dictionary.


<h3 id="get_input_at"><code>get_input_at</code></h3>

```python
get_input_at(
    node_index
)
```

Retrieves the input tensor(s) of a layer at a given node.


#### Arguments:


* <b>`node_index`</b>: Integer, index of the node
    from which to retrieve the attribute.
    E.g. `node_index=0` will correspond to the
    first time the layer was called.


#### Returns:

A tensor (or list of tensors if the layer has multiple inputs).



#### Raises:


* <b>`RuntimeError`</b>: If called in Eager mode.

<h3 id="get_input_mask_at"><code>get_input_mask_at</code></h3>

```python
get_input_mask_at(
    node_index
)
```

Retrieves the input mask tensor(s) of a layer at a given node.


#### Arguments:


* <b>`node_index`</b>: Integer, index of the node
    from which to retrieve the attribute.
    E.g. `node_index=0` will correspond to the
    first time the layer was called.


#### Returns:

A mask tensor
(or list of tensors if the layer has multiple inputs).


<h3 id="get_input_shape_at"><code>get_input_shape_at</code></h3>

```python
get_input_shape_at(
    node_index
)
```

Retrieves the input shape(s) of a layer at a given node.


#### Arguments:


* <b>`node_index`</b>: Integer, index of the node
    from which to retrieve the attribute.
    E.g. `node_index=0` will correspond to the
    first time the layer was called.


#### Returns:

A shape tuple
(or list of shape tuples if the layer has multiple inputs).



#### Raises:


* <b>`RuntimeError`</b>: If called in Eager mode.

<h3 id="get_losses_for"><code>get_losses_for</code></h3>

```python
get_losses_for(
    inputs
)
```

Retrieves losses relevant to a specific set of inputs.


#### Arguments:


* <b>`inputs`</b>: Input tensor or list/tuple of input tensors.


#### Returns:

List of loss tensors of the layer that depend on `inputs`.


<h3 id="get_output_at"><code>get_output_at</code></h3>

```python
get_output_at(
    node_index
)
```

Retrieves the output tensor(s) of a layer at a given node.


#### Arguments:


* <b>`node_index`</b>: Integer, index of the node
    from which to retrieve the attribute.
    E.g. `node_index=0` will correspond to the
    first time the layer was called.


#### Returns:

A tensor (or list of tensors if the layer has multiple outputs).



#### Raises:


* <b>`RuntimeError`</b>: If called in Eager mode.

<h3 id="get_output_mask_at"><code>get_output_mask_at</code></h3>

```python
get_output_mask_at(
    node_index
)
```

Retrieves the output mask tensor(s) of a layer at a given node.


#### Arguments:


* <b>`node_index`</b>: Integer, index of the node
    from which to retrieve the attribute.
    E.g. `node_index=0` will correspond to the
    first time the layer was called.


#### Returns:

A mask tensor
(or list of tensors if the layer has multiple outputs).


<h3 id="get_output_shape_at"><code>get_output_shape_at</code></h3>

```python
get_output_shape_at(
    node_index
)
```

Retrieves the output shape(s) of a layer at a given node.


#### Arguments:


* <b>`node_index`</b>: Integer, index of the node
    from which to retrieve the attribute.
    E.g. `node_index=0` will correspond to the
    first time the layer was called.


#### Returns:

A shape tuple
(or list of shape tuples if the layer has multiple outputs).



#### Raises:


* <b>`RuntimeError`</b>: If called in Eager mode.

<h3 id="get_updates_for"><code>get_updates_for</code></h3>

```python
get_updates_for(
    inputs
)
```

Retrieves updates relevant to a specific set of inputs.


#### Arguments:


* <b>`inputs`</b>: Input tensor or list/tuple of input tensors.


#### Returns:

List of update ops of the layer that depend on `inputs`.


<h3 id="get_weights"><code>get_weights</code></h3>

```python
get_weights()
```

Returns the current weights of the layer.


#### Returns:

Weights values as a list of numpy arrays.


<h3 id="set_weights"><code>set_weights</code></h3>

```python
set_weights(
    weights
)
```

Sets the weights of the layer, from Numpy arrays.


#### Arguments:


* <b>`weights`</b>: a list of Numpy arrays. The number
    of arrays and their shape must match
    number of the dimensions of the weights
    of the layer (i.e. it should match the
    output of `get_weights`).


#### Raises:


* <b>`ValueError`</b>: If the provided weights list does not match the
    layer's specifications.

<h3 id="with_name_scope"><code>with_name_scope</code></h3>

```python
@classmethod
with_name_scope(
    cls, method
)
```

Decorator to automatically enter the module name scope.

```
class MyModule(tf.Module):
  @tf.Module.with_name_scope
  def __call__(self, x):
    if not hasattr(self, 'w'):
      self.w = tf.Variable(tf.random.normal([x.shape[1], 64]))
    return tf.matmul(x, self.w)
```

Using the above module would produce `tf.Variable`s and `tf.Tensor`s whose
names included the module name:

```
mod = MyModule()
mod(tf.ones([8, 32]))
# ==> <tf.Tensor: ...>
mod.w
# ==> <tf.Variable ...'my_module/w:0'>
```

#### Args:


* <b>`method`</b>: The method to wrap.


#### Returns:

The original method wrapped such that it enters the module's name scope.




