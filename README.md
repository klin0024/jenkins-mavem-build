# Overview
![image](img/overview.JPG?raw=true "Overview") <br />
<br />
## 使用說明

### 1. 創建一個 pvc 存放 mavem repository
$ kubectl apply -f maven-repo-pvc.yaml -n < jenkins namespace > <br />
### 2. 創建一個 cm 做為 Dockerfile
$ kubectl apply -f dockerfile-cm.yaml -n < jenkins namespace > <br />
### 3. 設定 Jenkinsfile內的參數
##### 參數說明:
maven_repo_pvc: 存放 mavem repository 的 pvc 名稱, 預設: 'maven-repo-pvc' <br />
docker_config_map: 做為 Dockerfile cm 名稱, 預設: 'dockerfile' <br />
def dockerfile='Dockerfile' <br />
git_url: source code git的來源 <br />
git_branch: source code git的分支 <br />
image_registry: image推送置的註冊表 <br />
image_repository: image推送置的倉庫 <br />
image_tag: image標籤 <br />
### 4. 執行Jenkins Pipeline
### 5. 拉取image進行測試
$ docker run -it --rm < image_registry >/< image_repository >:< image_tag > <br />