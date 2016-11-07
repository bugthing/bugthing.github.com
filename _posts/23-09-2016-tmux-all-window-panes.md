---
layout: default
categories: blog
tags: tmux cli 
title: Tmux commands in all windows and all panes
---

Before I exit a tmux session I like to close down all the stuff, this usally means entering each pane of each window hitting Ctl-c a few times and typing 'exit' alot..  I can do that using bash like so:

```
function aktx {
  default="C-c C-c C-c exit Enter"
  current_win=${session}:0
  for _win in $(tmux list-windows -F '#I'); do
    tmux select-window -t "$_win"
    for _pane in $(tmux list-panes -F '#P'); do
      tmux send-keys -t ${_pane} ${@-$default}
    done
  done
  tmux select-window -t "$current_win"
}

```

aktx - stands for  *A*ll*K*ey*T*m*X*
