# Map SDK - 3D Earth Rendering Engine

![Rendering Engine Preview](https://gitee.com/ASun001/Resource/raw/master/RenderPreview.png)

## Project Introduction

The LACDT Render WebSDK is a 3D Earth rendering engine based on Cesium, providing rich map operations and data analysis capabilities. It adopts a modular architecture with multiple functional systems, supporting map loading, layer management, building model display, measurement tools, 3D Tiles search, and more.

## Core Features

### Map Rendering
- Support for multiple map data sources
- Rich map rendering configuration options
- Map switching and base map control

### Layer Management
- Support for image layers, terrain layers, 3D Tiles layers, and more
- Layer visibility and transparency control
- Layer addition, removal, and search

### Building Model Display
- Support for loading city building models
- City data search functionality
- Display of production areas and building boundaries

### Measurement Tools
- Straight-line distance measurement
- Ground-adhering distance measurement
- Area measurement
- Height measurement
- Azimuth measurement

### 3D Tiles Search
- Attribute query functionality
- Object highlighting display
- 3D Tiles model loading and operation

### Scene Control
- Map positioning and flight
- Scene state management
- Environmental effect control

## Quick Start

### Installation

```bash
npm install @metagl/sdk-render
```

### Initialization

```typescript
import { Render, RenderConfig } from '@metagl/sdk-render';
import { Viewer } from 'cesium';

// Initialize Cesium Viewer
const container = document.getElementById('cesiumContainer');
const viewer = new Viewer(container);

// Configure rendering engine
const config: RenderConfig = {
    viewer: viewer,
    token: 'your-access-token',  // Token for accessing LACDT resources (required)
    assetsBasePath: '/'          // Base path for resource files (optional, auto-detected by default)
};

// Initialize rendering engine (async method)
const render = await Render.create(config);
```

**Token Description**:
- `token` is a required parameter for accessing LACDT resources

**Resource Path Description**:
- `assetsBasePath` specifies the base path for SDK resource files (textures, data, etc.)
- Auto-detection is enabled by default, but manual configuration may be needed in some bundlers (e.g., Vite)
- Development and production modes may require different paths

### Resource Path Configuration

SDK requires loading resource files (textures, data, etc.). Proper path configuration is essential:

#### Method 1: Set During Initialization (Recommended)

```typescript
const render = await Render.create({
    viewer: viewer,
    token: 'your-token',
    assetsBasePath: '/'  // Resources at website root
});
```

#### Method 2: Manual Setup

```typescript
import { setSdkBasePath } from '@metagl/sdk-render';

// Set before using any system
setSdkBasePath('/');
```

#### Method 3: Global Variable Setup

```html
<script>
  window.__SDK_RENDER_BASE_PATH__ = '/';
</script>
<script src="lacdt.render.js"></script>
```

### Build Tool Configuration

#### Vite Project

Install `vite-plugin-static-copy` plugin:

```bash
npm install -D vite-plugin-static-copy
```

Configure `vite.config.js`:

```javascript
import { viteStaticCopy } from 'vite-plugin-static-copy'

export default defineConfig({
  plugins: [
    viteStaticCopy({
      targets: [
        // Cesium resources
        { src: 'node_modules/cesium/Build/Cesium/*', dest: 'cesium' },
        // SDK resources
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

Then set in your code:

```typescript
import { setSdkBasePath } from '@metagl/sdk-render';
setSdkBasePath('/');
```

#### Webpack Project

Add to `webpack.config.js`:

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

### Basic Usage

```typescript
// Update system configuration
render.updateSystemConfig('lighting', {
    intensity: 1.2,
    color: Cesium.Color.YELLOW
});

// Enable system
render.enableSystem('lighting');

// Disable system
render.disableSystem('lighting');

// Toggle system state
render.toggleSystem('volumetricClouds');

// Get system configuration
const lightingConfig = render.getSystemConfig('lighting');
console.log('Lighting config:', lightingConfig);

// Get system status
const shadowStatus = render.getSystemStatus('shadow');
console.log('Shadow status:', shadowStatus);

// Apply environment preset configuration
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

// Get debug information
const debugInfo = render.getDebugInfo();
console.log('Debug info:', debugInfo);
```

## System Architecture

The SDK rendering engine adopts a modular architecture, including the following systems:

### Rendering Systems
- **Lighting System**: Manages scene lighting
- **Shadow System**: Manages scene shadows
- **Atmospheric Scattering System**: Manages atmospheric scattering effects
- **Distance Fog System**: Manages distance fog effects
- **Height Fog System**: Manages height fog effects
- **Volumetric Clouds System**: Manages volumetric cloud effects
- **Post-Processing System**: Manages post-processing effects
- **Water System**: Manages water body effects

### Business Systems
- **Scene System**: Handles place name search and tile number query positioning business logic
- **Building System**: Manages and loads city building models
- **Layer System**: Manages various layers, entities, and primitives in the Cesium scene
- **Toolbox System**: Manages and executes various tools, including measurement tools, geographic data processing tools, etc.
- **Measurement System**: Performs various measurement operations in 3D scenes
- **3DTiles Search System**: Handles 3D Tiles model search and information display

## Changelog

### V1.0.14 (2026-04-09)

#### New Features
- **Resource Path Management System**
  - Added `AssetPathResolver` for unified SDK resource path management
  - Support for auto-detection in development mode and path configuration in production mode
  - Added `setSdkBasePath()`, `getSdkBasePath()`, `resolveAssetPath()` APIs
  - Added `assetsBasePath` configuration option to `RenderConfig`

#### Resource Directory Adjustment
- Unified resource directory structure to `assets` and `resources` directories
  - `assets/Textures/` - Volumetric cloud textures
  - `assets/Images/` - Water normal maps and other image resources
  - `resources/water/` - Ocean data
  - `resources/textures/` - Other texture resources

### V1.0.11 (2026-04-02)

#### New Features
- **Height Fog Effect**
  - Updated height fog effect, added forward scattering (fogG), sun direction scattering intensity (fogScattering), and ambient scattering ratio (fogAmbient)
- **Vector Tile System (VectorTileSystem)**
  - Support loading and displaying Mapbox vector tiles
  - Provide system lifecycle management (enable, disable, destroy)
  - Support dynamic style switching (`setStyle()`)
  - Provide style editing APIs:
    - `setLayoutProperty()` - Set layer layout property
    - `setPaintProperty()` - Set layer paint property
    - `setFilter()` - Set layer filter
  - Support visibility control (`setVisible()`, `getVisible()`)
  - Provide ready state check (`isReady()`)
  - Support Web Worker configuration for improved tile parsing performance

### V1.0.9 (2026-03-30)

#### New Features
- **Water System (WaterSystem)**
  - Support loading GeoJSON format water data from URL or file
  - Provide water effect configuration (base color, wave frequency, animation speed, amplitude, specular intensity, etc.)
  - Support water layer management (add, remove, visibility control)
  - Implement asynchronous batch loading with progress callback and cancellation support
  - Support real-time water layer effect updates without reloading
  - Use GroundPrimitive ground-clamping mode to ensure water aligns correctly with terrain
  - Shared material optimization for improved rendering performance
  - Complete event system (water layer add, remove, visibility change)

### V1.0.7 (2026-03-26)

#### Bug Fixes
- **DC V5.0.0 Compatibility**
  - DC removed the native cesiumWidget and rewrote a new UI widgetContainer
  - Added compatibility support for DC UI to ensure proper operation with DC V5.0.0

### V1.0.6 (2026-03-25)

#### Bug Fixes
- **SunLight direction initialization fix**
  - Fixed issue where Cesium SunLight direction property was not properly initialized in some cases
  - Added direction property check, manually setting default sun direction if not present
  - Using `Cartesian3(0, 0, 1)` as default direction (approximate noon sun direction)

### V1.0.5 (2026-03-24)

#### Viewshed Analysis Feature Optimization
- **Picking Interaction Optimization**
  - Fixed issue where first point was displayed prematurely in picking mode
  - No visualization elements shown before picking starts, only created after clicking on map
  - Added detailed debug logs for diagnosing picking issues
  - Mouse move no longer sets picking position prematurely, waiting for first click

- **Viewshed Panel Style Optimization**
  - Viewshed panel centered horizontally (left: 50% + transform: translateX(-50%))
  - Maintains vertical position at top (top: 18px)

- **Multi-Viewshed Analysis System (ViewshedAnalysisSystem)**
  - Supports LACDT.Obj.ViewshedAnalysis multi-frustum mode
  - Implemented startPicking/stopPicking methods
  - Added picking visualization Entity (green start point, yellow end point, arrow line)
  - Real-time update of arrow line and frustum direction during mouse move
  - Added right-click to cancel picking
  - Hide ViewshedAnalysis during picking, show again after completion

#### Bug Fixes
- Fixed ShadowMap creation missing context parameter issue
- Fixed MouseEventSource missing animationFrameScheduler import (RxJS scheduler bug)

### V1.0.4 (2026-03-23)
- **CustomPrimitive Cesium 1.132+ Compatibility Fix**
  - Root cause: `VertexArray.fromGeometry` no longer supports object literals for default geometry attributes
  - Solution: Use actual `Cesium.GeometryAttribute` instances with proper `values` arrays
  - Modified `createVertexArray` function in `CustomPrimitive.js`
  - Changed `normalAttribute`, `colorAttribute`, `textureCoordinate` to use `new Cesium.GeometryAttribute(...)` instead of object literals

- **Air Quality Monitoring Panel Tech Style Update**
  - Deep blue gradient background with glowing border effects
  - Animated scan line and corner decorations
  - Large glowing data values with status indicators
  - Improved typography and layout

### V1.0.0 (2026-03-04)
- **URL Camera Positioning Feature (CameraUrlManager)**
  - Read camera position from URL parameters
  - Automatically update URL parameters when camera moves (with debounce optimization)
  - Support for custom default camera angles
  - Callback support for camera position changes

## Contact Information

- **Official Documentation**: https://sdk.geovisearth.com/
- **Email Support**: sunyc@geovis.com.cn
