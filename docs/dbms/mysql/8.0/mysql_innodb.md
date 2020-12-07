# MySQL InnoDB Storage Engine

#### InnoDB源码结构

```
├─api             Native API
├─arch            Archiver: redo log and dirty page
├─btr             B-Tree
├─buf             Buffering
├─clone           Clone
├─data            Data
├─dict            Dictionary
├─eval            Evaluating
├─fil             File
├─fsp             File Space
├─fts             Full Text Search
├─fut             File Utility
├─gis             GIS R-Tree
├─ha              Hashing
├─handler         Handler: Interface between MySQL and InnoDB
├─ibuf            Insert Buffer
├─include         include
├─lob             LOB: Large Objects
├─lock            Locking
├─log             Logging
├─mach            Machine Format
├─mem             Memory
├─mtr             Mini-Transaction
├─os              Operating System
├─page            Page
├─pars            Parsing
├─que             Query Graph
├─read            Read
├─rem             Record Manager
├─row             Row
├─srv             Server
├─sync            Synchronization
├─trx             Transaction
├─usr             User
└─ut              Utilities
```


```
│  CMakeLists.txt
│  Doxyfile
│  innodb.cmake
│  
├─api
│      api0api.cc
│      api0misc.cc
│      
├─arch
│      arch0arch.cc
│      arch0log.cc
│      arch0page.cc
│      arch0recv.cc
│      
├─btr
│      btr0btr.cc
│      btr0bulk.cc
│      btr0cur.cc
│      btr0pcur.cc
│      btr0sea.cc
│      
├─buf
│      buf.cc
│      buf.h
│      buf0buddy.cc
│      buf0buf.cc
│      buf0dblwr.cc
│      buf0dump.cc
│      buf0flu.cc
│      buf0lru.cc
│      buf0rea.cc
│      checksum.cc
│      
├─clone
│      clone0api.cc
│      clone0apply.cc
│      clone0clone.cc
│      clone0copy.cc
│      clone0desc.cc
│      clone0repl.cc
│      clone0snapshot.cc
│      
├─data
│      data0data.cc
│      data0type.cc
│      
├─dict
│      dict.cc
│      dict.h
│      dict0boot.cc
│      dict0crea.cc
│      dict0dd.cc
│      dict0dict.cc
│      dict0load.cc
│      dict0mem.cc
│      dict0sdi.cc
│      dict0stats.cc
│      dict0stats_bg.cc
│      dict0upgrade.cc
│      mem.cc
│      mem.h
│      
├─eval
│      eval0eval.cc
│      eval0proc.cc
│      
├─fil
│      fil0fil.cc
│      
├─fsp
│      fsp0file.cc
│      fsp0fsp.cc
│      fsp0space.cc
│      fsp0sysspace.cc
│      
├─fts
│      fts0ast.cc
│      fts0blex.cc
│      fts0blex.l
│      fts0config.cc
│      fts0fts.cc
│      fts0opt.cc
│      fts0pars.cc
│      fts0pars.y
│      fts0plugin.cc
│      fts0que.cc
│      fts0sql.cc
│      fts0tlex.cc
│      fts0tlex.l
│      Makefile.query
│      
├─fut
│      fut0lst.cc
│      
├─gis
│      gis0geo.cc
│      gis0rtree.cc
│      gis0sea.cc
│      
├─ha
│      ha0ha.cc
│      ha0storage.cc
│      hash0hash.cc
│      
├─handler
│      handler0alter.cc
│      ha_innodb.cc
│      ha_innodb.h
│      ha_innopart.cc
│      ha_innopart.h
│      i_s.cc
│      i_s.h
│      p_s.cc
│      p_s.h
│      
├─ibuf
│      ibuf0ibuf.cc
│      
├─include
│      api0api.h
│      api0misc.h
│      arch0arch.h
│      arch0log.h
│      arch0page.h
│      arch0recv.h
│      btr0btr.h
│      btr0btr.ic
│      btr0bulk.h
│      btr0cur.h
│      btr0cur.ic
│      btr0pcur.h
│      btr0sea.h
│      btr0sea.ic
│      btr0types.h
│      buf0buddy.h
│      buf0buddy.ic
│      buf0buf.h
│      buf0buf.ic
│      buf0checksum.h
│      buf0dblwr.h
│      buf0dump.h
│      buf0flu.h
│      buf0flu.ic
│      buf0lru.h
│      buf0lru.ic
│      buf0rea.h
│      buf0stats.h
│      buf0types.h
│      clone0api.h
│      clone0clone.h
│      clone0desc.h
│      clone0monitor.h
│      clone0repl.h
│      clone0snapshot.h
│      data0data.h
│      data0data.ic
│      data0type.h
│      data0type.ic
│      data0types.h
│      db0err.h
│      dict0boot.h
│      dict0boot.ic
│      dict0crea.h
│      dict0crea.ic
│      dict0dd.h
│      dict0dd.ic
│      dict0dict.h
│      dict0dict.ic
│      dict0load.h
│      dict0load.ic
│      dict0mem.h
│      dict0mem.ic
│      dict0priv.h
│      dict0priv.ic
│      dict0sdi-decompress.h
│      dict0sdi.h
│      dict0stats.h
│      dict0stats.ic
│      dict0stats_bg.h
│      dict0stats_bg.ic
│      dict0types.h
│      dict0upgrade.h
│      dyn0buf.h
│      dyn0types.h
│      eval0eval.h
│      eval0eval.ic
│      eval0proc.h
│      eval0proc.ic
│      fil0fil.h
│      fil0types.h
│      fsp0file.h
│      fsp0fsp.h
│      fsp0fsp.ic
│      fsp0space.h
│      fsp0sysspace.h
│      fsp0types.h
│      fts0ast.h
│      fts0blex.h
│      fts0fts.h
│      fts0opt.h
│      fts0pars.h
│      fts0plugin.h
│      fts0priv.h
│      fts0priv.ic
│      fts0tlex.h
│      fts0tokenize.h
│      fts0types.h
│      fts0types.ic
│      fts0vlc.ic
│      fut0fut.h
│      fut0fut.ic
│      fut0lst.h
│      fut0lst.ic
│      gis0geo.h
│      gis0rtree.h
│      gis0rtree.ic
│      gis0type.h
│      ha0ha.h
│      ha0ha.ic
│      ha0storage.h
│      ha0storage.ic
│      handler0alter.h
│      hash0hash.h
│      hash0hash.ic
│      ha_prototypes.h
│      ib0mutex.h
│      ibuf0ibuf.h
│      ibuf0ibuf.ic
│      ibuf0types.h
│      lob0del.h
│      lob0first.h
│      lob0impl.h
│      lob0index.h
│      lob0inf.h
│      lob0ins.h
│      lob0lob.h
│      lob0pages.h
│      lob0undo.h
│      lob0util.h
│      lob0zip.h
│      lock0iter.h
│      lock0lock.h
│      lock0lock.ic
│      lock0prdt.h
│      lock0priv.h
│      lock0priv.ic
│      lock0types.h
│      log0ddl.h
│      log0log.h
│      log0log.ic
│      log0meb.h
│      log0recv.h
│      log0recv.ic
│      log0test.h
│      log0types.h
│      mach0data.h
│      mach0data.ic
│      mem0mem.h
│      mem0mem.ic
│      mtr0log.h
│      mtr0log.ic
│      mtr0mtr.h
│      mtr0mtr.ic
│      mtr0types.h
│      os0atomic.h
│      os0atomic.ic
│      os0event.h
│      os0event.ic
│      os0file.h
│      os0file.ic
│      os0numa.h
│      os0once.h
│      os0proc.h
│      os0proc.ic
│      os0thread-create.h
│      os0thread.h
│      page0cur.h
│      page0cur.ic
│      page0page.h
│      page0page.ic
│      page0size.h
│      page0types.h
│      page0zip.h
│      page0zip.ic
│      pars0grm.h
│      pars0opt.h
│      pars0opt.ic
│      pars0pars.h
│      pars0pars.ic
│      pars0sym.h
│      pars0sym.ic
│      pars0types.h
│      que0que.h
│      que0que.ic
│      que0types.h
│      read0read.h
│      read0types.h
│      rem0cmp.h
│      rem0cmp.ic
│      rem0rec.h
│      rem0rec.ic
│      rem0types.h
│      row0ext.h
│      row0ext.ic
│      row0ftsort.h
│      row0import.h
│      row0import.ic
│      row0ins.h
│      row0ins.ic
│      row0log.h
│      row0log.ic
│      row0merge.h
│      row0mysql.h
│      row0mysql.ic
│      row0pread-adapter.h
│      row0pread-histogram.h
│      row0pread.h
│      row0purge.h
│      row0purge.ic
│      row0quiesce.h
│      row0quiesce.ic
│      row0row.h
│      row0row.ic
│      row0sel.h
│      row0sel.ic
│      row0types.h
│      row0uins.h
│      row0uins.ic
│      row0umod.h
│      row0umod.ic
│      row0undo.h
│      row0undo.ic
│      row0upd.h
│      row0upd.ic
│      row0vers.h
│      row0vers.ic
│      sess0sess.h
│      srv0conc.h
│      srv0mon.h
│      srv0mon.ic
│      srv0srv.h
│      srv0srv.ic
│      srv0start.h
│      srv0tmp.h
│      sync0arr.h
│      sync0arr.ic
│      sync0debug.h
│      sync0policy.h
│      sync0policy.ic
│      sync0rw.h
│      sync0rw.ic
│      sync0sharded_rw.h
│      sync0sync.h
│      sync0types.h
│      trx0i_s.h
│      trx0purge.h
│      trx0purge.ic
│      trx0rec.h
│      trx0rec.ic
│      trx0roll.h
│      trx0roll.ic
│      trx0rseg.h
│      trx0rseg.ic
│      trx0sys.h
│      trx0sys.ic
│      trx0trx.h
│      trx0trx.ic
│      trx0types.h
│      trx0undo.h
│      trx0undo.ic
│      trx0xa.h
│      univ.i
│      usr0sess.h
│      usr0sess.ic
│      usr0types.h
│      ut0bitset.h
│      ut0bool_scope_guard.h
│      ut0byte.h
│      ut0byte.ic
│      ut0counter.h
│      ut0crc32.h
│      ut0dbg.h
│      ut0link_buf.h
│      ut0list.h
│      ut0list.ic
│      ut0lock_free_hash.h
│      ut0lst.h
│      ut0mem.h
│      ut0mem.ic
│      ut0mpmcbq.h
│      ut0mutex.h
│      ut0mutex.ic
│      ut0new.h
│      ut0pool.h
│      ut0rbt.h
│      ut0rnd.h
│      ut0rnd.ic
│      ut0sort.h
│      ut0stage.h
│      ut0ut.h
│      ut0ut.ic
│      ut0vec.h
│      ut0vec.ic
│      ut0wqueue.h
│      zlob0first.h
│      zlob0index.h
│      zlob0read.h
│      
├─lob
│      lob0del.cc
│      lob0first.cc
│      lob0impl.cc
│      lob0index.cc
│      lob0ins.cc
│      lob0lob.cc
│      lob0pages.cc
│      lob0purge.cc
│      lob0undo.cc
│      lob0update.cc
│      lob0util.cc
│      zlob0first.cc
│      zlob0index.cc
│      zlob0ins.cc
│      zlob0read.cc
│      zlob0update.cc
│      
├─lock
│      lock0iter.cc
│      lock0lock.cc
│      lock0prdt.cc
│      lock0wait.cc
│      
├─log
│      log0buf.cc
│      log0chkp.cc
│      log0ddl.cc
│      log0log.cc
│      log0meb.cc
│      log0recv.cc
│      log0test.cc
│      log0write.cc
│      
├─mach
│      mach0data.cc
│      
├─mem
│      memory.cc
│      
├─mtr
│      mtr0log.cc
│      mtr0mtr.cc
│      
├─os
│      file.cc
│      file.h
│      os0event.cc
│      os0file.cc
│      os0proc.cc
│      os0thread.cc
│      
├─page
│      page.ic
│      page0cur.cc
│      page0page.cc
│      page0zip.cc
│      zipdecompress.cc
│      zipdecompress.h
│      zipdecompress.ic
│      
├─pars
│      lexyy.cc
│      make_bison.sh
│      make_flex.sh
│      pars0grm.cc
│      pars0grm.y
│      pars0lex.l
│      pars0opt.cc
│      pars0pars.cc
│      pars0sym.cc
│      
├─que
│      que0que.cc
│      
├─read
│      read0read.cc
│      
├─rem
│      rec.cc
│      rec.h
│      rem0cmp.cc
│      rem0rec.cc
│      
├─row
│      row0ext.cc
│      row0ftsort.cc
│      row0import.cc
│      row0ins.cc
│      row0log.cc
│      row0merge.cc
│      row0mysql.cc
│      row0pread-adapter.cc
│      row0pread-histogram.cc
│      row0pread.cc
│      row0purge.cc
│      row0quiesce.cc
│      row0row.cc
│      row0sel.cc
│      row0uins.cc
│      row0umod.cc
│      row0undo.cc
│      row0upd.cc
│      row0vers.cc
│      
├─srv
│      srv0conc.cc
│      srv0mon.cc
│      srv0srv.cc
│      srv0start.cc
│      srv0tmp.cc
│      
├─sync
│      sync0arr.cc
│      sync0debug.cc
│      sync0rw.cc
│      sync0sync.cc
│      
├─trx
│      trx0i_s.cc
│      trx0purge.cc
│      trx0rec.cc
│      trx0roll.cc
│      trx0rseg.cc
│      trx0sys.cc
│      trx0trx.cc
│      trx0undo.cc
│      
├─usr
│      usr0sess.cc
│      
└─ut
        crc32.cc
        ut.cc
        ut.h
        ut0dbg.cc
        ut0list.cc
        ut0mem.cc
        ut0new.cc
        ut0rbt.cc
        ut0rnd.cc
        ut0ut.cc
        ut0vec.cc
        ut0wqueue.cc
```
