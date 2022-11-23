---
title: "JupyterLabを動かすだけのDocker環境の構築例"
emoji: "📔"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Python", "Docker", "メモ"]
published: true
published_at: 2022-11-23 15:00
---

データ分析環境をコンテナで管理したい事が多いのでメモ


# ファイル構成

入れたいライブラリは`requirements.txt`に記入済みという想定

```
.
├── Dockerfile
├── docker-compose.yml
└── requirements.txt
```

# ファイルの中身

## Dockerfile

```Dockerfile:Dockerfile
FROM python:3
WORKDIR /workdir

# cmakeが必要なライブラリを入れる場合
# RUN apt update && apt install vim cmake -y

# ライブラリの追加
RUN /usr/local/bin/python -m pip install --upgrade pip
RUN pip install jupyterlab
COPY requirements.txt .
RUN pip install -r requirements.txt
```

## docker-compose.yml

コンテナとホストPCでストレージを共有したい状況を想定しており、volumesでbindを設定している

```yml:docker-compose.yml
version: '3'
services:
  python:
    build: .
    ports:
    - "8888:8888"
    volumes:
    - .:/workdir
    command: jupyter-lab --ip=0.0.0.0 --port=8888 --allow-root --no-browser
```

# 起動

```sh
sudo docker compose build
sudo docker compose up
```

