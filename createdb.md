```SQL
m0=> CREATE TABLE Classes (
m0(> class varchar(50) CONSTRAINT firstkey PRIMARY KEY,
m0(> type  varchar(2)  NOT NULL,
m0(> country varchar(20) NOT NULL,
m0(> numGuns tinyint NULL,
m0(> bore    real    NULL,
m0(> displacement int NULL
m0(> );
ERROR:  type "tinyint" does not exist
LINE 5: numGuns tinyint NULL,
                ^
m0=> CREATE TABLE Classes (
class varchar(50) CONSTRAINT firstkey PRIMARY KEY,
type  varchar(2)  NOT NULL,
country varchar(20) NOT NULL,
numGuns smallint NULL,
bore    real    NULL,
displacement int NULL
);
CREATE TABLE

m0=> \d+
                     List of relations
 Schema |  Name   | Type  |  Owner  |  Size   | Description
--------+---------+-------+---------+---------+-------------
 public | classes | table | student | 0 bytes |
(1 row)

m0=> SELECT * FROM classes;
 class | type | country | numguns | bore | displacement
-------+------+---------+---------+------+--------------
(0 rows)

m0=> CREATE TABLE Ships (
m0(> name varchar(50) CONSTRAINT firstkey PRIMARY KEY,
m0(> class varchar(50) REFERENCES classes,
m0(> launched smallint NULL
m0(> )
m0-> ;
ERROR:  relation "firstkey" already exists
m0=> CREATE TABLE Ships (
name varchar(50) CONSTRAINT shipskey PRIMARY KEY,
class varchar(50) REFERENCES classes,
launched smallint NULL
)
;
CREATE TABLE
m0=> CREATE TABLE Outcomes (
m0(> ship varchar(50) CONSTRAINT outkey PRIMARY KEY,
m0(> ^C
m0=> ^C
m0=> CREATE TABLE Battles (
m0(> name varchar(20) CONSTRAINT battleskey PRIMARY KEY,
m0(> date datetime NOT NULL
m0(> );
ERROR:  type "datetime" does not exist
LINE 3: date datetime NOT NULL
             ^
m0=> CREATE TABLE Battles (
name varchar(20) CONSTRAINT battleskey PRIMARY KEY,
date date NOT NULL
);
CREATE TABLE
m0=> CREATE TABLE Outcomes (
m0(> ship varchar(50) CONSTRAINT outcomeskey PRIMARY KEY,
m0(> battle varchar(20) REFERENCES battles (name),
m0(> result varchar(10) NOT NULL
m0(> );
CREATE TABLE
m0=>
m0=>
m0=> INSERT INTO battles
m0-> VALUES ('#Cuba62a', '1962-10-20'), ('#Cuba62b', '1962-10-25');
INSERT 0 2
m0=> SELECT * FROM battles;
   name   |    date
----------+------------
 #Cuba62a | 1962-10-20
 #Cuba62b | 1962-10-25
(2 rows)

m0=> INSERT INTO battles
VALUES ('Guadalcanal', '1942-11-15'), ('North Atlantic', '1941-05-25'), ('North           Cape', '1943-12-26'), ('Surigao Strait', '1944-10-25');
INSERT 0 4
m0=> SELECT * FROM battles;
      name      |    date
----------------+------------
 #Cuba62a       | 1962-10-20
 #Cuba62b       | 1962-10-25
 Guadalcanal    | 1942-11-15
 North Atlantic | 1941-05-25
 North Cape     | 1943-12-26
 Surigao Strait | 1944-10-25
(6 rows)

m0=> SELECT * FROM Battles;
      name      |    date
----------------+------------
 #Cuba62a       | 1962-10-20
 #Cuba62b       | 1962-10-25
 Guadalcanal    | 1942-11-15
 North Atlantic | 1941-05-25
 North Cape     | 1943-12-26
 Surigao Strait | 1944-10-25
(6 rows)

m0=> INSERT INTO outcomes
VALUES ('Bismarck', 'North Atlantic', 'sunk'), ('California', 'Guadalcanal', 'da          maged'), ('CAlifornia', 'Surigao Strait','ok'), ('Duke of York', 'North Cape', '          ok');
INSERT 0 4

m0=> SELECT * FROM outcomes
m0-> ;
     ship     |     battle     | result
--------------+----------------+---------
 Bismarck     | North Atlantic | sunk
 California   | Guadalcanal    | damaged
 CAlifornia   | Surigao Strait | ok
 Duke of York | North Cape     | ok
(4 rows)

m0=> INSERT INTO outcomes
VALUES ('Fuso', 'Surigao Strait', 'sunk'), ('Hood', 'North Atlantic', 'sunk'), ('King George V', 'North Atlantic','ok'), ('Kirishima', 'Guadalcanal', 'sunk');            
INSERT 0 4

m0=> INSERT INTO outcomes
m0-> VALUES ('dgsd', 'dsgfsdfg', 'dsgsd');
ERROR:  insert or update on table "outcomes" violates foreign key constraint "outcomes_battle_fkey"
DETAIL:  Key (battle)=(dsgfsdfg) is not present in table "battles".

m0=> EXPLAIN SELECT * FROM outcomes;
                         QUERY PLAN
-------------------------------------------------------------
 Seq Scan on outcomes  (cost=0.00..13.30 rows=330 width=214)
(1 row)

m0=> EXPLAIN ANALYZE SELECT * FROM outcomes;

m0=> CREATE TABLE foo (c1 integer, c2 text);
CREATE TABLE

m0=> INSERT INTO foo
  SELECT i, md5(random()::text)
  FROM generate_series(1, 100000) AS i;
INSERT 0 100000
m0=> SELECT COUNT(*) FROM foo
m0-> ;
 count
--------
 100000
(1 row)

m0=> EXPLAIN SELECT * FROM foo;
                         QUERY PLAN
------------------------------------------------------------
 Seq Scan on foo  (cost=0.00..5721.00 rows=100000 width=37)
(1 row)

m0=> INSERT INTO foo
m0->   SELECT i, md5(random()::text)
m0->   FROM generate_series(1, 10) AS i;
INSERT 0 10

m0=> EXPLAIN SELECT * FROM foo;
                         QUERY PLAN
------------------------------------------------------------
 Seq Scan on foo  (cost=0.00..5721.00 rows=100000 width=37)
(1 row)

m0=> ANALYZE foo;
ANALYZE
m0=> EXPLAIN SELECT * FROM foo;
                         QUERY PLAN
------------------------------------------------------------
 Seq Scan on foo  (cost=0.00..5721.10 rows=100010 width=37)
(1 row)

m0=> EXPLAIN SELECT * FROM foo WHERE c1 > 500;
                        QUERY PLAN
-----------------------------------------------------------
 Seq Scan on foo  (cost=0.00..5971.12 rows=99533 width=37)
   Filter: (c1 > 500)
(2 rows)

m0=> CREATE INDEX ON foo(c1);
CREATE INDEX

m0=> EXPLAIN SELECT * FROM foo WHERE c1 < 500;
                                QUERY PLAN
--------------------------------------------------------------------------
 Index Scan using foo_c1_idx on foo  (cost=0.29..42.64 rows=477 width=37)
   Index Cond: (c1 < 500)
(2 rows)

m0=> EXPLAIN SELECT * FROM foo WHERE c1 > 500;
                        QUERY PLAN
-----------------------------------------------------------
 Seq Scan on foo  (cost=0.00..5971.12 rows=99533 width=37)
   Filter: (c1 > 500)
(2 rows)

m0=> EXPLAIN SELECT * FROM foo WHERE c1 < 500;
                                QUERY PLAN
--------------------------------------------------------------------------
 Index Scan using foo_c1_idx on foo  (cost=0.29..42.64 rows=477 width=37)
   Index Cond: (c1 < 500)
(2 rows)

m0=> EXPLAIN SELECT * FROM foo
m0->         WHERE c1 < 500 AND c2 LIKE 'abcd%';
                               QUERY PLAN
------------------------------------------------------------------------
 Index Scan using foo_c1_idx on foo  (cost=0.29..43.83 rows=1 width=37)
   Index Cond: (c1 < 500)
   Filter: (c2 ~~ 'abcd%'::text)
(3 rows)
```
