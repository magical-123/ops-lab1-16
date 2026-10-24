# 实验 1 记录：第 16 组

现场检查时将打开本文件，按其中的链接核对。证据一律以链接或文本形式给出，不要只贴截图。将 `<填写>` 替换为本组的内容。main 受保护之后，修改本文件也须通过 PR。

## 1. 基本信息

| 项 | 内容 |
|---|---|
| 小组 | 第 16 组 |
| 仓库地址 | <https://github.com/magical-123/ops-lab1-16> |
| 代码载体 | 统一示例（`enroll-conflict`，Go） |
| runner 与语言版本 | `ubuntu-24.04`，Go `1.26.9`（待 B 核对 CI 实际配置） |
| 合并方式 | squash |

## 2. 任务 0：本地基线

| 问题 | 观察结果 |
|---|---|
| 第一次 `make package` 得到的 BUILD_INFO（粘贴） | `name=enroll-conflict`<br>`version=1.0.0`<br>`build_no=1`<br>`run_id=local`<br>`commit=7b63b41c13f061439997b0e6e4eaa2af922a7aa1`<br>`go=go1.26.9` |
| 修改一行、不提交，再次打包：BUILD_INFO 是否变化？二进制文件的 sha256 是否变化？（贴出两次的 sha256） | `BUILD_INFO` 未变化；二进制文件的 SHA-256 发生变化。<br>第一次：`f23447d406c42af7dd35e23c2d1b2f15484cfb709cc9b4bb1262643694b8528e`<br>第二次：`a95a0f6b1d604ac705fdefbab2f693447b787f4bd28c4fac3f035e744bd3d593` |
| 两次 `go version -m` 输出中的 vcs 行有何不同？ | 两次的 `vcs=git`、`vcs.revision=7b63b41c13f061439997b0e6e4eaa2af922a7aa1`、`vcs.time=2026-10-12T16:26:07Z` 相同；`vcs.modified` 由第一次的 `false` 变为第二次的 `true`。 |
| 仅凭 BUILD_INFO，能否回答该包是什么、在哪台机器上、何时、用哪份代码构建？哪一项信息会出错？ | 可以知道包名、版本、构建号、Go 版本和记录的 Git 提交号；不能确定构建机器和构建时间。`commit` 记录的是 Git HEAD，而不是包含未提交修改的实际源码状态。本次两次 `BUILD_INFO` 完全相同，但二进制 SHA-256 不同，第二次的 `vcs.modified=true`。因此，仅凭 `BUILD_INFO` 判断“由哪份代码构建”可能出错。 |

## 3. 证据

| 项 | 链接或文本 |
|---|---|
| 规则集里启用的规则 | `Require a pull request before merging`（Required approvals = `1`）；`Require status checks to pass`（必需检查 `ci`，GitHub Actions）；`Restrict deletions`、`Block force pushes`（这两项请在 Rulesets 页面核对后确认）。规则集目标为 `main`，实际直接推送已被拒绝。 |
| 组长直接 `git push origin main` 被拒的终端输出 | `remote: error: GH013: Repository rule violations found for refs/heads/main.`<br>`remote: Review all repository rules at https://github.com/magical-123/ops-lab1-16/rules?ref=refs%2Fheads%2Fmain`<br>`remote: - Changes must be made through a pull request.`<br>`remote: - Required status check "ci" is expected.`<br>`! [remote rejected] main -> main (push declined due to repository rule violations)`<br>`error: failed to push some refs to 'https://github.com/magical-123/ops-lab1-16.git'` |
| 第一轮：失败的 PR 运行 | <链接> |
| 第一轮：修复后通过的 PR 运行 | <链接> |
| 第一轮：合入 main 后的运行（含制品） | <链接> |
| 第二轮：找到的缺陷，以及现有测试为何无法发现它 | <一两句话> |
| 第二轮：仅含缺陷时仍然通过的运行 | <链接> |
| 第二轮：补测试后失败的运行 | <链接> |
| 第二轮：撤销缺陷后通过的运行 | <链接> |
| 未合入分支上的 tag 被 release gate 拒绝的运行 | <链接> |
| Release v1.0.0 | <链接> |
| Release v1.0.1 | <链接> |

