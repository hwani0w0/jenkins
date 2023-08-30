# Git Jenkins 와 쿠버네티스 3강
> data:23.08.30.

## Jenkins on Kubernets
- kind : k8s utility 제공, cluster 만듬
- docker agent : port 2375 
- k8s agent : port 58350

### 1. kind 설치
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
./kind
```

### 2. cluster 구성
```
wget https://raw.githubusercontent.com/darinpope/ansible-jenkins/main/centos/7/005-controller-only/jenkins-config.yaml
# node ip 변경
vi jenkins-config.yaml 

./kind create cluster --config jenkins-config.yaml 
kubectl cluster-info --context kind-kind
```

### 3. jenkins - k8s agent 구성
- namespace는 secrete 한 공간
- key, configuration 등 저장
- service account 로 pod 생성 등 control 권한을 갖기 위해서 key 필요
- service account 컴포넌트간 이동할수 있는?
- ns 생성시 자동으로 key 가 생성됨
```
# sa 생성
kubectl create namespace jenkins
kubectl create serviceaccount jenkins --namespace=jenkins

# token 읽기
kubectl describe secret $(kubectl describe serviceaccount jenkins --namespace=jenkins | grep Token | awk '{print $2}') --namespace=jenkins

# sa 권한 주기
kubectl create rolebinding jenkins-admin-binding --clusterrole=admin --serviceaccount=jenkins:jenkins --namespace=jenkins
```
1) jenkins manager > clouds > New cloud > kubernetes 
 * kubernetes plugin 설치
2) kind url 입력 https://{agent node ip}:58350
3) Namespace : jenkins
4) Disable https certificate check / Websocket 체크
5) Credential Add : secrete text 에 token 입력

### 4. pipeline 
#### 4.1 gradle 설치
```
wget https://services.gradle.org/distributions/gradle-7.4.2-bin.zip
unzip gradle-7.4.2-bin.zip
mv gradle-7.4.2 /opt/gradle/
vi /etc/profile.d/gradle.sh
```
```
# /etc/profile.d/gradle.sh

export GRADLE_HOME=/opt/gradle
export PATH=${GRADLE_HOME}/bin:${PATH}
```
```
chmod +x /etc/profile.d/gradle.sh
source /etc/profile.d/gradle.sh
gradle -v
```
```
git clone https://github.com/leszko/calculator.git
```

#### 4.2 springboot
- https://start.spring.io/
- add dependecies : spring web > generate


## 운영
### 1. storage
- plugin : CloudBees Disk Usage SimpleVersion

### 2. prometheus
- plugin : Prometheus metrics plugin
- 프로메테우스 환경파일
```
wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml
vim prometheus.yml
```
```vim
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  scrape_timeout: 10s  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "jenkins"
    metrics_path: /prometheus/
    # scheme defaults to 'http'.
    static_configs:
      - targets: ["{jenkins ip}:8080"]
```
- 프로메테우스 접속` localhost:9090 ` 접속 > 지구본 `jenkins_executor_count_value` Execute

### 3. Grafana
- 그라파나 실행
```
docker run -d -p 3000:3000 --name grafana grafana/grafana
```
- 그라파나 접속 : ` http://localhost:3000 `
- search ` import dashboard ` > import `9964` Load > DataSource ` http://{node ip}:9090 `
- 대시보드 확인
   
   
   
## Jenkins on Kubernetes
- **vagrant**, **Virtualbox` 설치
- `multipass list/stop/delete` ??
- 처음 작업할때는... 작업경로에서 powershell 터미널 open > `vagrant init` > `vagrant up`

- vm 생성할때는...
> `git clone https://github.com/sysnet4admin/_Book_k8sInfra.git`
> `cd C:\Users\edu\Documents\6.28\_Book_k8sInfra\ch3\3.1.3` 
> `vagrant up`
> Vagrantfile 에 따라 vm 생성함 (master 1개, worker 3개)

- root / vagrant


- worker node 연결 검증
```
scp root@192.168.1.10:/etc/kubernetes/admin.conf .
kubectl get nodes --kubeconfig admin.conf
```
- windows : https://microk8s.io/ *helm 따로 설치할 필요 없음
- minikube는 helm 설치해야함
