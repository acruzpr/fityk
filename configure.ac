dnl $Id$
dnl Process this file with autoconf to produce a configure script.
AC_INIT
AC_CONFIG_SRCDIR([src/fzgbase.h])
AC_CONFIG_AUX_DIR(config)
AC_CONFIG_HEADERS([config.h])
AC_CANONICAL_TARGET([])
AM_INIT_AUTOMAKE(fityk, 0.4.0)
AM_MAINTAINER_MODE

dnl What versions of program are to be made 
AC_ARG_ENABLE(GUI, 
[  --disable-GUI            do not build GUI version of program])
test -z "$enable_GUI" && enable_GUI="yes"  
AC_ARG_ENABLE(CLI, 
[  --disable-CLI            do not build CLI version of program])
test -z "$enable_CLI" && enable_CLI="yes"  

dnl use static/dynamic wxWin library
AC_ARG_ENABLE(CLI, 
[  --disable-shared-wx      link with static version of wxWindows library])
test -z "$enable_shared_wx" && enable_shared_wx="yes"  

dnl The GNU readline library
AC_ARG_WITH(readline, 
[  --with-readline           use the GNU readline library for cfityk (default) 
  --with-readline=PATH      Specify the location of readline])
test -z "$with_readline" && with_readline="yes"  

AM_OPTIONS_WXCONFIG

dnl both CLI and GUI can't be disabled.
if test "$enable_CLI" = "no" -a "$enable_GUI" = "no"; then 
	AC_MSG_ERROR([Disabling both GUI and CLI versions makes no sense.])
fi


PROGRAM_EXT=
#################################
# MS Windows specific macros 

AC_MSG_CHECKING([for native Win32 - MinGW])
case "${host}" in
    *-*-mingw*)
        os_win32=yes
	PROGRAM_EXT=".exe"
	;;
    *)
        os_win32=no
	;;
esac
AC_MSG_RESULT([$os_win32])
AM_CONDITIONAL(OS_WIN32, test "$os_win32" = "yes")

if test "$os_win32" = "yes"; then
    AC_CHECK_TOOL(RC, windres)
    if test "${RC}" = "" ; then
        AC_MSG_ERROR([Required resource tool 'windres' not found on PATH.])
    fi
fi
#################################


dnl Checks for programs.
AC_PROG_CXX

dnl Checks for common header files.
AC_HEADER_STDC
if test "$ac_cv_header_stdc" != yes; then 
	AC_MSG_ERROR([ANSI C header files not found :-(])
fi
dnl use c++ for testing <sstream>
 
AC_LANG_PUSH([C++])
AC_CHECK_HEADER(sstream, [], [AC_MSG_ERROR(
 [You don't have a <sstream> header. It is a part of standard C++ library.
  Either you have too old compiler, or standard C++ library can't be found.])])
AC_LANG_POP([C++]) dnl C is faster than C++

dnl Checks for version specific libraries and headers.
if test "$enable_GUI" != "no" ; then 
    FITYK=fityk$PROGRAM_EXT 
    AM_PATH_WXCONFIG(2.3.4, wxWin=1)
    if test "$wxWin" != 1; then
    	AC_MSG_ERROR([
    	      wxWindows must be installed on your system
    	      but wx-config script couldn't be found.
    
    	      Please check that wx-config is in path, the directory
    	      where wxWindows libraries are installed (returned by
    	      'wx-config --libs' command) is in LD_LIBRARY_PATH or
    	      equivalent variable and wxWindows version is 2.3.3 or above.
            ])
    fi

    if test "$enable_shared_wx" != "no"; then
	    WX_LIBS_DS="$WX_LIBS"
	    dnl AC_MSG_RESULT([Chosen shared version of wxWindows library.])
    else
	    WX_LIBS_DS="$WX_LIBS_STATIC"
	    AC_MSG_RESULT([Chosen static version of wxWindows library.])
    fi

fi

if test "$enable_CLI" != "no"; then 
    CFITYK=cfityk$PROGRAM_EXT 
    READLINE_STUFF

    AC_CHECK_PROG(gnuplot_in_path, gnuplot, yes)
    if test "x$gnuplot_in_path" != "xyes" ; then
	    AC_MSG_RESULT(
	  [ gnuplot is used by cfityk (command line version) for drawing plots, 
		       but it is not necessery])
    fi

    AC_CHECK_HEADER(unistd.h, [], [AC_MSG_ERROR(
      [You don't have an unistd.h header, that is required to build cfityk.])])
fi


AC_SUBST(FITYK)
AC_SUBST(CFITYK)
AC_SUBST(WX_LIBS_DS) 


dnl Checks for typedefs, structures, and compiler characteristics.
dnl AC_TYPE_PID_T

dnl Checks for library functions.
dnl AC_TYPE_SIGNAL
dnl AC_CHECK_FUNCS(putenv strtod)

dnl in generated configure script, this line is too late
test "x$prefix" = xNONE && prefix=$ac_default_prefix

eval help_dir=${datadir} 

AC_MSG_RESULT([Directory for online help system: $help_dir])
AC_DEFINE_UNQUOTED(HELP_DIR, "$help_dir", [Directory for online help system])

AC_CONFIG_FILES([Makefile fityk.spec src/Makefile src/img/Makefile doc/Makefile doc/fitykhelp_img/Makefile samples/Makefile config/Makefile])
AC_OUTPUT