## 4. 追溯与运行

对 v1.0.0 和 v1.0.1 各运行一次 `scripts/trace.sh`，粘贴输出，并填写下表。

| 项 | v1.0.0 | v1.0.1 |
|---|---|---|
| 版本号 | <填写> | <填写> |
| 构建号 | <填写> | <填写> |
| 提交号（SHA） | <填写> | <填写> |
| 引入该提交的 PR | <编号与链接> | <编号与链接> |
| 构建它的 Actions 运行（`trace.sh` 给出的链接） | <链接> | <链接> |
| 制品 sha256（发布当时，取自 Release 里的 .sha256） | <填写> | <填写> |

仅凭构建号能否唯一确定是哪次运行？v1.0.0 的构建号在 `ci` 和 `release` 的运行列表中各对应哪次运行？<填写>

发布 v1.0.1 之后重新下载 v1.0.0 的附件，计算得到的 sha256：<填写>。与上表中发布当时的值是否一致？<填写>

`trace.sh` 输出（v1.0.0）：

```text
<粘贴>
```

`trace.sh` 输出（v1.0.1）：

```text
<粘贴>
```

任务 6 第 4 步，两次 `/version` 的输出：

```text
<运行 v1.0.1 时>
<换回 v1.0.0 后>
```

## 5. 数据

时间均从 GitHub 页面读取，并注明时区。

第一轮 gate 演练的时间线：

| 事件 | 时间 | 链接 |
|---|---|---|
| 发起 PR | <填写> | <PR 链接> |
| 第一次 `ci` 失败（运行结束） | <填写> | <链接> |
| 推送修复提交 | <填写> | <链接> |
| `ci` 通过（运行结束） | <填写> | <链接> |
| 合入 main | <填写> | <链接> |

提交到发布的间隔，至少记录三个合入的 PR：

| PR | 第一个提交的时间 | 合入时间 | 对应 Release 的发布时间 | 提交到发布的间隔 |
|---|---|---|---|---|
| #<编号> | <填写> | <填写> | <填写> | <填写> |
| #<编号> | <填写> | <填写> | <填写> | <填写> |
| #<编号> | <填写> | <填写> | <填写> | <填写> |

`ci` 工作流的运行时长（从 Actions 页面读取），至少 5 次，全部列出，不要只给出平均值：

| 运行 | 事件（PR / push） | 时长 | 备注 |
|---|---|---|---|
| #<编号> | <填写> | <填写> | <填写> |

## 6. 分析问题

每题先列出所用数据（可引用上面的表），再用一段话（4～8 句）写出结论，并说明结论的成立条件。

**问题 1：gate 的收益与成本。** 两轮演练中，gate 拦住了什么，没有拦住什么？用第 5 节的时间线计算：第一轮因失败多花了多少时间，其中等待 `ci` 的时间和人工修改的时间各占多少？一次 `ci` 大约需要多久，在“提交到发布的间隔”中占多大比例？第二轮补充测试之后，还有哪类问题是测试本身无法发现的？

<填写>

**问题 2：PR 中的构建与 main 上的构建。** 在 PR 触发的运行日志中，`github.sha` 与 `pull_request.head.sha` 是否相同？它们能否在 main 的历史中找到？答案与合并方式有关，请写明本组使用的方式。合并之后，main 上这次改动的提交号是哪个？据此说明，为什么待发布的制品只能从 main 上的提交或 tag 构建。

<填写>

**问题 3：可重复构建。** 在本地同一个提交上按下表构建，记录哈希值。④、⑤ 需临时去掉 Makefile 中 tar 的 `--mtime=@0`，完成后用 `git checkout -- Makefile` 恢复，不要提交。

