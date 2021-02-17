---
title: A Container instances biztonsági szempontjai
description: Javaslatok a lemezképek és a titkos kulcsok biztonságossá tételéhez a Azure Container Instanceshoz, valamint általános biztonsági megfontolások bármilyen tároló platformhoz
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: cbceeea24501bc9815cb07e1b0a054914ba8e964
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579277"
---
# <a name="security-considerations-for-azure-container-instances"></a>A Azure Container Instances biztonsági szempontjai

Ez a cikk a Azure Container Instances a Container apps futtatásához való használatának biztonsági szempontjait ismerteti. A témakörök a következők:

> [!div class="checklist"]
> * **Biztonsági javaslatok** a rendszerképek és a titkok kezeléséhez Azure Container instances
> * A tároló- **ökoszisztémára vonatkozó megfontolások** a tároló életciklusa során, bármilyen tároló platform esetében

Az üzembe helyezés biztonsági helyzetének javítására szolgáló átfogó javaslatokért tekintse meg a [Container instances Azure biztonsági alaptervét](security-baseline.md).


## <a name="security-recommendations-for-azure-container-instances"></a>A Azure Container Instances biztonsági javaslatai

### <a name="use-a-private-registry"></a>Privát beállításjegyzék használata

A tárolók egy vagy több adattárban található rendszerképekből állnak össze. Ezek a adattárak egy nyilvános beállításjegyzékbe, például a [Docker hub](https://hub.docker.com)-ba vagy egy privát beállításjegyzékbe tartozhatnak. Privát beállításjegyzék pedig például a [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), amely a helyszínen vagy virtuális magánfelhőbe is telepíthető. Használhatja továbbá a felhőalapú privát tároló beállításjegyzék-szolgáltatásait is, beleértve a [Azure Container registryt](../container-registry/container-registry-intro.md)is. 

Egy nyilvánosan elérhető tároló-rendszerkép nem garantálja A biztonságot. A tárolók képei több szoftverből állnak, és az egyes szoftveres rétegek biztonsági réseket tartalmazhatnak. A támadások fenyegetésének csökkentése érdekében a lemezképeket privát beállításjegyzékből kell tárolnia és beolvasnia, például Azure Container Registry vagy Docker megbízható beállításjegyzékből. A felügyelt privát beállításjegyzéken kívül a Azure Container Registry támogatja az egyszerű hitelesítési folyamatok Azure Active Directoryn keresztüli egyszerű [szolgáltatás-hitelesítést](../container-registry/container-registry-authentication.md) . Ez a hitelesítés szerepköralapú hozzáférést tartalmaz írásvédett (lekéréses), írási (leküldéses) és egyéb engedélyekhez.

### <a name="monitor-and-scan-container-images"></a>Tároló lemezképének monitorozása és vizsgálata

Használja ki a megoldások előnyeit a tároló-lemezképek egy privát beállításjegyzékben való vizsgálatához, és azonosítsa a lehetséges biztonsági réseket. Fontos megérteni a különböző megoldások által nyújtott veszélyforrások észlelésének mélységét.

Például Azure Container Registry opcionálisan [integrálható Azure Security Center](../security-center/defender-for-container-registries-introduction.md) a beállításjegyzékbe leküldett összes Linux-lemezkép automatikus vizsgálatához. Azure Security Center integrált Qualys képolvasó észleli a képbiztonsági réseket, osztályozza őket, és szervizelési útmutatást nyújt.

Az Azure piactéren a biztonsági monitorozási és a képvizsgálati megoldások, például a [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) és az [Aqua biztonság](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) is elérhető.  

### <a name="protect-credentials"></a>Hitelesítő adatok megóvása

A tárolók különböző fürtökön és Azure-régiókban is elterjedhetnek. Ezért a bejelentkezésekhez vagy az API-hozzáférésekhez szükséges hitelesítő adatokat, például jelszavakat vagy jogkivonatokat kell biztosítania. Győződjön meg arról, hogy csak az emelt szintű felhasználók férhetnek hozzá a tárolóhoz az átvitel és a nyugalmi állapotban. Leltárba kell venni az összes hitelesítő adatot, majd a fejlesztőknek a tároló platformokhoz tervezett, új titkokat kezelő eszközöket kell használniuk.  Győződjön meg arról, hogy a megoldás tartalmaz-e titkosított adatbázisokat, TLS-titkosítást az átvitel során, valamint a legalacsonyabb jogosultságú [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/overview.md). A [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkos kulcsoknak (például a tanúsítványoknak, a kapcsolatok karakterláncának és a jelszavaknak) a tároló alkalmazások számára. Mivel ezek az adatok bizalmasak és üzleti szempontból kritikus fontosságúak, biztonságos hozzáférést biztosítanak a kulcstartóhoz, így csak a jogosult alkalmazások és felhasználók férhetnek hozzájuk.

## <a name="considerations-for-the-container-ecosystem"></a>A tároló ökoszisztémájának szempontjai

Az alábbi, megfelelően megvalósított és felügyelt biztonsági intézkedések segíthetnek a tároló-ökoszisztémák biztonságossá tételében és védelmében. Ezek a mértékek a tároló életciklusa, a fejlesztés és az éles üzembe helyezés, valamint a tárolók, gazdagépek és platformok széles körére vonatkoznak. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>A biztonsági rések kezelése a tároló fejlesztési életciklusának részeként 

A tárolók fejlesztési életciklusa során a biztonsági rések felügyeletének használatával javíthatja a biztonsági problémák azonosítását és megoldását, mielőtt azok komolyabb problémát jelentenek. 

### <a name="scan-for-vulnerabilities"></a>Biztonsági rések keresése 

A rendszer folyamatosan észleli az új biztonsági réseket, így a biztonsági rések keresése és azonosítása folyamatos folyamat. A biztonsági rések vizsgálatának beépítése a tároló életciklusa során:

* A fejlesztési folyamat utolsó beadásakor a biztonsági rések vizsgálatát kell végrehajtani a tárolókban, mielőtt a lemezképeket nyilvános vagy privát beállításjegyzékbe szeretné elküldeni. 
* Folytassa a tároló lemezképek vizsgálatát a beállításjegyzékben, és azonosítsa azokat a hibákat, amelyek a fejlesztés során valahogy kimaradtak, és a tárolói lemezképekben használt kódban található újonnan felderített biztonsági réseket.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Biztonsági rések leképezése a tárolók futtatásához 

A tároló lemezképében azonosított biztonsági rések leképezésére van szükség, így a biztonsági problémák csökkenthetők vagy feloldhatók.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Győződjön meg arról, hogy csak a jóváhagyott lemezképek lesznek használatban a környezetben 

A tároló ökoszisztémájában elég változás és volatilitás van, anélkül, hogy az ismeretlen tárolókat is engedélyezi. Csak a jóváhagyott tárolók lemezképének engedélyezése. A nem jóváhagyott tárolók rendszerképének figyelésére és megelőzésére szolgáló eszközökkel és folyamatokkal rendelkezik. 

A támadási felület csökkentésének hatékony módja, és annak megakadályozása, hogy a fejlesztők kritikus biztonsági hibákkal irányítsák a tárolók rendszerképeinek áramlását a fejlesztési környezetbe. Előfordulhat például, hogy egy adott Linux-disztribúciót kihelyez egy alaprendszerképként, lehetőleg egy sovány (alpesi vagy CoreOS), amely az Ubuntu helyett az egyiket, az esetleges támadások felszínének csökkentése érdekében. 

A képaláírás vagy az ujjlenyomatok olyan felügyeleti láncot biztosítanak, amely lehetővé teszi a tárolók integritásának ellenőrzését. A Azure Container Registry támogatja például a Docker [tartalmi megbízhatósági](https://docs.docker.com/engine/security/trust/content_trust) modelljét, amely lehetővé teszi a képkiadók számára, hogy a beállításjegyzékbe leküldött képeket írják alá, és a felhasználók csak az aláírt lemezképek lekérését.

### <a name="permit-only-approved-registries"></a>Csak jóváhagyott kibocsátásiegység-forgalmi jegyzékek engedélyezése 

Egy bővítmény, amely biztosítja, hogy a környezet csak a jóváhagyott lemezképeket használja, csak a jóváhagyott tároló-nyilvántartások használatát engedélyezi. A jóváhagyott tároló-nyilvántartások használatának megkövetelése csökkenti a kockázatot, ha korlátozza az ismeretlen biztonsági rések vagy biztonsági problémák bevezetésének lehetséges lehetőségét. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>A rendszerképek integritásának biztosítása az életciklus során 

A biztonság kezelésének része a tároló életciklusa során a tároló lemezképek integritásának biztosítása a beállításjegyzékben, valamint azok módosítása vagy üzembe helyezése éles környezetben. 

* A biztonsági réseket, akár kisebbeket is tartalmazó rendszerképeket nem szabad éles környezetben futtatni. Ideális esetben az éles környezetben üzembe helyezett összes lemezképet egy kiválasztott privát beállításjegyzékbe kell menteni. Tartsa meg az éles rendszerképek számát, hogy azok hatékonyan felügyelhetők legyenek.

* Mivel nehezen lehet kijelölni a szoftver forrását egy nyilvánosan elérhető tároló-rendszerképből, lemezképeket építhet ki a forrásból a réteg eredetének megismerése érdekében. Ha a saját készítésű tárolórendszerképben jelentkezik valamilyen biztonsági rés, könnyebben található rá megoldás. Nyilvános rendszerképpel az ügyfeleknek meg kell találniuk egy nyilvános rendszerkép gyökerét, hogy kijavítsák vagy egy másik biztonságos rendszerképet kapjanak a közzétevőtől. 

* Az éles környezetben üzembe helyezett, alaposan beolvasott lemezképek nem garantáltan naprakészek az alkalmazás élettartama szempontjából. Biztonsági rések jelenhetnek meg a rendszerkép korábban nem ismert vagy az éles környezetben való üzembe helyezés után bevezetett rétegeiben. 

  Az éles környezetben üzembe helyezett rendszerképeket az elavult vagy nem frissített rendszerképek azonosítására használják fel rendszeresen. Előfordulhat, hogy a kék-zöld telepítési módszertant és a működés közbeni frissítési mechanizmusokat használja a tároló lemezképének állásidő nélküli frissítéséhez. A rendszerképeket az előző szakaszban ismertetett eszközök segítségével ellenőrizheti. 

* A folyamatos integrációs (CI) folyamat integrált biztonsági ellenőrzéssel gondoskodik a biztonságos rendszerképek létrehozásáról és a privát beállításjegyzékbe való leküldéséről. A CI megoldásba beépített biztonságirés-ellenőrzési funkció biztosítja, hogy az összes teszten megfelelő rendszerképek kerüljenek a privát beállításjegyzékbe, ahonnan az éles számítási feladatok üzembe helyezése történik. 

  A CI-folyamat meghibásodása biztosítja, hogy a sebezhető lemezképek ne legyenek leküldve az éles számítási feladatok üzembe helyezéséhez használt privát beállításjegyzékbe. Emellett automatizálja a rendszerképek biztonsági vizsgálatát is, ha jelentős számú kép van. Máskülönben a rendszerképek manuális vizsgálata rendkívül hosszadalmas és sok hibalehetőséget tartalmazó folyamat lenne. 

### <a name="enforce-least-privileges-in-runtime"></a>A legalacsonyabb jogosultságok betartatása futásidőben 

A minimális jogosultságok fogalma egy alapszintű biztonsági eljárás, amely a tárolók esetében is érvényes. Sebezhetőség kihasználása esetén általában a támadók hozzáférhetnek a feltört alkalmazáshoz vagy folyamathoz tartozó jogosultságokkal. Annak biztosítása, hogy a tárolók a lehető legalacsonyabb jogosultságokkal működjenek, és a feladatok elvégzéséhez szükséges hozzáférés csökkenti a kockázatot. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Csökkentse a tároló támadási felületét a szükségtelen jogosultságok eltávolításával 

A potenciális támadási felületet is csökkentheti, ha eltávolítja a nem használt vagy szükségtelen folyamatokat vagy jogosultságokat a tároló futtatókörnyezetből. A Kiemelt jogosultságú tárolók root-ként futnak. Ha egy rosszindulatú felhasználó vagy munkaterhelés kikerül egy emelt szintű tárolóba, a tároló ezt követően a rendszer gyökerét fogja futtatni.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Azok a fájlok és végrehajtható fájlok jóváhagyása, amelyek elérésére vagy futtatására jogosult a tároló 

A változók és az ismeretlenk számának csökkentése segít fenntartani a stabil és megbízható környezetet. A tárolók korlátozása, hogy csak az előjóváhagyott vagy a safelisted fájlok elérésére vagy futtatására jogosultak legyenek  

Ez sokkal könnyebben kezelhető a safelist, amikor a rendszer az elejéről implementálja. A safelist a szabályozás és kezelhetőség mértékét nyújtja, mivel megtudhatja, hogy milyen fájlokra és végrehajtható fájlokra van szükség ahhoz, hogy az alkalmazás megfelelően működjön. 

A safelist nem csupán csökkenti a támadási felületet, de megadhat egy alapkonfigurációt is, és megakadályozhatja a "zajos szomszéd" és a "Container breakout" forgatókönyvek használati eseteit. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Hálózati szegmentálás kényszerített futtatása a futó tárolók esetében  

Az egyik alhálózatban lévő tárolók védelmének biztosítása érdekében egy másik alhálózaton lévő biztonsági kockázatokkal kell fenntartani a hálózati szegmentálást (vagy a nano szegmentálást) vagy a futó tárolók közötti elkülönítést. A hálózati szegmentálás fenntartása a megfelelőségi megbízatások teljesítéséhez szükséges tárolók használata esetén is szükséges lehet.  

Az [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) partneri eszköz például automatizált megközelítést biztosít a nano-szegmentáláshoz. Az Aqua figyeli a tároló hálózati tevékenységeit futtatókörnyezetben. Ez azonosítja az összes bejövő és kimenő hálózati kapcsolatot más tárolókkal, szolgáltatásokkal, IP-címekkel és a nyilvános internettel. A nano-szegmentáció automatikusan jön létre a figyelt forgalom alapján. 

### <a name="monitor-container-activity-and-user-access"></a>Tároló tevékenység és felhasználói hozzáférés figyelése 

Más informatikai környezetekhez hasonlóan a tároló-ökoszisztémával kapcsolatos tevékenység-és felhasználói hozzáférés következetes monitorozásával gyorsan azonosíthatja a gyanús vagy kártékony tevékenységeket. Az Azure tároló-figyelési megoldásokat biztosít, többek között:

* A [tárolók Azure monitor](../azure-monitor/containers/container-insights-overview.md) figyeli az Azure Kubernetes szolgáltatásban (ak) üzemeltetett Kubernetes környezetekben üzembe helyezett számítási feladatok teljesítményét. A tárolók Azure Monitor a teljesítmény láthatóságát azáltal, hogy a mérőszámok API-n keresztül Kubernetes elérhető vezérlőket, csomópontokat és tárolókat gyűjt a memória és a processzor metrikáinak összegyűjtéséhez. 

* Az [Azure Container monitoring megoldás](../azure-monitor/containers/containers.md) segítségével egyetlen helyen tekintheti meg és kezelheti a többi Docker-és Windows-tároló gazdagépét. Például:

  * Tekintse meg a tárolók által használt parancsokat megjelenítő részletes naplózási információkat. 
  * A tárolók hibaelhárítása központi naplók megtekintésével és keresésével anélkül, hogy távolról kellene megtekintenie a Docker-vagy a Windows-gazdagépeket.  
  * Olyan tárolók keresése, amelyek zajosak lehetnek, és a gazdagépen felesleges erőforrásokat használnak fel.
  * Megtekintheti a tárolók központi CPU-, memória-, tárterület-és hálózati használati és teljesítmény-információit.  

  A megoldás támogatja a tároló-szervezőket, például a Docker Swarm, a DC/OS, a nem felügyelt Kubernetes, a Service Fabric és a Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Tároló-erőforrás figyelése tevékenység 

Figyelje az erőforrás-tevékenységeket, például a fájlokat, a hálózatot és a tárolók által használt egyéb erőforrásokat. A figyelés erőforrás-tevékenység és a felhasználás egyaránt hasznos a teljesítmény monitorozása és biztonsági mértékeként. 

[Azure monitor](../azure-monitor/overview.md) lehetővé teszi az Azure-szolgáltatások alapszintű figyelését azáltal, hogy lehetővé teszi a metrikák, a tevékenységek naplói és a diagnosztikai naplók gyűjtését. A tevékenységnaplók például elárulják, hogy mikor történt az új erőforrások létrehozása vagy módosítása. 

  Rendelkezésre állnak olyan metrikák, amelyek a virtuális gépek különböző erőforrásairól, sőt a virtuális gépen futó operációs rendszerről biztosítanak teljesítménystatisztikát. Az adatokat megtekintheti az Azure Portal valamelyik böngészőjében, és riasztásokat hozhat létre a metrikák alapján. Az Azure Monitor biztosítja a leggyorsabb metrikafolyamatot (5 perctől akár 1 percig), így az idő szempontjából kritikus fontosságú riasztásokhoz és értesítésekhez ezt a szolgáltatást érdemes használnia. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Az összes tároló rendszergazdai felhasználói hozzáférésének naplózása naplózáshoz 

A tároló ökoszisztémájának, például a Kubernetes-fürt, a tároló-beállításjegyzék és a tárolók rendszerképeinek pontos naplózási nyomvonalának fenntartása. Előfordulhat, hogy ezek a naplók naplózási célokra szükségesek, és a biztonsági incidensek után törvényszéki bizonyítékként lesznek hasznosak. Az Azure-megoldások a következők:

* Az [Azure Kubernetes szolgáltatás integrálása a Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) a fürt környezetének biztonsági konfigurációjának figyelésére és biztonsági javaslatok előállítására
* [Azure Container monitoring megoldás](../azure-monitor/containers/containers.md)
* [Azure Container instances](container-instances-log-analytics.md) és [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md) erőforrás-naplói

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Container instances Azure biztonsági alaptervét](security-baseline.md) , amely segítséget nyújt az üzembe helyezés biztonsági helyzetének javításához.

* További információ a [Azure Security Center](../security-center/container-security.md) a valós idejű veszélyforrások észleléséhez a tároló környezetekben.

* További információ a tárolók sebezhetőségének kezeléséről a [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) és az [Aqua biztonságával](https://www.aquasec.com/solutions/azure-container-security/)kapcsolatos megoldásokkal.