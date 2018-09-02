---
title: 安装 Sentry 服务
date: 2018-08-13 12:57:20
categories: 环境
tags:
  - 环境
  - 服务器

english_title: Install-Sentry-services
---

# 安装 Sentry 服务

## 安装

- [官方文档](https://docs.sentry.io/server/installation/docker/)

依赖

> Docker version 1.10+

克隆

`git clone https://github.com/getsentry/onpremise`

生成镜像

```
cd onpremise
mkdir -p data/{sentry,postgres}
docker-compose build
docker-compose run --rm web config generate-secret-key # 生成 key，将它添加到 docker-compose.yml 中的 SENTRY_SECRET_KEY
docker-compose run --rm web upgrade
docker-compose up -d
```
访问 `localhost:9000`

**如果打开后提示这个。确认下是否存在以下 issue 所描述的问题**

[Oops! Something went wrong.](https://github.com/getsentry/sentry/issues/8862#issuecomment-400807159)

```
docker-compose run --rm web shell

from sentry.models import Project
from sentry.receivers.core import create_default_projects
create_default_projects([Project])
```

```
postgres_1   | ERROR:  function sentry_increment_project_counter(integer, integer) does not exist at character 25
postgres_1   | HINT:  No function matches the given name and argument types. You might need to add explicit type casts.
postgres_1   | STATEMENT:
postgres_1   |                  select sentry_increment_project_counter(1, 1)
postgres_1   |

# 参考 https://github.com/getsentry/sentry/issues/9270#issuecomment-409577582
docker exec -it onpremise_postgres_1 bash
psql -h 127.0.0.1 -d postgres -U postgres
create or replace function sentry_increment_project_counter( project bigint, delta int) returns int as $$ declare new_val int; begin loop update sentry_projectcounter set value = value + delta where project_id = project returning value into new_val; if found then return new_val; end if; begin insert into sentry_projectcounter(project_id, value) values (project, delta) returning value into new_val; return new_val; exception when unique_violation then end; end loop; end $$ language plpgsql;
```

升级

```
docker-compose build
docker-compose run --rm web upgrade
docker-compose up -d
```

## ecs 放行端口

[ecs 控制台](ecs.console.aliyun.com) -> 安全组 -> 配置规则（选择实例）

```
端口号/端口号 # 端口范围
0.0.0.0/0 # 授权对象
```
