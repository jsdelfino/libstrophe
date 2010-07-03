AC_INIT([libstrophe], [trunk], [jack@metajack.im])
AC_CONFIG_MACRO_DIR([m4])
AM_INIT_AUTOMAKE
AC_PROG_LIBTOOL

AC_PROG_CC

dnl **********************************************************************
dnl DETECT_ICC ([ACTION-IF-YES], [ACTION-IF-NO])
dnl
dnl check if this is the Intel ICC compiler, and if so run the ACTION-IF-YES
dnl sets the $ICC variable to "yes" or "no"
dnl **********************************************************************
AC_DEFUN([DETECT_ICC],
[
    ICC="no"
    AC_MSG_CHECKING([for icc in use])
    if test "$GCC" = "yes"; then
       dnl check if this is icc acting as gcc in disguise
       AC_EGREP_CPP([^__INTEL_COMPILER], [__INTEL_COMPILER],
         AC_MSG_RESULT([no])
         [$2],
         AC_MSG_RESULT([yes])
         [$1]
         ICC="yes")
    else
       AC_MSG_RESULT([no])
       [$2]
    fi
])

DETECT_ICC([], [])

dnl **********************************************************************
dnl DETECT_SUNCC ([ACTION-IF-YES], [ACTION-IF-NO])
dnl
dnl check if this is the Sun Studio compiler, and if so run the ACTION-IF-YES
dnl sets the $SUNCC variable to "yes" or "no"
dnl **********************************************************************
AC_DEFUN([DETECT_SUNCC],
[
    SUNCC="no"
    AC_MSG_CHECKING([for Sun cc in use])
    AC_RUN_IFELSE(
      [AC_LANG_PROGRAM([], [dnl
#ifdef __SUNPRO_C
   return 0;
#else
   return 1;
#endif
      ])
    ],[
       AC_MSG_RESULT([yes])
       [$1]
       SUNCC="yes"
    ], [
       AC_MSG_RESULT([no])
       [$2]
    ])
])

DETECT_SUNCC([CFLAGS="-mt $CFLAGS"], [])


