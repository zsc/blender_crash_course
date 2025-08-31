# 第16章：合成与后期处理

## 本章概述

合成与后期处理是将原始渲染输出转化为最终成品的关键环节。就像摄影中的后期调色和电影制作中的剪辑合成，Blender的合成系统为您提供了强大的工具来增强渲染效果、修正颜色、添加特效并整合多个渲染元素。本章将深入探讨Blender的合成节点系统，学习如何通过渲染层和通道实现精确控制，掌握专业的色彩校正技术，以及如何创建视觉特效。无论是制作静帧作品还是动画序列，这些技能都将帮助您将作品质量提升到专业水准。

## 16.1 合成节点系统基础

### 16.1.1 节点编辑器介绍

Blender的合成系统基于节点工作流，这种非破坏性的编辑方式让您可以灵活地调整和实验各种效果。

```
输入节点 ─→ 处理节点 ─→ 输出节点
   │           │           │
[渲染层]    [滤镜效果]   [合成结果]
[图像]      [色彩调整]   [查看器]
[纹理]      [变形扭曲]   [文件输出]
```

### 16.1.2 启用合成功能

要开始使用合成系统：
1. 切换到Compositing工作区
2. 勾选"Use Nodes"选项
3. 连接Render Layers节点到Composite节点

基本节点流程：
```
[Render Layers] ──Image──→ [Color Balance] ──Image──→ [Composite]
                           ↑                          ↓
                      调整色彩平衡              最终输出
```

### 16.1.3 节点类型分类

**输入节点（Input）**
- Render Layers：渲染层输入
- Image：静态图像
- Movie Clip：视频片段
- Mask：遮罩输入
- RGB：纯色输入
- Value：数值输入
- Texture：纹理输入
- Time：时间曲线

**输出节点（Output）**
- Composite：最终合成输出
- Viewer：预览输出
- File Output：文件输出
- Split Viewer：分屏对比

**颜色节点（Color）**
- RGB Curves：RGB曲线
- Color Balance：色彩平衡
- Hue/Saturation：色相饱和度
- Brightness/Contrast：亮度对比度
- Gamma：伽马校正
- Color Correction：色彩校正
- Tonemap：色调映射

### 16.1.4 节点连接原则

节点之间通过插槽（Socket）连接，不同颜色代表不同数据类型：

```
黄色 ● ── 颜色数据（Color/Image）
灰色 ● ── 数值数据（Value/Factor）
蓝色 ● ── 矢量数据（Vector）
```

连接规则：
- 相同类型可直接连接
- 不同类型需要转换节点
- 一个输出可连接多个输入
- 一个输入只能接受一个连接

## 16.2 渲染层与通道管理

### 16.2.1 渲染层（Render Layers）

渲染层允许您将场景分成多个部分分别渲染，便于后期合成和调整。

层级组织示例：
```
场景结构：
├── 前景层（Characters）
│   ├── 主角
│   └── 配角
├── 中景层（Props）
│   ├── 道具
│   └── 家具
└── 背景层（Environment）
    ├── 建筑
    └── 天空
```

### 16.2.2 渲染通道（Render Passes）

渲染通道将渲染结果分解为独立的元素：

**基础通道：**
- Combined：完整渲染结果
- Z：深度信息
- Mist：雾效深度
- Normal：法线信息
- UV：UV坐标
- Object Index：物体索引
- Material Index：材质索引

**光照通道：**
- Diffuse：漫反射
- Glossy：光泽反射
- Transmission：透射
- Emission：自发光
- Environment：环境光
- Shadow：阴影

**数据通道：**
- AO：环境光遮蔽
- Vector：运动矢量
- Denoising：降噪数据
- Cryptomatte：加密遮罩

### 16.2.3 通道合成技术

基础合成公式：
```
最终图像 = 漫反射 × 阴影 + 光泽反射 + 自发光 + 环境光
```

高级合成流程：
```
[Diffuse] ────→ [Multiply] ───┐
                     ↑         │
[Shadow] ────────────┘         ↓
                          [Add Node] ──→ [Composite]
[Glossy] ──────────────────────↑
```

### 16.2.4 Cryptomatte工作流

Cryptomatte提供自动生成的物体遮罩，极大简化了后期选择和调整：

1. 启用Cryptomatte通道
2. 在合成中添加Cryptomatte节点
3. 使用吸管工具选择物体
4. 应用独立的调整效果

## 16.3 色彩校正技术

### 16.3.1 色彩理论基础

