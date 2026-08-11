# 四套配色

每份输出只用一套，**不做暗色模式、不做切换器** —— 一个文件只承诺一种观感。
模板 `assets/template.html` 默认已内置 `deep-blue`；要换配色，把下面对应的整个 `:root{...}` 块
替换掉模板 `<style>` 里的 `:root{...}` 块即可（两者结构完全一致，整块替换，不要逐行改）。

| 代号 | 名称 | 说明 |
|---|---|---|
| `teal-apricot` | **青绿杏** | 冷青主色 + 暖杏次色。清爽、偏工程感，冷暖对比清楚，长时间读不累。 |
| `deep-blue` | **深海蓝**（默认） | Nature 主色板 blue_main #0F4D92 做主角、赭棕做次色。最正式，接近 Nature 正刊图注气质。 |
| `rose-lilac` | **玫瑰霜** | Nature NMI 柔彩：玫瑰族做决策变量（主角）、钢蓝做不确定参数。最柔和，适合内容密集的长稿。 |
| `crimson-blue` | **朱蓝** | Nature genomics 口径：朱红 + 靛蓝双高亮，中性骨架。对比最强，适合需要快速扫读的场合。 |

全部四套均已通过 WCAG AA 校验（每套 35 项 ≥ 4.5:1，含 `--fill-df` / `--fill-do` 两支新笔的 6 项，
实测最低 9.5:1）。**改色值必须重跑对比度检查**，判据与配对清单见 `references/rendering.md`。

## 配色的五条硬约束

新配一套、或改动现成的任何一个色值，这五条都要重新验一遍。全部用 CIELAB ΔE 量，不要目测。

**① 徽章与公式底色成对。** 同一个符号在释义表里是徽章、在公式里是底色，两处色相必须同族 ——
读者默认这两处指的是同一件事。

| 对 | 徽章 | 公式底色 |
|---|---|---|
| 决策变量 | `--k-d` | `--fill-dv` |
| 不确定参数 | `--k-r` | `--fill-rp` |
| 派生中间量 | `--k-f` | `--fill-df` |
| 对偶变量 | `--k-o` | `--fill-do` |
| 已知参数 | `--k-p`（中性灰） | 无底色，`pm` 渲染成 `--muted` 灰字 |

`--k-p` 定成中性灰（饱和度 ≤ 0.10）是有意的：它对应公式里不涂底的 `pm`，
同时空出一个色相位给其他徽章，六个徽章才排得开。

`--fill-df` / `--fill-do` 直接取 `--k-f` / `--k-o` 的主色，alpha 按⑤反推，不另选色相 ——
一一对应就是这条规则的全部意义。

**② 六个徽章两两 ΔE ≥ 32。** 这是本 skill 最容易塌的一条 —— 徽章是并排出现的小色块，
撞色就等于没分类。四套现成配色都卡在 34 上下。

**③ `--k-d` 与 `--k-r` 色相至少差 90°。** 它俩是两支最主要的荧光笔，色相太近则 ④ 无解。
（`--k-f` / `--k-o` 也各驱动一支，但它们要先满足②的徽章间距，色相位不由这条定。）

**④ 四种高亮底两两 ΔE ≥ 7，且明度相当（|ΔL\*| ≤ 6）。**
明度那半条是防作弊的：只要允许一深一浅，优化器就会靠明暗差拉开 ΔE，
结果荧光笔一强一弱，读者会误以为其中一类更重要。分离必须来自色相。

阈值原本是 12，那时只有 `dv` / `rp` 两支笔。加上 `df` / `do` 之后 12 在四套配色里全部无解 ——
稀释到 L\*≈82 后彩度塌掉，四个色相挤不开（详见约束③的同一个理由）。
**这是一次有意的让步**：换来的是徽章与公式底色一一对应。
代价写在明处：`dv` 与 `df` 的实际 ΔE 是 deep-blue 9.8 / teal-apricot 15.3 / rose-lilac 7.5 / crimson-blue 7.0，
最差那两套里「决」和「派生」扫读时区分度有限。
**若日后要恢复 ΔE ≥ 12，唯一的出路是给 `--k-f` / `--k-o` 换色相**（`--k-d` 绑着 `--accent`，不能动），
且 teal-apricot 与 crimson-blue 在四支笔下无解，只能退回三支。

**⑤ 高亮底浓度统一在复合 L\* ≈ 85–87。** 再浓公式块就糊，再淡荧光笔看不见。
`--fill-*` 的 alpha 由此反推，四套各不相同（0.22–0.26），不要照抄别套的 alpha。

