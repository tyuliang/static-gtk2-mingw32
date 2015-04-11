I did try to release such a patch,youknow, working under msys is painful, it is much slower than that under linux
> ,until now I could't find a way to make my work into a patch file.

So, I can only tell you how I do.

Tools:
perl,gcc3/gcc4,msys,mingw32



---


1. Delete DllMain() function in gdk\_pixbuf,pango,gdk.

2. Convert all dllexport and  dllimport to extern. (May be not necessary when build a dll)

3. Change theme: In gtkrc.c, use MS-Windows theme to replace the parameter of function gtk\_rc\_parse\_string().

4. header file change for msvc:
> (1) add MACRO inline to gdkconfig.h

> (2) for  lib/glib-2.0/include/glibconfig.h,
> > `#`if _MSc\_VER < 1400
      1. ndef G\_HAVE\_ISO\_VARARGS
      1. ndef G\_HAVE\_GNUC\_VARARGS
> > `#`endif_

Just complier. make install, then you can get a lot of libxxx.a, use ar x to upack them,
you should rename  printf.o and localcharset.o  of libintl.a, because same name appears in gnu glib.(func different).
delete win\_iconv.o(conflict with glib), delete thai.o from pango (conflict with gtk). delete autoasprintf.o(depends on c++).
suppose you put all .o file into a same dir, then:


> gcc `*`.o -shared -o libgtk-mono.dll -Wl,-s -Wl,-export-all-symbols -Wl,-output-def,libgtk-mono.def,--out-implib,libgtk-mono.dll.a  -lcomdlg32 -lwinspool -lcomctl32 -limm32 -lshell32 -luuid -lshlwapi -ldnsapi  -lusp10  -lmsimg32 -lgdi32  -lws2\_32 -lole32

Now you get libgt-mono.dll.
Before you generate the import lib for msvc, you should first delete all  DATA in libgtk-mono.def