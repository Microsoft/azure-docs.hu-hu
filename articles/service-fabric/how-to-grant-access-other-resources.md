---
title: Alkalmazás-hozzáférés biztosítása más Azure-erőforrásokhoz
description: Ez a cikk azt ismerteti, hogyan biztosítható a felügyelt identitást támogató Service Fabric alkalmazásokhoz való hozzáférés más Azure-erőforrásokhoz Azure Active Directory-alapú hitelesítés támogatásával.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: c7560294fbf6d122396b6a5a8ffd3ee93bc89048
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97507455"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Service Fabric alkalmazás felügyelt identitás-hozzáférésének megadása az Azure-erőforrásokhoz

Ahhoz, hogy az alkalmazás a felügyelt identitásával hozzáférhessen más erőforrásokhoz, engedélyeket kell adni ahhoz az identitáshoz a védett Azure-erőforráson. Az engedélyek megadása általában a Azure Resource Manageron keresztül átirányított védett erőforrást birtokló Azure-szolgáltatás vezérlési síkon található felügyeleti művelet, amely az összes vonatkozó szerepköralapú hozzáférés-ellenőrzést kikényszeríti.

A lépések pontos lépései ezután az elérhető Azure-erőforrás típusától, valamint az engedélyek megadásához használt nyelvtől/ügyféltől függenek. A cikk hátralévő része feltételezi, hogy a felhasználó által hozzárendelt identitás az alkalmazáshoz van rendelve, és számos tipikus példát tartalmaz a kényelemre, de ez a témakör semmilyen módon nem használható. az engedélyek megadásáról a megfelelő Azure-szolgáltatások dokumentációjában tájékozódhat.  

## <a name="granting-access-to-azure-storage"></a>Hozzáférés biztosítása az Azure Storage-hoz
Az adatok Azure Storage-blobból való lekéréséhez használhatja a Service Fabric alkalmazás felügyelt identitását (ebben az esetben a felhasználóhoz rendelt). Adja meg a szükséges engedélyeket a Azure Portal a következő lépésekkel:

1. Navigáljon a Storage-fiókhoz
2. Kattintson a Hozzáférés-vezérlés (IAM) hivatkozásra a bal oldali panelen.
3. választható Meglévő hozzáférés ellenőrzése: válassza a rendszer vagy a felhasználó által hozzárendelt felügyelt identitás elemet a keresés vezérlőelemben; Válassza ki a megfelelő identitást az eredményül kapott eredmények listájából
4. Kattintson a + szerepkör-hozzárendelés hozzáadása lehetőségre az oldal tetején egy új szerepkör-hozzárendelés hozzáadásához az alkalmazás identitásához.
A szerepkör alatt a legördülő listából válassza a Storage blob-Adatolvasó lehetőséget.
5. A következő legördülő menüben válassza a hozzáférés társítása elemet `User assigned managed identity` .
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az Előfizetés legördülő menüben, majd állítsa az Erőforráscsoport értékét a Minden erőforráscsoport értékre.
7. A kiválasztás területen válassza ki a Service Fabric alkalmazásnak megfelelő UAI, majd kattintson a Mentés gombra.

A rendszer által hozzárendelt Service Fabric felügyelt identitások támogatása nem tartalmazza a Azure Portal integrációját; Ha az alkalmazás rendszer által hozzárendelt identitást használ, először meg kell keresnie az alkalmazás identitásának ügyfél-AZONOSÍTÓját, majd újra meg kell ismételnie a fenti lépéseket, de a keresés vezérlőelemben is ki kell választania a `Azure AD user, group, or service principal` lehetőséget.

## <a name="granting-access-to-azure-key-vault"></a>Hozzáférés biztosítása Azure Key Vault
A tárterület eléréséhez hasonlóan kihasználhatja egy Service Fabric alkalmazás felügyelt identitását az Azure Key Vault eléréséhez. A hozzáférés megadásának lépései a Azure Portalban hasonlóak a fent felsoroltak esetében, és itt nem ismétlődik meg. A különbségeket az alábbi képen tekintheti meg.

![Hozzáférési szabályzat Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Az alábbi példa azt szemlélteti, hogyan lehet hozzáférést biztosítani a tárolóhoz egy sablon központi telepítése révén; adja hozzá az alábbi kódrészlet (eke) t a sablon eleme alá tartozó másik bejegyzéshez `resources` . A minta azt mutatja be, hogy a hozzáférés megadására a felhasználó által hozzárendelt és a rendszer által hozzárendelt identitások típusai is érvényesek, vagy válassza ki a megfelelőt.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
És rendszerhez rendelt felügyelt identitások esetén:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

További részletekért lásd: tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Következő lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
