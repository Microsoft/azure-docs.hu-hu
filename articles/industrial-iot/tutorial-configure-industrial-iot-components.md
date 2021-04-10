---
title: Az Azure Industrial IoT összetevőinek konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan módosíthatja a konfiguráció alapértelmezett értékeit.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787686"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Oktatóanyag: az ipari IoT-összetevők konfigurálása

Az üzembe helyezési parancsfájl automatikusan konfigurálja az összes összetevőt, hogy az alapértelmezett értékek alapján működjön egymással. Az alapértelmezett értékek beállításai azonban módosíthatók a követelmények teljesítése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az összetevők konfigurációjának testreszabása


Íme néhány az összetevőkre vonatkozó további testreszabási beállításokkal:
* IoT Hub
    * Hálózatkezelés → nyilvános hozzáférés: az Internet-hozzáférés konfigurálása, például IP-szűrők
    * Hálózatkezelés → magánhálózati végponti kapcsolatok: hozzon létre egy olyan végpontot, amely nem érhető el az interneten keresztül, és más Azure-szolgáltatások vagy helyszíni eszközök (például VPN-kapcsolaton keresztül) számára is felhasználható.
    * IoT Edge: az OPC UA-kiszolgálókhoz csatlakozó peremhálózati eszközök konfigurációjának kezelése 
* Cosmos DB
    * Adatreplikálás globálisan: az adatredundancia konfigurálása
    * Tűzfal-és virtuális hálózatok: az Internet-és VNET-hozzáférés konfigurálása és IP-szűrők
    * Privát végponti kapcsolatok: olyan végpont létrehozása, amely nem érhető el az interneten keresztül 
* Key Vault
    * Titkok: a platform beállításainak kezelése
    * Hozzáférési szabályzatok: felügyelheti, hogy mely alkalmazások és felhasználók férhetnek hozzá a Key Vault lévő adatokhoz, és hogy mely műveletek (például olvasás, írás, Listázás, törlés) engedélyezettek a hálózaton, a tűzfalon, a VNET és a magánhálózati végpontokon.
* Azure Active Directory (HRE) → Alkalmazásregisztrációk
    * <APP_NAME>-web → hitelesítés: a válasz URI-k kezelése, amely a legördülő lapokként használható URI-k listája a hitelesítés sikeressége után. Előfordulhat, hogy az üzembe helyezési parancsfájl bizonyos helyzetekben nem tud automatikusan konfigurálni, például a HRE rendszergazdai jogosultságok hiánya miatt. Érdemes lehet URI-ket felvenni vagy módosítani a webalkalmazás állomásnévének módosításakor, például a localhost által a hibakereséshez használt portszámot.
* App Service
    * Konfiguráció: a szolgáltatásokat vagy felhasználói felületet vezérlő környezeti változók kezelése
* Virtuális gép
    * Hálózatkezelés: támogatott hálózatok és tűzfalszabályok konfigurálása
    * Serial console: SSH-hozzáférés az eredmények beszerzéséhez vagy a hibakereséshez, a hitelesítő adatok lekérése az üzembe helyezési parancsfájl kimenetéről vagy a jelszó alaphelyzetbe állítása
* IoT Hub → IoT Edge
    * Kezelheti a központhoz hozzáférő IoT Edge-eszközök identitásait, beállíthatja, hogy mely modulok legyenek telepítve, és milyen konfigurációt használnak, például az OPC-közzétevő kódolási paramétereit.
* IoT Hub → IoT Edge → \<DEVICE> → set modules → OpcPublisher (csak önálló OPC közzétevői művelet esetén)


## <a name="configuration-options"></a>Beállítási lehetőségek

|Konfigurációs beállítás (Gyorsírás/teljes név)    |    Leírás   |
|----------------------------------------------|------------------|
PF/publishfile |A fájlnevet a közzétenni kívánt csomópontok konfigurálásához. Ha ez a beállítás meg van adva, az OPC-közzétevőt önálló módba helyezi.
LF/logfile |A használandó naplófájl fájlneve.
ll/naplózási szint |A használandó naplózási szint (engedélyezett: végzetes, hiba, figyelmeztetés, információ, hibakeresés, részletes).
Me/MessageEncoding |A kimenő üzenetek üzenetkezelési kódolása engedélyezett értékek: JSON, Uadp
PP/messagingmode |Üzenetküldési mód a kimenő üzenetek számára megengedett értékek: közzététel, minták
FM/fullfeaturedmessage |Az üzenetek teljes funkcionalitású módja (az összes mező kitöltve). Az alapértelmezett érték a "true", az örökölt kompatibilitáshoz a "false" értéket használja.
AA/autoelfogadás |A közzétevő megbízható minden olyan kiszolgálót, amely a következőhöz csatlakozik
BS/batchSize |A kötegelt feldolgozáshoz gyorsítótárazni kívánt OPC UA-adatváltozási üzenetek száma.
si/iothubsendinterval |Az aktiválási köteg időköze másodpercben.
MS/iothubmessagesize |A (IoT D2C) üzenet maximális mérete.
om/maxoutgressmessages |A (IoT D2C) üzenet kimenő pufferének maximális mérete.
di/diagnosticsinterval |Megjeleníti a közzétevő diagnosztikai adatait a megadott intervallumban másodpercben (a naplózási szint adatai szükségesek). -1 letiltja a távoli diagnosztikai naplót és a diagnosztikai kimenetet
lt/logflugtimespan |A naplófájl kiürítésének TimeSpan másodpercben.
IH/iothubprotocol |A központtal való kommunikációhoz használandó protokoll. Megengedett értékek: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, Amqp, Mqtt, TCP, WebSocket, any
HB/heartbeatinterval |A közzétevő ezt az alapértelmezett értékként használja másodpercben a szívverési intervallum beállításának szívverési intervallum-beállítása nélkül.
ot/OperationTimeout |A közzétevő OPC UA-ügyfél műveletének időtúllépése az MS-ban.
ol/opcmaxstringlen |A sztring típusú OPC-k maximális hossza képes továbbítani/fogadni.
Oi/opcsamplinginterval |Az alapértelmezett érték ezredmásodpercben a kiszolgálók mintavételi értékekre való igényléséhez
op/opcpublishinginterval |Az alapértelmezett érték ezredmásodpercben az OPC UA-kiszolgálón lévő előfizetések közzétételi intervallumának beállításához.
CT/createsessiontimeout |Az az intervallum, ameddig a közzétevő üzenetet küld, másodpercek alatt megőrzi azokat az OPC-kiszolgálókat a végpontokon, amelyekhez csatlakoztatva van.
kt/keepalivethresholt |A munkamenet leválasztása előtt a kiszolgáló által kihagyható életben tartási csomagok számának meghatározása.
TM/trustmyself |A közzétevői tanúsítvány automatikusan bekerül a megbízható tárolóba.
at/appcertstoretype |A saját alkalmazás-tanúsítvány tárolójának típusa (engedélyezett: könyvtár, X509Store).


## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta, hogyan módosíthatja a konfiguráció alapértelmezett értékeit, 

> [!div class="nextstepaction"]
> [IIoT-ADX beolvasása](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Az Time Series Insights használatával megjelenítheti és elemezheti az adataikat](tutorial-visualize-data-time-series-insights.md)