**色彩空间：**
```
线性空间 ←→ sRGB空间 ←→ 显示空间
    ↓           ↓           ↓
  计算用     存储用      观看用
```

**色彩三要素：**
- 色相（Hue）：颜色的种类
- 饱和度（Saturation）：颜色的纯度
- 明度（Value/Brightness）：颜色的明暗

### 16.3.2 基础色彩调整

**亮度与对比度：**
```
输出 = (输入 - 0.5) × 对比度 + 0.5 + 亮度
```

**伽马校正：**
```
输出 = 输入^(1/Gamma)
```

**色彩平衡调整流程：**
```
[Render] → [Color Balance] → [RGB Curves] → [Output]
            ↑                  ↑
       调整高光/中间调/阴影   精细调整曲线
```

### 16.3.3 高级色彩分级

**三路色彩校正（Three-Way Color Correction）：**
- Lift：调整暗部（黑场）
- Gamma：调整中间调
- Gain：调整亮部（白场）

**查找表（LUT）应用：**
1. 导入LUT文件
2. 使用Color Lookup节点
3. 调整强度混合

### 16.3.4 色彩匹配技术

场景色彩统一：
```
参考图像 ──→ [分析色彩] ──→ 色彩配置文件
                           ↓
目标渲染 ──→ [应用配置] ──→ 匹配结果
```

## 16.4 特效合成技术

### 16.4.1 景深效果

使用Z深度通道创建景深：
```
[Image] ────────→ [Defocus] ──→ [Output]
                      ↑
[Z Depth] ──→ [Map Value] 
              (调整焦点范围)
```

景深参数控制：
- F-stop：光圈大小
- Max Blur：最大模糊量
- Threshold：阈值控制
- Preview：预览质量

### 16.4.2 辉光与泛光

创建辉光效果流程：
```
[Render] ──→ [Glare] ──→ [Mix] ──→ [Output]
     ↓         ↑           ↑
     └─────────┴───────────┘
         保留原始图像
```

辉光类型：
- Ghosts：鬼影效果
- Streaks：条纹效果
- Fog Glow：雾化辉光
- Simple Star：简单星芒

### 16.4.3 运动模糊

矢量运动模糊：
```
[Image] ───────→ [Vector Blur] ──→ [Output]
                      ↑
[Speed Vector] ───────┘
```

控制参数：
- Samples：采样数量
- Blur：模糊强度
- Speed：速度因子
- Curved：曲线模糊

### 16.4.4 镜头畸变

镜头效果模拟：
```
[Input] → [Lens Distortion] → [Chromatic Aberration] → [Vignette] → [Output]
             ↑                        ↑                      ↑
          桶形/枕形              色差分离              暗角效果
```

### 16.4.5 大气效果

雾效合成：
```
[Scene] ────────→ [Mix] ──→ [Output]
                    ↑
[Mist Pass] → [ColorRamp] → [Fog Color]
              (控制雾的渐变)
```

## 16.5 高级合成技术

### 16.5.1 抠像技术

绿幕抠像流程：
```
[Green Screen] → [Keying] → [Despill] → [Edge Blur] → [Composite]
                     ↑           ↑            ↑
                 色键抠像    去除溢色    边缘柔化
```

抠像质量优化：
1. 预处理：降噪、色彩校正
2. 核心抠像：选择合适的抠像算法
3. 边缘处理：收缩、羽化、柔化
4. 溢色处理：去除绿色反射
5. 细节恢复：头发、半透明物体

### 16.5.2 多层合成

复杂场景合成结构：
```
背景层 ────┐
          ↓
中景层 → [Alpha Over] ────┐
                         ↓
前景层 ────────────→ [Alpha Over] → [色彩校正] → [输出]
                         ↑
特效层 ──────────────────┘
```

### 16.5.3 遮罩与蒙版

遮罩应用技术：
```
[原始图像] ──────→ [Mix Node] ──→ [输出]
                      ↑
[效果处理] ──────────┤
                      ↑
[Mask] → [Blur] ─────┘
         (羽化遮罩)
```

### 16.5.4 时间重映射

速度控制：
- 慢动作：插值增加帧数
- 快动作：跳帧减少帧数
- 变速：使用时间曲线控制
- 定格：重复特定帧

## 16.6 输出与格式管理

### 16.6.1 文件输出节点

多通道输出设置：
```
[Render Layers] ──┬→ [File Output]
                  │   ├── Beauty Pass (EXR)
                  │   ├── Z Depth (PNG)
                  │   ├── Normal (EXR)
                  │   └── Cryptomatte (EXR)
                  │
                  └→ [Composite]
                      (预览输出)
```

