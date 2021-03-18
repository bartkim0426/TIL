# terraform use environment variable to var name

Sometimes you need use credential data via variable. It's bad way to directly right var in `.tf` file.

Then you can use environment variable to inject credentials.


Set `TF_VAR_{variable_name}` to environment variable.

```
export TF_VAR_myVar="myvariable"
```

Then you don't have to write down variable in tf file.


Below is simple terraform code to create mysql database in RDS. (`mysql.tf`)

We want to use `password` without write down inside tf file.

```
provider "aws" {
  region = "us-west-2"
}

variable "password" {
  type = string
}

resource "aws_db_instance" "default" {
  allocated_storage    = 10
  engine               = "mysql"
  engine_version       = "5.7"
  instance_class       = "db.t3.micro"
  name                 = "mydb"
  username             = "foo"
  password             = var.password
  parameter_group_name = "default.mysql5.7"
  skip_final_snapshot  = true
}
```

When you execute terrform command without environment variable, you have to enter password.

```
terraform plan
var.password
  Enter a value:
```

But if you set `TF_VAR_password` via environment variable, you don't have to enter it.

```
export TF_VAR_password=mypassword

# do it again and works very well
terraform plan

Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_db_instance.default will be created
  + resource "aws_db_instance" "default" {
      + address                               = (known after apply)
      ...
```


> If you got credential error from AWS, you have to set `profile` in aws provider.
