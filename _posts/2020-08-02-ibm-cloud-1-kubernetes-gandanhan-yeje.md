---
layout: post
title: 【IBM Cloud】쿠버네티스 서비스와 Kubernetes 배포 하기
date: 2020-08-02 15:18:00
categories: dev
---

# [IBM Cloud] IBM클라우드 쿠버네티스 서비스와 Kubernetes 배포 하기

# 시작하기 전에

-  [Kubernetes Service](https://gyutaehan.github.io/dev/2020/08/02/kubernetes-service-create/) 
  - Git
  - Docker



# node서버 구축

도커에서 실행하기 위한 노드서버 실행을 위한 `server.js`  와 `package.json`, `Dockerfile`  을 작성을 합니다.

### app/server.js

```javascript
import * as http from 'http';

const port = 3001;
const hostname = '0.0.0.0'

http.createServer((req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello, Node!\n');
}).listen(port, hostname, console.log(`Server running at http://${hostname}:${port}/`)
);
```

### app/package.json

```json
{
  "name": "hello-node",
  "version": "1.0.0",
  "description": "nodejs server",
  "type": "module",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "gyutae.h",
  "license": "ISC"
}
```

## app/Dockerfile

````dockerfile
FROM node:12

WORKDIR /app

COPY package*.json .
RUN npm install

COPY . .

EXPOSE 3001 

CMD ["node", "server.js"]

````

`Dockerfile`은 빌드를 하면 도커 이미지를 만들 수 있는데, 그 이미지에 대한 설정을 기술한 **파일**입니다.

명령어들에 관해선 다른 포스트에서 더욱 자세히 다룰 예정입니다.



## 빌드를 해보고 실행해보기

````bash
$ docker build -t hellonode:v0 .
$ docker run -p 3001:3001 hellonode:v0
````



[http://localhost:3001](http://localhost:3001)에 접속을 하면 다음과 같은 화면이 나오는 것을 확인할 수 있습니다.

```
Hello, Node!
```



## 도커 허브 리포지토리에 푸시하기

```shell
# 만약 docker계정이 kantmuyu라면, <docker 계정> 대신 kantmuyu가 들어가면 됩니다.
$ docker tag hellonode:v0 <docker 계정>/hellonode:v0
$ docker push <docker 계정>/hellonode:v0
```

쿠버네티스와의 연동을 위해 본인의 도커 허브 리포지토리에 이미지를 푸시해줍니다.
푸시가 되었는지 확인은 [Docker Hub](https://hub.docker.com/)에서 할 수 있습니다.



# 쿠버네티스와 연동하기

```shell
# 네임스페이스 생성
$ kubectl create namespace hellonode-ns
```

생성을 했다면 `get namespace`명령어로 생성이 되었는지 확인이 가능합니다.

```shell
# namespace 리스트 확인
$ kubectl get namespace
```

hellonode-ns라는 namespace가 생성된 것을 확인할 수 있습니다.

```shell
# output
$ kubectl get namespace
NAME              STATUS   AGE
default           Active   46m
hellonode-ns      Active   5s
ibm-cert-store    Active   42m
ibm-operators     Active   42m
ibm-system        Active   45m
kube-node-lease   Active   46m
kube-public       Active   46m
kube-system       Active   46m
```

## 파드 생성하기

`파드`란 쿠버네티스에서 가장 작고 기본적인 배포 가능한 객체입니다. 파드에는 하나 이상의 컨테이너가 존재하며 그 컨테이너들은 파드 내의 같은 자원을 공유합니다.

```yaml
# k8s/hellonode-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: hellonode-pod
  labels:
    app: app-hellonode
spec:
  containers:
  - name: hellonode-pod
    image: <docker 계정>/hellonode:v0
    ports:
    - containerPort: 3001
