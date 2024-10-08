
dnl  Copyright 2005 Red Hat, Inc.
dnl 
dnl  Permission to use, copy, modify, distribute, and sell this software and its
dnl  documentation for any purpose is hereby granted without fee, provided that
dnl  the above copyright notice appear in all copies and that both that
dnl  copyright notice and this permission notice appear in supporting
dnl  documentation, and that the name of Red Hat not be used in
dnl  advertising or publicity pertaining to distribution of the software without
dnl  specific, written prior permission.  Red Hat makes no
dnl  representations about the suitability of this software for any purpose.  It
dnl  is provided "as is" without express or implied warranty.
dnl 
dnl  RED HAT DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE,
dnl  INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS, IN NO
dnl  EVENT SHALL RED HAT BE LIABLE FOR ANY SPECIAL, INDIRECT OR
dnl  CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE,
dnl  DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER
dnl  TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR
dnl  PERFORMANCE OF THIS SOFTWARE.
dnl
dnl Process this file with autoconf to create configure.

# Initialize Autoconf
AC_PREREQ([2.60])
AC_INIT([xman], [1.1.6],
        [https://gitlab.freedesktop.org/xorg/app/xman/issues], [xman])
AC_CONFIG_SRCDIR([Makefile.am])
AC_CONFIG_HEADERS([config.h])

# Initialize Automake
AM_INIT_AUTOMAKE([foreign dist-xz])

# Require X.Org macros 1.8 or later for MAN_SUBSTS set by XORG_MANPAGE_SECTIONS
m4_ifndef([XORG_MACROS_VERSION],
	  [m4_fatal([must install xorg-macros 1.8 or later before running autoconf/autogen])])
XORG_MACROS_VERSION(1.8)
XORG_DEFAULT_OPTIONS

AC_CANONICAL_HOST

AC_CHECK_PROG([GROFF], [groff], [found], [missing])
if test "x$GROFF" = "xfound" ; then
   AC_DEFINE([HAS_GROFF], 1, [Define to 1 if you have the groff package.])
fi

AC_CHECK_PROG([MANDB], [mandb], [yes], [no])
if test x"$MANDB" = xyes; then
	AC_DEFINE([HAVE_MANDB], 1, [Define if the man implementation is man-db])
fi

# Checks for typedefs, structures, and compiler characteristics.
AC_SYS_LARGEFILE

# Checks for library functions.
AC_CHECK_FUNCS([mkstemp])

AC_ARG_WITH(helpdir,
        AS_HELP_STRING([--with-helpdir=<path>],
                       [Set default directory for xman.help (default: ${datadir}/X11)]),
        [HELPDIR="$withval"],
        [HELPDIR=${datadir}/X11])
AC_SUBST([HELPDIR])

AC_ARG_WITH(sysmanpath,
        AS_HELP_STRING([--with-sysmanpath=<path>],
                       [Set default system man page search path]),
        [SYSMANPATH="$withval"], [])
if test x$SYSMANPATH != x; then
    AC_DEFINE_UNQUOTED(SYSMANPATH, "$SYSMANPATH", 
	[Default system man page search path (default: none)])
fi

AC_ARG_WITH(localmanpath,
        AS_HELP_STRING([--with-localmanpath=<path>],
                       [Set default local man page search path]),
        [LOCALMANPATH="$withval"], [])
if test x$LOCALMANPATH != x; then
    AC_DEFINE_UNQUOTED(LOCALMANPATH, "$LOCALMANPATH", 
	[Default local man page search path (default: none)])
fi

# Obtain compiler/linker options from dependencies
PKG_CHECK_MODULES(XMAN, [xproto >= 7.0.17 xaw7 xt])

AC_ARG_WITH(manconfig,
        AS_HELP_STRING([--with-manconfig=<filename>],
                       [Set default system man configuration file]),
        [MANCONF="$withval"], [])
if test x$MANCONF = x; then
# Check for man page config files
    AC_CHECK_FILE([/etc/man.conf], [MANCONF="/etc/man.conf"], 
                  AC_CHECK_FILE([/etc/man.config], [MANCONF="/etc/man.config"], 
                                AC_CHECK_FILE([/etc/manpath.config], [MANCONF="/etc/manpath.config"],
                                              AC_CHECK_FILE([/usr/share/misc/man.conf], [MANCONF="/usr/share/misc/man.conf"]))))
fi

if test x$MANCONF != x ; then
    AC_DEFINE_UNQUOTED(MANCONF, "$MANCONF", 
	[Define to path to man config file if you have one])

    # Try to determine format of config file
    # would be better to somehow determine from the files themselves, but
    # we'll guess based on pathname and OS for now (mirrors old Imake tests)
    AC_MSG_CHECKING([man config file format])
    if test x$MANCONF = x/etc/manpath.config ; then
	MAN_CONFIG_STYLE="FreeBSD"
    else
	case $host_os in
	*darwin* | *openbsd* | *netbsd* )
	    MAN_CONFIG_STYLE="OpenBSD"
	    ;;
	*linux* | cygwin* )
	    MAN_CONFIG_STYLE="Linux"
	    ;;
	*bsd* )
	    MAN_CONFIG_STYLE="BSD"
	    ;;
	*)
	    ;;
	esac
    fi
    AC_MSG_RESULT($MAN_CONFIG_STYLE)

    case $MAN_CONFIG_STYLE in
	FreeBSD)
	    AC_DEFINE([MANCONFIGSTYLE_FreeBSD],1,
		[Define to 1 if you have FreeBSD format manpath.config])
	    ;;
	OpenBSD)
	    AC_DEFINE([MANCONFIGSTYLE_OpenBSD],1,
		[Define to 1 if you have OpenBSD format manpath.config])
	    ;;
	BSD)
	    AC_DEFINE([MANCONFIGSTYLE_BSD],1,
		[Define to 1 if you have BSD format manpath.config])
	    ;;
	Linux)
	    AC_DEFINE([MANCONFIGSTYLE_Linux],1,
		[Define to 1 if you have Linux format man.conf or man.config])
	    ;;
	*)
	    AC_MSG_ERROR([Could not determine man page file config format.])
    esac
fi

PKG_CHECK_MODULES(APPDEFS, xt)
xt_appdefaultdir=`$PKG_CONFIG --variable=appdefaultdir xt`
AC_ARG_WITH(appdefaultdir,
	AS_HELP_STRING([--with-appdefaultdir=<pathname>],
	  [specify directory for app-defaults files (default is autodetected)]),
	[appdefaultdir="$withval"], [appdefaultdir="${xt_appdefaultdir}"])
AC_SUBST(appdefaultdir)

if test "x$RELEASE_VERSION" != "x"; then
	AC_DEFINE_UNQUOTED([RELEASE_VERSION], "$RELEASE_VERSION", 
		[Release version string if set])
fi

AC_CONFIG_FILES([
	Makefile
	man/Makefile])
AC_OUTPUT
