<h1 align="center">LACDT Render WebSDK- 三维地球渲染引擎</h1>

<p align="center">一个基于Cesium的三维地球渲染引擎，提供了丰富的地图操作和数据分析功能。它采用模块化架构，包含多个功能系统，支持地图加载、图层管理、建筑模型展示、测量工具、3D Tiles搜索等功能。</p>

<p align="center">
  <a href="https://github.com/ASunYC/sdk-render/blob/main/LICENSE">
    <img src="https://img.shields.io/github/license/ASunYC/sdk-render.svg?style=flat&colorA=080f12&colorB=1fa669" alt="License">
  </a>
  <a href="https://github.com/ASunYC/sdk-render">
    <img src="https://img.shields.io/github/stars/ASunYC/sdk-render?style=flat&colorA=080f12&colorB=1fa669" alt="Stars">
  </a>
  <a href="https://github.com/ASunYC/sdk-render/issues">
    <img src="https://img.shields.io/github/issues/ASunYC/sdk-render?style=flat&colorA=080f12&colorB=1fa669" alt="Issues">
  </a>
  <a href="https://pypi.org/project/open-agent/">
    <img src="https://img.shields.io/pypi/v/open-agent.svg?style=flat&colorA=080f12&colorB=1fa669" alt="PyPI">
  </a>
</p>

<p align="center">
  <a href="#核心特性">功能特性</a> •
  <a href="#快速开始">快速开始</a> •
  <a href="#系统架构">系统架构</a> •
  <a href="#更新日志">更新日志</a> •
  <a href="#联系方式">联系方式</a>
</p>

<picture>
  <source
    width="100%"
    srcset="https://gitee.com/ASun001/Resource/raw/master/RenderPreview.png"
    media="(prefers-color-scheme: dark)"
  />
  <source
    width="100%"
    srcset="https://gitee.com/ASun001/Resource/raw/master/RenderPreview.png"
    media="(prefers-color-scheme: light), (prefers-color-scheme: no-preference)"
  />
  <img width="250" src="https://gitee.com/ASun001/Resource/raw/master/RenderPreview.png" />
</picture>

---

## 项目简介

 LACDT Render WebSDK是一个基于Cesium的三维地球渲染引擎，提供了丰富的地图操作和数据分析功能。它采用模块化架构，包含多个功能系统，支持地图加载、图层管理、建筑模型展示、测量工具、3D Tiles搜索等功能。

## 核心特性

### 地图渲染
- 支持多种地图数据源
- 提供丰富的地图渲染配置选项
- 支持地图切换和底图控制

### 图层管理
- 支持影像图层、地形图层、3D Tiles图层等多种类型
- 提供图层可见性、透明度控制
- 支持图层添加、移除和查找

### 建筑模型展示
- 支持加载城市建筑模型
- 提供城市数据搜索功能
- 显示生产区域和建筑边界

### 测量工具
- 直线距离测量
- 贴地距离测量
- 面积测量
- 高度测量
- 方位测量

### 3D Tiles搜索
- 属性查询功能
- 对象高亮显示
- 3D Tiles模型加载和操作

### 场景控制
- 地图定位和飞行
- 场景状态管理
- 环境特效控制

## 快速开始

### 安装

```bash
npm install @metagl/sdk-render
```

### 初始化

```typescript
import { Render, RenderConfig } from '@metagl/sdk-render';
import { Viewer } from 'cesium';

// 初始化 Cesium Viewer
const container = document.getElementById('cesiumContainer');
const viewer = new Viewer(container);

// 配置渲染引擎
const config: RenderConfig = {
    viewer: viewer,
    token: 'your-access-token',  // 访问 LACDT 资源的 token（必填）
    assetsBasePath: '/'          // 资源文件基础路径（可选，默认自动检测）
};

// 初始化渲染引擎（异步方式）
const render = await Render.create(config);
```

**Token 说明**：
- `token` 是访问 LACDT 资源的必填参数

**资源路径说明**：
- `assetsBasePath` 用于指定 SDK 资源文件（纹理、数据等）的基础路径
- 默认会自动检测，但在某些打包工具（如 Vite）中可能需要手动设置
- 开发模式和生产模式的路径可能不同，需要根据实际情况配置

### 资源路径配置

SDK 需要加载资源文件（纹理、数据等），需要正确配置资源路径：

#### 方式1：初始化时设置（推荐）

```typescript
const render = await Render.create({
    viewer: viewer,
    token: 'your-token',
    assetsBasePath: '/'  // 资源在网站根目录
});
```

#### 方式2：手动设置

```typescript
import { setSdkBasePath } from '@metagl/sdk-render';

// 在使用任何系统之前设置
setSdkBasePath('/');
```

#### 方式3：全局变量设置

