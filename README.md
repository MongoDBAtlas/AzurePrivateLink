<img src="https://companieslogo.com/img/orig/MDB_BIG-ad812c6c.png?t=1648915248" width="50%" title="Github_Logo"/> <br>


# MongoDB Atlas Azure Private Link

### Atlas Cluster & 권한
Azure와 Private Link로 내부 Vnet과 직접 연결을 위한 설정 입니다. Private Link는 단방향으로 Azure에 생성한 VNet에서 Atlas로 연결만 가능 합니다. 이는 Atlas Cluster의 VNet에 로드 밸런서가 생성되어 Azure VNet이 로드 밸런서를 이용한 접속을 하기 때문에 Atlas Cluster의 VNet에서 Azure VNet으로 접속이 불가 합니다.   
Private Link 설정을 위해 Atlas Cluster를 서울 리전에 생성하고 Azure Vnet을 동일한 서울에 생성합니다.   
설정을 위해 Atlas에 필요한 권한은 Organization Owner 혹은 Project Owner 권한이 필요 합니다.  

### Atlas Private Link 생성

Atlas 로그인 후 Project를 선택 후 Security 항목에서 Network Access를 클릭 합니다.   
Private Endpoint를 선택 하고 Add Private Endpoint를 클릭합니다.

<img src="/images/image01.png" width="80%" height="80%">

Azure를 선택 합니다.   

<img src="/images/image02.png" width="50%" height="50%">

Azure의 Vnet의 리전을 선택 합니다 (서울).    

<img src="/images/image03.png" width="50%" height="50%">

이후 Atlas의 endpoint 가 생성 되며 (3-4분 소요) Azure의 VNet 정보를 입력 하여 줍니다.

<img src="/images/image04.png" width="70%" height="70%">

Azure Vnet의 정보를 입력 하여 줍니다.

Resource Group Name    
<img src="/images/image05.png" width="70%" height="70%">

Virtual Network Name    
<img src="/images/image06.png" width="70%" height="70%">

Subnet Name    
<img src="/images/image07.png" width="70%" height="70%">

private endpoint name 을 입력 하여 줍니다.    

<img src="/images/image08.png" width="70%" height="70%">

Private endpoint 생성을 위한 Azure CLI 명령을 복사하여 줍니다. 

### Azure CLI

복사한 CLI를 실행 하여 줍니다.    

<img src="/images/image09.png" width="60%" height="60%">

`````
PS /home/kim> az network private-endpoint create --resource-group AzureGroup --name AtlasPrivateLink --vnet-name AzureVnet --subnet PrivateLinkTest --private-connection-resource-id /subscriptions/4fa98f2e-be39-42b9-b356-a75febc51fae/resourceGroups/rg_66b4156fd0467003bc9d721f_0jq6vkre/providers/Microsoft.Network/privateLinkServices/pls_66b41890d0467003bca183f0 --connection-name pls_66b41890d0467003bca183f0 --manual-request true
{
  "customDnsConfigs": [],
  "customNetworkInterfaceName": "",
  "etag": "W/\"f94cf0c6-b4cf-4a5e-ae55-fb7784fb4154\"",
  "id": "/subscriptions/f7d80858-847b-4fd5-948c-0238d01b24a5/resourceGroups/AzureGroup/providers/Microsoft.Network/privateEndpoints/AtlasPrivateLink",
  "ipConfigurations": [],
  "location": "koreacentral",
  "manualPrivateLinkServiceConnections": [
    {
      "etag": "W/\"f94cf0c6-b4cf-4a5e-ae55-fb7784fb4154\"",
      "id": "/subscriptions/f7d80858-847b-4fd5-948c-0238d01b24a5/resourceGroups/AzureGroup/providers/Microsoft.Network/privateEndpoints/AtlasPrivateLink/manualPrivateLinkServiceConnections/pls_66b41890d0467003bca183f0",
      "name": "pls_66b41890d0467003bca183f0",
      "privateLinkServiceConnectionState": {
        "actionsRequired": "None",
        "description": "Awaiting Approval",
        "status": "Pending"
      },
      "privateLinkServiceId": "/subscriptions/4fa98f2e-be39-42b9-b356-a75febc51fae/resourceGroups/rg_66b4156fd0467003bc9d721f_0jq6vkre/providers/Microsoft.Network/privateLinkServices/pls_66b41890d0467003bca183f0",
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureGroup",
      "type": "Microsoft.Network/privateEndpoints/manualPrivateLinkServiceConnections"
    }
  ],
  "name": "AtlasPrivateLink",
  "networkInterfaces": [
    {
      "id": "/subscriptions/f7d80858-847b-4fd5-948c-0238d01b24a5/resourceGroups/AzureGroup/providers/Microsoft.Network/networkInterfaces/AtlasPrivateLink.nic.0a81b071-448d-4538-9340-e9c07e409b89",
      "resourceGroup": "AzureGroup"
    }
  ],
  "privateLinkServiceConnections": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "AzureGroup",
  "subnet": {
    "id": "/subscriptions/f7d80858-847b-4fd5-948c-0238d01b24a5/resourceGroups/AzureGroup/providers/Microsoft.Network/virtualNetworks/AzureVnet/subnets/PrivateLinkTest",
    "resourceGroup": "AzureGroup"
  },
  "type": "Microsoft.Network/privateEndpoints"
}
PS /home/kim> 
`````

생성한 PrivateLink는 Vnet의 Private endpoint에서 확인 할 수 있습니다. 생성된 PrivateLink에서 Private endpoint Resource ID와 Private Endpoint IP Address를 확인하고 Atlas에 입력 하여야 합니다.
아래 페이지에 해당 값을 저장 하고 이를 Atlas에 입력 해주어 설정을 완료 합니다. 

Private endpoint Resource ID   

<img src="/images/image10.png" width="80%" height="80%">

Private Endpoint IP Address   

<img src="/images/image11.png" width="80%" height="80%">


### Atlas Private Link Azure 정보 입력

Atlas Console 에서 Private Link 설정에서 Next를 클릭 하여 주고 Private endpoint Resource ID와 Private Endpoint IP Address를 입력 하여 줍니다. 

<img src="/images/image12.png" width="80%" height="80%">    

최종 생성 버튼을 클릭 하면 후속 작업이 진행 됩니다. 

완료 되면 상태 정보가 Available로 보여 집니다.

<img src="/images/image13.png" width="80%" height="80%">    


### Atlas Connection

Atlas Cluster의 접속 주소를 얻기 위해 Cluster의 Connection을 선택 합니다. 

<img src="/images/image14.png" width="80%" height="80%">    

Connection type에서 Private Endpoint를 선택 합니다.

<img src="/images/image15.png" width="80%" height="80%">    

Connection String에 나온 주소를 복사 하고 VNet 내부에서 접속을 수행 합니다.
(Private endpoint의 주소와 일반 접근 주소가 다른 것을 볼 수 있습니다.)

