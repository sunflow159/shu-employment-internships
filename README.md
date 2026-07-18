# SHU Employment Internship Opportunities

> Bilingual README: 中文说明在前，English version follows.

## 项目简介

本项目整理“上大就业”公众号近期文章中的实习与见习信息，并筛选出较适合金融、应用统计、数据分析、金融科技等方向学生关注的岗位。

仓库包含：

- `data.csv`：由爬虫脚本抓取的公众号文章链接清单。
- `金融与应用统计实习岗位汇总.md`：从文章正文和图片海报中整理出的岗位汇总。

## 数据字段

`data.csv` 主要字段包括：

| 字段 | 含义 |
|---|---|
| `title` | 公众号文章标题 |
| `link` | 原始公众号文章链接 |
| `create_time` | 文章发布时间 |

## 最新状态

- 当前本地 `data.csv` 共 65 篇文章，最新一篇发布时间为 `2026-07-03 11:37`。
- 2026-07-18 再次执行默认增量抓取时，微信接口仍明确返回 `base_resp.ret=200003` / `invalid session`，说明当前 `crawl_html.py` 内的 `Cookie`/`token` 仍已失效；旧的 `data.csv` 和 `content_list.json` 已保留，未被覆盖。
- 已于 2026-07-18 基于本地缓存重新执行 `extract_article_text.py --ocr-mode none` 与既有候选海报文章的定向 OCR；`article_extracts/articles_text.json` 维持 65 篇正文，当前有 8 篇文章保留 OCR 文本。
- 2026-07-01 至 2026-07-03 的 9 篇最新文章中，未发现新增明确属于金融、应用统计、数据分析或金融科技方向的重点实习岗位。

## 技术路线

1. **文章链接抓取**
   - 使用微信公众号后台接口获取“上大就业”公众号文章列表。
   - 输出文章标题、链接和发布时间到 `data.csv`。
   - 爬虫支持增量更新：再次运行时从最新文章开始抓取，遇到已存在链接后停止，适合每隔几天定期更新。

2. **网页正文提取**
   - 读取 `data.csv` 中的文章链接。
   - 使用登录态 Cookie 和请求头访问微信公众号文章页面。
   - 解析 HTML 中的标题、正文、图片地址和外链。

3. **图片海报 OCR**
   - 对正文较少、主要信息位于图片海报中的文章进行定向 OCR。
   - 使用 Tesseract OCR，语言配置为 `chi_sim+eng`，识别中文和英文招聘信息。
   - 对二维码尝试使用 OpenCV 解码，能解出的投递入口会写入汇总文档。

4. **岗位筛选**
   - 依据关键词和人工复核筛选金融、证券、银行、保险/社保、投研、量化、精算、数据分析、数据产品、AI+金融等方向相关岗位。
   - 将岗位名称、公司名称、岗位描述、投递方式、截止时间和原始网页链接整理为 Markdown 表格。

5. **结果输出**
   - 输出 `金融与应用统计实习岗位汇总.md`。
   - 对无法从图片或二维码中完整识别的信息，在文档中明确标注“原文未识别”或“需打开原文人工核对”。

## 使用方式

如果需要更新文章链接：

```bash
python crawl_html.py
```

若返回 `invalid session`，需要先在 `crawl_html.py` 中刷新微信 `Cookie`/`token` 后再重试；在接口失效时请保留旧数据，不要覆盖本地结果。

如果需要全量重爬：

```bash
python crawl_html.py --mode full
```

如果需要重新提取正文和 OCR：

```bash
python extract_article_text.py --ocr-mode none
python extract_article_text.py --ocr-mode all --only-indexes 18,32,38,41,49,55,58,61
```

## 注意事项

- 微信公众号文章可能包含验证码、登录态限制或二维码投递入口，因此部分信息无法完全自动化提取。
- OCR 结果可能存在识别误差，正式投递前请以企业官网、招聘公众号或原始文章为准。
- 截止时间来自原文识别；如果文章较旧，部分机会可能已经过期。

---

# English Version

## Overview

This repository collects recent article links from the WeChat public account “上大就业” and summarizes internship opportunities that are especially relevant to students in finance, applied statistics, data analytics, and fintech.

Repository contents:

- `data.csv`: a list of crawled WeChat article links.
- `金融与应用统计实习岗位汇总.md`: a curated Markdown summary of relevant internship opportunities.

## Data Fields

Main columns in `data.csv`:

| Field | Description |
|---|---|
| `title` | WeChat article title |
| `link` | Original article URL |
| `create_time` | Article publication time |

## Technical Workflow

1. **Article Link Crawling**
   - The crawler calls the WeChat public account backend API to retrieve article metadata.
   - It exports article titles, URLs, and publication times into `data.csv`.
   - Incremental updates are supported: the crawler starts from the newest articles and stops once it reaches a link that already exists locally.

2. **HTML Content Extraction**
   - The pipeline reads article URLs from `data.csv`.
   - It accesses WeChat article pages with authenticated cookies and request headers.
   - It extracts titles, plain text, image URLs, and outbound links from the HTML.

3. **OCR for Image-Based Posters**
   - Some recruitment posts are image-only posters, so selected pages are processed with OCR.
   - Tesseract OCR is used with `chi_sim+eng` to recognize Chinese and English recruitment text.
   - OpenCV is used to decode QR codes when possible; decoded application links are included in the final summary.

4. **Opportunity Filtering**
   - The extracted text is filtered and manually reviewed for roles related to finance, securities, banking, insurance/social security, investment research, quantitative finance, actuarial analysis, data analytics, data products, and AI+finance.
   - The final Markdown table includes role name, company, job description, application method, deadline, and original article link.

5. **Output**
   - The final output is `金融与应用统计实习岗位汇总.md`.
   - Missing or partially recognized information is explicitly marked for manual verification.

## Usage

Incrementally update article links:

```bash
python crawl_html.py
```

Run a full crawl:

```bash
python crawl_html.py --mode full
```

Re-extract article text and run targeted OCR:

```bash
python extract_article_text.py --ocr-mode none
python extract_article_text.py --ocr-mode all --only-indexes 18,32,38,41,49,55,58,61
```

Latest local status:

- `data.csv` currently contains 65 articles; the newest local article is dated `2026-07-03 11:37`.
- The incremental run on 2026-07-18 again reached the WeChat API and got `base_resp.ret=200003` / `invalid session`, which confirms the `Cookie`/`token` in `crawl_html.py` is still expired; the existing local files were kept unchanged.
- On 2026-07-18, `extract_article_text.py --ocr-mode none` and the targeted OCR pass were rerun from the 65 cached HTML files; `article_extracts/articles_text.json` still contains 65 extracted articles, and 8 articles currently retain OCR text.
- Among the 9 most recent articles dated 2026-07-01 to 2026-07-03, no new clearly relevant finance/applied statistics/data internships were identified.

## Notes

- WeChat articles may require valid cookies or present captcha pages, and many application links are embedded in QR codes.
- OCR may introduce recognition errors. Always verify details on the original article or official employer recruitment page before applying.
- Deadlines are extracted from the original posts; older opportunities may have expired.
