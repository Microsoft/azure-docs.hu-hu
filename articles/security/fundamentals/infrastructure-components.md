---
title: Azure Information System-összetevők és-határok
description: Ez a cikk a Microsoft Azure architektúrájának és felügyeletének általános leírását tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727201"
---
# <a name="azure-information-system-components-and-boundaries"></a>Azure Information System-összetevők és-határok
Ez a cikk az Azure architektúrájának és felügyeletének általános leírását tartalmazza. Az Azure rendszerkörnyezet a következő hálózatokból áll:

- Microsoft Azure üzemi hálózat (Azure-hálózat)
- Microsoft vállalati hálózat (Corpnet)

Az IT-csapatok elkülönítése a hálózatok működésének és karbantartásának felelőssége.

## <a name="azure-architecture"></a>Azure-architektúra
Az Azure egy felhőalapú számítástechnikai platform és infrastruktúra az alkalmazások és szolgáltatások az adatközpontok hálózatán keresztül történő létrehozásához, üzembe helyezéséhez és kezeléséhez. A Microsoft ezeket az adatközpontokat kezeli. A megadott erőforrások száma alapján az Azure erőforrás-igény alapján hoz létre virtuális gépeket (VM). Ezek a virtuális gépek a felhőben való használatra tervezett Azure hypervisoron futnak, és nem hozzáférhetők a nyilvánosság számára.

Minden egyes Azure-beli fizikai kiszolgáló csomóponton van egy olyan hypervisor, amely közvetlenül a hardveren fut. A hypervisor a csomópontot változó számú vendég virtuális gépre osztja. Mindegyik csomóponthoz egy virtuális gép is tartozik, amely futtatja a gazdagép operációs rendszerét. A Windows tűzfal minden virtuális gépen engedélyezve van. A szolgáltatás definíciós fájljának konfigurálásával megadhatja, hogy mely portok legyenek címezve. Ezek a portok kizárólag nyitottak és címezhető, belsőleg vagy külsőleg is. A hypervisor és a gyökér operációs rendszer a lemez és a hálózat összes forgalmát és elérését is elvégzi.

A gazdagép rétegében az Azure-beli virtuális gépek a legújabb Windows Server testreszabott és megerősített verzióját futtatják. Az Azure a Windows Server egy olyan verzióját használja, amely csak a virtuális gépek üzemeltetéséhez szükséges összetevőket tartalmazza. Ez javítja a teljesítményt, és csökkenti a támadási felületet. A számítógép határait a hypervisor alkalmazza, amely nem függ az operációs rendszer biztonságával.

### <a name="azure-management-by-fabric-controllers"></a>Azure-felügyelet Fabric-vezérlők alapján

Az Azure-ban a fizikai kiszolgálókon (Blades/Nodes) futó virtuális gépek a 1000-es számú fürtbe vannak csoportosítva. A virtuális gépeket a Fabric Controller (FC) nevű, kibővített és redundáns platform szoftver-összetevőtől függetlenül kezelik.

Minden FC kezeli a fürtben futó alkalmazások életciklusát, és a felügyelete alá tartozó hardver állapotának szabályozására és figyelésére szolgál. Olyan vegetatív műveleteket futtat, mint például a virtuálisgép-példányok átállítása kifogástalan állapotú kiszolgálókon, ha azt állapítja meg, hogy egy kiszolgáló meghiúsult. Az FC az alkalmazás-felügyeleti műveleteket is végrehajtja, például az alkalmazások telepítését, frissítését és horizontális felskálázását.

Az adatközpont fürtökre van osztva. A fürtök az FC szintjén elkülönítik a hibákat, és megakadályozzák, hogy bizonyos típusú hibák a fürtön kívüli kiszolgálókra is hatással legyenek. Az adott Azure-fürtöt kiszolgáló FCs egy FC-fürtbe van csoportosítva.

### <a name="hardware-inventory"></a>Hardverleltár

Az FC előkészíti az Azure hardverek és hálózati eszközök leltárát a rendszerindítási konfigurációs folyamat során. Az Azure éles környezetbe belépő új hardver-és hálózati összetevőknek a rendszerindítási konfigurációs folyamattal kell rendelkezniük. Az FC felelős a datacenter.xml konfigurációs fájlban felsorolt teljes leltár kezeléséért.

### <a name="fc-managed-operating-system-images"></a>Az FC által felügyelt operációsrendszer-lemezképek

Az operációs rendszer csapata a lemezképeket virtuális merevlemezek formájában helyezi üzembe az Azure éles környezetben lévő összes gazdagépen és vendég virtuális gépen. A csapat kiépíti ezeket az alaplemezképeket egy automatizált offline létrehozási folyamaton keresztül. Az alaprendszerkép az operációs rendszer azon verziója, amelyben a kernel és egyéb alapvető összetevők módosultak és optimalizáltak az Azure-környezet támogatásához.

A háló által felügyelt operációsrendszer-lemezképek három típusa létezik:

- Gazdagép: testreszabott operációs rendszer, amely a gazdagépen futó virtuális gépeken fut.
- Natív: olyan natív operációs rendszer, amely a bérlőn fut (például Azure Storage). Ez az operációs rendszer nem rendelkezik hypervisorsal.
- Vendég: vendég operációs rendszer, amely a vendég virtuális gépeken fut.

