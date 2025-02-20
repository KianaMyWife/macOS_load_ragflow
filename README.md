# macOS_load_ragflow
macOS  安装ragflow踩的坑

## 1 拉取镜像时报错ERROR: failed to solve: infiniflow/ragflow_deps:latest: failed to resolve source metadata for docker.io/infiniflow/ragflow_deps:latest: no match for platform in manifest: not found

解决方法：
```
git clone https://github.com/infiniflow/ragflow.git
cd ragflow/
pip3 install huggingface_hub nltk
python3 download_deps.py
docker build -f Dockerfile.deps -t infiniflow/ragflow_deps .
docker build -f Dockerfile -t infiniflow/ragflow:nightly .
docker compose -f docker/docker-compose-macos.yml up -d
```

## 2 es01无限重启

解决方法：修改docker-compose-base.yml，es01最后两行加
```
    environment:
      - node.name=es01
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - bootstrap.memory_lock=false
      - discovery.type=single-node
      - xpack.security.enabled=true
      - xpack.security.http.ssl.enabled=false
      - xpack.security.transport.ssl.enabled=false
      - cluster.routing.allocation.disk.watermark.low=5gb
      - cluster.routing.allocation.disk.watermark.high=3gb
      - cluster.routing.allocation.disk.watermark.flood_stage=2gb
      - TZ=${TIMEZONE}
      - "ES_JAVA_OPTS=-XX:UseSVE=0"
      - "CLI_JAVA_OPTS=-XX:UseSVE=0"
```

## 3 即使下载了完整的ragflow，模型设置里仍然找不到一个模型

解决方法：不要使用docker-compose-macos.yml启动，使用docker-compose.yml启动

## 4 添加oallama模型报错connection refused
解决方法：在URL使用host.docker.internal代替ip

## 5 无法解析文档，一直显示Task is queued...
解决方法：目前没找到，据说是m4芯片独有的bug。。。。