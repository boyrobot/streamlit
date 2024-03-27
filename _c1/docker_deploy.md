
---
title: 第一章：google，github auth2.0 权鉴登录
author: liuyuqi
date: 2024-03-27
category: nlp
layout: post
---

# docker 部署

新建dockerfile

Dockerfile

```
FROM python:3.9.4

# Create the user that will run the app
RUN adduser --disabled-password --gecos '' ml-api-user

WORKDIR /opt/car_evaluation_api

ARG PIP_EXTRA_INDEX_URL

# Install requirements, including from Gemfury
ADD ./car_evaluation_api /opt/car_evaluation_api
RUN pip install --upgrade pip
RUN pip install -r /opt/car_evaluation_api/requirements.txt

RUN chmod +x /opt/car_evaluation_api/run.sh
RUN chown -R ml-api-user:ml-api-user ./

USER ml-api-user

EXPOSE 8001

CMD ["bash", "./run.sh"]
```

vim docker-compose.yml
```
version: '3'

services:
  car_evaluation_streamlit:
    build:
        dockerfile: car_evaluation_streamlit\Dockerfile
    ports:
      - 8501:8501
    depends_on:
      - car_evaluation_api

  car_evaluation_api:
    build:
        dockerfile: car_evaluation_api\Dockerfile
    ports:
      - 8001:8001
```

运行：
```
docker-compose -f docker-compse.yml up -d --build
```


heroku login
heroku create
git push heroku master
heroku ps:scale web=1

heroku logs --tail

heroku domains:add hivecnstats.iswbm.com








