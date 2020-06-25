# Organize configuration with modules

[Doc](https://learn.hashicorp.com/terraform/modules/using-modules)

## What are modules for?
- Organize configuration 
- Encapsulate configuration 
- Re-use configuration
- Provide consistency and ensure best practices
- Using modules can help reduce these errors

## What is a Terraform module?
- A Terraform module is a set of Terraform configuration files in a single directory. 
- Even a simple configuration consisting of a single directory with one or more `.tf` files is a module.

## Calling modules
A module that is called by another configuration is sometimes referred to as a "child module" of that configuration.

## Local and remote modules
Modules can either be loaded from the local filesystem, or a remote source.

## Module best practices
In many ways, Terraform modules are similar to the concepts of libraries, packages, or modules found in most programming languages, and provide many of the same benefits. 


## Create a module
### Module structure
Terraform treats any local directory referenced in the source argument of a module block as a module. A typical file structure for a new module is:
```console
$ tree minimal-module/
.
├── LICENSE
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
```

## `provider` in modules
You will notice that there is no provider block in this configuration. When Terraform processes a module block, it will inherit the provider from the enclosing configuration. Because of this, we recommend that you do not include provider blocks in modules.

## Install a module
- `terraform get`
- Note: When installing a remote module, Terraform will download it into the `.terraform` directory in your configuration's root directory. When installing a local module, Terraform will instead refer directly to the source directory. Because of this, Terraform will automatically notice changes to local modules without having to re-run `terraform init` or `terraform get`.