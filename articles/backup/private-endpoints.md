---
title: Privát végpontok
description: Megtudhatja, hogyan hozhat létre privát végpontokat a Azure Backuphoz, és hogy a saját végpontok használata hogyan segít megőrizni az erőforrások biztonságát.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0d9d77c139896f9067f73943dbb213fc655f00f6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054872"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure Backup magánhálózati végpontok

Azure Backup lehetővé teszi az adatok biztonságos biztonsági mentését és visszaállítását a Recovery Services-tárolóból [privát végpontok](../private-link/private-endpoint-overview.md)használatával. A privát végpontok egy vagy több magánhálózati IP-címet használnak a VNet, és így hatékonyan helyezik üzembe a szolgáltatást a VNet.

Ez a cikk segít megérteni a Azure Backup magánhálózati végpontok létrehozásának folyamatát, valamint azokat a forgatókönyveket, amelyekben a privát végpontok használata hozzájárul az erőforrások biztonságának fenntartásához.

## <a name="before-you-start"></a>Előkészületek

- A magánhálózati végpontok csak az új Recovery Services-tárolók számára hozhatók létre (amelyek nem rendelkeznek a tárolóhoz regisztrált elemekkel). Ezért létre kell hozni a privát végpontokat, mielőtt a tár bármely elemét védetté tenné.
- Egy virtuális hálózat több Recovery Services-tárolóhoz is tartalmazhat privát végpontokat. Emellett egy Recovery Services-tár több virtuális hálózatban is rendelkezhet saját végpontokkal. Azonban a tárolóhoz létrehozható privát végpontok maximális száma 12.
- Miután létrehozta a tárolóhoz egy privát végpontot, a rendszer zárolja a tárolót. Nem lesz elérhető (biztonsági mentéshez és visszaállításhoz) a hálózatokon kívülről is, amelyek a tárolóhoz privát végpontot tartalmaznak. Ha a tár összes privát végpontja törlődik, a tároló minden hálózatról elérhető lesz.
- A biztonsági mentéshez használt privát végponti kapcsolatok összesen 11 magánhálózati IP-címet használnak az alhálózaton, beleértve a Azure Backup által a Storage szolgáltatáshoz használt. Az egyes Azure-régiók esetében ez a szám magasabb (akár 25) is lehet. Ezért javasoljuk, hogy elegendő privát IP-cím álljon rendelkezésre, ha privát végpontokat próbál létrehozni a biztonsági mentéshez.
- A Recovery Services-tárolót (mindkettő) Azure Backup és Azure Site Recovery is használja, ez a cikk a privát végpontok használatát ismerteti csak Azure Backup esetén.
- Azure Active Directory jelenleg nem támogatja a privát végpontokat. A Azure Active Directory működéséhez szükséges IP-címeket és teljes tartományneveket engedélyezni kell a biztonságos hálózatról, amikor az Azure-beli virtuális gépeken található adatbázisok biztonsági mentését hajtja végre, és a MARS-ügynök használatával készít biztonsági mentést. NSG-címkéket és Azure Firewall címkéket is használhat az Azure AD-hez való hozzáférés engedélyezéséhez.
- A hálózati házirendekkel rendelkező virtuális hálózatok magánhálózati végpontok esetén nem támogatottak. A folytatás előtt le kell tiltania a hálózati házirendeket.
- A Recovery Services erőforrás-szolgáltatót újra regisztrálnia kell az előfizetéssel, ha azt a 1 2020. május előtt regisztrálta. A szolgáltató újbóli regisztrálásához nyissa meg az előfizetését a Azure Portalban, navigáljon az **erőforrás-szolgáltatóhoz** a bal oldali navigációs sávon, majd válassza a **Microsoft. recoveryservices szolgáltatónál** elemet, és válassza az **ismételt regisztráció** lehetőséget.
- Az SQL-és SAP HANA-adatbázisok [régiók közötti visszaállítása](backup-create-rs-vault.md#set-cross-region-restore) nem támogatott, ha a tárolón engedélyezve vannak a privát végpontok.

## <a name="recommended-and-supported-scenarios"></a>Ajánlott és támogatott forgatókönyvek

Habár a privát végpontok engedélyezve vannak a tárolóhoz, az SQL-és SAP HANA-munkaterhelések biztonsági mentésére és helyreállítására szolgálnak az Azure-beli virtuális gépeken és a MARS-ügynök biztonsági mentésében. A tárolót más számítási feladatok biztonsági mentéséhez is használhatja (a saját végpontokat azonban nem igénylik). Az SQL és a SAP HANA számítási feladatainak és a MARS-ügynök használatával történő biztonsági mentésének kiegészítéseként az Azure-beli virtuális gépek biztonsági mentésére szolgáló fájlok helyreállítására is használhatók a privát végpontok. További információkért tekintse meg a következő táblázatot:

| Munkaterhelések biztonsági mentése az Azure-beli virtuális gépen (SQL, SAP HANA), biztonsági mentés a MARS-ügynök használatával | A privát végpontok használata ajánlott a biztonsági mentés és a helyreállítás engedélyezéséhez anélkül, hogy a virtuális hálózatokról Azure Backup vagy Azure Storage-ra vonatkozó IP-címeket/teljes tartományneveket kellene engedélyezési. Ebben az esetben győződjön meg arról, hogy az SQL Database-t futtató virtuális gépek hozzáférhetnek az Azure AD IP-címeihez vagy teljes tartománynevéhez. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure-beli virtuális gép biztonsági mentése**                                         | A virtuális gép biztonsági mentése nem igényli, hogy bármely IP-cím vagy teljes tartománynév számára engedélyezze a hozzáférést. Így nincs szükség privát végpontokra a lemezek biztonsági mentéséhez és visszaállításához.  <br><br>   A privát végpontokat tartalmazó tárolóból származó fájlok helyreállítása azonban olyan virtuális hálózatokra korlátozódik, amelyek a tárolóhoz privát végpontot tartalmaznak. <br><br>    A ACL'ed nem felügyelt lemezek használata esetén győződjön meg arról, hogy a lemezeket tartalmazó Storage-fiók lehetővé teszi a **megbízható Microsoft-szolgáltatások** elérését, ha az ACL'ed. |
| **Azure Files biztonsági mentés**                                      | Azure Files biztonsági mentéseket a helyi Storage-fiók tárolja. Így nincs szükség privát végpontokra a biztonsági mentéshez és a visszaállításhoz. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Privát végpontok létrehozása és használata a biztonsági mentéshez

Ez a szakasz a virtuális hálózatokon belüli Azure Backup magánhálózati végpontok létrehozásával és használatával kapcsolatos lépésekkel foglalkozik.

>[!IMPORTANT]
> Javasoljuk, hogy kövesse az ebben a dokumentumban említett lépéseket. Ennek elmulasztása miatt előfordulhat, hogy a tár nem kompatibilis a privát végpontok használatára, és egy új tárolóval újra kell indítania a folyamatot.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

[Ebből a szakaszból](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) megtudhatja, hogyan hozhat létre tárolót az Azure Resource Manager-ügyfél használatával. Ezzel létrehoz egy tárolót, amelynek felügyelt identitása már engedélyezve van. További információ a Recovery Services-tárolóról [.](./backup-azure-recovery-services-vault-overview.md)

## <a name="enable-managed-identity-for-your-vault"></a>Felügyelt identitás engedélyezése a tárolóhoz

A felügyelt identitások lehetővé teszik, hogy a tároló privát végpontokat hozzon létre és használjon. Ez a szakasz a tár felügyelt identitásának engedélyezését tárgyalja.

1. Nyissa meg a Recovery Services > **identitását**.

    ![Identitás állapotának módosítása a következőre](./media/private-endpoints/identity-status-on.png)

1. Módosítsa az **állapotot** **be értékre, majd** válassza a **Mentés** lehetőséget.

1. A rendszer létrehoz egy **objektumazonosító-azonosítót** , amely a tár felügyelt identitása.

    >[!NOTE]
    >Ha engedélyezve van, a felügyelt identitás **nem** tiltható le (akár átmenetileg is). A felügyelt identitás letiltása inkonzisztens viselkedést eredményezhet.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Engedélyek megadása a tárolónak a szükséges privát végpontok létrehozásához

A Azure Backuphoz szükséges privát végpontok létrehozásához a tárolónak (a tár felügyelt identitásának) a következő erőforráscsoportok engedélyekkel kell rendelkeznie:

- A célként megadott VNet tartalmazó erőforráscsoport
- Az erőforráscsoport, amelyben létre kell hozni a privát végpontokat
- Az saját DNS zónákat tartalmazó erőforráscsoport, ahogy az [itt](#creating-private-endpoints-for-backup) részletesen szerepel

Javasoljuk, hogy adja meg a **közreműködői** szerepkört a három erőforráscsoport számára a tárolóhoz (felügyelt identitás). A következő lépések azt írják le, hogyan kell ezt megtenni egy adott erőforráscsoport esetében (ezt a három erőforráscsoporthoz kell elvégezni):

1. Nyissa meg az erőforráscsoportot, és navigáljon a **Access Control (iam)** elemre a bal oldali sávon.
1. Ha **Access Control**, lépjen a **szerepkör-hozzárendelés hozzáadása** elemre.

    ![Szerepkör-hozzárendelés hozzáadása](./media/private-endpoints/add-role-assignment.png)

1. A **szerepkör-hozzárendelés hozzáadása** panelen válassza a **közreműködő** **szerepkört**, és használja a tároló **nevét** a **rendszerbiztonsági tag** néven. Válassza ki a tárolót, és kattintson a **Mentés** gombra, ha elkészült.

    ![Szerepkör és rendszerbiztonsági tag kiválasztása](./media/private-endpoints/choose-role-and-principal.png)

Az engedélyek részletesebb kezeléséhez tekintse meg a [szerepkörök és engedélyek manuális létrehozása](#create-roles-and-permissions-manually)című témakört.

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Azure Backup magánhálózati végpontok létrehozása és jóváhagyása

### <a name="creating-private-endpoints-for-backup"></a>Privát végpontok létrehozása a biztonsági mentéshez

Ez a szakasz azt ismerteti, hogyan hozható létre saját végpont a tárolóhoz.

1. A keresési sávban keresse meg és válassza ki a **privát hivatkozás** lehetőséget. Ekkor megjelenik a **privát kapcsolati központ**.

    ![Privát hivatkozás keresése](./media/private-endpoints/search-for-private-link.png)

1. A bal oldali navigációs sávon válassza a **privát végpontok** lehetőséget. Egyszer a **privát végpontok** ablaktáblán válassza a **+ Hozzáadás** lehetőséget a saját tárolóhoz tartozó privát végpont létrehozásához.

    ![Privát végpont hozzáadása a privát kapcsolati központban](./media/private-endpoints/add-private-endpoint.png)

1. Ha a **privát végpont létrehozása** folyamatban van, meg kell adnia a privát végponti kapcsolat létrehozásához szükséges adatokat.

    1. **Alapismeretek**: adja meg a privát végpontok alapszintű részleteit. A régiónak meg kell egyeznie a tárolóval és az erőforrással.

        ![Adja meg az alapszintű részleteket](./media/private-endpoints/basic-details.png)

    1. **Erőforrás**: ezen a lapon megemlítheti azt a Pásti-erőforrást, amelyhez létre kívánja hozni a kapcsolatát. Válassza ki a **Microsoft. recoveryservices szolgáltatónál/Vaults** elemet a kívánt előfizetés erőforrástípus alapján. Ha elkészült, válassza ki a Recovery Services-tároló nevét az **erőforrás** -és **AzureBackup** célként megadott **alerőforrásként**.

        ![Töltse ki az Erőforrás lapot](./media/private-endpoints/resource-tab.png)

    1. **Konfiguráció**: a konfiguráció területen válassza ki azt a virtuális hálózatot és alhálózatot, ahol létre szeretné hozni a privát végpontot. Ez lesz a vnet, ahol a virtuális gép megtalálható. Dönthet úgy, hogy **integrálja a privát végpontot** egy privát DNS-zónával. Másik lehetőségként használhatja az egyéni DNS-kiszolgálót, vagy létrehozhat egy magánhálózati DNS-zónát is.

        ![Kitöltés a konfiguráció lapon](./media/private-endpoints/configuration-tab.png)

        Ha az Azure saját DNS zónákhoz való integrálás helyett az egyéni DNS-kiszolgálókat szeretné használni, tekintse meg [ezt a szakaszt](#dns-changes-for-custom-dns-servers) .  

    1. Igény szerint hozzáadhat **címkéket** a privát végponthoz.

    1. Folytassa a **felülvizsgálat + létrehozás** után a részletek beírásával. Ha az ellenőrzés befejeződött, válassza a **Létrehozás** lehetőséget a privát végpont létrehozásához.

## <a name="approving-private-endpoints"></a>Privát végpontok jóváhagyása

Ha a privát végpontot létrehozó felhasználó egyben a Recovery Services-tároló tulajdonosa is, a fent létrehozott privát végpont automatikusan jóvá lesz hagyva. Ellenkező esetben a tároló tulajdonosának jóvá kell hagynia a privát végpontot, mielőtt használni tudná. Ez a szakasz a privát végpontok manuális jóváhagyását ismerteti a Azure Portalon keresztül.

Lásd: [privát végpontok manuális jóváhagyása a Azure Resource Manager ügyfél használatával](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) a Azure Resource Manager ügyfél használatára a privát végpontok jóváhagyásához.

1. A Recovery Services-tárolóban navigáljon a bal oldali sávban található **privát végponti kapcsolatokhoz** .
1. Válassza ki a jóváhagyni kívánt privát végpont-kapcsolatokat.
1. Válassza a **jóváhagyás** lehetőséget a felső sávon. Az **elutasítás** vagy az **Eltávolítás** lehetőséget is választhatja, ha el kívánja utasítani vagy törölni szeretné a végponti kapcsolatokat.

    ![Privát végpontok jóváhagyása](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Privát végpontok használata biztonsági mentéshez

Miután jóváhagyta a VNet-tárolóhoz létrehozott privát végpontokat, megkezdheti a biztonsági mentések és a visszaállítások elvégzését.

>[!IMPORTANT]
>A folytatás előtt győződjön meg arról, hogy sikeresen elvégezte az összes fent említett lépést a dokumentumban. A betöltéshez el kell végeznie az alábbi ellenőrzőlista lépéseit:
>
>1. Létrehozott egy (új) Recovery Services-tárolót
>1. A tár engedélyezése a rendszerhez rendelt felügyelt identitás használatára
>1. A tár felügyelt identitásához hozzárendelt megfelelő engedélyek
>1. Létrehozott egy privát végpontot a tárolóhoz
>1. Jóváhagyta a privát végpontot (ha nem engedélyezett automatikusan)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Munkaterhelések biztonsági mentése és visszaállítása az Azure-beli virtuális gépen (SQL, SAP HANA)

Miután létrehozta és jóváhagyta a privát végpontot, a privát végpont használatához nincs szükség további módosításokra az ügyfél oldalán. A biztonságos hálózatról a tárolóra irányuló összes kommunikációt és adatátvitelt a privát végponton keresztül hajtja végre a rendszer.
Ha azonban egy kiszolgáló (SQL/SAP HANA) regisztrálása után eltávolít egy privát végpontot a tárolóhoz, akkor újra regisztrálnia kell a tárolót a tárolóban. Nem kell leállítania a védelmet.

### <a name="backup-and-restore-through-mars-agent"></a>Biztonsági mentés és visszaállítás a MARS-ügynökön keresztül

Ha a MARS-ügynököt használja a helyszíni erőforrások biztonsági mentésére, győződjön meg arról, hogy a helyszíni hálózat (a biztonsági mentéshez szükséges erőforrásokat tartalmazza) az Azure-VNet van társítva, amely a tárolóhoz saját végpontot tartalmaz, így használhatja azt. Ezután továbbra is telepítheti a MARS-ügynököt, és konfigurálhatja a biztonsági mentést az itt leírtak szerint. Gondoskodnia kell azonban arról, hogy a biztonsági mentés minden kommunikációja csak a csak a hálózaton keresztül történjen.

Ha azonban a MARS-ügynök regisztrálása után eltávolítja a tárolóhoz tartozó magánhálózati végpontokat, újra regisztrálnia kell a tárolót a tárolóban. Nem kell leállítania a védelmet.

## <a name="additional-topics"></a>További témakörök

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Recovery Services-tároló létrehozása az Azure Resource Manager ügyfél használatával

Létrehozhatja a Recovery Services-tárolót, és engedélyezheti a felügyelt identitását (a felügyelt identitás engedélyezése szükséges, ahogy azt később látni fogjuk) a Azure Resource Manager-ügyfél használatával. Ehhez az alábbi módon kell megosztva egy mintát:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

A fenti JSON-fájlnak a következő tartalommal kell rendelkeznie:

Kérelem JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Válasz JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>A példában a Azure Resource Manager ügyfélen létrehozott tár már létre van hozva egy rendszer által hozzárendelt felügyelt identitással.

### <a name="managing-permissions-on-resource-groups"></a>Erőforráscsoportok engedélyeinek kezelése

A tár felügyelt identitásának a következő engedélyekkel kell rendelkeznie az erőforráscsoport és a virtuális hálózat között, ahol a magánhálózati végpontok létre lesznek hozva:

- `Microsoft.Network/privateEndpoints/*` Ez szükséges ahhoz, hogy a szifilisz az erőforráscsoport privát végpontján legyen végrehajtva. Hozzá kell rendelni az erőforráscsoporthoz.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Erre azért van szükség, mert a virtuális hálózaton a magánhálózati IP-cím csatlakozik a privát végponthoz.
- `Microsoft.Network/networkInterfaces/read` Erre azért van szükség az erőforráscsoporthoz, hogy megkapja a magánhálózati végponthoz létrehozott hálózati adaptert.
- Saját DNS zóna közreműködői szerepköre ez a szerepkör már létezik, és használható a biztosításához és az engedélyek megadásához `Microsoft.Network/privateDnsZones/A/*` `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` .

A szükséges engedélyekkel rendelkező szerepkörök létrehozásához az alábbi módszerek egyikét használhatja:

#### <a name="create-roles-and-permissions-manually"></a>Szerepkörök és engedélyek manuális létrehozása

Hozza létre a következő JSON-fájlokat, és használja a szakasz végén található PowerShell-parancsot a szerepkörök létrehozásához:

PrivateEndpointContributorRoleDef.jsbekapcsolva

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsbekapcsolva

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsbekapcsolva

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Parancsfájl használata

1. Indítsa el a **Cloud Shell** a Azure Portalban, és válassza a **fájl feltöltése** lehetőséget a PowerShell ablakban.

    ![Válassza a fájl feltöltése a PowerShell-ablakban lehetőséget.](./media/private-endpoints/upload-file-in-powershell.png)

1. Töltse fel a következő szkriptet: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Nyissa meg a saját mappáját (például: `cd /home/user` )

1. Futtassa a következő parancsfájlt:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Ezek a paraméterek:

    - **előfizetés**: * * olyan SubscriptionId, amelyhez a tárolóhoz tartozó magánhálózati végpontot kell létrehozni, valamint azt az alhálózatot, ahol a tár magánhálózati végpontja csatolva lesz

    - **vaultPEResourceGroup**: az erőforráscsoport, amelyben létrejön a tár magánhálózati végpontja

    - **vaultPESubnetResourceGroup**: annak az alhálózatnak az erőforráscsoport, amelyhez a magánhálózati végpont csatlakozni fog

    - **vaultMsiName**: a tár MSI-fájljának neve, amely ugyanaz, mint a **VaultName**

1. Végezze el a hitelesítést, és a szkript a fent megadott előfizetés kontextusát fogja elvégezni. Létrehozza a megfelelő szerepköröket, ha hiányoznak a bérlőből, és hozzárendeli a szerepköröket a tároló MSI-hez.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Privát végpontok létrehozása Azure PowerShell használatával

#### <a name="auto-approved-private-endpoints"></a>Automatikusan jóváhagyott privát végpontok

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Privát végpontok manuális jóváhagyása az Azure Resource Manager ügyfél használatával

1. A **GetVault** használatával szerezze be a privát végponthoz tartozó MAGÁNHÁLÓZATI kapcsolatok azonosítóját.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Ez visszaküldi a magánhálózati végponti kapcsolatok AZONOSÍTÓját. A kapcsolat neve a következő módon kérhető le a kapcsolati azonosító első részének használatával:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Kérje le a **privát végponti kapcsolat azonosítóját** (és a **magánhálózati végpont nevét**, ahol szükséges) a válaszból, és cserélje le a következő JSON-és Azure Resource Manager URI-ra, majd próbálja meg módosítani az állapotot "jóváhagyott/visszautasított/leválasztott" értékre, ahogyan az alábbi példában látható:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>DNS-változások az egyéni DNS-kiszolgálókon

#### <a name="create-dns-zones-for-custom-dns-servers"></a>DNS-zónák létrehozása egyéni DNS-kiszolgálókhoz

Létre kell hoznia három privát DNS-zónát, és csatolnia kell őket a virtuális hálózathoz. Ne feledje, hogy a blob és a várólista használatával ellentétben a Backup szolgáltatás nyilvános URL-címei nem regisztrálják az Azure nyilvános DNS-ben az átirányítást a privát kapcsolat DNS-zónái számára. 

| **Zóna**                                                     | **Szolgáltatás** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | Üzenetsor       |

>[!NOTE]
>A fenti szövegben a *geo* a régiókódra hivatkozik. Például: *wcus* és *ne* az USA nyugati középső régiójában és Észak-Európában.

Tekintse át [ezt a listát](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) a régiókódokhoz. Tekintse meg az alábbi hivatkozásokat az URL-elnevezési konvenciók a nemzeti régiókban:

- [Kína](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Németország](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>DNS-rekordok hozzáadása az egyéni DNS-kiszolgálókhoz

Ehhez a privát végponton lévő összes FQDN-hez bejegyzéseket kell készítenie a saját DNS zónába.

Érdemes megjegyezni, hogy a biztonsági mentéshez, a Blobhoz és a Queue szolgáltatáshoz létrehozott privát végpontokat fogjuk használni.

- A tárolóhoz tartozó magánhálózati végpont a magánhálózati végpont létrehozásakor megadott nevet használja.
- A blob-és üzenetsor-szolgáltatásokhoz tartozó magánhálózati végpontok előre vannak meghatározva a tárolóhoz tartozó névvel.

Az alábbi képen például a *pee2epe* nevű privát végponti kapcsolatban létrehozott három privát végpont látható:

![Három privát végpont a privát végponti kapcsolatok számára](./media/private-endpoints/three-private-endpoints.png)

A biztonsági mentési szolgáltatás DNS-zónája ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Navigáljon a privát végponthoz a biztonsági mentéshez a **Private link Centerben**. Az áttekintő lap felsorolja a privát végpont teljes tartománynevét és magánhálózati IP-címeit.

1. Adjon hozzá egy bejegyzést minden FQDN és magánhálózati IP-cím típusú rekordként.

    ![Bejegyzés hozzáadása az egyes FQDN-és magánhálózati IP-címekhez](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

A Blob service DNS-zónája ( `privatelink.blob.core.windows.net` ):

1. Navigáljon a privát végponthoz a Blobhoz a **Private link Centerben**. Az áttekintő lap felsorolja a privát végpont teljes tartománynevét és magánhálózati IP-címeit.

1. Adjon hozzá egy bejegyzést a teljes tartománynév és a magánhálózati IP-cím típusú rekordként.

    ![Adja hozzá a teljes tartománynév és a magánhálózati IP-cím bejegyzést a Blob service](./media/private-endpoints/add-type-a-record-for-blob.png)

A Queue szolgáltatás DNS-zónája ( `privatelink.queue.core.windows.net` ):

1. Navigáljon a privát végponthoz a **privát kapcsolati központban**. Az áttekintő lap felsorolja a privát végpont teljes tartománynevét és magánhálózati IP-címeit.

1. Adjon hozzá egy bejegyzést a teljes tartománynév és a magánhálózati IP-cím típusú rekordként.

    ![Adja hozzá a teljes tartománynév és a magánhálózati IP-cím bejegyzést a Queue szolgáltatás](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. Létrehozhatok privát végpontot egy meglévő Backup-tárolóhoz?<br>
A. Nem, a saját végpontok csak az új biztonsági mentési tárolók számára hozhatók létre. Így a tár nem rendelkezhetett olyan elemmel, amely védett volt. Valójában a privát végpontok létrehozása előtt nem lehet a tárolóhoz tartozó elemeket védelemmel ellátni.

K. Megpróbáltam védelemmel ellátni egy elemet a tárolóban, de nem sikerült, és a tár továbbra sem tartalmaz védett elemeket. Létrehozhatok privát végpontokat ehhez a tárolóhoz?<br>
A. Nem, a tár nem rendelkezhet semmilyen, a múltbeli elemek elleni védelemmel kapcsolatos kísérlettel.

K. Olyan tárolóval rendelkezem, amely privát végpontokat használ a biztonsági mentéshez és a visszaállításhoz. Később is Hozzáadhatok vagy eltávolíthatok privát végpontokat ehhez a tárolóhoz, még akkor is, ha a biztonsági másolati elemek védettek?<br>
A. Igen. Ha már létrehozott privát végpontokat egy tárolóhoz, és védett biztonsági másolati elemeket hoz létre, akkor később szükség szerint hozzáadhat vagy eltávolíthat privát végpontokat.

K. Használható-e a Azure Backup privát végpontja a Azure Site Recoveryhoz is?<br>
A. Nem, a biztonsági mentéshez használt magánhálózati végpont csak Azure Backup használható. Ha a szolgáltatás támogatja, létre kell hoznia egy új privát végpontot a Azure Site Recoveryhoz.

K. Kihagytam a cikkben szereplő lépések egyikét, és folytattam az adatforrások védelme érdekében. Továbbra is használhatok privát végpontokat?<br>
A. Nem követi a cikkben szereplő lépéseket, és az elemek védelemmel való ellátása azt eredményezheti, hogy a tár nem tudja használni a privát végpontokat. Ezért javasoljuk, hogy tekintse meg ezt az ellenőrzőlistát, mielőtt továbblép az elemek védelemmel.

K. Használhatom a saját DNS-kiszolgálót az Azure saját DNS-zóna vagy egy integrált magánhálózati DNS-zóna használata helyett?<br>
A. Igen, használhatja a saját DNS-kiszolgálóit. Azonban győződjön meg arról, hogy az összes szükséges DNS-rekord hozzá van adva az ebben a részben javasolt módon.

K. Kell-e további lépéseket végrehajtani a kiszolgálón, miután követtem a jelen cikkben leírt eljárást?<br>
A. A cikkben részletezett folyamat után nem kell további munkát végeznie a privát végpontok használatához a biztonsági mentéshez és a visszaállításhoz.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Backup összes biztonsági szolgáltatásáról](security-overview.md)
