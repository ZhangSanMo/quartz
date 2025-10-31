## 🎯 一、什么是 `Options`？

当你用 `MyGui.Add("ControlType", Options, Text)` 创建一个控件（比如按钮、文本框）时，**`Options` 就是用来控制这个控件外观、位置、行为等属性的字符串**。

它就像你给控件“下命令”：

> “你宽度100，高度50，放在坐标(10,20)，文字右对齐，一开始是隐藏的…”

这些命令都写在 `Options` 字符串里，**多个命令用空格或制表符分隔**。

---

## 🧭 二、Options 的三大类功能

`Options` 主要控制三个方面：

1. **📍 位置和大小**（Positioning and Sizing）
2. **🎨 外观和样式**（Common/Uncommon Styles）
3. **💾 数据和交互**（Storing User Input）

我们一个一个讲，从最常用、最重要的开始！

---

## 📏 三、位置与大小控制（最重要！新手必学）

这是新手最容易困惑的部分，也是 GUI 布局的核心！

### ✅ 基础坐标系统：

- 窗口左上角是 `(0, 0)`
- `X` = 水平位置（从左往右）
- `Y` = 垂直位置（从上往下）
- 单位是**像素**

---

### 1️⃣ 绝对定位（新手推荐先掌握这个）

```ahk
MyGui.Add("Text", "x100 y50 w200 h30", "Hello World")
```

- `x100` → 控件左边缘距离窗口左边 100 像素
- `y50` → 控件上边缘距离窗口顶部 50 像素
- `w200` → 宽度 200 像素
- `h30` → 高度 30 像素

> 💡 小技巧：你可以只写 `x100`，不写 `y`，系统会自动帮你找一个合适的 `y`（通常是上一个控件下方 + 间距）

---

### 2️⃣ 相对定位（高手布局神器）

这是 AutoHotkey GUI 最强大的功能之一 —— **自动布局**，不用手动计算像素！

#### ➤ 相对于上一个控件：

```ahk
MyGui.Add("Edit", "w100")             ; 第一个控件
MyGui.Add("Button", "x+10 yp", "OK")  ; x+10 = 上一个控件右边 +10像素；yp = 和上一个控件顶部对齐
```

常见写法：

| 写法       | 含义                                      |
|------------|-------------------------------------------|
| `x+10`     | 在上一个控件**右边** +10 像素             |
| `y+20`     | 在上一个控件**下边** +20 像素             |
| `xp`       | X 位置 = 上一个控件的 X 位置（左对齐）    |
| `yp`       | Y 位置 = 上一个控件的 Y 位置（顶对齐）    |
| `wp`       | 宽度 = 上一个控件的宽度                   |
| `hp`       | 高度 = 上一个控件的高度                   |
| `x+m`      | X = 上一个控件右边 + 标准间距（margin）   |
| `y+m`      | Y = 上一个控件下边 + 标准间距             |

> ✅ `m` = margin，系统默认间距（通常是 5~10 像素，取决于字体）

#### ➤ 调整值：

```ahk
wp-20   ; 宽度 = 上一个控件宽度 - 20
x+-10   ; X = 上一个控件右边 -10（注意：负数要写 + -10）
```

---

### 3️⃣ 分区定位（Section —— 制作表单布局神器）

当你想做“两列布局”或“分组布局”，用 `Section` + `xs` / `ys`

```ahk
MyGui := Gui()
MyGui.Add("Text", "Section", "姓名：")  ; ← 这里用了 Section！
MyGui.Add("Edit", "w150")
MyGui.Add("Text", "ys", "年龄：")       ; ← ys = 回到上一个 Section 的 Y 位置（即和“姓名”同一行）
MyGui.Add("Edit", "w50")
MyGui.Show()
```

效果：

```
姓名： [_______________]
年龄： [____]
```

