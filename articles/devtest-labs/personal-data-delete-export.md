---
title: Személyes adatok törlése és exportálása Azure DevTest Labs
description: Megtudhatja, hogyan törölheti és exportálhatja a személyes adatait az Azure DevLast Labs szolgáltatásból a Általános adatvédelmi rendelet (GDPR) alatti kötelezettségek támogatásához.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483516"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Személyes adatok exportálása vagy törlése Azure DevTest Labs
Ez a cikk a személyes adatok Azure DevTest Labs szolgáltatásból történő törlésének és exportálásának lépéseit ismerteti. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Milyen személyes adatokat gyűjt a DevTest Labs?
A DevTest Labs két fő személyes adatot gyűjt a felhasználótól. Ezek a következők: felhasználói e-mail-cím és felhasználói objektum azonosítója. Ez az információ kritikus fontosságú ahhoz, hogy a szolgáltatás szolgáltatásokat nyújtson a laboratóriumi rendszergazdák és a labor felhasználói számára.

### <a name="user-email-address"></a>Felhasználói e-mail-cím
A DevTest Labs a felhasználói e-mail-cím használatával küldi el az e-mailek automatikus leállítását a labor felhasználói számára. Az e-mail értesíti arról, hogy a számítógép felhasználóinak leállítása folyamatban van. Ha szeretné, a felhasználók elhalaszthatja vagy kihagyhatják a leállítást. Az e-mail-címet a tesztkörnyezet szintjén vagy a virtuális gép szintjén kell konfigurálni.

**E-mailek beállítása a laborban:**

![Az e-mailek beállítása a labor szintjén](./media/personal-data-delete-export/lab-user-email.png)

**E-mailek beállítása a virtuális gépen:**

