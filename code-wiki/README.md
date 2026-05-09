# Code Wiki

本目录是一套面向开发者的仓库级 Code Wiki，用于快速理解 `RuoYi-Vue3-FastAPI` 单仓项目的架构、分层、模块职责、关键函数、依赖关系以及运行方式。

## 文档导航

1. [01-项目总览](./01-%E9%A1%B9%E7%9B%AE%E6%80%BB%E8%A7%88.md)
2. [02-后端架构](./02-%E5%90%8E%E7%AB%AF%E6%9E%B6%E6%9E%84.md)
3. [03-Web前端](./03-Web%E5%89%8D%E7%AB%AF.md)
4. [04-移动端](./04-%E7%A7%BB%E5%8A%A8%E7%AB%AF.md)
5. [05-业务模块清单](./05-%E4%B8%9A%E5%8A%A1%E6%A8%A1%E5%9D%97%E6%B8%85%E5%8D%95.md)
6. [06-依赖关系与关键流程](./06-%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB%E4%B8%8E%E5%85%B3%E9%94%AE%E6%B5%81%E7%A8%8B.md)
7. [07-运行与部署](./07-%E8%BF%90%E8%A1%8C%E4%B8%8E%E9%83%A8%E7%BD%B2.md)

## 仓库一句话说明

这是一个以前后端分离为核心、同时包含 PC Web 管理端、uni-app 移动端、FastAPI 后端和 Playwright 测试套件的单仓平台项目。

## 推荐阅读顺序

- 第一次接触项目：`01 -> 02 -> 03 -> 06 -> 07`
- 只关心后端：`02 -> 05 -> 06 -> 07`
- 只关心前端：`03 -> 04 -> 06 -> 07`
- 只想运行项目：直接看 `07-运行与部署`

## 仓库组成

- `ruoyi-fastapi-backend`：FastAPI 后端，负责认证、权限、系统管理、监控、代码生成、AI 能力。
- `ruoyi-fastapi-frontend`：Vue 3 Web 管理端，负责后台业务界面、动态菜单路由和权限控制。
- `ruoyi-fastapi-app`：uni-app 移动端，负责登录、首页、工作台、个人中心等轻量能力。
- `ruoyi-fastapi-test`：基于 pytest + Playwright 的端到端测试套件。
- 根目录 `docker-compose.*.yml`：提供 MySQL / PostgreSQL 两套容器化部署编排。

## 关键源码入口

- 后端进程入口：`ruoyi-fastapi-backend/app.py`
- 后端应用工厂：`ruoyi-fastapi-backend/server.py`
- Web 入口：`ruoyi-fastapi-frontend/src/main.js`
- App 入口：`ruoyi-fastapi-app/src/main.ts`
- 测试说明：`ruoyi-fastapi-test/README.md`

## Wiki 设计原则

- 以代码真实结构为准，不依赖 README 中未落地的抽象描述。
- 以“启动链路、请求链路、动态菜单链路、调度链路、代码生成链路、AI 对话链路”为重点。
- 以目录、模块、关键类与关键函数为组织单位，兼顾运行方式与部署方式。