### 16.6.2 格式选择策略

**工作格式：**
- EXR：32位浮点，无损，多通道
- TIFF：16位，无损，兼容性好
- PNG：8/16位，无损，通用格式

**交付格式：**
- H.264：视频压缩，网络发布
- ProRes：视频编辑，专业交付
- JPEG：静帧预览，快速分享

### 16.6.3 色彩管理设置

**显示变换：**
- Standard：标准sRGB显示
- Filmic：高动态范围映射
- Filmic Log：对数曲线显示
- Raw：线性无变换
- False Color：曝光检查

**视图变换参数：**
- Exposure：曝光调整
- Gamma：显示伽马
- Look：预设风格

## 本章小结

合成与后期处理是将3D渲染提升到专业水准的关键环节。通过本章学习，您已经掌握了：

1. **节点系统基础**：理解了Blender合成节点的工作原理，学会了基本的节点连接和数据流管理。

2. **渲染层与通道**：掌握了如何分离渲染元素，使用不同的渲染通道进行精确控制，以及Cryptomatte的高效工作流。

3. **色彩校正技术**：从基础的亮度对比度调整到高级的三路色彩校正，您现在能够对渲染结果进行专业的色彩分级。

4. **特效合成**：学会了创建景深、辉光、运动模糊等常见特效，以及绿幕抠像和多层合成等高级技术。

5. **输出管理**：理解了不同文件格式的特点和应用场景，能够根据项目需求选择合适的输出格式。

关键概念回顾：
- 非破坏性编辑：节点系统保持原始数据不变
- 通道分离：将复杂渲染分解为可控元素
- 色彩空间：理解线性工作流的重要性
- 合成层级：从后到前的层次关系
- 效率优化：合理使用节点避免重复计算

## 常见陷阱与错误

### 错误1：色彩空间混淆
**问题**：在sRGB图像上进行线性计算导致结果错误
**解决**：确保在合成前将所有输入转换到正确的色彩空间

### 错误2：节点连接循环
**问题**：创建循环连接导致Blender崩溃或计算错误
**解决**：始终保持单向数据流，避免节点输出连回自身输入

### 错误3：过度处理
**问题**：叠加太多效果导致图像质量下降
**解决**：适度使用效果，保持自然感，定期对比原始图像

### 错误4：忽视渲染顺序
**问题**：Alpha通道处理不当导致边缘问题
**解决**：使用Premultiplied或Straight Alpha，确保正确的混合模式

### 错误5：分辨率不匹配
**问题**：不同分辨率的元素合成出现对齐问题
**解决**：统一所有素材分辨率或使用Scale节点适配

### 调试技巧：
1. 使用Viewer节点检查中间结果
2. 临时断开连接隔离问题节点
3. 使用Mute功能快速开关节点效果
4. 保存节点组便于重复使用
5. 使用Frame节点组织复杂节点树

## 最佳实践检查清单

### 项目开始前：
- [ ] 确定最终输出格式和分辨率
- [ ] 设置正确的色彩管理配置
- [ ] 规划渲染层和通道需求
- [ ] 准备必要的参考图像和LUT

### 合成过程中：
- [ ] 保持节点树组织清晰
- [ ] 为复杂节点组添加注释
- [ ] 定期保存不同版本
- [ ] 使用节点组封装重复功能
- [ ] 监控内存使用避免崩溃

### 色彩校正时：
- [ ] 先调整整体曝光和对比度
- [ ] 然后处理色彩平衡
- [ ] 最后进行局部调整
- [ ] 在标准显示器上检查
- [ ] 导出前检查不同亮度环境

### 特效添加时：
- [ ] 效果强度从小开始逐步增加
- [ ] 保持效果与场景风格一致
- [ ] 注意保留必要的细节
- [ ] 避免过度使用流行效果
- [ ] 考虑渲染时间成本

### 输出检查：
- [ ] 确认色彩空间正确
- [ ] 检查Alpha通道处理
- [ ] 验证输出分辨率
- [ ] 测试在目标平台播放
- [ ] 保留项目文件和素材

## 练习题

### 基础题

#### 练习16.1：节点系统入门
创建一个简单的合成设置，对渲染图像应用基础色彩调整。
- 使用Color Balance节点调整色温
- 添加Brightness/Contrast节点增强对比度
- 使用RGB Curves微调颜色