```

도커허브에 있는 다른 이미지를 이용하려면 `spec.containers.image`에 `kantmuyu/hellonode:v0`처럼 도커허브 리포지토리 이름을 적어 넣으면 됩니다. 
`kantmuyu`는 제 도커 아이디이고 `hellonode:v0`는 리포지토리 이름입니다.


---
> ##### ※apiVersion
>
> - APIGROUP에 속해있는 경우 `apiVersion: <APIGROUP>/<APIVERSION>`
> - APIGROUP에 속해있지 않는 경우 `apiVersion: v1`
>
> `kubectl api-resources` 명령어를 이용하여 API그룹에 속해있는지 확인이 가능하고
>
> `kubectl api-versions`명령어를 이용하여 API그룹의 버전을 확인할 수 있습니다.
>
> ```shell
> $ kubectl api-resources
> NAME                              SHORTNAMES   APIGROUP                       NAMESPACED   KIND
> ... 중략
> controllerrevisions                            apps                           true         ControllerRevision        
> daemonsets                        ds           apps                           true         DaemonSet
> deployments                       deploy       apps                           true         Deployment
> replicasets                       rs           apps                           true         ReplicaSet
> ...
> ```
>
> Pod는 API그룹에 속해 있지 않고, Deployment는 `apps`라는 API그룹에 속해있는 것을 알 수 있습니다.
>
> ```shell
> $ kubectl api-versions
> ...중략
> apps/v1
> authentication.k8s.io/v1
> ...
> ```
>
> apps API그룹에서 이용가능한 버전은 `apps/v1`가 표시되어 나옵니다.
>
> Pod는 속해 있지 않기 때문에 `v1`
>
> Deployment는 `apps/v1`가 사용가능합니다. 
---

`kubectl apply -f`명령어를 이용하여 만든 파일을 이용해 파드를 생성합니다.
```shell
# kubectl apply -f 를 이용하여 flask 네임스페이스를 pod에 배포
$ kubectl apply -f hellonode-pod.yaml -n hellonode-ns
# 조금 기다려보면 pod은 클러스터에서 실행(Running)이 되어 있음을 확인할 수 있습니다.
$ kubectl get pod -n hellonode-ns
```

```shell
# Output
NAME            READY   STATUS    RESTARTS   AGE
hellonode-pod   1/1     Running   0          4s
```



STATUS가 Running상태가 되면 `port-forward`를 이용하여 로컬에 접근하기 위한 포트포워딩을 해줍니다.

```shell
# 내부 3001포트와 로컬 3001포트를 포트포워딩
$ kubectl port-forward pods/hellonode-pod -n hellonode-ns 3001:3001
```



마찬가지로 [http://localhost:3001](http://localhost:3001)에 접속을 하면 다음과 같은 화면이 나오는 것을 확인할 수 있습니다.

```
Hello, Node!
```





```shell
# pod 리소스를 확인
$ kubectl describe pod -n hellonode-ns
# pod 삭제
$ kubectl delete pod hellonode-pod -n hellonode-ns
```





## 디플로이먼트 생성하기

`디플로이먼트`란 동일한 여러 파드의 집합을 말하며, 여러 복제본을 실행하고 어떤 파드가 실패하거나 응답이 없을 때 자동으로 교체를 해줍니다.

```yaml
# k8s/hellonode-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hellonode-deployment
  labels:
    app: app-hellonode
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app-hellonode
  template:
    metadata:
      labels:
        app: app-hellonode
    spec:
      containers:
      - name: hellonode
        image: <docker 계정>/hellonode:v0
        ports:
        - containerPort: 3001
```

`spec.replicas`는 파드수를 뜻하며, 여기서는 두 개의 파드를 생성합니다.

```shell
# 위 pod와 마찬가지로 kubectl apply -f 를 이용하여 적용시킨다.
$ kubectl apply -f hellonode-deployment.yaml -n hellonode-ns
```



```shell
# 조금 후 kubectl get deploy를 이용하여 실행되고 있는 deploy를 확인
$ kubectl get deploy -n hellonode-ns
```

```shell
# output
NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
hellonode-deployment   2/2     2            2           21s
```



deployment controller에 의해 관리되고 있는 파드들도 `get pod`로 확인이 가능합니다.

```shell
$ kubectl get pod -n hellonode-ns
```

두 개의 동일한 파드가 생성된 것을 확인할 수 있습니다.
```shell
# Output
NAME                                    READY   STATUS    RESTARTS   AGE
hellonode-deployment-78cf6785dc-nwvml   1/1     Running   0          100s
hellonode-deployment-78cf6785dc-qdt7j   1/1     Running   0          100s
```



파드와 마찬가지로 로컬에 접근하기 위해 포트 포워딩을 해줍니다.

```shell
$ kubectl port-forward deployment/hellonode-deployment -n hellonode-ns 3001:3001
```



마찬가지로 [http://localhost:3001](http://localhost:3001)에 접속을 하면 다음과 같은 화면이 나오는 것을 확인할 수 있습니다.

```
Hello, Node!
```





## 서비스 생성하기

`서비스`는 각각의 파드 엔드포인트를 단일 리소스로 그룹화를 해줍니다. 기본적으로 모든 파드는 임시 클러스터 내부 IP주소가 할당되는데 그것들을 고정된 IP로 그룹화해줌으로써 파드간의 로드밸런싱 지원이 가능합니다.

```yaml
# k8s/hellonode-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: hellonode-service
  labels:
    app: app-hellonode
