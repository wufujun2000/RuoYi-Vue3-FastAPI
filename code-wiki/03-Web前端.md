# Web前端

## 1. 前端定位

`ruoyi-fastapi-frontend` 是 PC 管理后台，承担系统管理、监控、代码生成、AI 管理等完整业务界面的展示与交互。

它的设计核心不是“本地静态路由驱动页面”，而是“后端菜单树驱动前端路由和侧边栏”。

## 2. 目录结构

```text
ruoyi-fastapi-frontend
├── src/
│   ├── api/           # 按业务域拆分的接口封装
│   ├── assets/        # 图标、图片、样式
│   ├── components/    # 通用组件
│   ├── directive/     # 指令
│   ├── layout/        # 主布局与导航框架
│   ├── plugins/       # auth/cache/modal/tab/download 等插件
│   ├── router/        # 静态路由定义
│   ├── store/         # Pinia 状态管理
│   ├── utils/         # 请求、鉴权、字典、主题、加解密等工具
│   ├── views/         # 页面组件
│   ├── main.js        # 应用入口
│   ├── permission.js  # 路由守卫
│   └── settings.js    # 全局设置
├── vite/              # Vite 插件封装
├── vite.config.js     # Vite 配置
└── package.json
```

## 3. 技术栈

- Vue 3
- Vite
- Element Plus
- Pinia
- Vue Router
- Axios
- ECharts
- Ant Design Vue
- Monaco / Markdown / Mermaid 等增强组件依赖

## 4. 应用入口 `main.js`

`src/main.js` 做了以下事情：

- 创建 Vue 应用
- 注册路由 `router`
- 注册 Pinia `store`
- 注册插件 `plugins`
- 注册自定义指令 `directive`
- 注册全局组件，例如 `Pagination`、`RightToolbar`、`Editor`、`FileUpload`、`ImageUpload`
- 注册 `svg-icon`
- 注册 Element Plus，并根据 Cookie 设置全局尺寸
- 注入全局方法，例如 `download`、`parseTime`、`handleTree`、`getConfigKey`

这意味着页面组件通常可以直接使用全局方法和通用组件，而不需要每次局部引入。

## 5. 路由体系

### 5.1 静态路由

`src/router/index.js` 中的 `constantRoutes` 负责固定页面：

- `/login`
- `/register`
- `/401`
- `404`
- 仪表盘首页 `/index`
- 个人中心 `/user/profile/:activeTab?`
- 重定向页 `/redirect`

### 5.2 动态路由

`dynamicRoutes` 用于一些“只有具备特定按钮权限时才显示的隐藏页面”，例如：

- 分配角色
- 分配用户
- 字典数据详情
- 调度日志
- 代码生成编辑页

### 5.3 动态菜单路由

真正的大部分业务路由不是写死在前端，而是来自后端 `/getRouters`。

Web 端通过 `usePermissionStore.generateRoutes()`：

1. 请求后端菜单树
2. 深拷贝一份路由数据
3. 调用 `filterAsyncRouter()` 递归处理
4. 将后端返回的 `component` 字符串映射为真实 Vue 组件
5. 合并到路由实例中
6. 写入侧边栏、顶栏、默认路由状态

## 6. 路由守卫 `permission.js`

`src/permission.js` 是前端登录态控制中心。

### 6.1 主要逻辑

- 如果存在 token：
  - 访问登录页则跳回首页
  - 若用户信息尚未拉取，先调用 `getInfo()`
  - 再调用 `generateRoutes()` 动态添加路由
- 如果不存在 token：
  - 白名单页面直接放行
  - 其他页面重定向到登录页，并附带 `redirect` 参数

### 6.2 作用

- 确保页面访问前拿到完整的角色与权限信息
- 确保动态菜单注册完成后再进入目标页面
- 统一处理首次登录后的页面刷新与重定向问题

## 7. 状态管理

### 7.1 `store/modules/user.js`

用户状态模块负责：

- 登录并保存 token
- 获取当前用户信息
- 解析头像地址
- 保存 `roles` 与 `permissions`
- 提示初始密码和过期密码风险
- 退出登录并清空状态

