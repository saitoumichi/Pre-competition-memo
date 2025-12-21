
```
bash-3.2$ uv run python nplus1_transaction.py

2025-12-18 19:23:52,862 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,862 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("categories")

2025-12-18 19:23:52,862 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,863 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("jobs")

2025-12-18 19:23:52,863 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,863 INFO sqlalchemy.engine.Engine 

DROP TABLE jobs

2025-12-18 19:23:52,863 INFO sqlalchemy.engine.Engine [no key 0.00003s] ()

2025-12-18 19:23:52,863 INFO sqlalchemy.engine.Engine 

DROP TABLE categories

2025-12-18 19:23:52,863 INFO sqlalchemy.engine.Engine [no key 0.00002s] ()

2025-12-18 19:23:52,863 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("categories")

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine PRAGMA temp.table_info("categories")

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("jobs")

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine PRAGMA temp.table_info("jobs")

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine 

CREATE TABLE categories (

id INTEGER NOT NULL, 

name VARCHAR, 

PRIMARY KEY (id)

)

  

  

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine [no key 0.00002s] ()

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine 

CREATE TABLE jobs (

id INTEGER NOT NULL, 

title VARCHAR, 

category_id INTEGER, 

PRIMARY KEY (id), 

FOREIGN KEY(category_id) REFERENCES categories (id)

)

  

  

2025-12-18 19:23:52,864 INFO sqlalchemy.engine.Engine [no key 0.00002s] ()

2025-12-18 19:23:52,865 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,866 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,866 INFO sqlalchemy.engine.Engine INSERT INTO categories (name) VALUES (?) RETURNING id

2025-12-18 19:23:52,866 INFO sqlalchemy.engine.Engine [generated in 0.00004s (insertmanyvalues) 1/2 (ordered; batch not supported)] ('IT',)

2025-12-18 19:23:52,867 INFO sqlalchemy.engine.Engine INSERT INTO categories (name) VALUES (?) RETURNING id

2025-12-18 19:23:52,867 INFO sqlalchemy.engine.Engine [insertmanyvalues 2/2 (ordered; batch not supported)] ('Sales',)

2025-12-18 19:23:52,867 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,867 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,868 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,868 INFO sqlalchemy.engine.Engine [generated in 0.00005s] (1,)

2025-12-18 19:23:52,868 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,868 INFO sqlalchemy.engine.Engine [cached since 0.0005173s ago] (2,)

2025-12-18 19:23:52,869 INFO sqlalchemy.engine.Engine INSERT INTO jobs (title, category_id) VALUES (?, ?) RETURNING id

2025-12-18 19:23:52,869 INFO sqlalchemy.engine.Engine [generated in 0.00003s (insertmanyvalues) 1/3 (ordered; batch not supported)] ('Engineer', 1)

2025-12-18 19:23:52,869 INFO sqlalchemy.engine.Engine INSERT INTO jobs (title, category_id) VALUES (?, ?) RETURNING id

2025-12-18 19:23:52,869 INFO sqlalchemy.engine.Engine [insertmanyvalues 2/3 (ordered; batch not supported)] ('Designer', 1)

2025-12-18 19:23:52,869 INFO sqlalchemy.engine.Engine INSERT INTO jobs (title, category_id) VALUES (?, ?) RETURNING id

2025-12-18 19:23:52,869 INFO sqlalchemy.engine.Engine [insertmanyvalues 3/3 (ordered; batch not supported)] ('Sales Rep', 2)

2025-12-18 19:23:52,869 INFO sqlalchemy.engine.Engine COMMIT

  

=== 1 ) N+1(Bad): Get Jobs and touch job.category

2025-12-18 19:23:52,870 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,870 INFO sqlalchemy.engine.Engine SELECT jobs.id AS jobs_id, jobs.title AS jobs_title, jobs.category_id AS jobs_category_id 

FROM jobs

2025-12-18 19:23:52,870 INFO sqlalchemy.engine.Engine [generated in 0.00004s] ()

--Job & Category Name--

2025-12-18 19:23:52,870 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,870 INFO sqlalchemy.engine.Engine [generated in 0.00004s] (1,)

Engineer IT

Designer IT

2025-12-18 19:23:52,870 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,870 INFO sqlalchemy.engine.Engine [cached since 0.0002626s ago] (2,)

Sales Rep Sales

2025-12-18 19:23:52,871 INFO sqlalchemy.engine.Engine ROLLBACK

  

=== 2) N+1 (Good): Catch by only 'joinedload' ===

2025-12-18 19:23:52,871 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,871 INFO sqlalchemy.engine.Engine SELECT jobs.id AS jobs_id, jobs.title AS jobs_title, jobs.category_id AS jobs_category_id, categories_1.id AS categories_1_id, categories_1.name AS categories_1_name 

FROM jobs LEFT OUTER JOIN categories AS categories_1 ON categories_1.id = jobs.category_id

2025-12-18 19:23:52,871 INFO sqlalchemy.engine.Engine [generated in 0.00004s] ()

--Job & Category Name--

Engineer IT

Designer IT

Sales Rep Sales

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine ROLLBACK

  

=== 3)No Transaction: Partial Failure Demo ===

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine SELECT COUNT(*) FROM categories

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine [generated in 0.00003s] ()

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine SELECT COUNT(*) FROM jobs

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine [generated in 0.00003s] ()

[before] categories=2, jobs=3

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine ROLLBACK

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.name = ?

2025-12-18 19:23:52,872 INFO sqlalchemy.engine.Engine [generated in 0.00004s] ('IT',)

2025-12-18 19:23:52,873 INFO sqlalchemy.engine.Engine UPDATE categories SET name=? WHERE categories.id = ?

2025-12-18 19:23:52,873 INFO sqlalchemy.engine.Engine [generated in 0.00004s] ('IT-RENAMED', 1)

2025-12-18 19:23:52,873 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,873 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,873 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,873 INFO sqlalchemy.engine.Engine [cached since 0.005449s ago] (1,)

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine SELECT jobs.id AS jobs_id, jobs.title AS jobs_title, jobs.category_id AS jobs_category_id 

FROM jobs 

WHERE jobs.category_id = ?

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine [generated in 0.00004s] (1,)

!! simulated error: boom! (simulate crash)

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine ROLLBACK

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine [generated in 0.00004s] (1,)

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine SELECT jobs.id AS jobs_id, jobs.title AS jobs_title, jobs.category_id AS jobs_category_id 

FROM jobs 

WHERE jobs.category_id = ?

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine [cached since 0.0006385s ago] (1,)

after(no tx) category name: IT-RENAMED

after(no tx) job titles: ['Engineer', 'Designer']

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine ROLLBACK

  

=== 4)Transaction: All Rollback ===

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("categories")

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,874 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("jobs")

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine 

DROP TABLE jobs

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine [no key 0.00002s] ()

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine 

DROP TABLE categories

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine [no key 0.00002s] ()

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("categories")

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine PRAGMA temp.table_info("categories")

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine PRAGMA main.table_info("jobs")

2025-12-18 19:23:52,875 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine PRAGMA temp.table_info("jobs")

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine [raw sql] ()

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine 

CREATE TABLE categories (

id INTEGER NOT NULL, 

name VARCHAR, 

PRIMARY KEY (id)

)

  

  

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine [no key 0.00002s] ()

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine 

CREATE TABLE jobs (

id INTEGER NOT NULL, 

title VARCHAR, 

category_id INTEGER, 

PRIMARY KEY (id), 

FOREIGN KEY(category_id) REFERENCES categories (id)

)

  

  

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine [no key 0.00002s] ()

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,876 INFO sqlalchemy.engine.Engine INSERT INTO categories (name) VALUES (?) RETURNING id

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine [cached since 0.01014s ago (insertmanyvalues) 1/2 (ordered; batch not supported)] ('IT',)

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine INSERT INTO categories (name) VALUES (?) RETURNING id

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine [insertmanyvalues 2/2 (ordered; batch not supported)] ('Sales',)

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine [cached since 0.009254s ago] (1,)

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,877 INFO sqlalchemy.engine.Engine [cached since 0.009454s ago] (2,)

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine INSERT INTO jobs (title, category_id) VALUES (?, ?) RETURNING id

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine [cached since 0.008726s ago (insertmanyvalues) 1/3 (ordered; batch not supported)] ('Engineer', 1)

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine INSERT INTO jobs (title, category_id) VALUES (?, ?) RETURNING id

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine [insertmanyvalues 2/3 (ordered; batch not supported)] ('Designer', 1)

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine INSERT INTO jobs (title, category_id) VALUES (?, ?) RETURNING id

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine [insertmanyvalues 3/3 (ordered; batch not supported)] ('Sales Rep', 2)

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine COMMIT

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine SELECT COUNT(*) FROM categories

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine [cached since 0.006399s ago] ()

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine SELECT COUNT(*) FROM jobs

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine [cached since 0.006383s ago] ()

[reset] categories=2, jobs=3

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine ROLLBACK

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.name = ?

2025-12-18 19:23:52,878 INFO sqlalchemy.engine.Engine [cached since 0.006201s ago] ('IT',)

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine UPDATE categories SET name=? WHERE categories.id = ?

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine [cached since 0.005934s ago] ('IT-RENAMED', 1)

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine SELECT jobs.id AS jobs_id, jobs.title AS jobs_title, jobs.category_id AS jobs_category_id 

FROM jobs 

WHERE jobs.category_id = ?

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine [cached since 0.005268s ago] (1,)

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine ROLLBACK

!! simukalated error: boom! (simulate crash)

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine BEGIN (implicit)

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine SELECT categories.id AS categories_id, categories.name AS categories_name 

FROM categories 

WHERE categories.id = ?

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine [cached since 0.005162s ago] (1,)

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine SELECT jobs.id AS jobs_id, jobs.title AS jobs_title, jobs.category_id AS jobs_category_id 

FROM jobs 

WHERE jobs.category_id = ?

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine [cached since 0.005742s ago] (1,)

after(tx) category name: IT

after(tx) job titles: ['Engineer', 'Designer']

2025-12-18 19:23:52,879 INFO sqlalchemy.engine.Engine ROLLBACK

bash-3.2$

```

[[N＋1（悪いところ）]]

[[N＋1（良いところ）]]

[[トランザクションなし]]

[[トランザクションあり]]
