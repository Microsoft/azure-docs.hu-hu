---
title: Microsoft Azure Service Fabric kapcsolatos gyakori kérdések
description: Gyakori kérdések a Service Fabricről, beleértve a képességeket, a használati eseteket és a gyakori forgatókönyveket.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 4e03ccf432852a5fc9ee700ba0e39dfe2e64fcc9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456096"
---
# <a name="commonly-asked-service-fabric-questions"></a>Service Fabric – GYIK

Számos gyakran feltett kérdés arról, hogy mit tehet, és hogyan használható a Service Fabric. Ez a dokumentum ismerteti a gyakori kérdéseket és a rájuk adott válaszokat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Fürt beállítása és felügyelete

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hogyan vissza a Service Fabric-fürt tanúsítványát?

Az alkalmazásra való frissítés visszagörgetéséhez az Service Fabric fürt kvóruma előtt a módosítás véglegesítése előtt állapot-meghibásodás észlelése szükséges. a véglegesített módosításokat csak előre lehet görgetni. A eszkalációs mérnök az ügyfél-támogatási szolgáltatásokon keresztül szükséges lehet a fürt helyreállításához, ha a nem figyelt feltörési tanúsítvány megváltozása be lett vezetve.  [Service Fabric alkalmazásának frissítése](./service-fabric-application-upgrade.md) az [alkalmazás frissítési paramétereit](./service-fabric-application-upgrade-parameters.md)alkalmazza, és nulla állásidő-frissítési ígéretet biztosít.  Az ajánlott alkalmazás-frissítési figyelt üzemmódot követve a frissítési tartományokon keresztüli automatikus előrehaladás az állapot-ellenőrzéseken alapul, az alapértelmezett szolgáltatás frissítése esetén pedig automatikusan visszagörgethető.
 
