# RecommenderSystems Inference Microservices
    DeepLearning Based Recommender Systems Infer Microsevices for Golang.

## Dependent Components    
The model inference microservices based on deep learning mainly uses the following components:
| Type | Component | Description |
| --- | --- | --- |
| Data | Hive / Spark | ETL millions users's behavior data and then build the feature data warehouse. |
|| Redis |  Save the training samples in TFRecord format and store them in Redis Cluster. |
| Model | TensorFlow | Training deep learning recall / rank model , alse you can use other deep learning framework ,but need save models as *.pb format. |
|| TensorFlow Serving | Deploy models and provide a grpc service. |
||FAISS | Quick search thousands items from millions items. |
| Microservices | Nacos | Manage config files and services. |
|| Dubbo | Build dubbo protocol RPC services and register them to Nacos. |
|| Hystrix | How to distribute traffic during peak traffic (Latency and Fault Tolerance). |
|| Skywalking | Record the time spent on each request. |
|Deploy| Docker  | Docker containerization deployment services. |
|| Kubernetes  | Manage dockers and monitor the resource consumption of each service, such as memory and CPUs. |
||  Nginx、Apisix | API gateway. |



## Architecture
The core components of model inference microservices are as follows：
| Type | Component | Description |
| --- | --- | --- |
| Sample | [Recall Samples](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/cores/model/basemodel) | Search recall TFRcords format samples from redis cluster, such as dssm model. |
|| [Rank Samples](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/cores/model/deepfm) |  Search recall TFRcords format samples from redis cluster, such as deepfm model. |
| Recall | [Get embedding vector](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/cores/model/dssm) | Search user's/item's embedding vector from recall model which deployed by tfservig(grpc sevice) , input data is recall samples. |
|  | [Search index](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/cores/faiss) | Quick search thousands items from faiss index (millions items) service(grpc sevice) . |
| Rank | [Rank](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/cores/model/deepfm)  | Rank input items by rank model  which deployed by tfservig(grpc sevice) . |
| Service | [Config Loader](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/cores/service_config_loader) | Sparse service's start config from Naocs, such as grpc info 、 redis info and index info. |
|  | [Register Services](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/blob/master/apis/dubbo/server/dubbo_server_runner.go) | Register services to Nacos. |
|  | [Update Services](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/cores/nacos_config_listener) | Update services when nacos config files have changed, such as grpc info 、 redis info or index info. |
|API| [Dubbo](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/apis/dubbo) |Provide Dubbo protocol APIs. |
|| [gRPC](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/apis/grpc) |Provide gRPC protocol APIs. |
|| [REST](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/apis/rest) |Provide Http protocol APIs. |
|Web| [Web](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/web) |Services manage and Service monitor page. |
|Deploy| [Faiss](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/deploy/faiss) |Faiss index service deploy. |
|| [TFServing](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/deploy/tfserving) |Tensorflow model deploy. |
|| [Infer](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/deploy/infer) |Recommend System infer deploy. |




## Services Deploy
    Docker
    Kubernetes 
    Nginx
    Apisix
