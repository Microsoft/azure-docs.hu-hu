---
title: Ügyfél által felügyelt kulcsok beállítása az inaktív adatok titkosításához a ISEs-ben
description: Saját titkosítási kulcsok létrehozása és kezelése az integrációs szolgáltatási környezetek (ISEs) Azure Logic Appsban való biztonságossá tételéhez
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: d31fbd813f0c5d63ee9eddbff5b299209618626b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629674"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Ügyfél által felügyelt kulcsok beállítása az integrációs szolgáltatási környezetek (ISEs-EK) Azure Logic Apps-beli inaktív adatok titkosításához

Azure Logic Apps az Azure Storage-ra támaszkodik az adatok tárolására és automatikus [titkosítására](../storage/common/storage-service-encryption.md). Ez a titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Alapértelmezés szerint az Azure Storage a Microsoft által felügyelt kulcsokat használja az adatai titkosítására. További információ az Azure Storage-titkosítás működéséről: [Az Azure Storage titkosítása a REST](../storage/common/storage-service-encryption.md) -alapú adatok és az Azure-beli [adattitkosítás – nyugalmi](../security/fundamentals/encryption-atrest.md)állapotban.

Ha [integrációs szolgáltatási környezetet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) hoz létre a logikai alkalmazások üzemeltetéséhez, és az Azure Storage által használt titkosítási kulcsok hatékonyabb vezérlését szeretné végezni, akkor a [Azure Key Vault](../key-vault/general/overview.md)használatával beállíthatja, használhatja és kezelheti a saját kulcsát. Ez a képesség "Bring Your Own Key" (BYOK) néven ismert, és a kulcs neve "ügyfél által felügyelt kulcs". Ezzel a képességgel az Azure Storage automatikusan lehetővé teszi a [kettős titkosítást vagy az *infrastruktúra titkosítását* a kulcshoz tartozó platform által felügyelt kulcsok használatával](../security/fundamentals/double-encryption.md) . További információ: az [adattitkosítás megkettőzése az infrastruktúra titkosításával](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption).

Ebből a témakörből megtudhatja, hogyan állíthatja be és adhatja meg saját titkosítási kulcsát, amelyet az ISE a Logic Apps REST API használatával történő létrehozásakor használ. Az ISE Logic Apps REST API használatával történő létrehozásának általános lépéseiért tekintse meg az [integrációs szolgáltatási környezet (ISE) létrehozása az Logic Apps REST API segítségével](../logic-apps/create-integration-service-environment-rest-api.md)című témakört.

## <a name="considerations"></a>Megfontolandó szempontok

* Ebben az esetben az ISE ügyfél által felügyelt kulcsának támogatása csak ezekben az Azure-régiókban érhető el: USA 2. nyugati régiója, USA keleti régiója és az USA déli középső régiója

* Az ügyfél által felügyelt kulcs csak akkor adható meg, *Ha létrehozza az ISE*-t, nem pedig később. Az ISE létrehozása után ezt a kulcsot nem lehet letiltani. Jelenleg nem létezik támogatás egy ügyfél által felügyelt kulcs elforgatásához egy ISE esetében.

