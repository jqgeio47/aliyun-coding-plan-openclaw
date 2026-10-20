# 阿里云Coding Plan 是否支持 OpenClaw：官方明确支持，Pro 套餐价格、请求上限、Token Plan 新接入与配置避坑一次讲清

支持。阿里云Coding Plan 的官方套餐页把 OpenClaw 列在支持客户端名单里，和 Qwen Code、Claude Code、Codex、Cline、Cursor 并排；阿里云帮助中心还有专门的 OpenClaw 接入文档，2026 年 10 月仍在更新。这个问题本身没什么悬念，真正值得花时间的是后面几件事：四种接入方式怎么选、Pro 的请求额度怎么算、配置时哪两个地方最容易出错。

> **选购速览**
> - 支持，官方支持客户端列表可直接确认，不用自己摸索兼容性。
> - 新用户目前只有一个订阅档：**Coding Plan Pro，$50/月**，含 90,000 次请求；每月 API 花费到不了 $50 的轻度用户，按量付费更便宜。
> - 配置前先记住两点：Coding Plan 只认 **sk-sp-** 开头的专属 Key；模型配置里 **reasoning 必须设为 false**。
> - 👉 [查看阿里云Coding Plan 当前套餐与价格](https://bit.ly/aliyun-coupon)

## 结论：官方支持，套餐页和帮助文档都能对上

这个问题可以给得很确定：官方材料里写得明明白白。AI Coding Plan 的套餐卡在“支持的客户端”一栏明确列出 OpenClaw，同一行的还有 Qwen Code、OpenCode、Claude Code、Codex、Cline、Cursor 和 Kilo Code。帮助中心的 OpenClaw 文档则把接入方式分成四种：按量付费、Coding Plan、Token Plan 个人版、Token Plan 团队版。

动手前有一个硬性前提：OpenClaw 需要 Node.js 22.19.0 或更高版本。装好后会自动进入配置向导，模型部分选“稍后配置”，回头手动改配置文件更稳。接下来先解决“选哪种接入”，再处理“怎么配”。

## 四种接入方式和套餐对比

一句话先给答案：稳定重度使用选 Coding Plan Pro，轻度使用留在按量付费，工具链以 Anthropic 格式为主的可以对比 Token Plan 个人版。目前官方文档列出的全部方案如下：

| 套餐 | 价格 | 核心配置 | 适合谁 | 购买入口 |
| --- | --- | --- | --- | --- |
| Coding Plan Pro | $50/月 | 每月 90,000 次请求；5 小时窗口 6,000 次、每周滚动 45,000 次；Qwen 及 Kimi、GLM、MiniMax 等多模型 | 重度 agent、日常主力编码 | [ 开通 Coding Plan Pro](https://bit.ly/aliyun-coupon) |
| Coding Plan Lite | $3/月（已停售） | 原 5M output tokens/月；2026 年 3 月 20 日起不再接受新订阅 | 仅限老用户续费 | [ 老用户续费/管理入口](https://bailian.console.aliyun.com/cn-beijing/subscription/overview?userCode=5eqf8rny) |
| Token Plan 个人版 | 以下单页显示为准 | Anthropic 兼容端点；qwen3.8-max、qwen3.7-plus、qwen3.6-flash、glm-5.2、deepseek-v4 系列等模型 | 工具链走 Anthropic 格式 | [ 前往 Token Plan 订阅页](https://bailian.console.aliyun.com/cn-beijing/subscription/overview?userCode=5eqf8rny) |
| Token Plan 团队版 | 以下单页显示为准 | 团队共享的订阅额度，官方文档列出的第四种接入方式 | 团队协作场景 | [ 查看全部套餐](https://bit.ly/aliyun-coupon) |
| 按量付费 | 按 token 计费 | 无套餐额度约束；重度使用月账单可达 $100–420+ | 轻度、不定期使用 | [ 查看按量付费与优惠](https://bit.ly/aliyun-coupon) |

价格以购买页当前显示为准，Pro 的 $50/月是官方公布的包月定价。

顺带交代 Lite 的下落。这个 $3/月的档位在 2026 年 3 月 20 日停售，诱因之一是多开账号池化额度的滥用，GitHub 和 OpenClaw 的 Discord 社区当时都有这类操作被公开讨论。老用户可以按原价继续续费，但 token 余额不结转；新用户不必再惦记这个选项，能订的就 Pro 一档。

## Pro 的 90,000 次请求是怎么限的

Pro 的额度按请求次数计算，token 用量不直接计入，$50/月对应每月 90,000 次请求。真正影响日常体验的是两层滚动限制：每 5 小时最多 6,000 次，每周滚动 45,000 次，月度额度在订阅日重置。

滑动窗口的设计意味着用量不能堆着来——周一一口气烧掉几千次，窗口内再来的请求就会被限速，哪怕周配额还很宽裕。单人交互式编码很难碰到 5 小时 6,000 次的天花板，容易撞墙的是自动化场景：多项目同时跑 agent、超长多轮对话、频繁重新生成，这类操作每一次背后都是一串请求。

Reddit 的 OpenClaw 社区里，Pro 搭配一台每月 $5 左右的便宜 VPS（Hetzner 或 Oracle 免费层）是常见做法，总开销能控制在 $60 以内。👉 [查看 Pro 套餐当前价格与额度说明](https://bit.ly/aliyun-coupon)

## 什么情况下按量付费比 Pro 更划算

每月 API 开销到不了 $50，就别订 Pro。按量付费口径下，轻度用户月账单大致在 $10–40，重度才会上到 $100–420+。有第三方测评按 token 用量算过账：500 万 output tokens 按量付约 $10，800 万约 $16，2,500 万左右才和 Pro 打平。多数从其他方案迁移过来的用户，用量远低于这条线。

两个附带提醒。其一，订阅内的模型清单官方可以调整，之前就有用户因为依赖的模型变动而受影响，把工作流押在某个具体模型上要留退路。其二，这一档模型在复杂推理上和第一梯队还有可见差距：Pro 档位包含的官方旗舰编程模型 Qwen-Coder-Max，早期跑分为 SWE-bench Verified 58.2%、HumanEval 92.1%。常规代码生成、补全和重构够用，架构决策和疑难 debug 该换更强的模型就换。

## OpenClaw 接入 Coding Plan：两个最容易配错的地方

流程本身几分钟就能走完，报错几乎都集中在 Key 类型和 reasoning 参数上。完整步骤：

1. 在百炼控制台的 Coding Plan 页面创建专属 API Key，格式为 sk-sp- 开头。普通百炼 Key 用在 Coding Plan 端点上无效。
2. Base URL 按账号区域二选一：国际站用 `https://coding-intl.dashscope.aliyuncs.com/v1`（服务位于新加坡），北京区域用 `https://coding.dashscope.aliyuncs.com/v1`。结尾不加斜杠，也不要自己补 `/chat/completions` 之类的路径。
3. 编辑 `~/.openclaw/openclaw.json`，新增一个 openai-compatible 类型的 provider：

json
{
  "providers": {
    "alibaba-coding": {
      "type": "openai-compatible",
      "baseUrl": "https://coding-intl.dashscope.aliyuncs.com/v1",
      "apiKey": "sk-sp-你的专属Key",
      "models": {
        "qwen3-coder-plus": { "reasoning": false, "maxTokens": 8192, "contextWindow": 131072 },
        "qwen3.5-plus": { "reasoning": false, "maxTokens": 8192, "contextWindow": 131072 }
      }
    }
  }
}


4. 每个模型都显式写上 `"reasoning": false`。
5. 保存后执行 `openclaw restart` 重启网关，在启动日志里确认新 provider 加载成功。模型名以你控制台里 Coding Plan 的实际列表为准，这份清单官方会调整。

两个坑分开说。第一个是 reasoning 参数：Coding Plan 端点不支持它，配置里如果是 true（一些默认模板就是 true），请求会返回 400，或者连接正常却拿回空响应。日志显示连接成功、内容是空的，先查这里。

第二个是网关鉴权：官方文档给的示例配置把鉴权关掉了（`auth.mode: none`），只适合本机单人使用。打算部署到 VPS 上远程访问或共享给队友，先跑 `openclaw doctor --fix` 打开 token 鉴权，别把没有任何验证的 agent 网关直接挂在公网上。

## Token Plan 是什么，和 Coding Plan 差在哪

Token Plan 是官方文档里与 Coding Plan 并列的另外两种订阅接入，分个人版和团队版，端点、Key 和模型清单都不同。它走 Anthropic 兼容端点，Base URL 形如 `token-plan.cn-beijing.maas.aliyuncs.com/apps/anthropic`（国际站为 ap-southeast-1），API 类型是 anthropic-messages；模型方面以 qwen3.8-max、qwen3.7-plus、qwen3.6-flash、glm-5.2、deepseek-v4 系列为主。

两者分工可以这样概括：Coding Plan 是 OpenAI 兼容端点加按请求计数的包月额度，适合 OpenClaw 和各类编码工具的常规接法；Token Plan 吃 Anthropic 格式，配置里已经堆满这类 provider 的话，接入会省事不少。单看用量形态，就看你需要“请求包月”还是“token 额度”。👉 [查看 Token Plan 个人版订阅入口](https://bailian.console.aliyun.com/cn-beijing/subscription/overview?userCode=5eqf8rny)

## 常见问题

**国内账号和国际账号怎么区分端点？**
登录控制台看域名，带 intl 的是国际账号，对应 coding-intl 端点。国际端点服务位于新加坡，数据存储保持在区域内，推理算力在全球节点调度（不含中国大陆）。

**配置完返回 400 或空响应怎么办？**
先检查 reasoning 是否为 false，再核对 Key 是不是 sk-sp- 开头、Base URL 有没有多余斜杠或后缀路径。这三处覆盖了绝大多数连接问题。

**$3 的 Lite 还能买吗？**
不能。2026 年 3 月 20 日起停止新订阅，老用户可续费、余额不结转，新用户只有 Pro 一档可选。

**OpenClaw 本身要花钱吗？**
OpenClaw 是开源项目，免费；费用来自模型接入，也就是 Coding Plan、Token Plan 订阅或按量付费的部分。

## 按你的用量对号入座

- 长期月账单超过 $50，或者每天多项目跑 agent：订 Coding Plan Pro，包月单价明显更低；
- 每月几百万 output tokens 以内的轻度使用：留在按量付费，订 Pro 反而多花钱；
- 配置里已经大量使用 Anthropic 格式：先对一遍 Token Plan 个人版的模型清单再决定。

下单前最后确认三件事：账号区域（决定用哪个 Base URL）、Node.js 版本（OpenClaw 要求 22.19.0+）、是否需要远程访问（决定要不要跑 doctor --fix 开鉴权）。都确认完，就可以 👉 [前往阿里云查看 Coding Plan 套餐并开通](https://bit.ly/aliyun-coupon) 了。
