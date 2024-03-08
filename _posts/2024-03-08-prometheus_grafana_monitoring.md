---
layout: post
title:  "Prometheus와 Grafana를 활용한 모니터링 구축하기"
date: 2024-03-08 15:40:00 +0900
tags: [prometheus, grafana, monitoring]
categories: [Monitoring]
author: why
---

안녕하세요, 딩동유 백엔드 개발자 와이입니다! 

오늘은 **Prometheus**와 **Grafana**를 활용하여 시스템 모니터링 환경을 구축하는 방법을 간단히 소개하려고 합니다. 이 도구들을 통해 운영중인 서비스의 모니터링 환경을 어떻게 구축할 수 있는지 살펴보겠습니다.

---

## 사전 지식

본격적인 시작에 앞서 몇 가지 용어와 기술에 대해서 알아보겠습니다.

### 소프트웨어 모니터링

소프트웨어 모니터링은 시스템의 성능, 가용성, 그리고 오류를 실시간으로 감시하고, 시스템 상태에 대한 인사이트를 제공하는 과정입니다. 이를 통해 시스템의 문제를 조기에 발견하고, 성능 최적화 및 장애 대응을 할 수 있습니다.

### Node Exporter

Node Exporter는 서버의 하드웨어 및 운영체제 관련 메트릭을 수집하는 Prometheus용 exporter입니다. CPU 사용률, 메모리 사용량, 디스크 사용량, 네트워크 상태 등의 시스템 정보를 수집하여 Prometheus에 제공합니다.

### Prometheus

Prometheus는 오픈 소스 모니터링 시스템으로, 시계열 데이터를 수집, 저장, 질의, 알림을 처리합니다. 다양한 데이터 소스에서 메트릭을 수집하기 위한 exporter들을 지원합니다.

### Grafana

Grafana는 데이터 시각화 및 분석을 위한 오픈 소스 플랫폼입니다. Prometheus와 같은 다양한 데이터 소스로부터 메트릭을 가져와 시각화합니다. 다양한 대시보드를 통해 메트릭을 쉽게 모니터링할 수 있습니다.

---

## Yaml 파일 준비

필요한 소프트웨어 설치와 관리를 위해 docker-compose와 yaml 파일을 활용했습니다.

**`docker-compose.collecting.yml` 파일**

Node Exporter와 cAdvisor 서비스의 설정을 포함합니다. Node Exporter는 시스템 메트릭을, cAdvisor는 Docker 컨테이너 메트릭을 수집합니다.

```yaml
version: '3.7'

services:
  node-exporter:
      image: prom/node-exporter:latest
      container_name: node-exporter
      restart: unless-stopped
      volumes:
        - /proc:/host/proc:ro
        - /sys:/host/sys:ro
        - /:/rootfs:ro
      command:
        - '--path.procfs=/host/proc'
        - '--path.rootfs=/rootfs'
        - '--path.sysfs=/host/sys'
        - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
      ports:
        - '9100:9100'

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:v0.47.0
    container_name: cadvisor
    restart: unless-stopped
    volumes:
      - '/:/rootfs:ro'
      - '/var/run:/var/run:rw'
      - '/sys:/sys:ro'
      - '/var/lib/docker/:/var/lib/docker:ro'
    ports:
      - '8080:8080'
```

- **`version: '3.7'`**: Docker Compose 파일 버전을 명시합니다. 버전 3.7은 Docker와 Docker Compose의 특정 버전과 호환됩니다.
- **`services`**: 이 섹션에서는 실행할 서비스, 여기서는 Node Exporter와 cAdvisor를 정의합니다.
- **`image`**: 사용할 Docker 이미지를 지정합니다. **`prom/node-exporter:latest`**와 **`gcr.io/cadvisor/cadvisor:v0.47.0`**는 각각 Node Exporter와 cAdvisor의 최신 이미지를 사용함을 의미합니다.
- **`container_name`**: 컨테이너의 이름을 설정합니다. 로깅이나 관리 시 참조됩니다.
- **`restart: unless-stopped`**: 컨테이너가 중지되지 않는 한 자동으로 재시작되도록 설정합니다.
- **`volumes`**: 호스트와 컨테이너 간에 공유할 볼륨을 설정합니다. 시스템의 핵심 디렉토리를 컨테이너에 마운트하여 메트릭 수집을 가능하게 합니다.
- **`command`**: 컨테이너 실행 시 전달할 명령어입니다. Node Exporter와 cAdvisor의 메트릭 수집 행동을 세부적으로 제어합니다.
- **`ports`**: 호스트와 컨테이너 간에 연결할 포트를 설정합니다. 이를 통해 메트릭 수집 포트를 외부로 노출시킵니다.

