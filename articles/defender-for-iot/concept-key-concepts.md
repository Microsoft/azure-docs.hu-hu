---
title: Főbb előnyök
description: Ismerje meg az alapszintű Defender IoT kapcsolatos fogalmakat.
ms.date: 12/13/2020
ms.topic: article
ms.openlocfilehash: ca1e5a4d8554b208f5275fd0e7519f2db3fafc08
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784747"
---
# <a name="basic-concepts"></a>Alapfogalmak 

Ez a cikk a IoT készült Azure Defender legfontosabb előnyeit ismerteti.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Gyors, nem invazív üzembe helyezés és passzív figyelés

A Defender for IoT sensors összekapcsolja az átkapcsolási (Mirror) portokat és a hálózati csapokat, és azonnal megkezdi az IC hálózati forgalom összegyűjtését a passzív (ügynök nélküli) figyeléssel A részletes csomagok vizsgálata (DPI) a soros és az Ethernet-vezérlésű hálózati berendezések forgalmának felboncolára szolgál. A IoT Defender-nak nincs hatással az OT használó hálózatokra, mert nem az adatelérési útnak van helyezve, és nem ellenőrzi aktívan az eszközökön az OT. 

A részletes Windows-eszköz információinak azonnali pillanatképének biztosításához a Defender for IoT szenzor beállítható úgy, hogy egy opcionális aktív összetevővel kiegészítse a passzív figyelést. Ez az összetevő biztonságos, gyártó által jóváhagyott parancsokat használ a Windows-eszközök adatainak lekérdezéséhez, ahányszor vagy ritkán, ahogy szeretné.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Az ICS protokollok, eszközök és alkalmazások beágyazott ismerete

A DPI önmagában nem elegendő a protokollok rendellenességének azonosításához és az eszköz egy részletességi szinten való azonosításához. A Defender for IoT-érzékelő a legnagyobb és legbonyolultabb környezeteket is kezeli. Az összes ipari ágazatban több mint 1 300 OT-alapú hálózatot elemeztek a mai napig.

## <a name="analytics-and-self-learning-engines"></a>Elemzések és önképzési motorok

A motorok a folyamatos figyeléssel és öt olyan elemzési motorral azonosítják a biztonsági problémákat, amelyek önképzést tartalmaznak az aláírások frissítésének vagy a szabályok meghatározásának szükségessége miatt. A motorok az IC-specifikus viselkedés-elemzést és adatelemzést használják, hogy folyamatosan elemezzék az OT hálózati adatforgalmat az anomáliák számára. Az öt motor a következők:

- **Protokollok megsértésének észlelése**: az ICS protokoll specifikációit sértő csomagméret és mezőértékek használatát azonosítja.

- **Házirend megsértésének észlelése**: olyan szabályzatok megsértését azonosítja, mint például a függvények jogosulatlan használata, adott objektumokhoz való hozzáférés vagy az eszköz konfigurációjának módosítása.

- **Ipari kártevők észlelése**: az ismert kártevők, például a Conficker, a Black Energy, a Havex, a WannaCry és a NotPetya jelenlétét jelző viselkedéseket azonosítja.

- **Anomáliák észlelése**: észleli a szokatlan gépek közötti kommunikációt és viselkedést. A determinisztikus az állapotok és átmenetek kiépítésével modellezi az IC-hálózatokat, a motor egy ipari, véges állapotú modellezés (IFSM) nevű szabadalmaztatott technikát használ. A megoldás rövidebb tanulási időszakot igényel, mint az általános matematikai megközelítések vagy analitika, amelyeket eredetileg nem az OT fejlesztettek ki. Emellett a hibák gyors észlelését is észleli, minimális téves pozitív értékekkel.

- **Működési incidensek észlelése**: olyan működési problémákat azonosít, mint például az időszakos kapcsolat, amely jelzi a berendezések meghibásodásának korai jeleit.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Hálózati forgalom elemzése a kockázat-és sebezhetőségi felméréshez

