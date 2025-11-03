# Research results

The reuqired services are in the koku repo:
- koku-server
- masu-server
- koku-worker
- koku-db (postgres)
- minio/S3
- redis
- unleash
- create-parquet-bucket
- trino

Ingress needs to be enhanced. the uploaded TGZ file from the metrics operator includes cost mgmt CSV files. The TGZ file should be either saved to bucket or sent via HTTP
ingress will send a POST HTTP request to masu server. the request includes the TGZ file location or the file itself
each cluster needs to be registered by [creating a source](#creating-a-source).
The koku server handles the cost mgmt api endpoints:
Costs: /reports/openshift/costs/
Compute: /reports/openshift/compute/
Memory: /reports/openshift/memory/
Storage: /reports/openshift/volumes/
Network: /reports/openshift/network/
Trino is not desired but required. the cost mgmt dev team is working on providing an alternative solution. we need to wait for them.

# creating a source
```
[koku]> curl -d '{"name": "OCP Cluster 2", "source_type": "OCP", "authentication": {"credentials": {"cluster_id": "111-002"}}}' -H "Content-Type: application/json" -X POST http://0.0.0.0:8000/api/cost-management/v1/sources/
{"id":2,"uuid":"ae9cf628-a576-4c05-9841-dadd7eacc37c","name":"OCP Cluster 2","source_type":"OCP","authentication":{"credentials":{"cluster_id":"111-002"}},"billing_source":{}}
```

# Sending uploaded metrics to cost mgmt (MASU server)

## Direct upload of file content via HTTP

```
curl -F 'file1=@my-curl-payload.2023_10.tar.gz'  http://localhost:5042/api/cost-management/v1/ingest_ocp_payload/
```

## S3 URL

```
http://localhost:5042/api/cost-management/v1/ingest_ocp_payload/?payload_name=my-payload-name.2023_10.tar.gz
```