A gazdagép és a natív FC által felügyelt operációs rendszerek a felhőben való használatra készültek, és nem nyilvánosan elérhetők.

#### <a name="host-and-native-operating-systems"></a>Gazdagép és natív operációs rendszerek

A gazdagép és a natív a megerősített operációsrendszer-lemezképek, amelyek a háló ügynökeit futtatják, és egy számítási csomóponton futnak (a csomópont első virtuális gépeként fut) és a tárolási csomópontokon. A gazdagép és a natív optimalizált alaplemezképek használatának előnye, hogy csökkenti az API-k vagy a nem használt összetevők által elérhetővé tett felületi területet. Ezek magas biztonsági kockázatokat jelenthetnek, és növelhetik az operációs rendszer helyigényét. A csökkentett helyigényű operációs rendszerek csak az Azure-hoz szükséges összetevőket tartalmazzák.

#### <a name="guest-operating-system"></a>Vendég operációs rendszer

A vendég operációs rendszer virtuális gépein futó belső Azure-összetevők nem rendelkeznek a RDP protokoll futtatásához szükséges lehetőségekkel. Az alapkonfiguráció konfigurációs beállításainak változásainak át kell esniük a változás-és kiadás-felügyeleti folyamaton.

## <a name="azure-datacenters"></a>Azure-adatközpontok
A Microsoft Cloud Infrastructure and Operations (MCIO) csapat felügyeli az összes Microsoft-online szolgáltatások fizikai infrastruktúráját és adatközpont-szolgáltatásait. A MCIO elsődleges feladata az adatközpontok fizikai és környezeti ellenőrzésének kezelése, valamint a külső peremhálózati eszközök (például a peremhálózati útválasztók és az adatközpont-útválasztók) felügyelete és támogatása. A MCIO az adatközpontban lévő állványokon lévő minimális kiszolgálói hardverek beállítására is felelős. Az ügyfeleknek nincs közvetlen interakciójuk az Azure-ban.

## <a name="service-management-and-service-teams"></a>Service Management és Service teams
Különböző mérnöki csoportok, más néven szolgáltatási csapatok, az Azure szolgáltatás támogatásával kezelhetők. Minden szolgáltatási csapat felelős az Azure támogatásának területéről. Minden egyes szolgáltatási csapatnak elérhetővé kell tennie egy mérnök nonstop a szolgáltatásban előforduló hibák kivizsgálásához és megoldásához. A szolgáltatási csapatok alapértelmezés szerint nem rendelkeznek fizikai hozzáféréssel az Azure-ban működő hardverekhez.

A szolgáltatási csapatok a következők:

- Alkalmazás platform
- Azure Active Directory
- Azure Compute
- Azure-beli háló
- Cloud Engineering Services
- ISSD: biztonság
- Többtényezős hitelesítés
- SQL Database
- Tárolás

## <a name="types-of-users"></a>Felhasználók típusai
A Microsoft alkalmazottai (vagy alvállalkozói) belső felhasználóknak tekintendők. Minden más felhasználó külső felhasználónak számít. Az összes Azure-beli belső felhasználó alkalmazotti állapota olyan érzékenységi szint szerint van kategorizálva, amely meghatározza az ügyféladatok elérését (hozzáférés vagy nincs hozzáférés). Az Azure-ra vonatkozó felhasználói jogosultságok (hitelesítési engedély a hitelesítés után) az alábbi táblázatban olvashatók:

