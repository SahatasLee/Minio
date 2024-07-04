# Minio

minio version 5.0.15

[Deploy Operator With Helm — MinIO Object Storage for Kubernetes](https://min.io/docs/minio/kubernetes/upstream/operations/install-deploy-manage/deploy-operator-helm.html)

```bash
# Add repo to helm
helm repo add minio-operator https://operator.min.io
# Install
helm install minio-operator minio-operator/operator --version 5.0.15 -n minio 
```

## Service

```bash
# LoadBalancer
kubectl patch service -n minio console -p '
{
    "spec": {
        "type": "LoadBalancer"
    }
}'
```

## Access Token

minio operator console

```bash
kubectl -n minio-operator  get secret console-sa-secret -o jsonpath="{.data.token}" | base64 --decode
```

## Access key & Secret key

```bash
# Access key
kubectl -n minio-tenants get secrets demo-user-0 -o jsonpath="{.data.CONSOLE_ACCESS_KEY}" | base64 --decode

# Secret key
kubectl -n minio-tenants get secrets demo-user-0 -o jsonpath="{.data.CONSOLE_SECRET_KEY}" | base64 --decode
```

## Python SDK

```python
# file_uploader.py MinIO Python SDK example
from minio import Minio
from minio.error import S3Error

def main():
    # Create a client with the MinIO server playground, its access key
    # and secret key.
    client = Minio("10.111.0.120",
        access_key="BcngD7y202uX3P1i",
        secret_key="fAyrUx3hT2T8SgvQqDkwyXpvRO0SUejc",
        secure=False, # disable tls
    )

    # The file to upload, change this path if needed
    source_file = "test-file.txt"

    # The destination bucket and filename on the MinIO server
    bucket_name = "python-test-bucket"
    destination_file = "/test/my-test-file.txt"

    # Make the bucket if it doesn't exist.
    found = client.bucket_exists(bucket_name)
    if not found:
        client.make_bucket(bucket_name)
        print("Created bucket", bucket_name)
    else:
        print("Bucket", bucket_name, "already exists")

    # Upload the file, renaming it in the process
    client.fput_object(
        bucket_name, destination_file, source_file,
    )
    print(
        source_file, "successfully uploaded as object",
        destination_file, "to bucket", bucket_name,
    )

if __name__ == "__main__":
    try:
        main()
    except S3Error as exc:
        print("error occurred.", exc)
```