```html
<script>
  window.__SDK_RENDER_BASE_PATH__ = '/';
</script>
<script src="lacdt.render.js"></script>
```

### 打包工具配置

#### Vite 项目

安装 `vite-plugin-static-copy` 插件：

```bash
npm install -D vite-plugin-static-copy
```

配置 `vite.config.js`：

```javascript
import { viteStaticCopy } from 'vite-plugin-static-copy'

export default defineConfig({
  plugins: [
    viteStaticCopy({
      targets: [
        // Cesium 资源
        { src: 'node_modules/cesium/Build/Cesium/*', dest: 'cesium' },
        // SDK 资源
        { src: 'node_modules/@metagl/sdk-render/assets', dest: 'assets' },
        { src: 'node_modules/@metagl/sdk-render/resources', dest: 'resources' }
      ]
    })
  ],
  define: {
    CESIUM_BASE_URL: JSON.stringify('/cesium')
  }
})
```

然后在代码中设置：

```typescript
import { setSdkBasePath } from '@metagl/sdk-render';
setSdkBasePath('/');
```

#### Webpack 项目

在 `webpack.config.js` 中添加：

```javascript
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {
  plugins: [
    new CopyWebpackPlugin({
      patterns: [
        { from: 'node_modules/@metagl/sdk-render/assets', to: 'assets' },
        { from: 'node_modules/@metagl/sdk-render/resources', to: 'resources' }
      ]
    })
  ]
}
```

### 基本使用

```typescript
// 更新系统配置
render.updateSystemConfig('lighting', {
    intensity: 1.2,
    color: Cesium.Color.YELLOW
});

// 启用系统
render.enableSystem('lighting');

// 禁用系统
render.disableSystem('lighting');

// 切换系统状态
render.toggleSystem('volumetricClouds');

// 获取系统配置
const lightingConfig = render.getSystemConfig('lighting');
console.log('Lighting config:', lightingConfig);

// 获取系统状态
const shadowStatus = render.getSystemStatus('shadow');
console.log('Shadow status:', shadowStatus);

// 应用环境预设配置
render.applyPresetConfig({
    time: { hour: 12, minute: 0 },
    lighting: {
        enabled: true,
        intensity: 1.5,
        ambientIntensity: 0.3,
        followSun: true
    },
    shadow: {
        enabled: true,
        darkness: 0.3,
        softShadows: true
    },
    atmosphere: {
        enabled: true,
        intensity: 1.0,
        rayleighIntensity: 1.0,
        mieIntensity: 0.5,
        absorptionIntensity: 0.5
    },
    clouds: {
        enabled: true,
        cover: 0.5,
        base: 2000,
        top: 6000,
        lightIntensity: 5.3,
        windSpeed: 5
    },
    fog: {
        enabled: true,
        type: 'distance' // 'distance' | 'height' | 'both'
    },
    postProcessing: {
        enabled: true,
        brightness: 1.0,
        contrast: 1.0,
        saturation: 1.0,
        gamma: 1.0
    }
});

// 获取调试信息
const debugInfo = render.getDebugInfo();
console.log('Debug info:', debugInfo);
```

## 系统架构

SDK渲染引擎采用模块化架构，包含以下系统：

### 渲染系统
- **光照系统**：负责管理场景的光照
- **阴影系统**：负责管理场景的阴影
- **大气散射系统**：负责管理大气散射效果
- **距离雾系统**：负责管理距离雾效果
- **高度雾系统**：负责管理高度雾效果
- **体积云系统**：负责管理体积云效果
- **后处理系统**：负责管理后处理效果
- **水体系统**：负责管理水体效果

### 业务系统
- **场景系统**：处理地名搜索和瓦片号查询定位的业务逻辑
- **建筑系统**：负责管理和加载城市建筑模型
- **图层系统**：负责管理Cesium场景中的各种图层以及实体和图元对象
- **工具箱系统**：管理和执行各种工具，包含测量工具、地理数据处理工具等
- **测量系统**：用于在三维场景中进行各种测量操作
- **3DTiles搜索系统**：处理3D Tiles模型搜索和信息展示

## 更新日志

### V1.0.12 (2026-04-09)

#### 新增功能
- **资源路径管理系统**
  - 新增 `AssetPathResolver` 资源路径解析器，统一管理 SDK 资源路径
  - 支持开发模式自动检测和生产模式路径配置
  - 新增 `setSdkBasePath()`、`getSdkBasePath()`、`resolveAssetPath()` API
  - `RenderConfig` 新增 `assetsBasePath` 配置项

#### 资源目录调整
- 统一资源目录结构至 `assets` 和 `resources` 目录
  - `assets/Textures/` - 体积云纹理
  - `assets/Images/` - 水体法线贴图等图片资源
  - `resources/water/` - 海洋数据
  - `resources/textures/` - 其他纹理资源

