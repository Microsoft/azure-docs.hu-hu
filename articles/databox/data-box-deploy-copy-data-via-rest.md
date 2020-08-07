---
title: 'Oktatóanyag: másolás a blob Storage-ba REST API-kon keresztül'
titleSuffix: Azure Data Box
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat Azure Data Box blob Storage-hoz a REST API-k használatával http-vagy https-kapcsolaton keresztül, majd az adatok Azure Data Boxból való másolásával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: alkohli
ms.openlocfilehash: 71f966cd62ffd2c735259dcfa98b9b97f87d9a19
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926195"
---
# <a name="tutorial-use-rest-apis-to-copy-data-to-azure-data-box-blob-storage"></a>Oktatóanyag: REST API-k használata adatok másolásához Azure Data Box blob Storage-ba  

Ez az oktatóanyag a REST API-kon keresztül *http* -vagy *https*-kapcsolaton keresztüli Azure Data Box blob Storage-hoz való kapcsolódás eljárásait ismerteti. A csatlakozás után az Data Box blob Storage-ba való másolásához szükséges lépések, valamint a Data Box a szállításra való előkészítésének lépései is le vannak írva.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás Data Box blob Storage-hoz *http* -vagy *https* -kapcsolaton keresztül
> * Adatok másolása a Data Boxra

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Az [ Az Azure Data Box beállítása](data-box-deploy-set-up.md) című oktatóanyagot.
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Áttekintette [Data Box blob Storage rendszerkövetelményeit](data-box-system-requirements-rest.md) , és ismeri az API-k, SDK-k és eszközök támogatott verzióit.
4. Olyan gazdagéphez fér hozzá, amely a Data Boxba másolni kívánt adattal rendelkezik. A gazdaszámítógépen:
    * egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    * egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, a rendszer egy 1 GbE adatkapcsolatot használ, de a másolási sebesség hatással lesz rá.
5. [Töltse le a AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) a gazdagépen. A AzCopy használatával másolhatja át az adatait Azure Data Box blob Storage-ba a gazdagép számítógépről.

## <a name="connect-via-http-or-https"></a>Kapcsolat http-n vagy HTTPS-en keresztül

Data Box blob Storage-hoz *http* -vagy *https*-kapcsolaton keresztül is csatlakozhat.

* A *https* a Data Box blob Storage-hoz való kapcsolódás biztonságos és ajánlott módja.
* A *http* -t megbízható hálózatokhoz való csatlakozáskor használja a rendszer.

A csatlakozás lépései eltérőek, amikor *http* -vagy *https*-kapcsolaton keresztül csatlakozik Data Box blob Storage-hoz.

## <a name="connect-via-http"></a>Kapcsolat http-n keresztül

A Data Box blob Storage REST API-khoz *http* -n keresztül történő kapcsolódáshoz a következő lépések szükségesek:

* Az eszköz IP-címe és a blob Service-végpont hozzáadása a távoli gazdagéphez
* Külső gyártótól származó szoftver konfigurálása és a kapcsolatok ellenőrzése

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Eszköz IP-címének és blob szolgáltatásbeli végpontjának hozzáadása

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="verify-connection-and-configure-partner-software"></a>A kapcsolat ellenőrzése és a partneri szoftver konfigurálása

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Csatlakozási HTTPS-kapcsolaton keresztül

Az Azure Blob Storage REST API-khoz HTTPS-kapcsolaton keresztül történő kapcsolódáshoz a következő lépések szükségesek:

* A tanúsítvány letöltése Azure Portal
* A tanúsítvány importálása az ügyfélen vagy a távoli gazdagépen
* Az eszköz IP-címe és a blob szolgáltatás végpontjának hozzáadása az ügyfélhez vagy a távoli gazdagéphez
* Külső gyártótól származó szoftver konfigurálása és a kapcsolatok ellenőrzése

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

### <a name="download-certificate"></a>Tanúsítvány letöltése

A tanúsítvány letöltéséhez használja a Azure Portal.

1. Jelentkezzen be az Azure Portalra.
2. Lépjen a Data Box sorrendbe, és keresse meg az **általános > eszköz adatait**.
3. Az **eszköz hitelesítő adatai**területen nyissa meg az eszköz **API-hozzáférését** . Kattintson a **Letöltés**gombra. Ez a művelet letölt egy ** \<your order name> . cer** tanúsítványfájl-fájlt. **Mentse** ezt a fájlt. Ezt a tanúsítványt azon az ügyfélen vagy gazdaszámítógépen kell telepíteni, amelyet az eszközhöz való kapcsolódáshoz használni fog.

    ![Tanúsítvány letöltése Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)

