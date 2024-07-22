AC_INIT([Limine C Autoconf Template], [m4_esyscmd([./version.sh])], [https://github.com/mintsuki/limine-c-autoconf-template/issues], [limine-c-autoconf-template])

AC_PREREQ([2.69])

AC_CONFIG_AUX_DIR([build-aux])

SRCDIR="$(cd "$srcdir" && pwd -P)"

AC_SUBST([SRCDIR])

AC_DEFUN([PROG_ABSPATH], [
    case "$$1" in
        .*) $1="$(cd "$(dirname "$$1")" && pwd -P)/$(basename "$$1")" ;;
    esac
])

werror_state="no"
AC_ARG_ENABLE([werror],
    [AS_HELP_STRING([--enable-werror], [treat warnings as errors])],
    [werror_state="$enableval"])
if test "$werror_state" = "yes"; then
    AC_SUBST([WERROR_FLAG], [-Werror])
else
    AC_SUBST([WERROR_FLAG], [-Wno-error])
fi

m4_define([DEFAULT_KARCH], [x86_64])
AC_ARG_VAR([KARCH], [target architecture @<:@default: ]DEFAULT_KARCH[@:>@])
test "x$KARCH" = "x" && KARCH="DEFAULT_KARCH"

AC_PROG_MKDIR_P
PROG_ABSPATH([MKDIR_P])
AC_PROG_INSTALL
PROG_ABSPATH([INSTALL])

AC_CHECK_PROG([FIND_FOUND], [find], [yes])
if ! test "x$FIND_FOUND" = "xyes"; then
    AC_MSG_ERROR([find not found, please install find before configuring])
fi

#if test "x$KARCH" = "xx86_64"; then
#    AC_CHECK_PROG([NASM_FOUND], [nasm], [yes])
#    if ! test "x$NASM_FOUND" = "xyes"; then
#        AC_MSG_ERROR([nasm not found, please install nasm before configuring])
#    fi
#fi

AC_ARG_VAR([KTOOLCHAIN], [alternative toolchain prefix (or 'llvm', or 'gnu')])
AC_ARG_VAR([KCC], [C compiler command])
AC_ARG_VAR([KLD], [linker command])
AC_ARG_VAR([KSTRIP], [strip command])

rm -rf toolchain-files
$MKDIR_P toolchain-files
ARCHITECTURE="$KARCH" \
    FREESTANDING_TOOLCHAIN_PREFIX="K" \
    FREESTANDING_TOOLCHAIN="$KTOOLCHAIN" \
    WANT_FREESTANDING_CC=yes \
    FREESTANDING_CC="$KCC" \
    WANT_FREESTANDING_LD=yes \
    FREESTANDING_LD="$KLD" \
    WANT_FREESTANDING_STRIP=yes \
    FREESTANDING_STRIP="$KSTRIP" \
    "$SRCDIR/build-aux/freestanding-toolchain/freestanding-toolchain" 2>"toolchain-files/$KARCH-toolchain.mk" || exit 1

AC_SUBST([TOOLCHAIN_FILE], [toolchain-files/$KARCH-toolchain.mk])

m4_define([DEFAULT_KCFLAGS], [-g -O2 -pipe])
AC_ARG_VAR([KCFLAGS], [C flags @<:@default: ]DEFAULT_KCFLAGS[@:>@])
test "x$KCFLAGS" = "x" && KCFLAGS="DEFAULT_KCFLAGS"

m4_define([DEFAULT_KCPPFLAGS], [])
AC_ARG_VAR([KCPPFLAGS], [C preprocessor flags @<:@default: ]DEFAULT_KCPPFLAGS[@:>@])
test "x$KCPPFLAGS" = "x" && KCPPFLAGS="DEFAULT_KCPPFLAGS"

#m4_define([DEFAULT_KNASMFLAGS], [-F dwarf -g])
#AC_ARG_VAR([KNASMFLAGS], [nasm flags @<:@default: ]DEFAULT_KNASMFLAGS[@:>@])
#test "x$KNASMFLAGS" = "x" && KNASMFLAGS="DEFAULT_KNASMFLAGS"

m4_define([DEFAULT_KLDFLAGS], [])
AC_ARG_VAR([KLDFLAGS], [linker flags @<:@default: ]DEFAULT_KLDFLAGS[@:>@])
test "x$KLDFLAGS" = "x" && KLDFLAGS="DEFAULT_KLDFLAGS"

AC_PREFIX_DEFAULT([/usr/local])

AC_CONFIG_FILES([GNUmakefile])
AC_OUTPUT
