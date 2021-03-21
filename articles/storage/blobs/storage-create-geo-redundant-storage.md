---
title: Oktatóanyag – kiválóan elérhető alkalmazás létrehozása blob Storage-val
titleSuffix: Azure Storage
description: Használja az olvasási hozzáférésű geo-Zone-redundáns (RA-GZRS) tárolót, hogy az alkalmazás adatai elérhetők legyenek.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: 0d597f0742cfc43f1c7fb38568b2a2bbda352beb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049338"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Oktatóanyag: kiválóan elérhető alkalmazás létrehozása blob Storage-val

Ez az oktatóanyag egy sorozat első része. Itt megtudhatja, hogyan teheti elérhetővé az alkalmazás adatait az Azure-ban.

Az oktatóanyag elvégzése után egy olyan konzol-alkalmazás fog rendelkezni, amely feltölti és lekéri egy blobot egy [olvasási hozzáférésű geo-Zone-redundáns](../common/storage-redundancy.md) (ra-GZRS) Storage-fiókból.

Az Azure Storage-ban a Geo-redundancia aszinkron módon replikálja a tranzakciókat egy elsődleges régióból egy olyan másodlagos régióba, amely több száz mérföld távolságra van. A replikációs folyamat garantálja a másodlagos régió adatainak végső konzisztenciáját. A konzol alkalmazás az [áramkör-megszakító](/azure/architecture/patterns/circuit-breaker) mintával határozza meg, hogy melyik végponthoz kell csatlakoznia, a végpontok közötti automatikus váltás a hibák és a helyreállítások szimulálása.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * A kapcsolati sztring beállítása
> * A konzolalkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et az **Azure-fejlesztési** számítási feladattal.

  ![Azure-fejlesztés (a Web és felhőszolgáltatások alatt)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

* A [Python](https://www.python.org/downloads/) telepítése
* A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) letöltése és telepítése

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

* Telepítse a [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A Storage-fiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és eléréséhez.

Az alábbi lépéseket követve hozzon létre egy olvasási hozzáférésű geo-Zone-redundáns (RA-GZRS) Storage-fiókot:

1. Válassza az **erőforrás létrehozása** gombot a Azure Portal.
2. Válassza ki a **Storage-fiók-blob, fájl, tábla, üzenetsor** lehetőséget az **új** lapon.
4. Töltse ki a tárfiók űrlapját a következő adatokkal az alábbi képen látható módon, és kattintson a **Létrehozás** elemre:

   | Beállítás       | Mintaérték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Előfizetés** | *Saját előfizetés* | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.azure.com/Subscriptions) ismertető cikket. |
   | **ResourceGroup** | *myResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
   | **Név** | *mystorageaccount* | A Storage-fiók egyedi neve. |
   | **Hely** | *USA keleti régiója* | Válassza ki a helyet. |
   | **Teljesítmény** | *Standard* | A standard szintű teljesítmény jó megoldás a példaként szolgáló forgatókönyvhöz. |
   | **Fiók altípusa** | *StorageV2* | Az általános célú v2 Storage-fiók használata ajánlott. További információ az Azure Storage-fiókok típusairól: a [Storage-fiók áttekintése](../common/storage-account-overview.md). |
   | **Replikáció**| *Írásvédett geo-zónaredundáns tárolás (RA-GZRS)* | Az elsődleges régió a zóna redundáns, és egy másodlagos régióba replikálódik, és olvasási hozzáféréssel rendelkezik a másodlagos régióhoz. |
   | **Hozzáférési szint**| *Gyakori* | A gyakran használt adatokhoz használja a gyors elérésű szintet. |

    ![tárfiók létrehozása](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>A minta letöltése

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy konzolalkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy egyszerű Python-alkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

[Töltse le a minta projektet](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) , és bontsa ki a fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A minta projekt egy alapszintű Node.js alkalmazást tartalmaz.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>A minta konfigurálása

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. Ezt a kapcsolati sztringet tárolhatja egy környezeti változóban az alkalmazást futtató helyi gépen. A környezeti változó létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati sztringjét**. Futtassa a következő parancsok egyikét az operációs rendszer alapján, és cserélje \<yourconnectionstring\> le a-t a tényleges kapcsolatban lévő karakterláncra. A parancs egy környezeti változót ment a helyi számítógépen. A Windows rendszerben a környezeti változó nem érhető el, amíg újra nem tölti be a használni kívánt **parancssort** vagy rendszerhéjat.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

Az alkalmazásban meg kell adnia a Storage-fiók hitelesítő adatait. Ezeket az információkat az alkalmazást futtató helyi gépen található környezeti változókban tárolhatja. A környezeti változók létrehozásához kövesse az alábbi példák egyikét az operációs rendszertől függően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Illessze be a **Storage-fiók nevét** és a **kulcs** értékeit a következő parancsokra, és cserélje le a \<youraccountname\> és a \<youraccountkey\> helyőrzőket. Ez a parancs menti a környezeti változókat a helyi gépre. A Windows rendszerben a környezeti változó nem érhető el, amíg újra nem tölti be a használni kívánt **parancssort** vagy rendszerhéjat.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

A minta futtatásához hozzá kell adnia a Storage-fiók hitelesítő adatait a `.env.example` fájlhoz, majd át kell neveznie a következőre: `.env` .

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Ezeket az információkat a Azure Portal megkeresheti a Storage-fiókjához való navigálással, és a **Beállítások** szakaszban található **hozzáférési kulcsok lehetőség** kiválasztásával.

Telepítse a szükséges függőségeket. Ehhez nyisson meg egy parancssort, lépjen a minta mappájába, majd írja be a következőt: `npm install` .

---

## <a name="run-the-console-application"></a>A konzolalkalmazás futtatása

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A Visual Studióban nyomja le az **F5** billentyűt, vagy kattintson a **Start** gombra az alkalmazás hibakeresésének megkezdéséhez. A Visual Studio automatikusan visszaállítja a hiányzó NuGet-csomagokat, ha vannak konfigurálva. További információért látogasson el a [csomagok telepítése és újratelepítése csomag-visszaállítással](/nuget/consume-packages/package-restore#package-restore-overview) című témakörre.

Megnyílik a konzolablak, és az alkalmazás futni kezd. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a rendszerkép replikálva lett-e a másodlagos RA-GZRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden olvasást egy **P** vagy egy **S** jelöl. Ahol a **P** az elsődleges végpontot és az **S-t** jelöli, a másodlagos végpontot.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `Program.cs` fájlban található `RunCircuitBreakerAsync` művelettel letölt egy képet a tárfiókból a [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metódus segítségével. A letöltés előtt meg kell határozni egy [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) környezetet. A műveleti környezet határozza meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

Az alkalmazás terminálon vagy parancssorban való futtatásához lépjen a **circuitbreaker.py** könyvtárra, majd írja be a `python circuitbreaker.py` parancsot. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a rendszerkép replikálva lett-e a másodlagos RA-GZRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden olvasást egy **P** vagy egy **S** jelöl. Ahol a **P** az elsődleges végpontot és az **S-t** jelöli, a másodlagos végpontot.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `circuitbreaker.py` fájlban található `run_circuit_breaker` metódussal letölt egy képet a tárfiókból a [get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-) metódus segítségével.

A Storage-objektum újrapróbálkozási függvénye lineáris újrapróbálkozási szabályzatra van beállítva. Az újrapróbálkozási függvény határozza meg, hogy egy kérelmet újra kell-e próbálni, valamint megadja, hogy hány másodpercnyi várakozás után történjen az újrapróbálkozás. A **retry\_to\_secondary** paramétert állítsa true (igaz) értékre, ha a kérelmet a másodlagos végponton kell újra megkísérelni, amennyiben az elsődleges végpontra irányuló első kérelem sikertelen lenne. A mintaalkalmazásban az egyéni újrapróbálkozási szabályzat a Storage-objektum `retry_callback` függvényében van definiálva.

A letöltés előtt a szolgáltatás objektum [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) és [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) függvény van definiálva. Ezek a függvények határozzák meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

A minta futtatásához nyisson meg egy parancssort, lépjen a minta mappájába, majd írja be a következőt: `node index.js` .

A minta létrehoz egy tárolót a blob Storage-fiókban, feltölti **HelloWorld.png** a tárolóba, majd ismételten ellenőrzi, hogy a tároló és a rendszerkép replikálva lett-e a másodlagos régióba. A replikálást követően a rendszer felszólítja, hogy a letöltéshez vagy a kilépéshez adja meg a **D** vagy a **Q** értéket (majd írja be). A kimenetnek az alábbi példához kell hasonlítania:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `OperationContextRetrying` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) paramétere `SecondaryOnly` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Befejezett kérelem eseménykezelő

A rendszer akkor hívja meg az `OperationContextRequestCompleted` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) paramétert `PrimaryThenSecondary` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `retry_callback` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) paramétere `SECONDARY` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Befejezett kérelem eseménykezelő

A rendszer akkor hívja meg az `response_callback` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) paramétert `PRIMARY` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

A Node.js v10 SDK-val a visszahívási kezelők nem szükségesek. A minta Ehelyett egy újrapróbálkozási lehetőségekkel és egy másodlagos végponttal konfigurált folyamatot hoz létre. Ez lehetővé teszi, hogy az alkalmazás automatikusan átváltson a másodlagos folyamatra, ha nem sikerül elérnie az adatait az elsődleges folyamaton keresztül.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Következő lépések

A sorozat első részében megtanulta, hogyan lehet az alkalmazást az RA-GZRS Storage-fiókokkal nagykörben elérhetővé teszi.

Folytassa a sorozat második részével, hogy megtudja, hogyan szimulálhat egy hibát, és kényszerítheti az alkalmazást a másodlagos RA-GZRS végpont használatára.

> [!div class="nextstepaction"]
> [Az elsődleges régióból beolvasott hiba szimulálása](simulate-primary-region-failure.md)