| Szerepkör | Belső vagy külső | Érzékenységi szint | A jogosult jogosultságok és függvények végrehajtása | Hozzáférés típusa
| --- | --- | --- | --- | --- |
| Azure Datacenter mérnök | Belső | Nincs hozzáférése a vásárlói adatszolgáltatáshoz | A helyszíni fizikai biztonság kezelése. Az adatközponton belüli és kívüli őrjáratokat hajtja végre, és figyeli az összes belépési pontot. Az adatközpontba be-és kikapcsolhatja azokat a nem törölt személyeket, akik általános szolgáltatásokat (például étkezést vagy tisztítást) biztosítanak, vagy az adatközponton belül működnek. A hálózati hardver rutinos figyelésének és karbantartásának elvégzése. Az incidensek kezelését és a hibajavítási feladatokat számos eszköz használatával végezheti el. A fizikai hardverek rutinos figyelését és karbantartását végezheti el az adatközpontokban. Hozzáférés a tulajdonos igény szerinti környezetéhez. Képes a kriminalisztikai vizsgálatok, a log incidensek jelentéseinek végrehajtására, valamint a kötelező biztonsági képzés és a házirend követelményeinek megkövetelésére. A kritikus fontosságú biztonsági eszközök, például a képolvasók és a naplók begyűjtésének működési tulajdonosa és karbantartása. | Állandó hozzáférés a környezethez. |
| Azure incidens osztályozás (gyors reagálású mérnökök) | Belső | Ügyféladatok elérése | Kezelheti a kommunikációt a MCIO, a támogatási és a mérnöki csapatok között. Az osztályozási platform incidensei, a telepítési problémák és a szolgáltatási kérelmek. | Igény szerinti hozzáférés a környezethez, korlátozott állandó hozzáférés a nem ügyfél-rendszerekhez. |
| Azure üzembe helyezési mérnökök | Belső | Ügyféladatok elérése | Platform-összetevők, szoftverek és ütemezett konfigurációs változások üzembe helyezése és frissítése az Azure-támogatásban. | Igény szerinti hozzáférés a környezethez, korlátozott állandó hozzáférés a nem ügyfél-rendszerekhez. |
| Azure-ügyfél kimaradásának támogatása (bérlő) | Belső | Ügyféladatok elérése | A platform leállásait és hibáit az egyes számítási bérlők és az Azure-fiókok hibakeresésével és diagnosztizálásával diagnosztizálhatja. Hibák elemzése. A platformon vagy az ügyfélen kritikus fontosságú javításokat hajthat, és technikai javításokat hajthat meg a támogatáson keresztül. | Igény szerinti hozzáférés a környezethez, korlátozott állandó hozzáférés a nem ügyfél-rendszerekhez. |
| Azure Live site Engineers (monitorozási mérnökök) és incidens | Belső | Ügyféladatok elérése | A platform állapotának diagnosztizálása és enyhítése diagnosztikai eszközökkel. Javíthatja a kötetek illesztőprogramjainak javításait, kihasználhatja az kimaradásokból származó elemeket, és segítheti a leállás-visszaállítási műveleteket. | Igény szerinti hozzáférés a környezethez, korlátozott állandó hozzáférés a nem ügyfél-rendszerekhez. |
|Azure-ügyfelek | Külső | N.A. | N.A. | N.A. |

Az Azure egyedi azonosítókat használ a szervezeti felhasználók és ügyfelek (vagy a szervezeti felhasználók nevében eljáró folyamatok) hitelesítéséhez. Ez az Azure-környezet részét képező összes eszközre és eszközre vonatkozik.

### <a name="azure-internal-authentication"></a>Azure belső hitelesítés

Az Azure belső összetevői közötti kommunikáció TLS-titkosítással védett. A legtöbb esetben az X. 509 tanúsítvány önaláírt. Az Azure-hálózaton kívülről elérhető kapcsolatokkal rendelkező tanúsítványok kivételt jelentenek, mint az FCs tanúsítványai. Az FCs olyan tanúsítványokkal rendelkezik, amelyeket egy megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ által támogatott Microsoft-hitelesítésszolgáltató (CA) állít ki. Ez lehetővé teszi, hogy az FC nyilvános kulcsait könnyen át lehessen tekerni. A Microsoft fejlesztői eszközei emellett az FC nyilvános kulcsait is használják. Ha a fejlesztők új alkalmazás-lemezképeket küldenek, a rendszer a lemezképeket az FC nyilvános kulcsával titkosítja a beágyazott titkok biztosítása érdekében.

### <a name="azure-hardware-device-authentication"></a>Azure hardveres eszközök hitelesítése

Az FC több hitelesítő adatot (kulcsokat és/vagy jelszavakat) tart fenn, amelyekkel a vezérlésük alá tartozó különböző hardvereszközökre lehet hitelesíteni magukat. A Microsoft egy rendszer használatával megakadályozza a hitelesítő adatokhoz való hozzáférést. A hitelesítő adatok átvitelét, megőrzését és használatát úgy alakították ki, hogy az Azure-fejlesztők, a rendszergazdák és a biztonsági mentési szolgáltatások és a személyzet bizalmas, bizalmas vagy magánjellegű információkhoz való hozzáférését megakadályozza.

A Microsoft az FC fő identitásának nyilvános kulcsán alapuló titkosítást használ. Ez az FC Setup és az FC újrakonfigurálási idején fordul elő a hálózati hardvereszközök eléréséhez használt hitelesítő adatok átviteléhez. Ha az FC-nek szüksége van a hitelesítő adatokra, az FC lekéri és visszafejti őket.

### <a name="network-devices"></a>Hálózati eszközök

Az Azure hálózati csapat úgy konfigurálja a hálózati szolgáltatás fiókokat, hogy az Azure-ügyfél hitelesítse magát a hálózati eszközökön (útválasztók, kapcsolók és terheléselosztó).

## <a name="secure-service-administration"></a>Biztonságos szolgáltatás felügyelete
Az Azure Operations munkatársai a biztonságos rendszergazdai munkaállomások (fűrészek) használatához szükségesek. Az ügyfelek az emelt szintű hozzáférési munkaállomások használatával hasonló vezérlőket valósíthat meg. A fűrészek esetében a felügyeleti személyzet egy külön hozzárendelt rendszergazdai fiókot használ, amely nem azonos a felhasználó szokásos felhasználói fiókjával. A fűrész a fiók elkülönítési gyakorlatán alapul azáltal, hogy megbízható munkaállomást biztosít ezeknek a bizalmas fiókoknak.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy mit tesz a Microsoft az Azure-infrastruktúra biztonságossá tételéhez, tekintse meg a következőt:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
