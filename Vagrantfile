# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "hashicorp/bionic64"
    # config.ssh.extra_args = ["-t", "cd /vagrant/code; bash --login"]
  
    config.vm.define "gobox" do |gobox|
      gobox.vm.hostname = "gobox"
      gobox.vm.provision "shell", inline: <<-SHELL
                # install terraform
                export TFVER=1.1.3
  
                which terraform &>/dev/null || {
  
                  # deps to download TF
                  which curl unzip git tree &>/dev/null || {
                    export DEBIAN_FRONTEND=noninteractive
                    sudo apt-get update
                    sudo apt-get install -y curl unzip git tree
                  }
  
                  pushd /usr/local/bin
                  curl -fsSL https://releases.hashicorp.com/terraform/${TFVER}/terraform_${TFVER}_linux_amd64.zip -o terraform_${TFVER}_linux_amd64.zip
                  unzip terraform_${TFVER}_linux_amd64.zip
                  rm terraform_${TFVER}_linux_amd64.zip
                  popd
                }
  
                # install go
                GO_VERSION="17.5"
                pushd /usr/local/bin 
                curl -fsSL https://go.dev/dl/go1.${GO_VERSION}.linux-amd64.tar.gz -o go1.${GO_VERSION}.linux-amd64.tar.gz
                tar -xzf go1.${GO_VERSION}.linux-amd64.tar.gz
                popd
                echo "export PATH=${PATH}:/usr/local/bin/go/bin" >> /etc/profile     # add go to the PATH
                # mkdir -p /home/vagrant/.terraform.d/plugins/namespace-abc/find-the-ip/1.0.0/linux_amd64/
                # pushd /home
                # chown -R vagrant:vagrant vagrant/
                # popd
  
                # # download a repo that contains source code of a module
                # pushd /tmp
                # git clone https://github.com/petems/terraform-provider-extip.git
                # cd terraform-provider-extip
                # # GO compile a module
                # GOARCH=amd64 go build -o terraform-provider-find-the-ip
                # cp terraform-provider-find-the-ip /home/vagrant/.terraform.d/plugins/namespace-abc/find-the-ip/1.0.0/linux_amd64/
                # popd
  
                # touch main.tf
                # chown vagrant:vagrant main.tf
                # cat <<-EOF | sudo tee main.tf
                #     terraform {
                #         required_providers {
                #           extip = {
                #             source = "hashicorp.com/namespace-abc/find-the-ip"
                #             version = "1.0.0"
                #           }
                #         }
                #       }
  
                #       data "extip" "external_ip_from_aws" {
                #         resolver = "https://checkip.amazonaws.com/"
                #       }
  
                #       output "external_ip_from_aws" {
                #         value = data.extip.external_ip_from_aws.ipaddress
                #       }
                #   EOF
      SHELL
    end
  end
  