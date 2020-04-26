# tmux使用方法
```
1: tmux 进入tmux窗口， 按住ctl+D或者exit退出tmux
2: ctl+b是默认的前缀键,比如ctl+b ?就会显示帮助，按下 ESC 键或q键
3: tmux new -s <session-name> 给会话起名字
4: 按下Ctrl+b d或者输入tmux detach命令，就会将当前会话与窗口分离，会话还在，会话里进程还在运行
5； tmux ls命令可以查看当前所有的 Tmux 会话
6: tmux a -t 0或者tmux attach -t <session name>可以attach到会话
7: tmux kill-session -t 0或者tmux kill-session -t <session-name>杀死会话。和exit退出tmux会话差不多
8: tmux switch -t 0切换会话
9: tmux rename-session -t 0 <new-name> 重命名会话，
10: Ctrl+b d：分离当前会话。
    Ctrl+b s：列出所有会话。
    Ctrl+b $：重命名当前会话。
11:使用tmux最简单流程：
新建会话tmux new -s my_session。
在 Tmux 窗口运行所需的程序。
按下快捷键Ctrl+b d将会话分离。
下次使用时，重新连接到会话tmux attach-session -t my_session。
12: 划分多窗口：
tmux split-window  上下
tmux split-window -h 左右
13:  tmux select-pane命令用来移动光标位置，也可以ctl+b 上下左右改变
# 光标切换到上方窗格
$ tmux select-pane -U

# 光标切换到下方窗格
$ tmux select-pane -D

# 光标切换到左边窗格
$ tmux select-pane -L

# 光标切换到右边窗格
$ tmux select-pane -R
14: tmux swap-pane命令用来交换窗格位置
# 当前窗格上移
$ tmux swap-pane -U

# 当前窗格下移
$ tmux swap-pane -D
15: 快捷键
16: tmux new-window命令用来创建新窗口，Ctrl+b c：创建一个新窗口，状态栏会显示多个窗口的信息，tmux new-window -n <window-name>指定名字
17 tmux select-window -t <window-number>切换到指定编号的窗口，Ctrl+b p：切换到上一个窗口，Ctrl+b n：切换到下一个窗口，Ctrl+b <number>：切换到指定编号的窗口
Ctrl+b w：从列表中选择窗口
18 tmux rename-window重命名窗口，Ctrl+b ,：窗口重命名

```