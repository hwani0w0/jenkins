# Git Jenkins 와 쿠버네티스 1강
> data:23.08.28.

젠킨스 docker 기반(CI) / kubernetes 기반(CD)

minikube.ovf : Docker minikube 기본으로 설치됨
jenkins ci/cd tool
git-zara(협업툴) repo 수정사항 알림

ci/cd why?
> cloud native, container기반 개반 ex) 국내 5G 모두 가상화기반 
> 핵심기술(4) : MSA, CICD, Container, DynamicManagement

cd : jenkinsX tekton(cloud native 최적화) circleCI

컨테이너 : 사이즈가 작고 이식성이 뛰어남 (손쉽게 배포) bc. docker engine 모두 사용
<-> 기존 hypervisor 컨버터가 필요함
클러스터 : 자원 균등하게 배분되도록 모아둔 것
쿠버클러스터 l4 설정을 잘해줘야 jenkins 활용이 원활

jenkinsfile : agent 지정 가능 source code 가져고오 compile 할 수 있음

dockerfile 가장 중요
컨테이너 성능관리 observality prometheus grafana

manifest repo : yaml 파일 포맷 만들고, 바꿔줄 변수들 관리하는게 helm

결국 네트워킹.. 보안..이 어렵다.
controlplane-(management line, network)-data
> jenkins에 비해 / tekton이 상호연동성이 뛰어남 ecosystem

배포할 용환경을 만들어두고 cicd를 구성해야..

CI Jenkins
> 규모가 크면 agent 필수
> agent pod로 실행


dockercompose = maifest.yaml (helm chart)

# Jenkins 소개
> 웹을 통한 통합관리 > CasC 적용 시 menu를 짜서 code로 설정 적용 가능
> Jenkins on Kubernetes : 주기적인 업데이트
1 jenkins plugin
2 manage node
3 configure system > prometheus 연동

## 1. Plugin Manager
### 1.1 CasC 
- Plugin : 젠킨스 연동 github-oauth:0.35
- Agent :
- Job


# 2. Git
https://git-scm.com/downloads
git config --user.name " "
git config --user.email " "




root / mode1752

kubernete master node 안에  jenkins/agent 설치 > kubernetes agent 통신




=======================================


# GitLab

## github와 차이?
 - 오프라인에서도 쓸 수 있음
 - cicd pipeline 지원
 - agent X / runner O
 - 오픈소스

multipass : https://multipass.run/install





















