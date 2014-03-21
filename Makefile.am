ACLOCAL_AMFLAGS = -I m4
AM_CFLAGS = -I@top_srcdir@/src -Iproto
PROTOBUF_C_TEST_SRCS = t/addressbook.pb-c.c
PROTOBUF_C_TEST_HDRS = $(patsubst %.c,%.h,$(PROTOBUF_C_TEST_SRCS) \
				          $(PROTOBUF_C_TEST_SRCS))
PROTOBUF_CXX_TEST_SRCS = t/addressbook.pb.c
PROTOBUF_CXX_TEST_HDRS = $(patsubst %.c,%.h,$(PROTOBUF_CXX_TEST_SRCS) \
				            $(PROTOBUF_CXX_TEST_SRCS))

lib_LTLIBRARIES = protobuf-c-text/libprotobuf-c-text.la
TESTS = t/test_generation.sh
check_PROGRAMS = t/add_person t/dump_people t/list_people
check_SCRIPTS = t/test_generation.sh
BUILT_SOURCES = $(PROTOBUF_C_TEST_SRCS) $(PROTOBUF_C_TEST_SRCS) \
		$(PROTOBUF_CXX_TEST_SRCS) $(PROTOBUF_CXX_TEST_SRCS) 

#dist_man3_MANS = man/protobuf-c-text.3

valgrind: $(check_PROGRAMS)
	@t/valgrind.test_generation.sh

# Libraries.
protobuf_c_text_libprotobuf_c_text_la_SOURCES = \
    protobuf-c-text/generate.c
lib_libriakpbc_la_CFLAGS = $(AM_CFLAGS) $(COVERAGE_CFLAGS)
# Version updating rules:
# 1. If the library source code has changed but interfaces are the same,
#    then increment revision (c:r:a becomes c:r+1:a).
# 2. If any interfaces have been removed or changed since the last release,
#    set revision and age to 0 and increment current (c:r:a becomes c+1:0:0).
# 3. If interfaces have only been added since the last release, set
#    release to 0 and increment release and age (c:r:a becomes c+1:0:a+1).
protobuf_c_text_libprotobuf_c_text_la_LDFLAGS = $(COVERAGE_LDFLAGS) -version-info 0:0:0

# Headers for libraries.
libprotobuf_c_textdir = $(includedir)/google/protobuf-c
libprotobuf_c_text_HEADERS = protobuf-c-text/protobuf-c-text.h

# Static analysis.
analyze_srcs = $(protobuf_c_text_libprotobuf_c_text_la_SOURCES)
.PHONY: analyze
analyze: all
	@for f in $(analyze_srcs); do \
	  if test "$(AM_V)" = 1; then \
	    echo clang --analyze -I proto -I src $$f -o /dev/null; \
	  else \
	    echo "  CCSA  " $$f; \
	  fi; \
	  clang --analyze -I proto -I src $$f -o /dev/null; \
	done

# Test programs.
t_add_person_SOURCES = $(PROTOBUF_CXX_TEST_SRCS) t/add_person.c
t_add_person_CFLAGS = $(AM_CFLAGS) $(COVERAGE_CFLAGS) @CHECK_CFLAGS@
t_add_person_LDADD = $(COVERAGE_LDFLAGS) @CHECK_LIBS@
t_add_person_LDFLAGS = -static

t_dump_people_SOURCES = $(PROTOBUF_CXX_TEST_SRCS) t/dump_people.c
t_dump_people_CFLAGS = $(AM_CFLAGS) $(COVERAGE_CFLAGS) @CHECK_CFLAGS@
t_dump_people_LDADD = $(COVERAGE_LDFLAGS) @CHECK_LIBS@
t_dump_people_LDFLAGS = -static

t_list_people_SOURCES = $(PROTOBUF_CXX_TEST_SRCS) t/list_people.c
t_list_people_CFLAGS = $(AM_CFLAGS) $(COVERAGE_CFLAGS) @CHECK_CFLAGS@
t_list_people_LDADD = $(COVERAGE_LDFLAGS) @CHECK_LIBS@
t_list_people_LDFLAGS = -static

# Define macros to have quiet protoc-c statements.
AM_V_PROTOC_C = $(AM_V_PROTOC_C_@AM_V@)
AM_V_PROTOC_C_ = $(AM_V_PROTOC_C_@AM_DEFAULT_V@)
AM_V_PROTOC_C_0 = @echo PBC_C $<;
%.pb-c.c %.pb-c.h: %.proto
	$(AM_V_PROTOC_C)$(PROTOC_C) --proto_path=$(dir $<) --c_out=$(dir $@) $<

# Define macros to have quiet protoc statements.
AM_V_PROTOC = $(AM_V_PROTOC_@AM_V@)
AM_V_PROTOC_ = $(AM_V_PROTOC_@AM_DEFAULT_V@)
AM_V_PROTOC_0 = @echo PBC $<;
%.pb.c %.pb.h: %.proto
	$(AM_V_PROTOC)$(PROTOC) --proto_path=$(dir $<) --cpp_out=$(dir $@) $<

include am/aminclude_coverage.am
include am/aminclude_doxygen.am

.PHONY: changelog
changelog:
	@git shortlog --pretty='* [%h] %s' -e `git log -n 1 --pretty='%H' -- ChangeLog`..HEAD > ChangeLog.new

# Add things to remove for the "distclean" target.
distclean-local: distclean-local-ac
.PHONY: distclean-local-ac
distclean-local-ac:
	@rm -rf autom4te.cache build-aux/*~ m4/*~

# Add things to remove for the "clean" target.
clean-local: clean-local-build-dirs clean-coverage-html
.PHONY: clean-local-build-dirs
clean-local-build-dirs:
	@rm -rf bin lib tests
MOSTLYCLEANFILES = $(DX_CLEANFILES) \
		   $(BUILT_SOURCES)