# FreeBSD 13.0-p4 build error mariadb-10.6.5

Ticket: [MDEV-27222](https://jira.mariadb.org/browse/MDEV-27222)

System: FreeBSD 13.0-p4
Compiler: clang 11.0.1 with cmake 3.22.0 and bison 3.8.2
Build: cmake -DWITHOUT_TOKUDB=1 -DWITHOUT_MROONGA=1 ..

__make Error__
```
[ 50%] Building C object storage/heap/CMakeFiles/heap.dir/hp_write.c.o

[ 50%] Linking CXX static library libheap.a

[ 50%] Built target heap

[ 50%] Building CXX object storage/innobase/CMakeFiles/innobase.dir/btr/btr0btr.cc.o

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/btr/btr0btr.cc:28:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/btr0btr.h:31:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/dict0dict.h:32:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/dict0mem.h:45:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/buf0buf.h:43:

/usr/local/src/mariadb-10.6.5/storage/innobase/include/transactional_lock_guard.h:145:14: error: 'is_write_locked' is a private member of 'rw_lock'

      if (!m.is_write_locked())

             ^

/usr/local/src/mariadb-10.6.5/storage/innobase/include/buf0buf.h:1540:54: note: in instantiation of member function 'transactional_shared_lock_guard<page_hash_latch>::transactional_shared_lock_guard' requested here

    transactional_shared_lock_guard<page_hash_latch> g

                                                     ^

/usr/local/src/mariadb-10.6.5/storage/innobase/include/buf0types.h:182:25: note: constrained by private inheritance here

class page_hash_latch : private rw_lock

                        ^~~~~~~~~~~~~~~

/usr/local/src/mariadb-10.6.5/storage/innobase/include/rw_lock.h:225:8: note: member is declared here

  bool is_write_locked() const { return !!(value() & WRITER); }

       ^

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/btr/btr0btr.cc:28:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/btr0btr.h:31:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/dict0dict.h:32:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/dict0mem.h:45:

In file included from /usr/local/src/mariadb-10.6.5/storage/innobase/include/buf0buf.h:43:

/usr/local/src/mariadb-10.6.5/storage/innobase/include/transactional_lock_guard.h:145:12: error: cannot cast 'page_hash_latch' to its private base class 'const rw_lock'

      if (!m.is_write_locked())

           ^

/usr/local/src/mariadb-10.6.5/storage/innobase/include/buf0types.h:182:25: note: declared private here

class page_hash_latch : private rw_lock

                        ^~~~~~~~~~~~~~~

2 errors generated.

*** Error code 1

 

Stop.

make[2]: stopped in /usr/local/src/mariadb-10.6.5/build

*** Error code 1

 

Stop.

make[1]: stopped in /usr/local/src/mariadb-10.6.5/build

*** Error code 1

 

Stop.

make: stopped in /usr/local/src/mariadb-10.6.5/build
```

This error comes with commit https://github.com/mariadb/server/commit/1f02280904fcfbb2bd86404d1c85c025634f8c9d