### 7.2 `store/modules/permission.js`

权限状态模块负责：

- 从后端获取菜单树
- 生成最终路由表
- 生成侧边栏路由、顶栏路由、默认路由
- 将字符串组件路径映射成真实页面组件

### 7.3 其他状态模块

- `app.js`：应用 UI 状态
- `dict.js`：字典缓存
- `settings.js`：主题与布局设置
- `tagsView.js`：标签页管理

## 8. 请求层 `utils/request.js`

这是 Web 前端中最关键的基础设施文件之一。

### 8.1 请求拦截器职责

- 自动附加 JWT token
- 对 POST / PUT 请求做重复提交保护
- 在参数拼接之前执行传输层请求加密
- 将 GET 请求的 `params` 映射到 URL 查询串

### 8.2 响应拦截器职责

- 尝试对加密响应解密
- 统一按业务 `code` 处理返回结果
- 对 401 提示重新登录
- 对 500 / 601 / 其他错误统一展示消息
- 对传输密钥失效场景执行“清空公钥缓存后重试一次”

### 8.3 下载能力

`download()` 提供统一二进制下载逻辑：

- 使用 `blob` 响应类型
- 校验返回体是否为文件
- 出错时回退解析 JSON 错误消息

## 9. 组件与布局体系

### 9.1 `layout/`

`layout/` 是后台整体框架外壳，包含：

- 侧边栏
- 顶栏
- 标签页
- AppMain 内容区
- Iframe 切换
- 设置面板
- 面包屑和版权等辅助组件

### 9.2 `components/`

通用组件包括：

- `Pagination`
- `RightToolbar`
- `Editor`
- `FileUpload`
- `ImageUpload`
- `ImagePreview`
- `DictTag`
- `SvgIcon`
- `Crontab`
- `IconSelect`

其中 `Crontab` 和 `tool/build` 相关组件为运维和在线构建器功能提供了较强的平台特性。

## 10. 页面模块划分

`views/` 目录与后端模块高度对应：

| 页面目录 | 对应后端能力 |
| --- | --- |
| `views/system/*` | 用户、角色、菜单、部门、岗位、字典、参数、公告 |
| `views/monitor/*` | 缓存、任务、登录日志、在线用户、服务监控、传输加密 |
| `views/tool/gen/*` | 代码生成 |
| `views/tool/build/*` | 在线构建器 |
| `views/tool/swagger/*` | 接口文档嵌入页 |
| `views/ai/*` | AI 模型管理、AI 对话 |
| `views/dashboard/*` | 仪表盘 |

## 11. Vite 配置

`vite.config.js` 的关键点：

- 开发端口固定为 `80`
- `/dev-api` 代理到 `http://127.0.0.1:9099`
- 使用路径别名 `@ -> ./src`
- 构建产物输出到 `dist`
- 通过自定义插件移除 `@charset` 警告

这意味着本地开发时浏览器默认访问 `http://localhost:80`，API 通过代理访问后端 `9099`。

## 12. Web 端关键函数

### 12.1 `generateRoutes()`

职责：

- 请求后端菜单树
- 动态组装页面路由
- 注入权限型隐藏路由
- 更新侧边栏和顶栏状态

### 12.2 `filterAsyncRouter()`

职责：

- 将后端返回的 `Layout`、`ParentView`、`InnerLink` 字符串替换成实际组件
- 将其他字符串路径通过 `import.meta.glob()` 映射成页面组件
- 递归处理子路由

### 12.3 `useUserStore().getInfo()`

职责：

- 请求当前用户信息
- 写入角色、权限、头像
- 处理初始密码与密码过期提示

## 13. Web 端阅读建议

建议按以下顺序阅读：

1. `src/main.js`
2. `src/router/index.js`
3. `src/permission.js`
4. `src/store/modules/user.js`
5. `src/store/modules/permission.js`
6. `src/utils/request.js`
7. 对照 `src/api/*` 与 `src/views/*` 阅读具体业务模块