| 构建 | 条件 | 二进制 sha256 | tar.gz sha256 |
|---|---|---|---|
| ① | `make package`，`VERSION`、`BUILD_NO`、`RUN_ID`、`COMMIT` 均指定固定值 | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `17c4644b36c59182c967cbd2e20dd226d1526e73def25f03c86126849bc4c8bf3` |
| ② | 与 ① 完全相同，再构建一次 | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `17c4644b36c59182c967cbd2e20dd226d1526e73def25f03c86126849bc4c8bf3` |
| ③ | 仅将 `BUILD_NO` 换成另一个值 | `9f7e6413513c7291b53429a3b21ea62e9484da694ce5dfc834b6a2b4437babd1` | `7258efd316a9258ad2ae00c6f875bb98309adfa0a315b36e2598de485f67850e` |
| ④ | 去掉 `--mtime=@0`，其余同 ① | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `925c35eabff8b4d6e8930400ab3925331acb1686e23d7960de19c0ddc85ab551` |
| ⑤ | 同 ④，间隔几秒后再构建一次 | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `339891b3cefa5fd7dd09e274a3af4999839f4dfe664c2de1811999e3f710812` |
| Actions | 对 main 上的一次 push 运行执行 Re-run all jobs，比较两次尝试中 package 步骤打印的值 | <两次的值> | <两次的值> |

哪些相同、哪些不同，原因分别是什么？④ 的二进制文件与 ① 是否相同？如果不同，请结合任务 0 中看到的 vcs 信息分析。据此说明制品中应当包含什么、不应包含什么。另外，`release` 在同一个提交上再次编译，而没有直接发布 `ci` 产出的主干制品：为什么这样做？这与 L-05 第 11 页的“一次构建、多处部署”是否冲突，代价是什么？

1. **哪些相同、哪些不同？** 构建①和②的Makefile中记录的参数完全相同，而可能变化的构建环境信息被Makefile标准化或排除，二进制和 tar.gz 哈希一致；构建③改变 `BUILD_NO`，Makefile中记录的参数值改变，因此二进制和 tar.gz 哈希都改变。构建④、⑤与①的二进制哈希一致，但去掉 `--mtime=@0` 后，tar.gz 会记录不同的文件修改时间，所以压缩包哈希改变。

2. **④的二进制文件与①是否相同？** 相同。④只改变了打包阶段的 tar 时间戳处理，没有改变源码、提交号或 Go 编译参数，因此生成的二进制内容不变。

3. **制品应包含什么、不应包含什么？** 制品应包含版本号、提交号、构建号等可追溯信息；不应包含本机路径和不必要的构建时间戳等非确定性信息。

4. **为什么 release 要重新编译？是否与“一次构建、多处部署”冲突？** `release` 重新编译是为了在发布 gate 通过后再次执行检查、打包并生成正式附件，但可能因 Go 版本、依赖或构建环境变化，导致 CI 测试过的制品与最终发布的制品不一致。工业界通常采用“一次构建、多处部署”，让测试、预发布和生产环境使用同一个已经验证的制品，从而避免这种差异。因此，本实验的 release 重编译与该理念存在取舍：优点是发布流程独立、便于再次验证；缺点是增加时间，并且必须保证两次构建环境和参数一致，否则可能产生不同制品。

**问题 4：发布与回退。** 假设 v1.0.1 发布后发现缺陷，应当如何处理？为什么不应删除 v1.0.1 的 tag、再用同一版本号重新发布？任务 6 中将服务从 v1.0.1 切换回 v1.0.0 时，使用的是哪个文件，是否需要重新构建？请用两次 `/version` 的输出说明。如果改为从源码重新构建 v1.0.0，得到的文件与原附件是否相同（参考问题 3）？最后，如果有人删除 v1.0.0 的附件，换上一个新文件和对应的新 `.sha256`，`trace.sh` 能否发现？本组在第 4 节记录的值能否发现？可以查阅 GitHub 的 Immutable releases 设置的用途。

1. **发现 v1.0.1 存在缺陷时怎么办？** 应修复缺陷、重新测试，并发布新的版本，例如 `v1.0.2`，不要覆盖已有版本。

