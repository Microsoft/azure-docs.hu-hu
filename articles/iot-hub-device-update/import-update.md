---
title: Új frissítési | Microsoft Docs
description: How-To útmutató az új frissítések importáláshoz a IoT Hub eszközfrissítésbe a IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 196a449f25d97fb1c1b7b8d79ee8889e0d31a5ae
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717746"
---
# <a name="import-new-update"></a>Új frissítés importálása
Ismerje meg, hogyan importálhat új frissítéseket a IoT Hub. Ha még nem tette meg, ismerkedjen meg az importálás alapvető [fogalmaival.](import-concepts.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy meglévő frissítési fájl, amely az eszközökre telepítve van. Ez lehet képalapú frissítéshez szükséges képfájl, csomagalapú [frissítéshez](device-update-apt-manifest.md) pedig APT-jegyzékfájl. ([Hogyan válassza?](understand-device-update.md#support-for-a-wide-range-of-update-artifacts))
* [Hozzáférés IoT Hub eszközfrissítéssel rendelkező](create-device-update-account.md)IoT Hub számára. 
* Eszközfrissítéshez kiépítve egy IoT-eszköz (vagy szimulátor) a IoT Hub.
* [PowerShell 5 vagy](/powershell/scripting/install/installing-powershell) újabb (linuxos, macOS- és Windows-telepítéseket is tartalmaz)
* Támogatott böngészők:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Előfordulhat, hogy a szolgáltatásba beküldött adatok egy része azon a régión kívüli régióban lesz feldolgozva, ahol a példányt létrehozták.

## <a name="create-device-update-import-manifest"></a>Eszközfrissítés importálási jegyzékének létrehozása

1. Ha még nem tette meg, szerezze be az eszközökre telepíteni kívánt képfájlt vagy APT-jegyzékfájlt. Ez lehet az eszközök gyártójától vagy egy ön által használt rendszerintegrátortól, vagy akár a szervezet egy csoportjától is. Győződjön meg arról, hogy a frissítési képfájl vagy az APT-jegyzékfájl a PowerShellből elérhető könyvtárban található.

2. Hozzon létre egy **AduUpdate.psm1** nevű szövegfájlt abban a könyvtárban, ahol a frissítési képfájl vagy az APT-jegyzékfájl található. Ezután nyissa meg [az AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell-parancsmagot, másolja a tartalmát a szövegfájlba, majd mentse a szövegfájlt.

3. A PowerShellben keresse meg azt a könyvtárat, amelyben létrehozta a PowerShell-parancsmagot a 2. lépésben. Használja az alábbi Másolás lehetőséget, majd illessze be a PowerShellbe a parancsok futtatásához:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Futtassa a következő parancsokat úgy, hogy lecseréli a mintaparaméter-értékeket egy importálási jegyzék létrehozásához, amely a frissítést leíró JSON-fájl:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Rövid összefoglalásként íme néhány példaérték a fenti paraméterekhez. További részletekért megtekintheti a teljes [importálási](import-schema.md) jegyzéksémát is.

    | Paraméter | Leírás |
    | --------- | ----------- |
    | deviceManufacturer | Annak az eszköznek a gyártója, amelynél a frissítés kompatibilis például a Contosóval. Meg kell _egyeznie a gyártó_ [eszköztulajdonságának.](./device-update-plug-and-play.md#device-properties)
    | deviceModel (eszközmodell) | Annak az eszköznek a modellje, amely a frissítéssel kompatibilis, például a Toasterrel. Meg kell _egyeznie a modell_ [eszköztulajdonságának.](./device-update-plug-and-play.md#device-properties)
    | updateProvider | A frissítést létrehozására vagy közvetlenül felelős entitásra. Ez gyakran a vállalat neve lesz.
    | updateName (frissítés neve) | A frissítések egy osztályának azonosítója. A osztály bármi lehet, amit csak választ. Ez gyakran egy eszköz- vagy modellnév.
    | updateVersion | Verziószám, amely megkülönbözteti a frissítést az azonos Provider és Name verziójúaktól. Nem egyezik az eszköz egyes szoftverösszetevőinek verziójával (de igen, ha ön ezt választja).
    | updateType (frissítés típusa) | <ul><li>`microsoft/swupdate:1`Képfrissítéshez adja meg a következőt:</li><li>Csomagfrissítéshez adja meg a `microsoft/apt:1` következőt:</li></ul>
    | installedCriteria | <ul><li>A SWVersion értékének megadása a `microsoft/swupdate:1` frissítési típushoz</li><li>Adja **meg a name-version (névverzió)** értéket, ahol _a name_ (név) az APT-jegyzékfájl neve, a _verzió_ pedig az APT-jegyzékfájl verziója. Például: contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | A számítógépen található frissítési fájl(ak) elérési útja


## <a name="review-generated-import-manifest"></a>A létrehozott importálási jegyzék áttekintése

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

> [!NOTE]
> Az alábbi utasítások azt mutatják be, hogyan importálhat frissítéseket a Azure Portal felhasználói felületén. A frissítés importálását az [eszközfrissítési API IoT Hub is](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) használhatja. 

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és lépjen a IoT Hub eszközfrissítéssel.

2. Az oldal bal oldalán válassza az "Automatikus frissítés" alatt az "Eszközfrissítések" Eszközkezelés.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Frissítések importálása" lightbox="media/import-update/import-updates-3.png":::

3. A képernyő felső részén több lap jelenik meg. Válassza a Frissítések lapot.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Frissítések" lightbox="media/import-update/updates-tab.png":::

4. Válassza a "+ Új frissítés importálása" lehetőséget a "Ready to Deploy" (Üzembe helyezésre kész) fejléc alatt.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Új frissítés importálása" lightbox="media/import-update/import-new-update-2.png":::

5. Válassza ki a mappa ikonját vagy szövegmezőt a "Select an Import Manifest File" (Jegyzékfájl importálása) lehetőség alatt. Megjelenik egy fájlválasztó párbeszédpanel. Válassza ki a korábban a PowerShell-parancsmag használatával létrehozott importálási jegyzékfájlt. Ezután válassza a mappa ikont vagy szövegmezőt a "Válasszon ki egy vagy több frissítési fájlt" alatt. Megjelenik egy fájlválasztó párbeszédpanel. Válassza ki a frissítési fájl(ak)t.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Válassza a Fájlok frissítése lehetőséget" lightbox="media/import-update/select-update-files.png":::

6. Válassza ki a mappa ikonját vagy szövegmezőt a "Tároló kiválasztása" alatt. Ezután válassza ki a megfelelő tárfiókot. A storage-tároló a frissítési fájlok ideiglenes szakaszára használható.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Tárfiók" lightbox="media/import-update/storage-account.png":::

7. Ha már létrehozott egy tárolót, újra felhasználhatja. (Ellenkező esetben válassza a "+ Tároló" lehetőséget egy új tároló frissítéshez való létrehozásához.)  Válassza ki a használni kívánt tárolót, és kattintson a "Select" (Kijelölés) gombra.

   :::image type="content" source="media/import-update/container.png" alt-text="Tároló kiválasztása" lightbox="media/import-update/container.png":::

8. Az importálási folyamat elkezdődjön a "Küldés" gombra.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Frissítés közzététele" lightbox="media/import-update/publish-update.png":::

9. Megkezdődik az importálási folyamat, és a képernyő átvált az "Előzmények importálása" szakaszra. Válassza a "Frissítés" lehetőséget az importálási folyamat befejezéséig (a frissítés méretétől függően ez néhány percen belül befejeződhet, de tovább is tarthat).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Importálási alkalmazás-alkalmazás frissítése" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Ha az Állapot oszlop azt jelzi, hogy az importálás sikeres volt, válassza a "Ready to Deploy" (Üzembe helyezésre kész) fejlécet. Most már látnia kell az importált frissítést a listában.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Feladat állapota" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Következő lépések

[Csoportok létrehozása](create-update-group.md)

[Tudnivalók az importálási fogalmakról](import-concepts.md)