Az iparágban egyedülálló, a IoT Defender a saját hálózati forgalmi elemzési (NTA) algoritmusokat használja az összes hálózati és végponti biztonsági rés passzív azonosítására, például:

- Nem engedélyezett távelérési kapcsolatok
- Gazember vagy nem dokumentált eszközök
- Gyenge hitelesítés
- Sebezhető eszközök (a foltozatlan CVEs alapján)
- Nem engedélyezett hidak az alhálózatok között
- Gyenge tűzfalszabályok

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Adatbányászat a nyomozásokhoz, a kriminalisztikaekhez és a fenyegetések vadászatához

A platform intuitív adatbányászati felületet biztosít a korábbi adatforgalom részletes kereséséhez az összes releváns dimenzión belül. Ilyenek például az időtartam, az IP-cím, a MAC-cím és a portok. A protokoll-specifikus lekérdezéseket a Function Codes, a Protocol Services és a modulok alapján is elvégezheti. A teljes hűségű PCAPs további részletezési elemzésre is használhatók.

## <a name="sensor-cloud-management-mode"></a>Érzékelő Cloud Management üzemmód

Az érzékelő Felhőbeli kezelési módja határozza meg, hogy az eszköz, a riasztás és az érzékelő által észlelt egyéb információk megjelenjenek-e.

A **felhőhöz csatlakoztatott érzékelők** esetében az érzékelő által észlelt információk az érzékelő konzolon jelennek meg. A riasztási adatokat egy IoT hub továbbítja, és más Azure-szolgáltatásokkal, például az Azure Sentinelrel is megosztható.

A **helyileg csatlakoztatott érzékelők** esetében az érzékelő által észlelt információk az érzékelő konzolján jelennek meg. Az észlelési információk a helyszíni felügyeleti konzollal is megoszthatók, ha az érzékelő csatlakoztatva van hozzá.

## <a name="air-gapped-networks"></a>Gapped hálózatok

Ha gapped-környezetben dolgozik, a Defender for IoT helyszíni felügyeleti konzolja valós idejű áttekintést nyújt a kulcsfontosságú IoT, valamint a kockázati mutatókról és a riasztásokról az összes létesítményben. Szorosan integrálva van a SOC-munkafolyamatokkal és a runbookok, így egyszerűen rangsorolhatja a kockázatcsökkentő tevékenységeket és a fenyegetések közötti összefüggéseket.  

A IoT Defender az összes eszköz összevont nézetét biztosítja. Emellett fontos információkat nyújt az eszközökről, mint például a Type (PLC, RTU, DCS stb.), a gyártó, a modell és a belső vezérlőprogram verziószáma, valamint a riasztási információk.  

A IoT Defender lehetővé teszi több üzemelő példány hatékony felügyeletét, valamint a hálózat átfogó, egységesített nézetét. A Defender for IoT optimalizálja a riasztások kezelését és az operatív hálózatok biztonságának ellenőrzését.

A helyszíni felügyeleti konzol egy webalapú felügyeleti platform, amellyel figyelheti és szabályozhatja a globális érzékelők telepítésének tevékenységeit. A telepített érzékelőktől kapott adatok kezelése mellett a helyszíni felügyeleti konzol zökkenőmentesen integrálja a különböző üzleti erőforrások adatait: CMDBs, DNS, tűzfalak, webes API-k és sok más.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="A helyszíni felügyeleti konzol megjelenítése.":::

Javasoljuk, hogy ismerkedjen meg az érzékelők által elérhető fogalmakkal, képességekkel és szolgáltatásokkal, mielőtt a helyszíni felügyeleti konzollal dolgozik.

## <a name="integrations"></a>Integráció

Kiterjesztheti a Defender IoT képességeit úgy, hogy az eszköz és a riasztási információk megosztását is megosztja a partneri rendszerekkel. Az integrációk segítenek a vállalatoknak a korábban silózott biztonsági megoldások kiépítésében, így jelentősen növelhetik az eszközök láthatóságát és az intelligenciát. Az integrációk segítségével a vállalatok felgyorsítják a rendszerszintű válaszokat, és gyorsabban csökkenthetik a kockázatokat. 

