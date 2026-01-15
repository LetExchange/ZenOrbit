Zen-Orbit - PRD文档
1. 项目定义
项目名称: Zen-Orbit
应用类型: 静态Web应用 (纯HTML/CSS/JS)
核心目标: 为高压职场人士提供60秒快速呼吸冥想体验，实现情绪重置
2. 核心功能规格
2.1 智能呼吸引导系统
呼吸算法: 4-6呼吸法
吸气阶段: 4秒 (圆环扩张)
呼气阶段: 6秒 (圆环收缩)
单次完整周期: 10秒
60秒总时长: 6个完整周期
视觉状态机:
idle: 初始状态，以圆心为中心的圆环轻微扩张/收缩脉动，代表正常呼吸状态，文案"click to begin"
inhaling: 圆环从20%→45%尺寸扩张，线条同时变粗，文案"Inhale"
holding: 吸气顶部停留0.5秒
exhaling: 圆环从45%→20%尺寸收缩，线条同时变细，文案"Exhale"
complete: 圆环收缩为实心小圆点，显示"mind cleared."
2.2 计时器系统
不使用数字倒计时
外圈虚线轨道圆环，直径>主圆环50px
轨道颜色: rgba(255,255,255,0.1) (暗色模式)
6px大小的轨道点，60秒完整绕行一圈
轨道动画: transform: rotate(360deg)，时长60秒，linear
3. UI/UX技术规范
3.1 画布布局
背景色:
暗色模式: #0A0A0B (午夜深蓝)
亮色模式: #FEFEFE (纸质白)
安全区: 所有元素严格居中，四周保留15%负空间
字体: Inter/Jost, font-weight: 300, letter-spacing: 0.1em
文案样式: 全小写，opacity: 0.2→0.8，带blur过渡
3.2 核心圆环元素
css
/* SVG参数 */
<circle id="breath-circle"
  cx="50%" cy="50%"
  r="自适应屏幕短边"
  stroke-width="1px"
  stroke="石墨灰: #333333 / 月光白: #E5E5E5"
  fill="none"
  filter="drop-shadow(0 0 10px rgba(255,255,255,0.3))"
/>
尺寸规则:
呼气结束: r = min(vw, vh) * 0.2
吸气结束: r = min(vw, vh) * 0.45
动画曲线: cubic-bezier(0.4, 0, 0.2, 1) (ease-in-out)
3.3 隐藏式UI系统
触发条件: mousemove或touchstart事件后1.5秒显示
布局位置:
左上角: zen orbit (项目名，opacity: 0.2)
右上角:
主题切换: 🌓图标 (切换Light/Dark)
全屏: ⛶图标 (双击屏幕也触发)
右下角:
☕图标 (Buy Me a Coffee链接)
?图标 (隐私说明)
底部中间: AdSense广告位 (仅非全屏模式)
显示动画:
css
transition: opacity 0.3s ease;
opacity: 0; /* 默认 */
opacity: 0.2; /* 激活 */
4. 交互逻辑
4.1 事件流
首次加载: 呼吸圆环动画 + "click to begin"
点击启动:
隐藏初始文案
启动60秒倒计时器
开始呼吸循环动画
轨道点开始旋转，初始位置在12点方向
运行中:
圆环每10秒完成1次呼吸周期
文案同步淡入淡出
完成状态:
轨道点回到起点
圆环收缩为实心点，实心点大小2px
显示"mind cleared." + 随机赋能文案
显示"reset"按钮
4.2 错误处理
页面失去焦点: 暂停动画，恢复后自动继续
窗口大小改变: 重新计算圆环尺寸
全屏切换: 保持动画状态不变
5. 技术实现细节
5.1 状态管理
JavaScript
const appState = {
  phase: 'idle', // idle | running | complete
  elapsed: 0, // 已进行毫秒数
  total: 60000, // 60秒总时长
  isDarkMode: true,
  isFullscreen: false
};
5.2 动画控制
JavaScript
// 呼吸周期控制器
function startBreathingCycle() {
  const cycleDuration = 10000; // 10秒周期
  const inhaleDuration = 4000;
  const exhaleDuration = 6000;
  
  // CSS动画关键帧生成
  setKeyframeAnimation('inhale', inhaleDuration, scale(0.2) => scale(0.45));
  setKeyframeAnimation('exhale', exhaleDuration, scale(0.45) => scale(0.2));
}
5.3 PWA配置
必须包含 manifest.json
支持离线缓存 (Service Worker)
安装到桌面图标: 512x512 PNG
启动URL: /
5.4 响应式规则
圆环尺寸基于 min(vw, vh) 动态计算
移动端触摸优化: 增加touch事件监听
字体大小: 基于clamp()函数响应式缩放
6. 变现集成
6.1 广告位
原生硬广: 结果页底部，文字链形式
示例: Partner: Ergonomic Chair by XYZ
AdSense: 底部横幅，970x90 (桌面) / 320x50 (移动)
显示逻辑: appState.isFullscreen === false 且 phase === 'complete'
6.2 打赏系统
Buy Me a Coffee: 右下角图标链接
URL: https://buymeacoffee.com/xyz
打开方式: _blank新标签页
7. 性能要求
首屏加载: < 1.5秒 (静态资源)
动画帧率: 保持60fps，避免掉帧
内存使用: < 50MB
无第三方库: 纯原生实现
8. 隐私与合规
底部声明: "100% Private. No data leaves your browser."
不集成任何分析工具
不使用Cookie/LocaleStorage
HTTPS部署
9. 文案库
9.1 结果页随机文案 (每次随机选1)
JavaScript
const completionMessages = [
  "mind cleared.",
  "ready for what's next.",
  "you are back.",
  "one task at a time.",
  "deep breaths, clear mind.",
  "you've got this."
];
9.2 微交互提示
点击干扰时: "enjoy the stillness."
10. 开发优先级
P0 (核心功能):
圆环呼吸动画
60秒计时器
明暗主题切换
全屏模式
P1 (体验优化):
隐藏式UI
响应式设计
文案动效
P2 (变现):
AdSense集成
Buy Me a Coffee链接
交付物: 单HTML文件 + CSS内联 + JS内联 (完全静态)