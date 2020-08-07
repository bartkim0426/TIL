# Chapter 8. Accessing pod metadata and other resources from applications

이번 장에서는 다음 내용들을 다룰 것

- 컨테이너에 정보를 전달하기 위해 Downward API 사용
- Kubernetes REST API 살펴보기
- authentication과 server verification을 `kubectl proxy`에 남기기
- 컨테이너에서 API server에 접속
- ambassador container pattern 이해하기
- Kubernetes client 라이브러리 사용하기

> 해당 장은 kubernetes를 최초 설정할 때에는 필수적이지만 사용자 입장에서는 크게 중요하지 않을 듯 하여 간단하게 읽고 넘어간다.

## 8.1. Passing Metadata through the downward api

이전 장에서는 `configMap`과 `secret` 볼륨을 사용하여 애플리케이션에 설정 데이터를 전달하였다.
이는 pod가 스케줄 되기 이전에 미리 정해져 있기 때문에 가능한데, pod의 IP나 노드의 이름, 심지어 pod 자체의 이름 등 pod가 스케줄 되기 전에는 알 수 없는 데이터들은 어떻게 해야할까?

이런 문제들은 Kubernetes Downward API를 통해 해결이 가능하다. 이는 pod와 관련된 metadata를 환경변수나 파일(`downwardAPI volume`)을 통해 전달한다.

이름이 헷갈릴 수 있지만, Downward API는 REST endpoint와 같은 것이 아니다. 이는 pod의 상세나 상태 등의 값을 환경변수나 file로 populated 할 수 있는 방식 중 하나이다.

### Understanding the available metadata

컨테이너에 다음 정보들을 전달할 수 있다.

- The pod’s name
- The pod’s IP address
- The namespace the pod belongs to
- The name of the node the pod is running on
- The name of the service account the pod is running under
- The CPU and memory requests for each container
- The CPU and memory limits for each container
- The pod’s labels
- The pod’s annotations

대부분의 내용은 딱히 설명이 필요 없는 것들이다.

service account와 CPU/memory requests and limits는 아직 다루지 않은 내용인데 이를 12장 service account에서 다룰 것이다.

일단은 service account는 API 서버와 소통시에 pod authentication을 다루는 것이라고만 이해하면 됨.
CPU/memory requests and limit는 14장에서 다룰 것인데 각 컨테이너에 할당된 CPU, memory를 얘기한다.

### Exposing metadata through environment variables

간단한 pod를 다음 manifest로 만들자.

`downward-api-env.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: downward
spec:
  containers:
  - name: main
    image: busybox
    command: ["sleep", "9999999"]
    resources:
      requests:
        cpu: 15m
        memory: 100Ki
      limits:
        cpu: 100m
        memory: 4Mi
    env:
    - name: POD_NAME
      valueFrom:                                   1
        fieldRef:                                  1
          fieldPath: metadata.name                 1
    - name: POD_NAMESPACE
      valueFrom:
        fieldRef:
          fieldPath: metadata.namespace
    - name: POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP
    - name: NODE_NAME
      valueFrom:
        fieldRef:
          fieldPath: spec.nodeName
    - name: SERVICE_ACCOUNT
      valueFrom:
        fieldRef:
          fieldPath: spec.serviceAccountName
    - name: CONTAINER_CPU_REQUEST_MILLICORES
      valueFrom:                                   2
        resourceFieldRef:                          2
          resource: requests.cpu                   2
          divisor: 1m                              3
    - name: CONTAINER_MEMORY_LIMIT_KIBIBYTES
      valueFrom:
        resourceFieldRef:
          resource: limits.memory
          divisor: 1Ki
```

- 1. 특정 값을 지정하는 대신 `metadata.name` 필드를 사용
- 

