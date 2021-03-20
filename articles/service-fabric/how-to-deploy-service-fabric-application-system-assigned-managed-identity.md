---
title: Service Fabric alkalmazás üzembe helyezése rendszer által hozzárendelt MI
description: Ez a cikk bemutatja, hogyan rendelhet hozzá egy rendszerhez rendelt felügyelt identitást egy Azure Service Fabric-alkalmazáshoz
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81415653"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Service Fabric alkalmazás központi telepítése rendszer által hozzárendelt felügyelt identitással

Az Azure Service Fabric-alkalmazások felügyelt identitás funkciójának eléréséhez először engedélyeznie kell a felügyelt Identity token szolgáltatást a fürtön. Ez a szolgáltatás felelős a felügyelt identitások használatával Service Fabric alkalmazások hitelesítéséhez, valamint a hozzáférési jogkivonatok nevében való beszerzéséhez. Ha a szolgáltatás engedélyezve van, a bal oldali ablaktábla **System (rendszer** ) szakaszának Service Fabric Explorer alatt láthatja, hogy az egyéb rendszerszolgáltatások mellett a **háló:/System/ManagedIdentityTokenService** néven fut.

> [!NOTE] 
> Service Fabric alkalmazások felügyelt identitásokkal való üzembe helyezése az API-verziótól kezdődően támogatott `"2019-06-01-preview"` . Ugyanezt az API-verziót használhatja az alkalmazás típusa, az alkalmazás típusa és a szolgáltatás erőforrásai esetében is. A Service Fabric futtatókörnyezet minimálisan támogatott 6,5 CU2. A additoin-ben a Build/Package környezetnek az SF .net SDK-t is meg kell adni a CU2 vagy újabb verziónál

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

### <a name="application-template"></a>Alkalmazássablon

Ha egy rendszer által hozzárendelt felügyelt identitással szeretné engedélyezni az alkalmazást, adja hozzá az **Identity** tulajdonságot az alkalmazás-erőforráshoz az alábbi példában látható módon: **systemAssigned** .

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Ez a tulajdonság deklarálja (Azure Resource Manager, valamint a felügyelt identitást és Service Fabric erőforrás-szolgáltatót, hogy az erőforrásnak implicit ( `system assigned` ) felügyelt identitással kell rendelkeznie.

### <a name="application-and-service-package"></a>Alkalmazás-és szolgáltatáscsomag

1. Frissítse az alkalmazás jegyzékfájlját egy **ManagedIdentity** elem hozzáadásához a **rendszerbiztonsági tag** szakaszban, amely egyetlen bejegyzést tartalmaz az alábbi ábrán látható módon:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Ez leképezi az alkalmazáshoz rendelt identitást erőforrásként egy rövid névre, az alkalmazást tartalmazó szolgáltatásokhoz való további hozzárendeléshez. 

2. A felügyelt identitást hozzárendelő szolgáltatáshoz tartozó **ServiceManifestImport** szakaszban adjon hozzá egy **IdentityBindingPolicy** elemet az alábbiak szerint:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Ez az elem az alkalmazás identitását rendeli hozzá a szolgáltatáshoz. a hozzárendelés nélkül a szolgáltatás nem fogja tudni elérni az alkalmazás identitását. A fenti kódrészletben az `SystemAssigned` identitás (amely egy foglalt kulcsszó) a szolgáltatás definíciójában van leképezve a rövid név alatt `WebAdmin` .

3. Frissítse a szolgáltatás jegyzékfájlját egy **ManagedIdentity** elemnek az **erőforrások** szakaszban való hozzáadásához az `ServiceIdentityRef` `IdentityBindingPolicy` alkalmazás jegyzékfájljának definíciójában szereplő beállítás értékének megfelelő névvel:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Ez egy, a fentiekben leírtak szerint a szolgáltatás azonosságának megfelelő leképezése, de a szolgáltatás definíciójának szemszögéből. Az identitást itt `WebAdmin` tekintheti meg az alkalmazás jegyzékfájljában deklarált rövid névvel ().

## <a name="next-steps"></a>Következő lépések
* [Felügyelt identitások támogatásának](./concepts-managed-identity.md) áttekintése az Azure Service Fabric
* [Új telepítése](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-fürt felügyelt identitások támogatásával 
* [Felügyelt identitás engedélyezése](./configure-existing-cluster-enable-managed-identity-token-service.md) meglévő Azure Service Fabric-fürtben
* Service Fabric alkalmazás [felügyelt identitásának kihasználása a forráskódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
