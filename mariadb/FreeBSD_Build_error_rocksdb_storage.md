# Build error rocksdb storage mariadb-10.4.7 on FreeBSD 12.0-p8

Ticket: [MDEV-20248](https://jira.mariadb.org/browse/MDEV-20248)

System: FreeBSD 12.0 - p8  
Compiler: clang 6.0.1 with cmake 3.15.1  
Build: cmake -DWITHOUT_TOKUDB=1 .  

__make Error__
```
[ 74%] Built target rocksdb_tools
[ 75%] Building CXX object storage/rocksdb/CMakeFiles/rocksdblib.dir/rocksdb/db/malloc_stats.cc.o
c++: warning: -Wl,-z,relro,-z,now: 'linker' input unused [-Wunused-command-line-argument]
In file included from /usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/db/malloc_stats.cc:16:
/usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/port/jemalloc_helper.h:44:19: error: redefinition of
      'malloc_usable_size' as different kind of symbol
extern "C" size_t malloc_usable_size(JEMALLOC_USABLE_SIZE_CONST void*)
                  ^
/usr/include/malloc_np.h:71:8: note: previous definition is here
size_t  malloc_usable_size(const void *ptr);
       ^
In file included from /usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/db/malloc_stats.cc:16:
/usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/port/jemalloc_helper.h:44:38: error: use of undeclared
      identifier 'JEMALLOC_USABLE_SIZE_CONST'
extern "C" size_t malloc_usable_size(JEMALLOC_USABLE_SIZE_CONST void*)
                                     ^
/usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/port/jemalloc_helper.h:44:71: error: expected ';' after top
      level declarator
extern "C" size_t malloc_usable_size(JEMALLOC_USABLE_SIZE_CONST void*)
                                                                      ^
                                                                      ;
/usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/port/jemalloc_helper.h:45:24: warning: declaration does not
      declare anything [-Wmissing-declarations]
    JEMALLOC_CXX_THROW __attribute__((__weak__));
                       ^
/usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/port/jemalloc_helper.h:58:43: warning: comparison of function
      'malloc_usable_size' not equal to a null pointer is always true [-Wtautological-pointer-compare]
         malloc_stats_print != nullptr && malloc_usable_size != nullptr;
                                          ^~~~~~~~~~~~~~~~~~    ~~~~~~~
/usr/local/src/mariadb-10.4.7/storage/rocksdb/rocksdb/port/jemalloc_helper.h:58:43: note: prefix with the address-of
      operator to silence this warning
         malloc_stats_print != nullptr && malloc_usable_size != nullptr;
                                          ^
                                          &
2 warnings and 3 errors generated.
*** Error code 1
Stop.
make[2]: stopped in /usr/local/src/mariadb-10.4.7
*** Error code 1
Stop.
make[1]: stopped in /usr/local/src/mariadb-10.4.7
*** Error code 1
Stop.
make: stopped in /usr/local/src/mariadb-10.4.7
```

## Workaround

Use mariadDB FreeBSD patch from gsnw-ports tree: https://github.com/gsnw/gsnw-ports/tree/main/databases/mariadb-105/patch

1) Download source from https://downloads.mariadb.org/
2) Extract source
3) Download appropriate patch from gsnw-ports tree
4) Go inside the mariadb source and execute ```patch -p1 -i <path patchfile>```

### Patch content
```
JEMALLOC_USABLE_SIZE_CONST is not defined in FreeBSD's jemalloc(3), define the
macro to "const".
This prevents build failures in FreeBSD.
Reference upstream bug report: https://jira.mariadb.org/browse/MDEV-20248
--- storage/rocksdb/rocksdb/port/jemalloc_helper.h.orig	2019-08-12 02:20:55 UTC
+++ storage/rocksdb/rocksdb/port/jemalloc_helper.h
@@ -12,6 +12,10 @@
 #include <jemalloc/jemalloc.h>
 #endif

+#ifdef __FreeBSD__
+#define JEMALLOC_USABLE_SIZE_CONST const
+#endif
+
 #ifndef JEMALLOC_CXX_THROW
 #define JEMALLOC_CXX_THROW
 #endif
```
