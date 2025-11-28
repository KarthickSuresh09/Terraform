1. Destroy the previous deployment
2. Create 2 EC2 instances in Ohio and N.Virginia respectively
3. Rename Ohio’s instance to ‘hello-ohio’ and Virginia’s instance to
‘hello-virginia’

Step 1: terraform destroy --auto-approve

Step 2: Creating new provider.tf files 
* provider means like aws,azure,gcp
* alias is like
   * One SIM → one region
   * Want two regions? → You need two SIMs
   * Terraform calls each SIM a provider, and the label you give it is the alias
* region we mention like which location to create 

```
provider "aws" {
  alias  = "ohio"
  region = "us-east-2"
}

provider "aws" {
  alias  = "virginia"
  region = "us-east-1"
}
```
