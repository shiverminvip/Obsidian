root@hcss-ecs-2de8:~/maple079-docker# docker-compose logs db
Attaching to maplestory-db
maplestory-db | 2025-06-27 03:39:09+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.44-1.el7 started.
maplestory-db | 2025-06-27 03:39:09+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
maplestory-db | 2025-06-27 03:39:09+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.44-1.el7 started.
maplestory-db | 2025-06-27 03:39:09+00:00 [Note] [Entrypoint]: Initializing database files
maplestory-db | 2025-06-27T03:39:09.731675Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
maplestory-db | 2025-06-27T03:39:09.970656Z 0 [Warning] InnoDB: New log files created, LSN=45790
maplestory-db | 2025-06-27T03:39:10.017479Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
maplestory-db | 2025-06-27T03:39:10.034127Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 4922b755-5308-11f0-bab7-0242ac120002.
maplestory-db | 2025-06-27T03:39:10.036763Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
maplestory-db | 2025-06-27T03:39:10.157485Z 0 [Warning] A deprecated TLS version TLSv1 is enabled. Please use TLSv1.2 or higher.
maplestory-db | 2025-06-27T03:39:10.157698Z 0 [Warning] A deprecated TLS version TLSv1.1 is enabled. Please use TLSv1.2 or higher.
maplestory-db | 2025-06-27T03:39:10.158278Z 0 [Warning] CA certificate ca.pem is self signed.
maplestory-db | 2025-06-27T03:39:10.185957Z 1 [Warning] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
maplestory-db | 2025-06-27 03:39:12+00:00 [Note] [Entrypoint]: Database files initialized
maplestory-db | 2025-06-27 03:39:12+00:00 [Note] [Entrypoint]: Starting temporary server
maplestory-db | 2025-06-27 03:39:12+00:00 [Note] [Entrypoint]: Waiting for server startup
maplestory-db | 2025-06-27T03:39:13.188694Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
maplestory-db | 2025-06-27T03:39:13.190409Z 0 [Note] mysqld (mysqld 5.7.44) starting as process 124 ...
maplestory-db | 2025-06-27T03:39:13.194179Z 0 [Note] InnoDB: PUNCH HOLE support available
maplestory-db | 2025-06-27T03:39:13.194201Z 0 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
maplestory-db | 2025-06-27T03:39:13.194205Z 0 [Note] InnoDB: Uses event mutexes
maplestory-db | 2025-06-27T03:39:13.194207Z 0 [Note] InnoDB: GCC builtin __atomic_thread_fence() is used for memory barrier
maplestory-db | 2025-06-27T03:39:13.194209Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.13
maplestory-db | 2025-06-27T03:39:13.194214Z 0 [Note] InnoDB: Using Linux native AIO
maplestory-db | 2025-06-27T03:39:13.194462Z 0 [Note] InnoDB: Number of pools: 1
maplestory-db | 2025-06-27T03:39:13.194601Z 0 [Note] InnoDB: Using CPU crc32 instructions
maplestory-db | 2025-06-27T03:39:13.196689Z 0 [Note] InnoDB: Initializing buffer pool, total size = 128M, instances = 1, chunk size = 128M
maplestory-db | 2025-06-27T03:39:13.205565Z 0 [Note] InnoDB: Completed initialization of buffer pool
maplestory-db | 2025-06-27T03:39:13.207751Z 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
maplestory-db | 2025-06-27T03:39:13.219676Z 0 [Note] InnoDB: Highest supported file format is Barracuda.
maplestory-db | 2025-06-27T03:39:13.231067Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
maplestory-db | 2025-06-27T03:39:13.231127Z 0 [Note] InnoDB: Setting file './ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
maplestory-db | 2025-06-27T03:39:13.255723Z 0 [Note] InnoDB: File './ibtmp1' size is now 12 MB.
maplestory-db | 2025-06-27T03:39:13.256566Z 0 [Note] InnoDB: 96 redo rollback segment(s) found. 96 redo rollback segment(s) are active.
maplestory-db | 2025-06-27T03:39:13.256581Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are active.
maplestory-db | 2025-06-27T03:39:13.256958Z 0 [Note] InnoDB: Waiting for purge to start
maplestory-db | 2025-06-27T03:39:13.307155Z 0 [Note] InnoDB: 5.7.44 started; log sequence number 2768291
maplestory-db | 2025-06-27T03:39:13.307492Z 0 [Note] InnoDB: Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
maplestory-db | 2025-06-27T03:39:13.308446Z 0 [Note] Plugin 'FEDERATED' is disabled.
maplestory-db | 2025-06-27T03:39:13.309885Z 0 [Note] InnoDB: Buffer pool(s) load completed at 250627  3:39:13
maplestory-db | 2025-06-27T03:39:13.316532Z 0 [Note] Found ca.pem, server-cert.pem and server-key.pem in data directory. Trying to enable SSL support using them.
maplestory-db | 2025-06-27T03:39:13.316545Z 0 [Note] Skipping generation of SSL certificates as certificate files are present in data directory.
maplestory-db | 2025-06-27T03:39:13.316549Z 0 [Warning] A deprecated TLS version TLSv1 is enabled. Please use TLSv1.2 or higher.
maplestory-db | 2025-06-27T03:39:13.316551Z 0 [Warning] A deprecated TLS version TLSv1.1 is enabled. Please use TLSv1.2 or higher.
maplestory-db | 2025-06-27T03:39:13.318290Z 0 [Warning] CA certificate ca.pem is self signed.
maplestory-db | 2025-06-27T03:39:13.318338Z 0 [Note] Skipping generation of RSA key pair as key files are present in data directory.
maplestory-db | 2025-06-27T03:39:13.321426Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
maplestory-db | 2025-06-27T03:39:13.331393Z 0 [Note] Event Scheduler: Loaded 0 events
maplestory-db | 2025-06-27T03:39:13.331736Z 0 [Note] mysqld: ready for connections.
maplestory-db | Version: '5.7.44'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server (GPL)
maplestory-db | 2025-06-27 03:39:14+00:00 [Note] [Entrypoint]: Temporary server started.
maplestory-db | '/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
maplestory-db | 2025-06-27T03:39:14.105499Z 3 [Note] InnoDB: Stopping purge
maplestory-db | 2025-06-27T03:39:14.114391Z 3 [Note] InnoDB: Resuming purge
maplestory-db | 2025-06-27T03:39:14.118294Z 3 [Note] InnoDB: Stopping purge
maplestory-db | 2025-06-27T03:39:14.125234Z 3 [Note] InnoDB: Resuming purge
maplestory-db | 2025-06-27T03:39:14.129278Z 3 [Note] InnoDB: Stopping purge
maplestory-db | 2025-06-27T03:39:14.134709Z 3 [Note] InnoDB: Resuming purge
maplestory-db | 2025-06-27T03:39:14.137039Z 3 [Note] InnoDB: Stopping purge
maplestory-db | 2025-06-27T03:39:14.142161Z 3 [Note] InnoDB: Resuming purge
maplestory-db | Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
maplestory-db | Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
maplestory-db | Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
maplestory-db | Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
maplestory-db | Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
maplestory-db | 2025-06-27 03:39:15+00:00 [Note] [Entrypoint]: Creating database maplestory_079
maplestory-db | 
maplestory-db | 2025-06-27 03:39:15+00:00 [Note] [Entrypoint]: /usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/init.sql
maplestory-db | 
maplestory-db | 
maplestory-db | 2025-06-27 03:39:18+00:00 [Note] [Entrypoint]: Stopping temporary server
maplestory-db | 2025-06-27T03:39:18.331714Z 0 [Note] Giving 0 client threads a chance to die gracefully
maplestory-db | 2025-06-27T03:39:18.331740Z 0 [Note] Shutting down slave threads
maplestory-db | 2025-06-27T03:39:18.331743Z 0 [Note] Forcefully disconnecting 0 remaining clients
maplestory-db | 2025-06-27T03:39:18.331748Z 0 [Note] Event Scheduler: Purging the queue. 0 events
maplestory-db | 2025-06-27T03:39:18.331889Z 0 [Note] Binlog end
maplestory-db | 2025-06-27T03:39:18.332410Z 0 [Note] Shutting down plugin 'ngram'
maplestory-db | 2025-06-27T03:39:18.332421Z 0 [Note] Shutting down plugin 'partition'
maplestory-db | 2025-06-27T03:39:18.332424Z 0 [Note] Shutting down plugin 'BLACKHOLE'
maplestory-db | 2025-06-27T03:39:18.332427Z 0 [Note] Shutting down plugin 'ARCHIVE'
maplestory-db | 2025-06-27T03:39:18.332429Z 0 [Note] Shutting down plugin 'PERFORMANCE_SCHEMA'
maplestory-db | 2025-06-27T03:39:18.332532Z 0 [Note] Shutting down plugin 'MRG_MYISAM'
maplestory-db | 2025-06-27T03:39:18.332541Z 0 [Note] Shutting down plugin 'MyISAM'
maplestory-db | 2025-06-27T03:39:18.332546Z 0 [Note] Shutting down plugin 'INNODB_SYS_VIRTUAL'
maplestory-db | 2025-06-27T03:39:18.332549Z 0 [Note] Shutting down plugin 'INNODB_SYS_DATAFILES'
maplestory-db | 2025-06-27T03:39:18.332551Z 0 [Note] Shutting down plugin 'INNODB_SYS_TABLESPACES'
maplestory-db | 2025-06-27T03:39:18.332607Z 0 [Note] Shutting down plugin 'INNODB_SYS_FOREIGN_COLS'
maplestory-db | 2025-06-27T03:39:18.332610Z 0 [Note] Shutting down plugin 'INNODB_SYS_FOREIGN'
maplestory-db | 2025-06-27T03:39:18.332613Z 0 [Note] Shutting down plugin 'INNODB_SYS_FIELDS'
maplestory-db | 2025-06-27T03:39:18.332615Z 0 [Note] Shutting down plugin 'INNODB_SYS_COLUMNS'
maplestory-db | 2025-06-27T03:39:18.332616Z 0 [Note] Shutting down plugin 'INNODB_SYS_INDEXES'
maplestory-db | 2025-06-27T03:39:18.332618Z 0 [Note] Shutting down plugin 'INNODB_SYS_TABLESTATS'
maplestory-db | 2025-06-27T03:39:18.332620Z 0 [Note] Shutting down plugin 'INNODB_SYS_TABLES'
maplestory-db | 2025-06-27T03:39:18.332622Z 0 [Note] Shutting down plugin 'INNODB_FT_INDEX_TABLE'
maplestory-db | 2025-06-27T03:39:18.332624Z 0 [Note] Shutting down plugin 'INNODB_FT_INDEX_CACHE'
maplestory-db | 2025-06-27T03:39:18.332628Z 0 [Note] Shutting down plugin 'INNODB_FT_CONFIG'
maplestory-db | 2025-06-27T03:39:18.332630Z 0 [Note] Shutting down plugin 'INNODB_FT_BEING_DELETED'
maplestory-db | 2025-06-27T03:39:18.332634Z 0 [Note] Shutting down plugin 'INNODB_FT_DELETED'
maplestory-db | 2025-06-27T03:39:18.332637Z 0 [Note] Shutting down plugin 'INNODB_FT_DEFAULT_STOPWORD'
maplestory-db | 2025-06-27T03:39:18.332640Z 0 [Note] Shutting down plugin 'INNODB_METRICS'
maplestory-db | 2025-06-27T03:39:18.332643Z 0 [Note] Shutting down plugin 'INNODB_TEMP_TABLE_INFO'
maplestory-db | 2025-06-27T03:39:18.332646Z 0 [Note] Shutting down plugin 'INNODB_BUFFER_POOL_STATS'
maplestory-db | 2025-06-27T03:39:18.332650Z 0 [Note] Shutting down plugin 'INNODB_BUFFER_PAGE_LRU'
maplestory-db | 2025-06-27T03:39:18.332653Z 0 [Note] Shutting down plugin 'INNODB_BUFFER_PAGE'
maplestory-db | 2025-06-27T03:39:18.332656Z 0 [Note] Shutting down plugin 'INNODB_CMP_PER_INDEX_RESET'
maplestory-db | 2025-06-27T03:39:18.332659Z 0 [Note] Shutting down plugin 'INNODB_CMP_PER_INDEX'
maplestory-db | 2025-06-27T03:39:18.332662Z 0 [Note] Shutting down plugin 'INNODB_CMPMEM_RESET'
maplestory-db | 2025-06-27T03:39:18.332664Z 0 [Note] Shutting down plugin 'INNODB_CMPMEM'
maplestory-db | 2025-06-27T03:39:18.332716Z 0 [Note] Shutting down plugin 'INNODB_CMP_RESET'
maplestory-db | 2025-06-27T03:39:18.332743Z 0 [Note] Shutting down plugin 'INNODB_CMP'
maplestory-db | 2025-06-27T03:39:18.332766Z 0 [Note] Shutting down plugin 'INNODB_LOCK_WAITS'
maplestory-db | 2025-06-27T03:39:18.332802Z 0 [Note] Shutting down plugin 'INNODB_LOCKS'
maplestory-db | 2025-06-27T03:39:18.332845Z 0 [Note] Shutting down plugin 'INNODB_TRX'
maplestory-db | 2025-06-27T03:39:18.332856Z 0 [Note] Shutting down plugin 'InnoDB'
maplestory-db | 2025-06-27T03:39:18.332936Z 0 [Note] InnoDB: FTS optimize thread exiting.
maplestory-db | 2025-06-27T03:39:18.333108Z 0 [Note] InnoDB: Starting shutdown...
maplestory-db | 2025-06-27T03:39:18.433300Z 0 [Note] InnoDB: Dumping buffer pool(s) to /var/lib/mysql/ib_buffer_pool
maplestory-db | 2025-06-27T03:39:18.433761Z 0 [Note] InnoDB: Buffer pool(s) dump completed at 250627  3:39:18
maplestory-db | 2025-06-27T03:39:20.350610Z 0 [Note] InnoDB: Shutdown completed; log sequence number 15199711
maplestory-db | 2025-06-27T03:39:20.353849Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
maplestory-db | 2025-06-27T03:39:20.353871Z 0 [Note] Shutting down plugin 'MEMORY'
maplestory-db | 2025-06-27T03:39:20.353879Z 0 [Note] Shutting down plugin 'CSV'
maplestory-db | 2025-06-27T03:39:20.353883Z 0 [Note] Shutting down plugin 'sha256_password'
maplestory-db | 2025-06-27T03:39:20.353885Z 0 [Note] Shutting down plugin 'mysql_native_password'
maplestory-db | 2025-06-27T03:39:20.354011Z 0 [Note] Shutting down plugin 'binlog'
maplestory-db | 2025-06-27T03:39:20.356784Z 0 [Note] mysqld: Shutdown complete
maplestory-db | 
maplestory-db | 2025-06-27 03:39:21+00:00 [Note] [Entrypoint]: Temporary server stopped
maplestory-db | 
maplestory-db | 2025-06-27 03:39:21+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.
maplestory-db | 
maplestory-db | 2025-06-27T03:39:21.526854Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
maplestory-db | 2025-06-27T03:39:21.528465Z 0 [Note] mysqld (mysqld 5.7.44) starting as process 1 ...
maplestory-db | 2025-06-27T03:39:21.531580Z 0 [Note] InnoDB: PUNCH HOLE support available
maplestory-db | 2025-06-27T03:39:21.531602Z 0 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
maplestory-db | 2025-06-27T03:39:21.531605Z 0 [Note] InnoDB: Uses event mutexes
maplestory-db | 2025-06-27T03:39:21.531608Z 0 [Note] InnoDB: GCC builtin __atomic_thread_fence() is used for memory barrier
maplestory-db | 2025-06-27T03:39:21.531612Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.13
maplestory-db | 2025-06-27T03:39:21.531615Z 0 [Note] InnoDB: Using Linux native AIO
maplestory-db | 2025-06-27T03:39:21.531850Z 0 [Note] InnoDB: Number of pools: 1
maplestory-db | 2025-06-27T03:39:21.531967Z 0 [Note] InnoDB: Using CPU crc32 instructions
maplestory-db | 2025-06-27T03:39:21.533571Z 0 [Note] InnoDB: Initializing buffer pool, total size = 128M, instances = 1, chunk size = 128M
maplestory-db | 2025-06-27T03:39:21.540855Z 0 [Note] InnoDB: Completed initialization of buffer pool
maplestory-db | 2025-06-27T03:39:21.542984Z 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
maplestory-db | 2025-06-27T03:39:21.556691Z 0 [Note] InnoDB: Highest supported file format is Barracuda.
maplestory-db | 2025-06-27T03:39:21.575704Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
maplestory-db | 2025-06-27T03:39:21.575829Z 0 [Note] InnoDB: Setting file './ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
maplestory-db | 2025-06-27T03:39:21.601551Z 0 [Note] InnoDB: File './ibtmp1' size is now 12 MB.
maplestory-db | 2025-06-27T03:39:21.602322Z 0 [Note] InnoDB: 96 redo rollback segment(s) found. 96 redo rollback segment(s) are active.
maplestory-db | 2025-06-27T03:39:21.602334Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are active.
maplestory-db | 2025-06-27T03:39:21.602745Z 0 [Note] InnoDB: 5.7.44 started; log sequence number 15199711
maplestory-db | 2025-06-27T03:39:21.603007Z 0 [Note] Plugin 'FEDERATED' is disabled.
maplestory-db | 2025-06-27T03:39:21.608192Z 0 [Note] InnoDB: Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
maplestory-db | 2025-06-27T03:39:21.608634Z 0 [Note] Found ca.pem, server-cert.pem and server-key.pem in data directory. Trying to enable SSL support using them.
maplestory-db | 2025-06-27T03:39:21.608714Z 0 [Note] Skipping generation of SSL certificates as certificate files are present in data directory.
maplestory-db | 2025-06-27T03:39:21.608738Z 0 [Warning] A deprecated TLS version TLSv1 is enabled. Please use TLSv1.2 or higher.
maplestory-db | 2025-06-27T03:39:21.608762Z 0 [Warning] A deprecated TLS version TLSv1.1 is enabled. Please use TLSv1.2 or higher.
maplestory-db | 2025-06-27T03:39:21.609356Z 0 [Warning] CA certificate ca.pem is self signed.
maplestory-db | 2025-06-27T03:39:21.609440Z 0 [Note] Skipping generation of RSA key pair as key files are present in data directory.
maplestory-db | 2025-06-27T03:39:21.609789Z 0 [Note] Server hostname (bind-address): '*'; port: 3306
maplestory-db | 2025-06-27T03:39:21.609861Z 0 [Note] IPv6 is available.
maplestory-db | 2025-06-27T03:39:21.610867Z 0 [Note]   - '::' resolves to '::';
maplestory-db | 2025-06-27T03:39:21.610955Z 0 [Note] Server socket created on IP: '::'.
maplestory-db | 2025-06-27T03:39:21.613407Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
maplestory-db | 2025-06-27T03:39:21.614005Z 0 [Note] InnoDB: Buffer pool(s) load completed at 250627  3:39:21
maplestory-db | 2025-06-27T03:39:21.621723Z 0 [Note] Event Scheduler: Loaded 0 events
maplestory-db | 2025-06-27T03:39:21.621948Z 0 [Note] mysqld: ready for connections.
maplestory-db | Version: '5.7.44'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
