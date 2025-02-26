## Introduction
 基于Goalng、Docker和微服务思想实现了高并发、高性能和高可用的推荐系统推理微服务，包括多种召回/排序服务，并提供多种接口访问方式（REST、gRPC和Dubbo）等，每日可处理上千万次推理请求。   
 Large Scale Deep Learning Recommender Systems Inference Services / Microservices base on TFServing、Faiss 、Redis、Dubbo、Nacos、gRPC and Golang. This system provides real-time inference services(Dubbo api、gPRC api and REST api),which can withstand millions of inference requests per day.


## Dependent Components    
The model inference microservices based on deep learning mainly uses the following components:
| Type          | Component          | Description                                                                                                                       |
| ------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| Data          | Hive / Spark       | ETL millions users's behavior data and then build the feature data warehouse.                                                     |
|               | Redis              | Save the training samples in TFRecord format and store them in Redis Cluster.                                                     |
| Model         | TensorFlow         | Training deep learning recall / rank model , alse you can use other deep learning framework ,but need save models as *.pb format. |
|               | TensorFlow Serving | Deploy models and provide a grpc service.                                                                                         |
|               | FAISS              | Quick ANN search thousands items from millions items.                                                                             |
| Microservices | Nacos              | Manage config files and services.                                                                                                 |
|               | Dubbo              | Build dubbo protocol RPC services and register them to Nacos.                                                                     |
|               | Hystrix            | How to distribute traffic during peak traffic (Latency and Fault Tolerance).                                                      |
|               | Skywalking         | Record the time spent on each request.                                                                                            |
| Deploy        | Docker             | Docker containerization deployment services.                                                                                      |
|               | Kubernetes         | Manage dockers and monitor the resource consumption of each service, such as memory and CPUs.                                     |
|               | Nginx、Apisix      | API gateway.                                                                                                                      |


## Architecture
The core components of model inference microservices are as follows：
| Type     | Component                                                                                                                                    | Description                                                                               |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Feature  | [feature engineering](https://github.com/solidglue/Recommender_System_Inference_Services/tree/master/pkg/infer_features)                     | user offline、user realtime、user seq features and item features.                         |
| Sample   | [recall/rank samples](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_samples)                 | create TFRcords format samples.                                                           |
| Recall   | [cf recall](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_models/recall/cf)                  | user cf 、 item cf and swing.                                                             |
|          | [dssm recall](https://github.com/solidglue/Recommender_System_Inference_Services/blob/master/pkg/infer_models/recall/u2i/u2i_dssm_recall.go) | recall from dssm model and faiss index.                                                   |
|          | [simple recall](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_models/recall/simple_recall)   | rules recall, such as hot items recall.                                                   |
|          | [cold start](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_models/recall/cold_start)         | new users and new items cold start.                                                       |
| Rank     | [pre_ranking](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_models/pre_ranking)              | thousands items pre_ranking after recall .                                                |
|          | [ranking](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_models/ranking)                      | hundreds items ranking after pre_ranking.                                                 |
|          | [re_rank](https://github.com/solidglue/Recommender_System_Inference_Services/tree/master/pkg/infer_models/re_rank)                           | hundreds items re_ranking after ranking .                                                 |
| Services | [config loader](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/pkg/config_loader)                       | Sparse service's start config from Naocs, such as grpc info 、 redis info and index info. |
|          | [dubbo service](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/blob/master/pkg/infer_services/dubbo_service)        | dubbo protocol service.                                                                   |
|          | [gRPC service](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_services/grpc_service)          | grpc protocol service.                                                                    |
|          | [rest service](https://github.com/solidglue/RecommenderSystems-Inference-Microservices/tree/master/pkg/infer_services/rest_service)          | restful service.                                                                          |
| APIs     | [dubbo api](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/api/dubbo_api)                               | provide dubbo protocol api.                                                               |
|          | [gRPC api](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/api/grpc_api)                                 | provide gRPC protocol api.                                                                |
|          | [rest api](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/api/rest_api)                                 | provide http protocol api.                                                                |
| Web      | [web](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/pkg/web)                                           | services manage and Service monitor page.                                                 |
| Deploy   | [faiss](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/scripts/deployments/faiss)                       | faiss index service deploy.                                                               |
|          | [tfserving](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/scripts/deployments/tfserving)               | tensorflow model deploy.                                                                  |
|          | [infer](https://github.com/beachdogs/RecommenderSystems-Inference-Microservices/tree/master/scripts/deployments/infer)                       | recommend system infer deploy.                                                            |


## Services Deploy
    Docker
    Kubernetes 
    Nginx
    Apisix
    ELK


## *扩展

1.**推荐系统**  
王树森推荐系统公开课 - 基于小红书的场景讲解工业界真实的推荐系统。  
●  [**Recommender_System**](https://github.com/solidglue/Recommender_System) 

2.**YouTuBe推荐系统排序模型**  
以"DNN_for_YouTube_Recommendations"模型和电影评分数据集（ml-1m）为基础，详尽的展示了如何基于TensorFlow2实现推荐系统排序模型。  
●  [**YouTube深度排序模型(多值embedding、多目标学习)**](https://github.com/solidglue/DNN_for_YouTube_Recommendations) 

3.**机器学习 Sklearn入门教程**  
●  [**机器学习Sklearn入门教程**](https://github.com/solidglue/Machine_Learning_Sklearn_Examples)  

4.**深度学习TensorFlow入门教程**  
●  [**深度学习TensorFlow入门教程**](https://github.com/solidglue/Deep_Learning_TensorFlow2_Examples)  
