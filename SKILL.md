---
name: read-paper
description: 把一篇论文读成一份 HTML 精读笔记，默认四节 motivation / literature review / 模型 / 算例与结果（原文没有的写明并交代替代内容），其余章节按论文定；公式给直觉、符号首次出现即释义、决策变量与参数用底色区分。仅在用户显式调用 /read-paper 时生效，不要自动触发。
version: 1.3.0
---

# read-paper — 论文精读笔记生成器

产出**一个自包含的 HTML 文件**：读者不看原文也能弄清这篇在解决什么问题、模型长什么样、
每条公式在说什么事、结论有多硬。

**排版、配色、组件**已经定稿，不要即兴发挥；**章节结构不是** —— 见第 3 步。

## 调用

仅在用户显式输入 `/read-paper` 时生效。

```
/read-paper <PDF 路径> [配色代号] [输出路径]
```

配色代号见 `references/palettes.md`，缺省 `deep-blue`（深海蓝）。

## 前置依赖

第 1 步取全文要用 **PyMuPDF**：`pip install pymupdf`（导入名是 `fitz`，不是 `pymupdf`）。
自检脚本只用标准库，无额外依赖。

---

## 执行流程

### 1. 取全文

`Read` 工具打不开加密 PDF 时（很常见，出版商加了权限锁），用 PyMuPDF 空密码打开：

```python
import fitz
d = fitz.open(path)
if d.needs_pass: d.authenticate('')
text = '\n'.join('===== PAGE %d =====\n' % (i+1) + p.get_text() for i, p in enumerate(d))
open(txt_path, 'w', encoding='utf-8').write(text)
print(len(d), 'pages,', len(text), 'chars ->', txt_path)
```

`txt_path` 放输出 HTML 的同目录或系统临时目录，**不要写进 PDF 所在目录** ——
存放论文原文的目录常被设成只读（不少论文项目会把「原始文献」目录锁掉，防止误改）。
这个 txt 第 5 步还要用来核对式号，**自检跑完之前不要删**。

**预期**：打印出形如 `18 pages, 92341 chars -> ....txt`，字符数通常 5–15 万，且该 txt 文件已生成。
若字符数为 0，说明是扫描件、只有图像层，此时**必须停下来告诉用户**，不要靠标题和摘要硬编内容。

提取出的文本层常有损伤：减号、除号、上下划线、根号容易丢失或变形
（`a−b` 变 `a  b`、`x/y` 变 `x=y`）。**公式按上下文重建后，要在交付说明里写清这一点。**

### 2. 通读并判断

读完全文再动笔。写作时**必须区分「原文说的」和「我推断的」**：
凡是原文没有明写、由你推出来的判断，一律标注
`<span class="conf">置信度 high/medium/low；推断</span>`。
**这条是本 skill 的底线，比排版重要。**

### 3. 按内容契约写

见 `references/content.md`。**默认四节**：motivation / literature review / model / 算例与结果。
默认不等于无条件必须，原文没有的那节怎么处理、其余章节加不加，判据都在 content.md 里。

### 4. 套模板产出 HTML

复制 `assets/template.html`，替换 `<body>` 内容 —— 模板 body 里每个组件都有可照抄的骨架。
若指定了非默认配色，按 `references/palettes.md` 整块替换 `:root`。

规则见 `references/rendering.md`。

### 5. 交付前自检（必做）

跑 `references/rendering.md` 里的自检脚本：标签配平、CSS 配平、无等宽字体残留、样式自包含、
折叠块一律带 `open`、每个折叠块有 `summary`、释义块不是旧的 `div.gloss`。
**七条断言任何一条不过就不要交付。**

再跑同一文件里的**式号回原文核对**（拿第 1 步的 txt 查 `.eqn`，抓凭空造号）。
它打印清单、不阻断交付，但列出来的号**必须逐个交代**，不许沉默放过。

改过色值的话，另跑对比度检查（判据同文件）。

---

## 不可协商的几条

1. 公式里**每个量都要打标记**：`var.dv` / `var.rp` / `var.df` / `var.do` / `var.pm`
   —— **徽章是什么，`<var>` 的类就是什么**，只有「集」不包 `<var>`。
   （**非优化类论文的徽章退化规则**见 rendering.md。）
2. **每条实质性公式配一段直觉**（`.intu`），讲物理/经济含义与建模后果，不是复述公式。
   纯代数过渡式怎么处理见 rendering.md。
3. **符号释义在公式之前**（`.gloss`），标出身份徽章；单位与算例取值**原文给了就写，没给就留空**，不要脑补。
   释义块写成 `<details class="gloss" open>` + `<summary>`。
   **查阅型长表套 `.fold` 折叠壳**，哪些该套、哪些不套见 rendering.md。
   两种折叠块交付时**`open` 都不许省**（理由见 rendering.md）。
4. **流程图必须是 HTML 盒子**（`.dgm`），不用 ASCII 画。
5. **式号用 `.eqn` 右对齐**，不靠数空格；**编号照抄原文，不得自己重排**（跳过的式子留空洞是对的）。
6. **表格空单元格填 `—`**；含 `rowspan` 的表加 `class="pairs"`。
7. **区块字体一律继承正文**，字号统一 `--blk`，区块内标签不得比正文小。

每条的理由与写法见 `references/rendering.md`。
