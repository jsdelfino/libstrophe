ACLOCAL_AMFLAGS=-I m4
AUTOMAKE_OPTIONS = subdir-objects

AM_CFLAGS = -g

pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = libstrophe.pc

PARSER_CFLAGS=@PARSER_CFLAGS@
PARSER_LIBS=@PARSER_LIBS@

STROPHE_FLAGS = -I$(top_srcdir) $(AM_CFLAGS)
STROPHE_LIBS = libstrophe.la

## Main build targets
noinst_LTLIBRARIES =

lib_LTLIBRARIES = libstrophe.la
include_HEADERS = strophe.h strophepp.h src/*.h

EXTRA_DIST = README.txt CHANGES.txt Doxyfile GPL-LICENSE.txt LICENSE.txt MIT-LICENSE.txt docs vs2008

libstrophe_la_CFLAGS=$(STROPHE_FLAGS) $(PARSER_CFLAGS) $(AM_CFLAGS)
libstrophe_la_LIBADD = $(PARSER_LIBS) -lssl -lresolv
libstrophe_la_SOURCES = src/auth.c src/conn.c src/ctx.c \
	src/event.c src/handler.c src/hash.c \
	src/jid.c src/md5.c src/sasl.c src/sha1.c \
	src/snprintf.c src/sock.c src/stanza.c \
	src/tls_openssl.c src/util.c src/*.h
# ALK: this is not used presently, but causes extra dependency on -lpthread
# libstrophe_la_SOURCES += src/thread.c
if PARSER_EXPAT
libstrophe_la_SOURCES += src/parser_expat.c
else
libstrophe_la_SOURCES += src/parser_libxml2.c
endif

## Examples
noinst_PROGRAMS = examples/active examples/roster examples/basic examples/bot
examples_active_SOURCES = examples/active.c
examples_active_CFLAGS = $(STROPHE_FLAGS)
examples_active_LDADD = $(STROPHE_LIBS)
examples_roster_SOURCES = examples/roster.c
examples_roster_CFLAGS = $(STROPHE_FLAGS)
examples_roster_LDADD = $(STROPHE_LIBS) 
examples_basic_SOURCES = examples/basic.c
examples_basic_CFLAGS = $(STROPHE_FLAGS)
examples_basic_LDADD = $(STROPHE_LIBS) 
examples_bot_SOURCES = examples/bot.c
examples_bot_CFLAGS = $(STROPHE_FLAGS)
examples_bot_LDADD = $(STROPHE_LIBS) 

if HAVE_CHECK

## Tests
TESTS = tests/check_parser tests/check_stanza tests/check_ctx
check_PROGRAMS = tests/check_parser tests/check_stanza tests/check_ctx

tests_check_parser_SOURCES = tests/check_parser.c tests/test.h
tests_check_parser_CFLAGS = @check_CFLAGS@ $(PARSER_CFLAGS) $(STROPHE_FLAGS) \
	-I$(top_srcdir)/src
tests_check_parser_LDADD = @check_LIBS@ $(STROPHE_LIBS)

tests_check_stanza_SOURCES = tests/check_stanza.c tests/test.h
tests_check_stanza_CFLAGS = @check_CFLAGS@ $(PARSER_CFLAGS) $(STROPHE_FLAGS) \
	-I$(top_srcdir)/src
tests_check_stanza_LDADD = @check_LIBS@ $(STROPHE_LIBS)

tests_check_ctx_SOURCES = tests/check_ctx.c tests/test.h
tests_check_ctx_CFLAGS = @check_CFLAGS@ $(PARSER_CFLAGS) $(STROPHE_FLAGS) \
	-I$(top_srcdir)/src
tests_check_ctx_LDADD = @check_LIBS@ $(STROPHE_LIBS)

endif
