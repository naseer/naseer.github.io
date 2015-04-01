---
layout: post
title: "Android C/C++ source code completion with YouCompleteMe and vim"
date: 2015-04-01 12:05:29 -0400
comments: true
categories: c, c++, vim, ycm, kernel, linux, aosp, android, intellisense, emacs, youcompleteme
---
[YouCompleteMe](http://valloric.github.io/YouCompleteMe/) is currently the best clang based completion engine for vim/neovim and there is also an [emacs version](https://github.com/abingham/emacs-ycmd).

A gif is worth a thousand words -

{% img /images/blog/vim-ycm.gif 576 620 'image' 'images' %}

I've been using YCM for a while for C/C++ code completion in the Android source tree as well as the Linux kernel and it just works without any need to generate tags.
The basic idea is relying on `$ANDROID_BUILD_TOP` to set up the necessary header paths in `ycm_extra_conf.py`
Here is my configuration for vim. The python part of the configuration below also works with emacs.

* Install [vim](http://www.vim.org/) > version 7.04 or the latest alpha version of [neovim](http://neovim.org/). This can be easily done with [brew](http://brew.sh/) on OSX or [linuxbrew](http://brew.sh/linuxbrew/) on Linux.
* [Install YCM](http://valloric.github.io/YouCompleteMe/#installation) for your OS with the clang completer. I've only tested Linux and OSX
* Configure YCM with the following options in your `~/.vimrc`
```vim
let g:ycm_complete_in_comments_and_strings=1
let g:ycm_key_list_select_completion=['<C-n>', '<Down>']
let g:ycm_key_list_previous_completion=['<C-p>', '<Up>']
let g:ycm_autoclose_preview_window_after_completion = 1

if !empty($ANDROID_BUILD_TOP)
    if getcwd() =~ "kernel"
        let g:ycm_global_ycm_extra_conf='~/ycm_extra_conf_kernel.py'
    else
        let g:ycm_global_ycm_extra_conf='~/ycm_extra_conf.py'
    endif
endif
```
* Copy the contents of [this `ycm_extra_conf.py`](https://github.com/Valloric/ycmd/blob/master/examples/.ycm_extra_conf.py) into `~/ycm_extra_conf.py`
* Edit `~/.ycm_extra_conf.py` to add Android build flags and the Android source code paths you need to work on

```python
flags = [
'-Wall',
'-Wextra',
'-Wc++98-compat',
'-Wno-long-long',
'-Wno-variadic-macros',
'-fexceptions',
'-fno-rtti',
'-fno-strict-aliasing',
'-fPIE',
'-fpic',
'-DNDEBUG',
'-DANDROID',
'-DUSE_CLANG_COMPLETER',
'-std=c++11',
'-x', 'c++',
'-I', '.'
]

if (os.getenv('ANDROID_BUILD_TOP')):
    android_build_top = os.getenv('ANDROID_BUILD_TOP');
    out_dir =  os.getenv('OUT')
    androidflags = [
        '-isystem', os.path.join(android_build_top, 'system/core/include'),
        '-isystem', os.path.join(android_build_top, 'hardware/libhardware/include'),
        '-isystem', os.path.join(android_build_top, 'hardware/libhardware_legacy/include'),
        '-isystem', os.path.join(android_build_top, 'hardware/ril/include'),
        '-isystem', os.path.join(android_build_top, 'libnativehelper/include'),
        '-isystem', os.path.join(android_build_top, 'bionic/libc/arch-arm/include'),
        '-isystem', os.path.join(android_build_top, 'bionic/libc/include'),
        '-isystem', os.path.join(android_build_top, 'bionic/libc/kernel/common'),
        '-isystem', os.path.join(android_build_top, 'bionic/libc/kernel/arch-arm'),
        '-isystem', os.path.join(android_build_top, 'bionic/libstdc++/include'),
        '-isystem', os.path.join(android_build_top, 'bionic/libstdc++/include'),
        '-isystem', os.path.join(android_build_top, 'bionic/libm/include'),
        '-isystem', os.path.join(android_build_top, 'bionic/libm/include/arm'),
        '-isystem', os.path.join(android_build_top, 'bionic/libthread_db/include/'),
        '-isystem', os.path.join(android_build_top, 'frameworks/native/include'),
        '-isystem', os.path.join(android_build_top, 'frameworks/native/opengl/include'),
        '-isystem', os.path.join(android_build_top, 'frameworks/av/include'),
        '-isystem', os.path.join(android_build_top, 'frameworks/base/include'),
        #You can add the paths to the HAL or other native code you're working on here
        '-I', os.path.join(android_build_top, 'hardware/qcom/display/libgralloc'),
        '-I', os.path.join(android_build_top, 'hardware/qcom/display/libhwcomposer'),
    ]
    flags = flags + androidflags
```
* And that should be it for userspace code! If you have successfully made it this far and also want to configure the kernel for code completion, create a `~/ycm_extra_conf_kernel.py` from the same example file above and use the following flags.

```python
flags = [
'-Wall',
'-Wextra',
'-Wc++98-compat',
'-D__KERNEL__',
'-nostdinc',
'-DUSE_CLANG_COMPLETER',
'-std=c99',
'-x', 'c',
'-I', '.'
]

#NOTE: My kernel root is the same as $ANDROID_BUILD_TOP.
#If yours is different, you need to set the right variable here.

if (os.getenv('ANDROID_BUILD_TOP')):
    kernel_root = os.getenv('ANDROID_BUILD_TOP')
    kernelflags = [
    '-isystem', os.path.join(kernel_root, '/kernel/include'),
    '-isystem', os.path.join(kernel_root, '/kernel/include/linux'),
    '-isystem', os.path.join(kernel_root, '/kernel/include/asm-generic'),
    '-isystem', os.path.join(kernel_root, '/kernel/arch/arm/include'),
    '-isystem', os.path.join(kernel_root, '/kernel/arch/arm64/include'),
    ]
    flags = flags + kernelflags
```
* This may or may not work on all kernels, I've had varying levels of success depending on the level of clang support in that kernel version, It does seem to work fine with kernel 3.10.
