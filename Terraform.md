Run the below commands in Amazon Linux terminal to install terraform-

Install yum-config-manager to manage your repositories.
sudo yum install -y yum-utils
2. Use yum-config-manager to add the official HashiCorp Linux repository.
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo

3. Install.
sudo yum -y install terraform

4. Version
terraform --version
