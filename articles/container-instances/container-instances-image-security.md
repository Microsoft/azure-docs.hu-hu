---
title: A tárolópéldányok biztonsági szempontjai
description: Javaslatok a rendszerképek és titkos kulcsok biztonságossá Azure Container Instances tárolóplatformok rendszerképének és általános biztonsági szempontjainak alkalmazásával kapcsolatban
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: acccd79ecd1c216f92f19d1cf035682414cd17ca
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750139"
---
# <a name="security-considerations-for-azure-container-instances"></a>Biztonsági szempontok a Azure Container Instances

Ez a cikk a tárolóalkalmazások futtatására Azure Container Instances biztonsági szempontokat mutat be. A témakörök a következők:

> [!div class="checklist"]
> * **Biztonsági javaslatok a rendszerképek** és titkos kulcsok kezeléséhez Azure Container Instances
> * **A tároló-ökoszisztéma szempontjai** a tároló életciklusa során, bármely tárolóplatformhoz

Az üzembe helyezés biztonsági javítására vonatkozó átfogó javaslatokért tekintse meg az Azure biztonsági alapkonfigurációját a [Container Instances.](security-baseline.md)


## <a name="security-recommendations-for-azure-container-instances"></a>Biztonsági javaslatok a Azure Container Instances

### <a name="use-a-private-registry"></a>Privát beállításjegyzék használata

A tárolók egy vagy több adattárban található rendszerképekből állnak össze. Ezek az adattárak egy nyilvános regisztrációs [](https://hub.docker.com)adatbázishoz, például a Docker Hub vagy egy privát regisztrációs adatbázishoz is tartoznak. Privát beállításjegyzék pedig például a [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), amely a helyszínen vagy virtuális magánfelhőbe is telepíthető. Felhőalapú privát tároló-beállításjegyzék-szolgáltatásokat is használhat, beleértve a [Azure Container Registry.](../container-registry/container-registry-intro.md) 

A nyilvánosan elérhető tároló rendszerképe nem garantálja a biztonságot. A tárolóképek több szoftverrétegből állnak, és minden szoftverrétegnek lehetnek biztonsági rései. A támadások kockázatának csökkentése érdekében a rendszerképeket egy privát regisztrációs adatbázisból kell tárolni és lekérni, például Azure Container Registry Docker Trusted Registryből. A felügyelt privát beállításjegyzék biztosítása mellett a Azure Container Registry támogatja a [szolgáltatásnév-alapú](../container-registry/container-registry-authentication.md) hitelesítést Azure Active Directory alapszintű hitelesítési folyamatokhoz. Ez a hitelesítés magában foglalja a szerepköralapú hozzáférést a csak olvasási (leküldéses), írási (leküldési) és egyéb engedélyekhez.

### <a name="monitor-and-scan-container-images"></a>Tároló rendszerképének figyelése és beolvasása

Használja ki a megoldásokat a privát regisztrációs adatbázisban található tárolóképek vizsgálatához és a potenciális biztonsági rések azonosításához. Fontos megérteni a különböző megoldások által jelentett fenyegetésészlelés mélységét.

Például a Azure Container Registry integrálható [](../security-center/defender-for-container-registries-introduction.md) a Azure Security Center a regisztrációs adatbázisba leküldhető linuxos rendszerképek automatikus vizsgálatához. Azure Security Center Qualys-ellenőrzője észleli a képek biztonsági réseit, osztályoz azokat, és szervizelési útmutatást nyújt.

Az olyan biztonsági monitorozási és képkeresési megoldások, mint a [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) és az [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) szintén elérhetők a Azure Marketplace.  

### <a name="protect-credentials"></a>Hitelesítő adatok védelme

A tárolók több fürtben és Azure-régióban is elterjednek. Ezért biztonságossá kell tegye a bejelentkezéshez vagy az API-hozzáféréshez szükséges hitelesítő adatokat, például jelszavakat vagy jogkivonatokat. Győződjön meg arról, hogy csak kiemelt jogosultságú felhasználók férhetnek hozzá ezekhez a tárolókhoz átvitel közben és nem átvitel közben. Leltáraz minden titkos adatokat, majd a fejlesztőknek új, tárolóplatformok számára kialakított titkosadat-kezelő eszközöket kell használniuk.  Győződjön meg arról, hogy a megoldás tartalmazza a titkosított adatbázisokat, az átvitel alatt található titkos kulcsok TLS-titkosítását és a legkevesebb jogosultsággal rendelkező [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC).](../role-based-access-control/overview.md) [Azure Key Vault](../key-vault/general/security-overview.md) egy felhőszolgáltatás, amely a tárolóalapú alkalmazások titkosítási kulcsait és titkos kulcsait (például tanúsítványokat, kapcsolati sztringeket és jelszavakat) védi. Mivel ezek az adatok bizalmasak és üzleti szempontból kritikus fontosságúak, biztonságos hozzáférés a kulcstartókhoz, hogy csak a jogosult alkalmazások és felhasználók férnek hozzájuk.