- `Section` → 标记当前位置为“新分区起点”
- `xs` → 回到上一个 Section 的 X 位置（通常用于换列）
- `ys` → 回到上一个 Section 的 Y 位置（通常用于换行或对齐）

> ✅ 你可以在一个控件上同时写 `"Section xs"` —— 表示“用上一个分区的位置，然后自己成为新分区”

---

### 4️⃣ 用 `R`（行数）代替 `H`（高度）—— 推荐！

为什么？因为字体大小可能变，用“行数”更灵活！

```ahk
MyGui.Add("Edit", "r3 w200")  ; 高度 = 3 行文字的高度
```

- `r2.5` 也可以！支持小数
- 比如 `r3` 通常 ≈ `h45`（取决于字体），但会自动适应

> 📌 优先用 `R`，除非你需要精确像素控制

---

## 🎨 四、外观与样式控制（美化控件）

### 1️⃣ 对齐方式

```ahk
MyGui.Add("Text", "Center", "居中的文字")
MyGui.Add("Edit", "Right", "右对齐输入框")
```

支持的控件：Text, Edit, Button, CheckBox, Radio, Slider, GroupBox, DateTime, Link

- `Left`（默认）
- `Center`
- `Right`（对 CheckBox/Radio，还会把勾选框放到右边！）

---

### 2️⃣ 颜色控制

```ahk
MyGui.Add("Text", "cRed", "红色文字")
MyGui.Add("Text", "c00FF00 BackgroundYellow", "绿字黄底")
```

- `c...` → 文字颜色（`cRed`, `cFF0000`, `c0xFF0000`）
- `Background...` → 背景色（`BackgroundBlue`, `BackgroundFFDD99`）
- `BackgroundDefault` → 恢复系统默认背景色

> ⚠️ 不是所有控件都支持背景色！Text、Picture、GroupBox、CheckBox、Radio、Slider、Tab、Link 支持

---

### 3️⃣ 可见性与可用性

```ahk
MyGui.Add("Button", "Hidden", "看不见的按钮")
MyGui.Add("Edit", "Disabled", "禁用的输入框")
```

- `Hidden` → 初始隐藏（用 `GuiControl.Visible := true` 显示）
- `Disabled` → 初始禁用（用 `GuiControl.Enabled := true` 启用）
- 可加数字：`Hidden1` = 隐藏，`Hidden0` = 显示（用于变量控制）

> 🔸 Edit 控件如需“只读”，用 `ReadOnly`，不是 `Disabled`

---

### 4️⃣ 其他常用样式

```ahk
MyGui.Add("Edit", "VScroll HScroll", "带滚动条的编辑框")
MyGui.Add("Text", "Wrap", "自动换行的文字")  ; 默认已开启，关闭用 -Wrap
MyGui.Add("Edit", "-Tabstop", "按 Tab 键会跳过这个控件")
```

- `VScroll` / `HScroll` → 显示垂直/水平滚动条
- `Wrap` → 自动换行（Text、Edit 默认开启）
- `-Tabstop` → 禁用 Tab 导航（前面加减号表示“移除”某个属性）

---

## 💾 五、数据交互（给控件起名字！）

最重要的一个选项：**`v`**

```ahk
MyGui.Add("Edit", "vUserName w200", "默认文字")
```

- `vUserName` → 给这个控件命名为 `UserName`
- 之后你可以用 `Gui.Submit()` 获取值，或用 `Gui["UserName"].Value` 读写

> ✅ 所有需要用户输入或你之后要操作的控件，**一定要加 `v名字`！**

---

## 🧩 六、特殊技巧与注意事项

### ✅ 选项顺序不重要！

```ahk
"x100 y50 w200 cBlue vInput"
```

和

```ahk
"vInput cBlue w200 y50 x100"
```

完全一样！

---

### ✅ 多个选项用空格分隔

```ahk
"xp y+10 w150 r2 Center cGreen vMyEdit"
```

---

### ✅ 用变量动态控制

