# Learn Terraform Code Organization

Learn best practices for Terraform as your organization grows. Split a Dev & Prod environment and compose shared modules.

Follow along with the [Learn guide](https://learn.hashicorp.com/terraform/modules/code-org-envs) at HashiCorp Learn.


## Separate states

There are two primary methods to separate state between environments: **directories** and **workspaces**.
- Use directory
  - To separate environments with potential configuration differences
  - With potential duplication
- Use workspaces 
  - for environments that do not greatly deviate from one another
  - to avoid duplicating your configurations. 


### Directory
When you are finished separating these environments into directories, your file structure should look like the one below.

```
$ tree
.
├── assets
│   ├── index.html
├── prod
│   ├── main.tf
│   ├── variables.tf
│   ├── terraform.tfstate
│   └── terraform.tfvars
└── dev
   ├── main.tf
   ├── variables.tf
   ├── terraform.tfstate
   └── terraform.tfvars
```
Execute `terraform init` in each `dev` and `prod` directory.

### Workspaces
- Show all workspace
    - In your terminal, type `terraform workspace list` to see the list of your workspaces and which one is currently selected represented by `*`.
        ```
        $ terraform workspace list
        *  default
        ```
- Create a workspace
  - `terraform workspace new dev`
  - output: 
    ```
    Created and switched to workspace "dev"!

    You're now on a new, empty workspace. Workspaces isolate their state, so if you run "terraform plan" Terraform will not see any existing state for this configuration.
    ```
  - **Any previous state files from your `default` workspace are hidden** from your `dev` workspace, but your directory and file structure do not change.
  - Apply the configuration for your development environment in the new workspace, specifying the `dev.tfvars` file with the `-var-file` flag.
  - `terraform apply -var-file=dev.tfvars`
    ```
    Do you want to perform these actions in workspace "dev"? 
      Terraform will perform the actions described above.
      Only 'yes' will be accepted to approve.
      
      Enter a value:
    ```
- Switching between workspace
  - `terraform workspace select dev`
- State storage in workspaces
  - When you use the default workspace with the local backend, your `terraform.tfstate` file is stored in the root directory of your Terraform project. 
  - When you add additional workspaces your state location changes; Terraform internals manage and store state files in the directory `terraform.tfstate.d`.
  - Your directory should look similar to the one below.
    ```
    $ tree
    .
    ├── README.md
    ├── assets
    │   └── index.html
    ├── dev.tfvars
    ├── main.tf
    ├── outputs.tf
    ├── prod.tfvars
    ├── terraform.tfstate.d
    │   ├── dev
    │   │   └── terraform.tfstate
    │   ├── prod
    │   │   └── terraform.tfstate
    ├── terraform.tfvars
    └── variables.tf
    ``` 