## <a name="considerations-for-the-container-ecosystem"></a>A tároló-ökoszisztéma szempontjai

A következő, jól megvalósított és hatékonyan felügyelt biztonsági intézkedések segíthetnek a tároló-ökoszisztéma védelmében és védelmében. Ezek a intézkedések a tárolók teljes életciklusára vonatkoznak, a fejlesztéstől az éles üzembe helyezésen át a tárolóvezénylésekig, gazdagépekig és platformokig. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Biztonsági rések kezelése a tárolófejlesztési életciklus részeként 

Ha a tárolófejlesztési életciklus során hatékony biztonságirés-kezelési megoldást használ, azzal növeli annak esélyét, hogy még azelőtt azonosít és old meg biztonsági problémákat, hogy azok komoly problémává válnak. 

### <a name="scan-for-vulnerabilities"></a>Biztonsági rések megkeresés 

Folyamatosan új biztonsági réseket fedeznek fel, ezért a biztonsági rések vizsgálata és azonosítása folyamatos folyamat. A biztonsági rések vizsgálatának beépítése a tároló életciklusa során:

* A fejlesztési folyamat utolsó ellenőrzéseként biztonsági réseket kell ellenőriznie a tárolókon, mielőtt a rendszerképeket egy nyilvános vagy privát regisztrációs adatbázisba kellene lekedni. 
* Folytassa a tárolóképek vizsgálatával a regisztrációs adatbázisban a fejlesztés során valahogyan kihagyott hibák azonosításához és az újonnan felfedezett biztonsági rések kezeléséhez, amelyek a tárolóképekben használt kódban lehetnek.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Rendszerkép biztonsági réseit leképezi futó tárolókra 

Szükség van egy olyan eszközre, amely leképezi a tárolóképekben azonosított biztonsági réseket a futó tárolókra, hogy a biztonsági problémák enyhíthetők vagy megoldhatók.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Győződjön meg arról, hogy csak jóváhagyott rendszerképeket használ a környezetben 

A tárolók ökoszisztémájában elegendő változás és változékonyság van anélkül, hogy ismeretlen tárolókat is engedélyezünk. Csak jóváhagyott tároló rendszerképek engedélyezése. Olyan eszközökkel és folyamatokkal kell rendelkezésre álló eszközökkel és folyamatokkal felhasználhatja a nem jóváhagyott tároló rendszerképeket, amelyek figyelik és megakadályozzák a használatot. 

A támadási felület hatékony csökkentésének és a fejlesztők kritikus fontosságú biztonsági hibák megelőzésének hatékony módja a tárolóképeknek a fejlesztési környezetbe való áramlásának szabályozása. Előfordulhat például, hogy egyetlen Linux-disztribúciót alapként használ, lehetőleg egy olyant, amely alapként használható (Ubuntu helyett Alpine vagy CoreOS), hogy minimálisra csökkentse a potenciális támadások felületét. 

