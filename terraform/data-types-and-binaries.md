# Working with Data in Terraform

Terraform provides several mechanisms to work with data efficiently, allowing you to customize and extract information during the deployment process.

## Key Concepts

- **Input Variables**: 
  - Allow passing information into Terraform at runtime.
- **Local Values**: 
  - Computed internally for reuse within the configuration.
- **Outputs**: 
  - Extract information from Terraform after deployment.

---

## Defining Input Variables

```hcl
variable "<name>" {
  type        = <data_type>
  description = "<description>"
  default     = <default_value>   # Optional
  sensitive   = <true/false>      # Optional
}
```
### Key Properties

- **type**: Specifies data type (`string`, `number`, `list`, `map`).
- **description**: Explains the variable’s purpose.
- **default**: Makes a variable optional by providing a default value.
- **sensitive**: Hides values from logs (e.g., passwords).
### Usage
```hcl
var.<variable_name>
```
### Example
```hcl
variable "azure_region" {
  type        = string
  default     = "East US"
  description = "Azure region to deploy resources"
}
```
# Data Types in Terraform

Terraform supports various data types to define and manage infrastructure efficiently.

## 1. Primitive Types
- **string**: Represents text.
- **number**: Represents numeric values.
- **bool**: Represents true/false values.

## 2. Collection Types
- **list**: 
  - Ordered collection of elements of the same type.
    ```hcl
    variable "my_list" {
      type    = list(string)
      default = ["East US", "West US", "Central US"]
    }
    ```
- **set**: 
  - Unordered collection of unique elements.
    ```hcl
      variable "my_set" {
      type    = set(string)
      default = ["dev", "staging", "prod", "dev"]
    }
    ```
- **map**: 
  - Key-value pairs with values of the same type.
    ```hcl
        variable "my_map" {
      type = map(string)
      default = {
        environment = "production"
        region      = "East US"
        owner       = "admin"
      }
    }
    ```
## 3. Structural Types
- **tuple**: 
  - Fixed sequence that allows mixed types.
    ```hcl
    variable "my_tuple" {
      type    = tuple([string, number, bool])
      default = ["Azure", 5, true]
    }
    ```
- **object**: 
  - Map-like structure with attributes of different types.
```hcl
  variable "my_object" {
  type = object({ name = string, count = number })
  default = { name = "resource", count = 3 }
}
```

## 4. Special Types
- **any**: 
  - Flexible type that accepts any data type.
    ```hcl
    variable "my_dynamic_var" {
    type    = any
    default = ["East US", "West US"]
  }
  ```
- **null**: 
  - Represents the absence of a value.
    ```hcl
      locals {
    common_tags = {
      company = var.company
      project = "${var.company}-${var.project}"
    }
  }
  ```
 # Local Values in Terraform

### Purpose
- **Store reusable values** within a configuration to simplify and optimize code.
```hcl
locals {
  common_tags = {
    company = var.company
    project = "${var.company}-${var.project}"
    billing_code = var.billing_code
  }
}

resource "azurerm_virtual_machine" "vm" {
  tags = local.common_tags
}
```
#### Usage: Access with local.<name>, e.g., local.common_tags.

# Output Values
- **Outputs** are printed to the terminal and stored in the state file.
- **Use sensitive** = true to hide values from terminal output.
  ```hcl
    output "<name>" {
      value       = <expression>
      description = "<description>"
      sensitive   = <true/false>
    }

  output "vm_public_ip" {
  value       = azurerm_public_ip.my_vm_ip.address
  description = "The public IP address of the VM"
  }
  ```

# Validating & Formatting Configuration in Terraform
## Formatting Terraform Code
- **`terraform fmt`**: Formats code to HashiCorp’s standard style.
- **`terraform validate`**: Checks syntax and logic (after terraform init).
  
  ```bash
    terraform fmt -check
    terraform validate
  ```
# Supplying Variable Values in Terraform

## Ways to Provide Values

1. **Default values** in the variable block.
2. **Command line**: -var="key=value"

3. **Variable files**: terraform.tfvars or .auto.tfvars.
4. **Environment variables**: TF_VAR_<variable_name>.
   
### Order of Precedence: CLI > Environment Variables > .tfvars file > Defaults.

# Deployment in Local
```bash
# Initialize the Terraform configuration
terraform init

# Generate a plan and save it to an output file
terraform plan -out=plan_output.tfplan

# Apply the saved plan to deploy resources
terraform apply plan_output.tfplan
```
     
