---
name: ziniao-page
description: "Use when a request concerns a webpage running inside an already-opened 紫鸟/ZClaw store browser, including navigation, inspection, interaction, extraction, or screenshots. If the task starts by identifying or opening a store, use ziniao-store first."
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 紫鸟页面能力

本技能负责已打开紫鸟店铺浏览器中的通用网页资源操作，不负责店铺生命周期，也不定义用户的业务流程。

## 负责范围

- 对当前店铺浏览器中的网页进行通用操作或读取。
- 在页面操作中复用已确认的 `store-id`，必要时指定 `target-id`。
- 根据页面状态选择 CLI 已提供的页面命令；参数以 `ziniao-cli page <command> --help` 为准。
- 页面操作需要复杂编排时，按需读取 automation reference。

## 路由边界

- 已有店铺浏览器并且目标是网页资源：直接使用本技能。
- 任务尚未确定或打开店铺：转到 `ziniao-store`，不要自行猜测店铺 ID。
- 不要把用户的业务目标或任务参数写入技能；这些是用户任务，由模型组合通用页面能力完成。
- 配置、认证、Bridge、客户端或网络异常：按需读取 `ziniao-shared` 的诊断规则，不在本技能重复维护环境修复流程。

## 最小执行规则

1. 确认店铺已打开，并取得有效 `store-id`；页面命令通常都需要它。
2. 先确认当前页面状态，再执行必要的页面操作；页面变化后不要复用失效的目标或定位信息。
3. 对导航、等待、读取、交互、提取和截图等能力，直接使用 CLI 的对应命令；不确定参数时先看 `--help`。
4. 涉及附件或图片上传时，在下载、路径转换或上传调用前，必须完整读取 [page upload 安全规则](references/ziniao-page-upload.md)；未读取不得执行上传。
5. 只在当前任务需要时读取 reference，不要默认读取全部页面文档。
6. 报错时区分页面/目标定位问题和环境问题；环境问题再转 `ziniao-shared`/`doctor`。

## 页面读取与定位顺序

1. 页面状态、`target-id` 和稳定 CSS selector 已知时，直接复用并执行必要操作；不要为每一步重复读取页面。
2. 需要理解页面或验证导航、搜索、提交结果时，优先使用 `page content --content-format structured`；只需 URL/title 时也可使用 `page exec`。
3. selector 未知时，先使用 `page content`、`page query` 或 `page exec` 分析 DOM，再使用 `page input` / `page click --selector`。
4. 只有上传控件、自定义或图标控件、多个相似控件存在歧义，或上述 DOM 方法仍无法唯一定位时，才使用 `page snapshot` 和当前页面 ref。

`page snapshot` 不是通用页面状态检查命令。不得为了验证店铺窗口、读取 URL/title/`target-id`、操作已知 selector、普通搜索或结果验证而调用 snapshot；页面未变化时也不得重复创建 snapshot/ref。snapshot ref 只属于当前店铺、tab 和页面，页面导航或刷新后不得复用。

输入后提交只能选择一种方式：使用 `page input --submit` 后等待并验证结果，或不传 `--submit` 再执行提交按钮的 `page click --wait-nav`；不得重复提交。

## 按需参考

- [page visit](references/ziniao-page-visit.md)：需要导航时读取。
- [page screenshot](references/ziniao-page-screenshot.md)：需要截图时读取。
- [page upload 安全规则](references/ziniao-page-upload.md)：涉及附件、图片、文件输入框或 `page upload` 时必须读取。
- [automation run](references/ziniao-page-automation.md)：需要多步骤自动化时读取。
- `ziniao-store`：需要确定或打开店铺时使用。
- `ziniao-shared`：仅在环境、配置、认证或 Bridge 诊断时读取。
