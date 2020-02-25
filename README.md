# postgres s3 fdw for csv 

## Usage

* init datas

```code
docker-compose up -d
```

* login  minio &&  add bucket_name && upload files

```code
bucket_name: apps
upload  users.csv
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
CREATE FOREIGN TABLE apps
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
```

## Notes

Dockerfile && source code [s3_fdw_py](https://github.com/smomni/s3_fdw_py)

