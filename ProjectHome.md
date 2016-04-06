Bindings for Qt 4 (http://qtsoftware.com) for .NET languages.

Please feel free to download the latest version, drop me a line, ask questions, report issues or contribute with the project in any way.

### News: ###
  * July 14th 2010: New version 4.6.3 uploaded.
  * Oct 21st 2009: New version 4.5.2 uploaded. Compiled with ikvm 0.40.0.1
  * Jun 25th 2009: New version 4.5.0. Works for .NET 2.0 and Mono 2.4.2 or greater. (Thanks to Zoltan Varga for fixing MONO so ikvm will work without modifications) Changed license to LGPL.
  * Feb 28th 2009: Created packages for MONO, .NET 2.0 Win32 and Win64. The MONO package contains a modified IKVM so it won't crash. That makes the library slower (uses more CPU). We are still working to find a solution for that problem. The .NET version doesn't have that problem. It needs the ikvm-native.dll for 32 or 64 bits according to the arch of your Windows. Please make sure you copy ikvm-native.dll next to your executable or on your System32 folder. IKVM on .NET hasn't been modified. Comes with version 0.38.0.4 of that library.
  * Feb 24th 2009: Created discussion group.
  * Feb 23rd 2009: Initial MONO support. Now all the examples should work on any platform using Mono.
  * Feb 5th 2009: Initial release. Most examples work with .NET 2.0. Mono crashes with almost any example.

### Usage: ###

  * Download MONO 2.4.2 or greater if you are using Linux or Mac OS X (http://mono-project.com). For Windows you need MONO 2.4.2 or .NET 2.0 or greater. All the examples in this site are known to work with MONO 2.4.2 RC1 or .NET 2.0.
  * Download qtjambi-4.6.3.zip from this site.
  * Download Qt Jambi (Qt for Java version 4.6.3) from here: http://sourceforge.net/projects/qtjambi/files/ or install it using your distribution's package management system (if your are using Linux). This contains the shared libraries (Qt and Qt Jambi) required by all Qt applications.
  * Reference from your application the following libraries:
    * qtjambi-4.6.3\_01.dll
    * IKVM.OpenJDK.Core.dll
    * IKVM.Runtime.dll
    * IKVM.Runtime.JNI.dll
  * Copy the file ikvm-native.dll (there is a 64bit version of this file under the 64 bit folder) next to your executable or to your System32 folder if you are using .NET 2.0 (not required for MONO since it comes with it)
  * From your\_qtjambi\_dir/lib, copy all the dlls of the form com\_trolltech\_qt**_.dll that you need. For example if you don't need phonon, don't copy the phonon one. Then from your\_qt\_dir/bin, copy all the dlls of the form Qt\*4.dll that you need. All these dlls should go next to your application's executable or in a folder where your system can find
  * Start coding!!_

### Examples ###**

Please check the [WIKI](http://code.google.com/p/qt4dotnet/w/list) for examples.

### Why this project? ###

For many reasons:

  1. This project is derived from Qt Jambi. Qt Jambi is officially supported by Nokia (the makers of Qt). This project is just reusing their work. All the features supported by Qt Jambi are supported by this project.
  1. There is no need to constantly update the bindings. After Qt Jambi releases a new version, we apply a patch and compile it with ikvm. No need to write 1 line of code to upgrade to the latest Qt version.
  1. Works great on 3 platforms with no extra effort: Windows, Mac and Linux.

### Known problems / missing features ###

  1. No Designer to C# tool yet. We are working on it.
  1. Lack of custom signals. Use .NET Events instead (We are working on a solution to migrate from signal/slots to .NET events.) Please check the qt4dotnetlib.zip file under the download section.