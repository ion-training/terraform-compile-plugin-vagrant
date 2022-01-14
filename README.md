# terraform-compile-plugin-vagrant
Build a provider plugin with go in vagrant

Compile a module for terraform that queries AWS APIs to return your public IP address.

# How to use this repo
Clone
```
git clone https://github.com/ion-training/terraform-compile-plugin-vagrant.git
```
cd into the repo
```
cd terraform-compile-plugin-vagrant
```

Vagrant up and login
```
vagrant up
```
```
vagrant ssh
```

# Terraform directory structure
Requirements for terraform providers [docs](https://www.terraform.io/language/providers/requirements).

Create a main.tf file
```
touch main.tf
```

Add null resource to main.tf
```
resource "null_resource" "null-name" {
}
```

Initialize with terraform
```
terraform init
```

terraform init creates a .terraform.d dir in home directory
```
$ tree ~/.terraform.d
/home/vagrant/.terraform.d
├── checkpoint_cache
└── checkpoint_signature

0 directories, 2 files
```

Create the expected directory structure by terraform\
A module  will be placed here (to be compiled later)
```
mkdir -p ~/.terraform.d/plugins/example.com/namespace-abc/find-the-ip/1.0.0/linux_amd64/
```

# Download code and compile into a terraform module
Download
```
git clone https://github.com/petems/terraform-provider-extip.git
```

cd into the repo
```
cd terraform-provider-extip
```
Compile the module
_Note that output name is has fixed prefix "terraform-provider-" plus the type, in this case "find-the-ip"_
```
GOARCH=amd64 go build -o terraform-provider-find-the-ip
```
```
cp terraform-provider-find-the-ip ~/.terraform.d/plugins/example.com/namespace-abc/find-the-ip/1.0.0/linux_amd64/
```

Directory structure that includes the compiled module
```
$ tree ~/.terraform.d/
/home/vagrant/.terraform.d/
├── checkpoint_cache
├── checkpoint_signature
└── plugins
    └── example.com
        └── namespace-abc
            └── find-the-ip
                └── 1.0.0
                    └── linux_amd64
                        └── terraform-provider-find-the-ip

6 directories, 3 files
```

# Put the module at work
Make sure you are in home directory
```
cd ~
```

Append code to main.tf (references the module)\
_terraform will look locally first in ~/.terraform.d/plugins then try download form internet_
```
terraform {
  required_providers {
    # extip name of the module cannot change (due to RPC)
    extip = {
      source = "example.com/namespace-abc/find-the-ip"
      version = "1.0.0"
    }
  }
}

data "extip" "external_ip" {
}

data "extip" "external_ip_from_aws" {
  resolver = "https://checkip.amazonaws.com/"
}

output "external_ip" {
  value = data.extip.external_ip.ipaddress
}

output "external_ip_from_aws" {
  value = data.extip.external_ip_from_aws.ipaddress
}
```

Terraform init and apply
```
terraform init
```
```
terraform apply -auto-approve
```

Use terraform output to view public IP
```
terraform output
```

# Destroy the LAB
On the host running vagrant box in directory terraform-compile-plugin-vagrant
```
vagrant destroy -f
```

# Sample output
```
terraform apply -auto-approve
null_resource.null-name: Refreshing state... [id=1303158606801117523]

Changes to Outputs:
  + external_ip          = "87.262.168.71"
  + external_ip_from_aws = "87.262.168.71"

You can apply this plan to save these new output values to the Terraform state, without changing any real infrastructure.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

external_ip = "87.262.168.71"
external_ip_from_aws = "87.262.168.71"
```
