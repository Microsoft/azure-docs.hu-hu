---
title: Biztonsági riasztások és incidensek Azure Security Center
description: Ismerje meg, hogyan hozza létre a Azure Security Center biztonsági riasztásokat, és korrelálja őket incidensekkel.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: memildin
ms.openlocfilehash: 5824457a9f2ce411a871b5e76802ecf2e2f106c3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099505"
---
# <a name="security-alerts-and-incidents-in-azure-security-center"></a>Biztonsági riasztások és incidensek Azure Security Center

A Security Center riasztásokat hoz létre az Azure-beli, helyszíni és hibrid felhőalapú környezetekben üzembe helyezett erőforrásokhoz.

A biztonsági riasztásokat a speciális észlelések váltották ki, és csak az Azure Defender esetében érhetők el. A **díjszabási & beállítások** oldaláról a gyors útmutató [: az Azure Defender engedélyezése](enable-azure-defender.md)című témakörben leírtak szerint lehet frissíteni. Ingyenes 30 napos próbaverzió érhető el. A díjszabással kapcsolatos részletekért a választott pénznemben és a régiója szerint tekintse meg a [Security Center díjszabását](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>Mik azok a biztonsági riasztások és biztonsági incidensek? 

A **riasztások** a Security Center által generált értesítések, amikor észlelik a fenyegetéseket az erőforrásokon. A Security Center rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is felkínál a támadás elhárítására.

**A biztonsági incidensek** a kapcsolódó riasztások gyűjteményei, az egyes riasztások egyenkénti listázása helyett. A Security Center a [Felhőbeli intelligens riasztások korrelációját](#cloud-smart-alert-correlation-in-azure-security-center-incidents) használja a különböző riasztások és az alacsony megbízhatósági jelzések biztonsági incidensekre való összekapcsolásához.

Az incidensek használatával a Security Center egyetlen nézetet biztosít a támadási kampányokról és az összes kapcsolódó riasztásról. Ez a nézet lehetővé teszi, hogy gyorsan megértse, milyen műveleteket tartott a támadó, és milyen erőforrásokat érintettek.

## <a name="respond-to-todays-threats"></a>Válasz a mai fenyegetésekre

Az elmúlt 20 évben jelentős változásokat figyelhettünk meg a fenyegetések területén. A múltban a vállalatoknak általában csak azért kellett aggódniuk az egyes támadók számára, akik leginkább érdeklik a "mit tehetnek". A mai támadók sokkal kifinomultabbak és megrendezettek. Gyakran konkrét pénzügyi és stratégiai célokat követnek. Emellett több erőforrás is elérhető számukra, mivel ezek a Nemzetek vagy a szervezett bűnözés által finanszírozva lehetnek.

Ezek a változó realitások a támadó soraiban példátlan szintű professzionalizmust eredményeztek. Már nem a webhelyek megrongálása érdekli őket. Most már érdeklik az információk, a pénzügyi fiókok és a személyes adatok ellopása, amelyek segítségével készpénzt hozhatnak a nyílt piacon, vagy egy adott üzleti, politikai vagy katonai pozíciót is kihasználhatnak. Még nagyobb aggodalomra ad okot, hogy a pénzügyi célokat követő támadókon kívül olyanokkal is számolni kell, akik azért törnek be a hálózatokba, hogy kárt okozzanak az infrastruktúrában és a felhasználóknak.

Erre reagálva a szervezetek gyakran olyan adott pontokon működő megoldásokat alkalmaznak, amelyek a vállalat peremsávját vagy végpontjait védelmezik a támadások ismert jelei után kutatva. Ezek a megoldások általában nagy mennyiségű, alacsony megbízhatósági szintű riasztást eredményeznek, és ezeket a biztonsági elemzőknek szét kell válogatniuk és ki kell vizsgálniuk. A legtöbb szervezetnek nincs ideje és megfelelő szaktudása ahhoz, hogy reagáljon ezekre a riasztásokra, ezért sok probléma megoldatlan marad.  

Emellett a támadók kifejlődték a módszereiket számos aláírás-alapú védelem kialakulásához, és [alkalmazkodnak a felhőalapú környezetekhez](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Az új módszerek a fenyegetések egyre bővülő körének gyorsabb felismerését, azonnali észlelését és kiiktatását igénylik.


## <a name="continuous-monitoring-and-assessments"></a>Folyamatos monitorozás és értékelések

Azure Security Center a biztonsági kutatási és adatelemzési csapatokból származó előnyöket a Microsoftnál, akik folyamatosan figyelik a fenyegetések tájképének változásait. Ide tartoznak a következők:

* A fenyegetések **felderítésének figyelése**: a fenyegetések felderítése magában foglalja a meglévő vagy újonnan felmerülő fenyegetésekkel kapcsolatos mechanizmusokat, indikátorokat, következményeket és gyakorlati tanácsokat. Ez az információ elérhető a biztonsági közösség számára, és a Microsoft folyamatosan figyeli a fenyegetésekre vonatkozó intelligencia belső és külső forrásból származó hírcsatornáit.
* A **jelek megosztása**: a Microsoft széles körben elérhető Felhőbeli és helyszíni szolgáltatások, kiszolgálók és ügyfél-végponti eszközökről származó biztonsági csapatokkal kapcsolatos elemzések megoszthatók és elemezhetők.
* **A Microsoft biztonsági szakértői**: folyamatos kapcsolatot tartunk a Microsoft csapataival, amelyek tagjai meghatározott biztonsági szakterületekkel foglalkoznak, például igazságügyi szakértői tevékenységekkel és a webes támadások észlelésével.
* **Az észlelés finomhangolása**: a biztonsági szakértők az ügyfelek valós adatkészletein futtatják az algoritmusokat, és az ügyfelekkel együttműködve ellenőrzik az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az összetett erőfeszítések új és továbbfejlesztett észleléseket eredményeznek, amelyekkel azonnal kihasználhatja a műveletet.

## <a name="how-does-security-center-detect-threats"></a>Hogyan ismeri fel Security Center a fenyegetéseket? <a name="detect-threats"> </a>

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. A felhőben és a helyszínen a globális jelenlétük miatt a telemetria expanzív készletét is elérjük. Az adatkészletek széles körű és változatos gyűjteménye lehetővé teszi, hogy felfedezzék az új támadási mintákat és trendeket a helyszíni fogyasztói és nagyvállalati termékek, valamint a online szolgáltatások között. Ennek eredményeképpen a Security Center gyorsan tudja frissíteni az észlelési algoritmusait, igazodva a támadók újabb és egyre összetettebb biztonságirés-kihasználási megoldásaihoz. Ez a módszer segít lépést tartani a fenyegetések gyors ütemben növekvő körével.

A valós fenyegetések észlelése és a téves pozitív állapotok csökkentése érdekében Security Center gyűjti, elemzi és integrálja az Azure-erőforrások és a hálózat naplófájljait. Együttműködik továbbá a csatlakoztatott partneri megoldásokkal, például a tűzfal-és Endpoint Protection-megoldásokkal. Security Center elemzi ezeket az információkat, és gyakran korrelál több forrásból származó információkat a fenyegetések azonosítása érdekében.

![A Security Center adatgyűjtése és adatábrázolása](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák áttörést jelentő eredményeinek felhasználásával értékelhetők ki az események a teljes felhőalapú hálóban, és így olyan fenyegetések is észlelhetők, amelyeket manuális módszerekkel lehetetlen volna azonosítani, és lehetőség van a támadások fejlődésének előrejelzésére. Ezek a biztonsági elemzések a következők:

- **Integrált veszélyforrások felderítése**: a Microsoft óriási mennyiségű globális fenyegetési intelligenciával rendelkezik. A telemetria több forrásból, például az Azure-ból, Microsoft 365ból, a Microsoft CRM Online-ból, a Microsoft Dynamics AX-ból, a outlook.com, a MSN.com, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Center (MSRC) szolgáltatásból áramlik. A kutatók emellett a jelentős felhőalapú szolgáltatók és más harmadik féltől származó adatcsatornák között megosztott veszélyforrások felderítésére vonatkozó információkat is kapnak. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről.

- **Viselkedési elemzés**: a viselkedési elemzés olyan módszer, amely az adatelemzést és az azokhoz való összevetését ismert minták gyűjteményéhez hasonlítja össze. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt. Ezenkívül szakértő elemzők mélyrehatóan elemezték a kártékony működést a meghatározásukhoz. A Azure Security Center a működés elemzésével azonosíthatja a feltört erőforrásokat a virtuális gépek naplói, a virtuális hálózati eszközök naplói, a háló naplói és más források elemzése alapján.

- **Anomáliák észlelése**: a Azure Security Center rendellenességek észlelését is használja a fenyegetések azonosítására. A viselkedési elemzésekkel szemben (amely a nagyméretű adatkészletből származtatott ismert mintáktól függ), a rendellenességek észlelése több "személyre szabott", és a központi telepítésekre jellemző alaptervekre összpontosít. Ez a módszer gépi tanulás alkalmazásával felméri az üzemelő példányok normál tevékenységeit, majd szabályokat hoz létre az olyan rendkívüli körülmények meghatározásához, amelyek a biztonságot érintő eseményre utalhatnak.

## <a name="how-are-alerts-classified"></a>Hogyan vannak besorolva a riasztások?

A Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

> [!NOTE]
> A riasztás súlyossága másképp jelenik meg a portálon, és a 01-01-2019-es dátumot tartalmazó REST API verzióiban. Ha az API egy régebbi verzióját használja, frissítsen az alább ismertetett konzisztens élményre.

| Súlyosság          | Javasolt válasz                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Magas**          | Nagy valószínűséggel az erőforrás biztonsága sérül. Azonnal megtekintheti. Security Center a kártékony szándék és a riasztás kibocsátására szolgáló megállapítások terén is nagy megbízhatósággal rendelkezik. Például egy olyan riasztás, amely észleli egy ismert kártékony eszköz, például a Mimikatz végrehajtását, a hitelesítő adatok ellopására szolgáló közös eszköz.                                                                                                               |
| **Medium**        | Ez valószínűleg egy gyanús tevékenység, amely arra utalhat, hogy az erőforrás biztonsága sérül. Security Center az elemzésben vagy a keresésben a közepes és a rosszindulatú szándék megbízhatósága közepes és magas. Ezek általában gépi tanulás vagy anomália-alapú észlelések. Például egy rendellenes helyről érkező bejelentkezési kísérlet.                                                                                                                |
| **Alacsony**           | Ez egy jóindulatú pozitív vagy tiltott támadás lehet. Security Center nem eléggé bízik abban, hogy a szándék rosszindulatú, és a tevékenység ártalmatlan lehet. A napló törlése például olyan művelet, amely akkor fordulhat elő, ha egy támadó megpróbálja elrejteni a zeneszámokat, de sok esetben a rendszergazdák által végrehajtott rutin művelet. Security Center általában nem derül ki, ha a támadások blokkolva lettek, kivéve, ha azt javasoljuk, hogy tekintse át a következőt:. |
| **Tájékoztató** | A biztonsági incidensek részletezése során csak tájékoztató riasztások jelennek meg, vagy ha a REST API egy adott riasztási AZONOSÍTÓval használja. Az incidensek általában számos riasztásból állnak, amelyek némelyike csak tájékoztató jelleggel jelenhet meg, de a többi riasztás kontextusában érdemes lehet alaposabban megtekinteni.                                                                                                                         |  |
|                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

## <a name="export-alerts"></a>Riasztások exportálása

Számos lehetősége van a riasztások Security Centeron kívüli megtekintésére, többek között a következőkre:

- **CSV-jelentés letöltése** a riasztások irányítópultján egy egyszeri exportálás CSV-be.
- A díjszabási & beállítások **folyamatos exportálásával** konfigurálhatja a biztonsági riasztások és javaslatok adatfolyamait log Analytics munkaterületekre és Event Hubsokra. [További információ a folyamatos exportálásról](continuous-export.md)
- Az **Azure Sentinel connector** Azure Security Center az Azure sentinelbe továbbítja a biztonsági riasztásokat. [További információ a Azure Security Center és az Azure Sentinel összekapcsolásáról](../sentinel/connect-azure-security-center.md)

## <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Felhőbeli intelligens riasztás korrelációja Azure Security Centerban (incidensek)

Azure Security Center folyamatosan elemzi a hibrid Felhőbeli számítási feladatokat a fejlett Analitika és a veszélyforrások felderítésével, hogy riasztást küldjön a rosszindulatú tevékenységekről.

A veszélyforrások lefedettségének szélessége növekszik. Fontos, hogy a rendszer még a legcsekélyebb kompromisszumot is felderítse, és kihívást jelenthet a biztonsági elemzők számára a különböző riasztások osztályozása és a tényleges támadás azonosítása érdekében. Security Center segít az elemzőknek megbirkózni ezzel a riasztással. Segít diagnosztizálni a támadásokat, és a különböző riasztásokat és az alacsony megbízhatósági jeleket a biztonsági incidensekre kell összeegyeztetni.

A Fusion Analytics a technológia és az analitikus háttérrendszer, amely a Security Center incidensek kiépítésére szolgál, lehetővé téve a különböző riasztások és környezetfüggő jelek összekapcsolását. A Fusion az adott előfizetésben jelentett különböző jeleket tekinti át az erőforrásokon. A Fusion megkeresi azokat a mintákat, amelyek a támadási folyamatokat vagy a közös kontextussal kapcsolatos jeleket mutatják, és azt jelzi, hogy egyesített válasz eljárást kell használni számukra.

A Fusion Analytics a biztonsági tartományok ismereteit ötvözi a AI-vel a riasztások elemzéséhez, és felfedi az új támadási mintákat. 

Security Center kihasználja a ferde támadási mátrixot, hogy a riasztásokat a vélt szándékkal társítsa, ami segít a formalizálása biztonsági tartomány ismeretében. Emellett a támadás egyes lépéseihez összegyűjtött információk használatával Security Center kizárni a támadás lépéseit megtekintő tevékenységeket, de valójában nem.

Mivel a támadások gyakran fordulnak elő a különböző bérlők között, a Security Center az egyes előfizetésekben jelentett támadási folyamatok elemzéséhez az AI-algoritmusokat is kombinálhatja. Ezzel a technikával azonosíthatja a támadási folyamatokat az elterjedt riasztási mintázatként, ahelyett, hogy az egymáshoz kapcsolható.

Az incidensek vizsgálata során az elemzőknek gyakran további kontextusra van szükségük a fenyegetés természetével és annak enyhítésével kapcsolatos döntéshez. Például akkor is, ha hálózati rendellenesség észlelhető, anélkül, hogy meg kellene értenie, hogy mi történik a hálózaton vagy a megadott erőforrás tekintetében, nehéz megérteni, hogy milyen műveleteket kell végrehajtani a következő lépésekben. A biztonsági incidensek lehetnek például összetevők, kapcsolódó események és információk. A biztonsági incidensek számára elérhető további információk az észlelt fenyegetés típusától és a környezet konfigurációjától függően változnak. 

> [!TIP]
> A Fusion Analytics által előállítható biztonsági incidensekről szóló riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-fusion)tekintheti meg.

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Képernyőkép a biztonsági incidensek által észlelt jelentésről":::

A biztonsági incidensek kezeléséhez lásd: [biztonsági incidensek kezelése a Azure Security Centerban](security-center-incident.md).

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, milyen típusú riasztások érhetők el a Security Centerban. További információkért lásd:

- [Biztonsági riasztások az Azure-beli tevékenység naplójában](https://go.microsoft.com/fwlink/?linkid=2114113) – amellett, hogy elérhetővé válnak a Azure Portal vagy programozott módon, a biztonsági riasztások és incidensek naplózása eseményként történik az Azure-beli tevékenység naplójában
- [Azure Defender-riasztások – tájékoztató táblázat](alerts-reference.md)
- [Válaszadás a biztonsági riasztásokra](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)