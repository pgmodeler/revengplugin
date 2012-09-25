EngReversaPlugin
----------------

Reverse engeenering plugin

SQL Commands
------------

* Roles:

    /* List all roles */
    SELECT rolname FROM pg_roles;

     /* List attributes of given role ${name} */
     SELECT rl.rolname AS name, rl.rolsuper AS superuser, rl.rolinherit AS inherit,
            rl.rolcreaterole AS createrole, rl.rolcreatedb AS createdb, rl.rolcanlogin AS login,
            rl.rolconnlimit AS connlimit, rl.rolvaliduntil AS validity, sd.description AS comment
     FROM pg_roles AS rl
     LEFT JOIN pg_shdescription AS sd ON sd.objoid = rl.oid
     WHERE rolname = '${name}';

     /* List password of given role ${name} */
     SELECT passwd FROM pg_shadow WHERE usename='${name}'

     /* List groups that given role ${name} is member */
     SELECT groname AS group
     FROM pg_group AS gr
     LEFT JOIN pg_roles AS rl ON grolist @> ARRAY[rl.oid]
     WHERE rl.rolname='${name}'

* Schemas:

     /* List all roles */
     SELECT nspname FROM pg_namespace;

     /* List attributes of given schema ${name} */
     SELECT ns.nspname AS name, sd.description FROM pg_namespace AS ns
     LEFT JOIN pg_description AS sd ON sd.objoid = ns.oid
     WHERE ns.nspname='${name}'

* Tablespaces:

     /* List all tablespaces */
     SELECT spcname AS name FROM pg_tablespace;

     /* List attributes of given tablespace ${name} */
     /* 8.0 - 9.1 */
     SELECT ts.spcname AS name, spclocation AS directory,
            rl.rolname AS owner, sd.description AS comment
     FROM pg_tablespace AS ts
     LEFT JOIN pg_roles AS rl ON rl.oid = ts.spcowner
     LEFT JOIN pg_shdescription AS sd ON sd.objoid = ts.oid
     WHERE ts.spcname = '${name}';

     /* 9.2 */
     SELECT ts.spcname AS name, pg_tablespace_location(ts.oid) AS directory,
            rl.rolname AS owner, sd.description AS comment
     FROM pg_tablespace AS ts
     LEFT JOIN pg_roles AS rl ON rl.oid = ts.spcowner
     LEFT JOIN pg_shdescription AS sd ON sd.objoid = ts.oid
     WHERE ts.spcname = '${name}';

* Databases:

     /* List all databases */
     SELECT datname AS name FROM pg_database;

     /* List attributes of given database ${name} */
     SELECT db.datname AS name, pg_encoding_to_char(db.encoding) AS encoding, rl.rolname AS owner,
            db.datcollate AS lc_collate, db.datctype AS lc_ctype, db.datconnlimit AS connlimit,
            ts.spcname AS tablespace, sd.description AS comment
     FROM pg_database AS db
     LEFT JOIN pg_tablespace AS ts ON ts.oid = db.dattablespace
     LEFT JOIN pg_description AS ds ON ds.objoid = db.oid
     LEFT JOIN pg_roles AS rl ON rl.oid = db.datdba
     LEFT JOIN pg_shdescription AS sd ON sd.objoid = db.oid
     WHERE db.datname = '${name}';

* Functions (user defined)

* Languages (user defined)

* Types (user defined)

