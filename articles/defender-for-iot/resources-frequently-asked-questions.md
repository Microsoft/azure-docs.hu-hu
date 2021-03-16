---
title: Defender a IoT-hez – gyakori kérdések
description: Válaszok az Azure Defender IoT funkcióinak és szolgáltatásának leggyakrabban feltett kérdéseire.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: rkarlin
ms.openlocfilehash: 89da9ed378118875f0c09d3c23799349a4d093b0
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467697"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Azure Defender a IoT-hez – gyakori kérdések

Ez a cikk a Defender for IoT szolgáltatással kapcsolatos gyakori kérdések és válaszok listáját tartalmazza.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Mi az Azure egyedi Value IoT biztonsága?

A Defender for IoT lehetővé teszi a vállalatok számára, hogy a meglévő Cyber-biztonsági nézetet a teljes IoT-megoldásra terjesszek. Az Azure teljes körű áttekintést nyújt az üzleti megoldásról, amely lehetővé teszi, hogy üzleti tevékenységeit és döntéseit a vállalati biztonsági helyzet és az összegyűjtött adatok alapján tegye elérhetővé. Az Azure IoT, a Azure IoT Edge és a Azure Security Center együttes biztonsága lehetővé teszi a kívánt megoldás létrehozását a szükséges biztonsággal.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>A szervezetünk szabadalmaztatott, nem szabványos ipari protokollokat használ. Ezeket támogatja a szolgáltatás? 

A IoT készült Azure Defender átfogó protokoll-támogatást biztosít. A beágyazott protokoll támogatásán kívül a védett és egyéni protokollokat futtató eszközök, illetve a szabványostól eltérő protokollok IoT is biztonságossá tehetők. A Horizon Open Development Environment (ODE) SDK-val a fejlesztők olyan, nem ágazati beépülő modulokat hozhatnak létre, amelyek a megadott protokollok alapján dekódolják a hálózati forgalmat. A szolgáltatások elemzik a forgalmat, hogy teljes körű monitorozást, riasztást és jelentéskészítést nyújtsanak. Horizont használata:
- A láthatóság és a vezérlés kibontása anélkül, hogy új verzióra kellene frissítenie.
- Külső beépülő modulként a helyszínen fejlesztheti a védett adatokat. 
- A riasztások, események és protokollok paramétereinek honosítása.

Ez az egyedi megoldás a protokollok beépülő modulként való fejlesztéséhez, nem igényel dedikált fejlesztői csapatot vagy verziót az új protokoll támogatásához. A fejlesztők, a partnerek és az ügyfelek biztonságosan fejleszthet protokollokat, és megoszthatják a horizonton keresztüli információkat és ismereteket. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Kell-e hardveres készülékeket vásárolni a Microsoft partnereitől?
Az Azure Defender for IoT-érzékelő a [hardver specifikációs útmutatójában](./how-to-identify-required-appliances.md)leírtak szerint a hardveres specifikációk útmutatójában leírtak szerint a Microsoft partnereitől származó minősített hardvereket vásárolhat, vagy használhatja a mellékelt anyagjegyzéket, és saját maga is megvásárolhatja azokat. 

A minősítéssel rendelkező hardvereket a laborban teszteltük az illesztőprogramok stabilitása, a csomagok elejtése és a hálózat méretezése érdekében.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>A szabályozás nem teszi lehetővé, hogy a rendszer az internethez kapcsolódjon. Továbbra is használhatom a Defendert a IoT?

Igen, lehet! Az Azure Defender for IoT platform helyszíni megoldását olyan fizikai vagy virtuális érzékelő berendezésként helyezi üzembe, amely passzívan betölti a hálózati forgalmat (a SPAN, a RSPAN vagy a TAP használatával) a hálózatok elemzéséhez, felderítéséhez és folyamatos figyeléséhez, valamint az OT és a IoT. Nagyobb vállalatok esetében több érzékelő is összesítheti az adatokat egy helyszíni felügyeleti konzolra.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>A hálózatban hol kell csatlakozni a figyelési portokhoz?

