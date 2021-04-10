---
title: Azure Defender a IoT-ügynökhöz – gyakori kérdések
description: Válaszok az Azure Defender IoT-ügynökkel kapcsolatos leggyakrabban feltett kérdésekre.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4bd7d3c9b76dfb37e53cc51e5e16b660545cb7f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778661"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Azure Defender a IoT-ügynökhöz – gyakori kérdések

Ez a cikk a IoT-ügynök Defender szolgáltatásával kapcsolatos gyakori kérdések és válaszok listáját tartalmazza.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Telepíteni kell egy beágyazott biztonsági ügynököt?

Az ügynök telepítése a IoT-eszközökön nem kötelező, hogy a Defender IoT legyen engedélyezve. A következő három lehetőség közül választhat, és a kiválasztott beállításoknak megfelelően különböző szintű biztonsági monitorozási és felügyeleti képességeket kaphat:

- Passzív, nem invazív (ügynök nélküli) üzembe helyezés a NTA (Network Traffic Analysis) érzékelőkkel, amelyekkel a hálózatra és az eszközökre gyakorolt negatív teljesítmény negatívan befolyásolhatja a IoT/OT kockázatokat.
- Telepítse a Defender for IoT beágyazott biztonsági ügynökét módosításokkal vagy anélkül. Ez a lehetőség a legmagasabb szintű biztonsági elemzéseket biztosítja az eszközök viselkedésének és elérésének.

- Hozzon létre saját ügynököt, és implementálja a Defender for IoT biztonsági üzenet sémáját. Ez a beállítás lehetővé teszi a Defender használatát a IoT Analysis Tools számára az eszköz biztonsági ügynökén felül.

- Nincs biztonsági ügynök telepítése a IoT-eszközökön. Ez a beállítás lehetővé teszi IoT Hub kommunikáció figyelését, csökkentve a biztonsági monitorozási és felügyeleti képességeket.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Mi a IoT-ügynök Defenderje?

A Defender for IoT-ügynök az eszközök konfigurálására, viselkedésére és hozzáférésére (a konfiguráció vizsgálatával) biztosítja az eszköz szintű veszélyforrások lefedettségét, feldolgozza & a kapcsolatot. A IoT biztonsági ügynök Defender nem vizsgálja meg az üzleti tevékenységgel kapcsolatos adatait vagy tevékenységeit.

A Defender for IoT biztonsági ügynök nyílt forráskódú, és elérhető a GitHubon a 32 bites és a 64-bites Windows-és Linux-verziókon: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Mik az ügynök függőségei és előfeltételei?

A IoT Defender számos platformot támogat. Az adott eszközök támogatásának ellenőrzéséhez tekintse meg a [támogatott eszközök platformot](how-to-deploy-agent.md) .

## <a name="which-data-is-collected-by-the-agent"></a>Milyen adatokat gyűjt az ügynök?

A kapcsolat, a hozzáférés, a tűzfal konfigurációja, a folyamatok listája & az operációs rendszer alapkonfigurációját az ügynök gyűjti.

## <a name="how-much-data-will-the-agent-generate"></a>Mennyi adattal fog létrejönni az ügynök?

Az ügynök adatgenerálását az eszköz, az alkalmazás, a kapcsolat típusa és az ügyfél-ügynök konfigurációja vezérli. Az eszközök és a IoT megoldások közötti nagyfokú változékonyság miatt javasoljuk, hogy először üzembe helyezze az ügynököt egy tesztkörnyezetben vagy tesztelési beállításban, hogy megfigyelje, megismerje és beállítsa az igényeinek megfelelő egyedi konfigurációt, miközben megméri a generált adatmennyiséget. A szolgáltatás elindítása után a IoT Agent Defender az ügynök átviteli sebességének optimalizálásához nyújt operatív javaslatokat a konfiguráció és a Testreszabás folyamatának elősegítése érdekében.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Az ügynök üzenetei kvótát használnak a IoT Hubtól?

Igen. Az ügynök által továbbított adatokat a rendszer a IoT Hub kvótába számítja.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Mi a következő lépés? Telepítettem az ügynököt, és nem látok semmilyen tevékenységet vagy naplót...

1. Győződjön meg arról, hogy az [ügynök típusa megfelel az eszköz kijelölt operációsrendszer-platformjának](how-to-deploy-agent.md)

1. Ellenőrizze, hogy az [ügynök fut-e az eszközön](how-to-agent-configuration.md).

1. Győződjön meg arról, hogy a [szolgáltatás sikeresen engedélyezve lett](quickstart-onboard-iot-hub.md) a IoT hub **biztonsága** érdekében.

1. Győződjön meg arról, hogy az eszköz [konfigurálva van IoT hub a Defender for IoT modullal](quickstart-create-security-twin.md).

Ha a tevékenységek vagy naplók még nem érhetők el, további segítségért forduljon a Defender for IoT partnerhez.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Mi történik, ha az internetkapcsolat leáll?

Az érzékelők és ügynökök továbbra is futtatják és tárolják az adataikat, amíg az eszköz fut. A rendszer a biztonsági üzenet gyorsítótárában tárolja az adatmennyiséget a méret konfigurációjának megfelelően. Amikor az eszköz visszanyeri a kapcsolatot, a biztonsági üzenetek folytatják a küldést.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Hatással lehet az ügynök az eszköz vagy más telepített szoftver teljesítményére?

Az ügynök bármilyen más alkalmazásként vagy folyamatként felhasználja a gépi erőforrásokat, és nem zavarja meg a normál eszköz tevékenységeit. Az erőforrás-használat azon az eszközön, amelyen az ügynök fut, a beállítással és a konfigurációval párosul. Azt javasoljuk, hogy az ügynök konfigurációját egy foglalt környezetben tesztelje, valamint a többi IoT-alkalmazással és-funkcióval való együttműködéssel, mielőtt éles környezetben telepítené az üzembe helyezést.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Karbantartást végezek az eszközön. Ki tudom kapcsolni az ügynököt?

Az ügynök nem kapcsolható ki.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Van mód annak tesztelésére, hogy az ügynök megfelelően működik-e?

Ha az ügynök leállítja a kommunikációt, vagy nem tudja elküldeni a biztonsági üzeneteket, az **eszköz csendes** riasztást generál.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan kezdheti meg a Defendert a IoT-hez, tekintse meg a következő cikkeket:

- Olvassa el a Defender for IoT [áttekintését](overview.md)
- A [Előfeltételek](quickstart-system-prerequisites.md) ellenőrzése
- További információ a [IoT Defender](getting-started.md) használatába való használatáról
- [A Defender IoT biztonsági riasztások](concept-security-alerts.md) megismerése
