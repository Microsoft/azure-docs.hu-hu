---
title: Modell átalakítása
description: Egy egyéni modell átalakítási lépéseit bemutató gyors útmutató.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: c9b5d525954e7f0742cd13fe4d64a73df64ea854
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594467"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Gyorsútmutató: Modell konvertálása a renderelés előtt

A rövid útmutatóban [: modell megjelenítése egységgel](render-model.md), megtanulta, hogyan használható a Unity Sample Project egy beépített modell megjelenítéséhez. Ez az útmutató bemutatja, hogyan alakíthatja át saját modelljeit.

A következőket fogja megtanulni:

> [!div class="checklist"]
>
> * Azure Blob Storage-fiók beállítása a bevitelhez és a kimenethez
> * 3D modell feltöltése és átalakítása az Azure távoli rendereléssel való használatra
> * Konvertált 3D modell belefoglalása egy alkalmazásba megjelenítéshez

## <a name="prerequisites"></a>Előfeltételek

* Teljes rövid útmutató [: modell megjelenítése egységgel](render-model.md)
* Az átalakításhoz a PowerShell-parancsfájl használatával: install Azure PowerShell [(dokumentáció)](/powershell/azure/)
  * Rendszergazdai jogosultságokkal rendelkező PowerShell megnyitása
  * Futtassa `Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Áttekintés

A kiszolgálón a megjelenítő nem tud közvetlenül működni a forrás modell formátumával, például a FBX vagy a GLTF. Ehelyett a modellnek védett bináris formátumúnak kell lennie.
Az átalakítási szolgáltatás felhasználja az Azure Blob Storage modelljeit, és visszaírja a konvertált modelleket egy megadott Azure Blob Storage-tárolóba.

A következők szükségesek:

* Azure-előfizetés
* Az előfizetéshez tartozó "StorageV2" fiók
* BLOB Storage-tároló a bemeneti modellhez
* A kimeneti adatokat tartalmazó blob Storage-tároló
* Egy konvertálandó modell, lásd: [minta modellek](../samples/sample-model.md)
  * Tekintse meg a [támogatott forrás-formátumok](../how-tos/conversion/model-conversion.md#supported-source-formats) listáját
  * A minta konverziós parancsfájl használatához győződjön meg arról, hogy előkészít egy bemeneti mappát, amely tartalmazza a modellt és az összes külső függőséget (például külső textúrákat vagy geometriát).

## <a name="azure-setup"></a>Azure-telepítés

Ha még nincs fiókja, nyissa meg a parancsot [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/) , kattintson az ingyenes fiók lehetőségre, és kövesse az utasításokat.

Ha már rendelkezik Azure-fiókkal, nyissa meg a következőt: [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home) .

### <a name="storage-account-creation"></a>Storage-fiók létrehozása

BLOB Storage létrehozásához először egy Storage-fiókra van szükség.
Egy létrehozásához kattintson az "erőforrás létrehozása" gombra:

![Azure – erőforrás hozzáadása](media/azure-add-a-resource.png)

Az új képernyőn válassza a **tároló** lehetőséget a bal oldalon, majd a **Storage Account-blob, fájl, tábla, üzenetsor** elemet a következő oszlopból:

![Azure – tárterület hozzáadása](media/azure-add-storage.png)

Ha erre a gombra kattint, a következő képernyő jelenik meg a tárolási tulajdonságok kitöltésével:

![Azure-telepítés](media/azure-setup1.png)

Töltse ki az űrlapot a következő módon:

* Hozzon létre egy új erőforráscsoportot a lenti hivatkozás alatt a legördülő listából, és nevezze el ezt a **ARR_Tutorial**
* A **Storage-fiók neve** mezőben adjon meg egy egyedi nevet. **Ennek a névnek globálisan egyedinek kell lennie**, ellenkező esetben egy üzenet jelenik meg, amely tájékoztatja arról, hogy a név már használatban van. Ennek a rövid útmutatónak a hatókörében a **arrtutorialstorage** nevet adja. Ennek megfelelően le kell cserélnie a nevét a rövid útmutató bármely előfordulása esetén.
* Válasszon ki egy **helyet** a közelben. Ideális esetben ugyanazt a helyet használja, mint a renderelés beállításához a másik rövid útmutatóban.
* A **teljesítmény** a "standard" értékre van beállítva
* A **Fiók típusa** a következőre van beállítva: "StorageV2 (általános célú v2)"
* **Replikálás** a "READ-Access geo-redundáns tárolás (ra-GRS)" értékre
* A **hozzáférési szint** a "forró" értékre van állítva

A többi lapon lévő tulajdonságok egyikét sem kell módosítania, így folytathatja a **"felülvizsgálat + létrehozás"** parancsot, majd a telepítés befejezéséhez kövesse a lépéseket.

A webhely most tájékoztatja az üzemelő példány előrehaladásáról, és az "üzembe helyezés befejezése" jelentésekről. Kattintson a **"Ugrás az erőforráshoz"** gombra a következő lépésekhez:

![Az Azure Storage létrehozása befejeződött](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>BLOB Storage létrehozása

A következő két blob-tárolóra van szükségünk, egyet a bemenethez, egyet pedig a kimenethez.

A fenti **"Ugrás az erőforráshoz"** gombra kattintva megtekintheti a bal oldali panelt, amely egy lista menüt tartalmaz. A **"blob Service"** kategóriába tartozó listában kattintson a **"containers" (tárolók** ) gombra:

![Azure – tárolók hozzáadása](./media/azure-add-containers.png)

A **bemeneti** blob Storage-tároló létrehozásához nyomja meg a **"+ Container"** gombot.
A létrehozáskor használja az alábbi beállításokat:
  
* Név = arrinput
* Nyilvános hozzáférési szint = Private

Miután létrehozta a tárolót, kattintson ismét a **+ tároló** elemre, majd ismételje meg ezeket a beállításokat a **kimeneti** tárolóhoz:

* Név = arroutput
* Nyilvános hozzáférési szint = Private

Most két blob Storage-tárolóval kell rendelkeznie:

![Blob Storage telepítő](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Az átalakítás futtatása

A modellek átalakítása három különböző módon aktiválható:

### <a name="1-conversion-via-the-arrt-tool"></a>1. konverzió a ARRT eszköz használatával

A [ARRT nevű UI-alapú eszköz](./../samples/azure-remote-rendering-asset-tool.md) a konverziók elindítására és a megjelenített eredményekkel való interakcióra szolgál.
![ARRT](./../samples/media/azure-remote-rendering-asset-tool.png "ARRT képernyőkép")

### <a name="2-conversion-via-a-powershell-script"></a>2. átalakítás PowerShell-parancsfájl használatával

Ahhoz, hogy könnyebb legyen meghívni az Asset Conversion Service-t, biztosítunk egy segédprogram-szkriptet. A *szkriptek* mappában található, és a neve **Conversion.ps1**.

Ez a szkript különösen a következő:

1. egy adott könyvtár összes fájljának feltöltése a helyi lemezről a bemeneti tárolóba
1. meghívja az [eszköz konverziós Rest APIét](../how-tos/conversion/conversion-rest-api.md), amely beolvassa az adatokat a bemeneti tárolóból, és elindít egy konverziót, amely egy konverziós azonosítót ad vissza.
1. a konverziós állapot API lekérdezése a beolvasott konverziós AZONOSÍTÓval, amíg az átalakítási folyamat nem fejeződik be sikeres vagy sikertelen
1. a kimeneti tárolóban található átalakított eszközre mutató hivatkozást kérdez le.

A parancsfájl beolvassa a konfigurációját a (z) *Scripts\arrconfig.js* fájlból. Nyissa meg a JSON-fájlt egy szövegszerkesztőben.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

A **accountSettings** csoporton belüli konfigurációt (fiókazonosító és kulcs) a modell kimutatása az [Unity](render-model.md)rövid útmutatóval, a hitelesítő adatokhoz hasonlóan kell kitölteni.

A **assetConversionSettings** csoportban ügyeljen arra, hogy a fent látható módon módosítsa a **resourceGroup**, a **blobInputContainerName** és a **blobOutputContainerName** .
Vegye figyelembe, hogy a **arrtutorialstorage** értékét a Storage-fiók létrehozása során kiválasztott egyedi névvel kell helyettesíteni.

Módosítsa a **localAssetDirectoryPath** úgy, hogy a lemezen lévő könyvtárba mutasson, amely tartalmazza az átalakítani kívánt modellt. Ügyeljen arra, hogy az elérési úton lévő fordított perjeleket ("" \\ ) a dupla fordított perjel (" \\ \\ ") használatával megfelelően elkerülje.

A **localAssetDirectoryPath** -ben megadott elérési útról származó összes adat fel lesz töltve a **blobInputContainerName** blob-tárolóba a **inputFolderPath** által megadott alútvonalon. Így a "D: tmp robot" könyvtár tartalmához tartozó példa konfigurációban a " \\ \\ arrtutorialstorage" Storage-fiók "arrinput" nevű blob-tárolójában lesz feltöltve a "robotConversion" elérési úton. A már meglévő fájlok felülírva lesznek.

Módosítsa a **inputAssetPath** a konvertálandó modell elérési útjára – az elérési út a localAssetDirectoryPath viszonyítva van. \\Az elérési út elválasztója helyett használja a "/" karaktert. Tehát a "D: tmp robot" mappában található "robot. FBX" fájlhoz \\ használja a \\ "robot. FBX" fájlt.

A modell átalakítása után a rendszer visszaírja a **blobOutputContainerName** által megadott tárolóba. A választható **outputFolderPath** megadásával egy alelérési út is megadható. A fenti példában az eredményül kapott "robot. arrAsset" a "konvertált/robot" alatt található kimeneti blob-tárolóba lesz másolva.

A **outputAssetFileName** konfigurációs beállítás határozza meg a konvertált eszköz nevét – a paraméter nem kötelező, és a kimeneti fájlnév a bemeneti fájlnévtől eltérő lesz.

Nyisson meg egy PowerShellt, győződjön meg arról, hogy telepítette az [előfeltételekben](#prerequisites)említett *Azure PowerShell* . Ezután jelentkezzen be az előfizetésbe az alábbi paranccsal, és kövesse a képernyőn megjelenő utasításokat:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Ha a szervezet egynél több előfizetéssel rendelkezik, lehet, hogy meg kell adnia a SubscriptionId és a bérlői argumentumokat. A részletek a [AzAccount dokumentációjában](/powershell/module/az.accounts/connect-azaccount)találhatók.

Váltson át a `azure-remote-rendering\Scripts` könyvtárra, és futtassa az átalakítási parancsfájlt:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

A következőhöz hasonlónak kell lennie: ![Conversion.ps1](./media/successful-conversion.png)

### <a name="3-conversion-via-api-calls"></a>3. átalakítás API-hívások használatával

A C# és a C++ API is biztosít egy belépési pontot a szolgáltatással való kommunikációhoz:
* [C# RemoteRenderingClient. StartAssetConversionAsync ()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.startassetconversionasync)
* [C++ RemoteRenderingClient:: StartAssetConversionAsync ()](/cpp/api/remote-rendering/remoterenderingclient#startassetconversionasync)


## <a name="insert-new-model-into-quickstart-sample-app"></a>Új modell beszúrása a Gyorsindítás minta alkalmazásba

Az átalakítási parancsfájl létrehoz egy *közös hozzáférési aláírási (SAS) URI-* t a konvertált modellhez. Ezt az URI-t mostantól a **modell neveként** is másolhatja a gyors üzembe helyezési minta alkalmazásba (lásd: gyors útmutató [: modell megjelenítése Unity](render-model.md)használatával).

![Modell cseréje Unity](./media/replace-model-in-unity.png)

 A mintának most be kell töltenie és megjelenítenie az egyéni modellt!

## <a name="optional-re-creating-a-sas-uri"></a>Nem kötelező: SAS URI újbóli létrehozása

Az átalakítási parancsfájl által létrehozott SAS URI-azonosító csak 24 órára érvényes. A lejártát követően azonban nem kell újra konvertálnia a modellt. Ehelyett létrehozhat egy új SAS-t a portálon a következő lépésekben leírtak szerint:

1. Nyissa meg az [Azure Portalt](https://www.portal.azure.com)
1. Kattintson a **Storage-fiók** erőforrás: ![ képernyőkép, amely kiemeli a kiválasztott Storage-fiók erőforrását.](./media/portal-storage-accounts.png)
1. A következő képernyőn kattintson a bal oldali panel **Storage Explorer** elemére, és keresse meg a kimeneti modellt (*. arrAsset* fájlt) a *arroutput* blob Storage-tárolóban. Kattintson a jobb gombbal a fájlra, majd válassza a **közös hozzáférésű aláírás beolvasása** lehetőséget a helyi menüben: ![ aláírás-hozzáférés](./media/portal-storage-explorer.png)
1. Megnyílik egy új képernyő, ahol kiválaszthatja a lejárati dátumot. Kattintson a **Létrehozás** gombra, és másolja ki a következő párbeszédpanelen megjelenő URI-t. Ez az új URI a parancsfájl által létrehozott ideiglenes URI-t váltja fel.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az alapokat, tekintse meg az oktatóanyagokat, amelyekkel részletesebb ismereteket szerezhet.

Ha szeretné megismerni a modell átalakításának részleteit, tekintse meg [a modell átalakítási REST API](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Oktatóanyag: távolról megjelenített modellek megtekintése](../tutorials/unity/view-remote-models/view-remote-models.md)