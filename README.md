# 🚀 oracle-k3s-gitops-lab

**Oracle Cloud Free Tier** 자원을 극한으로 활용하여 구축한 **Enterprise-grade Cloud Native 인프라 및 데브옵스 파이프라인** 실습 저장소입니다.

---

## 🎯 프로젝트 목표
1. **Cost & Resource Optimization**: 24GB RAM 내에서 고부하 미들웨어(Kafka, OpenSearch)와 관측성 도구(PLG Stack)의 공존.
2. **GitOps Implementation**: ArgoCD를 통한 선언적 인프라 관리 및 자동 배포 체계 구축.
3. **Full Observability**: Prometheus, Grafana, Loki, Fluent Bit를 활용한 메트릭 및 로그 통합 모니터링.
4. **License Compliance**: 기업 실무를 고려하여 Elasticsearch 대신 **OpenSearch**, Redis 대신 **Valkey** 등 완전 오픈소스 스택 채택.

---

## 🏗️ 시스템 아키텍처 (Architecture)

### 1. 하드웨어 리소스 배분 (Oracle Cloud Free Tier)
| 노드 이름 | 사양 (Shape) | 역할 (Role) | 주요 구성 요소 |
| :--- | :--- | :--- | :--- |
| **Master Node** | ARM (2 OCPUs, 12GB) | Control Plane & Ops | K3s Server, ArgoCD, Prometheus, Grafana |
| **Worker Node 1** | ARM (2 OCPUs, 12GB) | Data Pipeline | Kafka, Zookeeper, OpenSearch, Loki |
| **Edge Node** | AMD (1/8 OCPU, 1GB) | Frontend & Gateway | Nginx (Vue.js 빌드본), Nginx Proxy Manager |
| **Admin Node** | AMD (1/8 OCPU, 1GB) | Management | Bastion Host, Uptime Kuma |

### 2. 서비스 흐름도 (Service Flow)
```mermaid
graph TD
    User((User)) -->|HTTPS| Edge[AMD: Nginx/Vue.js]
    Edge -->|API Request| Master[ARM: K3s Master]
    
    subgraph "K3s Cluster (ARM Nodes)"
        Master --> App[Spring Boot / Flask Pods]
        App --> Kafka[Apache Kafka]
        App --> DB[(Oracle Autonomous DB)]
        
        subgraph "Observability (PLG Stack)"
            App -->|stdout| FB[Fluent Bit]
            FB --> Loki[Grafana Loki]
            Loki --> Grafana[Grafana Dashboard]
            Prom[Prometheus] --> Grafana
        end
        
        subgraph "Search & Analytics"
            App --> OS[OpenSearch]
        end
    end
    
    subgraph "GitOps Pipeline"
        GitHub[GitHub Repo] -->|Webhook| ArgoCD[ArgoCD]
        ArgoCD -->|Sync| Master
    end
````

-----

## 🛠️ 기술 스택 (Tech Stack)

### Infrastructure & Orchestration

  * **Cloud**: Oracle Cloud Infrastructure (OCI)
  * **K8s Distro**: K3s (Lightweight Kubernetes)
  * **Package Manager**: Helm v3
  * **GitOps**: ArgoCD

### Middleware & Storage

  * **Messaging**: Apache Kafka & Zookeeper
  * **Search Engine**: **OpenSearch** (Apache 2.0 License)
  * **Caching**: **Valkey** (Redis Open Source Alternative)
  * **Database**: Oracle Autonomous MySQL / MariaDB

### Observability (The PLG Stack)

  * **Logging**: **Loki** & **Fluent Bit** (Lightweight & Cost-effective)
  * **Monitoring**: Prometheus & Grafana (OSS Edition)

### Applications

  * **Frontend**: Vue.js (Nginx Container)
  * **Backend**: Spring Boot 3.x, Python Flask

-----

## 📝 학습 및 트러블슈팅 기록

프로젝트 진행 중 발생한 주요 이슈와 해결 과정을 아래 링크에 기록합니다. (업데이트 예정)

  * [ARM(aarch64) 아키텍처용 Docker 이미지 빌드 전략](https://www.google.com/search?q=./docs/arm-build.md)
  * [Loki와 Fluent Bit를 이용한 로그 파이프라인 최적화](https://www.google.com/search?q=./docs/loki-setup.md)
  * [ArgoCD를 활용한 Helm 차트 배포 자동화](https://www.google.com/search?q=./docs/argocd-helm.md)

-----

## ⚡ 시작하기 (Quick Start)

1.  **Prerequisites**: OCI 계정 생성 및 SSH 키 등록.
2.  **Infrastructure**: `scripts/install-k3s.sh` 실행 (마스터 및 워커 노드 구성).
3.  **Bootstrap**: `kubectl apply -f bootstrap/argocd.yaml` 명령으로 ArgoCD 설치.
4.  **App Deploy**: ArgoCD UI 접속 후 각 Application 등록 및 동기화.

-----

## 📜 라이선스 (License)

본 프로젝트는 **Apache License 2.0** 및 관련 오픈소스 라이선스를 준수합니다. 기업 환경에서의 자유로운 사용과 배포를 지향합니다.