spec:
  type: NodePort 
  ports:
  - port: 80
    targetPort: 3001
    protocol: TCP
  selector:
    app: app-hellonode
```

> ### ServiceTypes
>
> - `ClusterIP`: 서비스타입의 디폴트값으로 서비스를 클러스터-내부 IP에 할당합니다. `클러스터 내부`에서만 서비스에 접속할 수 있습니다.
> - `NodePort`:  < NodeIP >:< NodePort >에 접속하여 `클러스터 외부`에서도 서비스에 접속할 수 있습니다.
> - `LoadBalancer`: 클라우드 공급자의 로드 밸런서를 사용하여 서비스를 외부에서도 접속이 가능합니다. `IBM Cloud Kubernetes Service 무료티어`에서는 사용이 `불가능`합니다.
> - `ExternalName`: 서비스를 `externalName` 필드의 콘텐츠 (예:`foo.bar.example.com`)에 포워딩해준다.



```shell
# pod와 deploy와 마찬가지로 apply -f 를 이용하여 적용시킨다.
$ kubectl apply -f hellonode-service.yaml -n hellonode-ns
```

클라우드 로드 밸런서를 프로비저닝하기 위해서 포드나 디플로이에 비해서 조금 시간이 걸립니다.
경과를 확인하기 위해선 `get service`명령어로 확인이 가능합니다.

```shell
$ kubectl -n hellonode-ns get service -w
# service 대신 svc도 됨.
```

```shell
# output
NAME                TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hellonode-service   NodePort   <클러스터 IP>   <none>        80:31031/TCP   17s
```

포트에 `80:31031`부분중 `31031`이 `노드포트`입니다. 내부 클러스터 80번 포트를 외부에서도 접속이 가능하도록 31031포트에 포트포워딩을 한 것입니다.

```shell
$ kubectl get node -o wide
```

```shell
# output
NAME      STATUS   ROLES    AGE    VERSION     INTERNAL-IP   EXTERNAL-IP       OS-IMAGE        KERNEL-VERSION      CONTAINER-RUNTIME
<내부 IP>   Ready   <none>   101m   v1.17.9+IKS   <내부 IP>     <외부 IP>   Ubuntu 16.04.6 LTS   4.4.0-185-generic   containerd://1.3.4
```



http://<외부 IP>:<노드포트>에 접속하여 접속이 되는지 확인합니다.

```
Hello, Node!
```


## IBM Cloud 대시보드에서 확인하기

[IBM Cloud](https://cloud.ibm.com/)에 접속하여 `대시보드`에서 만들었던 `클러스터`에 접속하여 쿠버네티스 대시보드버튼을 클릭합니다.
![kuber2-1](http://localhost/content/images/2020/08/kuber2-1.png)

위 `네임스페이스`를 `hellonode-ns`로 바꿔준다음, `좌측 메뉴`에서 만든 `파드`, `디플로이`, `서비스`가 있는지 확인하면 됩니다.
![kuber3-2](http://localhost/content/images/2020/08/kuber3-2.png)


## 예제 코드

- https://github.com/trapkka997/node-docker-k8s.git


## 참고
- [쿠버네티스 공식 문서](https://kubernetes.io/)
- [Getting Started with Containers and Kubernetes: A DigitalOcean Workshop Kit](https://www.digitalocean.com/community/meetup_kits/getting-started-with-containers-and-kubernetes-a-digitalocean-meetup-kit)