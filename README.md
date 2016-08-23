## 编译问题
mupdf默认使用的是X11窗口，在Mac OS X下X11窗口不支持Fullscreen，也就意味着Splitview不能使用，但glfw可以用原生的Cocoa窗口，
所以编译的时候一定要设置HAVE_GLFW开关。

由于Makefile的问题，自动编译glfw会使得它包含X11，建议先进入glfw目录下执行`cmake`和`make`，再返回主目录执行下面的make命令：

    make HAVE_GLFW=yes HAVE_X11=yes HAVE_CURL=no verbose=yes GLFW_LIBS="-framework Cocoa -framework OpenGL -framework IOKit -framework CoreVideo"
    
这里又可以看到Makefile的一个小问题，如果HAVE_X11设置为no，那platform/gl下面的几个文件都不会编译，
所以这里也设置打开。如果一切正常的话，在主目录下会产生一个build/release目录，有两个文件：

	mupdf-x11
	mupdf-gl
	
mupdf-x11还是用XQuartz来执行，这个可以删除，mupdf-gl就是我们最终的目标程序。

## mupdf-gl中的修改

1. gl-main.c: update_title 中没有用utf-8处理文件名，在文件名包含汉字且超过15个的时候，程序会crash
2. 命令行的跳转到指定<page>功能没有实现，但是help中却显示有这个功能？！ 好吧，现在我帮他们实现了
3. 其他一些细节问题，例如h,j,k,l的滚屏也没有实现，我加上去了

## 使用方法

打开filename.pdf的文件，并定位到82页

	mupdf-gl filename.pdf 82
	
* +,= 放大
* - 缩小
* w 适合宽度
* H 适合高度
* h,j,k,l 移动
* d,u,b,<space> 翻页
* v 反转颜色(晚上用不错)
* q 退出
* ……

## 使用效果

### 用`o`打开和关闭书签栏

![Outline](http://ww3.sinaimg.cn/mw690/3e37e59cgw1f73rgqu058j20r40l8qdi.jpg)

### 用`[`和`]`进行左右旋转微调，再也不担心倾斜问题了

![before rotate](http://ww2.sinaimg.cn/mw690/3e37e59cgw1f73rgm7xudj20si0kudj0.jpg)
![after rotate](http://ww3.sinaimg.cn/mw690/3e37e59cgw1f73rgkit87j20si0kujur.jpg)

### 用`v`反色，夜间减少屏幕亮度刺眼

![no invert](http://ww3.sinaimg.cn/mw690/3e37e59cgw1f73rggc44ij20j70kuwk9.jpg)
![invert](http://ww1.sinaimg.cn/mw690/3e37e59cgw1f73rgj32akj20j70ku7a2.jpg)


---

ABOUT

MuPDF is a lightweight PDF, XPS, EPUB and CBZ viewer and parser/rendering
library.

The renderer in MuPDF is tailored for high quality anti-aliased graphics. It
renders text with metrics and spacing accurate to within fractions of a pixel
for the highest fidelity in reproducing the look of a printed page on screen.

MuPDF is also small, fast, and yet complete. We support PDF 1.7 with
transparency, encryption, hyperlinks, annotations, search and many other bells
and whistles. MuPDF can also read XPS documents (OpenXPS / ECMA-388),
EPUB and CBZ (Comic Book archive) files.

MuPDF is written to be both modular and portable; the example applications
are merely thin layers on top of the functionality offered by the library,
so custom viewers can be easily built for a wide range of platforms. Example
viewer applications are supplied for Windows, Linux, MacOS, iOS and Android.

MuPDF is deliberately designed to be threading library agnostic, while still
supporting multi-threaded operation. In the absence of a thread library
it will run single-threaded, but by adding one significant benefits in
rendering speed on multi-core platforms can be obtained.

Interactive features such as form filling, javascript and transitions
are in development and partially supported by the Android application.

LICENSE

MuPDF is Copyright 2006-2015 Artifex Software, Inc.

This program is free software: you can redistribute it and/or modify it under
the terms of the GNU Affero General Public License as published by the Free
Software Foundation, either version 3 of the License, or (at your option) any
later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU Affero General Public License along
with this program. If not, see <http://www.gnu.org/licenses/>.

For commercial licensing, including our "Indie Dev" friendly options,
please contact sales@artifex.com.

COMPILING

If you are compiling from source you will need several third party libraries:
freetype2, jbig2dec, libjpeg, openjpeg, and zlib. These libraries are contained
in the source archive. If you are using git, they are included as git
submodules.

You will also need the X11 headers and libraries if you're building on Linux.
These can typically be found in the xorg-dev package. Alternatively, if you
only want the command line tools, you can build with HAVE_X11=no.

The new OpenGL-based viewer also needs OpenGL headers and libraries. If you're
building on Linux, install the mesa-common-dev and libgl1-mesa-dev packages.
You'll also need several X11 development packages: xorg-dev, libxcursor-dev,
libxrandr-dev, and libxinerama-dev. To skip building the OpenGL viewer, build
with HAVE_GLFW=no.

DOWNLOAD

The latest development source is available directly from the git repository:

	git clone http://mupdf.com/repos/mupdf.git

In the mupdf directory, update the third party libraries:

	git submodule update --init

INSTALLING (UNIX)

Typing "make prefix=/usr/local install" will install the binaries, man-pages,
static libraries and header files on your system.

REPORTING BUGS AND PROBLEMS

The MuPDF developers hang out on IRC in the #ghostscript channel on
irc.freenode.net.

Report bugs on the ghostscript bugzilla, with MuPDF as the selected component.

	http://bugs.ghostscript.com/

If you are reporting a problem with PDF parsing, please include the problematic
file as an attachment.
