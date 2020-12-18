# move remote backend state from terraform cloud to s3


### Create s3 bucket and dynamodb for store state

```
resource "aws_s3_bucket" "terraform_state" {
  bucket = "bucket_name"

  # terraform destroy로 지워지지 않도록
  lifecycle {
    prevent_destroy = true
  }

  # 이전 버전의 state 관리 가능하게
  versioning {
    enabled = true
  }

  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }
}

resource "aws_dynamodb_table" "terraform_locks" {
  name = "dynamodb_name"
  billing_mode = "PAY_PER_REQUEST"
  hash_key = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}
```

And apply it

```
terraform apply
```


### Add (or replace) backend

terraform s3 backend 추가 후 다시 init

```
terraform {
  backend "s3" {
    bucket = "terraform-up-and-running-state-sckim"
    key = "global/s3/terraform.tfstate"
    region = "us-west-2"

    dynamodb_table = "terraform-up-and-running-locks"
    encrypt = true
  }
}
```

```
terraform init
```
