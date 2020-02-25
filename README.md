# postgres s3 fdw for csv 

## Usage

* init datas

```code
docker-compose up -d
```

* login  minio &&  add bucket_name && upload files

```code
bucket_name: apps
upload  users.csv && apps.csv
```

```code
// 创建扩展
CREATE EXTENSION multicorn;
//  创建 
CREATE SERVER multicorn_es FOREIGN DATA WRAPPER multicorn
OPTIONS (
  wrapper 's3_fdw.S3ForeignDataWrapper'
);
//  创建外部表映射
CREATE FOREIGN TABLE users
    (
        name text,
        age int,
        tel TEXT,
        app TEXT
    )
SERVER multicorn_es
OPTIONS
    (
        bucket_name 'apps',
        object_name 'users.csv',
        access_key 'dalongrong',
        secret_key 'dalongrong',
        endpoint_url 'http://minio:9000'
    );

CREATE FOREIGN TABLE apps
    (
        name text,
        version text
    )
SERVER multicorn_es
OPTIONS
    (
        bucket_name 'apps',
        object_name 'apps.csv',
        access_key 'dalongrong',
        secret_key 'dalongrong',
        endpoint_url 'http://minio:9000'
    );
// with join 
select * from users a join apps b on a.app=b.name;

```


## with postgres fdw

```code
//  创建扩展
create extension postgres_fdw;
// 创建server
CREATE SERVER pg_server
  FOREIGN DATA WRAPPER postgres_fdw
  OPTIONS (host 'postgres', dbname 'postgres');
// 创建用户映射
CREATE USER MAPPING FOR postgres
  SERVER pg_server
  OPTIONS (user 'postgres', password 'dalong');
// 创建schema
CREATE SCHEMA app;
// 导入schema
IMPORT FOREIGN SCHEMA public
  FROM SERVER pg_server
  INTO app;
// 数据查询
select * from app.articles_es;
```

## Notes

Dockerfile && source code [s3_fdw_py](https://github.com/smomni/s3_fdw_py)

