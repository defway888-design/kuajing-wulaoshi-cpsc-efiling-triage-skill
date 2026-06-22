# 跨境吴老师 CPSC/eFiling 自动初筛 Skill

## 一、这个 Skill 是做什么的

这是一个面向亚马逊美国站卖家的 CPSC/eFiling 自动初筛 Skill。

用户只需要输入一个 Amazon US ASIN，Skill 会自动调用卖家精灵 MCP 获取商品标题、类目、五点描述、图片、变体、尺寸、重量、包装信息等数据，并根据 Listing 信息初步判断：

- 商品是否可能受 CPSC 监管
- 是否可能触发 eFiling
- 证书路径更接近 CPC、GCC、暂不能判定还是暂不适用
- 可能涉及哪些 16 CFR 法规或检测方向
- 还缺哪些关键信息
- 卖家下一步应该补什么资料、找谁复核、走什么申报路径

重要定位：

```text
本 Skill 是 CPSC/eFiling 自动初筛工具，不是最终法律判定工具。
最终以 CPSC 官方规则、检测机构、报关行和专业合规意见为准。
```

## 二、用户需要输入什么

最低输入：

```text
ASIN
```

默认业务场景：

```text
站点：Amazon US
履约：FBA 发美国
用途：CPSC/eFiling 初筛
```

示例：

```text
使用跨境吴老师 CPSC/eFiling 初筛 Skill，帮我检查 ASIN：B0DJ88YX8H
```

如果 Listing 数据不足，Skill 只会追问影响判断的关键问题，例如：

- 产品是否面向 12 岁及以下儿童？
- 是否含纽扣电池或硬币电池？
- 是否带充电器、电源适配器或电池？
- 是否有油漆、涂层、印刷涂层？
- 是否含纺织面料、填充物、泡棉？
- 是否有小零件、磁铁、尖锐边缘、绳带？
- HTS 编码是多少？

用户不需要一开始手动填写完整合规表。

## 三、用户会得到什么输出

Skill 会输出一份“跨境吴老师 CPSC/eFiling 自动初筛报告”。

标准输出结构：

```text
0. 重要定位
说明这是自动初筛，不是最终法律判定。

1. 商品识别
展示 ASIN、产品名称、品牌、类目、FBA/US 默认场景、关键 Listing 信息。

2. 一句话结论
直接告诉卖家 CPSC 风险等级、eFiling 状态、证书路径是否明确。

3. 为什么这么判断
列出来自标题、五点、类目、图片、变体、尺寸、材质关键词的判断依据。

4. 可能涉及的法规/检测方向
列出可能涉及的 16 CFR、CPC/GCC、检测项目、标签或记录要求。

5. 还缺什么信息
只列出影响判断的缺失资料。

6. 下一步怎么做
给卖家可执行动作，例如补 HTS、确认电池类型、准备测试资料、找报关行复核。
```

风险结果示例：

```text
CPSC 风险：高风险 / 中风险 / 低风险 / 信息不足
eFiling：可能触发 / 待 HTS 确认 / 暂未发现明显触发点 / 信息不足
证书路径：CPC / GCC / 暂不能判定 / 暂不适用
人工复核：需要 / 建议 / 暂不需要
```

## 四、适合什么时候用

适合：

- 亚马逊美国站 FBA 产品上架前合规排查
- eFiling 生效前批量筛查 ASIN 风险
- 判断产品是否可能需要 CPC 或 GCC
- 判断 Listing 是否存在儿童、电池、涂层、纺织、泡棉、小零件等 CPSC 风险点
- 给运营、采购、合规、报关行沟通前准备初筛材料

不适合：

- 作为最终法律意见
- 代替实验室检测
- 代替报关行或律师判断
- 仅凭 Listing 直接证明产品合规或豁免

## 五、首次安装

公共仓库安装方式：

```text
请从以下 GitHub 公共仓库安装跨境吴老师 CPSC/eFiling 自动初筛 Skill：
https://github.com/defway888-design/kuajing-wulaoshi-cpsc-efiling-triage-skill
```

安装完成后，关闭并重新打开 Codex，让 Skill 生效。

如果后续改为私有仓库，首次安装流程如下：

1. 登录 GitHub 并提交用户名

首次安装前，需要先将自己的 GitHub 用户名提交给跨境吴老师。

操作步骤：

```text
登录 GitHub
-> 点击右上角头像
-> 选择 Your profile
-> 进入个人主页
-> 复制浏览器地址栏中的完整网址
-> 将网址发送给跨境吴老师
```

注意：

```text
GitHub 用户名不是邮箱。
请不要发送邮箱密码、GitHub 密码或其他授权信息。
```

2. 接受私有仓库邀请

跨境吴老师收到 GitHub 用户名后，会发送私有仓库访问邀请。

用户操作：

```text
登录 GitHub
-> 打开 GitHub 发送的邀请邮件
-> 点击 View invitation
-> 点击 Accept invitation
```

如果可以看到仓库页面和文件列表，说明已经获得访问权限。

3. 在 Codex 中发出安装指令

打开 Codex，新建一个对话，输入：

```text
请从以下 GitHub 私有仓库安装跨境吴老师 CPSC/eFiling 自动初筛 Skill：
<GitHub 仓库地址>
```

按照 Codex 提示完成 GitHub 授权。

4. 重启 Codex

安装完成后，关闭并重新打开 Codex，使 Skill 生效。

## 六、常用启动语

```text
用跨境吴老师 CPSC/eFiling 初筛检查这个 ASIN：B0XXXXXXX
```

```text
帮我判断这个 ASIN 是否可能触发 CPSC eFiling：B0XXXXXXX
```

```text
用跨境吴老师标准输出 CPSC/eFiling 初筛报告，ASIN 是 B0XXXXXXX
```

```text
这个美国站 FBA 产品需要 CPC 还是 GCC？ASIN：B0XXXXXXX
```

## 七、仓库文件说明

```text
SKILL.md
Skill 主文件，包含触发条件、卖家精灵 MCP 调用流程、CPSC/eFiling 初筛逻辑和输出标准。

agents/openai.yaml
Codex Skill 展示信息，包括名称、简介、默认启动语和工具依赖。

README.md
面向用户的安装、启动和使用说明。
```

## 八、重要安全说明

- 本 Skill 只做自动初筛，不输出最终法律判定。
- HTS 编码只是 eFiling 兜底和系统标记线索，不是唯一判断依据。
- Listing 没写某项风险，不代表产品一定没有该风险。
- 涉及儿童产品、电池、软体家具、床垫、纺织品、涂层、小零件等情况时，应保留人工复核。
- 最终清关、检测和证书要求，应以 CPSC 官方规则、检测机构、报关行和专业合规意见为准。