### V1.0.11 (2026-04-02)

#### 新增功能
- **高度雾效果**
  - 更新高度雾效果，补充前向散射（fogG）、太阳方向散射强度（fogScattering）及环境散射占比（fogAmbient）
- **矢量瓦片系统 (VectorTileSystem)**
  - 支持加载和显示 Mapbox 矢量瓦片
  - 提供系统生命周期管理（启用、禁用、销毁）
  - 支持动态切换样式 (`setStyle()`)
  - 提供样式编辑 API：
    - `setLayoutProperty()` - 设置图层布局属性
    - `setPaintProperty()` - 设置图层绘制属性
    - `setFilter()` - 设置图层过滤器
  - 支持显示状态控制 (`setVisible()`, `getVisible()`)
  - 提供就绪状态检查 (`isReady()`)
  - 支持 Web Worker 配置以提升瓦片解析性能

### V1.0.9 (2026-03-30)

#### 新增功能
- **水体系统 (WaterSystem)**
  - 支持从 URL 或文件加载 GeoJSON 格式的水体数据
  - 提供水体效果配置（基础颜色、波纹频率、动画速度、振幅、高光强度等）
  - 支持水图层管理（添加、移除、可见性控制）
  - 实现异步分批加载，支持进度回调和取消操作
  - 支持实时更新水图层效果，无需重新加载
  - 使用 GroundPrimitive 贴地模式，确保水体与地形正确贴合
  - 共享材质优化，提升渲染性能
  - 完整的事件系统（水图层添加、移除、可见性变化）

### V1.0.7 (2026-03-26)

#### 问题修复
- **适配 DC V5.0.0 版本**
  - 由于 DC 将原生的 cesiumWidget 移除，DC 重写了新的 UI widgetContainer
  - 添加对 DC UI 的兼容性支持，确保与 DC V5.0.0 版本正常工作

### V1.0.6 (2026-03-25)

#### 问题修复
- **SunLight direction 初始化修复**
  - 修复 Cesium SunLight 在某些情况下 direction 属性未正确初始化的问题
  - 添加 direction 属性检查，若不存在则手动设置默认太阳方向
  - 使用 `Cartesian3(0, 0, 1)` 作为默认方向（正午时分的大致方向）

### V1.0.5 (2026-03-24)

#### 视域分析功能优化
- **拾取交互优化**
  - 修复拾取模式下第一个点过早显示的问题
  - 拾取开始前不显示可视化元素，只有在地图上点击后才创建
  - 添加详细的调试日志用于诊断拾取问题
  - 鼠标移动不再过早设置拾取位置，等待第一次点击

- **视域面板样式优化**
  - 视域面板水平居中（left: 50% + transform: translateX(-50%)）
  - 保持垂直位置在顶部（top: 18px）

- **多视域分析系统 (ViewshedAnalysisSystem)**
  - 支持 LACDT.Obj.ViewshedAnalysis 多视锥体模式
  - 实现 startPicking/stopPicking 方法
  - 添加拾取可视化 Entity（绿色起点、黄色终点、箭头线）
  - 鼠标移动时实时更新箭头线和视锥体方向
  - 添加右键取消拾取功能
  - 拾取期间隐藏 ViewshedAnalysis，完成后重新显示

#### 问题修复
- 修复 ShadowMap 创建缺少 context 参数的问题
- 修复 MouseEventSource 缺少 animationFrameScheduler 导入的问题（RxJS scheduler bug）

### V1.0.4 (2026-03-23)
- **CustomPrimitive Cesium 1.132+ 兼容性修复**
  - 根本原因：`VertexArray.fromGeometry` 不再支持对象字面量作为默认几何属性
  - 解决方案：使用实际的 `Cesium.GeometryAttribute` 实例和正确的 `values` 数组
  - 修改 `CustomPrimitive.js` 中的 `createVertexArray` 函数
  - 将 `normalAttribute`、`colorAttribute`、`textureCoordinate` 改为使用 `new Cesium.GeometryAttribute(...)` 而不是对象字面量

- **空气质量监测面板科技风格更新**
  - 深蓝色渐变背景配合发光边框效果
  - 动画扫描线和角落装饰
  - 大型发光数据值配合状态指示器
  - 改进排版和布局

### V1.0.0 (2026-03-04)
- **URL相机定位功能 (CameraUrlManager)**
  - 从URL参数读取相机位置
  - 相机移动时自动更新URL参数（带防抖优化）
  - 支持自定义默认相机角度
  - 支持相机位置变化回调

## 联系方式

- **官方文档**：https://sdk.geovisearth.com/
- **邮箱支持**：sunyc@geovis.com.cn