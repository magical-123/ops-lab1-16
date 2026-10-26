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
| 仅凭 BUILD_INFO，能否回答该包是什么、在哪台机器上、何时、用哪份代码构建？哪一项信息会出错？ | 可以知道包名、版本、构建号、Go 版本和记录的 Git 提交号；不能确定构建机器和构建时间。`commit` 记录的是 Git HEAD，而不是包含未提交修改的实际源码状态。本次两次 `BUILD_INFO` 完全相同，但二进制 SHA-256 不同，第二次的 `vcs.modified=true`。因此，仅凭 `BUILD_INFO` 判断"由哪份代码构建"可能出错。 |

## 3. 证据

| 项 | 链接或文本 |
|---|---|
| 规则集里启用的规则 | `Require a pull request before merging`（Required approvals = `1`）；`Require status checks to pass`（必需检查 `ci`，GitHub Actions）；`Restrict deletions`、`Block force pushes`（这两项请在 Rulesets 页面核对后确认）。规则集目标为 `main`，实际直接推送已被拒绝。 |
| 组长直接 `git push origin main` 被拒的终端输出 | `remote: error: GH013: Repository rule violations found for refs/heads/main.`<br>`remote: Review all repository rules at https://github.com/magical-123/ops-lab1-16/rules?ref=refs%2Fheads%2Fmain`<br>`remote: - Changes must be made through a pull request.`<br>`remote: - Required status check "ci" is expected.`<br>`! [remote rejected] main -> main (push declined due to repository rule violations)`<br>`error: failed to push some refs to 'https://github.com/magical-123/ops-lab1-16.git'` |
| 第一轮：失败的 PR 运行 | [run #9](https://github.com/magical-123/ops-lab1-16/actions/runs/39591490938)（`go test` 失败：`节次部分重叠`、`单周对单周冲突`） |
| 第一轮：修复后通过的 PR 运行 | [run #10](https://github.com/magical-123/ops-lab1-16/actions/runs/39592661130) |
| 第一轮：合入 main 后的运行（含制品） | [run #11](https://github.com/magical-123/ops-lab1-16/actions/runs/39594245304) |
| 第二轮：找到的缺陷，以及现有测试为何无法发现它 | `weeksOverlap` 把「双周对双周」误判为不冲突；现有用例未覆盖「双周对双周」这一组合 |
| 第二轮：仅含缺陷时仍然通过的运行 | [run #13](https://github.com/magical-123/ops-lab1-16/actions/runs/39595610009) |
| 第二轮：补测试后失败的运行 | [run #14](https://github.com/magical-123/ops-lab1-16/actions/runs/39595819626) |
| 第二轮：撤销缺陷后通过的运行 | [run #15](https://github.com/magical-123/ops-lab1-16/actions/runs/39595991582) |
| 未合入分支上的 tag 被 release gate 拒绝的运行 | [release #2](https://github.com/magical-123/ops-lab1-16/actions/runs/39605181673) |
| Release v1.0.0 | [v1.0.0](https://github.com/magical-123/ops-lab1-16/releases/tag/v1.0.0) |
| Release v1.0.1 | [v1.0.1](https://github.com/magical-123/ops-lab1-16/releases/tag/v1.0.1) |

## 4. 追溯与运行

对 v1.0.0 和 v1.0.1 各运行一次 `scripts/trace.sh`，粘贴输出，并填写下表。

| 项 | v1.0.0 | v1.0.1 |
|---|---|---|
| 版本号 | 1.0.0 | 1.0.1 |
| 构建号 | 1 | 3 |
| 提交号（SHA） | `ff1694c8ce40b011b5ee0e1a6e2172a3f299c72f` | `aef82d83b0ef54ea310a73dc60805576a53e49f5` |
| 引入该提交的 PR | [PR #4](https://github.com/magical-123/ops-lab1-16/pull/4)<br>[Commit ff1694c](https://github.com/magical-123/ops-lab1-16/commit/ff1694c8ce40b011b5ee0e1a6e2172a3f299c72f) | [PR #6](https://github.com/magical-123/ops-lab1-16/pull/6)<br>[Commit aef82d8](https://github.com/magical-123/ops-lab1-16/commit/aef82d83b0ef54ea310a73dc60805576a53e49f5) |
| 构建它的 Actions 运行（`trace.sh` 给出的链接） | [release #1](https://github.com/magical-123/ops-lab1-16/actions/runs/39603429484) | [release #3](https://github.com/magical-123/ops-lab1-16/actions/runs/39730857391) |
| 制品 sha256（发布当时，取自 Release 里的 .sha256） | `77962108125115b7a1719dcbebff8649ad1befb19e91ea1e20cbee345ce2c246`<br>`enroll-conflict_1.0.0_linux_amd64.tar.gz` | `2f0521b5059a330e032d441b44b638d9ef90e5a198b60a259f1861d778e3f2af`<br>`enroll-conflict_1.0.1_linux_amd64.tar.gz` |

仅凭构建号能否唯一确定是哪次运行？ 不能，不同操作（`ci`、`release`）的构建号各自累计。

v1.0.0 的构建号在 `ci` 和 `release` 的运行列表中各对应哪次运行？ 构建号为 1，在 `ci` 中对应推送 `ci.yml` 的运行，`release` 中对应推送 v1.0.0 标签的运行。

发布 v1.0.1 之后重新下载 v1.0.0 的附件，计算得到的 sha256：77962108125115b7a1719dcbebff8649ad1befb19e91ea1e20cbee345ce2c246

与上表中发布当时的值是否一致？ 一致

`trace.sh` 输出（v1.0.0）：

```text
== 1. 校验和
enroll-conflict_1.0.0_linux_amd64.tar.gz: OK
== 2. 制品里的 BUILD_INFO
name=enroll-conflict
version=1.0.0
build_no=1
run_id=39603429484
commit=ff1694c8ce40b011b5ee0e1a6e2172a3f299c72f
go=go1.26.9
== 3. 提交 ff1694c8ce40b011b5ee0e1a6e2172a3f299c72f
作者: b-wx-ai <aurora_bai@sjtu.edu.cn>
时间: 2026-10-21T20:01:05+08:00
标题: Add even-week overlap test case (#4)
主干: 该提交在 origin/main 的历史上
指向它的 tag: v1.0.0
版本号: 1.0.0    构建号: 1
提交页面（含关联的 PR）: https://github.com/magical-123/ops-lab1-16/commit/ff1694c8ce40b011b5ee0e1a6e2172a3f299c72f
构建它的运行: https://github.com/magical-123/ops-lab1-16/actions/runs/39603429484
```

`trace.sh` 输出（v1.0.1）：

```text
== 1. 校验和
enroll-conflict_1.0.1_linux_amd64.tar.gz: OK
== 2. 制品里的 BUILD_INFO
name=enroll-conflict
version=1.0.1
build_no=3
run_id=39730857391
commit=aef82d83b0ef54ea310a73dc60805576a53e49f5
go=go1.26.9
== 3. 提交 aef82d83b0ef54ea310a73dc60805576a53e49f5
作者: rbhd <rh_sjtu@sjtu.edu.cn>
时间: 2026-10-22T09:13:08+08:00
标题: refactor: extract tue helper to align with mon (#6)
主干: 该提交在 origin/main 的历史上
指向它的 tag: v1.0.1
版本号: 1.0.1    构建号: 3
提交页面（含关联的 PR）: https://github.com/magical-123/ops-lab1-16/commit/aef82d83b0ef54ea310a73dc60805576a53e49f5
构建它的运行: https://github.com/magical-123/ops-lab1-16/actions/runs/39730857391
```

任务 6 第 4 步，两次 `/version` 的输出：

```text
运行 v1.0.1 时：
{"version":"1.0.1","commit":"aef82d83b0ef54ea310a73dc60805576a53e49f5","build_no":"3","go":"go1.26.9"}

换回 v1.0.0 后：
{"version":"1.0.0","commit":"ff1694c8ce40b011b5ee0e1a6e2172a3f299c72f","build_no":"1","go":"go1.26.9"}
```

## 5. 数据

时间均从 GitHub 页面读取，并注明时区。

第一轮 gate 演练的时间线：

| 事件 | 时间 | 链接 |
|---|---|---|
| 发起 PR | 10:47:16 UTC | [#3](https://github.com/magical-123/ops-lab1-16/pull/3) |
| 第一次 `ci` 失败（运行结束） | 11:28:59 UTC（约） | [run #9](https://github.com/magical-123/ops-lab1-16/actions/runs/39591490938) |
| 推送修复提交 | 11:32:06 UTC | [run #10](https://github.com/magical-123/ops-lab1-16/actions/runs/39592661130) |
| `ci` 通过（运行结束） | 11:32:29 UTC（约） | [run #10](https://github.com/magical-123/ops-lab1-16/actions/runs/39592661130) |
| 合入 main | 11:44:52 UTC | [#3](https://github.com/magical-123/ops-lab1-16/pull/3) |

提交到发布的间隔，至少记录三个合入的 PR：

| PR | 第一个提交的时间 | 合入时间 | 对应 Release 的发布时间 | 提交到发布的间隔 |
|---|---|---|---|---|
| #3 | `2026-10-21T11:09:53Z` | `2026-10-21T11:44:52Z` | `2026-10-21T12:55:01Z` | 1h 45min |
| #4 | `2026-10-21T11:49:42Z` | `2026-10-21T12:01:05Z` | `2026-10-21T12:55:01Z` | 1h 5min |
| #6 | `2026-10-21T17:13:03Z` | `2026-10-22T01:13:09Z` | `2026-10-22T05:56:21Z` | 12h 43min |

`ci` 工作流的运行时长（从 Actions 页面读取），至少 5 次，全部列出，不要只给出平均值：

| 运行 | 事件（PR / push） | 时长 | 备注 |
|---|---|---|---|
| #1 | push | 30s | add CI workflow |
| #2 | PR | 18s |  |
| #3 | PR | 20s |  |
| #4 | PR | 11s |  |
| #5 | push | 20s |  |
| #6 | PR | 10s | failed |
| #7 | PR | 13s | failed |
| #8 | PR | 9s | failed |
| #9 | PR | 17s | failed |
| #10 | PR | 23s |  |
| #11 | push | 16s |  |
| #12 | push | 14s |  |
| #13 | PR | 14s |  |
| #14 | PR | 16s | failed |
| #15 | PR | 16s |  |
| #16 | push | 17s |  |
| #17 | PR | 15s |  |
| #18 | PR | 17s |  |
| #19 | push | 23s |  |
| #20 | PR | 14s |  |
| #21 | push | 20s |  |


## 6. 分析问题

每题先列出所用数据（可引用上面的表），再用一段话（4～8 句）写出结论，并说明结论的成立条件。

**问题 1：gate 的收益与成本。** 两轮演练中，gate 拦住了什么，没有拦住什么？用第 5 节的时间线计算：第一轮因失败多花了多少时间，其中等待 `ci` 的时间和人工修改的时间各占多少？一次 `ci` 大约需要多久，在"提交到发布的间隔"中占多大比例？第二轮补充测试之后，还有哪类问题是测试本身无法发现的？

所用数据：

| 指标 | 计算结果 |
|---|---|
| 第一轮恢复时间 | `11:32:29 − 10:47:30 = 44分59秒` |
| 恢复期间 CI 累计耗时 | #7、#8、#9 两次尝试、#10：`13＋9＋19＋17＋23＝81秒` |
| 其余响应、排查、修改及操作间隔 | `2699 − 81＝2618秒＝43分38秒` |
| 最后一次失败到修复通过 | `11:32:29 − 11:28:59＝3分30秒`，其中修复推送前间隔3分7秒，修复后CI为23秒 |
| 表中21次CI平均时长 | `353 ÷ 21 ≈ 16.8秒`，范围9～30秒 |
| 单次平均CI占提交到发布间隔 | PR #3约0.27%；#4约0.43%；#6约0.037% |

其中，恢复期间累计耗时需额外计入此前已核实的 [#9 第一次尝试的19秒](https://github.com/magical-123/ops-lab1-16/actions/runs/39591490938/attempts/1)；21次CI的平均值则严格按第5节表列的21条记录计算。恢复时间从 2026-10-21 UTC 的 [#6 首次失败结束](https://github.com/magical-123/ops-lab1-16/actions/runs/39587142259) 起，计算至 [#10 修复后通过](https://github.com/magical-123/ops-lab1-16/actions/runs/39592661130)，不包含 #6 自身的10秒。

第一轮中，gate 拦住了 gofmt 格式检查不通过的提交，也通过现有测试发现了节次边界判断的逻辑缺陷，使这些问题在修复、检查通过后才进入主干。第二轮中，“双周对双周”缺陷因缺少对应测试而在 #13 中通过，补测试后 #14 失败、撤销缺陷后 #15 通过，说明 gate 的有效性取决于检查内容和测试覆盖。按首次失败结束至修复通过的口径，第一轮恢复耗时约44分59秒，其中后续CI累计运行1分21秒，占约3%。其余43分38秒约占97%，包含响应、排查、人工修改和操作之间的等待，不能全部视为纯人工工时；这个窗口也是观察到的恢复耗时，缺少无失败对照时，不能精确认定它就是失败造成的净额外延迟。若只看最后一次测试失败后的修复阶段，则耗时3分30秒，其中失败结束至推送修复间隔3分7秒，修复后的CI耗时23秒。表中21次CI平均约16.8秒，单次平均运行分别占三个PR提交到发布间隔的约0.27%、0.43%、0.037%，表明本项目单次CI运行的时间占比较小，但这些比例不代表各PR的累计CI成本。即使补齐“双周对双周”用例，其他未覆盖的边界、错误的需求理解，以及未专门验证的性能、安全和生产环境问题仍可能漏检，需要结合人工审查及相应测试发现。上述结论以门禁规则持续强制执行、相关检查真实运行及本组当前项目和样本为条件，不能仅凭CI通过就认定代码正确，也不能直接推广到其他项目。

**问题 2：PR 中的构建与 main 上的构建。** 在 PR 触发的运行日志中，`github.sha` 与 `pull_request.head.sha` 是否相同？它们能否在 main 的历史中找到？答案与合并方式有关，请写明本组使用的方式。合并之后，main 上这次改动的提交号是哪个？据此说明，为什么待发布的制品只能从 main 上的提交或 tag 构建。

以 PR #10 为例，在 PR 触发的运行日志中（[证据链接](https://github.com/magical-123/ops-lab1-16/actions/runs/40105163910/job/120905231368)），`github.sha` 为 `f30265a21b8047425e62635daea83e9d5fa2bcae`，`pull_request.head.sha` 为 `86b5981ed480ba3785a7661880880edf45a5f2bb`，两者**不相同**。`pull_request.head.sha` 是 PR 分支上的最后一个提交，而 `github.sha` 是 GitHub 为测试目的创建的临时 merge commit，用于模拟将 PR 合并到 main 后的状态。这两个 SHA **都不在** main 的历史中：`86b5981` 是 PR 分支上的提交，尚未合并；`f30265a` 是临时测试用的 merge commit，不会进入任何分支的历史。

本组使用 **squash merge** 方式。合并后，main 上这次改动的提交号是 `c4df7231be922aecf03b8dc064773d1bdff6b23a`，这是一个全新的提交，将 PR 中的多个提交（`27bc682` 和 `86b5981`）压缩成一个，提交信息、SHA 和作者时间都重新生成。

**为什么待发布的制品只能从 main 或 tag 构建：** (1) PR 中的 `github.sha` 和 `pull_request.head.sha` 都不在 main 历史中，无法通过 `git log` 追溯；(2) squash merge 会生成新的提交号，PR 中构建的制品对应的 SHA 在合并后不再存在；(3) 只有 main 或 tag 上的提交才是真正发布的代码状态，能够保证可追溯性和可重复构建；(4) 如果从 PR 构建制品，其 BUILD_INFO 中的 commit 字段将指向一个不在 main 历史上的幽灵提交，`trace.sh` 会报错"该提交不在 main 的历史上"，无法验证发布的制品来源。

**问题 3：可重复构建。** 在本地同一个提交上按下表构建，记录哈希值。④、⑤ 需临时去掉 Makefile 中 tar 的 `--mtime=@0`，完成后用 `git checkout -- Makefile` 恢复，不要提交。

| 构建 | 条件 | 二进制 sha256 | tar.gz sha256 |
|---|---|---|---|
| ① | `make package`，`VERSION`、`BUILD_NO`、`RUN_ID`、`COMMIT` 均指定固定值 | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `17c4644b36c59182c967cbd2e20dd226d1526e73def25f03c86126849bc4c8bf3` |
| ② | 与 ① 完全相同，再构建一次 | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `17c4644b36c59182c967cbd2e20dd226d1526e73def25f03c86126849bc4c8bf3` |
| ③ | 仅将 `BUILD_NO` 换成另一个值 | `9f7e6413513c7291b53429a3b21ea62e9484da694ce5dfc834b6a2b4437babd1` | `7258efd316a9258ad2ae00c6f875bb98309adfa0a315b36e2598de485f67850e` |
| ④ | 去掉 `--mtime=@0`，其余同 ① | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `925c35eabff8b4d6e8930400ab3925331acb1686e23d7960de19c0ddc85ab551` |
| ⑤ | 同 ④，间隔几秒后再构建一次 | `94446a4f86375afc0a149ddfe89a62251adc29715860bddfb6201b8ec1e3bc2` | `339891b3cefa5fd7dd09e274a3af4999839f4dfe664c2de1811999e3f710812` |
| Actions | 对 main 上的一次 push 运行执行 Re-run all jobs，比较两次尝试中 package 步骤打印的值 | 第一次：<br>`7c550ce2a35b69fb89d17afe771716e2b8b73a4f33c846f97b3e49a325db1b56`<br>Re-run：<br>`7c550ce2a35b69fb89d17afe771716e2b8b73a4f33c846f97b3e49a325db1b56` | 第一次：<br>`024757461472ca9bd22703e8aecf98d2bbfcc0e771826e5d8e4e6db339f3a85e`<br>Re-run：<br>`024757461472ca9bd22703e8aecf98d2bbfcc0e771826e5d8e4e6db339f3a85e` |

哪些相同、哪些不同，原因分别是什么？④ 的二进制文件与 ① 是否相同？如果不同，请结合任务 0 中看到的 vcs 信息分析。据此说明制品中应当包含什么、不应包含什么。另外，`release` 在同一个提交上再次编译，而没有直接发布 `ci` 产出的主干制品：为什么这样做？这与 L-05 第 11 页的"一次构建、多处部署"是否冲突，代价是什么？

1. **哪些相同、哪些不同？**

    构建①和②的Makefile中记录的参数完全相同，而可能变化的构建环境信息被Makefile标准化或排除，二进制和 tar.gz 哈希一致；构建③改变 `BUILD_NO`，Makefile中记录的参数值改变，因此二进制和 tar.gz 哈希都改变。构建④、⑤与①的二进制哈希一致，但去掉 `--mtime=@0` 后，tar.gz 会记录不同的文件修改时间，所以压缩包哈希改变。
    
    Re-run 前后二进制和 tar.gz 哈希均相同。这是因为 Re-run 是同一次运行的重试（新 Attempt），`BUILD_NO` 和 `RUN_ID` 保持不变，且 Makefile 通过 `--mtime=@0`、`--sort=name`、`--owner=0` 等参数排除了时间戳和环境差异，实现了可重复构建。

2. **④的二进制文件与①是否相同？**
    
    相同。④只改变了打包阶段的 tar 时间戳处理，没有改变源码、提交号或 Go 编译参数，因此生成的二进制内容不变。

3. **制品应包含什么、不应包含什么？**
    
    制品应包含版本号、提交号、构建号等可追溯信息；不应包含本机路径和不必要的构建时间戳等非确定性信息。

4. **为什么 release 要重新编译？是否与"一次构建、多处部署"冲突？**
    
    `release` 重新编译是因为**版本号需要在编译时写入二进制文件**。CI 构建时还不知道最终版本号（使用的是 `0.0.0-main`），只有推送 tag 时才能从 tag 名称提取正式版本号（如 `1.0.0`），因此 release 必须重新编译以将正式版本号写入制品。
    
    这与"一次构建、多处部署"存在取舍：工业界通常在 CI 阶段就确定版本号，构建一次后让测试、预发布和生产环境使用同一个已验证的制品；而本实验的做法是 CI 和 release 分别构建，导致 CI 测试过的制品与最终发布的制品不是同一个文件。优点是发布流程独立、便于再次验证；缺点是增加构建时间，且必须保证两次构建环境和参数一致，否则可能产生不同制品。

**问题 4：发布与回退。** 假设 v1.0.1 发布后发现缺陷，应当如何处理？为什么不应删除 v1.0.1 的 tag、再用同一版本号重新发布？任务 6 中将服务从 v1.0.1 切换回 v1.0.0 时，使用的是哪个文件，是否需要重新构建？请用两次 `/version` 的输出说明。如果改为从源码重新构建 v1.0.0，得到的文件与原附件是否相同（参考问题 3）？最后，如果有人删除 v1.0.0 的附件，换上一个新文件和对应的新 `.sha256`，`trace.sh` 能否发现？本组在第 4 节记录的值能否发现？可以查阅 GitHub 的 Immutable releases 设置的用途。

1. **发现 v1.0.1 存在缺陷时怎么办？**

    应修复缺陷、重新测试，走 PR 流程合并到主分支，并发布新的版本（`v1.0.2`）。不能原地修复覆盖已有版本。

2. **为什么不能删除旧 tag 后重复发布？**

    同一个版本号应始终对应同一份代码和制品，重新使用版本号会使标签含义混乱、破坏哈希校验；重新使用版本号后，已经拉取旧 tag 的人不会自动更新，导致同一 tag 指向不同提交。

3. **回退文件和是否需要重新构建？**

    使用 v1.0.0 Release 中已经验证过的原附件，不需要重新构建。证据为 `/version` 输出中，切换后 `"version"` 的值从 `"1.0.1"` 变为 `"1.0.0"`。

4. **重新构建后文件是否与原附件相同？** 

    根据问题 3 的数据和 Makefile 文件，已限制 `mtime`，新文件的哈希值与版本号、构建号、运行号等有关。无论是本地直接构建（使用默认值），还是在 Github Actions 中重新运行构建（运行号不同），新文件的哈希值都与原附件不同。只有在严格填入原参数构建时才能得到相同的文件。

5. **附件被替换后能否发现？** 

    `trace.sh` 无法发现。它通过检查文件的哈希值是否等于 `.sha256` 记录的哈希值来判断是否被替换。文件和哈希记录同时被替换的情况 `trace.sh` 无法发现。

    外部记录可以发现。外部记录在 Release 后可以保证文件没被替换时写下，后续如果文件被替换，无论 `.sha256` 是否一并替换，只要外部记录的原哈希值与此文件的哈希值不同，就可以判定文件被替换了。

    Immutable releases 设置开启后，保护 Release 的附件不可被删除或修改，Release 本身不可被删除。使得附件一旦上传就不可变，保护了制品的完整性。


## 7. 选做记录

| 选做 | 完成情况与证据 |
|---|---|
| O1 容器镜像 | 已完成。<br>Actions：[v1.0.1 release 运行](https://github.com/magical-123/ops-lab1-16/actions/runs/39730857391)；<br>GHCR：<https://github.com/magical-123/ops-lab1-16/pkgs/container/ops-lab1-16>；<br>镜像：`ghcr.io/magical-123/ops-lab1-16`；<br>v1.0.1 digest：`sha256:b69da7943fa9d4a17f0cf7241431175e97411e374119fc533f7d243724324c1d`；<br>架构：`linux/amd64`；<br>使用 digest 启动容器成功，`/version` 返回 `version=1.0.1`、`commit=aef82d83b0ef54ea310a73dc60805576a53e49f5`、`build_no=3`、`go=go1.26.9`。 |
| O2 缓存对反馈时间的影响 | **预期：** 示例项目虽无第三方依赖，但 `setup-go` 仍需安装 Go 工具链并缓存构建结果。预期缓存能节省 10-15 秒，主要来自 `go vet` 静态分析：`go vet` 需要类型信息来进行代码检查，无缓存时需先编译生成类型信息再分析，有缓存时可直接使用构建缓存中的类型信息，预期节省约 10 秒。其他步骤（工具链安装、测试）的缓存收益相对较小。<br><br>**实际数据：**<br>• 有缓存：18s, 17s, 17s, 18s, 20s（平均18s，波动3s）<br>• 无缓存：28s, 32s, 29s, 43s, 25s（平均31.4s，波动18s）<br><br>**证据链接：**<br>• 有缓存运行：[Run #1](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866597/attempts/1), [#2](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866597/attempts/2), [#3](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866597/attempts/3), [#4](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866597/attempts/4), [#5](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866597)<br>• 无缓存运行：[Run #1](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866598/attempts/1), [#2](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866598/attempts/2), [#3](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866598/attempts/3), [#4](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866598/attempts/4), [#5](https://github.com/magical-123/ops-lab1-16/actions/runs/40101866598)<br>• 实验 PR（未合并）：[#8 experiment/cache-test](https://github.com/magical-123/ops-lab1-16/pull/8)<br>• 缓存配置：[ci.yml 第19-22行](https://github.com/magical-123/ops-lab1-16/blob/main/.github/workflows/ci.yml#L19-L22)，`actions/setup-go@v7` 的 `cache` 参数（默认 `true`，实验分支设为 `false`）<br><br>**结论：** 缓存使 CI 加速约43%（节省13.4秒），**符合预期**。通过对比各步骤耗时发现，缓存收益主要来自 **`go vet` 步骤**（节省约10秒）：`go vet` 进行静态分析时需要类型信息，无缓存时必须先编译所有代码生成类型信息再分析，有缓存时可直接使用构建缓存（$GOCACHE）中的类型信息跳过编译。其他步骤如工具链安装、测试等的缓存收益相对有限（合计约3-4秒）。无缓存组波动更大（18s vs 3s），表明共享 runner 负载对无缓存场景影响更明显。实验局限：样本量小，第4次无缓存运行(43s)可能为异常值。 |
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
| magical-123 | #2 | #5, #10 |
| rbhd-sjtu | #6 | #11 |
| Bugbank2 | #10, #11 | #2, #3, #4 |
| lexa677 | #1, #5 | #6 |
| b-wx-ai | #3, #4 | #1 |
