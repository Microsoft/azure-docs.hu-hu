---
title: SAS URI létrehozása virtuálisgép-rendszerképhez – Azure Marketplace
description: Közös hozzáférésű aláírás (SAS) URI létrehozása virtuális merevlemezekhez (VHD) az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 21ccafe3e15f902e35657a9aa31516bbaeb3b4c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558006"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>SAS URI létrehozása virtuálisgép-rendszerképhez

> [!NOTE]
> A virtuális gép közzétételéhez nincs szükség SAS URI-ra. Egyszerűen megoszthat egy rendszerképet a parter Centerben. Lásd: [virtuális gép létrehozása jóváhagyott alap használatával](./azure-vm-create-using-approved-base.md) vagy [virtuális gép létrehozása saját rendszerkép](./azure-vm-create-using-own-image.md) -utasítások használatával.

Az SAS URI-k létrehozása a VHD-k számára a következő követelményekkel rendelkezik:

- Csak a nem felügyelt virtuális merevlemezeket támogatják.
- Csak a lista és az olvasási engedélyek szükségesek. Ne adjon meg írási vagy törlési hozzáférést.
- A hozzáférés időtartamának (lejárati dátum) legalább három héttel a SAS URI létrehozása után kell lennie.
- Az UTC-időváltozások elleni védelem érdekében állítsa be a kezdési dátumot az aktuális dátum előtt egy nappal. Ha például az aktuális dátum június 16., 2020, válassza a 6/15/2020 elemet.

## <a name="extract-vhd-from-a-vm"></a>Virtuális merevlemez kinyerése virtuális gépről

> [!NOTE]
> Ezt a lépést kihagyhatja, ha már rendelkezik egy Storage-fiókban feltöltött VHD-vel.

A virtuális merevlemeznek a virtuális gépről való kinyeréséhez pillanatképet kell készítenie a VM-lemezről, és ki kell bontania a virtuális merevlemezt a pillanatképből.

Kezdés: pillanatkép készítése a virtuálisgép-lemezről:

1. Jelentkezzen be az Azure Portalra.
2. A bal felső sarokban válassza az erőforrás létrehozása elemet, majd keresse meg és válassza a pillanatkép lehetőséget.
3. A pillanatkép panelen válassza a létrehozás lehetőséget.
4. Adja meg a pillanatkép nevét.
5. Válasszon ki egy meglévő erőforráscsoportot, vagy adja meg egy új csoport nevét.
6. A forrásoldali lemez lapon válassza ki a felügyelt lemezt a pillanatképhez.
7. Válassza ki a pillanatkép tárolására használni kívánt fiókot. A standard HDD csak akkor használja, ha nagy teljesítményű SSD-meghajtón tárolja.
8. Válassza a Létrehozás lehetőséget.

### <a name="extract-the-vhd"></a>A VHD kibontása

A következő parancsfájl használatával exportálja a pillanatképet egy virtuális merevlemezre a Storage-fiókjában.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. Currently, only general purpose v1 storage is supported.
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

$sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő parancsokat használja egy pillanatkép SAS URI-kódjának létrehozásához, és a mögöttes VHD-t egy Storage-fiókba másolja a SAS URI használatával. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.


|Parancs  |Jegyzetek  |
|---------|---------|
| az disk grant-access    |     Létrehoz egy írásvédett SAS-t, amelynek használatával a mögöttes VHD-fájl átmásolható egy tárfiókba, vagy letölthető a helyszíni rendszerre    |
|  az storage blob copy start   |    Aszinkron módon másol egy blobot az egyik Storage-fiókból a másikba. Az az Storage blob show paranccsal tekintheti meg az új blob állapotát.     |
|

## <a name="generate-the-sas-address"></a>SAS-címek előállítása

A SAS-cím (URL) létrehozásához két gyakori eszköz használható:

1. **Azure Storage Explorer** – elérhető a Azure Portal.
2. **Azure CLI** – ajánlott nem Windows operációs rendszerekhez, valamint automatizált vagy folyamatos integrációs környezetekhez.

### <a name="using-tool-1-azure-storage-explorer"></a>Az 1. eszköz használata: Azure Storage Explorer

1. Nyissa meg a **Storage-fiókját**.
1. Nyissa meg **Storage Explorer**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Storage-fiók ablaka.":::

3. A **tárolóban** kattintson a jobb gombbal a VHD-fájlra, és válassza a **megosztás hozzáférési aláírásának beolvasása** elemet.
4. A **megosztott hozzáférés aláírása** párbeszédpanelen hajtsa végre a következő mezőket:

    1. Kezdési idő – a VHD-hozzáférés engedélyének kezdő dátuma. Adja meg azt a dátumot, amely egy nappal az aktuális dátum előtt van.
    2. Lejárati idő – a VHD-hozzáférés engedélyének lejárati dátuma. Adjon meg legalább három héttel az aktuális dátumon túli dátumot.
    3. Engedélyek – válassza az olvasási és listázási engedélyeket.
    4. Tároló szintje – jelölje be a tároló szintű közös hozzáférésű aláírás URI-azonosítójának előállítása jelölőnégyzetet.

    ![Megosztott hozzáférés aláírása párbeszédpanel.](media/vm/create-sas-uri-storage-explorer.png)