定色顺序：先 ①③ 定 `--k-d` / `--k-r` 的色相 → 再排另外四个徽章满足 ②（`--k-p` 走灰）→
⑤ 反推四支 `--fill-*` 的 alpha → 验 ④ → 最后每个徽章验文字/底 ≥ 4.5:1。

**「排徽章」必须排在「验 ④」之前**：`--fill-df` / `--fill-do` 由 `--k-f` / `--k-o` 决定（见①），
徽章没定完，四支笔里就有两支还不存在，④ 根本无从验起。

徽章的浅底与描边由主色按固定比例调出：**`-soft` = 12% 色 + 88% 白，`-line` = 34% 色 + 66% 白。**
改主色时这两个必须一起重算，不要手调。

## teal-apricot — 青绿杏

```css
:root{
    --bg:#f7fbfa;
    --fg:#14201e;
    --muted:#4a6f6a;
    --line:#ccdfdc;
    --card:#fbfdfd;
    --accent:#1f6f66;
    --accent-soft:#dbe9e7;
    --accent-line:#bfd7d4;
    --code-bg:#ebf3f2;
    --box-bg:#edf5f3;
    --toc-bg:#e7f0ef;
    --toc-line:#bbd4d1;
    --row-alt:#ebf3f2;
    --row-hl:#d2e3e1;
    --intu-bg:#eff6f5;
    --gloss-bg:#f1f7f6;
    --gloss-line:#b6d1ce;
    --fill-dv:rgba(31,111,102,.25);
    --fill-rp:rgba(137,89,53,.26);
    --fill-df:rgba(132,62,118,0.23);
    --fill-do:rgba(46,99,33,0.24);
    --ok:#1f6b52;
    --ok-soft:#e5efec;
    --ok-line:#b6d0c8;
    --warn:#a05626;
    --warn-soft:#f0ede8;
    --warn-line:#ddc9ba;
    --bad:#a33c3c;
    --bad-soft:#f0ebea;
    --bad-line:#dec2c1;
    --k-s:#2f5fa7;
    --k-s-soft:#e6ecf4;
    --k-s-line:#b8c9e1;
    --k-p:#584b54;
    --k-p-soft:#ebe9ea;
    --k-p-line:#c6c2c5;
    --k-d:#1f6f66;
    --k-d-soft:#e4eeed;
    --k-d-line:#b3cecb;
    --k-r:#895935;
    --k-r-soft:#f1ebe7;
    --k-r-line:#d7c7ba;
    --k-o:#2e6321;
    --k-o-soft:#e6ece4;
    --k-o-line:#b8cab4;
    --k-f:#843e76;
    --k-f-soft:#f0e8ef;
    --k-f-line:#d5bdd0;
    --maxw:1040px;
    --blk:15px;
  }
```

## deep-blue — 深海蓝

**默认配色，已内置于 `assets/template.html`**，直接复制模板即可，无需替换。

```css
:root{
    --bg:#ffffff;
    --fg:#272727;
    --muted:#586e88;
    --line:#cfdbe9;
    --card:#ffffff;
    --accent:#0f4d92;
    --accent-soft:#e0e8f1;
    --accent-line:#c1d1e3;
    --code-bg:#f2f5f9;
    --box-bg:#f4f7fa;
    --toc-bg:#edf2f7;
    --toc-line:#bccde0;
    --row-alt:#f2f5f9;
    --row-hl:#d6e1ec;
    --intu-bg:#f7f9fb;
    --gloss-bg:#f8fafc;
    --gloss-line:#b7cade;
    --fill-dv:rgba(15,77,146,.22);
    --fill-rp:rgba(143,78,50,.25);
    --fill-df:rgba(123,61,120,0.23);
    --fill-do:rgba(35,118,81,0.26);
    --ok:#2d7a3c;
    --ok-soft:#edf4ee;
    --ok-line:#c0d7c5;
    --warn:#8a4380;
    --warn-soft:#f5eff4;
    --warn-line:#dcc7d9;
    --bad:#b64342;
    --bad-soft:#f9efef;
    --bad-line:#e9c7c6;
    --k-s:#3636ab;
    --k-s-soft:#e7e7f5;
    --k-s-line:#bbbbe2;
    --k-p:#67645b;
    --k-p-soft:#edeceb;
    --k-p-line:#cbcac7;
    --k-d:#0f4d92;
    --k-d-soft:#e2eaf2;
    --k-d-line:#adc2da;
    --k-r:#8f4e32;
    --k-r-soft:#f2eae6;
    --k-r-line:#d9c3b9;
    --k-o:#237651;
    --k-o-soft:#e5efea;
    --k-o-line:#b4d0c4;
    --k-f:#7b3d78;
    --k-f-soft:#efe8ef;
    --k-f-line:#d2bdd1;
    --maxw:1040px;
    --blk:15px;
  }
```

