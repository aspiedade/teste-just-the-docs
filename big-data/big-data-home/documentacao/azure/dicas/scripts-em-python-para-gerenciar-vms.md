
[Documentação](../../../documentacao.md) > [Azure](../../azure.md) > [Dicas](../dicas.md)

# Scripts em python para gerenciar Vms

#### pré-requisitos para executar os scripts:

1. Instalar o **python 3.6** - <https://www.python.org/downloads/>
2. Instalar o **Visual Studio Code** ou outra IDE que dê suporte à linguagem Python - <https://code.visualstudio.com/>
3. Instalar a **biblioteca do Azure**:

   ```java
   #via terminal, execute o seguinte comando:
    
   pip3 install azure
   ```

#### Iniciar VM

```py
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.compute import ComputeManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute.models import DiskCreateOption

SUBSCRIPTION_ID = ''
GROUP_NAME = ''
VM_NAME = ''

def get_credentials():
    credentials = ServicePrincipalCredentials(
        client_id = '',
        secret = '',
        tenant = ''
    )

    return credentials

def start_vm(compute_client):
    compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    
if __name__ == "__main__":
 credentials = get_credentials()

 compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)

 start_vm(compute_client)


```

**Parar VM**

```java
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.compute import ComputeManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute.models import DiskCreateOption

SUBSCRIPTION_ID = ''
GROUP_NAME = ''
VM_NAME = ''

def get_credentials():
    credentials = ServicePrincipalCredentials(
        client_id = '',
        secret = '',
        tenant = ''
    )

    return credentials

def stop_vm(compute_client):
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    
if __name__ == "__main__":
 credentials = get_credentials()

 compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)

 stop_vm(compute_client)


```

**Criar VM (Windows Server), grupo de recursos e rede virtual**

```java
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.compute import ComputeManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute.models import DiskCreateOption

SUBSCRIPTION_ID = ''
GROUP_NAME = ''
LOCATION = ''
VM_NAME = ''

def create_vm(network_client, compute_client):  
    nic = network_client.network_interfaces.get(
        GROUP_NAME, 
        'myNic'
    )
    avset = compute_client.availability_sets.get(
        GROUP_NAME,
        'myAVSet'
    )
    vm_parameters = {
        'location': LOCATION,
        'os_profile': {
            'computer_name': VM_NAME,
            'admin_username': 'azureuser',
            'admin_password': 'Azure12345678'
        },
        'hardware_profile': {
            'vm_size': 'Standard_DS1'
        },
        'storage_profile': {
            'image_reference': {
                'publisher': 'MicrosoftWindowsServer',
                'offer': 'WindowsServer',
                'sku': '2012-R2-Datacenter',
                'version': 'latest'
            }
        },
        'network_profile': {
            'network_interfaces': [{
                'id': nic.id
            }]
        },
        'availability_set': {
            'id': avset.id
        }
    }
    creation_result = compute_client.virtual_machines.create_or_update(
        GROUP_NAME, 
        VM_NAME, 
        vm_parameters
    )

    return creation_result.result()

def create_nic(network_client):
    subnet_info = network_client.subnets.get(
        GROUP_NAME, 
        'myVNet', 
        'mySubnet'
    )
    publicIPAddress = network_client.public_ip_addresses.get(
        GROUP_NAME,
        'myIPAddress'
    )
    nic_params = {
        'location': LOCATION,
        'ip_configurations': [{
            'name': 'myIPConfig',
            'public_ip_address': publicIPAddress,
            'subnet': {
                'id': subnet_info.id
            }
        }]
    }
    creation_result = network_client.network_interfaces.create_or_update(
        GROUP_NAME,
        'myNic',
        nic_params
    )

    return creation_result.result()


def create_subnet(network_client):
    subnet_params = {
        'address_prefix': '10.0.0.0/24'
    }
    creation_result = network_client.subnets.create_or_update(
        GROUP_NAME,
        'myVNet',
        'mySubnet',
        subnet_params
    )

    return creation_result.result()

def create_vnet(network_client):
    vnet_params = {
        'location': LOCATION,
        'address_space': {
            'address_prefixes': ['10.0.0.0/16']
        }
    }
    creation_result = network_client.virtual_networks.create_or_update(
        GROUP_NAME,
        'myVNet',
        vnet_params
    )
    return creation_result.result()

def create_public_ip_address(network_client):
    public_ip_addess_params = {
        'location': LOCATION,
        'public_ip_allocation_method': 'Dynamic'
    }
    creation_result = network_client.public_ip_addresses.create_or_update(
        GROUP_NAME,
        'myIPAddress',
        public_ip_addess_params
    )

    return creation_result.result()

def create_availability_set(compute_client):
    avset_params = {
        'location': LOCATION,
        'sku': { 'name': 'Aligned' },
        'platform_fault_domain_count': 3
    }
    availability_set_result = compute_client.availability_sets.create_or_update(
        GROUP_NAME,
        'myAVSet',
        avset_params
    )

def create_resource_group(resource_group_client):
    resource_group_params = { 'location':LOCATION }
    resource_group_result = resource_group_client.resource_groups.create_or_update(
        GROUP_NAME, 
        resource_group_params
    )

def get_credentials():
    credentials = ServicePrincipalCredentials(
        client_id = '',
        secret = '',
        tenant = ''
    )

    return credentials

    
  
if __name__ == "__main__":
  
  credentials = get_credentials()

  resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
  network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
  compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)


  create_resource_group(resource_group_client)
  input('Resource group created. Press enter to continue...')


  create_availability_set(compute_client)
  print("------------------------------------------------------")
  input('Availability set created. Press enter to continue...')

  creation_result = create_public_ip_address(network_client)
  print("------------------------------------------------------")
  print(creation_result)
  input('Press enter to continue...')

  creation_result = create_vnet(network_client)
  print("------------------------------------------------------")
  print(creation_result)
  input('Press enter to continue...')

  creation_result = create_subnet(network_client)
  print("------------------------------------------------------")
  print(creation_result)
  input('Press enter to continue...')


  creation_result = create_nic(network_client)
  print("------------------------------------------------------")
  print(creation_result)
  input('Press enter to continue...')

  
  creation_result = create_vm(network_client, compute_client)
  print("------------------------------------------------------")
  print(creation_result)
  input('Press enter to continue...')  


```

**Observação: É possível utilizar o script acima para criar uma VM linux, por exemplo, porém é necessário adequá-lo.**

 

**Excluir grupo de recursos**

```py
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.compute import ComputeManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute.models import DiskCreateOption

SUBSCRIPTION_ID = ''
GROUP_NAME = ''


def get_credentials():
    credentials = ServicePrincipalCredentials(
        client_id = '',
        secret = '',
        tenant = ''
    )

    return credentials

def delete_resources(resource_group_client):
    resource_group_client.resource_groups.delete(GROUP_NAME)
    
  
if __name__ == "__main__":
  credentials = get_credentials()

  resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
  delete_resources(resource_group_client)
    





```

<https://docs.microsoft.com/pt-br/azure/virtual-machines/windows/python>
