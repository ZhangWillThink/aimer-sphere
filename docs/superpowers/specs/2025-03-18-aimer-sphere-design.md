# Aimer Sphere - FPS 瞄准训练平台设计文档

## 项目概述

Aimer Sphere 是一个基于 Vue 3 的 FPS 鼠标控制训练平台，采用电竞霓虹风格设计，旨在帮助 FPS 玩家提升瞄准精度、反应速度和鼠标控制能力。

## 设计风格

### 视觉风格：电竞霓虹（Cyberpunk Esports）

**核心配色**
- 背景色：`#0a0a0f`（深空黑）
- 主强调色：`#00f5d4`（霓虹青）→ `#bc13fe`（霓虹紫）渐变
- 次强调色：`#ff006e`（霓虹粉）
- 成功色：`#00ff88`（霓虹绿）
- 警告色：`#ffaa00`（霓虹橙）
- 文字主色：`#ffffff`
- 文字次色：`rgba(255,255,255,0.6)`

**视觉效果**
- 霓虹光晕发光效果（box-shadow + filter: glow）
- 动态背景粒子系统（可选开关）
- 玻璃态卡片（backdrop-filter: blur）
- 目标脉冲/呼吸动画
- 平滑过渡动画（cubic-bezier(0.4, 0, 0.2, 1)）

### 字体
- 主字体：`Rajdhani`（Google Fonts）- 科技感无衬线体
- 数字字体：`JetBrains Mono` - 等宽显示数字

## 训练模式

### 1. 网格点击训练（Grid Shot）

**玩法**
- 3x3 或 4x4 网格，目标按顺序出现
- 用户需要按数字顺序点击目标
- 训练时长：30/60/90/120 秒可选
- 目标大小：小(40px)/中(60px)/大(80px)可选

**计分**
- 命中得分：+100 分
- 失误扣分：-50 分
- 准确度 = 命中次数 / 总点击次数 × 100%

**数据追踪**
- 每秒得分（Score Per Second）
- 平均反应时间
- 命中率

### 2. 随机点击训练（Flick Shot）

**玩法**
- 目标在屏幕随机位置出现
- 点击后目标消失，新目标立即出现
- 限时内点击尽可能多的目标
- 目标大小可调

**计分**
- 命中：+100 分
- 失误（未命中目标）：-25 分
- 连击奖励：连续命中增加倍数（x1, x1.5, x2, x2.5, x3），最大 x3，失误后重置为 x1

### 3. 反应速度训练（Reaction Shot）

**玩法**
- 目标在随机延迟后出现（0.5-3秒随机）
- 用户需在目标出现后尽快点击
- 目标自动消失时间：2秒

**计分**
- 基于反应时间得分：<200ms = 500分, <300ms = 400分, <400ms = 300分
- 错过目标：0分

**数据追踪**
- 平均反应时间（ms）
- 最快/最慢反应时间
- 命中率

### 4. 追踪训练（Tracking）

**玩法**
- 目标在屏幕上移动，用户需要持续将鼠标悬停在目标上
- 多种运动模式可选：
  - 直线匀速：水平/垂直/对角线移动
  - 圆周运动：绕中心点旋转
  - 随机变速：随机方向和速度
  - 8字运动：∞ 形状轨迹

**计分**
- 追踪时间百分比：鼠标在目标上的时间 / 总时间
- 准确度得分：追踪精度 × 1000

**数据追踪**
- 追踪准确度（%）
- 平均偏离距离
- 最佳追踪时段

### 5. 压枪训练（Recoil Control）

**玩法**
- 第一人称视角，屏幕中央准星
- 按住鼠标持续射击，模拟枪械后坐力
- 用户需要通过鼠标下移来抵消后坐力
- 弹着点实时显示在目标上

**后坐力模式**
- 简单模式：固定垂直后坐力
- 进阶模式：垂直 + 随机水平偏移
- 专家模式：模拟真实枪械后坐力曲线

**计分**
- 集中度评分：弹着点与目标中心的平均距离
- 有效命中：在目标区域内的弹着点

**数据追踪**
- 弹着点散布范围
- 后坐力控制评分
- 有效命中率

## 技术架构

### 项目结构

```
src/
├── components/           # 公共组件
│   ├── NeonButton.vue    # 霓虹按钮
│   ├── GlassCard.vue     # 玻璃态卡片
│   ├── ParticleBg.vue    # 粒子背景
│   └── StatsPanel.vue    # 统计数据面板
├── views/                # 页面视图
│   ├── HomeView.vue      # 首页/训练选择
│   ├── TrainingView.vue  # 训练页面（动态组件）
│   └── ResultsView.vue   # 训练结果
├── trainings/            # 训练模式组件
│   ├── GridShot.vue
│   ├── FlickShot.vue
│   ├── ReactionShot.vue
│   ├── TrackingShot.vue
│   └── RecoilControl.vue
├── composables/          # 组合式函数
│   ├── useTraining.ts    # 训练逻辑通用封装
│   ├── useStats.ts       # 统计数据管理
│   ├── useTimer.ts       # 计时器
│   └── useLocalStorage.ts # 本地存储
├── stores/               # Pinia 状态管理
│   ├── settings.ts       # 用户设置
│   └── history.ts        # 训练历史
├── types/                # TypeScript 类型
│   └── index.ts
├── utils/                # 工具函数
│   └── helpers.ts
├── App.vue
└── main.ts
```

### 技术栈

