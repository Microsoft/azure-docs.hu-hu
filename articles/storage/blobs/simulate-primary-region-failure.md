---
title: Oktatóanyag – hiba szimulálása az adatoknak az elsődleges régióból való beolvasásakor
titleSuffix: Azure Storage
description: Hiba szimulálása az adatok az elsődleges régióból való beolvasásakor, ha a Storage-fiók konfigurálva van az olvasási hozzáférésű geo-Zone-redundáns tároláshoz (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91280351"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Oktatóanyag: hiba szimulálása az adatoknak az elsődleges régióból való beolvasása során

Ez az oktatóanyag egy sorozat második része. Ebben az útmutatóban megismerheti az [olvasási hozzáférésű geo-Zone-redundáns tárolás](../common/storage-redundancy.md) (ra-GZRS) előnyeit a hibák szimulálása révén.

A hiba szimulálása érdekében [statikus útválasztást](#simulate-a-failure-with-an-invalid-static-route) vagy [hegedűst](#simulate-a-failure-with-fiddler)is használhat. Mindkét módszer lehetővé teszi, hogy szimulálja a kéréseket az [olvasási hozzáférésű geo-redundáns](../common/storage-redundancy.md) (ra-GZRS) Storage-fiók elsődleges végpontján, így helyette az alkalmazásnak a másodlagos végpontról való olvasását fogja eredményezni.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás futtatása és szüneteltetése
> * [Érvénytelen statikus útvonal](#simulate-a-failure-with-an-invalid-static-route) vagy [Hegedűs](#simulate-a-failure-with-fiddler) hibájának szimulálása
> * Elsődleges végpont visszaállításának szimulálása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végezze el az előző oktatóanyagot: az [alkalmazásadatok elérhetővé tétele az Azure Storage][previous-tutorial]szolgáltatással.

Ha statikus útválasztással szeretne hibát szimulálni, egy rendszergazda jogú parancssort fog használni.

Ha a Hegedűs használatával nem sikerül szimulálni a hibát, töltse le és [telepítse a hegedűst](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Hiba szimulálása érvénytelen statikus útvonallal

Érvénytelen statikus útvonalat hozhat létre az [olvasási hozzáférésű geo-redundáns](../common/storage-redundancy.md) (ra-GZRS) Storage-fiók elsődleges végpontjának az összes kéréshez. Ebben az oktatóanyagban a helyi gazdagép a tárfiókba érkező kérések útválasztására szolgáló átjáró. Ha a helyi gazdagépet használja átjáróként, a tárfiók elsődleges végpontjára érkező összes kérés visszatér a gazdagépre, ami hibához vezet. Kövesse az alábbi lépéseket, ha az érvénytelen statikus útvonal használatával szeretné a hibát szimulálni, és az elsődleges végpontot visszaállítani.

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

Az [előző oktatóanyag][previous-tutorial] utasításait követve elindíthatja a mintát, és letöltheti a teszt fájlt, amely megerősíti, hogy az elsődleges tárolóból származik. A célként megadott platformtól függően manuálisan is szüneteltetheti a mintát, vagy megvárhatja a kérdést.

### <a name="simulate-failure"></a>Hibaszimuláció

Amíg az alkalmazás szüneteltetve van, nyisson meg egy parancssort a Windows rendszergazdaként, vagy futtassa a terminált Linux rendszeren root-ként.

A Storage-fiók elsődleges végpontjának tartományával kapcsolatos információk lekéréséhez írja be a következő parancsot egy parancssorba vagy terminálba, `STORAGEACCOUNTNAME` és cserélje le a nevet a Storage-fiók nevére.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

A későbbi lépésekhez másolja a tárfiók IP-címét egy szövegszerkesztőbe.

A helyi gazdagép IP-címének lekéréséhez írja be az `ipconfig` parancsot a Windows-parancssorba vagy az `ifconfig` parancsot a Linux-terminálba.

Ha statikus útvonalat szeretne hozzáadni a cél gazdagéphez, írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba, `<destination_ip>` majd cserélje le a Storage-fiók IP-címére és `<gateway_ip>` a helyi gazdagép IP-címére.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

A futó mintát tartalmazó ablakban folytassa az alkalmazást, vagy nyomja le a megfelelő kulcsot a minta fájl letöltéséhez, és ellenőrizze, hogy az a másodlagos tárolóból származik-e. Ezután szüneteltetheti a mintát, vagy megvárhatja a kérést.

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

Ha újra szeretné szimulálni az elsődleges végpont működőképességét, törölje az érvénytelen statikus útvonalat az útválasztási táblából. Ez lehetővé teszi, hogy az elsődleges végpont az alapértelmezett átjárón keresztül legyen irányítva. Írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Ezután folytathatja az alkalmazást, vagy lenyomhatja a megfelelő kulcsot a minta fájl újbóli letöltéséhez, ezzel megerősítve, hogy ismét az elsődleges tárolóból származik.

## <a name="simulate-a-failure-with-fiddler"></a>Hiba szimulálása a Fiddlerrel

A Hegedűs hibájának szimulálása érdekében az RA-GZRS Storage-fiók elsődleges végpontjának küldött kérések sikertelen válaszát adja meg.

Az alábbi fejezetek azt mutatják be, hogyan szimulálható a hiba és az elsődleges végpont visszaállítása a hegedűsrel.

### <a name="launch-fiddler"></a>A Fiddler elindítása

Nyissa meg a Fiddlert, és válassza a **Rules** (Szabályok), majd a **Cutomize Rules** (Szabályok testreszabása) lehetőséget.

![Fiddler-szabályok testreszabása](media/simulate-primary-region-failure/figure1.png)

A Hegedűs ScriptEditor elindítja és megjeleníti a **SampleRules.js** fájlt. Ezzel a fájllal szabható testre a Fiddler.

Illessze be a következő kódrészletet a `OnBeforeResponse` függvénybe, és cserélje `STORAGEACCOUNTNAME` le a nevet a Storage-fiók nevére. A mintától függően előfordulhat, hogy a lecserélni kívánt `HelloWorld` teszt fájl (vagy egy előtag, például) nevét is le kell cserélni `sampleFile` . Az új kód megjegyzésbe kerül, hogy a szolgáltatás ne fusson azonnal.

Ha elkészült, válassza a **fájl** és **Mentés** lehetőséget a módosítások mentéséhez. Hagyja nyitva a ScriptEditor ablakot a következő lépésekben való használatra.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Testreszabott szabály beillesztése](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

Az [előző oktatóanyag][previous-tutorial] utasításait követve elindíthatja a mintát, és letöltheti a teszt fájlt, amely megerősíti, hogy az elsődleges tárolóból származik. A célként megadott platformtól függően manuálisan is szüneteltetheti a mintát, vagy megvárhatja a kérdést.

### <a name="simulate-failure"></a>Hibaszimuláció

Amíg az alkalmazás szüneteltetve van, váltson vissza a Hegedűsre, és írja be a megjegyzésbe a függvényben mentett egyéni szabályt `OnBeforeResponse` . Ügyeljen arra, hogy a **fájl** és **Mentés** lehetőség kiválasztásával mentse a módosításokat, így a szabály érvénybe lép. Ez a kód az RA-GZRS Storage-fiókra irányuló kéréseket keres, és ha az elérési út tartalmazza a minta fájl nevét, a a válasz kódját adja vissza `503 - Service Unavailable` .

A futó mintát tartalmazó ablakban folytassa az alkalmazást, vagy nyomja le a megfelelő kulcsot a minta fájl letöltéséhez, és ellenőrizze, hogy az a másodlagos tárolóból származik-e. Ezután szüneteltetheti a mintát, vagy megvárhatja a kérést.

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

A Hegedűsben távolítsa el, vagy véleményezze az egyéni szabályt. A **fájl** és **Mentés** lehetőség kiválasztásával győződjön meg arról, hogy a szabály már nem lesz érvényben.

A futó mintát tartalmazó ablakban folytassa az alkalmazást, vagy nyomja le a megfelelő kulcsot a minta fájl letöltéséhez, és győződjön meg arról, hogy az elsődleges tárolóból származik. Ezután kilép a mintából.

## <a name="next-steps"></a>Következő lépések

A sorozat második részében megtanulta, hogyan szimulálhatja az olvasási hozzáférés földrajzi redundáns tárolásának tesztelési hibáját.

Ha többet szeretne megtudni az RA-GZRS tárolás működéséről, valamint a kapcsolódó kockázatokról, olvassa el a következő cikket:

> [!div class="nextstepaction"]
> [HA-alkalmazások tervezése RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