![E-mailek beállítása a virtuális gép szintjén](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Felhasználói objektum azonosítója
A DevTest Labs a felhasználói objektum AZONOSÍTÓjának használatával jeleníti meg a havi költségeket és a költségeket az erőforrás-információk alapján a laboratóriumi rendszergazdáknak. Lehetővé teszi számukra a költségek nyomon követését és a tesztkörnyezet küszöbértékének kezelését. 

**Az aktuális naptári hónap becsült költségek trendje:** 
 ![ Az aktuális naptári hónap becsült költségek trendje](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Becsült havi költségek erőforrás szerint:** 
 ![ Becsült havi költségek erőforrás szerint](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Miért van szükségünk erre a személyes adatszolgáltatásra?
A DevTest Labs szolgáltatás a személyes adatkezelési célokat használja. Ezek az adatelemek kritikus fontosságúak a szolgáltatás kulcsfontosságú funkcióinak kézbesítéséhez. Ha adatmegőrzési szabályzatot állít be a felhasználói e-mail-címen, a labor felhasználói nem kapnak időben automatikusan leállítási értesítő e-maileket, miután az e-mail-címük törölve lett a rendszerből. Hasonlóképpen, a labor rendszergazdája nem tudja megtekinteni a havi hónapra kiterjedő költségeket, a laborban lévő gépek esetében pedig a költségeket, ha a felhasználói objektumok azonosítóit adatmegőrzési házirend alapján törlik. Ezért ezeket az adatforrásokat mindaddig meg kell őrizni, amíg a felhasználó erőforrása aktív a laborban.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Honnan tudom, hogy a rendszeren felejtsem a személyes adataim?
Ha a személyes adatmennyiséget törölni szeretné a laborban, a megfelelő erőforrás törlésével megteheti a laborban. A DevTest Labs szolgáltatás 30 nappal névteleníti a törölt személyes adatfájlokat, miután a felhasználó törölte azt.

Ha például törli a virtuális gépet, vagy eltávolította az e-mail-címét, a DevTest Labs szolgáltatás az erőforrás törlése után 30 nappal névteleníti ezt az adatforrást. A 30 napos adatmegőrzési szabályzat törlés utáni megadásával gondoskodhat arról, hogy a tesztkörnyezet rendszergazdájának pontos havi havi költségszámítást biztosítson.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hogyan Kérhetek exportálást a személyes adataim között?
A személyes és laboratóriumi használati adatokat a Azure Portal vagy a PowerShell használatával is exportálhatja. Az adatexportálás két különböző CSV-fájlként történik:

- **disks.csv** – a különböző virtuális gépek által használt lemezekkel kapcsolatos információkat tartalmazza
- **virtualmachines.csv** – a laborban található virtuális gépekkel kapcsolatos információkat tartalmaz.

### <a name="azure-portal"></a>Azure Portal
Labor-felhasználóként az DevTest Labs szolgáltatás által tárolt személyes értékekre is kérhet exportálást. Az Exportálás kérelmezéséhez navigáljon a **személyes adatai** lehetőségre a labor **Áttekintés** lapján. Válassza az **Exportálás kérése** gombot egy letölthető Excel-fájl létrehozásával a labor rendszergazdája Storage-fiókjában. Ezután kapcsolatba léphet a labor rendszergazdájával, és megtekintheti ezeket az adatfájlokat.

1. A bal oldali menüben válassza a **személyes adatértékek** lehetőséget. 

    ![Személyes adatoldal](./media/personal-data-delete-export/personal-data-page.png)
2. Válassza ki a labort tartalmazó **erőforráscsoportot** .

    ![Erőforráscsoport kiválasztása](./media/personal-data-delete-export/select-resource-group.png)
3. Válassza ki a **Storage-fiókot** az erőforráscsoporthoz.
4. A **Storage-fiók** lapon válassza a **Blobok** lehetőséget.

    ![Blobok csempe kiválasztása](./media/personal-data-delete-export/select-blobs-tile.png)
5. Válassza ki a **labresourceusage** nevű tárolót a tárolók listájában.

    ![BLOB-tároló kiválasztása](./media/personal-data-delete-export/select-blob-container.png)
6. Válassza ki a laborból elnevezett **mappát** . Ebben a mappában megtalálja a laborban található **lemezek** és **virtuális gépek** **CSV** -fájljait. Letöltheti ezeket a CSV-fájlokat, szűrheti a hozzáférést kérő labor felhasználó tartalmát, és megoszthatja velük.

    ![CSV-fájl letöltése](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

A fenti példában a legfontosabb összetevők a következők:

- A Invoke-AzureRmResourceAction parancs.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Két művelet paraméterei
    - **blobStorageAbsoluteSasUri** – a Storage-fiók URI-ja a közös hozzáférési aláírás (SAS) jogkivonatával. A PowerShell-parancsfájlban ezt az értéket a Storage-kulcs helyett lehet átadni.
    - **usageStartDate** – az adatok lekérésének kezdő dátuma, a befejezési dátum pedig a művelet végrehajtásának aktuális dátuma. A részletesség a nap szintjén történik, így akkor is, ha időt vesz fel, a rendszer figyelmen kívül hagyja.

### <a name="exported-data---a-closer-look"></a>Exportált adatértékek – alaposabb keresés
Most nézzük meg közelebbről az exportált információkat. Ahogy korábban említettük, az adatexportálás sikeres exportálása után két CSV-fájl lesz. 

A **virtualmachines.csv** a következő adatoszlopokat tartalmazza:

| Oszlop neve | Leírás |
| ----------- | ----------- | 
| SubscriptionId | A laborban található előfizetés-azonosító. |
| LabUId | A labor egyedi GUID azonosítója. |
| LabName | A labor neve. |
| LabResourceId | Teljes körű laboratóriumi erőforrás-azonosító. |
| ResourceGroupName | A virtuális gépet tartalmazó erőforráscsoport neve | 
| ResourceId | A virtuális gép teljes erőforrás-azonosítója. |
| ResourceUId | A virtuális gép GUID azonosítója |
| Name | A virtuális gép neve. |
| CreatedTime | A virtuális gép létrehozásának dátuma és időpontja. |
| DeletedDate | A virtuális gép törlésének dátuma és időpontja. Ha üres, a törlés még nem történt meg. |
| ResourceOwner | A virtuális gép tulajdonosa. Ha az érték üres, akkor vagy egy lekérhető virtuális gép, vagy egy egyszerű szolgáltatásnév hozza létre. |
| PricingTier | A virtuális gép díjszabási szintje |
| ResourceStatus | A virtuális gép rendelkezésre állási állapota. Aktív, ha még létezik vagy inaktív, ha a virtuális gépet törölték. |
| ComputeResourceId | A virtuális gép számítási erőforrás-azonosítója teljesen minősített. |
| Igényelhető | Értéke TRUE (igaz), ha a virtuális gép igényelhető virtuális gép | 
| EnvironmentId | Az a környezeti erőforrás-azonosító, amelyen belül a virtuális gép létrejött. Üres, ha a virtuális gép nem a környezeti erőforrás részeként lett létrehozva. |
| ExpirationDate | A virtuális gép lejárati dátuma. Ha nincs beállítva lejárati dátum, az üres értékre van állítva.
| GalleryImageReferenceVersion |  A virtuális gép alaprendszerképének verziója. |
| GalleryImageReferenceOffer | A virtuális gép alaprendszerképének ajánlata. |
| GalleryImageReferencePublisher | A virtuális gép alaprendszerképének közzétevője. |
| GalleryImageReferenceSku | A virtuális gép alaprendszerképének SKU-a |
| GalleryImageReferenceOsType | A virtuális gép alaprendszerképének operációsrendszer-típusa |
| CustomImageId | A virtuális gép alapszintű egyéni rendszerképének teljesen minősített azonosítója. |

A **disks.csvban** található adatoszlopok alább láthatók:

| Oszlop neve | Leírás | 
| ----------- | ----------- | 
| SubscriptionId | A labort tartalmazó előfizetés azonosítója |
| LabUId | A tesztkörnyezet GUID azonosítója |
| LabName | A labor neve | 
| LabResourceId | A tesztkörnyezet teljes erőforrás-azonosítója | 
| ResourceGroupName | A labort tartalmazó erőforráscsoport neve | 
| ResourceId | A virtuális gép teljes erőforrás-azonosítója. |
| ResourceUId | A virtuális gép GUID azonosítója |
 |Name | A csatolt lemez neve |
| CreatedTime |Az adatlemez létrehozásának dátuma és időpontja. |
| DeletedDate | Az adatlemez törlésének dátuma és időpontja. |
| ResourceStatus | Az erőforrás állapota. Aktív, ha az erőforrás létezik. Inaktív, törléskor. |
| DiskBlobName | Az adatlemez blobjának neve. |
| DiskSizeGB | Az adatlemez mérete |
| DiskType | Az adatlemez típusa. 0 standard, prémium szintű 1. |
| LeasedByVmId | Annak a virtuális gépnek az erőforrás-azonosítója, amelyhez az adatlemez hozzá lett csatolva. |


> [!NOTE]
> Ha több labort is kezel, és általános információkat szeretne kapni, a két fő oszlop a **LabUID** és a **ResourceUId**, amelyek az egyedi azonosítók az előfizetések között.

Az exportált adatai az eszközök, például SQL Server, Power BI stb. használatával kezelhetők és megjeleníthetők. Ez a funkció különösen akkor hasznos, ha a tesztkörnyezet használatát szeretné jelenteni a felügyeleti csapatának, amely esetleg nem ugyanazt az Azure-előfizetést használja.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 

- [Tesztkörnyezet szabályzatának beállítása](devtest-lab-set-lab-policy.md)
- [Gyakori kérdések](devtest-lab-faq.md)