AC_CHECK_HEADER(openssl/ssl.h, [], [AC_MSG_ERROR([couldn't find openssl headers, openssl required])])

# check for 'check' unit-testing framework
PKG_CHECK_MODULES([check], [check >= 0.9.4], [HAVE_CHECK=yes], [
HAVE_CHECK=no
echo "Regression tests will not be built"
])
AM_CONDITIONAL([HAVE_CHECK], [test x$HAVE_CHECK = xyes])

# Configure path to libxml2 includes and lib.
AC_MSG_CHECKING([for libxml2])
AC_ARG_WITH([libxml2], [AC_HELP_STRING([--with-libxml2=PATH], [path to installed libxml2 [default=/usr]])], [
  LIBXML2_INCLUDE="${withval}/include/libxml2"
  LIBXML2_LIB="${withval}/lib"
  AC_MSG_RESULT("${withval}")
], [
  LIBXML2_INCLUDE="/usr/include/libxml2"
  LIBXML2_LIB="/usr/lib"
  AC_MSG_RESULT(/usr)
])
AC_SUBST(LIBXML2_INCLUDE)
AC_SUBST(LIBXML2_LIB)

# Configure path to expat includes and lib.
AC_MSG_CHECKING([for expat])
AC_ARG_WITH([expat], [AC_HELP_STRING([--with-expat=PATH], [path to installed expat [default=/usr]])], [
  LIBEXPAT_INCLUDE="${withval}/include"
  LIBEXPAT_LIB="${withval}/lib"
  AC_MSG_RESULT("${withval}")
], [
  LIBEXPAT_INCLUDE="/usr/include"
  LIBEXPAT_LIB="/usr/lib"
  AC_MSG_RESULT(/usr)
])
AC_SUBST(LIBEXPAT_INCLUDE)
AC_SUBST(LIBEXPAT_LIB)

# Enable Libxml2 support
default_LIBS="${LIBS}"
AC_MSG_CHECKING([whether to use libxml2 for XML parsing])
AC_ARG_ENABLE(libxml2, [AS_HELP_STRING([--enable-libxml2], [use libxml2 for XML parsing [default=no]])],
[ case "${enableval}" in
  no)
    AC_MSG_RESULT(no)
    ;;
  *)
    AC_MSG_RESULT(yes)
    want_libxml2=true
    ;;
  esac ],
[ AC_MSG_RESULT(no)])
if test "${want_libxml2}" = "true"; then
  LIBS="-L${LIBXML2_LIB} ${default_LIBS}"
  AC_CHECK_LIB([xml2], [xmlInitParser], [], [AC_MSG_ERROR([couldn't find a suitable libxml2, use --with-libxml2=PATH])])
  AM_CONDITIONAL([WANT_LIBXML2], true)
  AC_DEFINE([WANT_LIBXML2], 1, [use libxml2 for XML parsing])
  AM_CONDITIONAL([WANT_LIBEXPAT], false)

  with_parser=libxml2
  PARSER_NAME=libxml2
  PARSER_CFLAGS="-I${LIBXML2_INCLUDE}"
  PARSER_LIBS="-L${LIBXML2_LIB} -R${LIBXML2_LIB} -lxml2"

else

  # Enable expat support
  LIBS="-L${LIBEXPAT_LIB} ${default_LIBS}"
  AC_CHECK_LIB([expat], [XML_ExpatVersionInfo], [], [AC_MSG_ERROR([couldn't find a suitable libexpat, use --with-expat=PATH])])
  AM_CONDITIONAL([WANT_LIBEXPAT], true)
  AC_DEFINE([WANT_LIBEXPAT], 1, [use expat for XML parsing])
  AM_CONDITIONAL([WANT_LIBXML2], false)

  with_parser=libexpat
  PARSER_NAME=expat
  PARSER_CFLAGS="-I${LIBEXPAT_INCLUDE}"
  PARSER_LIBS="-L${LIBEXPAT_LIB} -R${LIBEXPAT_LIB} -lexpat"

fi
LIBS="${default_LIBS}"

AC_CHECK_FUNCS(snprintf)
AC_CHECK_FUNCS(vsnprintf)
AC_CHECK_FUNCS(memmove)
AC_C_BIGENDIAN([byteorder=1234], [byteorder=4321], [], [])

AC_MSG_NOTICE([libstrophe will use the $with_parser XML parser])
AC_SEARCH_LIBS([socket], [socket])
AC_CHECK_LIB([resolv], [main])

AM_CONDITIONAL([PARSER_EXPAT], [test x$with_parser != xlibxml2])
AC_DEFINE_UNQUOTED([BYTEORDER], [$byteorder])
AC_SUBST(PARSER_NAME)
AC_SUBST(PARSER_CFLAGS)
AC_SUBST(PARSER_LIBS)

dnl Let the compiler be a bit more picky. Please note that you cannot
dnl specify these flags to the compiler before AC_CHECK_FUNCS, because
dnl the test program will generate a compilation warning and hence fail
dnl to detect the function ;-)if test "$ICC" = "yes"
dnl Lines borrowed from memcached 1.4.2
if test "$ICC" = "yes"
then
   dnl ICC trying to be gcc.
   CFLAGS="$CFLAGS -diag-disable 187 -Wall -Werror"
   AC_DEFINE([_GNU_SOURCE],[1],[find sigignore on Linux])
elif test "$GCC" = "yes"
then
  GCC_VERSION=`$CC -dumpversion`
  dnl removed -pedantic -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations
  CFLAGS="$CFLAGS -Wall -Wredundant-decls"
  case $GCC_VERSION in
    4.4.*)
    CFLAGS="$CFLAGS -fno-strict-aliasing"
    ;;
  esac
  AC_DEFINE([_GNU_SOURCE],[1],[find sigignore on Linux])
elif test "$SUNCC" = "yes"
then
  CFLAGS="$CFLAGS -errfmt=error -errtags -errwarn=%all,no%E_ATTRIBUTE_UNKNOWN,no%E_EMPTY_TRANSLATION_UNIT -errshort=tags"
fi

AC_CONFIG_FILES([Makefile libstrophe.pc])
AC_OUTPUT