## rose-lilac — 玫瑰霜

```css
:root{
    --bg:#fcfcfe;
    --fg:#23233a;
    --muted:#6a6a85;
    --line:#d8d8e3;
    --card:#ffffff;
    --accent:#484878;
    --accent-soft:#e5e5ed;
    --accent-line:#cdcddb;
    --code-bg:#f2f2f7;
    --box-bg:#f4f4f8;
    --toc-bg:#efeff4;
    --toc-line:#cacad8;
    --row-alt:#f2f2f7;
    --row-hl:#dddde7;
    --intu-bg:#f6f6f9;
    --gloss-bg:#f7f7fa;
    --gloss-line:#c6c6d6;
    --fill-dv:rgba(156,79,105,.26);
    --fill-rp:rgba(58,102,168,.26);
    --fill-df:rgba(149,55,144,0.23);
    --fill-do:rgba(31,111,59,0.25);
    --ok:#237837;
    --ok-soft:#eaf1ed;
    --ok-line:#bbd4c2;
    --warn:#8a4a7a;
    --warn-soft:#f2edf3;
    --warn-line:#dac7d6;
    --bad:#c0322f;
    --bad-soft:#f7ebec;
    --bad-line:#eabfc0;
    --k-s:#1f1f6f;
    --k-s-soft:#e4e4ee;
    --k-s-line:#b3b3ce;
    --k-p:#5b675c;
    --k-p-soft:#ebedeb;
    --k-p-line:#c7cbc8;
    --k-d:#9c4f69;
    --k-d-soft:#f3eaed;
    --k-d-line:#ddc3cc;
    --k-r:#3a66a8;
    --k-r-soft:#e7edf5;
    --k-r-line:#bccbe1;
    --k-o:#1f6f3b;
    --k-o-soft:#e4eee7;
    --k-o-line:#b3cebc;
    --k-f:#953790;
    --k-f-soft:#f2e7f2;
    --k-f-line:#dbbbd9;
    --maxw:1040px;
    --blk:15px;
  }
```

## crimson-blue — 朱蓝

```css
:root{
    --bg:#ffffff;
    --fg:#272727;
    --muted:#886060;
    --line:#edd9d9;
    --card:#ffffff;
    --accent:#a5403f;
    --accent-soft:#f3e6e6;
    --accent-line:#e8cdcd;
    --code-bg:#faf4f4;
    --box-bg:#fbf6f6;
    --toc-bg:#f8f1f1;
    --toc-line:#e6cac9;
    --row-alt:#faf4f4;
    --row-hl:#f0dfde;
    --intu-bg:#fcf8f8;
    --gloss-bg:#fcf9f9;
    --gloss-line:#e4c6c5;
    --fill-dv:rgba(165,64,63,.24);
    --fill-rp:rgba(51,105,159,.26);
    --fill-df:rgba(152,62,116,0.24);
    --fill-do:rgba(47,96,69,0.24);
    --ok:#2d7a3c;
    --ok-soft:#edf4ee;
    --ok-line:#c0d7c5;
    --warn:#8a4380;
    --warn-soft:#f5eff4;
    --warn-line:#dcc7d9;
    --bad:#b64342;
    --bad-soft:#f9efef;
    --bad-line:#e9c7c6;
    --k-s:#392461;
    --k-s-soft:#e7e5ec;
    --k-s-line:#bcb5c9;
    --k-p:#74636a;
    --k-p-soft:#eeeced;
    --k-p-line:#d0cacc;
    --k-d:#a5403f;
    --k-d-soft:#f4e8e8;
    --k-d-line:#e0bebe;
    --k-r:#33699f;
    --k-r-soft:#e7edf3;
    --k-r-line:#baccde;
    --k-o:#2f6045;
    --k-o-soft:#e6ece9;
    --k-o-line:#b8c9c0;
    --k-f:#983e74;
    --k-f-soft:#f3e8ee;
    --k-f-line:#dcbdd0;
    --maxw:1040px;
    --blk:15px;
  }
```