5. Ha a társított SAS URI-t szeretné létrehozni ehhez a VHD-hez, válassza a **Létrehozás** lehetőséget.
6. Másolja ki az URI-t, és mentse a fájlt egy biztonságos helyen lévő szövegfájlba. Ez a generált SAS URI a tároló szintű hozzáféréshez szükséges. A virtuális merevlemez nevének hozzáadásához szerkessze a szövegfájlt.
7. Szúrja be a VHD-nevet a SAS URI-ban lévő VHD-karakterlánc után (adja meg a perjelet). A végső SAS URI-nak így kell kinéznie:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Ismételje meg ezeket a lépéseket a közzétenni kívánt csomagok minden virtuális merevlemezén.

### <a name="using-tool-2-azure-cli"></a>A 2. eszköz használata: Azure CLI

1. Töltse le és telepítse a [Microsoft Azure CL](/cli/azure/install-azure-cli)I-t. A verziók a Windows, a macOS és a Linux különböző disztribúciói számára érhetők el.
2. Hozzon létre egy PowerShell-fájlt (. ps1 kiterjesztésű fájlt), másolja a következő kódot, majd mentse helyileg.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Szerkessze a fájlt a következő paraméterérték használatára. Adja meg a dátumokat UTC dátum és idő formátumban, például: 2020-04-01T00:00:00Z.

    - fiók neve – az Azure Storage-fiók neve.
    - Account-Key – az Azure Storage-fiók kulcsa.
    - Start-Date – engedély kezdő dátuma a VHD-hozzáféréshez. Adja meg a dátumot egy nappal az aktuális dátum előtt.
    - lejárati dátum – a VHD-hozzáférés engedélyének lejárati dátuma. Legalább három héttel az aktuális dátum után adjon meg egy dátumot.

    Íme egy példa a paraméterek megfelelő értékeire (az írás időpontjában):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Mentse a módosításokat.
2. A következő módszerek egyikének használatával futtassa ezt a parancsfájlt rendszergazdai jogosultságokkal SAS-kapcsolati karakterlánc létrehozásához a tároló szintű hozzáféréshez:

    - Futtassa a szkriptet a-konzolról. A Windowsban kattintson a jobb gombbal a parancsfájlra, és válassza a **Futtatás rendszergazdaként** lehetőséget.
    - Futtassa a szkriptet egy PowerShell-parancsfájl szerkesztőjéből, például [Windows PowerShell integrált parancsprogram-kezelési környezet](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Ezen a képernyőn látható az SAS-kapcsolatok karakterláncának létrehozása a szerkesztőben:

    [![SAS-kapcsolatok karakterláncának létrehozása a PowerShell-szerkesztőben](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Másolja az SAS-kapcsolódási karakterláncot, és mentse a fájlt egy biztonságos helyen lévő szövegfájlba. Szerkessze ezt a karakterláncot a VHD-hely adatainak hozzáadásához a végső SAS URI létrehozásához.
7. A Azure Portal nyissa meg a blob Storage-t, amely tartalmazza az új URI-hoz társított virtuális merevlemezt.
8. Másolja a blob Service-végpont URL-címét:

    ![A blob Service-végpont URL-címének másolása.](media/vm/create-sas-uri-blob-endpoint.png)

9. Szerkessze a szövegfájlt a 6. lépésből származó SAS-kapcsolatok karakterláncával. Hozza létre a teljes SAS URI-t a következő formátumban:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Az SAS URI ellenőrzése

Győződjön meg arról, hogy az SAS URI-ja közzé van adva a partner Centerben, hogy elkerülje a kéréshez tartozó SAS URI-küldéssel kapcsolatos problémákat. Ez a folyamat nem kötelező, de ajánlott.

- Az URI tartalmazza a VHD-rendszerkép fájlnevét, beleértve a fájlnév kiterjesztését is `.vhd` .
- `Sp=rl` az URI közepe közelében jelenik meg. Ez a karakterlánc megjeleníti az olvasási és listázási hozzáférést.
- Ha `sr=c` megjelenik, az azt jelenti, hogy a tároló szintű hozzáférés meg van adva.
- Másolja és illessze be az URI-t egy böngészőbe a blob letöltéséhez (a letöltés befejezése előtt megszakíthatja a műveletet).

## <a name="next-steps"></a>Következő lépések

- Ha problémákba ütközik, lásd: [VM sas-hibaüzenetek](azure-vm-sas-failure-messages.md).
- [Bejelentkezés a partner központba](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Virtuálisgép-ajánlat létrehozása az Azure Marketplace-en](azure-vm-create.md)