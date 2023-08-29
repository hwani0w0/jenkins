# Git Jenkins 와 쿠버네티스 2강
> data:23.08.29.

docker는 linux기반

dockerfile 개발이 아니라 환경을 만드는 것

jenkins를 버리지는 않을 것. 전통적.. 다양함..
gitlab 

- docker에서 기본으로 제공하는 jenkins/agent 이미지가 있음
```
docker run -it --name user1 jenkins/agent 
```

## Kubernetes
- ETCD : node 정보를 담은 db, 각 node의 cAdvisor가 정보를 보냄
- Controller Manager -> API Server -> Kubelet
- 정보를 저장, 명령을 처리, 모두 queue가 중요
- Service Account : 명령하는 주체, 보안, 권한 중요.
- Flannel : 서로 다른 노드의 pod간 통신을 위한 CNI 제공.
 - https://malwareanalysis.tistory.com/254

### Minikube
- k8s 기본 api port 8443
```
minikube status
minikube start
minikube ip
minikube service list
```
- docker network : 172.17.0.0/16

```
kubectl get storageclass
```

### helm
- 헬름차트
- yaml 파일로 변수가 들어갈 공간? 지정

```
helm repo add bitnami https://charts.bitnami.com/bitnami  # 기본. 주로 사용하는 helm repository bitnami
helm repo update # bitnami add 후 반드시 update
````


## Jenkins
### 구조
- 보통은 Master-Slave 구조로 운용
- Permanent Agent
- CI : Repository(Github) -> Jenkins -> Registry (Dockerhub)
- Jenkins를 컨테이너 상태로 활용한다면 반드시 Docker 설치해야함

- Master-Agent(Executor실행) 
- Executor : 빌드 실행 단위
- Job : Pipeline의 sub routine (stage)

### 설치
- java 기반, 환경 구성
```
yum list java*openjdk

yum -y install openjdk 11 설치
java -version
alternatives --config java

yum -y install git
```

- jenkins 초기 암호
8907e624c5ef47dcb160970f756ac609


  
### jenkins on docker 
- 컨테이너가 마스터 노드의 docker 권한을 갖을 수 있도록 jenkins 생성
- 아래와 같은 명령어로 jenkins를 설치해야 docker 이미지를 가져올 수 있음
```
docker run -u 0 --privileged --name jenkins -it -d -p 8080:8080 -p 50000:50000 \
-v /var/run/docker.sock:/var/run/docker.sock \
-v $(which docker):/usr/bin/docker \
-v /home/jenkins_home:/var/jenkins_home \
jenkins/jenkins:latest
```