**提示（Hint）**：先连接基础的输入输出节点，然后在中间插入处理节点。记住节点的执行顺序会影响最终效果。

<details>
<summary>参考答案</summary>

建立以下节点链：
1. Render Layers节点作为输入源
2. 首先连接到Color Balance节点，调整Lift（暗部）偏冷，Gain（亮部）偏暖，营造自然的色温对比
3. 输出连接到Brightness/Contrast节点，轻微提高对比度（1.2-1.5），保持亮度为0
4. 最后通过RGB Curves节点，创建轻微的S曲线增强对比，可单独调整红绿蓝通道平衡颜色
5. 连接到Composite节点输出

关键参数建议：
- Color Balance: Lift向蓝色偏移0.1，Gain向黄色偏移0.1
- Contrast: 1.3左右
- RGB Curves: 整体S曲线，中间点坐标约(0.5, 0.5)不变，暗部点下压至(0.25, 0.2)，亮部点上提至(0.75, 0.8)
</details>

#### 练习16.2：渲染通道分离
使用一个包含不同材质物体的场景，分离并单独调整各个渲染通道。
- 分离Diffuse和Glossy通道
- 单独增强Glossy通道的强度
- 重新合成为最终图像

**提示（Hint）**：在渲染设置中启用所需的渲染通道，然后在合成中使用Render Layers节点的不同输出插槽。

<details>
<summary>参考答案</summary>

设置步骤：
1. 在View Layer Properties中启用Diffuse > Direct和Glossy > Direct通道
2. 在合成编辑器中：
   - 从Render Layers节点拖出Diffuse Direct和Glossy Direct输出
   - Glossy Direct连接到Math节点（Multiply模式），第二个值设为1.5-2.0增强反射
   - 使用Add节点将增强后的Glossy与Diffuse相加
   - 如果场景有Emission，也需要加入
   - 最终连接到Composite输出

优化建议：
- 可以对Diffuse通道单独进行色彩校正，使基础色更丰富
- Glossy增强不宜过度，避免过曝
- 保存节点组方便重复使用
</details>

#### 练习16.3：景深效果制作
为一个产品渲染添加真实的景深效果。
- 使用Z深度通道
- 调整焦点位置
- 控制模糊程度模拟不同光圈

**提示（Hint）**：Defocus节点需要Z深度信息来确定模糊程度。使用Map Value节点可以调整深度范围。

<details>
<summary>参考答案</summary>

实现流程：
1. 启用Mist通道或使用Z深度通道
2. 节点设置：
   - Render Layers的Image输出连接到Defocus节点的Image输入
   - Z输出连接到Map Value节点进行范围调整
   - Map Value输出连接到Defocus的Z输入
3. Defocus节点参数：
   - 勾选"Use Z-Buffer"
   - F-stop设为2.8-5.6模拟真实镜头
   - Max Blur设为20-50像素
4. Map Value调整：
   - 通过Offset调整焦点位置（负值近焦，正值远焦）
   - Size控制深度范围（越小景深越浅）

技巧：使用Viewer节点预览Z深度，帮助理解深度分布
</details>

#### 练习16.4：色彩匹配
将渲染结果的色彩风格匹配到参考图像。
- 分析参考图像的色彩特征
- 调整渲染的色彩平衡
- 匹配整体氛围

**提示（Hint）**：观察参考图的高光、中间调和阴影的色彩倾向，使用Color Balance节点的三路调整。

<details>
<summary>参考答案</summary>

匹配流程：
1. 导入参考图像到合成编辑器
2. 使用Viewer节点对比查看
3. 分析参考图：
   - 识别暗部色调（通常偏蓝/青）
   - 中间调色温（暖色或冷色主导）
   - 高光色彩（常见暖黄色）
4. 应用调整：
   - Color Balance节点：
     * Lift（暗部）：匹配参考的阴影色
     * Gamma（中间调）：调整主体色温
     * Gain（亮部）：匹配高光色调
   - 使用Hue/Saturation微调特定颜色
   - RGB Curves做最终微调

验证方法：将结果与参考图并排对比，或使用Mix节点快速切换查看
</details>

### 挑战题

#### 练习16.5：高级抠像合成
完成一个绿幕抠像并将角色合成到新背景中。
- 实现干净的抠像效果
- 处理边缘和半透明区域
- 匹配光照和色彩
- 添加接触阴影

**提示（Hint）**：抠像质量的关键在于边缘处理。使用多个节点逐步优化，不要试图用单个节点解决所有问题。

<details>
<summary>参考答案</summary>

