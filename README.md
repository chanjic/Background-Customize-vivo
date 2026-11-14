# Background-Customize-vivo
一个自定义背景的lsposed模块

折腾日记：写一个 vivo 窗口背景替换模块踩过的坑
============================================

① 主题引擎会立刻把你改的东西覆盖回去
   一开始想直接替换系统主题资源，改完不到一秒就被主题引擎重新解压覆盖。
   最后用 chattr +i 锁文件 + mount --bind 挂载才稳住，这条路花的时间最多。

② 模块死活加载不了
   报错：Xposed API classes are compiled into the module's APK。
   原因是编译时把 Xposed 的桩类一起打进了 dex，排除掉就好。

③ 设置里的 hook 全都不触发
   换了好几种入口，日志里一条回调都没有。
   最后挂在 Instrumentation.callActivityOnCreate / callActivityOnResume 上才有反应。

④ 崩在 Immutable bitmap
   缩放后的位图是只读的，直接给 Canvas 会崩，复制一份可写的就行。

⑤ 每个应用的背景类名字都不一样
   纯靠一个个 dump 出来试。设置用的类在电池里不存在，
   电池用的类在钱包里又换了个名字，混淆得七零八落。

⑥ 最坑的一个：设置写了，但等于没写
   做了深色/浅色两套数值，点下去没任何变化。
   查了很久才发现：所有绘制路径调用的判断函数里根本没去读模式，
   读的逻辑写在另一个函数里，而那个函数只有一条从不执行的路径会调它。
   等于两套数值都白写，一直在用默认值。一行调用修好。

⑦ 尺寸门槛总是差一点点
   电池“每日耗电”大卡片高 2355px，刚好超过 70% 上限；
   模式切换三个按钮宽 404px，刚好不到 30%；
   隐私页大方框 1290x1159，又刚好压线。
   这种“就差一点”出现三四次，最后改成按 id 精确处理，不再靠门槛硬套。

⑧ 信息底栏是最碎的
   SIM 选择条、输入栏、加号展开的功能菜单、最近联系人面板，
   背景挂在不同层级。改完输入栏，SIM 条还是黑的；
   改完 SIM 条，最近联系人又是黑的。来回好几轮才清干净。

⑨ 浅色模式是盲区
   我自己一直用深色模式，浅色模式适配靠反复切换去对。
   浅色的底色是白色系，和背景图叠在一起容易糊掉，
   卡片底色的框有可能直接消失或看不出边界。

（本文件由 DSH 生成，随 v6.2 安装包放在同一目录）

An lsposed module with a custom background

Toss the diary: write a vivo the window background replacement module has stepped on the pit
============================================

① The theme engine will immediately overwrite what you changed
At first, I wanted to directly replace the system theme resource, but it was decompressed and overwritten by the theme engine in less than a second.
Last use chattr +i lock File + mount --bind Mount to stabilize, this road takes the most time.

② The module cannot be loaded dead or alive
Error: Xposed API classes are compiled into the module's APK.
The reason is that when compiling, Xposed the piles were driven into dex together. Just rule it out.

③ In Settings hook none of them trigger
I changed several entrances, and there was no callback in the log.
Last hanging on Instrumentation.callActivityOnCreate/callActivityOnResume there's a reaction.

④ Collapse in Immutable bitmap
The scaled bitmap is read-only, given directly to the Canvas it will collapse. Just make a copy that can be written.

⑤ The background class name of each application is different
Just one by one dump come out and try. The class for setting does not exist in the battery,
The battery class changed its name in the wallet and got mixed up.

⑥ The worst one: the settings are written, but they are equal to not written
I made two sets of dark/light values, and there was no change when I clicked them down.
After searching for a long time, I found that all the judgment functions called by the draw path did not read the pattern at all.
The read logic is written in another function, and that function is called only by a path that is never executed.
It means that both sets of values are written in vain, and the default value is always used. One line call fix.

⑦ The size of the doorsill is always a little short
Battery "daily power consumption" large card high 2355px, just above the upper limit of 70%;
Mode switch three buttons wide 404px, just under 30%;
Privacy Page Big Box 1290x 1159, just pressing the line again.
This kind of "almost" appeared three or four times, and finally changed to press id precise handling, no more hard sleeve against the door sill.

⑧ The bottom column of information is the most broken
SIM selection bar, input field, function menu expanded by plus sign, recent contacts panel,
The background hangs at different levels. After changing the input field, SIM the bar is still black;
Finished modification SIM bar, the latest contact is black again. It took several rounds to clean it up.

⑨ Light mode is a blind spot
I've always used dark mode myself, and light mode adaptation depends on switching repeatedly.
The light background color is white, and it can easily be pasted together with the background image.
The frame of the card background may disappear directly or the border may not be visible.

(This document is issued by DSH generate, with v 6.2 installation packages in the same directory)
