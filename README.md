# antalya-s3-cache


1. Update `cache.yaml` `s3-cache` deployment (optional)
   * Update/remove nodeSelector (prefer VMs with fast network and storage)     
     ```
     nodeSelector:
        node.kubernetes.io/instance-type: r8g.large
     ```
   * Env configuration
   
   | Environment Variable         | Description                                                   | Default Value |
   |------------------------------|---------------------------------------------------------------|---------------|
   | `PROXY_CACHE_VALID_OK`       | Cache validity duration for HTTP 200 responses.               | `30d`         |
   | `PROXY_CACHE_VALID_LIST_OK`  | Cache validity duration for list operations.                  | `5m`          |
   | `PROXY_CACHE_VALID_NOT_FOUND`| Cache validity duration for HTTP 404 responses.               | `1m`          |
   | `PROXY_CACHE_SLICE`          | Size of cache slices for s3 range queries                     | `16m`         |
   | `PROXY_CACHE_SIZE`           | Maximum size of the proxy cache, should be less than PVC size | `100g`        |
   | `DEFAULT_S3_REGION`          | Default AWS S3 region.                                        | `us-east-2`   |
   
   

4. Deploy the cache
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