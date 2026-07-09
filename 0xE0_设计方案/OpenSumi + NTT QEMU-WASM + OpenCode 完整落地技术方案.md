# OpenSumi + NTT QEMU-WASM + OpenCode 完整落地技术方案

## 一、整体架构定义
本方案采用**前端算力下沉架构**，全程无用户态后端容器、无用户Node进程、无会话Pod，所有用户沙箱运行时、Agent执行环境全部运行在浏览器客户端。

整体分层：
1. 静态资源层：仅静态文件服务，托管IDE前端资源、WASM虚拟机资源、系统镜像资源，无业务逻辑、无动态计算。
2. 前端IDE层：OpenSumi纯前端版本承载全部编辑器UI、布局配置、插件体系、终端、文件管理、视图交互。
3. 浏览器沙箱层：NTT QEMU-WASM虚拟机（浏览器内完整x86_64 Linux环境），运行原生OpenCode二进制可执行文件。
4. 智能服务层：独立部署的AI模型推理服务，仅接收网络请求，不承载用户沙箱负载。

架构核心规则：
- OpenSumi 仅运行在浏览器主线程，**不部署在虚拟机内部**。
- OpenCode 仅运行在浏览器WASM虚拟机内部，**不运行在服务端**。
- 服务端只保留静态托管 + AI推理，零用户运行时开销。

## 二、核心技术选型与版本规范
### 1. IDE层：OpenSumi CodeBlitz（纯前端构建产物）
技术特性：
- 无后端Node服务依赖，全静态HTML/JS/WASM资源。
- 原生支持 VSCode 标准 `settings.json` 全局布局、尺寸、交互配置。
- 支持自定义视图、AI面板、底部终端、文件树、侧边栏尺寸自定义。
- 完整兼容VSCode插件生态。

部署形式：
源码构建生成 `dist` 静态目录，由静态服务器直接托管访问。

禁止使用：
OpenSumi 后端Docker镜像、code-server、服务端多进程IDE方案。

### 2. 沙箱层：NTT QEMU-WASM + container2wasm
技术能力：
- 在浏览器内模拟完整x86_64 Linux系统。
- 支持运行任意原生Linux ELF二进制文件，解决WebContainer无法运行OpenCode的限制。
- 基于WebWorker隔离运行，不阻塞IDE UI渲染。
- 支持串口终端、虚拟磁盘、文件预挂载、持久化存储。

工具链：
- `container2wasm`：将预装OpenCode的Linux容器打包为浏览器可用内核+根文件系统镜像。
- QEMU-WASM编译产物：`qemu-system-x86_64.wasm`、worker脚本、运行时胶水脚本。

### 3. Agent层：OpenCode
运行方式：
- 原生Linux二进制，运行于浏览器QEMU虚拟机内。
- 以 `opencode serve` 服务模式启动，提供内部RPC/HTTP能力。
- 通过前端桥接层与OpenSumi IDE双向通信。

### 4. 推理层：AI大模型服务
- 独立部署、独立扩容、独立运维。
- 仅接收虚拟机内OpenCode发起的网络请求。
- 与用户浏览器沙箱完全解耦。

## 三、核心运行逻辑
1. 用户访问静态地址，优先加载 OpenSumi 前端资源，IDE界面、布局、配置**秒级完成渲染**，所有`settings.json`配置即时生效。
2. IDE就绪后，后台WebWorker异步加载QEMU-WASM运行时、Linux内核、根文件系统镜像。
3. 虚拟机初始化完成后，自动后台启动虚拟机内 OpenCode Agent服务。
4. 通过JS桥接层完成双向打通：
- OpenSumi终端 ↔ 虚拟机系统终端
- OpenSumi工作区文件 ↔ 虚拟机挂载目录
- OpenSumi自定义AI面板 ↔ 虚拟机OpenCode服务
5. 用户所有代码编辑、文件操作、终端命令、Agent调用均在客户端完成；服务端无任何用户算力消耗。

## 四、构建与部署完整流程
### 1. OpenSumi 纯前端构建流程
1. 拉取 OpenSumi 官方 `ide-startup-codeblitz` 纯前端模板。
2. 安装依赖、执行打包命令，生成 `dist` 静态产物。
3. 产物内容：纯前端HTML、JS、CSS、静态资源，无服务端代码。

### 2. OpenCode 镜像打包流程
1. 编写Dockerfile，基于轻量Linux镜像预装OpenCode二进制。
2. 使用 `container2wasm` 工具将容器打包为浏览器可用镜像包：
- `bzImage` 内核文件
- `rootfs.bin` 根文件系统镜像
3. 输出完整虚拟机预加载资源包。

### 3. QEMU-WASM 资源准备
1. 准备官方编译的QEMU-WASM运行时全套资源：
- wasm核心文件
- worker隔离脚本
- 初始化Module胶水脚本

### 4. 统一静态部署
1. 将以下全部资源放入同一静态服务目录：
- OpenSumi `dist` 所有文件
- QEMU-WASM运行时文件
- 打包后的Linux内核、rootfs镜像
2. 配置静态资源长效缓存策略，对wasm、bin、镜像文件开启长期浏览器缓存。
3. 单静态服务统一对外提供访问入口。

## 五、前端桥接核心能力（固定标准实现）
1. 虚拟机生命周期管理：页面就绪异步启动、后台运行、不阻塞UI。
2. 终端双向透传：IDE终端输入投递至虚拟机tty，虚拟机输出回显至IDE终端。
3. 文件系统映射：IDE工作区目录与虚拟机挂载目录双向同步。
4. OpenCode服务探测：虚拟机就绪后自动拉起serve服务，前端自动连接RPC能力。
5. 加载状态管控：虚拟机未就绪时禁用Agent功能，展示加载状态。

## 六、方案核心优势
1. 服务端零用户算力、零用户容器、零会话运维，可支撑大规模并发。
2. IDE界面秒启，布局配置完全遵循VSCode settings标准。
3. 突破WebContainer限制，原生OpenCode二进制可直接运行。
4. 全部用户运行时下沉客户端，服务器成本极低。
5. 架构分层清晰，UI、沙箱、Agent、模型四层完全解耦。

## 七、严格禁止的实现方式
1. 禁止将OpenSumi、WebIDE、code-server运行在QEMU虚拟机内部，避免双层服务嵌套、性能雪崩。
2. 禁止将OpenCode部署在服务端容器运行，违背算力下沉架构。
3. 禁止使用OpenSumi服务端Docker版本，禁止多用户Pod调度架构。
4. 禁止使用WebContainer承载OpenCode，无法运行原生二进制。

## 八、运行环境依赖标准
1. 客户端：现代浏览器，支持WASM、WebWorker、IndexedDB大容量存储。
2. 构建环境：Node.js、Go（用于container2wasm）、Docker（用于打包系统镜像）。
3. 服务端：仅普通静态文件服务 + AI推理服务，无需应用服务、无需容器集群。

## 九、缓存策略标准
1. 首次访问：完整下载所有静态资源与虚拟机镜像，启动耗时允许10–20秒。
2. 二次及后续访问：依赖浏览器本地缓存与IndexedDB持久化，虚拟机秒级复用、页面秒开。
3. 静态大资源永久缓存，版本更新仅需版本号增量刷新。