---
title: 'Rövid útmutató: a-fiók létrehozása a Python használatával'
description: Hozzon létre egy Azure-beli hatáskörébe tartozó fiókot a Python használatával.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387478"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Rövid útmutató: a-fiók létrehozása a Python használatával

Ebben a rövid útmutatóban a Python használatával hoz létre egy hatáskörébe tartozó fiókot. 

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* A fióknak engedéllyel kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre az előfizetésben

* Ha **Azure Policy** blokkolja az összes alkalmazást a Storage- **fiók** és a **EventHub-névtér** létrehozásához, akkor a címkével kell megadnia a szabályzatot, amely a hatáskörébe tartozó fiók létrehozásának folyamata során megadható. Ennek fő oka, hogy minden létrehozott felügyeleti fiókhoz létre kell hoznia egy felügyelt erőforráscsoportot, és ezen az erőforráscsoport, egy Storage-fiók és egy EventHub-névtér között. További információt a katalógus- [portál létrehozása](create-catalog-portal.md) című témakörben talál.


## <a name="install-the-python-package"></a>Python-csomag telepítése

1. Nyisson meg egy terminált vagy parancssort rendszergazdai jogosultságokkal. 
2. Először telepítse a Python-csomagot az Azure felügyeleti erőforrásokhoz:

    ```python
    pip install azure-mgmt-resource
    ```
3. A Python-csomag a hatáskörébe való telepítéséhez futtassa a következő parancsot:

    ```python
    pip install azure-mgmt-purview
    ```

    A [hatáskörébe tartozó PYTHON SDK](https://github.com/Azure/azure-sdk-for-python) a Python 2,7, 3,3, 3,4, 3,5, 3,6 és 3,7 nyelveket támogatja.

4. A Python-csomag Azure Identity-hitelesítéshez való telepítéséhez futtassa a következő parancsot:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > Előfordulhat, hogy az "Azure-Identity" csomag egyes gyakori függőségeinél ütközik az "Azure-CLI"-vel. Ha bármilyen hitelesítési probléma teljesül, távolítsa el az Azure-CLI-t és annak függőségeit, vagy használjon tiszta gépet anélkül, hogy az "Azure-CLI" csomagot telepítené a működéséhez.
    
## <a name="create-a-purview-client"></a>A hatáskörébe tartozó ügyfél létrehozása

1. Hozzon létre egy **purview.py** nevű fájlt. Adja hozzá az alábbi utasításokat, hogy a névterekre mutató hivatkozásokat tudjon felvenni.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a PurviewManagementClient osztály egy példányát. Ezzel az objektummal létrehozhat egy hatáskörébe tartozó fiókot, törölheti a hatáskörébe tartozó fiókot, megtekintheti a név rendelkezésre állását és más erőforrás-szolgáltatói műveleteket.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Hatáskörébe tartozó fiók létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **hatáskörébe tartozó fiókot**. Ha az erőforráscsoportja már létezik, tegye megjegyzésbe az első `create_or_update` utasítást.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Most adja meg az alábbi utasítást, hogy a rendszer meghívja a **main** metódust, amikor a program fut:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Teljes szkript

Itt látható a teljes Python-kód:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>A kód futtatása

Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezután például az [Azure Storage Explorerhez](https://azure.microsoft.com/features/storage-explorer/) hasonló eszközök használatával ellenőrizheti, hogy a blobok a változókban megadottak szerint át lettek-e másolva az outputBlobPath helyre az inputBlobPath helyről.

Itt látható a minta kimenete:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Kimenet ellenőrzése

Lépjen a Azure Portal **hatáskörébe tartozó fiókok** lapra, és ellenőrizze a fenti kóddal létrehozott fiókot. 

## <a name="delete-purview-account"></a>A hatáskörébe tartozó fiók törlése

A hatáskörébe tartozó fiók törléséhez adja hozzá a következő kódot a programhoz:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban szereplő kód egy hatáskörébe tartozó fiókot hoz létre, és törli a hatáskörébe tartozó fiókot. Most már letöltheti a Python SDK-t, és megismerheti a hatáskörébe tartozó fiókokhoz elvégezhető egyéb erőforrás-szolgáltatói műveleteket is.

A következő cikkből megtudhatja, hogyan engedélyezheti a felhasználók számára az Azure hatáskörébe tartozó fiók elérését. 

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure-beli hatáskörébe-fiókhoz](catalog-permissions.md)