Ha a fürt továbbra is kihasználja a klasszikus tanúsítvány ujjlenyomatát a Resource Manager-sablonban, javasoljuk, hogy a modern titkok kezelési funkcióinak kihasználásához [módosítsa a fürtet a tanúsítvány ujjlenyomatáról a köznapi névre](./service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Létrehozhatok olyan fürtöt, amely több Azure-régióra vagy saját adatközpontra is kiterjed?

Igen. 

Az alapszintű Service Fabric fürtözési technológia a világ bármely pontján futó gépek összevonására használható, feltéve, hogy hálózati kapcsolattal rendelkeznek egymással. Az ilyen fürtök létrehozása és futtatása azonban bonyolult lehet.

Ha érdekli ezt a forgatókönyvet, javasoljuk, hogy vegye fel a kapcsolatot az [Service Fabric GitHub-problémák listáján](https://github.com/azure/service-fabric-issues) vagy a támogatási képviselőjén keresztül, hogy további útmutatást kapjon. Az Service Fabric csapat dolgozik a forgatókönyvhöz kapcsolódó további egyértelműség, útmutatás és javaslatok biztosításán. 

Néhány megfontolandó szempont: 

1. Az Azure-beli Service Fabric fürterőforrás a mai régió, a virtuális gépek méretezési csoportjai pedig a fürtre épülnek. Ez azt jelenti, hogy regionális meghibásodás esetén előfordulhat, hogy a Azure Resource Manager vagy a Azure Portal segítségével szeretné kezelni a fürtöt. Ez akkor is előfordulhat, ha a fürt továbbra is fut, és közvetlenül kommunikálni tud vele. Ezen kívül az Azure jelenleg nem képes egyetlen, régión belül használható virtuális hálózat kialakítására. Ez azt jelenti, hogy az Azure-ban egy többrégiós fürtnek Nyilvános IP-címek kell lennie [a virtuálisgép-méretezési csoportokban](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) vagy az [Azure VPN-átjárókban](../vpn-gateway/vpn-gateway-about-vpngateways.md)található minden virtuális géphez. Ezek a hálózatkezelési döntések eltérő hatással vannak a költségekre, a teljesítményre és a bizonyos fokú alkalmazások kialakítására, ezért alapos elemzésre és tervezésre van szükség, mielőtt felépítjük ezt a környezetet.
2. Ezeknek a gépeknek a karbantartása, kezelése és monitorozása bonyolult lehet, különösen ha a különböző _típusú_ környezetek, például a különböző felhőalapú szolgáltatók, illetve a helyszíni erőforrások és az Azure közötti átfedések. Gondoskodni kell arról, hogy a frissítés, a monitorozás, a felügyelet és a diagnosztika a fürt és az alkalmazások számára is érthető legyen, mielőtt az éles számítási feladatokat egy ilyen környezetben futtatná. Ha már rendelkezik az Azure-ban vagy a saját adatközpontokban található problémák megoldásával, akkor valószínű, hogy ugyanezek a megoldások alkalmazhatók a Service Fabric-fürt kiépítésekor vagy futtatásakor is. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Az Service Fabric-csomópontok automatikusan kapják meg az operációs rendszer frissítéseit?

A virtuálisgép- [méretezési csoport automatikus operációs rendszer rendszerképének frissítése](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) általánosan elérhető funkcióként érhető el.

Az Azure-ban nem futtatott fürtök esetében [biztosítunk egy alkalmazást](service-fabric-patch-orchestration-application.md) a Service Fabric-csomópontok alatti operációs rendszerek javításához.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Használhatok nagyméretű virtuálisgép-méretezési csoportokat az SF-fürtben? 

**Rövid válasz** – nem. 

**Hosszú válasz** – bár a nagyméretű virtuálisgép-méretezési csoportok lehetővé teszik, hogy a virtuálisgép-méretezési csoport akár 1000 VM-példányra is méretezhető legyen, így az elhelyezési csoportok (PGs) használatával végezhető el. A tartalék tartományok (tartalék) és a frissítési tartományok (frissítési-EK) csak az elhelyezési csoporton belüli szolgáltatás-hálón belül konzisztensek a tartalék és a frissítési használatával, hogy elhelyezési döntéseket hozhassanak a szolgáltatás replikái/szolgáltatási példányain. Mivel a tartalék és a frissítési csak az elhelyezési csoporton belül hasonlíthatók össze, az SF nem tudja használni azt. Ha például a VM1 a PG1-ben az FD = 0 topológiája van, a PG2 pedig az FD = 4 topológiája van, akkor nem jelenti azt, hogy a VM1 és a VM2 két különböző hardveres állványon található, ezért az SF nem tudja használni az FD-értékeket ebben az esetben az elhelyezési döntések elvégzéséhez.

A nagyméretű virtuálisgép-méretezési csoportokkal kapcsolatban más problémák is léteznek, például a 4-es szintű terheléselosztás támogatásának hiányában. A [nagyméretű méretezési csoportokkal kapcsolatos részletekért](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) lásd:



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Mi a Service Fabric-fürt minimális mérete? Miért nem lehet kisebb?

Az éles munkaterheléseket futtató Service Fabric fürtök minimálisan támogatott mérete öt csomópont. A fejlesztői forgatókönyvek esetében támogatunk egy csomópontot (a Visual Studióban a gyors fejlesztési élményhez optimalizált) és öt csomópontos fürtöt.

A következő három ok miatt a termelési fürtnek legalább öt csomóponttal kell rendelkeznie:
1. A Service Fabric-fürt állapot-nyilvántartó rendszerszolgáltatások készletét is futtatja, beleértve az elnevezési szolgáltatást és a Feladatátvevőfürt-kezelő szolgáltatást, még akkor is, ha nem fut felhasználói szolgáltatás. Ezek a rendszerszolgáltatások elengedhetetlenek ahhoz, hogy a fürt működőképes maradjon.
2. A csomópontok egy-egy replikáját mindig elhelyezjük, így a fürt mérete a szolgáltatás (valójában partíció) replikáinak felső határa lehet.
3. Mivel a fürt frissítése legalább egy csomópontot tartalmaz, legalább egy csomópont pufferét szeretnénk használni, ezért azt szeretnénk, hogy egy üzemi fürt legalább *két csomóponttal rendelkezzen a* minimálisan megengedettnél. A minimális érték egy rendszerszolgáltatás Kvórumának mérete, ahogy az alább is látható.  

Azt szeretnénk, hogy a fürt a két csomópont egyidejű meghibásodása előtt legyen elérhető. Ahhoz, hogy egy Service Fabric fürt elérhető legyen, elérhetőnek kell lennie a rendszerszolgáltatásoknak. Állapot-nyilvántartó rendszerszolgáltatások, például a névadási szolgáltatás és a Feladatátvevőfürt-kezelő szolgáltatás, amely nyomon követi, hogy a fürtön milyen szolgáltatások lettek telepítve, és hogy hol vannak jelenleg üzemeltetve, erős konzisztencia függ. Az erős konzisztencia azonban attól függ, hogy képes-e *kvórumot* beszerezni a szolgáltatások állapotára vonatkozóan, ahol a kvórum a replikák szigorú többségét jelöli (N/2 + 1) egy adott szolgáltatás esetében. Így ha a két csomópont egyidejű elvesztése miatt rugalmasan szeretnénk állni (azaz a rendszerszolgáltatás két replikájának egyidejű elvesztése), a ClusterSize-QuorumSize >= 2 értéknek kell lennie, amely a minimális méretet 5-re kényszeríti. Ennek megtekintéséhez vegye figyelembe, hogy a fürt N csomóponttal rendelkezik, és a rendszerszolgáltatások N replikái vannak – egyet az egyes csomópontokon. A rendszerszolgáltatások Kvórumának mérete (N/2 + 1). A fenti egyenlőtlenség úgy néz ki, mint N-(N/2 + 1) >= 2. Két esetet érdemes figyelembe venni: Ha N páros, és ha N páratlan. Ha N páros, mondjuk N = 2 \* m, ahol m >= 1, a megegyezőség 2 \* m-(2 \* m/2 + 1) >= 2 vagy m >= 3. Az N érték minimuma 6, amely az m = 3 értéknél érhető el. Másrészről, ha N páratlan, tegyük fel, hogy N = 2 \* m + 1, ahol m >= 1, a megegyezőség a következőhöz hasonló: 2 \* m + 1 – ((2 \* m + 1)/2 + 1) >= 2 vagy 2 \* m + 1 – (m + 1) >= 2 vagy m >= 2. Az N érték minimuma 5, amely az m = 2 értéknél érhető el. Ezért az N érték minden olyan értéke között, amely megfelel a ClusterSize – QuorumSize >= 2, a minimum 5.

Vegye figyelembe, hogy a fenti argumentumban feltételezzük, hogy minden csomóponthoz tartozik egy rendszerszolgáltatás replikája, így a rendszer a fürt csomópontjainak száma alapján számítja ki a kvórum méretét. A *TargetReplicaSetSize* módosításával azonban a kvórum mérete (N/2 + 1) kisebb lehet, ami azt a benyomást keltheti, hogy a fürt 5 csomópontnál kisebb lehet, és továbbra is 2 további csomópontja van a kvórum méretének meghaladása előtt. Például egy 4 csomópontos fürtben, ha a TargetReplicaSetSize 3 értékre állítjuk, a TargetReplicaSetSize alapuló kvórum mérete (3/2 + 1) vagy 2, ezért ClusterSize-QuorumSize = 4-2 >= 2. Azonban nem tudjuk garantálni, hogy a rendszerszolgáltatás a kvórumnál vagy fölött marad, ha a csomópontok egyidejű elvesztése egyszerre történik, előfordulhat, hogy az elveszett két csomópont két replikát futtatott, így a rendszerszolgáltatás a kvórum elvesztését eredményezi (csak egyetlen replika maradt), és elérhetetlenné válik.

Ebben a háttérben vizsgáljuk meg a lehetséges fürtkonfiguráció-konfigurációkat:

**Egy csomópont**: Ez a lehetőség nem biztosít magas rendelkezésre állást, mivel az egyetlen csomópont elvesztése bármilyen okból kifolyólag a teljes fürt elvesztését jelenti.

**Két csomópont**: két csomóponton üzembe helyezett szolgáltatás kvóruma (N = 2) 2 (2/2 + 1 = 2). Egyetlen replika elvesztése esetén nem lehet kvórumot létrehozni. A szolgáltatás frissítésének végrehajtása óta átmenetileg szükséges egy replika leállítása, ez nem hasznos konfiguráció.

**Három csomópont**: három csomóponttal (N = 3) a kvórum létrehozásának követelménye még mindig két csomópont (3/2 + 1 = 2). Ez azt jelenti, hogy elveszítheti az egyes csomópontokat, és továbbra is megtarthatja a kvórumot, de a két csomópont egyidejű meghibásodása miatt a rendszerszolgáltatások Kvórumának elvesztését eredményezik, és a fürt elérhetetlenné válik.

**Négy csomópont**: négy csomóponttal (N = 4) a kvórum létrehozásának követelménye három csomópont (4/2 + 1 = 3). Ez azt jelenti, hogy elveszítheti az egyes csomópontokat, és továbbra is megtarthatja a kvórumot, de a két csomópont egyidejű meghibásodása miatt a rendszerszolgáltatások Kvórumának elvesztését eredményezik, és a fürt elérhetetlenné válik.

**Öt csomópont**: öt csomóponttal (N = 5) a kvórum létrehozásának követelménye még mindig három csomópont (5/2 + 1 = 3). Ez azt jelenti, hogy egyszerre két csomópontot veszíthet el, és továbbra is fenntarthatja a rendszerszolgáltatások kvórumát.

Éles számítási feladatokhoz legalább két csomópont egyidejű meghibásodása esetén (például egy fürt frissítése miatt, egy másik ok miatt) rugalmasnak kell lennie, ezért öt csomópontra van szükség.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kikapcsolhatom a fürtöt éjjel/hétvégén a költségek megtakarítása érdekében?

Általában nem. Service Fabric a helyi, ideiglenes lemezeken tárolja az állapotot, ami azt jelenti, hogy ha a virtuális gépet egy másik gazdagépre helyezi át, akkor az nem helyezi át azokat. Normál működés esetén ez nem probléma, mivel az új csomópontot más csomópontok is naprakészen tekintik. Ha azonban az összes csomópontot leállítja és később újraindítja, akkor jelentős lehetőség van arra, hogy a csomópontok többsége új gazdagépeken induljon el, és a rendszer nem állítható helyre.

Ha fürtöket szeretne létrehozni az alkalmazás üzembe helyezése előtt, javasoljuk, hogy dinamikusan hozza létre ezeket a fürtöket a [folyamatos integráció/folyamatos üzembe helyezési folyamat](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)részeként.


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hogyan frissíteni az operációs rendszert (például a Windows Server 2012-ről a Windows Server 2016-re)?

Miközben fejlesztünk egy továbbfejlesztett élményt, ma már a frissítésért felelős. Az operációs rendszer rendszerképét egyszerre kell frissítenie a fürt virtuális gépei között. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Titkosítani lehet a csatlakoztatott adatlemezeket egy fürtcsomópont-típusban (virtuálisgép-méretezési csoport)?
Igen.  További információ: [fürt létrehozása csatolt adatlemezekkel](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) és [Azure Disk Encryption Virtual Machine Scale Setshoz](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Használhatok alacsony prioritású virtuális gépeket egy fürtcsomópont-típusban (virtuálisgép-méretezési csoport)?
Nem. Az alacsony prioritású virtuális gépek nem támogatottak. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Milyen címtárakat és folyamatokat kell kizárni a fürtön futó víruskereső program futtatásakor?

| **Víruskereső által kizárt könyvtárak** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (a fürt konfigurációjától) |
| FabricLogRoot (a fürt konfigurációjától) |

| **Víruskereső által kizárt folyamatok** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-key-vault-to-get-secrets"></a>Hogyan hitelesíthető az alkalmazásom Key Vault a titkok beszerzése érdekében?
Az alábbiak azt jelentik, hogy az alkalmazás hitelesítő adatokat kapjon a Key Vault hitelesítéséhez:

A. Az alkalmazások felépítési/csomagolási feladataként lekérhet egy tanúsítványt az SF alkalmazás adatcsomagjába, és ezzel hitelesítheti Key Vault.
B. A virtuálisgép-méretezési csoport MSI-kompatibilis gazdagépei esetében létrehozhat egy egyszerű PowerShell-SetupEntryPoint az SF-alkalmazáshoz, amely [hozzáférési tokent kap az MSI-végponttól](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), majd [lekéri a titkokat Key Vault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Alkalmazás kialakítása

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Mi a legjobb módszer az adatlekérdezésre egy megbízható gyűjtemény partíciói között?

A megbízható gyűjtemények általában [particionálva](service-fabric-concepts-partitioning.md) vannak a nagyobb teljesítmény és átviteli sebesség érdekében. Ez azt jelenti, hogy egy adott szolgáltatás állapota több tíz vagy több száz gép között is elterjedhet. Ha a teljes adathalmazon műveleteket hajt végre, néhány lehetőség közül választhat:

- Hozzon létre egy szolgáltatást, amely lekérdezi egy másik szolgáltatás összes partícióját, hogy lekérje a szükséges adatmennyiséget.
- Hozzon létre egy olyan szolgáltatást, amely egy másik szolgáltatás összes partíciójának adatait képes fogadni.
- Az egyes szolgáltatásokból származó adatok rendszeres leküldése egy külső tárolóba. Ez a megközelítés csak akkor megfelelő, ha a végrehajtott lekérdezések nem részei az alapvető üzleti logikájának, mivel a külső tároló adatai elavultak lesznek.
- Azt is megteheti, hogy olyan adatokat tárol, amelyeknek támogatnia kell az összes rekord lekérdezését közvetlenül egy adattárból, nem pedig megbízható gyűjteményben. Ezzel kiküszöbölheti az elavult adatmennyiséggel kapcsolatos problémát, de nem teszi lehetővé a megbízható gyűjtemények kihasználása előnyeit.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Mi a legjobb módszer a saját színészek adatai lekérdezésére?

A szereplők úgy vannak kialakítva, hogy függetlenek legyenek az államnak és a számítási feladatoknak, ezért nem ajánlott a szereplők állapotának széles körű lekérdezését végrehajtani futásidőben. Ha a színészi állapot teljes készletén kell lekérdezni, akkor a következőket kell figyelembe vennie:

- A Actor Services kicserélése állapot-nyilvántartó megbízható szolgáltatásokkal, így a hálózati kérések száma, hogy a szereplők számától származó összes adatokat a szolgáltatásban lévő partíciók száma szerint gyűjtsön.
- A résztvevők megtervezése, hogy rendszeres időközönként leküldsék az állapotukat egy külső tárolóba. A fentiek szerint ez a megközelítés csak akkor életképes, ha az éppen végrehajtott lekérdezések nem szükségesek a futásidejű működéséhez.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Mekkora mennyiségű adattárolót tárolhatok egy megbízható gyűjteményben?

A megbízható szolgáltatásokat általában particionálják, így a tárolható mennyiség csak a fürtben található gépek számától és a gépen elérhető memória mennyiségétől függ.

Tegyük fel például, hogy megbízható gyűjteménye van egy 100-partícióval és 3 replikával rendelkező szolgáltatásban, amely átlagosan 1 KB méretű objektumokat tárol. Most tegyük fel, hogy egy 10 GB memóriával rendelkező számítógép-fürttel rendelkezik. Az egyszerűség és a konzervatívság érdekében tegyük fel, hogy az operációs rendszer és a rendszerszolgáltatások, a Service Fabric futtatókörnyezet és a szolgáltatásai 6gb-t használnak, és a fürt számára a 10 GB-ot, illetve 100 GB-ot.

Ne feledje, hogy az egyes objektumokat háromszor kell tárolni (egy elsődleges és két replikát), ezért elegendő memória áll rendelkezésre a gyűjtemény körülbelül 35 000 000 objektumához, ha teljes kapacitással működik. Azt javasoljuk azonban, hogy legyen rugalmas a meghibásodási tartomány és a frissítési tartomány egyidejű elvesztése miatt, ami körülbelül 1/3 kapacitást képvisel, és a számot körülbelül 23 000 000-ra csökkentse.

Ez a számítás azt is feltételezi, hogy:

- Az adatok a partíciók közötti eloszlása nagyjából egységes, vagy a fürt erőforrás-kezelőjének betöltési mérőszámait jelenti. Alapértelmezés szerint a Service Fabric a replika száma alapján tölti be az egyenleget. Az előző példában 10 elsődleges replikát és 20 másodlagos replikát eredményezne a fürt mindegyik csomópontján. Ez jól működik olyan terhelés esetén, amely egyenletesen oszlik el a partíciók között. Ha a betöltés még nem áll fenn, be kell jelentenie a betöltést, hogy a Resource Manager több replikát is csomagoljon, és lehetővé tegye a nagyobb replikák számára, hogy több memóriát fogyasszanak egy adott csomóponton.

- A kérdéses megbízható szolgáltatás az egyetlen tárolási állapot a fürtben. Mivel több szolgáltatást is üzembe helyezhet egy fürtön, figyelembe kell vennie azokat az erőforrásokat, amelyek futtatásához és kezeléséhez szükséges.

- A fürt nem növekszik vagy csökken. Ha további gépeket ad hozzá, Service Fabric a további kapacitás kihasználása érdekében újra kiegyenlíti a replikákat, amíg a gépek száma meghaladja a szolgáltatásban lévő partíciók számát, mivel az egyes replikák nem terjedhetnek ki a gépekre. Ezzel szemben, ha csökkenti a fürt méretét a gépek eltávolításával, a replikák szorosabban vannak becsomagolva, és kevesebb általános kapacitással rendelkeznek.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Mennyi adattal tárolhatok egy színész?

A megbízható szolgáltatásokhoz hasonlóan a actoring szolgáltatásban tárolható adatmennyiséget csak a fürt csomópontjain elérhető teljes lemezterület és memória korlátozza. Az egyes szereplők azonban a leghatékonyabb, ha a kis mennyiségű állam és az ahhoz kapcsolódó üzleti logika beágyazására használják őket. Általános szabályként az egyes színészeknek kilobájtban mért állapottal kell rendelkezniük.


### <a name="where-does-azure-service-fabric-resource-provider-store-customer-data"></a>Hová tárolja az Azure Service Fabric erőforrás-szolgáltató az ügyféladatokat?

Az Azure Service Fabric erőforrás-szolgáltatója nem helyezi át vagy nem tárolja az ügyféladatokat a-ben üzembe helyezett régióból.


## <a name="other-questions"></a>Egyéb kérdések

### <a name="how-does-service-fabric-relate-to-containers"></a>Hogyan kapcsolódik Service Fabric a tárolóhoz?

A tárolók egyszerű módszert biztosítanak a szolgáltatások és a függőségek kiépítésére úgy, hogy azok minden környezetben konzisztensek legyenek, és egyetlen gépen elszigetelt módon működjenek. Service Fabric lehetővé teszik a szolgáltatások üzembe helyezését és kezelését, beleértve [a tárolóba csomagolt szolgáltatásokat](service-fabric-containers-overview.md)is.

### <a name="are-you-planning-to-open-source-service-fabric"></a>A nyílt forráskódú Service Fabrict tervezi?

Nyílt forráskódú Service Fabric ([megbízható szolgáltatások keretrendszere](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [megbízható szereplők keretrendszere](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.net Core integrációs kódtárak](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)és [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) találhatók a githubon, és közösségi hozzájárulásokat fogadhatnak ezekhez a projektekhez. 

[Nemrég bejelentettük](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) , hogy a Service Fabric futtatókörnyezet nyílt forráskódú. Ezen a ponton a GitHubon a Linux Build-és tesztelési eszközeivel is rendelkezünk a [Service Fabric](https://github.com/Microsoft/service-fabric/) -tárházban, ami azt jelenti, hogy a tárház klónozása, a Linux-alapú Service Fabric, az alapszintű tesztek futtatása, a nyílt problémák és a lekéréses kérelmek elküldése. Keményen dolgozunk a Windows-Build környezet áttelepítésének és a teljes körű CI-környezetnek a beszerzésével.

További részletekért tekintse meg a [Service Fabric blogot](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [alapszintű Service Fabric fogalmakat](service-fabric-technical-overview.md) és az [ajánlott eljárásokat](service-fabric-best-practices-overview.md)