<br>

**`docker-compose.monitoring.yml` 파일 작성**

Prometheus와 Grafana 서비스의 설정을 포함합니다. Prometheus는 메트릭을 수집 및 저장하며, Grafana는 이 메트릭을 시각화합니다.

```yaml
version: '3.7'

services:
  prometheus:
    image: prom/prometheus:v2.26.0
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./configs/prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.enable-lifecycle'
      - --storage.tsdb.retention.time=30d
    ports:
      - '9090:9090'

  grafana:
    image: grafana/grafana-oss:latest
    container_name: grafana
    restart: unless-stopped
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=****
      - GF_USERS_ALLOW_SIGN_UP=false
    ports:
      - '3000:3000'

volumes:
  prometheus_data:
  grafana_data:
```

- **`volumes`**: 설정 파일과 데이터베이스를 저장할 볼륨을 정의합니다. 이를 통해 설정을 유지하고 메트릭 데이터를 안전하게 보관할 수 있습니다.
- **`command`**: Prometheus 서버의 동작을 세부적으로 제어하는 옵션들을 설정합니다. 구성 파일의 위치, 데이터 저장 경로, 웹 인터페이스 관련 설정, 데이터 보존 정책 등을 지정합니다.
- **`environment`**: Grafana에 관리자 비밀번호 및 사용자 가입 허용 여부를 환경 변수로 설정합니다.

<br>

**`prometheus.yml` 파일 작성**

Prometheus의 스크레이핑(scraping) 설정을 정의합니다. scrape_interval로 메트릭을 수집하는 주기를 설정하고, scrape_configs에서 수집할 대상과 방법을 정의합니다. 특히, ec2_sd_configs를 통해 AWS EC2 인스턴스를 대상으로 메트릭을 수집하는 설정을 포함합니다.

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: 'prometheus'
    ec2_sd_configs:
      - region: 'ap-northeast-2'
        port: 9100
        filters:
          - name: 'tag:Monitoring'
            values: ['Enabled']
          - name: 'instance-state-name'
            values: ['running']
```

- `region`은 메트릭을 수집할 AWS 리전을 지정합니다.
- `port`는 Node Exporter가 수집하는 메트릭의 포트 번호를 지정합니다.
- `filters`를 통해 Monitoring 태그가 Enabled이며 상태가 'running'인 인스턴스만을 대상으로 합니다.

---

## 인스턴스 및 대시보드 세팅

운영중인 인스턴스에 적용하고, 수집된 데이터를 확인하는 단계입니다.

### 라이브

1. 인스턴스 인바운드 규칙에 모니터링 인스턴스 보안 그룹 TCP 9100 포트 허용
2. 인스턴스 태그 Key: Monitoring / Value: Enabled 추가
3. `docker-compose.collecting.yml`이 있는 경로에서 `docker-compose -f docker-compose.collecting.yml up -d` 실행

### 모니터링

1. 인스턴스 보안 그룹에 필요 시 monitoring 관련 보안 그룹 지정
2. 인스턴스에 IAM Role “AmazonEC2ReadOnlyAccess” 부여
3. Grafana로 접근할 DNS 설정, 예시) `monitor.example.com`
4. 인스턴스에 접근해서 DNS로 설정한 도메인 Nginx에 3000번 포트로 포워딩
5. `docker-compose.monitoring.yml`이 있는 경로에서 `docker-compose -f docker-compose.monitoring.yml up -d` 실행
6. 3번에서 지정한 Grafana DNS로 접속해 `docker-compose.monitoring.yml`에서 설정한 계정 정보로 로그인
7. Data Source에 Prometheus를 선택, Connection Prometheus server URL에 http://prometheus:9090으로 추가
8. Dashboard에서 import dashboard를 선택, dashboard URL or ID에 1860 입력 후 Load

![monitoring](../assets/img/posts/monitoring/node_exporter_grafana.png?raw=true)

---

## 마치며

간단하게 Prometheus와 Grafana를 활용한 모니터링 구축 방법을 알아보았습니다. 모든 문제에 대한 조기 발견은 어렵더라도 잘 만들어놓은 모니터링은 여러 문제를 방지할 수 있다고 생각합니다. 이 글이 안정적인 서비스를 운영하는데 조금이라도 도움이 되기를 바랍니다.

긴 글 읽어주셔서 감사합니다 :)

---

## 참고 자료

[Prometheus 공식 사이트](https://prometheus.io/)
[Grafana 공식 사이트](https://grafana.com/)