# Sample clients

Azure Machine Learning Hardware Accelerated Models implements part of the [Tensorflow Serving Predict API](https://github.com/tensorflow/serving/blob/r1.12/tensorflow_serving/apis/prediction_service.proto), which uses gRPC, an HTTP/2 RPC protocol. Specifically, we only support the `Predict` RPC call.  

## Provided clients
We provide sample clients for CSharp and Python. These clients are built on top of the code generated by the gRPC codegen tools.

### Using provided clients
For optimal performance, you should reuse the client object between calls.
#### C#
See `csharp\resnet` for a sample console application that uses the provided C# client.
#### Python
``` python
from client import PredictionClient
def run(ip_address, port, input_data):
    client = PredictionClient(ip_address, self.port, False, "")
        if isinstance(input_data, str):
            return client.score_image(input_data)
        if isinstance(input_data, np.ndarray):
            return client.score_numpy_array(input_data)
        return client.score_file(input_data.read())
```

## Generated Clients
You can also generate clients for your prefered language by using the gRPC codegen tools. For more information - especially on using the generated clients - consult the [gRPC documentation.](https://grpc.io/docs/
)

### Generating clients
#### Requirements
1. Git
2. gRPC and its prerequesites for your desired language. To install these, follow the [quickstart instructions](https://grpc.io/docs/) for the desired language.
#### Instructions
1. Clone [Tensorflow](https://github.com/tensorflow/tensorflow) and [Tensorflow-Serving](https://github.com/tensorflow/serving) to a directory. 
```
sample-clients> git clone https://github.com/tensorflow/tensorflow.git
sample-clients> git clone https://github.com/tensorflow/serving.git
```
2. Checkout the appropriate version of the dependencies. (r1.12)

```
sample-clients> cd tensorflow 
sample-clients/tensorflow> git fetch 
sample-clients/tensorflow> git checkout 1.12
sample-clients/tensorflow> cd ../serving
sample-clients/serving> git fetch 
sample-clients/serving> git checkout 1.12 
sample-clients/serving> cd ..
```
3. Run protoc with the grpc plugin to generate code for the desired language. Consult the GRPC docs for instructions on how to install and locate protoc and the grpc plugin.

This example is for Golang
```
sample-clients>protoc.exe -I tensorflow/ -I serving/ tensorflow/tensorflow/core/framework/tensor.proto tensorflow/tensorflow/core/framework/types.proto tensorflow/tensorflow/core/framework/resource_handle.proto tensorflow/tensorflow/core/framework/tensor_shape.proto --go_out=plugins=grpc:go
sample-clients>protoc.exe -I tensorflow/ -I serving/ serving/tensorflow_serving/apis/predict.proto serving/tensorflow_serving/apis/model.proto serving/tensorflow_serving/apis/prediction_service.proto --go_out=plugins=grpc:go
```

This will generate the go source code in `sample-clients\go`
### Using generated clients
Using the generated clients is going to vary based on language, however the basic flow is:
1. Create a connection with the remote server
2. Create a prediction client with this connection
3. Construct the tensor(s) for input
4. Construct the predict request from the tensors
5. Make an RPC call with the request using the client
6. Read the response and consume it

For an example of building on the generated code, look in `python/client.py`