A képek aláírása vagy ujjlenyomat-ának használata felügyeleti láncot biztosít, amely lehetővé teszi a tárolók integritásának ellenőrzését. A Azure Container Registry támogatja például a Docker [](https://docs.docker.com/engine/security/trust/content_trust) tartalom megbízhatósági modelljét, amely lehetővé teszi a rendszerkép-közzétevők számára, hogy aláírják a regisztrációs adatbázisba lekért rendszerképeket, a rendszerkép-felhasználók pedig csak aláírt rendszerképeket lekértek.

### <a name="permit-only-approved-registries"></a>Csak jóváhagyott beállításregisztrálók engedélye 

Annak biztosítása, hogy a környezet csak jóváhagyott rendszerképeket használjon, csak jóváhagyott tárolóregisztrálók használatát engedélyezi. A jóváhagyott tárolóregisztrálók használatának megkövetelve csökkenti a kockázatnak való kitettséget azáltal, hogy csökkenti az ismeretlen biztonsági rések vagy biztonsági problémák bevezető kockázatát. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>A képek integritásának biztosítása az életciklus során 

A tároló életciklusa során a biztonság kezelésének része a tároló-lemezképek integritásának biztosítása a regisztrációs adatbázisban, valamint azok módosítása vagy éles környezetben való üzembe helyezése során. 

* A biztonsági réseket, még a kisebbeket is, nem szabad éles környezetben futtatni. Ideális esetben az éles környezetben üzembe helyezett összes rendszerképet egy privát beállításjegyzékbe kell menteni, amely csak néhány számára érhető el. Az éles rendszerképek számát tartsa alacsonyan, hogy hatékonyan lehessen kezelni őket.

* Mivel nehéz egy nyilvánosan elérhető tároló rendszerképből kitűzni a szoftver eredetét, a réteg eredetével kapcsolatos ismeretek biztosításához készítsen lemezképeket a forrásból. Ha a saját készítésű tárolórendszerképben jelentkezik valamilyen biztonsági rés, könnyebben található rá megoldás. Nyilvános rendszerkép esetében az ügyfeleknek meg kell találniuk a nyilvános rendszerkép gyökerét a javításhoz, vagy egy másik biztonságos rendszerképet kell kapniuk a közzétevőtől. 

* Az éles környezetben üzembe helyezett alaposan ellenőrzött rendszerkép nem garantáltan naprakész az alkalmazás teljes élettartama során. Biztonsági rések jelenhetnek meg a rendszerkép korábban nem ismert vagy az éles környezetben való üzembe helyezés után bevezetett rétegeiben. 

  Rendszeresen naplóítsa az éles környezetben üzembe helyezett rendszerképeket, hogy azonosítsa azokat a rendszerképeket, amelyek elavultak, vagy már jó ideje nem frissültek. A kék-zöld üzembe helyezési módszerek és a működés közbeni frissítési mechanizmusok használatával állásidő nélkül frissítheti a tároló rendszerképeket. A képeket az előző szakaszban leírt eszközökkel lehet beolvasni. 

* A folyamatos integrációs (CI) folyamat és az integrált biztonsági vizsgálat használatával biztonságos rendszerképeket építhet ki, és lekullhatja őket a privát regisztrációs adatbázisba. A CI megoldásba beépített biztonságirés-ellenőrzési funkció biztosítja, hogy az összes teszten megfelelő rendszerképek kerüljenek a privát beállításjegyzékbe, ahonnan az éles számítási feladatok üzembe helyezése történik. 

  A CI-folyamat hibája biztosítja, hogy a sebezhető rendszerképek ne leküldhetők az éles számítási feladatok üzembe helyezéséhez használt privát regisztrációs adatbázisba. Emellett automatizálja a rendszerképek biztonsági vizsgálatát is, ha jelentős számú rendszerkép van. Máskülönben a rendszerképek manuális vizsgálata rendkívül hosszadalmas és sok hibalehetőséget tartalmazó folyamat lenne. 

### <a name="enforce-least-privileges-in-runtime"></a>A futtatáskor a legkevesebb jogosultság kényszeríthető ki 

A legkevesebb jogosultság fogalma egy alapvető ajánlott biztonsági eljárás, amely a tárolókra is vonatkozik. Ha kihasználnak egy biztonsági rést, az általában a feltört alkalmazás vagy folyamat jogosultságaihoz biztosít hozzáférést és jogosultságokat. Annak biztosítása, hogy a tárolók a feladat munkához szükséges legalacsonyabb jogosultságokkal és hozzáféréssel üzemelnek, csökken a kockázatnak való kitettség. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Csökkentse a tároló támadási felületét a szükségtelen jogosultságok eltávolításával 

A potenciális támadási felületet úgy is minimalizálhatja, ha eltávolítja a nem használt vagy szükségtelen folyamatokat vagy jogosultságokat a tároló-futásidőből. A kiemelt tárolók gyökérként futnak. Ha egy rosszindulatú felhasználó vagy számítási feladat egy kiemelt jogosultságú tárolóban szabadul fel, a tároló gyökérként fog futni a rendszeren.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Fájlok és végrehajtható fájlok előzetes alkalmazásával, amelyekhez a tároló hozzáférhet vagy futtatható 

A változók vagy ismeretlenek számának csökkentésével stabil, megbízható környezet tartható fenn. A tárolók korlátozása, hogy csak előre jóváhagyott vagy biztonságos fájlokat és végrehajtható fájlokat férnek hozzá vagy futtassanak, bevált módszer a kockázatoknak való kitettség korlátozására.  

Sokkal egyszerűbb a legbiztonságosabb listát kezelni, ha az elejétől kezdve van megvalósítva. A safelist (biztonságos lista) az ellenőrzés és a kezelhetőség mértéke, mivel megtudhatja, hogy milyen fájlokra és végrehajtható fájlokra van szükség ahhoz, hogy az alkalmazás megfelelően működjön. 

A biztonsági lista nem csupán csökkenti a támadási felületet, de alapkonfigurációt is biztosít az anomáliák számára, és meggátolhatja a "zajos szomszédok" és a tárolók megszakító forgatókönyvének használatát. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Hálózatszegmentálás kényszerítása futó tárolókon  

Az egyik alhálózatban található tárolók egy másik alhálózatban található biztonsági kockázatokkal szembeni védelméhez fenn kell tartani a hálózati szegmentálást (vagy nano-szegmentálást) vagy a futó tárolók közötti elkülönítést. A hálózati szegmentálás fenntartására is szükség lehet a megfelelőségi előírások teljesítéséhez szükséges iparágak tárolóihoz.  

A Tengerkék partnereszköz például [automatizált](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) megközelítést biztosít a nano-szegmentáláshoz. A tengerkék a tárolóhálózat tevékenységeit figyeli futásidőben. Azonosítja a más tárolókra, szolgáltatásokra, IP-címekre és a nyilvános internetre irányuló és onnan kimenő összes bejövő és kimenő hálózati kapcsolatot. A nanoszegmentálás automatikusan létrejön a figyelt forgalom alapján. 

### <a name="monitor-container-activity-and-user-access"></a>Tárolótevékenység és felhasználói hozzáférés figyelése 

Mint minden más it-környezetben, a gyanús vagy rosszindulatú tevékenységek gyors azonosítása érdekében rendszeresen figyelnie kell a tevékenységeket és a felhasználók hozzáférését a tároló-ökoszisztémához. Az Azure tárolófigyelési megoldásokat biztosít, többek között a következőket:

* [Azure Monitor tárolókhoz](../azure-monitor/containers/container-insights-overview.md) készült alkalmazás monitorozza a számítási feladatok teljesítményét, amelyek az Azure Kubernetes Service (AKS) által üzemeltetett Kubernetes-környezetekben vannak üzembe stb. Azure Monitor tárolókhoz való használata lehetővé teszi a teljesítmény átláthatóságát azáltal, hogy összegyűjti a Kubernetesben a Metrics API-n keresztül elérhető vezérlőkből, csomópontokból és tárolókból származó memória- és processzormetrikákat. 

* Az [Azure Container Monitoring megoldással](../azure-monitor/containers/containers.md) egyetlen helyen megtekintheti és kezelheti a többi Docker- és Windows-tároló gazdagépét. Például:

  * A tárolókhoz használt parancsokat bemutató részletes naplózási információk megtekintése. 
  * A tárolók hibaelhárítása központosított naplók megtekintésével és kereséssel anélkül, hogy távolról meg kell tekintenie a Docker- vagy Windows-gazdagépeket.  
  * Keresse meg a zajos tárolókat, amelyek felesleges erőforrásokat fogyasztanak a gazdagépen.
  * A tárolók központosított processzor-, memória-, tárolási és hálózathasználati és teljesítményinformációinak megtekintése.  

  A megoldás olyan tárolóvezénylőket támogat, mint a Docker Swarm, a DC/OS, a nemmanaged Kubernetes, a Service Fabric és a Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Tárolóerőforrás-tevékenység figyelése 

Figyelje az erőforrás-tevékenységeket, például a fájlokat, a hálózatot és a tárolók által elérhető egyéb erőforrásokat. Az erőforrás-tevékenység és -felhasználás monitorozása a teljesítményfigyelés és biztonsági intézkedésként egyaránt hasznos. 

[Azure Monitor](../azure-monitor/overview.md) lehetővé teszi az Azure-szolgáltatások alapvető monitorozását metrikák, tevékenységnaplók és diagnosztikai naplók gyűjtésével. A tevékenységnaplók például elárulják, hogy mikor történt az új erőforrások létrehozása vagy módosítása. 

  Rendelkezésre állnak olyan metrikák, amelyek a virtuális gépek különböző erőforrásairól, sőt a virtuális gépen futó operációs rendszerről biztosítanak teljesítménystatisztikát. Az adatokat megtekintheti az Azure Portal valamelyik böngészőjében, és riasztásokat hozhat létre a metrikák alapján. Az Azure Monitor biztosítja a leggyorsabb metrikafolyamatot (5 perctől akár 1 percig), így az idő szempontjából kritikus fontosságú riasztásokhoz és értesítésekhez ezt a szolgáltatást érdemes használnia. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Az összes tároló-rendszergazdai felhasználói hozzáférés naplózáshoz való naplózása 

Pontos naplót tart fenn a tároló-ökoszisztémához való rendszergazdai hozzáférésről, beleértve a Kubernetes-fürtöt, a tárolójegyzéket és a tárolórendszerképeket. Ezekre a naplókra naplózási célokra lehet szükség, és a biztonsági incidensek után törvényszéki bizonyítékként is hasznosak lehetnek. Az Azure-megoldások a következők:

* [Integráció a Azure Kubernetes Service és Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) a fürtkörnyezet biztonsági konfigurációjának figyelése és biztonsági javaslatok létrehozása érdekében
* [Azure Container Monitoring megoldás](../azure-monitor/containers/containers.md)
* Erőforrásnaplók [](container-instances-log-analytics.md) Azure Container Instances és [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Következő lépések

* Az [Azure biztonsági alapkonfigurációja Container Instances](security-baseline.md) átfogó javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági helyzetét.

* További információ [a](../security-center/container-security.md) Azure Security Center valós idejű fenyegetésészlelési módszerekről a tárolókörnyezetekben.

* További információ a tárolók sebezhetőségének [a Twistlock és](https://www.twistlock.com/solutions/microsoft-azure-container-security/) [az Aqua Security megoldásaival való kezeléséről.](https://www.aquasec.com/solutions/azure-container-security/)