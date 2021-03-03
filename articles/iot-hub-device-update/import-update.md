---
title: Új frissítés importálása | Microsoft Docs
description: How-To útmutató egy új frissítés importálásához IoT Hub eszköz frissítése IoT Hub.
author: andbrown
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d8757f3076f784576f95bbdfc30abf578446c776
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663288"
---
# <a name="import-new-update"></a>Új frissítés importálása
Ismerje meg, hogyan importálhat új frissítést a IoT Hub eszköz frissítésében.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzáférés egy IoT hubhoz az eszköz frissítésével IoT hub engedélyezve](create-device-update-account.md). Javasoljuk, hogy a IoT Hub S1 (standard) vagy újabb verzióját használja. 
* Az eszköz frissítéséhez kiépített IoT-eszköz (vagy szimulátor) IoT Hubon belül.
   * Ha valódi eszközt használ, szüksége lesz a rendszerkép frissítése vagy az [apt manifest-fájl](device-update-apt-manifest.md) frissítése a csomagok frissítéséhez.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) vagy újabb.
* Támogatott böngészők:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Előfordulhat, hogy a szolgáltatásnak küldött egyes adatkészleteket a rendszer azon régión kívüli régióban dolgozza fel, amelyben a példány létrejött.

## <a name="create-device-update-import-manifest"></a>Eszköz frissítésének importálási jegyzékfájljának létrehozása

1. Győződjön meg arról, hogy a frissítési lemezképfájl vagy az APT manifest fájl a PowerShellből elérhető címtárban található.