完整抠像流程：
1. **预处理阶段**：
   - 使用Denoise节点减少绿幕噪点
   - Color Correction节点增强绿色通道对比度

2. **核心抠像**：
   - Keying节点：
     * Key Color用吸管选择绿幕主色
     * Despill Factor: 0.8-1.0
     * Edge Kernel Radius: 3
     * Edge Kernel Tolerance: 0.1
     * Clip Black: 0.0-0.1
     * Clip White: 0.9-1.0

3. **边缘优化**：
   - 抠像输出的Matte连接到Dilate/Erode节点（Distance: -1到-2）收缩边缘
   - 接Blur节点（Size: 2-5）柔化边缘
   - 可选：使用第二个Keying节点专门处理边缘区域

4. **溢色处理**：
   - 在Keying节点后添加Color Spill节点
   - 或使用Hue/Saturation降低绿色饱和度

5. **合成集成**：
   - 使用Alpha Over节点合成到新背景
   - 添加Color Balance匹配背景光照色温
   - 使用轻微的Blur模拟景深匹配

6. **接触阴影**：
   - 复制抠出的人物图层
   - 应用Blur（Size: 20-50）
   - 使用Transform节点压扁并定位到接触面
   - Mix节点以Multiply模式混合，Factor: 0.3-0.5

7. **最终调整**：
   - 整体Color Correction统一色调
   - 可选添加轻微的Film Grain匹配背景颗粒感
</details>

#### 练习16.6：动态效果合成
创建一个包含多种动态效果的动作场景合成。
- 运动模糊
- 镜头光晕
- 色差效果
- 速度线效果

**提示（Hint）**：使用Vector pass创建真实的运动模糊，结合多个效果时注意叠加顺序。

<details>
<summary>参考答案</summary>

动态效果实现：
1. **运动模糊设置**：
   - 渲染设置中启用Vector通道
   - Vector Blur节点：
     * Samples: 32（质量与速度平衡）
     * Blur Factor: 0.5-1.0
     * Speed设置：Min: 0, Max: 1024
     * 勾选Curved获得弧形模糊

2. **镜头光晕**：
   - 使用Glare节点：
     * Type: Streaks
     * Quality: High
     * Iterations: 3
     * Color Modulation: 0.5
     * Angle Offset: 15度间隔
   - 仅对高亮区域应用（使用Luminance Key分离）

3. **色差效果**：
   - 方法1：使用Lens Distortion节点的Dispersion选项
   - 方法2：手动分离RGB通道
     * Separate RGB节点分离通道
     * 对R和B通道分别应用轻微的Scale
     * R通道: 1.002, B通道: 0.998
     * Combine RGB重新合成

4. **速度线效果**：
   - 使用Directional Blur节点：
     * 从中心向外的放射状模糊
     * Iterations: 6-10
     * 配合Mask限定效果区域
   - 或使用Speed Vector信息生成

5. **合成顺序**（重要）：
   - 原始渲染 → 运动模糊 → 色差 → 光晕 → 速度线 → 最终色彩校正

6. **优化技巧**：
   - 使用Mask节点限定效果范围提高效率
   - 将常用效果组合保存为节点组
   - 渲染预览使用较低采样值
</details>

#### 练习16.7：电影级调色
创建三种不同的电影风格调色预设。
- 科幻片冷色调（青橙对比）
- 复古胶片风格
- 高对比黑白效果

**提示（Hint）**：电影调色的关键是色彩的选择性和层次感，不是简单的整体调整。

<details>
<summary>参考答案</summary>

**1. 科幻片冷色调（青橙对比）**：
- Color Balance基础调整：
  * Lift: 偏青色 (0, -0.05, -0.1)
  * Gamma: 轻微偏青 (0, -0.02, -0.05)
  * Gain: 偏橙色 (0.05, 0.02, 0)
- RGB Curves：
  * 整体轻微S曲线增加对比
  * 蓝色通道：暗部上提，亮部下压
  * 红色通道：亮部轻微上提
- Hue/Saturation：
  * 整体饱和度: 0.9
  * 单独增强橙色和青色饱和度
- 添加轻微Vignette暗角

**2. 复古胶片风格**：
- 基础色调：
  * Color Balance整体偏暖
  * Lift: (0.05, 0.03, 0)
  * Gamma: (0.02, 0.01, 0)
  * Gain: (0, -0.02, -0.05)
- 褪色效果：
  * RGB Curves提升黑场（暗部不纯黑）
  * 各通道最低点提升到0.05-0.1