Az Azure Defender for IoT érzékelő egy SPAN porthoz vagy egy hálózati KOPPINTÁShoz csatlakozik, és azonnal megkezdi az INTERNETKAPCSOLATtal rendelkező hálózati forgalom összegyűjtését a passzív (ügynök nélküli) figyelés használatával. Ez a funkció nulla hatással van az OT hálózatokra, mivel az nem az adatelérési útra van helyezve, és nem vizsgálja aktívan az eszközökön.

Például:
- Egyetlen berendezés (virtuális fizikai) lehet az Üzemirányítási rétegben, amely az összes, a réteg felé irányuló, az összes üzemi cella forgalmát átirányítja.
- Azt is megteheti, hogy megkeresi a kisméretű mini-szenzorokat az egyes üzemi cellákban, amelyek Felhőbeli vagy helyi felügyelettel rendelkeznek, amely az Üzemirányítási rétegben található. Egy másik készülék (virtuális vagy fizikai) figyelheti a forgalmat az Üzemirányítási rétegben (a SCADA, a történész vagy a MES esetében).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Hogyan hasonlítja össze a Defender a IoT a versenyt?

A IoT készült Azure Defender átfogó biztonságot nyújt az összes IoT/OT-eszközön. A **végfelhasználói szervezetek** számára a IoT készült Azure Defender ügynök nélküli, a gyors üzembe helyezést biztosító, különböző tulajdonosi eszközök és örökölt Windows rendszerű rendszerek, valamint az Azure Sentinel és más SoC-eszközök együttműködésével működik. Helyszíni vagy Azure-kapcsolattal rendelkező környezetekben is üzembe helyezhető. A **IoT-eszközökhöz** készült Azure Defender for IoT egyszerűsített ügynököket kínál az eszközök rétegbeli biztonságának új IOT/ot kezdeményezésekbe való beágyazásához.

## <a name="do-i-have-to-be-an-azure-customer"></a>Azure-ügyfélnek kell lennie?

Nem, a IoT készült Azure Defender ügynök nélküli verziójához nem szükséges Azure-ügyfélnek lennie. Ha azonban riasztásokat szeretne küldeni az Azure Sentinelnek; hálózati érzékelők kiépítése és a felhőből való állapotának monitorozása; az automatikus szoftver-és veszélyforrások elleni védelem előnyeinek kihasználása érdekében az érzékelőt az Azure-IoT Hub keresztül kell összekötnie az Azure-ban.

Az Azure Defender IoT-hez készült ügynök-alapú verziójának Azure-ügyfélnek kell lennie.

## <a name="can-i-create-my-own-alerts"></a>Létrehozhatom a saját riasztásokat?

Igen, több paraméteren alapuló egyéni riasztásokat is létrehozhat, például az IP/MAC-címet, a protokoll típusát, az osztályt, a szolgáltatást, a függvényt, a parancsot stb., valamint a hasznos adatokban szereplő egyéni címkék értékeit.  Az egyéni riasztásokkal és azok létrehozásával kapcsolatos további tudnivalókért tekintse meg az [egyéni riasztások létrehozása](quickstart-create-custom-alerts.md) című témakört.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Mi történik, ha az internetkapcsolat leáll?

Az érzékelők és ügynökök továbbra is futtatják és tárolják az adataikat, amíg az eszköz fut. A rendszer a biztonsági üzenet gyorsítótárában tárolja az adatmennyiséget a méret konfigurációjának megfelelően. Amikor az eszköz visszanyeri a kapcsolatot, a biztonsági üzenetek folytatják a küldést.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan kezdheti meg a Defendert a IoT-hez, tekintse meg a következő cikkeket:

- Olvassa el a Defender for IoT [áttekintését](overview.md)
- A [Előfeltételek](quickstart-system-prerequisites.md) ellenőrzése
- További információ a [IoT Defender](getting-started.md) használatába való használatáról
- [A Defender IoT biztonsági riasztások](concept-security-alerts.md) megismerése