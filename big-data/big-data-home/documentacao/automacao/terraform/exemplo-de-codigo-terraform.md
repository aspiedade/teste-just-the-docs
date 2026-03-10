
[Documentação](../../../documentacao.md) > [Automacao](../../automacao.md) > [TerraForm](../terraform.md)

# Exemplo de codigo - Terraform

Script que cria uma instância Ec2 com um volumes EBS

```bash
#região
variable "region" {
  default = "us-east-1"
}
#az
variable "availability_zone" {
  default = "us-east-1a"
}

#autenticação através de role (é necessário se autenticar via aws-azure-login )
#https://confluence.intranet.uol.com.br/confluence/pages/viewpage.action?pageId=219623032

provider "aws" {
  region = "${var.region}"
    assume_role {
      role_arn     = <role>
    }
}


#instância ec2
resource "aws_instance" "ec2-instance2" {
	ami = "ami-9887c6e7"
	instance_type = "t2.micro"
	tags { Name = "terraform-instance5" }
	subnet_id                   = <subnetid>
	vpc_security_group_ids      = [<sg_id>]
	associate_public_ip_address = false
	key_name = <key name>	
	#ebs_optimized               = true
#disco SO	
	root_block_device {
		volume_type           = "gp2"
		volume_size           = 8
		delete_on_termination = true
	}
#disco de dados, que precisa ser montado posteriormente  
    ebs_block_device {
		device_name           = "/dev/sdg"
		volume_type           = "gp2"
		volume_size           = 15
		delete_on_termination = true
	}
	
	#user_data = "${file("files/attach_ebs.sh")}"
	
  }



```

No stash do time de big data há um código mais complexo que faz a utilização de módulos:
