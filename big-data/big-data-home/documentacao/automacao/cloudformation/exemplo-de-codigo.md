
[Documentação](../../../documentacao.md) > [Automacao](../../automacao.md) > [CloudFormation](../cloudformation.md)

# Exemplo de codigo

Este código cria uma instância Ec2 com três discos, sendo o sda1 para o SO, e executa um script shell na sua inicialização

```java
{
  "AWSTemplateFormatVersion" : "2010-09-09",
  "Description" : "Single Instance",
  "Resources" : {
      "EC2Instance" : {
      "Type" : "AWS::EC2::Instance",
      "Properties" : {
	  "Tags" : [
            {"Key" : "Name", "Value" : "EC2_TESTE2"},
			{"Key" : "company", "Value" : "uolcsm"},
			{"Key" : "segment ", "Value" : "uolcsm-geral"},
			{"Key" : "area", "Value" : "bi-analytics"}
        ],
        "InstanceType" : "t2.micro",
        "SecurityGroupIds" : [<sg_id>],
		"SubnetId" : <subnet_id>,
        "KeyName" : <key_bigdata>,
        "ImageId" : "ami-9887c6e7",
		"BlockDeviceMappings" : [
		  {
            "DeviceName" : "/dev/sda1", 
            "Ebs" : { "VolumeSize" : "10" }
          },
          {
            "DeviceName" : "/dev/sdb", 
            "Ebs" : { "VolumeSize" : "10" }
          },
		  {
            "DeviceName" : "/dev/sdc",
            "Ebs" : { "VolumeSize" : "10" }
          }
        ],
		"UserData" : {
                    "Fn::Base64" : {
                        "Fn::Join" : [
                            "",
                            [
                                "#!/bin/bash\n",
                                "echo 172.23.5.100 ni-36947-0 >> /etc/hosts\n"
                            ]
                        ]
                    }
                }
				
		}
	}

   }
}
```