* Az ügyfél által felügyelt kulcsok támogatásához az ISE megköveteli, hogy engedélyezze a [rendszer által hozzárendelt vagy a felhasználó által hozzárendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Ez az identitás lehetővé teszi, hogy az ISE hitelesítse a biztonságos erőforrásokhoz, például a virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz való hozzáférést, amelyek egy Azure-beli virtuális hálózathoz tartoznak vagy kapcsolódnak. Így nem kell bejelentkeznie a hitelesítő adataival.

* Jelenleg olyan ISE létrehozásához, amely támogatja az ügyfél által felügyelt kulcsokat, és a felügyelt identitás típusa engedélyezve van, egy HTTPS PUT-kérelem használatával kell meghívnia a Logic Apps REST API.

* A [Key vaultnak hozzáférést kell adnia az ISE felügyelt identitásához](#identity-access-to-key-vault), az időzítés azonban attól függ, hogy melyik felügyelt identitást használja.

  * **Rendszer által hozzárendelt felügyelt identitás**: az ISE-t létrehozó HTTPS Put-kérelem elküldése *után 30 percen* belül meg kell [adnia a Key Vault-hozzáférést az ISE felügyelt identitásához](#identity-access-to-key-vault). Ellenkező esetben az ISE létrehozása meghiúsul, és az engedélyek hibaüzenetet kapnak.

  * **Felhasználó által hozzárendelt felügyelt identitás**: az ISE-t létrehozó HTTPS Put-kérelem elküldése előtt adja meg a [Key Vault számára az ISE felügyelt identitásának elérését](#identity-access-to-key-vault).

## <a name="prerequisites"></a>Előfeltételek

* Ugyanazok az [Előfeltételek](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) és [követelmények, amelyek lehetővé teszik az ISE hozzáférését](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) , mint amikor egy ISE-t hoz létre a Azure Portal

* Egy olyan Azure Key Vault, amely rendelkezik a helyreállítható **törléssel** , és **nem** törölheti az engedélyezett tulajdonságokat

  További információ ezekről a tulajdonságokról: [Azure Key Vault a Soft-delete áttekintése](../key-vault/general/soft-delete-overview.md) és [az ügyfél által felügyelt kulcsok konfigurálása a Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md). Ha még nem ismeri a [Azure Key Vaultt](../key-vault/general/overview.md), megtudhatja, hogyan hozhat létre kulcstartót a [Azure Portal](../key-vault/general/quick-create-portal.md), az [Azure CLI](../key-vault/general/quick-create-cli.md)vagy a [Azure PowerShell](../key-vault/general/quick-create-powershell.md)használatával.

* A Key vaultban a következő tulajdonság-értékekkel létrehozott kulcs:

  | Tulajdonság | Érték |
  |----------|-------|
  | **Kulcs típusa** | RSA |
  | **RSA-kulcs mérete** | 2048 |
  | **Engedélyezve** | Yes |
  |||

  ![Az ügyfél által felügyelt titkosítási kulcs létrehozása](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  További információ: az [ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md) vagy a Azure PowerShell paranccsal, [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Egy eszköz, amellyel létrehozhatja az ISE-t úgy, hogy meghívja a Logic Apps REST API egy HTTPS PUT-kéréssel. Használhatja például a [Poster](https://www.getpostman.com/downloads/)-t, vagy létrehozhat egy logikai alkalmazást, amely elvégzi ezt a feladatot.

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>ISE létrehozása a Key vaulttal és a felügyelt identitások támogatásával

Az ISE létrehozásához a Logic Apps REST API meghívásával végezze el ezt a HTTPS PUT-kérelmet:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A Logic Apps REST API 2019-05-01-es verziójának a saját HTTPS PUT-kérelmét kell tennie az ISE-összekötők számára.

Az üzembe helyezés általában két órán belül befejeződik. Alkalmanként az üzembe helyezés akár négy órát is igénybe vehet. A központi telepítés állapotának megtekintéséhez a [Azure Portal](https://portal.azure.com)az Azure eszköztárán válassza az értesítések ikont, amely megnyitja az értesítések panelt.

> [!NOTE]
> Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure akár egy órát is igénybe vehet az alhálózatok felszabadítása előtt. Ez azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználja ezeket az alhálózatokat egy másik ISE-ben.
>
> Ha törli a virtuális hálózatot, az Azure általában akár két órával az alhálózatok felszabadítása előtt is eltarthat, de ez a művelet hosszabb időt is igénybe vehet. 
> A virtuális hálózatok törlésekor győződjön meg arról, hogy egyetlen erőforrás sincs még csatlakoztatva. 
> Lásd: [virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Kérelem fejléce

A kérelem fejlécében adja meg a következő tulajdonságokat:

* `Content-type`: Állítsa ezt a tulajdonságot értékre `application/json` .

* `Authorization`: Állítsa ezt a tulajdonságot annak az ügyfélnek a tulajdonosi jogkivonatára, aki hozzáfér a használni kívánt Azure-előfizetéshez vagy erőforráscsoporthoz.

### <a name="request-body"></a>A kérés törzse

A kérelem törzsében engedélyezze a további elemek támogatását az ISE-definícióban található információk megadásával:

* Az ISE által a Key vaulthoz való hozzáféréshez használt felügyelt identitás
* A Key Vault és a használni kívánt ügyfél által felügyelt kulcs

#### <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa

Itt látható a kérelem törzsének szintaxisa, amely leírja az ISE létrehozásakor használandó tulajdonságokat:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Példa a kérelem szövegtörzsére

A példaként szolgáló kérelem törzse a következő minta értékeket jeleníti meg:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

Bár az időzítés eltér a használt felügyelt identitástól, meg kell [adnia a Key vaulthoz való hozzáférést az ISE felügyelt identitásához](#identity-access-to-key-vault).

* **Rendszer által hozzárendelt felügyelt identitás**: az ISE-t létrehozó HTTPS Put-kérelem elküldése *után 30 percen* belül hozzá kell adnia egy hozzáférési szabályzatot a kulcstartóhoz az ISE rendszerhez rendelt felügyelt identitásához. Ellenkező esetben az ISE létrehozása meghiúsul, és az engedélyek hibaüzenetet kapnak.

* **Felhasználó által hozzárendelt felügyelt identitás**: mielőtt elküldte az ISE-t létrehozó HTTPS Put-kérést, adjon hozzá egy hozzáférési szabályzatot a kulcstartóhoz az ISE felhasználó által hozzárendelt felügyelt identitásához.

Ehhez a feladathoz használhatja a Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsot, vagy a következő lépésekkel követheti el a Azure Portal:

1. A [Azure Portal](https://portal.azure.com)nyissa meg az Azure Key vaultot.

1. A Key Vault menüjében válassza a **hozzáférési szabályzatok**  >  **hozzáférési házirend hozzáadása** lehetőséget, például:

   ![Hozzáférési szabályzat hozzáadása a rendszerhez rendelt felügyelt identitáshoz](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. A **hozzáférési szabályzat hozzáadása** panel megnyitása után kövesse az alábbi lépéseket:

   1. Válassza ki ezeket a beállításokat:

      | Beállítás | Értékek |
      |---------|--------|
      | **Konfigurálás sablonból (nem kötelező) lista** | Kulcskezelő |
      | **Kulcs engedélyei** | - **Kulcskezelő műveletek**: lekérés, Listázás <p><p>- **Titkosítási műveletek**: kicsomagolási kulcs, betakarás kulcsa |
      |||

      ![Válassza a "kulcskezelő" > "kulcs engedélyei" elemet.](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Válassza a **tag kiválasztása** lehetőséget, majd a **nincs kiválasztva** lehetőséget. A **résztvevő** ablaktábla megnyitása után keresse meg és válassza ki az ISE elemet a keresőmezőbe. Ha elkészült, válassza a Hozzáadás **lehetőséget**  >  .

      ![Válassza ki a rendszerbiztonsági tagként használni kívánt ISE-t](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Ha elkészült a **hozzáférési szabályzatok** ablaktáblával, válassza a **Mentés** lehetőséget.

További információ: a [hitelesítés Key Vault](../key-vault/general/authentication.md) és [egy Key Vault hozzáférési szabályzat kiosztása](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Key Vault](../key-vault/general/overview.md)
