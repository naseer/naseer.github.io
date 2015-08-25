---
layout: post
title: "C/C++ source code completion with YouCompleteMe and vim for Android and the Linux kernel"
date: 2015-04-01 12:05:29 -0400
comments: true
categories: c, c++, vim, ycm, kernel, linux, aosp, android, intellisense, emacs, youcompleteme
---
[YouCompleteMe](http://valloric.github.io/YouCompleteMe/) is currently the best clang based completion engine for vim/neovim and there is also an [emacs version](https://github.com/abingham/emacs-ycmd).

A gif is worth a thousand words -

{% img /images/blog/vim-ycm.gif 576 620 'image' 'images' %}

I've been using YCM for a while for C/C++ code completion in the Android source tree as well as the Linux kernel and it just works without any need to generate tags.
It also does syntax error checking on the fly with syntastic, which is an added bonus.
The basic idea is to rely on `.repo` as a marker to set up the necessary header paths in `ycm_extra_conf.py` for userspace and `Kbuild` as a marker for the kernel
Here is my configuration for vim. The python part of the configuration below also works with emacs.

* Install [vim](http://www.vim.org/) > version 7.04 or the latest alpha version of [neovim](http://neovim.org/). This can be easily done with [brew](http://brew.sh/) on OSX or [linuxbrew](http://brew.sh/linuxbrew/) on Linux.
* [Install YCM](http://valloric.github.io/YouCompleteMe/#installation) for your OS with the clang completer. I've only tested Linux and OSX
* Configure YCM with the following options in your `~/.vimrc`
```vim
let g:ycm_complete_in_comments_and_strings=1
let g:ycm_key_list_select_completion=['<C-n>', '<Down>']
let g:ycm_key_list_previous_completion=['<C-p>', '<Up>']
let g:ycm_autoclose_preview_window_after_completion = 1

"This assumes your kernel directory has the word 'kernel'
if getcwd() =~ "kernel"
    let g:ycm_global_ycm_extra_conf='~/ycm_extra_conf_kernel.py'
else
    let g:ycm_global_ycm_extra_conf='~/ycm_extra_conf.py'
endif
```
* Copy the contents of [this gist](https://gist.github.com/naseer/329a6ea99a8ef880770e) into `~/ycm_extra_conf.py` and that should be it for userspace code!

* To configure the kernel for code completion, copy the contents of [this gist](https://gist.github.com/naseer/6ada5a32580275e0ace9)  into `~/ycm_extra_conf_kernel.py`. This may or may not work on all kernels, I've had varying levels of success depending on the level of clang support in that kernel version, It does seem to work fine with kernel 3.10.

**UPDATE - Aug 25, 2015**: Updated this post with links to the full config files and edited the ycm configs to avoid the need for sourcing the build environment.
