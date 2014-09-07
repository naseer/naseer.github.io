---
layout: post
title: "Use cscope to browse the Android source code"
date: 2010-08-10 10:02:38 -0400
comments: true
categories: [android, code, vim, cscope]
---

Cscope can be a great tool to browse the Android source code. Here is what I did to get it working with vim

* Get cscope_maps.vim from [here](http://cscope.sourceforge.net/cscope_maps.vim) and place it into your ~.vim/plugin directory. (create this dir if it does not exist)
* Add the following lines to your .vimrc

```
set nocsverb
if filereadable("cscope.out")
else
    if $ANDROID_BUILD_TOP !=""
        "This assumes you have sourced the Android build environment
        cscope add $ANDROID_BUILD_TOP/cscope.out
 else
        "Or, you can point to your android source directory in $ANDROID_DIR
        cscope add $ANDROID_DIR/cscope.out
    endif
endif
```

Use the following shell script to create the Android  cscope database. You can add/remove search paths based on relevance to you.

```

#!/bin/bash

echo "Listing Android files ..."

find "$PWD/bionic"
"$PWD/bootable"
"$PWD/build"
"$PWD/dalvik"
"$PWD/development"
"$PWD/device"
"$PWD/external"
"$PWD/frameworks"
"$PWD/hardware"
"$PWD/packages"
"$PWD/system"
"$PWD/vendor"
-name '*.java' -print -o
-name '*.aidl' -print -o
-name '*.hpp' -print -o
-name '*.cpp'  -print -o
-name '*.xml'  -print -o
-name '*.mk'  -print -o
-name '*.[chxsS]' -print > cscope.files

echo "Listing Kernel files ..."
find  kernel
-path "kernel/arch/*" -prune -o
-path "kernel/tmp*" -prune -o
-path "kernel/Documentation*" -prune -o
-path "kernel/scripts*" -prune -o
-name "*.[chxsS]" -print >> cscope.files

find "$PWD/kernel/arch/arm/include/"
"$PWD/kernel/arch/arm/kernel/"
"$PWD/kernel/arch/arm/common/"
"$PWD/kernel/arch/arm/boot/"
"$PWD/kernel/arch/arm/lib/"
"$PWD/kernel/arch/arm/mm/"
"$PWD/kernel/arch/arm/mach-msm/" -name "*.[chxsS]" -print >> cscope.files

echo "Creating cscope DB ..."
/usr/bin/cscope -b -q -k
echo "Done"
```

* Go to your Android source code directory and run the above script
* `source build/envsetup.sh` and `lunch/choosecombo` as usual
* open vim - your cscope DB should be sourced into this vim instance
* Run `:cs find f filename` to go to a file
* If you want to go to the definition of a symbol, press `Ctrl+]`
* If you want to find all references to the token under the cursor, press `Ctrl +\`  then `s`
* If you want to go to the filename under the cursor, press `Ctrl +\`  then `f`
* The other shortcuts are in the cscope_maps.vim that you downloaded above
* You can also run `:cscope help` for more help on cscope in vim
