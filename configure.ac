AC_INIT([Limine Bare Bones], [m4_esyscmd([./version.sh])], [https://github.com/mintsuki/limine-barebones-autotools/issues], [limine-barebones])

AC_PREREQ([2.69])

AC_CONFIG_AUX_DIR([build-aux])

AC_DEFUN([PROG_ABSPATH], [
    case "$$1" in
        .*) $1="$(cd "$(dirname "$$1")" && pwd -P)/$(basename "$$1")" ;;
    esac
])

SRCDIR="$(cd "$srcdir" && pwd -P)"
BUILDDIR="$(pwd -P)"

AC_SUBST([SRCDIR])
AC_SUBST([BUILDDIR])

werror_state="no"
AC_ARG_ENABLE([werror],
    [AS_HELP_STRING([--enable-werror], [treat warnings as errors])],
    [werror_state="$enableval"])

m4_define([DEFAULT_ARCH], [x86_64])
AC_ARG_VAR([ARCH], [target architecture @<:@default: ]DEFAULT_ARCH[@:>@])
test "x$ARCH" = "x" && ARCH="DEFAULT_ARCH"

AC_PROG_MKDIR_P
PROG_ABSPATH([MKDIR_P])
AC_PROG_INSTALL
PROG_ABSPATH([INSTALL])

AC_CHECK_PROG([FIND_FOUND], [find], [yes])
if ! test "x$FIND_FOUND" = "xyes"; then
    AC_MSG_ERROR([find not found, please install find before configuring])
fi

#if test "x$ARCH" = "xx86_64"; then
#    AC_CHECK_PROG([NASM_FOUND], [nasm], [yes])
#    if ! test "x$NASM_FOUND" = "xyes"; then
#        AC_MSG_ERROR([nasm not found, please install nasm before configuring])
#    fi
#fi

AC_ARG_VAR([CROSS_TOOLCHAIN], [alternative cross toolchain prefix (or 'llvm')])
AC_ARG_VAR([CROSS_CC], [cross C compiler command])
AC_ARG_VAR([CROSS_LD], [cross linker command])
AC_ARG_VAR([CROSS_STRIP], [cross strip command])

rm -rf "$BUILDDIR/cross-files"
$MKDIR_P "$BUILDDIR/cross-files"
ARCHITECTURE="$ARCH" WANT_CROSS_CC=yes WANT_CROSS_LD=yes WANT_CROSS_STRIP=yes "$SRCDIR/freestanding-toolchain" >"$BUILDDIR/cross-files/$ARCH-toolchain.mk"

AC_SUBST([CROSS_FILE], [cross-files/$ARCH-toolchain.mk])

m4_define([DEFAULT_CROSS_CFLAGS], [-g -O2 -pipe -Wall -Wextra])
AC_ARG_VAR([CROSS_CFLAGS], [cross C flags @<:@default: ]DEFAULT_CROSS_CFLAGS[@:>@])
test "x$CROSS_CFLAGS" = "x" && CROSS_CFLAGS="DEFAULT_CROSS_CFLAGS"
if test "$werror_state" = "yes"; then
    CROSS_CFLAGS="$CROSS_CFLAGS -Werror"
fi

m4_define([DEFAULT_CROSS_CPPFLAGS], [])
AC_ARG_VAR([CROSS_CPPFLAGS], [cross C preprocessor flags @<:@default: ]DEFAULT_CROSS_CPPFLAGS[@:>@])
test "x$CROSS_CPPFLAGS" = "x" && CROSS_CPPFLAGS="DEFAULT_CROSS_CPPFLAGS"

#m4_define([DEFAULT_CROSS_NASMFLAGS], [-F dwarf -g])
#AC_ARG_VAR([CROSS_NASMFLAGS], [cross nasm flags @<:@default: ]DEFAULT_CROSS_NASMFLAGS[@:>@])
#test "x$CROSS_NASMFLAGS" = "x" && CROSS_NASMFLAGS="DEFAULT_CROSS_NASMFLAGS"
#if test "$werror_state" = "yes"; then
#    CROSS_NASMFLAGS="$CROSS_NASMFLAGS -Werror"
#fi

m4_define([DEFAULT_CROSS_LDFLAGS], [])
AC_ARG_VAR([CROSS_LDFLAGS], [cross linker flags @<:@default: ]DEFAULT_CROSS_LDFLAGS[@:>@])
test "x$CROSS_LDFLAGS" = "x" && CROSS_LDFLAGS="DEFAULT_CROSS_LDFLAGS"

AC_PREFIX_DEFAULT([/usr/local])

AC_CONFIG_FILES([GNUmakefile])
AC_OUTPUT