- **框架**: Vue 3.5 + Composition API + `<script setup lang="ts">`
- **状态管理**: Pinia
- **路由**: Vue Router
- **样式**: SCSS + CSS Variables
- **动画**: CSS Animations + Vue Transition

### 核心类型定义

```typescript
// 训练模式
enum TrainingMode {
  GRID_SHOT = 'grid-shot',
  FLICK_SHOT = 'flick-shot',
  REACTION_SHOT = 'reaction-shot',
  TRACKING = 'tracking',
  RECOIL_CONTROL = 'recoil-control'
}

// 训练配置
interface TrainingConfig {
  duration: number;      // 训练时长（秒）
  targetSize: 'small' | 'medium' | 'large';
  difficulty: 'easy' | 'normal' | 'hard';
}

// 训练结果
interface TrainingResult {
  id: string;
  mode: TrainingMode;
  timestamp: number;
  config: TrainingConfig;
  score: number;
  accuracy: number;
  avgReactionTime?: number;  // 反应时间（毫秒）
  hits: number;
  misses: number;
  duration: number;          // 实际训练时长
}

// 用户设置
interface UserSettings {
  showParticles: boolean;
  defaultConfig: Record<TrainingMode, TrainingConfig>;
}
```

### 本地存储结构

```typescript
// localStorage keys
const STORAGE_KEYS = {
  SETTINGS: 'aimer-sphere:settings',
  HISTORY: 'aimer-sphere:history',
  PERSONAL_BESTS: 'aimer-sphere:personal-bests'
};
```

## 页面设计

### 首页（HomeView）

**布局**
- 顶部：Logo + 标题
- 中部：训练模式卡片网格（5个）
- 底部：个人最佳数据概览

**训练模式卡片**
- 图标 + 名称 + 简短描述
- 悬停显示霓虹光晕效果
- 点击进入配置页面或直接开始

### 训练配置页（ConfigView）

**配置选项**
- 时长选择：30s / 60s / 90s / 120s
- 目标大小：小 / 中 / 大
- 难度选择（如果适用）
- 开始按钮

### 训练页面（TrainingView）

**通用布局**
- 顶部：计时器 + 当前分数 + 暂停按钮
- 中部：训练区域（全屏或居中）
- 训练开始时 3-2-1 倒计时

**各模式特有元素**
- Grid Shot：3x3/4x4 网格区域
- Flick Shot：全屏点击区域
- Reaction Shot：等待提示 + 目标
- Tracking：移动目标 + 进度条
- Recoil Control：准星 + 弹着点显示

### 结果页面（ResultsView）

**展示内容**
- 最终得分（大数字动画）
- 准确度百分比
- 命中率
- 平均反应时间（如适用）
- 与历史最佳对比
- 重新训练按钮 + 返回首页按钮

## 动画设计

### 进入动画
- 页面切换：淡入 + 轻微上滑
- 卡片出现：依次延迟淡入（stagger）

### 交互动画
- 按钮悬停：光晕增强 + 轻微放大
- 目标出现：缩放弹出 + 发光脉冲
- 命中反馈：粒子爆散效果 + 分数弹出
- 失误反馈：屏幕轻微震动（shake）

### 背景动画
- 浮动粒子：缓慢漂移的霓虹光点
- 网格线：细微的透视网格（可选）

## 性能考虑

1. **requestAnimationFrame**：用于追踪训练的平滑动画
2. **will-change**：GPU 加速关键动画元素
3. **动态组件**：训练模式使用 Vue 动态组件按需加载
4. **粒子优化**：限制粒子数量，使用 CSS 动画而非 JS

## 响应式设计

- 桌面端：完整布局，支持高刷新率（144Hz+）
- 平板：适配触摸操作
- 移动端：简化训练模式，优化触控体验（次要支持）

## 后续扩展（可选）

1. 用户账号系统（Firebase/Supabase）
2. 全球排行榜
3. 训练数据分析图表
4. 自定义训练配置保存
5. 音效系统（击中音、倒计时音等）

## 文件变更计划

### 新建文件
- `src/App.vue` - 根组件
- `src/views/HomeView.vue` - 首页
- `src/views/TrainingView.vue` - 训练主页面
- `src/views/ResultsView.vue` - 结果页
- `src/components/NeonButton.vue` - 霓虹按钮
- `src/components/GlassCard.vue` - 玻璃卡片
- `src/components/ParticleBg.vue` - 粒子背景
- `src/components/StatsPanel.vue` - 统计面板
- `src/trainings/GridShot.vue` - 网格训练
- `src/trainings/FlickShot.vue` - 点击训练
- `src/trainings/ReactionShot.vue` - 反应训练
- `src/trainings/TrackingShot.vue` - 追踪训练
- `src/trainings/RecoilControl.vue` - 压枪训练
- `src/composables/useTraining.ts` - 训练逻辑
- `src/composables/useStats.ts` - 统计逻辑
- `src/composables/useTimer.ts` - 计时器
- `src/composables/useLocalStorage.ts` - 本地存储
- `src/stores/settings.ts` - 设置状态
- `src/stores/history.ts` - 历史记录状态
- `src/types/index.ts` - 类型定义
- `src/utils/helpers.ts` - 工具函数
- `src/styles/variables.scss` - SCSS 变量
- `src/styles/animations.scss` - 动画样式

### 修改文件
- `src/main.ts` - 改为 Vue 应用入口
- `index.html` - 添加字体链接
- `package.json` - 添加 Vue、Vue Router、Pinia 依赖
- `vite.config.ts` - 配置路径别名

### 删除文件
- `src/counter.ts` - 模板文件
- `src/style.css` - 替换为 SCSS
