# antalya-s3-cache




1. Deploy the cache (configured to cache `aws-public-blockchain.s3.us-east-2.amazonaws.com` bucket)
    ```shell
    kubectl apply -f cache.yaml
    ```
2. Configure clickhouse to use the cache as proxy
    ```xml
   <clickhouse>
    <proxy>
        <disable_tunneling_for_https_requests_over_http_proxy>1</disable_tunneling_for_https_requests_over_http_proxy>
        <https>
            <uri>http://s3-cache</uri>
        </https>
        <http>
            <uri>http://s3-cache</uri>
        </http>
    </proxy>
    </clickhouse> 
    ```
3. Use the cache
    ```sql
    SELECT date, sum(output_count)
    FROM s3('s3://aws-public-blockchain/v1.0/btc/transactions/date=2024-02*/*.parquet', NOSIGN)
    GROUP BY date ORDER BY date
    ```


### Utils



#### Drop filesystem cache on all shards
```sql
system drop filesystem cache on cluster 'all-sharded'
```