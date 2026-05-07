# EAI2 Product Review - 2026-05-07 Five Iterations

Mode: `same-day iterative run`
Review focus: `AdSense trust / launch-readiness / source density`
Iteration count: `5`
Evidence grades: `repo-confirmed | rendered-confirmed | inferred`

## 0. 本轮目标

用户要求执行 product review 并进行 5 次迭代。由于当前业务问题仍是 AdSense 审核长期 `Getting ready`，本轮把 5 次迭代都限定在低风险、可验证、对审核/读者信任有帮助的改动上，而不是新增未审批变现入口。

## 1. Iteration 1 - Article trust header and category link correctness

问题：

- 文章页虽然有作者和日期，但读者无法从首屏直接进入 Editorial Policy / Corrections。
- `Tools & Frameworks` 分类 slug 由简单替换生成，存在 `/categories/tools-frameworks/` 错链风险。

改动：

- 文章 hero 增加 `Editorial policy` 和 `Corrections` 链接。
- 修复 `Tools & Frameworks` 在 article category link 和 JSON-LD breadcrumb 中的 slug。
- 如果文章 `lastmod > date`，文章元信息显示 `Updated ...`。

证据：

- `rendered-confirmed`：`open-source-humanoid-projects` 显示 `Updated May 7, 2026`。
- `rendered-confirmed`：检查页面 HTML，不再包含 `/categories/tools-frameworks/`。

## 2. Iteration 2 - Publisher schema enrichment

问题：

- 站点已有 schema，但 publisher 信息偏薄。

改动：

- TechArticle / WebSite JSON-LD publisher 增加 `email: contact@eai2.cloud`。
- 增加 `sameAs: https://github.com/lauraghcd/eai2-site`。

证据：

- `repo-confirmed`：`layouts/partials/extend_head.html` 已更新。

## 3. Iteration 3 - Expand thin high-intent article

问题：

- `open-source-humanoid-projects` 是 backlog 中明确的薄弱高意图页面，且之前没有 source box、没有外部官方链接。

改动：

- 增加 `How This List Was Selected`。
- 给 InMoov、K-Scale、LeRobot、OpenVLA、MuJoCo Menagerie、Isaac Lab 增加官方来源链接。
- 增加 `What to Verify Before You Build` 检查表。
- 增加 source box。
- 设置 `lastmod: 2026-05-07`。

证据：

- `repo-confirmed`：文章字符数约 6,189 -> 8,548。
- `repo-confirmed`：外部链接数 0 -> 7。
- `rendered-confirmed`：页面 source box 数量为 1，无横向溢出。

## 4. Iteration 4 - Source notes for high-risk pages

问题：

- 机器人市场规模、国内公司、法规、安全、框架推荐等内容属于高风险/高判断密度页面，之前没有 visible source boundary。

改动：

新增 source box 和 lastmod：

- `humanoid-robot-landscape-2026.md`
- `china-humanoid-robot-companies.md`
- `robot-safety-standards-2026.md`
- `robot-learning-frameworks-2026.md`

证据：

- `repo-confirmed`：带 source box 的文章从 3 篇增加到 8 篇。
- `rendered-confirmed`：`robot-learning-frameworks-2026` source box 数量为 1，无横向溢出。

## 5. Iteration 5 - Homepage trust strip and stale link repair

问题：

- Editorial Policy 主要在 footer，对首页首屏用户不够明显。
- 首页 Monthly Tracker 链接指向旧路径 `/posts/embodied-ai-funding-tracker/`，实际文章路径是 `/posts/embodied-ai-funding-tracker-2026/`。

改动：

- 首页新增 `home-trust-strip`：
  - Editorial policy
  - By EAI2 Editorial
  - Corrections welcome
- 修复 Monthly Tracker 链接。
- 首页 page count 从 195 调整为 196。

证据：

- `rendered-confirmed`：首页 trust strip 有 3 个入口。
- `rendered-confirmed`：移动端 trust strip 正常显示，无横向溢出。
- `rendered-confirmed`：首页 HTML 不再包含旧 funding tracker 链接。

## 6. 验证

Fresh verification:

- `hugo --minify`：通过，生成 196 pages。
- 本地 Hugo server：`127.0.0.1:1315`。
- Playwright 检查：
  - 首页无横向溢出。
  - 移动端首页无横向溢出。
  - `open-source-humanoid-projects` 有 source box、updated meta、article trust links。
  - `robot-learning-frameworks-2026` 有 source box、updated meta。
  - `ros2-for-modern-robotics-2026` article trust links 正常。
  - `/editorial-policy/` 渲染正常。
  - 不存在 `8086` 异常年份。
  - 不存在 `/categories/tools-frameworks/` 错链。
  - 不存在旧 `/posts/embodied-ai-funding-tracker/` 首页链接。

## 7. 当前产品状态

Launch verdict: `stronger limited-validation readiness`

仍不能声称 AdSense 一定会通过，因为后台仍未给出拒绝原因，审核也可能只是排队。但本轮消除了多个 repo-confirmed trust gaps：

- 更明显的编辑责任入口。
- 更正确的结构化数据。
- 更多高风险页面的 source boundary。
- 一个薄弱高意图页面被补强。
- 一个首页错链被修复。

## 8. 剩余风险

1. 仍有 10 篇文章没有 source box。
2. 多数 source notes 仍是来源边界声明，不是逐条 citation footnotes。
3. About/Author 仍是团队身份，没有更强的真实个人署名。
4. 没有 Search Console / Analytics 数据，无法证明用户行为改善。

## 9. 下一轮建议

如果 AdSense 到 2026-05-24 仍未通过，下一轮优先做：

1. 给剩余高流量候选页面补 primary source links。
2. 增加逐条 citation / reference sections，而不是只写 source notes。
3. 进一步增强 About / Author 的真实运营者信息。

## 10. Review Reflection

- Review weakness this run: 5 次迭代集中在 trust surface，仍无法观察 Google 审核内部判断。
- Corrective feedback: 每个改动都绑定可渲染证据，避免只写报告。
- Next deliberate focus: 下次如果继续卡审，重点转向内容原创性、逐条引用密度和重复语气检查。
- Behavior change next run: 不再继续增加新政策页，除非有后台拒绝原因支持。
