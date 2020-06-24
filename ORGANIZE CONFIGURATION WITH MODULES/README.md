# Organize configuration with modules

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