### <a name="import-certificate"></a>Tanúsítvány importálása

Data Box blob Storage HTTPS-kapcsolaton keresztüli eléréséhez TLS/SSL-tanúsítvány szükséges az eszközhöz. A tanúsítványnak az ügyfélalkalmazás számára elérhetővé tételének módja az alkalmazástól az alkalmazásig, illetve az operációs rendszerek és a disztribúciók között változik. Egyes alkalmazások hozzáférnek a tanúsítványhoz a rendszer tanúsítványtárolóba való importálása után, míg más alkalmazások nem használják ezt a mechanizmust.

Az egyes alkalmazásokra vonatkozó konkrét információkat ebben a szakaszban talál. Az egyéb alkalmazásokkal kapcsolatos további információkért olvassa el az alkalmazás dokumentációját és a használt operációs rendszert.

A következő lépésekkel importálhatja a `.cer` fájlt egy Windows-vagy Linux-ügyfél legfelső szintű tárolójába. Windows rendszeren a Windows PowerShell vagy a Windows Server felhasználói felület használatával importálhatja és telepítheti a tanúsítványt a rendszeren.

#### <a name="use-windows-powershell"></a>A Windows PowerShell használata

1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként.
2. A parancssorba írja be a következőt:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>A Windows Server felhasználói felületének használata

1. Kattintson a jobb gombbal a `.cer` fájlra, majd válassza a **tanúsítvány telepítése**lehetőséget. Ez a művelet elindítja a tanúsítvány importálása varázslót.
2. Az **áruház helye**területen válassza a **helyi számítógép**lehetőséget, majd kattintson a **tovább**gombra.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3. Jelölje be **az összes tanúsítvány tárolása a következő tárolóban**jelölőnégyzetet, majd kattintson a **Tallózás**gombra. Navigáljon a távoli gazdagép legfelső szintű tárolójához, majd kattintson a **tovább**gombra.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4. Kattintson a **Befejezés** gombra. Megjelenik egy üzenet, amely tájékoztatja, hogy az importálás sikeres volt.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Linux rendszer használata

A tanúsítvány importálásának módszere az eloszlástól függ.

Több, például Ubuntu és Debian is használja az `update-ca-certificates` parancsot.  

* Nevezze át a Base64 kódolású tanúsítványfájl `.crt` kiterjesztését, és másolja a fájlba `/usr/local/share/ca-certificates directory` .
* Futtassa a következő parancsot: `update-ca-certificates`.

A RHEL, Fedora és CentOS legújabb verziói a parancsot használják `update-ca-trust` .

* Másolja a tanúsítványfájl-fájlt a `/etc/pki/ca-trust/source/anchors` könyvtárba.
* A `update-ca-trust` parancs futtatása.

A részletekért olvassa el a disztribúcióra vonatkozó dokumentációt.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Eszköz IP-címének és blob szolgáltatásbeli végpontjának hozzáadása 

