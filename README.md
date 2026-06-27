# gogogotoken-doc-cn

本仓库是 **gogogotoken.com 国内站** 的 Mintlify 文档站点，面向中国大陆用户与 `.com` API 域名。

## 与 gogogotoken-doc 的关系

| 仓库 | 站点 | 说明 |
| --- | --- | --- |
| [gogogotoken-doc](https://github.com/...) | gogogotoken.ai（新加坡） | 海外站完整文档：大模型、工具配置、工作流等 |
| **gogogotoken-doc-cn**（本仓库） | gogogotoken.com（国内） | Seedance / Seedream 图像与视频、素材、兼容接口与计费 |

两套文档 **独立部署**，内容源可从 `gogogotoken-doc/domestic/gogogotokenzh/` 同步到本仓库根目录的 `*.mdx`。

## 本地预览

```bash
npx mint dev
```

站点配置见根目录 `docs.json`。

## 维护说明

- 修改文档：编辑根目录下对应 `.mdx` 文件
- 导航与主题：编辑 `docs.json` 中的 `navigation.groups` 与品牌字段
- API 示例 Base URL 统一为 `https://gogogotoken.com`