2. Az [eszköz klónozása IoT hub adattárhoz](https://github.com/azure/iot-hub-device-update), vagy töltse le. zip-fájlként a powershellből elérhető helyre (a zip-fájl letöltése után kattintson a jobb gombbal a `Properties`  >  `General` Tab > `Unblock` a `Security` PowerShell biztonsági figyelmeztetési kérésének elkerüléséhez) szakaszra.

3. A PowerShellben navigáljon a `tools/AduCmdlets` könyvtárhoz, és futtassa a következő parancsot:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Futtassa az alábbi parancsokat úgy, hogy lecseréli a minta paraméter értékeit egy importálási jegyzékfájl létrehozásához, amely a frissítést leíró JSON-fájlt tartalmaz:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    A rövid útmutatóhoz Íme néhány példa a fenti paraméterek értékeire. A teljes dokumentációt az alábbi, a teljes importálási jegyzékfájlt ismertető szakaszban találja.

    | Paraméter | Leírás |
    | --------- | ----------- |
    | deviceManufacturer | Az eszköz gyártója, amelyen a frissítés kompatibilis, például: contoso
    | deviceModel | Az eszköz modellje, amely kompatibilis a frissítéssel, például kenyérpirító
    | updateProvider | A frissítési identitás szolgáltatói része, például: fabrikam
    | updateName | A frissítési identitás részének neve, például ImageUpdate
    | updateVersion | Frissítés verziója, például 2,0
    | Frissítés típusa | <ul><li>`microsoft/swupdate:1`Rendszerkép frissítésének megadása</li><li>`microsoft/apt:1`Csomag frissítésének megadása</li></ul>
    | installedCriteria | <ul><li>SWVersion értékének megadása a `microsoft/swupdate:1` frissítési típushoz</li><li>Adja meg a `microsoft/apt:1` frissítési típus javasolt értékét.
    | updateFilePath (ok) | A frissítési fájl (ok) elérési útja a számítógépen

    Teljes importálási jegyzékfájl sémája

    | Név | Típus | Leírás | Korlátozások |
    | --------- | --------- | --------- | --------- |
    | UpdateId | `UpdateId` objektum | Identitás frissítése. |
    | Frissítés típusa | sztring | Frissítés típusa: <ul><li>Itt adhatja meg, `microsoft/apt:1` Mikor hajtson végre egy Package-alapú frissítést a Reference Agent használatával.</li><li>Itt adhatja meg, `microsoft/swupdate:1` hogy mikor hajtson végre rendszerkép-alapú frissítést a Reference Agent használatával.</li><li>Itt adhatja meg `microsoft/simulator:1` a minta ügynök szimulátor használatát.</li><li>Egyedi típust adjon meg, ha egyéni ügynököt fejleszt.</li></ul> | <ul><li>Formátumban `{provider}/{type}:{typeVersion}`</li><li>Maximális 32 karakter összesen</li></ul> |
    | InstalledCriteria | sztring | Az ügynök által értelmezett karakterlánc annak megállapítására, hogy a frissítés sikeresen alkalmazva lett-e:  <ul><li>Adja  meg a frissítési típus SWVersion értékét `microsoft/swupdate:1` .</li><li>Adja meg a `{name}-{version}` frissítési típust `microsoft/apt:1` , amelynek a nevét és verzióját az apt-fájlból kell beolvasni.</li><li>Adja meg a frissítési fájl kivonatát a frissítési típushoz `microsoft/simulator:1` .</li><li>Egyéni karakterláncot egyedi ügynök létrehozásakor kell megadni.</li></ul> | Legfeljebb 64 karakter |
    | Kompatibilitás | Objektumok tömbje `CompatibilityInfo` | A frissítéssel kompatibilis eszköz kompatibilitási információi. | Legfeljebb 10 elem |
    | CreatedDateTime | dátum és idő | A frissítés létrehozásának dátuma és időpontja. | Tagolt ISO 8601 dátum és idő formátuma (UTC) |
    | ManifestVersion | sztring | A jegyzékfájl-séma verziójának importálása. Megadható `2.0` , amely kompatibilis lesz a `urn:azureiot:AzureDeviceUpdateCore:1` csatolóval és az `urn:azureiot:AzureDeviceUpdateCore:4` adapterrel.</li></ul> | Kötelező `2.0` |
    | Fájlok | Objektumok tömbje `File` | Hasznos adatok fájljainak frissítése | Legfeljebb 5 fájl |

Megjegyzés: minden mező kitöltése kötelező.

## <a name="review-generated-import-manifest"></a>Létrehozott importálási jegyzékfájl áttekintése

Példa:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Frissítés importálása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub az eszköz frissítésével.

2. A lap bal oldalán válassza az "eszközök frissítése" lehetőséget az "automatikus eszközkezelés" területen.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Frissítések importálása" lightbox="media/import-update/import-updates-3.png":::

3. A képernyő felső részén több lap jelenik meg. Válassza a frissítések lapot.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Frissítések" lightbox="media/import-update/updates-tab.png":::

4. Válassza az "új frissítés importálása" lehetőséget a "készen áll a telepítésre" fejléc alatt.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Új frissítés importálása" lightbox="media/import-update/import-new-update-2.png":::

5. Válassza ki a mappa ikont vagy szövegmezőt a "Select a manifest file List" (fájl importálása) elem alatt. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a korábban létrehozott importálási jegyzékfájlt a PowerShell-parancsmag használatával. Ezután válassza a mappa ikont vagy a szövegmezőt a "válasszon ki egy vagy több frissítési fájlt" területen. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a frissítési fájl (oka) t.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Frissítési fájlok kiválasztása" lightbox="media/import-update/select-update-files.png":::

6. Válassza a mappa ikont vagy a szövegmezőt a "Storage-tároló kiválasztása" területen. Ezután válassza ki a megfelelő Storage-fiókot. A tárolási tároló a frissítési fájlok ideiglenes előkészítésére szolgál.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Tárfiók" lightbox="media/import-update/storage-account.png":::

7. Ha már létrehozott egy tárolót, újra használhatja azt. (Ellenkező esetben válassza a "+ tároló" lehetőséget a frissítések új tárolójának létrehozásához.).  Válassza ki a használni kívánt tárolót, és kattintson a Kiválasztás gombra.

   :::image type="content" source="media/import-update/container.png" alt-text="Tároló kiválasztása" lightbox="media/import-update/container.png":::

8. Az importálási folyamat elindításához válassza a Küldés lehetőséget.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Frissítés közzététele" lightbox="media/import-update/publish-update.png":::

9. Az importálási folyamat megkezdődik, és a képernyő az "importálási előzmények" szakaszra vált. Válassza a "frissítés" lehetőséget, ha az importálási folyamat befejeződéséig szeretné megtekinteni az előrehaladást (a frissítés méretétől függően ez néhány percen belül elvégezhető, de hosszabb időt is igénybe vehet).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Importálási sorrend frissítése" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Ha az Állapot oszlop jelzi, hogy az importálás sikeres volt, válassza a "készen áll a telepítésre" fejlécet. Ekkor megjelenik az importált frissítés a listában.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Feladatok állapota" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Következő lépések

[Csoportok létrehozása](create-update-group.md)

[Tudnivalók az importálási fogalmakról](import-concepts.md)