[Ha *http*-kapcsolaton keresztül csatlakozik, kövesse ugyanezen lépéseket az eszköz IP-címének és a blob-szolgáltatás végpontjának hozzáadásához](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>A partner szoftver konfigurálása és a kapcsolat ellenőrzése

Kövesse a lépéseket a [ *http*-kapcsolaton keresztül használt partneri szoftverek konfigurálásához](#verify-connection-and-configure-partner-software). Az egyetlen különbség, hogy a *http használata beállítás* nincs bejelölve.

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Ha csatlakozott a Data Box blob Storage-hoz, a következő lépés az Adatmásolás. Az Adatmásolás előtt tekintse át a következő szempontokat:

* Adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az [Azure Storage és a Data Box korlátaival](data-box-limits.md) foglalkozó cikkben ismertetett méretkorlátoknak.
* Ha az Data Box által feltöltött adatok párhuzamosan fel vannak töltve a Data Boxon kívül más alkalmazásokkal, ez a feladatok feltöltésével és az adatok sérülésével járhat.

> [!IMPORTANT]
> Gondoskodjon róla, hogy megtartja a forrásadatok egy másolatát addig, amíg ellenőrizheti, hogy a Data Box valóban átvitte-e az adatokat az Azure Storage-ba.

Ebben az oktatóanyagban a AzCopy az Adatmásolás Data Box blob Storage-ba való másolására szolgál. A Azure Storage Explorer is használhatja (ha a GUI-alapú eszköz) vagy a partner szoftverét az adatmásoláshoz.

A másolási eljárás a következő lépésekből áll:

* Tároló létrehozása
* Mappa tartalmának feltöltése Data Box blob Storage-ba
* Módosított fájlok feltöltése Data Box blob Storage-ba

Ezeket a lépéseket a következő szakaszokban részletesen ismertetjük.

### <a name="create-a-container"></a>Tároló létrehozása

Első lépésként hozzon létre egy tárolót, mivel a Blobok mindig egy tárolóba lesznek feltöltve. A tárolók a Blobok csoportjait rendezik, például a fájlok mappákba rendezését a számítógépén. A blob-tárolók létrehozásához kövesse az alábbi lépéseket.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki azt a Storage-fiókot, amelyen létre kívánja hozni a BLOB-tárolót.
3. Kattintson a jobb gombbal a **blob-tárolók**elemre, majd a helyi menüben válassza a **blob-tároló létrehozása**lehetőséget.

   ![BLOB-tárolók létrehozása helyi menü](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. A **blob-tárolók** mappa alatt egy szövegmező jelenik meg. Adja meg a blobtároló nevét. Tekintse meg a [tároló létrehozása és az engedélyek beállítása](../storage/blobs/storage-quickstart-blobs-dotnet.md) a blob-tárolók elnevezésére vonatkozó szabályokra és korlátozásokra vonatkozó információkat.
5. Nyomja le az **ENTER** billentyűt, amikor elkészült a blob-tároló létrehozásához, vagy az **ESC billentyűt** a megszakításhoz. A blob-tároló sikeres létrehozása után a rendszer a kiválasztott Storage-fiók **blob containers** mappájában jelenik meg.

   ![BLOB-tároló létrehozva](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Mappa tartalmának feltöltése Data Box blob Storage-ba

A AzCopy használatával feltöltheti a mappában lévő összes fájlt a blob Storage-ba Windows vagy Linux rendszeren. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

#### <a name="linux"></a>Linux

```azcopy
azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S
```

Cserélje le a `<key>` billentyűt a fiók kulcsára. A fiók kulcsának beszerzéséhez a Azure Portal nyissa meg a Storage-fiókját. Lépjen a **beállítások > hozzáférési kulcsok**elemre, válasszon ki egy kulcsot, és illessze be a AzCopy parancsba.

Ha a célként megadott tároló nem létezik, az AzCopy létrehozza, majd feltölti a fájlt a tárolóba. Frissítse a forrás elérési útját az adatkönyvtárra, és cserélje le a `data-box-storage-account-name` célhely URL-címére a Data Box társított Storage-fiók nevével.

A megadott könyvtár tartalmának a Blob Storage-ba való rekurzív feltöltéséhez adja meg a `--recursive` (Linux) vagy az `/S` (Windows) beállítást. Ha az AzCopyt ezen beállítások egyikével futtatja, minden almappa és a bennük tárolt fájlok is feltöltődnek.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Módosított fájlok feltöltése Data Box blob Storage-ba

A AzCopy használatával tölthet fel fájlokat a legutóbbi módosítási idejük alapján. Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Ha csak a frissített vagy új fájlokat szeretné feltölteni, adja hozzá az `--exclude-older` (Linux) vagy az `/XO` (Windows) paramétert az AzCopy-parancshoz.

Ha csak azokat az erőforrásokat szeretné átmásolni a forrásból, amelyek nem léteznek a célhelyen, adja meg az `--exclude-older` és az `--exclude-newer` (Linux), vagy az `/XO` és az `/XN` (Windows) paramétereket az AzCopy-parancsban. Az AzCopy az időbélyegek alapján csak a frissített adatokat tölti fel.

#### <a name="linux"></a>Linux

```azcopy
azcopy \
--source /mnt/myfolder \
--destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
--dest-key <key> \
--recursive \
--exclude-older
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO
```

Ha a csatlakozás vagy a másolás művelet során hibák léptek fel, tekintse meg a [Data Box blob Storage problémáinak elhárítása](data-box-troubleshoot-rest.md)című témakört.

A következő lépés az eszköz szállításra való előkészítése.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás Data Box blob Storage-hoz *http* -vagy *https* -kapcsolaton keresztül
> * Adatok másolása a Data Boxra

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)