```ahk
width := 200
isVisible := 1
MyGui.Add("Text", "w" width " Hidden" isVisible, "动态文本")
```

---

### ⚠️ 负数写法要加 `+`

❌ `x-10` → 被解析为“绝对定位 x=-10”

✅ `x+-10` → “相对上一个控件右边 -10 像素”

---

### ⚠️ 某些控件有默认尺寸

- Edit、DDL、ComboBox 默认宽 = 15 × 字体大小
- GroupBox 默认宽 = 18 × 字体大小（内部留空）
- 没写 `w`/`h` 时，系统会智能计算！

---

## 🧪 七、实战例子（新手模仿练习）

### 🎯 例子1：简单表单

```ahk
MyGui := Gui("", "用户注册")
MyGui.Add("Text", "Section", "用户名：")
MyGui.Add("Edit", "vName w150")
MyGui.Add("Text", "ys", "密码：")     ; ys = 和“用户名”同一行高度
MyGui.Add("Edit", "vPass w150 Password")  ; Password = 隐藏输入
MyGui.Add("Button", "x+m y+20", "提交")   ; x+m = 换行后靠左，y+20 = 和上一个控件间隔20
MyGui.Show()
```

### 🎯 例子2：两列布局

```ahk
MyGui := Gui()
MyGui.Add("Text", "Section", "字段1：")
MyGui.Add("Edit", "vF1 w100")
MyGui.Add("Text", "ys Section", "字段2：")  ; Section = 开始新列
MyGui.Add("Edit", "vF2 w100")
MyGui.Add("Text", "ys Section", "字段3：")
MyGui.Add("Edit", "vF3 w100")
MyGui.Show()
```

效果：

```
字段1： [____]    字段2： [____]    字段3： [____]
```

---

## 📘 八、学习建议（新手路线图）

1. **先掌握绝对定位** `x`, `y`, `w`, `h`
2. **再学相对定位** `x+`, `y+`, `xp`, `yp`, `wp`, `hp`
3. **然后学分区** `Section`, `xs`, `ys` —— 做表单布局
4. **给控件命名** `v名字` —— 后续交互必备
5. **最后学样式** `Center`, `cRed`, `Background...`, `Disabled`, `Hidden`

---

## ❓ 九、常见问题解答（FAQ）

### Q：不写 `x` 和 `y` 会怎样？
A：控件会自动放在**上一个控件下方 + 标准间距**。适合垂直排列。

### Q：怎么让两个控件在同一行？
A：第一个控件写 `Section`，第二个控件写 `ys`（同一高度），`x+m`（右边间距）

### Q：`r3` 和 `h45` 有什么区别？
A：`r3` 是 3 行文字高度，会随字体缩放；`h45` 是固定 45 像素，不随字体变化。

### Q：怎么调试位置？
A：先用绝对定位写死，布局完成后再改成相对定位，更灵活！

---

## ✅ 总结卡片（打印/收藏）

| 类别       | 常用选项                          | 说明                         |
|------------|-----------------------------------|------------------------------|
| 位置       | `x100 y50`                        | 绝对定位                     |
|            | `x+10 y+20`                       | 相对上一个控件               |
|            | `xp yp wp hp`                     | 复制上一个控件位置/尺寸      |
|            | `x+m y+m`                         | 加上默认间距                 |
| 分区       | `Section`                         | 标记分区起点                 |
|            | `xs ys`                           | 回到上一个分区位置           |
| 尺寸       | `w200 h30`                        | 宽高（像素）                 |
|            | `r2.5`                            | 行数（推荐代替 h）           |
| 样式       | `Center Right Left`               | 文字对齐                     |
|            | `cRed BackgroundBlue`             | 颜色                         |
|            | `Disabled Hidden ReadOnly`        | 状态控制                     |
|            | `VScroll HScroll`                 | 滚动条                       |
| 数据       | `vMyName`                         | 命名控件（最重要！）         |
