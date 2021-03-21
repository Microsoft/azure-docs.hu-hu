---
title: Élő videó-elemzések – IoT Edge – Azure
description: Ez a cikk a IoT Edge élő videó-elemzések hibaelhárítási lépéseit ismerteti.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d766843f58bc2cdd0dcdddfad337b23fefb28768
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698739"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Élő videó-elemzések hibáinak megoldása IoT Edge

Ez a cikk a Azure IoT Edge élő video Analytics (LVA) hibaelhárítási lépéseit ismerteti.

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

### <a name="diagnostics"></a>Diagnosztika

Az élő video Analytics üzembe helyezésének részeként be kell állítania az Azure-erőforrásokat, például a IoT Hub és IoT Edge eszközöket. A problémák diagnosztizálásának első lépéseként mindig ellenőrizze, hogy a peremhálózati eszköz megfelelően van-e beállítva az alábbi utasítások követésével:

1. [Futtassa a `check` parancsot](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Győződjön meg róla, hogy IoT Edge verziója](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. A [IoT Edge Security Manager és a naplóinak állapotának ellenõrzése](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Megtekintheti az IoT Edge hub-on keresztül haladó üzeneteket](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Indítsa újra a tárolókat](../../iot-edge/troubleshoot.md#restart-containers).
1. [Keresse meg a tűzfal és a port konfigurációs szabályait](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Üzembe helyezés előtti problémák

Ha a peremhálózat-infrastruktúra rendben van, megkeresheti a telepítési jegyzékfájlval kapcsolatos problémákat. Ha a IoT Edge eszközön IoT Edge modulban szeretné üzembe helyezni az élő videó-elemzést a többi IoT-modul mellett, akkor olyan központi telepítési jegyzékfájlt használ, amely tartalmazza az IoT Edge hubot, a IoT Edge ügynököt és az egyéb modulokat és azok tulajdonságait. A jegyzékfájl üzembe helyezéséhez a következő parancsot használhatja:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Ha a JSON-kód nem megfelelően van formázva, a következő hibaüzenet jelenhet meg:   
&nbsp;&nbsp;&nbsp;**Nem sikerült elemezni a JSON-fájlt a következő fájlból: " <deployment manifest.json> " a "Content" argumentummal: "extra adatok: sor 101 oszlop 1 (char 5325)"**

Ha ezt a hibát tapasztalja, javasoljuk, hogy ellenőrizze a JSON-t a hiányzó zárójelek vagy más problémák esetén a fájl struktúrájával. A fájl struktúrájának ellenőrzéséhez használhat olyan ügyfelet, mint például a [Notepad + + JSON Viewer beépülő modullal](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) vagy egy online eszközzel, például a [json formázó & validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Üzembe helyezés során: Diagnosztizálás a Media Graph közvetlen módszereivel 

Miután a IoT Edge eszközön megfelelően telepítette az élő videó-elemzést IoT Edge modulon, a [közvetlen metódusok](direct-methods.md)meghívásával létrehozhatja és futtathatja az adathordozó-diagramot.  
>[!NOTE]
>  A közvetlen metódus hívásait csak a modulhoz kell átadni **`lvaEdge`** .

A Azure Portal használatával a Media Graph diagnosztizálását közvetlen módszerekkel futtathatja:

1. A Azure Portal nyissa meg a IoT Edge eszközhöz csatlakoztatott IoT hubot.

1. Keresse meg az **automatikus eszközkezelés** elemet, majd válassza a **IoT Edge** lehetőséget.  

1. Az Edge-eszközök listájában válassza ki a diagnosztizálni kívánt eszközt.  
         
    ![A Edge-eszközök listáját megjelenítő Azure Portal képernyőképe](./media/troubleshoot-how-to/lva-sample-device.png)


1. Ellenőrizze, hogy a válasz kódja *200 – OK*. A [IoT Edge futtatókörnyezethez](../../iot-edge/iot-edge-runtime.md) tartozó egyéb válaszok a következők:
    * 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
    * 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
    * 412 – a séma verziója érvénytelen a telepítési konfigurációban.
    * 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
    * 500 – hiba történt a IoT Edge futtatókörnyezetben.

    > [!TIP]
    > Ha Azure IoT Edge-modulok futtatásával kapcsolatos problémákat tapasztal a környezetben, a hibaelhárításhoz és a diagnosztikahez használja a **[szabványos diagnosztikai lépéseket Azure IoT Edge](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** útmutatóként.
### <a name="post-deployment-direct-method-error-code"></a>Üzembe helyezés utáni hiba: közvetlen metódus hibakódja
1. Ha állapotot kap `501 code` , győződjön meg arról, hogy a közvetlen metódus neve pontos. Ha a metódus neve és a kérelmek hasznos adatai pontosak, akkor az eredményeket a siker Code = 200 értékkel együtt kell megkapnia. 
1. Ha a kérelem hasznos adatai pontatlanok, akkor egy olyan állapotot `400 code` és egy válasz-adattartalmat fog kapni, amely a közvetlen metódus hívásával segít a probléma diagnosztizálásában.
    * A jelentett és a kívánt tulajdonságok ellenőrzése segíthet megérteni, hogy a modul tulajdonságai szinkronizálva lettek-e a telepítéssel. Ha nem, akkor újraindíthatja IoT Edge eszközét. 
    * A [közvetlen metódusok](direct-methods.md) útmutatóval hívhat meg néhány módszert, különösen az egyszerűeket, például a GraphTopologyList. Az útmutató a várt kérelmek és válaszok hasznos adatait és hibakódokat is megadja. Az egyszerű közvetlen metódusok sikeres elvégzése után biztos lehet abban, hogy az élő videó-elemzési IoT Edge modul működés közben rendben van.
        
       ![Képernyőkép a IoT Edge modul "Direct metódus" paneljéről.](./media/troubleshoot-how-to/direct-method.png) 

1. Ha a **központi telepítésben megadott** és az **eszköz oszlopai által jelentett** érték azt jelzi, hogy az *Igen*, közvetlen metódusokat hívhat meg az élő videó Analytics IoT Edge modulján. Válassza ki a modult, ahol megtekintheti a kívánt és jelentett tulajdonságokat, és közvetlen metódusokat hívhat meg. Fontos szem előtt tartani a következőket: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Üzembe helyezés után: a Futtatás során felmerülő hibák diagnosztizálásának diagnosztizálása 

A IoT Edge modulhoz tartozó tároló naplófájljainak diagnosztikai adatokat kell tartalmazniuk a modul futtatókörnyezete során felmerülő hibák hibakereséséhez. A probléma megoldásához megtekintheti a [tárolói naplókat, és megvizsgálhatja](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) a problémát. 

Ha az összes korábbi ellenőrzést futtatta, és továbbra is problémákba ütközik, Gyűjtse össze a naplókat a IoT Edge eszközről [az `support bundle` paranccsal](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) , hogy az Azure csapata további elemzéseket végezzen. A támogatási szolgálattal és az összegyűjtött naplók beküldésével [kapcsolatba léphet velünk](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="common-error-resolutions"></a>Gyakori hibák megoldásai

Az élő videó elemzése IoT Edge modulként van telepítve a IoT Edge eszközön, és együttműködik a IoT Edge ügynökkel és a hub-modulokkal. Az élő videó Analytics-telepítéssel kapcsolatos gyakori hibákat az alapul szolgáló IoT-infrastruktúrával kapcsolatos problémák okozzák. A hibák a következők:

* [A IoT Edge ügynök egy perc múlva leáll](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [A IoT Edge ügynök nem fér hozzá a modul rendszerképéhez (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Az IoT Edge Agent modul "üres konfigurációs fájlt" jelent, és egyetlen modul sem indul el az eszközön](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Az IoT Edge hub nem indul el](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [A IoT Edge biztonsági démon érvénytelen állomásnévvel meghiúsul](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Az élő videó-elemzés vagy bármely más egyéni IoT Edge modul nem tud üzenetet küldeni a peremhálózati hubhoz 404 hibával](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [A IoT Edge modul üzembe helyezése sikeresen megtörtént, majd eltűnik az eszközről](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Ha Azure IoT Edge-modulok futtatásával kapcsolatos problémákat tapasztal a környezetben, a hibaelhárításhoz és a diagnosztikahez használja a **[szabványos diagnosztikai lépéseket Azure IoT Edge](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** útmutatóként.

Az **[élő videó elemzési erőforrásainak telepítési parancsfájljának](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)** futtatásakor problémák merülhetnek fel. Gyakori problémák a következők:

* Olyan előfizetés használata, amely nem rendelkezik tulajdonosi jogosultságokkal. Ez azt eredményezi, hogy a parancsfájl **ForbiddenError** vagy **AuthorizationFailed** hiba miatt meghiúsul.
    * A probléma megkezdéséhez győződjön meg arról, hogy rendelkezik a használni kívánt előfizetéshez tartozó **tulajdonosi** jogosultságokkal. Ha saját maga nem tudja megtenni, lépjen kapcsolatba az előfizetés rendszergazdájával, és adja meg a megfelelő jogosultságokat.
* **A sablon központi telepítése a szabályzat megsértése miatt nem sikerült.**
    * A probléma megoldásához működjön együtt a rendszergazdával, és győződjön meg arról, hogy a hívás (ok) hoz létre virtuális gépet az SSH-hitelesítés blokkolásának mellőzéséhez. Erre nem lesz szükség, mert olyan biztonságos megerősített hálózatot használunk, amely felhasználónevet és jelszót igényel az Azure-erőforrásokkal való kommunikációhoz. Ezeket a hitelesítő adatokat a rendszer a **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** fájlban tárolja Cloud Shell a virtuális gép sikeres létrehozása, üzembe helyezése és a IoT hubhoz való csatolása után.
* A telepítési parancsfájl nem tud létrehozni egyszerű szolgáltatásnevet és/vagy Azure-erőforrásokat.
    * A probléma megkezdéséhez ellenőrizze, hogy az előfizetés és az Azure-bérlő nem érte el a maximális szolgáltatási korlátot. További információ az [Azure ad szolgáltatás korlátairól és korlátozásáról](../../active-directory/enterprise-users/directory-service-limits-restrictions.md) , valamint az [Azure-előfizetések és-szolgáltatások korlátairól, kvótáinak és megkötéséről.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

> [!TIP]
> Ha további problémák merülhetnek fel, amelyekhez segítségre lehet szüksége, **[Gyűjtse össze a naplókat, és nyújtson be egy támogatási jegyet](#collect-logs-for-submitting-a-support-ticket)**. A következő címen küldhet nekünk egy e-mailt a címen: **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** .
### <a name="live-video-analytics-working-with-external-modules"></a>Külső modulok használata élő videó-elemzéssel

A Media Graph bővítmény processzorait használó élő videó-elemzések kiterjeszthetik a Media Graphot, hogy más IoT Edge-moduloktól származó adatok küldésére és fogadására HTTP vagy gRPC protokollok használatával. Egy [adott példaként](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)ez a Media Graph képkockákat küldhet képként egy külső következtetési modulba, mint például a Yolo v3, és HTTP protokoll használatával fogadja a JSON-alapú elemzési eredményeket. Ilyen topológiában az események rendeltetése többnyire az IoT hub. Olyan helyzetekben, amikor nem látja a következtetési eseményeket a központban, ellenőrizze a következőket:

* Ellenőrizze, hogy a Media Graph által közzétett központ és a megtekinteni kívánt központ azonos-e. Ha több központi telepítést hoz létre, előfordulhat, hogy több hubokat is felvesz, és helytelenül vizsgálja meg a nem megfelelő hubot az eseményekhez.
* Azure Portal ellenőrizze, hogy a külső modul telepítve van-e és fut-e. A példában szereplő képen a rtspsim, a yolov3, a tinyyolov3 és a logAnalyticsAgent IoT Edge a lvaEdge modulon kívül futó modulok.

    [![Képernyőkép, amely megjeleníti a modulok futó állapotát az Azure IoT Hubban. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Ellenőrizze, hogy a megfelelő URL-végpontra küld-e eseményeket. A külső AI-tároló egy URL-címet és egy portot tesz elérhetővé, és visszaadja a POST kérések adatait. Ez az URL-cím `endpoint: url` a http-bővítmény processzorának tulajdonsága. Ahogy a [topológia URL-címe](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json)is látható, a végpont az URL-cím paraméterre van beállítva. Győződjön meg arról, hogy a paraméter vagy az átadott érték alapértelmezett értéke pontos. Teszteléssel ellenőrizheti, hogy működik-e az ügyfél URL-címe (cURL) használatával.  

    Példaként itt látható egy olyan Yolo v3-tároló, amely a helyi gépen fut a 172.17.0.3 IP-címével.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Eredmény visszaadva:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > A gép IP-címének megkereséséhez használja a **[Docker-vizsgálat parancsot](https://docs.docker.com/engine/reference/commandline/inspect/)** .
    
* Ha olyan gráf egy vagy több példányát futtatja, amely a Media Graph bővítmény processzorát használja, akkor a `samplingOptions` mező használatával kezelheti a videó-hírcsatorna képkockák másodpercenkénti sebességét. 

   * Bizonyos helyzetekben, ahol a peremhálózati gép PROCESSZORa vagy memóriája magas kihasználtsággal rendelkezik, elveszítheti bizonyos következtetési eseményeket. A probléma megoldásához állítson be egy alacsony értéket a `maximumSamplesPerSecond` mező tulajdonságához `samplingOptions` . Beállíthatja a 0,5 ("maximumSamplesPerSecond": "0,5") értékre a gráf minden egyes példányán, majd újra futtathatja a példányt, hogy megkeresse a következtetési eseményeket a központban.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Több közvetlen metódus párhuzamos – időtúllépési hiba 

A IoT Edge Live Video Analytics közvetlen, módszeren alapuló programozási modellt biztosít, amely lehetővé teszi több topológia és több gráf-példány beállítását. A topológia és a gráf beállításának részeként a IoT Edge modulban több közvetlen metódus hívása is meghívja őket. Ha párhuzamosan hívja meg ezeket a több metódust, különösen azokat, amelyek elindítják és leállítják a diagramokat, időtúllépési hibát tapasztalhat, például az alábbiakat: 

A szerelvény inicializálási metódusa Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync kivételt okozott. Microsoft. Azure. Devices. Common. kivétellistájára. IotHubException: Microsoft. Azure. Devices. Common. kivételek. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Javasoljuk, hogy a közvetlen metódusokat *ne* hívja párhuzamosan. Hívja meg őket egymás után (azaz hozzon egy közvetlen metódust csak az előző befejezése után).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Naplók gyűjtése támogatási jegy elküldéséhez

Ha az önállóan vezérelt hibaelhárítási lépések nem oldják meg a problémát, lépjen a Azure Portal, és [Nyisson meg egy támogatási jegyet](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> A naplók tartalmazhatnak személyes azonosításra alkalmas adatokat, például az Ön IP-címét. A naplók összes helyi példánya törölve lesz, amint befejeződik a vizsgálat, és lezárjuk a támogatási jegyet.  

A jegyhez hozzáadni kívánt naplók összegyűjtéséhez kövesse az alábbi utasításokat a támogatási kérés **részletek** ablaktábláján, és töltse fel a naplófájlokat.  
1. [Az élő videó elemzési moduljának konfigurálása a részletes naplók összegyűjtéséhez](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Hibakeresési naplók bekapcsolása](#live-video-analytics-debug-logs)
1. Reprodukálja a hibát
1. Kapcsolódás a virtuális géphez a portál **IoT hub** oldaláról
    1. A *debugLogs* mappában található összes fájl zip.

       > [!NOTE]
       > Ezek a naplófájlok nem az öndiagnosztika célja. Ezeket az Azure mérnöki csapatának szánták, hogy elemezzék a problémákat.

       * A következő parancsban ne felejtse el lecserélni **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICEt** a **2. lépésben** korábban beállított peremhálózati eszközön a hibakeresési naplók helyére.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Csatolja a *debugLogs.zip* fájlt a támogatási jegyhez.
1. Futtassa a [támogatási Köteg parancsot](#use-the-support-bundle-command), Gyűjtse össze a naplókat, és csatolja a támogatási jegyhez.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Az élő videó elemzési moduljának konfigurálása a részletes naplók összegyűjtéséhez
Konfigurálja az élő videó elemzési modulját a részletes naplók összegyűjtéséhez a `logLevel` következő beállításával `logCategories` :
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Ezt a következő módokon teheti meg:
* **Azure Portal** az élő videó Analytics moduljában az Identity Twin-beállítások elemre kattintva frissítse a modul Identity Twin tulajdonságát [ ![ . ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Vagy a **telepítési jegyzékfájlban** is hozzáadhatja ezeket a bejegyzéseket az élő videó elemzési moduljának tulajdonságok csomópontjában.

### <a name="use-the-support-bundle-command"></a>A support-Bundle parancs használata

Ha IoT Edge-eszközről kell összegyűjtenie a naplókat, a legegyszerűbb módszer a `support-bundle` parancs használata. Ez a parancs a következőket gyűjti:

- Modulok naplói
- A IoT Edge Security Manager és a Container Engine naplói
- IoT Edge a JSON-kimenet ellenőrzését
- Hasznos hibakeresési információk

1. Futtassa a `support-bundle` parancsot a *--since* jelzővel annak megadásához, hogy mennyi idő elteltével szeretné lefedni a naplókat. Például a 2H az elmúlt két órában naplózza a naplókat. A jelző értékét módosíthatja úgy, hogy a különböző időszakokra vonatkozó naplókat tartalmazzon.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Ez a parancs létrehoz egy *support_bundle.zip* nevű fájlt abban a könyvtárban, ahol a parancsot futtatta. 
   
1. Csatolja a *support_bundle.zip* fájlt a támogatási jegyhez.

### <a name="live-video-analytics-debug-logs"></a>Élő videó-elemzési hibakeresési naplók

Ha a hibakeresési naplók létrehozásához IoT Edge modul Live Video Analytics szolgáltatását szeretné konfigurálni, tegye a következőket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg az IoT hubot.
1. A bal oldali ablaktáblán válassza a **IoT Edge** lehetőséget.
1. Az eszközök listájában válassza ki a cél eszköz AZONOSÍTÓját.
1. A panel tetején válassza a **modulok beállítása** lehetőséget.

   ![Képernyőkép a "modulok beállítása" gombra a Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. A **IoT Edge-modulok** szakaszban keresse meg és válassza a **lvaEdge** lehetőséget.
1. Válassza a **tároló létrehozási beállítások lehetőséget**.
1. A **kötések** szakaszban adja hozzá a következő parancsot:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Ez a parancs a peremhálózati eszköz és a tároló közötti naplók mappáját köti össze. Ha a naplókat egy másik helyen szeretné összegyűjteni, használja a következő parancsot, és cserélje le **$LOG _LOCATION_ON_EDGE_DEVICE** a használni kívánt helyre: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Válassza a **Frissítés** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Egy sikeres érvényesítési üzenet a zöld szalagcím alatt lesz közzétéve.
1. Válassza a **Létrehozás** lehetőséget.
1. Frissítse a **modul Identity Twin** értékét, hogy az DebugLogsDirectory paraméterre mutasson, amely arra a könyvtárra mutat, amelyben a naplók gyűjtése történik:

    a. A **modulok** táblázatban válassza a **lvaEdge** lehetőséget.  
    b. A panel tetején válassza a **modul Identity Twin** elemet. Megnyílik egy szerkeszthető ablaktábla.  
    c. A **kívánt kulcs** alatt adja hozzá a következő kulcs/érték párokat:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Ez a parancs a peremhálózati eszköz és a tároló közötti naplók mappáját köti össze. Ha a naplókat egy másik helyen szeretné összegyűjteni az eszközön:
    > 1. Hozzon létre egy kötést a hibakeresési napló helyéhez a **kötések** szakaszban, és cserélje le a **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** és **$Debug _LOG_LOCATION** a kívánt helyre: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Használja az alábbi parancsot, és cserélje le **$DEBUG _LOG_LOCATION** az előző lépésben használt helyre:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Kattintson a **Mentés** gombra.


1. Leállíthatja a naplózási gyűjteményt úgy, hogy az értéket a **modul Identity Twin** értékével *NULL értékűre* állítja. Térjen vissza a **modul Identity Twin** oldalára, és frissítse a következő paramétert:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Ajánlott eljárások a naplózáshoz

A [figyelésnek és a naplózásnak](monitoring-logging.md) segítséget kell nyújtania a taxonómia megismeréséhez és olyan naplók létrehozásához, amelyek segítenek a LVA kapcsolatos hibák hibakeresésében. 

Mivel a gRPC-kiszolgáló implementációja különbözik az egyes nyelveken, nincs szabványos módja a naplózás hozzáadásának a kiszolgálón belül.  

Ha például a .NET Core használatával hoz létre gRPC-kiszolgálót, a gRPC szolgáltatás a **gRPC** kategóriában adja meg a naplókat. Ha a gRPC részletes naplókat szeretne engedélyezni, konfigurálja a Grpc előtagjait a hibakeresési appsettings.jsszintre a fájlban a következő elemek a naplózás naplózási szint alszakaszba való hozzáadásával: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Ezt a Startup. cs fájlban is konfigurálhatja a ConfigureLogging használatával: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

A [gRPC-ben a .net-ben a naplózás és a diagnosztika](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) útmutatást nyújt bizonyos diagnosztikai naplók gRPC-kiszolgálókról történő összegyűjtéséhez. 

### <a name="a-failed-grpc-connection"></a>Sikertelen gRPC-kapcsolatok 

Ha egy gráf aktív, és egy kameráról áramlik, a rendszer az élő videó Analytics szolgáltatásban tartja karban a kapcsolatokat. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>A CPU-és GPU-erőforrások terhelésének figyelése és kiegyensúlyozása, ha ezek az erőforrások szűk keresztmetszetet jelentenek

Az élő videó elemzése nem figyeli vagy nem nyújt hardveres erőforrás-figyelést. A fejlesztőknek a hardvergyártók figyelési megoldásait is használniuk kell. Ha azonban Kubernetes-tárolókat használ, az eszközt a [Kubernetes-irányítópult](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)használatával figyelheti. 

a .NET Core-dokumentumok gRPC emellett értékes információkat is megoszthatnak a [teljesítmény bevált eljárásaival](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) és a [terheléselosztással](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing)kapcsolatban.  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Egy következtetési kiszolgáló hibaelhárítása, ha nem kap semmilyen képkockát és kap egy "ismeretlen" protokollhiba 

Több dolgot is megtehet a problémával kapcsolatos további információk megszerzéséhez.  

* Adja meg a "**ediaPipeline** -napló kategóriáját az élő videó analitikai moduljának kívánt tulajdonságaiban, és győződjön meg arról, hogy a naplózási szint értékre van állítva `Information` .  
* A hálózati kapcsolat teszteléséhez futtassa a következő parancsot a peremhálózati eszközről. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Ha a parancs egy rövid, zagyva szövegű karakterláncot ad vissza, akkor a Telnet sikeresen megnyitotta a kapcsolódást a következtetési kiszolgálóhoz, és megnyit egy bináris gRPC csatornát. Ha ezt nem látja, akkor a Telnet hálózati hibát jelez. 
* A következtetési kiszolgálón engedélyezheti a további naplózást a gRPC-könyvtárban. Ez további információkat is biztosíthat a gRPC csatornáról. Ez a nyelvtől függően változik, a [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1)-ra vonatkozó utasításokat itt találja. 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>További rendszerképek kiválogatása a gRPC pufferből anélkül, hogy vissza kellene küldenie az első puffer eredményét.

A gRPC adatátviteli szerződés részeként az élő videó Analytics által küldött összes üzenetet meg kell ismerni a gRPC-hivatkozó kiszolgálónak. Nem ismerhető fel, hogy egy képkeret visszaigazolja az adategyezményt, és nemkívánatos helyzeteket eredményezhet.  

A gRPC-kiszolgáló élő videó-elemzéssel való használatához a közös memória a legjobb teljesítmény érdekében használható. Ehhez a programozási nyelv/környezet által kiváltott linuxos megosztott memória-képességeket kell használnia. 

1. Nyissa meg a Linux megosztott memória leíróját.
1. Egy keret fogadásakor a rendszer a megosztott memórián belüli eltoláshoz fér hozzá.
1. Nyugtázza a keret feldolgozásának befejezését, hogy a memóriája visszaigényelhető legyen az élő videó-elemzéssel.

   > [!NOTE]
   > Ha késlelteti a keret beérkezésének elfogadását a videós elemzésre hosszú ideig, az azt eredményezheti, hogy a megosztott memória megtelt, és adatvesztést okoz.
1. Az egyes kereteket tetszés szerinti adatstruktúrában (lista, tömb stb.) tárolhatja a következtetési kiszolgálón.
1. Ezután futtathatja a feldolgozási logikát, ha a kívánt számú képkockával rendelkezik.
1. Ha elkészült, visszaállíthatja az eredményül kapott eredményeket az élő videó elemzéséhez.

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: eseményvezérelt videofelvétel a felhőben és a felhőből való lejátszás](event-based-video-recording-tutorial.md)