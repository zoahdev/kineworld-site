# kineworld.com 上线修复清单（需要你的凭据，Agent 无法直接执行）

> 诊断时间：2026-09-01。以下两步都需要你自己的账号权限（Cloudflare / 阿里云 OSS），
> Agent 没有这些凭据，只能定位问题并给出可执行命令。请在你本机或 CI 中执行。

## 现状（已核实）

| URL | 状态 | 根因 |
|---|---|---|
| `https://kineworld.com/`（根域） | **404** | Cloudflare 没有为 apex 域名配置站点/回源；只有 `www` 的 CNAME 指向 OSS 桶 |
| `https://www.kineworld.com/` | 200 | 正常 |
| `https://www.kineworld.com/index.html` | 200 | 正常 |
| `https://www.kineworld.com/kine-bench.html` | 200 | 正常 |
| `https://www.kineworld.com/vs-baize.html` | **404** | 仓库有文件，但**未同步到 OSS 桶**（OSS 直接返回 404） |
| `https://www.kineworld.com/exp002.html` | **404** | 同上，OSS 未上传 |
| `https://www.kineworld.com/data/exp002.json` | **404** | 同上，OSS 未上传 |

本地 `zoahdev/kineworld-site` 仓库里这些文件都存在（已确认），所以问题只是**没上传 / 根域没路由**，不是内容缺失。

## 修复 1：根域 404（Cloudflare）

根域 `kineworld.com` 没生效，而 `www` 正常，说明 CNAME 只配了 `www`。二选一：

**方案 A（推荐，根域直接回源 OSS）**：在 Cloudflare DNS 给 apex 加一条 CNAME `kineworld.com` → `kineworld-web.oss-cn-hongkong.aliyuncs.com`（与 www 相同目标），并开启"扁平化 CNAME / CNAME Flattening"（Free 计划默认开启）。
**方案 B（根域 301 跳 www）**：在 Cloudflare 的 Page Rules / Redirect Rules 把 `kineworld.com/*` 301 到 `https://www.kineworld.com/*`。

> 同时确认 OSS 桶的"绑定自定义域名"里包含了 `kineworld.com`（不止 `www.kineworld.com`），否则即使 DNS 到了 OSS，桶也会拒。

## 修复 2：OSS 同步缺口（vs-baize / exp002 / data）

在已配置 `ossutil` 且桶凭据可用的机器上，从仓库根目录执行（与 DEPLOY.md 一致）：

```bash
cd kineworld-site
ossutil cp -r . oss://<你的bucket>/ --exclude ".git/*" --exclude "DEPLOY.md" --exclude "SITE_REMEDIATION.md"
```

上传后用以下命令逐条核验（应全部 200）：
```bash
for p in "" "kine-bench.html" "vs-baize.html" "exp002.html" "data/exp002.json"; do
  curl -sS -o /dev/null -w "%{http_code}  /$p\n" "https://www.kineworld.com/$p"
done
```

> 建议把这条 `ossutil cp` 接进 GitHub Actions（push 到 main 即同步），避免以后再出现"仓库有、线上 404"的脱节。仓库里已有 `DEPLOY.md` 说明，CI 脚本可在此基础上加。

## 不需要改的代码

`zoahdev/kineworld-site` 仓库内容本身是对的；本次无需改 HTML，只需**上传 + 根域路由**。
