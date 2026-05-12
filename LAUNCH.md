# Launch Checklist

Use this checklist to make `lanhu-to-code` easier to find, understand, and star.

## GitHub Repository Settings

Set the repository description to:

```text
Lanhu to Code for Claude Code: AI design-to-code skills that turn Lanhu designs into Vue/React frontend code with component reuse and design token mapping.
```

Add these topics:

```text
claude-code
claude-code-skills
lanhu
lanhu-to-code
design-to-code
ai-coding
frontend
vue
react
design-tokens
component-reuse
```

Enable:

- Issues
- Discussions, if you want workflow questions and showcase posts
- Releases, once the install flow is stable

## First Issues To Open

Create these issues so visitors see an active roadmap:

1. `Add 60-second Lanhu-to-code demo GIF`
2. `Add real Vue example generated from Lanhu design`
3. `Add real React example generated from Lanhu design`
4. `Support Claude Code plugin marketplace installation`
5. `Add Figma adapter notes for mixed Figma/Lanhu teams`

## Demo GIF Script

Record a short GIF or video with this flow:

1. Open a frontend project in Claude Code.
2. Run `/design-to-code`.
3. Paste a Lanhu page URL.
4. Show Lanhu MCP extraction completing.
5. Show generated `src/views/order/index.vue`.
6. Show reused components and TODO items.
7. Show optional review prompt.

Keep it under 60 seconds. Put the final file at:

```text
assets/demo.gif
```

Then replace the README demo placeholder with:

```md
![Lanhu to Code demo](./assets/demo.gif)
```

## Launch Post

English:

```text
I built a Claude Code skill that turns Lanhu design links into Vue/React frontend code.

It reads Lanhu through MCP, reuses existing project components, maps raw design values to design tokens, marks uncertain APIs/enums as TODO, and can run a review pass after generation.

GitHub:
https://github.com/ursazoo/lanhu-to-code
```

Chinese:

```text
我做了一个 Claude Code Skill：粘贴蓝湖链接，自动生成前端页面代码。

它会通过蓝湖 MCP 读取设计稿，优先复用项目已有组件，把颜色和间距映射成项目 design token，不确定的 API / 枚举 / 业务逻辑会标 TODO，生成后还能接 code review。

GitHub:
https://github.com/ursazoo/lanhu-to-code
```

## Places To Submit

- GitHub awesome lists for Claude Code skills
- Claude Code skill/plugin discovery sites
- V2EX `分享创造`
- Juejin frontend and AI coding topics
- X / Twitter with `Claude Code`, `design-to-code`, and `frontend`
- Reddit communities such as `r/ClaudeAI`, `r/webdev`, and `r/Frontend`
- Chinese frontend and AI coding WeChat groups

## Search Phrases To Reuse

Use these naturally in README, issues, posts, and release notes:

- Lanhu to Code
- Lanhu design to code
- Claude Code skills
- Claude Code design to code
- AI frontend code generator
- Vue design to code
- React design to code
- Figma to code alternative
- design token mapping
- component reuse
- 蓝湖转代码
- 蓝湖设计稿生成前端代码
- Claude Code 前端工作流
