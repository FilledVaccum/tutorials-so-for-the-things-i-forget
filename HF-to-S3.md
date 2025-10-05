# Hugging Face to S3 Upload Tutorial

## Overview
This tutorial shows how to download models from Hugging Face Hub and upload them to Amazon S3 for deployment.

## Prerequisites
- AWS CLI configured with appropriate permissions
- Python 3.8+
- Required Python packages (see installation below)

## Code 

```python
def main():
    args = parse_args()
    hf_token = os.environ.get("HUGGING_FACE_HUB_TOKEN")
    if not hf_token:
        raise RuntimeError("Please set HUGGING_FACE_HUB_TOKEN environment variable")

    # Prepare boto3 S3 client with region
    session = boto3.session.Session(region_name=args.region)
    s3 = session.client("s3")

    # Configure multipart transfer
    chunk_bytes = args.chunk_size_mb * 1024 * 1024
    transfer_config = TransferConfig(
        multipart_threshold=chunk_bytes,
        max_concurrency=args.workers,
        multipart_chunksize=chunk_bytes,
        use_threads=True
    )

    # List model files
    list_url = f"https://huggingface.co/api/models/{args.model}"
    resp = requests.get(
        list_url,
        headers={"Authorization": f"Bearer {hf_token}"}
    )
    resp.raise_for_status()
    model_info = resp.json()
    files = model_info.get("siblings", [])

    def upload_file(file_info):
        path = file_info["rfilename"]
        download_url = f"https://huggingface.co/{args.model}/resolve/main/{path}"
        s3_key = f"{args.prefix}/{path}"

        with requests.get(download_url, headers={"Authorization": f"Bearer {hf_token}"}, stream=True) as r:
            r.raise_for_status()
            s3.upload_fileobj(
                Fileobj=r.raw,
                Bucket=args.bucket,
                Key=s3_key,
                ExtraArgs={"ACL": "bucket-owner-full-control"},
                Config=transfer_config
            )
        print(f"Uploaded: s3://{args.bucket}/{s3_key}")

    # Parallel upload
    with ThreadPoolExecutor(max_workers=args.workers) as executor:
        executor.map(upload_file, files)

if __name__ == "__main__":
    main()
```

## Installation

```bash
pip install boto3 huggingface_hub transformers torch
```

## AWS Permissions Required
Your AWS credentials need these S3 permissions:
- `s3:PutObject`
- `s3:PutObjectAcl`
- `s3:CreateBucket` (if bucket doesn't exist)
- `s3:ListBucket`

## Usage

### Basic Usage
```bash
python hf_to_s3.py --model-id microsoft/DialoGPT-medium --bucket my-models-bucket
```

### Advanced Usage
```bash
python hf_to_s3.py \
  --model-id microsoft/DialoGPT-medium \
  --bucket my-models-bucket \
  --s3-prefix models/dialogpt/ \
  --region us-west-2 \
  --cache-dir ./model_cache
```

## Command Line Arguments

| Argument | Required | Description | Default |
|----------|----------|-------------|---------|
| `--model-id` | Yes | Hugging Face model identifier | - |
| `--bucket` | Yes | S3 bucket name | - |
| `--s3-prefix` | No | S3 key prefix for uploaded files | `models/` |
| `--region` | No | AWS region | `us-east-1` |
| `--cache-dir` | No | Local cache directory | `./hf_cache` |
| `--force-download` | No | Force re-download even if cached | False |

## Examples

### Upload a Chat Model
```bash
python hf_to_s3.py --model-id microsoft/DialoGPT-small --bucket chat-models-bucket
```

### Upload with Custom S3 Path
```bash
python hf_to_s3.py \
  --model-id facebook/blenderbot-400M-distill \
  --bucket my-bucket \
  --s3-prefix production/chatbots/blenderbot/
```

### Upload Large Model with Custom Cache
```bash
python hf_to_s3.py \
  --model-id microsoft/DialoGPT-large \
  --bucket large-models \
  --cache-dir /tmp/large_model_cache \
  --force-download
```

## What Gets Uploaded

The script uploads all model files including:
- Model weights (`pytorch_model.bin` or `.safetensors`)
- Configuration files (`config.json`)
- Tokenizer files (`tokenizer.json`, `vocab.txt`, etc.)
- Any additional model-specific files

## Output

The script provides:
- Download progress from Hugging Face
- Upload progress to S3
- Final S3 URLs for all uploaded files
- Total upload time and file count

## Troubleshooting

### Common Issues

**Authentication Error**
```
Error: Unable to locate credentials
```
Solution: Configure AWS CLI with `aws configure`

**Permission Denied**
```
Error: Access Denied
```
Solution: Ensure your AWS user has S3 write permissions

**Model Not Found**
```
Error: Repository not found
```
Solution: Verify the model ID exists on Hugging Face Hub

**Large Model Timeout**
```
Error: Connection timeout
```
Solution: Use `--cache-dir` on faster storage and retry

## Best Practices

1. **Use specific S3 prefixes** to organize models by type/version
2. **Cache models locally** for repeated uploads to different buckets
3. **Use appropriate regions** close to your deployment infrastructure
4. **Monitor costs** - large models can incur significant transfer costs
5. **Version your uploads** using date/version in S3 prefix

## Integration with SageMaker

After upload, reference your model in SageMaker:
```python
model_data = "s3://my-bucket/models/my-model/"
```