2. **为什么不能删除旧 tag 后重复发布？** 因为同一个版本号应始终对应同一份代码和制品；重新使用版本号会破坏追溯、哈希校验和用户对旧版本的信任。

3. **如何回退到 v1.0.0？** 应直接使用 v1.0.0 Release 中已经验证过的原附件，不需要重新构建。重新构建可能因环境或参数变化产生不同文件。

4. **如何确认回退成功？** 比较切换前后的 `/version` 输出：回退后版本号、构建号和提交号应恢复为 v1.0.0 对应的值。

## 7. 选做记录

| 选做 | 完成情况与证据 |
|---|---|
| O1 容器镜像 | <未做 / 链接、镜像摘要（digest）、运行容器的主机架构> |
| O2 缓存对反馈时间的影响 | <未做 / 预期、数据与结论> |
| O3 迁移到小组项目 | **前端迁移与正式发布验收通过。** 真实项目：[InfoDigest](https://github.com/magical-123/SE26Project-08)；[迁移 PR #36](https://github.com/magical-123/SE26Project-08/pull/36)；[迁移差异与证据](https://github.com/magical-123/SE26Project-08/blob/main/docs/o3-migration.md)；[v1.0.0 Release](https://github.com/magical-123/SE26Project-08/releases/tag/v1.0.0)。 |

O3 本次范围为 InfoDigest 前端，后端迁移留待后续。原仓库已公开，迁移 [PR #36](https://github.com/magical-123/SE26Project-08/pull/36) 经 Bugbank2 审批后 squash 合入 main。[main 门禁](https://github.com/magical-123/SE26Project-08/rules/25775887) 为 Active、无 Bypass、必需 ci、其他成员一次审批、禁止删除/强推；管理员直接推 main 被 GH013 拒绝的服务器原文见迁移说明。

迁移差异：Go 检查/测试/单二进制替换为固定 Node 24.14.0 的 npm ci、oxlint、Vitest 和 Vite 静态资源；恢复五套 205 项真实测试并 mock 外部 API，BUILD_INFO 和 /version.json 使用同一组 Actions 上下文。主干制品保留 14 天，标签发布先检查提交属于 main 历史。原校内服务器已停用，看门狗自动部署暂停，采用 GitHub Release 和本地静态启动验收；业务接口仍需后端。完整归档页面测试尚有过时 fixture，本次不宣称全应用测试覆盖。

门禁证据：[故意错误断言使 CI 失败](https://github.com/magical-123/SE26Project-08/actions/runs/40079231793)、[修复后检查/205 项测试/构建通过](https://github.com/magical-123/SE26Project-08/actions/runs/40079374698)、[非 main 历史标签被 release gate 拒绝且未生成 Release](https://github.com/magical-123/SE26Project-08/actions/runs/40079415954)。正式验收：[main CI 与主干 artifact](https://github.com/magical-123/SE26Project-08/actions/runs/40080173019)、[v1.0.0 发布运行](https://github.com/magical-123/SE26Project-08/actions/runs/40080459868)、[正式 Release 与两个附件](https://github.com/magical-123/SE26Project-08/releases/tag/v1.0.0)。

实际下载发布的 tar.gz 和同名 sha256 后，使用 scripts/frontend_release.py smoke 校验 SHA256、单一顶层目录、BUILD_INFO 与 HTTP /version.json 一致，并验证静态首页；trace 返回对应源码、Actions 和 Release 链接。记录如下：

| 字段 | 实际值 |
|---|---|
| name | infodigest-frontend |
| version | 1.0.0 |
| build_no | 2 |
| run_id | 40080459868 |
| commit | e609f8e86d4db438ae1f852eeffc378aeb999d94 |
| tar.gz SHA256 | 105a694fe885425d289caebf9b0e0bc18eedf7ffc4448f977c4e03f320b62a91 |

## 8. 成员贡献

每位成员一行，只填写 GitHub 用户名，无需填写姓名；行数应与小组人数一致。

| GitHub 用户名 | 发起并合入的 PR 编号 | 审批过的 PR 编号 |
|---|---|---|
| magical-123 | <编号> | <编号> |