Az integrációk csökkentik a bonyolultságot, és a meglévő SOC-munkafolyamatokhoz és biztonsági verembe integrálják azokat. Például:

- SIEM-ekkel, mint például az IBM QRadar, a splunk, a ArcSight, a LogRhythm és az RSA NetWitness

- Biztonsági összehangolás és jegyeladási rendszerek, például a ServiceNow és az IBM rugalmas

- Biztonságos távelérési megoldások, például a CyberArk privilegizált munkamenet-kezelő (PSM) és a BeyondTrust

- Biztonságos hálózati hozzáférés-vezérlési (NAC) rendszerek, például az Aruba ClearPass és a Forescout ellensúlyozására

- Tűzfalak, például Fortinet és ellenőrzési pont

## <a name="complete-protocol-support"></a>Protokoll-támogatás befejezése

A beágyazott protokoll támogatásán kívül a tulajdonosi és egyéni protokollokat futtató IoT és ICS-eszközöket, illetve bármely standardtól eltérő protokollokat is biztonságossá tehet. A Horizon Open Development Environment (ODE) SDK használatával a fejlesztők olyan, nem ágazati beépülő modulokat hozhatnak létre, amelyek a megadott protokollok alapján dekódolják a hálózati forgalmat. A szolgáltatások elemzik a forgalmat, és teljes körű monitorozást, riasztást és jelentéskészítést biztosítanak. Horizont használata:

- A láthatóság és a vezérlés kibontása anélkül, hogy új verzióra kellene frissítenie.

- Gondoskodjon arról, hogy a helyszínen külső beépülő modulként fejlessze a védett adatokat.

- A riasztások, események és protokollok paramétereinek honosítása.

Emellett a saját protokollal kapcsolatos riasztásokat is használhat a kommunikációhoz:

- Tudnivalók a forgalom észleléséről protokollok és alapul szolgáló protokollok alapján egy saját horizontú beépülő modulban.

- A protokoll mezőinek kombinációja az összes protokoll rétegből. Például egy MODBUS-t futtató környezetben érdemes lehet riasztást létrehozni, ha az érzékelő írási parancsot észlel egy adott IP-cím és Ethernet-célhelyen lévő memória-regisztráláshoz. Vagy előfordulhat, hogy riasztást szeretne készíteni, ha bármely hozzáférés egy adott IP-címhez van elvégezve.

A riasztások akkor aktiválódnak, ha a horizont riasztási szabály feltételei teljesülnek.

Emellett a Horizon egyéni riasztások használata lehetővé teszi a saját riasztási címeinek és üzeneteinek megírását. A feloldott protokoll mezői és értékei beágyazható a riasztási üzenet szövegébe.

Az egyéni, a feltételen alapuló riasztások aktiválásával és üzenetkezelésével konkrét hálózati tevékenységek azonosíthatók, és hatékonyan frissíthetők a biztonsági, informatikai és operatív csapatok.


## <a name="high-availability"></a>Magas rendelkezésre állás

A magas rendelkezésre állású berendezés a helyszíni felügyeleti konzolon való telepítésével növelheti a Defender rugalmasságát a IoT üzembe helyezéséhez. A magas rendelkezésre állású központi telepítések biztosítják, hogy a felügyelt érzékelők folyamatosan jelentsenek egy aktív helyszíni felügyeleti konzolt.

Ez az üzembe helyezés egy, az elsődleges és másodlagos berendezést tartalmazó helyszíni felügyeleti konzol-párral van megvalósítva.

## <a name="localization"></a>Honosítás

Számos konzolos funkció támogatja a nyelvek széles körét.

## <a name="next-step"></a>Következő lépés

[Ismerkedés a Defender IoT-vel való használatába](getting-started.md)
