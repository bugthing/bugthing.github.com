---
layout: default
tags: vim ruby rspec
title: vim-plugin-not-required
---
When using vim and working within Rails project I found it a PITA to manually open the corrosponding
rspec file or app file in the split layout I wanted.

I knew plugins like vim-rails, rspec.vim can do stuff simlar to what I wanted (and lot more), but they
did not do exactly what I wanted and often needed extra stuff to get them working.

So I thought I'd try writing a function to do what I wanted. Ended up being very simple:

```
function! SpecCorresponding()
  let l:efile = @%
  if match(expand(l:efile), "_spec.rb$") != -1
    let l:cfile = substitute(l:efile, "_spec.rb$", ".rb", "s")
    let l:cfile = substitute(l:cfile, "spec/", "app/", "s")
    execute("rightbelow vsplit " . l:cfile)
  else
    let l:cfile = substitute(l:efile, ".rb$", "_spec.rb", "s")
    let l:cfile = substitute(l:cfile, "app/", "spec/", "s")
    execute("leftabove vsplit " . l:cfile)
  endif
endfunction

map <leader>r :call SpecCorresponding()<CR>
```

The above function inspecs the current file (l:efile) to see if it ends in '_spec.rb'. If it does I
create a new var replacing '_spec.rb' with '.rb', then replace 'spec/' with 'app/' then open that
file in a right hand virtical spit. Otherwise I effectively do the opersite.

Could probably be worked on further but seems to do what I want for now.


