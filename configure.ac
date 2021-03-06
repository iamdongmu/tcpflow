## Process this file with autoconf to produce a configure script.
## The simpler way is to run ./autogen.sh.

# TODO: Put checks in this order:
# 1. initialization and options processing
# 2. programs
# 3. libraries
# 4. header files
# 5. types
# 6. structures
# 7. compiler characteristics
# 8. functions, global variables
# 9. system services

# make sure we're interpreted by some minimal autoconf
AC_PREREQ(2.57)

AC_INIT(tcpflow, 0.22-pre, jelson@circlemud.org)

# The argument here is just something that should be in the current directory
# (for sanity checking)
AC_CONFIG_SRCDIR(src/tcpflow.h)
AM_INIT_AUTOMAKE
AM_CONFIG_HEADER(src/conf.h)

# Populate $host_os, $host_cpu, etc.
AC_CANONICAL_HOST

AC_LANG(C)

# Checks for programs.
AC_PROG_CC
AM_CONDITIONAL(GCC, test "$GCC" = yes)   # let the Makefile know if we're gcc

# Check headers
AC_HEADER_STDC

AC_CHECK_HEADERS([sys/resource.h sys/types.h unistd.h signal.h])
AC_CHECK_HEADERS([sys/socket.h netinet/tcp.h netinet/in_systm.h netinet/in.h])

# On FreeBSD, test for netinet/ip.h will fail unless netinet/in.h is included first.
AC_CHECK_HEADERS([netinet/ip.h], [], [], [AC_INCLUDES_DEFAULT
  #ifdef HAVE_NETINET_IN_H
  # include <netinet/in.h>
  #endif
])

# On FreeBSD, test for net/if.h will fail unless sys/socket.h is included first.
AC_CHECK_HEADERS([net/if.h], [], [],[AC_INCLUDES_DEFAULT
  #ifdef HAVE_SYS_SOCKET_H
  # include <sys/socket.h>
  #endif
])

# On FreeBSD, test for netinet/if_ether.h will fail unless sys/socket.h and netinet/in.h are included first.
AC_CHECK_HEADERS([netinet/if_ether.h], [], [],[AC_INCLUDES_DEFAULT
  #ifdef HAVE_SYS_SOCKET_H
  # include <sys/socket.h>
  #endif
  #ifdef HAVE_NETINET_IN_H
  # include <netinet/in.h>
  #endif
])

AC_CHECK_HEADERS(linux/if_ether.h)

# These are the types to check. We look for them in either stdint.h,
# sys/types.h, or inttypes.h, all of which are part of the default-includes.
# TODO(asd): type checking is obsolete. Fix it.
AC_CHECK_TYPE(u_int32_t, unsigned long)
AC_CHECK_TYPE(u_int16_t, unsigned short)
AC_CHECK_TYPE(u_int8_t, unsigned char)
AC_CHECK_TYPE(u_char, unsigned char)

AC_TYPE_SIGNAL

AC_CHECK_FUNCS(sigaction)
AC_CHECK_FUNCS(sigset)

# We check for the library only if the function is not available without the library.
AC_CHECK_FUNC(gethostbyaddr, [], [AC_CHECK_LIB(nsl, gethostbyaddr)])
AC_CHECK_FUNC(socket, [], [AC_CHECK_LIB(socket, socket)])

# Checking pcap.
# Note: The check for -lsocket and -lnsl must go before -lpcap, because -lpcap uses those libraries.

AC_ARG_WITH([pcap], 
  [AS_HELP_STRING([--with-pcap=PATH], [Specify path to pcap library.])],
  [
    AC_MSG_CHECKING(for --with-pcap option)
    case "$withval" in
      yes|no)
        AC_MSG_ERROR(PATH required with pcap option)
        ;;
      *)
        if test '!' -d "$withval"; then
          AC_MSG_ERROR($withval does not exist!)
        else
          AC_MSG_RESULT($withval)
          if test -d "$withval/include"; then
            CFLAGS="$CFLAGS -I$withval/include -I../$withval/include"
            CPPFLAGS="$CPPFLAGS -I$withval/include -I../$withval/include"
          else
            CFLAGS="$CFLAGS -I$withval -I../$withval"
            CPPFLAGS="$CPPFLAGS -I$withval -I../$withval"
          fi
          if test -d "$withval/lib"; then
            LIBS="$LIBS -L$withval/lib -L../$withval/lib"
          else
            LIBS="$LIBS -L$withval -L../$withval"
          fi
        fi
        ;;
    esac
  ]
)

# TODO: check if make install and make install-incl are both necessary.
AC_CHECK_HEADER(pcap.h, [], [AC_MSG_ERROR([
Can't find pcap.h; install the pcap library  (from http://www.tcpdump.org/) 
and/or use --with-pcap to specify the path to it on your system.

When installing libpcap do both 'make install' and 'make install-incl'])
])

AC_CHECK_LIB(pcap, pcap_lookupdev, [], [AC_MSG_ERROR([
Can't find the libpcap.a file; install the pcap library (from http://www.tcpdump.org/) 
and/or use --with-pcap to specify the path to it on your system.

When installing libpcap do both 'make install' and 'make install-incl'])
])

AC_MSG_CHECKING([for special system dependencies])
case "$host_os" in
  linux*)
    AC_DEFINE(DLT_NULL_BROKEN, [1], [Libpcap's DLT_NULL seems to be broken in Linux.])
    AC_MSG_RESULT([Linux])
    ;;
  *)
    AC_MSG_RESULT([none])
    ;;
esac

# Write generated configuration file
AC_CONFIG_FILES([Makefile src/Makefile doc/Makefile doc/tcpflow.1])
AC_OUTPUT
