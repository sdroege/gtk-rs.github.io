---
layout: default
---

# Requirements

The __gtk__ crate expects __GTK+__, __GLib__ and __Cairo__ development files to be installed on your system.

## Debian and Ubuntu

~~~bash
> sudo apt-get install libgtk-3-dev
~~~

## Fedora

~~~bash
> sudo dnf install gtk3-devel glib2-devel

### Fedora 21 and earlier
> sudo yum install gtk3-devel glib2-devel
~~~

## OS X

~~~bash
> brew install gtk+3
~~~

## Windows

On Windows Rust can use either the MSVC toolchain or the GNU toolchain. The latter is easier to
install, but the former provides a better overall experience and allows interfacing with other
Windows libraries.

### MSVC toolchain

1. Install GTK using [the instructions on the GTK website](https://www.gtk.org/download/windows.php).
   This will take some time. Once it's finished, make a note of the folder `installed\x86-windows`
   (32-bit)/`installed\x64-windows` (64-bit); these instructions will refer to it as `%VCPKGDIR%`.
2. Add `%VCPKGDIR%\bin` to the beginning of your `PATH` environment variable.
3. Set the `GTK_LIB_DIR` environment variable to `%VCPKGDIR%\lib`.
4. Create the following symlinks. (NTFS has supported symlinks since Windows Vista, but prior to the
   Windows 10 Creators Update it required elevated privileges. Copying the files also works, but
   you'll need to do it again when GTK is updated.)
    * `%VCPKGDIR%\lib\gtk-3.0.lib` points to `%VCPKGDIR%\lib\gtk-3.lib`.
    * `%VCPKGDIR%\lib\gdk-3.0.lib` points to `%VCPKGDIR%\lib\gdk-3.lib`.
    * `%VCPKGDIR%\bin\gtk-3.0.dll` points to `%VCPKGDIR%\bin\gtk-3.dll`.
    * `%VCPKGDIR%\bin\gdk-3.0.dll` points to `%VCPKGDIR%\bin\gdk-3.dll`.
5. Create the folder `%VCPKGDIR%\etc\gtk-3.0`. Inside it, create the file `settings.ini`:

```ini
[Settings]
gtk-theme-name=win32
```

#### Possible problems

##### `unresolved external symbol gtk_font_chooser_level_get_type`

This is [a bug](https://github.com/gtk-rs/gtk/issues/794) in version 0.6 of gtk-rs with versions of
GTK prior to 3.22.30; either use gtk-rs from Git directly or backport [the fix](https://github.com/gtk-rs/gtk/pull/804)
into the copy of gtk-rs in your Cargo registry.

##### `process didn't exit successfully (exit code: 0xc0000139, STATUS_ENTRYPOINT_NOT_FOUND)`

`%VCPKGDIR%\bin` is too late in the `PATH` environment variable, and DLLs for other programs are being found before the ones installed alongside GTK.  Move `%VCPKGDIR%\bin` earlier in your `PATH` and try again.

### GNU toolchain

Make sure you have the [GNU ABI] version of the rust compiler installed.
Contrary to earlier instructions, **you don't need to uncheck "Linker and
platform libraries" in the Rust setup or delete `gcc.exe` and `ld.exe` in Rust's
`bin` directory.**

[GNU ABI]: https://github.com/rust-lang-nursery/rustup.rs/blob/master/README.md#working-with-rust-on-windows

### Getting the GTK+ SDK

The GTK+ Project [recommends][gtk-download] using the GTK+ SDK provided by [MSYS2]:

[gtk-download]: http://www.gtk.org/download/windows.php
[MSYS2]: http://www.msys2.org/

 *  [Install MSYS2](http://www.msys2.org/).
    We're going to assume it's installed in `C:\msys64` on 64-bit systems and `C:\msys32`
    on 32-bit ones. Adjust the paths in the following steps if necessary.

 *  In the "MSYS2 MSYS Shell" install `libgtk3`.

     -    32-bit targets:

          ~~~bash
          > pacman -S mingw-w64-i686-gtk3
          ~~~

     -    64-bit targets:

          ~~~bash
          > pacman -S mingw-w64-x86_64-gtk3
          ~~~

If you prefer getting the SDK from [gtk-win64] or building it from source, set
the variables in the next section accordingly.

[gtk-win64]: https://github.com/tschoonj/GTK-for-Windows-Runtime-Environment-Installer

### Using the native Windows shell

Back in the `cmd.exe` shell set the `GTK_LIB_DIR` and `PATH` environment
variables:

 *    32-bit targets:

      ~~~cmd
      C:\> SET GTK_LIB_DIR=C:\msys32\mingw32\lib
      C:\> SET PATH=%PATH%;C:\msys32\mingw32\bin
      ~~~

 *    64-bit targets:

      ~~~cmd
      C:\> SET GTK_LIB_DIR=C:\msys64\mingw64\lib
      C:\> SET PATH=%PATH%;C:\msys64\mingw64\bin
      ~~~

If you're happy with these changes, make them permanent with
[`SETX`](https://technet.microsoft.com/en-us/library/cc755104.aspx):

~~~cmd
C:\> SETX GTK_LIB_DIR %GTK_LIB_DIR%
C:\> SETX PATH %PATH%
~~~

### Using the MSYS2 MinGW shell

Instead of setting the environment variables manually, you can let `pkg-config`
sort the paths out for you.

 *  In the "MSYS2 MSYS Shell" install the `mingw-w64` toolchain:

     -    32-bit targets:

          ~~~bash
          > pacman -S mingw-w64-i686-toolchain
          ~~~

     -    64-bit targets:

          ~~~bash
          > pacman -S mingw-w64-x86_64-toolchain
          ~~~

 *  Start the "MSYS2 MinGW Shell" (not to be confused with "MSYS2 MSYS Shell").

### Possible problems

#### Error on linking on Windows MSYS2: undefined reference to `__imp___acrt_iob_func'

Download these packages and install it with `pacman -U <path>`:

* http://repo.msys2.org/mingw/x86_64/mingw-w64-x86_64-crt-git-5.0.0.5002.34a7c1c0-1-any.pkg.tar.xz
* http://repo.msys2.org/mingw/x86_64/mingw-w64-x86_64-headers-git-5.0.0.5002.34a7c1c0-1-any.pkg.tar.xz

Also pkg-config better ignore these packages on updating, add "IgnorePkg = mingw-w64-x86_64-crt-git mingw-w64-x86_64-headers-git" to /etc/pacman.conf 
