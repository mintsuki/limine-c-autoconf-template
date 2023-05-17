AC_INIT([Limine Bare Bones], [m4_esyscmd([./version.sh])], [https://github.com/mintsuki/limine-barebones-autotools/issues], [limine-barebones])

AC_PREREQ([2.69])

AC_CONFIG_AUX_DIR([build-aux])

SRCDIR="$(cd "$srcdir" && pwd -P)"
BUILDDIR="$(pwd -P)"

AC_SUBST([SRCDIR])
AC_SUBST([BUILDDIR])

werror_state="no"
AC_ARG_ENABLE([werror],
    [AS_HELP_STRING([--enable-werror], [treat warnings as errors])],
    [werror_state="$enableval"])
if test "$werror_state" = "yes"; then
    AC_SUBST([WERROR_FLAG], [-Werror])
else
    AC_SUBST([WERROR_FLAG], [-Wno-error])
fi

m4_define([DEFAULT_ARCH], [x86_64])
AC_ARG_VAR([ARCH], [target architecture @<:@default: ]DEFAULT_ARCH[@:>@])
test "x$ARCH" = "x" && ARCH="DEFAULT_ARCH"

AC_DEFUN([PROG_ABSPATH], [
    case "$$1" in
        .*) $1="$(cd "$(dirname "$$1")" && pwd -P)/$(basename "$$1")" ;;
    esac
])

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

AC_ARG_VAR([TOOLCHAIN], [alternative toolchain prefix (or 'llvm', or 'gnu')])
AC_ARG_VAR([CC], [C compiler command])
AC_ARG_VAR([LD], [linker command])
AC_ARG_VAR([STRIP], [strip command])

rm -rf "$BUILDDIR/toolchain-files"
$MKDIR_P "$BUILDDIR/toolchain-files"
ARCHITECTURE="$ARCH" \
    FREESTANDING_TOOLCHAIN="$TOOLCHAIN" \
    WANT_FREESTANDING_CC=yes \
    FREESTANDING_CC="$CC" \
    WANT_FREESTANDING_LD=yes \
    FREESTANDING_LD="$LD" \
    WANT_FREESTANDING_STRIP=yes \
    FREESTANDING_STRIP="$STRIP" \
    "$SRCDIR/freestanding-toolchain" >"$BUILDDIR/toolchain-files/$ARCH-toolchain.mk" || exit 1

AC_SUBST([TOOLCHAIN_FILE], [toolchain-files/$ARCH-toolchain.mk])

m4_define([DEFAULT_CFLAGS], [-g -O2 -pipe])
AC_ARG_VAR([CFLAGS], [C flags @<:@default: ]DEFAULT_CFLAGS[@:>@])
test "x$CFLAGS" = "x" && CFLAGS="DEFAULT_CFLAGS"

m4_define([DEFAULT_CPPFLAGS], [])
AC_ARG_VAR([CPPFLAGS], [C preprocessor flags @<:@default: ]DEFAULT_CPPFLAGS[@:>@])
test "x$CPPFLAGS" = "x" && CPPFLAGS="DEFAULT_CPPFLAGS"

#m4_define([DEFAULT_NASMFLAGS], [-F dwarf -g])
#AC_ARG_VAR([NASMFLAGS], [nasm flags @<:@default: ]DEFAULT_NASMFLAGS[@:>@])
#test "x$NASMFLAGS" = "x" && NASMFLAGS="DEFAULT_NASMFLAGS"

m4_define([DEFAULT_LDFLAGS], [])
AC_ARG_VAR([LDFLAGS], [linker flags @<:@default: ]DEFAULT_LDFLAGS[@:>@])
test "x$LDFLAGS" = "x" && LDFLAGS="DEFAULT_LDFLAGS"

AC_PREFIX_DEFAULT([/usr/local])

AC_CONFIG_FILES([GNUmakefile])
AC_OUTPUT
