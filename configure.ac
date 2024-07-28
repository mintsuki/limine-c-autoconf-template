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

AC_ARG_VAR([KARCH], [target architecture])
test "x${KARCH+set}" = "x" && KARCH="x86_64"

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
    "$SRCDIR/build-aux/freestanding-toolchain" 2>"toolchain-files/$KARCH-toolchain.mk" || exit 1

AC_SUBST([TOOLCHAIN_FILE], [toolchain-files/$KARCH-toolchain.mk])

AC_ARG_VAR([CFLAGS], [C flags])
test "x${CFLAGS+set}" = "x" && CFLAGS="-g -O2 -pipe"

BORROWED_CFLAGS=""
for cflag in $CFLAGS; do
    case $cflag in
        -O*|-pipe|-g|-f*-prefix-map*)
            BORROWED_CFLAGS="$BORROWED_CFLAGS $cflag"
            ;;
    esac
done

AC_ARG_VAR([KCFLAGS], [C flags])
test "x${KCFLAGS+set}" = "x" && KCFLAGS="$BORROWED_CFLAGS"

AC_ARG_VAR([KCPPFLAGS], [C preprocessor flags])
test "x${KCPPFLAGS+set}" = "x" && KCPPFLAGS=""

#AC_ARG_VAR([KNASMFLAGS], [nasm flags])
#test "x${KNASMFLAGS+set}" = "x" && KNASMFLAGS="-F dwarf -g"

AC_ARG_VAR([KLDFLAGS], [linker flags])
test "x${KLDFLAGS+set}" = "x" && KLDFLAGS=""

AC_PREFIX_DEFAULT([/usr/local])

AC_CONFIG_FILES([GNUmakefile])
AC_OUTPUT
