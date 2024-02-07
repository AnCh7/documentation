## ONNX

> https://onnx.ai/onnx/intro/concepts.html
> https://github.com/onnx/onnx

Open standard for machine learning interoperability.

Open Neural Network Exchange.

ONNX can be compared to a programming language specialized in mathematical functions. It defines all the necessary operations a machine learning model needs to implement its inference function with this language.

It can be also represented as a graph that shows step-by-step how to transform the features to get a prediction - **ONNX graph**.

ONNX Operators: Add, Sub, MatMul, Transpose, Greater, IsNaN, Shape, Reshape etc.

```
r = onnx.MatMul(x, a)
y = onnx.Add(r, c)
```

ONNX uses *protobuf* to serialize the graph into one single block.

ONNX offers the possibility to store additional data in the model itself.

ONNX specifications are optimized for numerical computation with tensors. A *tensor* is a multidimensional array.

ONNX implements tests and loops.

Converters:

- [sklearn-onnx](https://onnx.ai/sklearn-onnx/): converts models from [scikit-learn](https://scikit-learn.org/stable/),
- [tensorflow-onnx](https://github.com/onnx/tensorflow-onnx): converts models from [tensorflow](https://www.tensorflow.org/),
- [onnxmltools](https://github.com/onnx/onnxmltools): converts models from [lightgbm](https://lightgbm.readthedocs.io/), [xgboost](https://xgboost.readthedocs.io/en/stable/), [pyspark](https://spark.apache.org/docs/latest/api/python/), [libsvm](https://github.com/cjlin1/libsvm)
- [torch.onnx](https://pytorch.org/docs/master/onnx.html): converts model from [pytorch](https://pytorch.org/).

