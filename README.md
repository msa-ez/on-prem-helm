# Installing on-premise MSA-Easy

### 1. Clone helm Charts 
    git clone https://github.com/msa-ez/on-prem-helm.git
---
### 2. Create & Connect Kubernetes Cluster
---
### 3. helm chart 기반 MSA-Easy 설치 
  - Cluster ip: ```kubectl cluster-info```
  - Domain: DNS 서비스에서 보유한 도메인 주소 
  - token: ```kubectl describe secret default```
    #### 1. yaml 파일 별 수정 필요 내용 
    - <b>deployment.yaml</b>: Cluster ip, Domain, token
    - <b>values.yaml</b>: Cluster ip, Domain
    - <b>2q</b>: Domain  
    - <b>ingress.yaml</b>: Domain 
    - <b>issuer.yaml</b> Domain
      * 변경 후 <b>issuer.yaml</b> 전체내용 주석 
    #### 2. helm install on-prem . 
      * on-prem install 후 <b>issuer.yaml</b> 주석 제거
    #### 3. Apply issuer, secrets.yaml 
        kubectl apply -f issuer.yaml, secrets.yaml
    #### 4. ingress-nginx 설치
    ```
      helm upgrade --install ingress-nginx ingress-nginx \
        --repo https://kubernetes.github.io/ingress-nginx \
        --namespace ingress-nginx --create-namespace
    ```
---
### 4. DNS 서비스 도메인 record 생성 & 설정
  #### 1. Create Record 
  ![createRecord1](https://user-images.githubusercontent.com/65217813/192461326-ad37d114-cc4e-4fb8-8813-f45270e31c7d.png)
   - gitlab, kas, minio, registry, *, api, www, file, acebase 각 Record 생성 
    <img width="1007" alt="onprem_createRecord" src="https://user-images.githubusercontent.com/65217813/192455799-0f3300f6-7fd9-4ef6-8665-8b5fbafb9831.png">
  #### 1. edit record(gitlab, kas, minio, registry)
   - 등록할 IP: ```kubectl get ing```
   ![getIngress](https://user-images.githubusercontent.com/65217813/192466549-3336cd69-9a73-440a-843b-0711822f371d.png)
   - IP 수정
   ![editIngressRecord](https://user-images.githubusercontent.com/65217813/192468220-9a1670b3-9ec3-4ffe-98c7-7dc86c6e1778.png)
  #### 2. edit record(*, api, www, file, acebase)
   - 등록할 IP: ```kubectl get svc -n ingress-nginx```/ external-ip 
   - 위와 동일한 방법으로 수정 
---
### 5. ide 관련 operator 설치
  #### 1. Operator 설치
    cd ide-operator
    make deploy IMG=gcr.io/eventstorming-tool/theia-ide-lab:v137
---
### 6. NFS 설치
  #### 1. <b>values.yaml/disk</b> 수정(클러스터에 연결할 disk 명으로 수정)
  #### 2. nfs 설치 
    cd gcp_helm
    helm install gcp-msaez .
---
