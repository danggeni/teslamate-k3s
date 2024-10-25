# teslamate-k3s
k3s 및 k8s에서 사용 가능한 teslamate deployment 및 statefulset yml 파일을 제공합니다. 현재 작성기준(2024.10.25) 기준으로 teslamate 1.30.버전 기준으로 편집되었습니다.

## Reference
 - teslamate  [https://docs.teslamate.org](https://docs.teslamate.org/)

## Install

step1. k3s 설치 환경 필요 

step2. k3s 및 k8s git clone
```shell
    git clone https://github.com/danggeni/teslamate-k3s.git
```

step3. 폴더 이동 및 namespace 생성
```shell
    cd k3s-deployment
    
    kubectl create ns teslamate
```

step4. 01.teslamate.yaml 및 00.database.yml 버전 확인 및 수정(작성기준 teslamate 1.30.1, postgresql 16)
```shell
    vi 00.database.yml
    vi 01.teslamate.yml
```

step5. k3s-deployment 배포
```shell
    kubectl apply -f .
```

step6. k3s-deployment 배포 상태 확인
```shell
    kubectl get pods -n teslamate
```

step7. Teslamate Service NodePort 지정
- 01.grafana.yaml 파일의 vi 또는 에디터를 통해 열고 spec부분의 type: NodePort 추가
- ports의 nodePort: 노트포트 지정(기본값 : 30000-32767 내 지정가능)
```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: teslamate
  name: teslamate
  namespace: teslamate
spec:
  type: NodePort
  ports:
    - name: "4000"
      port: 4000
      targetPort: 4000
      nodePort: 30010
  selector:
    app: teslamate
```
Teslamate Service 적용
```shell
    ## 재배포
     kubectl apply -f 01.teslamate.yaml
```

step6. Ganafana Service NodePort 지정
 - 03.grafana.yaml 파일의 vi 또는 에디터를 통해 열고 spec부분의 type: NodePort 추가
 - ports의 nodePort: 노트포트 지정(기본값 : 30000-32767 내 지정가능)
```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: grafana
  name: grafana
  namespace: teslamate
spec:
  type: NodePort
  ports:
    - name: "3000"
      port: 3000
      targetPort: 3000
      nodePort: 30022
  selector:
    app: grafana


```
grafana service 적용
```shell
    ## 재배포
     kubectl apply -f 03.grafana.yaml
```

step8. 서비스 상태 확인
- 서비스 상태및 NodePort 지정한 Teslamate, Garafana NodePort 적용 확인
```shell
    kubectl get pods -n teslamate -o wide 
```