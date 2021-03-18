# terraform with specific resources

using `-target` option

## Usage

```
terraform plan -target=[module path][resource spec]
```

## Module path

```
module.module_name[module_index]
```

- module: Module keyword indicating a child module.

> For more information about terraform module, check [terraform docs - module](https://www.terraform.io/docs/language/modules/index.html)


## resource spec

You can specify recource in the config.

```
resource_type.resource_name[recourse_index]
```
- resource_type: type of the resource
- resource_name: user-defined name

For example, if you have the file below.

```
provider "aws" {
  region = "us-west-2"
  profile = "seul"
}

resource "aws_db_instance" "create" {
  allocated_storage    = 10
  engine               = "mysql"
  engine_version       = "5.7"
  instance_class       = "db.t3.micro"
  name                 = "mydb"
  username             = "foo"
  password             = "test"
  parameter_group_name = "default.mysql5.7"
  skip_final_snapshot  = true
}


resource "aws_db_instance" "donotcreate" {
  allocated_storage    = 10
  engine               = "mysql"
  engine_version       = "5.7"
  instance_class       = "db.t3.micro"
  name                 = "mydb"
  username             = "foo"
  password             = "test"
  parameter_group_name = "default.mysql5.7"
  skip_final_snapshot  = true
}
```

If you execute `terraform plan`, you'll see 2 to add.

```
terraform plan
...
Plan: 2 to add, 0 to change, 0 to destroy.
...
```

If you want to specify only `create` resource, 

```
terraform plan -target=aws_db_instance.create
...
Plan: 1 to add, 0 to change, 0 to destroy.
...
```

That's it! Voila!
