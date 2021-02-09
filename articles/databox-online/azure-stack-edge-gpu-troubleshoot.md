---
title: Az Azure Stack Edge Pro és a GPU használatával történő hibakeresés Azure Portal Microsoft Docs
description: Útmutatás Azure Stack Edge Pro GPU-problémák elhárításához.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: c56fcecbd850dd0add26e5d50093eea595e3d825
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833422"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Az Azure Stack Edge Pro GPU-eszköz problémáinak elhárítása 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez a cikk az Azure Stack Edge Pro GPU-eszközön felmerülő problémák elhárítását ismerteti. 


## <a name="run-diagnostics"></a>Diagnosztika futtatása

Az eszközök diagnosztizálásához és a hibák elhárításához futtassa le a diagnosztikai teszteket. Eszközének helyi webes felhasználói felületen kövesse az alábbi lépéseket a diagnosztikai tesztek futtatásához.

1. A helyi webes felhasználói felületen válassza a **Hibaelhárítás > Diagnosztikai tesztek** lehetőséget. Válassza ki a futtatni kívánt tesztet, és válassza a **teszt futtatása** lehetőséget. A teszt a hálózat, az eszköz, a webproxy, az idő vagy a felhő beállításainak esetleges problémáit diagnosztizálja. Értesítést kap, hogy az eszköz teszteket futtat.

    ![Tesztek kiválasztása ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. A tesztek lefutása után megjelennek az eredmények. 

    ![Teszt eredményeinek megtekintése](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Ha egy teszt sikertelen, megjelenik a javasolt művelet URL-címe. Válassza ki az URL-címet a javasolt művelet megtekintéséhez.
 
    ![A sikertelen tesztek figyelmeztetések áttekintése](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Támogatási csomag összeállítása

A naplócsomag tartalmazza az összes naplót, amely segít a Microsoft ügyfélszolgálatának az eszközproblémák elhárításában. A naplócsomagot a helyi webes felhasználói felületen hozhatja létre.

Kövesse az alábbi lépéseket a támogatási csomag összeállításához. 

1. A helyi webes felhasználói felületen válassza a **Hibaelhárítás > Támogatás** lehetőséget. Válassza a **támogatási csomag létrehozása** lehetőséget. A rendszer megkezdi a támogatási csomag összeállítását. A csomagok gyűjteményének létrehozása eltarthat néhány percig.

    ![Válassza a felhasználó hozzáadása elemet.](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. A támogatási csomag létrehozása után válassza a **támogatási csomag letöltése** lehetőséget. A rendszer a tömörített csomagot letölti a megadott helyre. Csomagolja ki a csomagot, és tekintse meg a rendszernapló fájljait.

    ![Válassza a felhasználó hozzáadása 2 lehetőséget](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Speciális biztonsági naplók összegyűjtése

A speciális biztonsági naplók lehetnek szoftveres vagy hardveres behatolási naplók az Azure Stack Edge Pro-eszközhöz.

### <a name="software-intrusion-logs"></a>Szoftveres behatolási naplók

A rendszer begyűjti a szoftver behatolását vagy az alapértelmezett tűzfal naplóit a bejövő és kimenő forgalomhoz. 

- Ha az eszköz a gyárban található, az alapértelmezett tűzfal-naplózás engedélyezve van. Ezek a naplók alapértelmezés szerint a támogatási csomagba kerülnek, amikor a helyi felhasználói felületen vagy az eszköz Windows PowerShell-felületén keresztül hoz létre egy támogatási csomagot.

- Ha a támogatási csomagban csak a tűzfal-naplókra van szükség a szoftveres (NW) behatolás ellenőrzéséhez az eszközön, használja a `-Include FirewallLog` támogatási csomag létrehozásakor lehetőséget. 

- Ha nincs megadva konkrét belefoglalási lehetőség, a rendszer alapértelmezés szerint a tűzfal naplóját is tartalmazza a támogatási csomagban.

- A támogatási csomagban a tűzfal naplója a `pfirewall.log` és a gyökérmappa. Íme egy példa az Azure Stack Edge Pro-eszköz szoftveres behatolási naplójára. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Hardveres behatolási naplók

Az eszközre irányuló hardveres behatolás észlelése érdekében a rendszer jelenleg az összes váz-eseményt naplózza, például a váz megnyitását vagy zárását. 

- Az eszközről származó rendszer eseménynaplója a parancsmag használatával olvasható `racadm` . Ezeket az eseményeket a rendszer a váz vonatkozású eseményekre szűri egy `HWIntrusion.txt` fájlba.

- Ha csak a hardveres behatolási naplót szeretné beolvasni a támogatási csomagban, a `-Include HWSelLog` támogatási csomag létrehozásakor használja a lehetőséget. 

- Ha nincs megadva konkrét belefoglalási lehetőség, a rendszer a hardveres behatolási naplót alapértelmezettként tartalmazza a támogatási csomagban.

- A támogatási csomagban a hardveres behatolási napló a `HWIntrusion.txt` és a gyökérkönyvtárban található. Íme egy példa az Azure Stack Edge Pro-eszköz hardveres behatolási naplójára. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Hibaelhárítás naplók használatával

A feltöltési és frissítési folyamat során észlelt hibákat a megfelelő hibafájlok tartalmazzák.

1. A hibaüzenetek megtekintéséhez lépjen a megosztásra, és válassza ki a megosztást a tartalom megtekintéséhez. 


2. Válassza ki a _Microsoft Data Box Edge mappát_. Ebben a mappában két almappát talál:

    - A feltöltési mappa, amely a feltöltési hibákat tartalmazó naplófájlokat tárolja.
    - A frissítési mappa a frissítés során bekövetkezett hibák számára.

    Íme egy minta naplófájl, amely a frissítési hibákat tartalmazza.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Ha ebben a fájlban hibát talál (a mintában kiemelve látható), jegyezze fel a hibakódot, amely ebben az esetben 16001. Keresse meg a hibakód leírását a következő hibaadatok alapján.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Hibajelentések használata a hibák megoldásához

A hibák listáját azonosított forgatókönyvek állítják össze, és az öndiagnosztizáláshoz és a hibaelhárításhoz is használható. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Az itt látható hibák a Azure Resource Manager konfigurációjában jelenhetnek meg az eszköz eléréséhez. 

| **Probléma/hibák** |  **Resolution** (Osztás) | 
|------------|-----------------|
|Általános problémák|<li>[Ellenőrizze, hogy a peremhálózati eszköz megfelelően van](#verify-the-device-is-configured-properly)-e konfigurálva.<li> [Ellenőrizze, hogy az ügyfél megfelelően van-e konfigurálva](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: hiba történt a kérelem elküldésekor.<br>At. sor: 1 karakter: 1<br>+ Add-AzureRmEnvironment-Name AZ3-ARMEndpoint " https://management.dbe ...|Ez a hiba azt jelenti, hogy az Azure Stack Edge Pro-eszköz nem érhető el vagy nem megfelelően van konfigurálva. Ellenőrizze, hogy a peremhálózati eszköz és az ügyfél megfelelően van-e konfigurálva. Útmutatásért tekintse meg a táblázat **általános problémák** sorát.|
|A szolgáltatás hibát adott vissza. További részletekért tekintse meg a InnerException: az alapul szolgáló kapcsolat bezárult: nem hozható létre megbízhatósági kapcsolat az SSL/TLS biztonságos csatorna számára. |   Ennek a hibának az a valószínűsége, hogy egy vagy több saját tanúsítványa helytelenül lett végrehajtva. [Itt](./azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)talál útmutatást. |
|A művelet érvénytelen állapotkódot adott vissza: "ServiceUnavailable". <br> A válasz állapotkód nem jelzi a sikert: 503 (a szolgáltatás nem érhető el). | Ez a hiba a fenti feltételek bármelyikének következménye lehet.<li>A ArmStsPool leállított állapotban van.</li><li>A Azure Resource Manager-vagy biztonsági jogkivonat-szolgáltatások webhelyei nem állnak le.</li><li>A Azure Resource Manager fürterőforrás nem található.</li><br><strong>Megjegyzés:</strong> Előfordulhat, hogy a készülék újraindítása megoldhatja a problémát, de a támogatási csomagot össze kell gyűjtenie, hogy tovább lehessen a hibakereséshez.|
|AADSTS50126: Érvénytelen felhasználónév vagy jelszó.<br>Nyomkövetési azonosító: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>Korrelációs azonosító: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Timestamp: 2019-11-15 09:21:57Z: a távoli kiszolgáló hibát adott vissza: (400) hibás kérés.<br>At. sor: 1 karakter: 1 |Ez a hiba a fenti feltételek bármelyikének következménye lehet.<li>Az érvénytelen Felhasználónév és jelszó beállításnál ellenőrizze, hogy az ügyfél módosította-e a jelszót Azure Portal az [itt](./azure-stack-edge-j-series-set-azure-resource-manager-password.md) leírt lépéseket követve, majd a megfelelő jelszó használatával.<li>Érvénytelen bérlői azonosító esetén a bérlő azonosítója rögzített GUID, és a következőre kell beállítani: `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|összekapcsolás-AzureRmAccount: AADSTS90056: az erőforrás le van tiltva, vagy nem létezik. Ellenőrizze az alkalmazás kódjában, hogy megadta-e az elérni kívánt erőforrás URL-címét.<br>Nyomkövetési azonosító: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>Korrelációs azonosító: 75c8ef5a-830e-48b5-B039-595a96488ff9 timestamp: 2019-11-18 07:00:51Z: a távoli kiszolgáló hibát adott vissza: (400) rossz |A parancsban használt erőforrás-végpontok `Add-AzureRmEnvironment` helytelenek.|
|Nem lehet lekérni a végpontokat a felhőből.<br>Gondoskodjon arról, hogy legyen hálózati kapcsolatban. Hiba részletei: HTTPSConnectionPool (host = "Management. dbg-of4k6suvm.microsoftdatabox.com", Port = 30005): a maximális újrapróbálkozások száma túllépte az URL-címet:/metadata/endpoints? API-Version = 2015-01-01 (SSLError okozta (SSLError ("hibás kézfogás: hiba ([) (SSL-rutinok," tls_process_server_certificate, "tanúsítvány ellenőrzése sikertelen")],) ",") |Ez a hiba többnyire egy Mac-/Linux-környezetben jelenik meg, és az alábbi problémák miatt fordul elő:<li>Nem adtak hozzá PEM formátumú tanúsítványt a Python tanúsítványtárolóhoz.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Ellenőrizze, hogy az eszköz megfelelően van-e konfigurálva

1. A helyi felhasználói felületen ellenőrizze, hogy az eszköz hálózata megfelelően van-e konfigurálva.

2. Ellenőrizze, hogy a tanúsítványok frissültek-e az összes végpontra vonatkozóan, az [itt](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)említettek szerint.

3. Kérje le a Azure Resource Manager felügyeleti és bejelentkezési végpontot az **eszköz** oldaláról a helyi felhasználói felületen.

4. Győződjön meg arról, hogy az eszköz aktiválva van és regisztrálva van az Azure-ban.


### <a name="verify-the-client-is-configured-properly"></a>Ellenőrizze, hogy az ügyfél megfelelően van-e konfigurálva

1. Ellenőrizze, hogy a PowerShell megfelelő verziója telepítve van-e az [itt](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client)említettek szerint.

2. Ellenőrizze, hogy a megfelelő PowerShell-modulok telepítve vannak-e az [itt](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)említettek szerint.

3. Ellenőrizze, hogy a Azure Resource Manager és a bejelentkezési végpontok elérhetők-e. A végpontok pingelése is megpróbálkozhat. Például:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Ha nem érhetők el, adja hozzá a DNS/Host-fájlok bejegyzéseit az [itt](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)említettek szerint.
   
4. Ellenőrizze, hogy az Ügyféltanúsítványok telepítve vannak-e az [itt](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)említettek szerint.

5. Ha az ügyfél a PowerShellt használja, engedélyeznie kell a hibakeresési előnyt, hogy a PowerShell-parancs futtatásával részletes üzeneteket lásson. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Blob Storage az eszközön 

Itt láthatók a blob Storage szolgáltatással kapcsolatos hibák Azure Stack Edge Pro/Data Box Gateway eszközön.

| **Probléma/hibák** |  **Resolution** (Osztás) | 
|--------------------|-----------------|
|Nem sikerült beolvasni a gyermek erőforrásokat. A HTTP-fejlécek egyikének értéke nem megfelelő formátumú.| A **Szerkesztés** menüben válassza a **cél Azure stack API**-k elemet. Ezután indítsa újra Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Győződjön meg arról, hogy a végpont neve `<accountname>.blob.<serialnumber>.microsoftdatabox.com` hozzá van adva a Hosts-fájlhoz ezen az elérési úton: `C:\Windows\System32\drivers\etc\hosts` Windows vagy `/etc/hosts` Linux rendszeren.|
|Nem sikerült beolvasni a gyermek erőforrásokat.<br> Részletek: önaláírt tanúsítvány |Importálja az eszközéhez tartozó SSL-tanúsítványt Azure Storage Explorerba: <ol><li>Töltse le a tanúsítványt a Azure Portal. További információ: [a tanúsítvány letöltése](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>A **Szerkesztés** menüben válassza az SSL-tanúsítványok lehetőséget, majd válassza a **tanúsítványok importálása** lehetőséget.</li></ol>|
|A AzCopy parancs úgy tűnik, hogy a hiba megjelenítése előtt nem válaszol egy percre:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Győződjön meg arról, hogy a végpont neve `<accountname>.blob.<serialnumber>.microsoftdatabox.com` hozzá van adva a Hosts fájlhoz a következő helyen: `C:\Windows\System32\drivers\etc\hosts` .|
|A AzCopy parancs úgy tűnik, hogy a hiba megjelenítése előtt nem válaszol egy percre:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importálja az eszköz SSL-tanúsítványát a rendszer tanúsítványtárolóba. További információ: [a tanúsítvány letöltése](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|A AzCopy parancs úgy tűnik, hogy a hiba megjelenítése előtt 20 perccel nem válaszol:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Győződjön meg arról, hogy a végpont neve `<accountname>.blob.<serialnumber>.microsoftdatabox.com` hozzá van adva a Hosts fájlhoz a következő helyen: `/etc/hosts` .|
|A AzCopy parancs úgy tűnik, hogy a hiba megjelenítése előtt 20 perccel nem válaszol:<br>`Error parsing source location… The SSL connection could not be established`. |Importálja az eszköz SSL-tanúsítványát a rendszer tanúsítványtárolóba. További információ: [a tanúsítvány letöltése](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|A AzCopy parancs úgy tűnik, hogy a hiba megjelenítése előtt 20 perccel nem válaszol:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Győződjön meg arról, hogy a végpont neve `<accountname>.blob.<serialnumber>.microsoftdatabox.com` hozzá van adva a Hosts fájlhoz a következő helyen: `/etc/hosts` .|
|A AzCopy parancs úgy tűnik, hogy 20 percen belül leállítja a válaszadást a hiba megjelenítése előtt: `Error parsing source location… The SSL connection could not be established` .|Importálja az eszköz SSL-tanúsítványát a rendszer tanúsítványtárolóba. További információ: [a tanúsítvány letöltése](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|A HTTP-fejlécek egyikének értéke nem megfelelő formátumú.|A Data Box nem támogatja a Python Microsoft Azure Storage könyvtárának telepített verzióját. Lásd: Azure Data Box blob Storage-követelmények a támogatott verziókhoz.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| A Python futtatása előtt állítsa a REQUESTS_CA_BUNDLE környezeti változót a Base64 kódolású SSL-tanúsítványfájl elérési útjára (lásd: [a tanúsítvány letöltése](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Például:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Másik lehetőségként adja hozzá a tanúsítványt a rendszer tanúsítványtárolóhoz, majd állítsa be ezt a környezeti változót a tároló elérési útjára. Például az Ubuntu rendszeren:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|A kapcsolatok időtúllépést mutatnak.|Jelentkezzen be az Azure Stack Edge Pro-ba, majd győződjön meg róla, hogy a zárolása fel van oldva. Amikor az eszköz újraindul, zárolva marad, amíg valaki bejelentkezik.|

## <a name="troubleshoot-iot-edge-errors"></a>IoT Edge hibák elhárítása

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Következő lépések

- További információ az [eszközök aktiválásával kapcsolatos hibák elhárításáról](azure-stack-edge-gpu-troubleshoot-activation.md).