- 颗粒添加：
  * 使用Noise Texture → ColorRamp → Mix(Overlay)
  * Factor: 0.05-0.1
- 色彩分离：
  * 轻微Chromatic Aberration
  * Lens Distortion: Dispersion 0.01
- 最终添加Film响应曲线LUT

**3. 高对比黑白效果**：
- 转换为黑白：
  * 方法1: Hue/Saturation，Saturation设为0
  * 方法2: 使用RGB to BW节点（更多控制）
- 色彩通道混合（模拟滤镜）：
  * Separate RGB → 加权混合
  * 红色滤镜效果: R*0.5 + G*0.3 + B*0.2
  * 黄色滤镜效果: R*0.4 + G*0.4 + B*0.2
- 对比度增强：
  * RGB Curves创建强S曲线
  * 关键点: (0.2, 0.05), (0.5, 0.5), (0.8, 0.95)
- 局部对比：
  * 使用High Pass技术
  * Blur节点 → Subtract → Overlay混合
- 可选：添加Film Grain增加质感

**保存预设**：
- 将每个风格保存为节点组
- 添加自定义属性控制强度
- 使用Mix Factor提供可调整的效果强度
</details>

#### 练习16.8：复杂多层合成项目
完成一个包含至少5个渲染层的复杂合成项目。
- 背景环境层
- 中景建筑层
- 前景角色层
- 特效层
- 大气效果层
每层独立调整并完美融合

**提示（Hint）**：使用File Output节点保存各层的中间结果，方便调试和修改。合理使用Cryptomatte进行精确的遮罩控制。

<details>
<summary>参考答案</summary>

**项目结构设置**：

1. **渲染层规划**：
   - Layer 1: 天空和远景（HDR背景）
   - Layer 2: 中景建筑（主体结构）
   - Layer 3: 前景角色（主要动作）
   - Layer 4: 粒子特效（烟雾、火花）
   - Layer 5: 体积雾（大气深度）

2. **各层处理流程**：

**Layer 1 - 背景环境**：
- 基础HDRI → Exposure调整 → Blur轻微模糊模拟景深
- Color Balance调整符合场景时间
- 输出：Beauty + Mist

**Layer 2 - 中景建筑**：
- 启用Cryptomatte（Object）
- Shadow、AO、Diffuse、Glossy分离
- 单独调整各通道后重组
- 边缘Light Wrap效果融合背景光
- 输出：Beauty + Cryptomatte + Z-depth

**Layer 3 - 前景角色**：
- 启用所有光照通道
- Motion Vector用于运动模糊
- Cryptomatte（Material）分离不同材质
- SSS单独处理皮肤
- 输出：Beauty + Vector + Cryptomatte

**Layer 4 - 特效层**：
- 使用Emission通道
- Glare节点增强发光
- 单独的Alpha通道控制
- 输出：RGBA + Emission

**Layer 5 - 大气效果**：
- Volume渲染分离
- 使用Z-depth控制密度
- 输出：Volume + Z-depth

3. **合成集成**：

```
背景层 ──────────────┐
                    ↓
建筑层 → Alpha Over ─┼─→ Fog混合 ─┐
                    ↓             ↓
角色层 → Alpha Over ─┘             ├→ 特效叠加 → 最终输出
                                  ↑
特效层 → Add/Screen ──────────────┘
```

4. **关键技术点**：

**深度排序**：
- 使用Z-Combine节点自动处理遮挡关系
- 手动用Cryptomatte创建精确遮罩

**光照统一**：
- 创建"光照参考"节点组
- 所有层使用相同的Color Balance设置

**边缘融合**：
- Dilate/Erode处理边缘
- 使用Bilateral Blur保持边缘锐利
- Light Wrap技术：
  * 背景模糊 → 提取亮部 → 边缘混合

**性能优化**：
- 使用Render Region渲染局部测试
- File Output保存中间结果
- 复杂节点使用Mute临时禁用
- 最终渲染前简化节点树

5. **质量检查清单**：
- [ ] 所有层的透视匹配
- [ ] 光照方向一致性
- [ ] 色彩空间统一
- [ ] 边缘无穿帮
- [ ] 运动模糊自然
- [ ] 景深关系正确
- [ ] 大气透视合理
- [ ] 最终色彩和谐

6. **输出设置**：
- 主输出：EXR 32位全通道
- 预览输出：PNG/JPEG sRGB
- 存档：保留所有中间层EXR
- 备份：节点设置截图
</details>