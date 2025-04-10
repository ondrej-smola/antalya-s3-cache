# antalya-s3-cache



# Nginx s3 range cache

https://github.com/nginx/nginx-s3-gateway/pull/215



```shell

SELECT date, sum(output_count)
FROM s3('s3://aws-public-blockchain/v1.0/btc/transactions/date=2024-02*/*.parquet', NOSIGN)
GROUP BY date ORDER BY date



SELECT date, sum(output_count)
FROM s3('s3://nginx/test/*.parquet', NOSIGN)
GROUP BY date ORDER BY date
```