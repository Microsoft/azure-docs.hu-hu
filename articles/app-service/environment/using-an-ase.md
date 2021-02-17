---
title: App Service Environment használata és kezelése
description: Megtudhatja, hogyan hozhat létre, tehet közzé és méretezheti alkalmazásokat egy App Service Environmentban. A cikkben szereplő általános feladatok megkeresése.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 9/22/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e8cc42500b9d8bfc507683b5cc96c7b12f95909b
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548454"
---
# <a name="use-an-app-service-environment"></a>App Service-környezet használata

Az App Service Environment (beszállítói) Azure App Service központi telepítése az ügyfél Azure Virtual Network-példányában lévő alhálózatba. A betanítás a következőkből áll:

- **Előtér**: http-vagy https-megszakítások egy app Service Environment
- **Feldolgozók**: az alkalmazásokat üzemeltető erőforrások
- **Adatbázis**: a környezetet meghatározó információk tárolására szolgál.
- **Storage**: az ügyfél által közzétett alkalmazások üzemeltetéséhez használatos

Az alkalmazások eléréséhez külső vagy belső virtuális IP-címmel (VIP) is üzembe helyezhető. A külső virtuális IP-vel való üzembe helyezést általában *külső* bevezetésnek nevezik. A belső VIP-alapú telepítést *ILB* -bevezetésnek nevezzük, mivel belső TERHELÉSELOSZTÓ (ILB) használatával működik. További információ a ILB-szolgáltatóról: ILB-betanító [létrehozása és használata][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Alkalmazás létrehozása a központban

Ha egy alkalmazást szeretne létrehozni egy kiegészítő szolgáltatásban, ugyanazt a folyamatot kell használnia, mint amikor általában létrehoz egy alkalmazást, de néhány kisebb eltéréssel. Új App Service terv létrehozásakor:

- Ahelyett, hogy egy földrajzi helyet válasszon az alkalmazás üzembe helyezéséhez, válasszon egy bevezetési helyet.
- A teljes előállítók által létrehozott App Service csomagok csak elszigetelt árképzési szinten lehetnek.

Ha nem rendelkezik beadással, a [létrehozásához kövesse az App Service Environment létrehozása][MakeExternalASE]című témakör utasításait.

Alkalmazás létrehozása a kiegészítőben:

1. Válassza **az erőforrás létrehozása**  >  **web és mobil**  >  **webalkalmazás** lehetőséget.

1. Adja meg az alkalmazás nevét. Ha már kiválasztott egy App Service-csomagot a központhoz, az alkalmazás tartományneve a kiegészítő csomag tartománynevét tükrözi:

    ![Alkalmazás nevének kiválasztása][1]

1. Válasszon egy előfizetést.

1. Adja meg az új erőforráscsoport nevét, vagy válassza a **meglévő használata** lehetőséget, és válasszon egyet a legördülő listából.

1. Válassza ki az operációs rendszert.

1. Válasszon ki egy meglévő App Service-csomagot a központból, vagy hozzon létre egy újat a következő lépések végrehajtásával:

    a. A Azure Portal bal oldali menüben válassza az **erőforrás létrehozása > webalkalmazás** lehetőséget.

    b. Válassza ki az előfizetést.

    c. Válassza ki vagy hozza létre az erőforráscsoportot.

    d. Adja meg a webalkalmazás nevét.

    e. Válassza ki a **kód** -vagy **DockerContainer**.

    f. Válasszon futtatókörnyezetet.

    : Válassza a **Linux** vagy a **Windows** lehetőséget. 

    h. Válassza ki a központot a **régió** legördülő listából. 

    i. Válasszon ki vagy hozzon létre egy új App Service csomagot. Ha új App Service tervet hoz létre, válassza ki a megfelelő **elkülönített** SKU-méretet.

    ![Elkülönített árképzési szintek][2]

    > [!NOTE]
    > A Linux-alkalmazások és a Windows-alkalmazások nem lehetnek ugyanabban a App Servicei csomagban, de ugyanabban a App Service Environmentban lehetnek.
    >

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, győződjön meg arról, hogy az adatok helyesek, majd válassza a **Létrehozás** lehetőséget.

## <a name="how-scale-works"></a>A skálázás működése

Minden App Service alkalmazás egy App Service tervben fut. App Service környezetek App Service terveket tartanak fenn, és a App Service csomagok megtartják az alkalmazásokat. Az alkalmazások skálázásakor a App Service tervet és az ugyanazon a csomagban található összes alkalmazást is át kell méreteznie.

App Service csomag skálázásakor a rendszer automatikusan hozzáadja a szükséges infrastruktúrát. Az infrastruktúra hozzáadásakor idő telik el a műveletek méretezése során. Ha több méretezési műveletet hajt végre egymás után, a rendszer az első infrastruktúra-méretezési kérelmet is végrehajtja, és a többi várólistára kerül. Ha az első skálázási művelet befejeződik, a többi infrastruktúra-kérelem együtt működik. Az infrastruktúra hozzáadásakor a rendszer a App Service csomagokat rendeli hozzá a megfelelő módon. Egy új App Service terv létrehozása maga a skálázási művelet, mert további hardvert kér.

A több-bérlős App Service esetén a skálázás azonnal megtörténik, mivel az erőforrások készlete azonnal elérhetővé válik a támogatásához. Egy központhoz nem tartoznak ilyen pufferek, és az erőforrások igény szerint vannak lefoglalva.

Egy központból akár 100 példányra is méretezheti az App Service-tervet. A központból legfeljebb 201 teljes példány lehet az adott központhoz tartozó összes App Service csomagra vonatkozóan.

## <a name="ip-addresses"></a>IP-címek

A App Service hozzárendelheti a dedikált IP-címet egy alkalmazáshoz. Ez a képesség az IP-alapú SSL konfigurálása után érhető el, a következő témakörben leírtak szerint: [meglévő egyéni TLS/SSL-tanúsítvány kötése Azure app Servicehoz][ConfigureSSL]. Egy ILB-ben nem adhat hozzá IP-alapú SSL-hez használandó IP-címeket.

A külső kiegészítő szolgáltatással ugyanúgy konfigurálhatja az alkalmazáshoz tartozó IP-alapú SSL-t, mint a több-bérlős App Service. Mindig van egy tartalék cím a kiegészítőben, amely legfeljebb 30 IP-címmel rendelkezik. Minden alkalommal, amikor használja az egyiket, a rendszer egy másikat ad hozzá, hogy a címek mindig azonnal elérhetők legyenek. Egy másik IP-cím lefoglalásához késleltetés szükséges. Ez a késleltetés megakadályozza az IP-címek gyors egymásutánban való hozzáadását.

## <a name="front-end-scaling"></a>Előtér-méretezés

A App Service-csomagok méretezése után a rendszer automatikusan hozzáadja a feldolgozókat a támogatáshoz. Minden előkészítés két előtéri végponttal jön létre. Az előtér automatikusan kibővíti az összes 15 App Service csomag példányának minden készlete esetében az egyik előtéri sebességet. Ha például három App Service-csomaggal rendelkezik, amelyek mindegyike öt példányból áll, összesen 15 példánya és három előtér áll rendelkezésre. Ha összesen 30 példányra méretezi a méretezést, négy előtér áll rendelkezésre. Ez a minta a Kibővítés során folytatódik.

Az alapértelmezés szerint kiosztott előtér-végpontok száma megfelelő a mérsékelt terheléshez. Minden öt példány esetében csökkentheti az arányt az egyik előtérként. Módosíthatja az előtér-végpontok méretét is. Alapértelmezés szerint egyetlen mag van. A Azure Portal a méretet két vagy négy magot is megváltoztathatja.

Az arány vagy az előtér-méretek módosítása díjköteles. További információ: [Azure app Service díjszabása][Pricing]. Ha javítani szeretné a betöltési kapacitást, akkor a méretezési arány módosítása előtt nagyobb javulást érhet el a kétmagos előtér-végpontok között. Az előtér alapméretének módosítása a bevezetés frissítését eredményezi, és a szokásos munkaidőn kívül kell történnie.

Az előtér-erőforrások a szolgáltatáshoz tartozó HTTP/HTTPS-végpont. Az alapértelmezett előtér-konfigurációnál az előtér-memóriahasználat körülbelül 60%-kal egyenletes. A kezdeti végpontok méretezésének elsődleges oka a CPU-használat, amelyet elsődlegesen a HTTPS-forgalom vezérel.

## <a name="app-access"></a>Alkalmazás-hozzáférés

Egy külső benyújtó esetében az alkalmazás létrehozásához használt tartomány utótagja a következő: *. &lt; asename &gt; . p.azurewebsites.net*. Ha a kiegészítő szolgáltató neve _külső-_ bemutató, és a _contoso_ nevű alkalmazást üzemelteti, akkor az alábbi URL-címeken érhető el:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

További információ a külső kiegészítő szolgáltatás létrehozásáról: [app Service Environment létrehozása][MakeExternalASE].

Egy ILB-előkészítőben az alkalmazás létrehozásához használt tartomány utótagja a következő: *. &lt; asename &gt; . appserviceenvironment.net*. Ha a szolgáltató neve _ILB-kiegészítő_ , és a _contoso_ nevű alkalmazást üzemelteti, akkor az alábbi URL-címeken érhető el:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

További információ a ILB-előkészítés létrehozásáról: [ILB-bekészítés létrehozása és használata][MakeILBASE].

Az SCM URL-cím a kudu-konzol eléréséhez, illetve az alkalmazás Web Deploy használatával történő közzétételéhez használható. További információ a kudu-konzolról: [kudu-konzol a Azure app Service][Kudu]. A kudu-konzol webes felhasználói felületet biztosít a hibakereséshez, a fájlok feltöltéséhez és a fájlok szerkesztéséhez.

### <a name="dns-configuration"></a>DNS-konfiguráció 

Ha külső beadást használ, a szolgáltatón keresztül készített alkalmazások regisztrálva vannak Azure DNSban. Az alkalmazások nyilvánosan elérhetővé tételéhez nincs szükség további lépésekre. A ILB bevezetésével a saját DNS-t kell kezelnie. Ezt megteheti a saját DNS-kiszolgálójában vagy Azure DNS privát zónában.

A DNS konfigurálása a saját DNS-kiszolgálójában a ILB-alapú központtal:

1. hozzon létre egy zónát a benyújtó &lt; neve számára &gt; . appserviceenvironment.net
1. hozzon létre egy olyan rekordot az adott zónában, amely * a ILB IP-címére mutat.
1. hozzon létre egy olyan rekordot az adott zónában, amely a @-t a ILB IP-címére mutat.
1. hozzon létre egy zónát a központhoz tartozó &lt; név &gt; . appserviceenvironment.net neve: SCM
1. hozzon létre egy olyan rekordot az SCM-zónában, amely * a ILB IP-címére mutat.

A DNS konfigurálása Azure DNS privát zónában:

1. hozzon létre egy Azure DNS privát zónát, amely &lt; neve &gt; . appserviceenvironment.net
1. hozzon létre egy olyan rekordot az adott zónában, amely * a ILB IP-címére mutat.
1. hozzon létre egy olyan rekordot az adott zónában, amely a @-t a ILB IP-címére mutat.
1. hozzon létre egy olyan rekordot az adott zónában, amely a *. SCM-t a ILB IP-címére mutat.

A szolgáltatói alapértelmezett tartomány utótagjának DNS-beállításai nem korlátozzák, hogy az alkalmazások csak az adott nevek számára legyenek elérhetők. Egyéni tartománynevet az alkalmazások érvényesítése nélkül is beállíthat egy ILB-ben. Ha ezután létre szeretne hozni egy *contoso.net* nevű zónát, ezt megteheti, és rámutathat a ILB IP-címére. Az Egyéni tartománynév az alkalmazásra vonatkozó kérelmek esetében működik, de nem az SCM-helyhez. Az SCM-hely csak a *&lt; AppName &gt; . SCM &lt; -ben érhető el. asename &gt; . appserviceenvironment.net*. 

A nevű zóna *. &lt; a asename &gt; . appserviceenvironment.net* globálisan egyedi. A 2019. május előtt az ügyfelek megadhatják a ILB beadásának tartományi utótagját. Ha a *. contoso.com* -t szeretné használni a tartománynév-utótaghoz, akkor ezt megteheti, és ez magában foglalja az SCM-helyet. A modellel kapcsolatos kihívások is megtalálhatók; az alapértelmezett SSL-tanúsítvány kezelése, az egyszeri bejelentkezés hiánya az SCM-hellyel, valamint a helyettesítő tanúsítvány használatára vonatkozó követelmény. A ILB-ben használt alapértelmezett tanúsítvány-frissítési folyamat megszakadt, és az alkalmazás újraindítását is okozta. Ezeknek a problémáknak a megoldásához a ILB-bevezetési viselkedés úgy módosult, hogy a szolgáltató neve és a Microsoft tulajdonában lévő utótag alapján egy tartományi utótagot használjon. A ILB-beli beváltási viselkedés változása csak a 2019 májusi ILB-ASE érinti. A meglévő ILB-ASE továbbra is kezelni kell a bevezetési és DNS-konfigurációjuk alapértelmezett tanúsítványát.

## <a name="publishing"></a>Közzététel

A több-bérlős App Servicehoz hasonlóan a következő módszerekkel teheti közzé a szolgáltatót:

- Webes telepítés
- FTP
- Folyamatos integráció (CI)
- Drag and drop a kudu-konzolon
- IDE, például a Visual Studio, az Eclipse vagy a IntelliJ IDEA

A külső bevezetők esetében ezek a közzétételi lehetőségek mind ugyanúgy működnek. További információ: [Deployment in Azure app Service][AppDeploy].

A ILB-kiegészítő szolgáltatással a közzétételi végpontok csak a ILB érhetők el. A ILB egy magánhálózati IP-címen található a virtuális hálózatban lévő bekapcsolási alhálózaton. Ha nincs hálózati hozzáférése a ILB, akkor nem tehet közzé alkalmazásokat az adott kiegészítő csomagon. Amint azt a [ILB-előállítók létrehozása és használata][MakeILBASE]című része feljegyezte, konfigurálnia kell a DNS-t a rendszerben lévő alkalmazásokhoz. Ez a követelmény magában foglalja az SCM-végpontot. Ha a végpontok nincsenek megfelelően definiálva, akkor nem lehet közzétenni. Az ide-nek a ILB való közvetlen közzétételhez is hálózati hozzáférésre van szükségük.

További változtatások nélkül az internetalapú CI-rendszerek (például a GitHub és az Azure DevOps) nem működnek a ILB-vel, mert a közzétételi végpont nem érhető el az interneten. Engedélyezheti a közzétételt az Azure DevOps származó ILB-előállítók számára, ha telepít egy saját üzemeltetésű kiadási ügynököt a virtuális hálózatban, amely tartalmazza a ILB-javítót. Azt is megteheti, hogy olyan CI-rendszer használatát is használja, amely lekéréses modellt, például Dropbox-t használ.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Megtekintheti az alkalmazás közzétételi profiljában és az alkalmazás portál paneljén (az **Áttekintés**  >  **alapjai** és a **Tulajdonságok** területen is).

## <a name="storage"></a>Tárolás

A kiegészítő csomag 1 TB tárterülettel rendelkezik a központhoz tartozó összes alkalmazáshoz. Az elkülönített díjszabási SKU-ban App Service csomag 250 GB-os korláttal rendelkezik. Egy előállítók esetében 250 GB tárterület kerül App Service csomagba az 1 TB-os korlátig. Több App Service terv is lehet, mint négy, de az 1 TB-os korláton túl nincs több tárterület.

## <a name="logging"></a>Naplózás

Integrálhatja a beAzure Monitort a beépítés az Azure Storage-ba, az Azure Event Hubs-ra vagy a Log Analytics-re való elküldéséhez. Ezeket az elemeket a rendszer a mai napon naplózza:

| Helyzet | Üzenet |
|---------|----------|
| A Bea nem kifogástalan állapotú | A megadott beadási állapot nem kifogástalan, mert érvénytelen a virtuális hálózati konfiguráció. Ha a sérült állapot továbbra is fennáll, a rendszer felfüggeszti a bevezetés állapotát. Győződjön meg arról, hogy az itt definiált irányelvek követik: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| A bevezetési alhálózat csaknem el van szabadítva | A megadott beadási pont egy olyan alhálózaton van, amely szinte nem elegendő. {0}Fennmaradó címek vannak. A címek kimerítése után a kisegítő lehetőség nem méretezhető.  |
| A Bea teljes példány korlátja közeledik | A megadott beadási szolgáltatás a teljes körű beadási korláthoz közeledik. Jelenleg {0} a 201-példányok maximális számát tartalmazza app Service. |
| A kiegészítő szolgáltatás nem tudja elérni a függőséget | A megadott beadási lehetőség nem érhető el {0} .  Győződjön meg arról, hogy az itt definiált irányelvek követik: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| A beépítés fel van függesztve | A megadott beadási szolgáltatás fel van függesztve. A bevonási felfüggesztés oka lehet a fiók hiánya vagy az érvénytelen virtuális hálózati konfiguráció. Oldja meg a kiváltó okot, és folytassa a beszállítást, hogy továbbra is kiszolgálja a forgalmat. |
| A bevezető csomag frissítése megkezdődött | Megkezdődött a platform frissítése a megadott beadásra. Késések várhatók a skálázási műveletekben. |
| A betöltési frissítés befejeződött | Befejeződött a platform frissítése a megadott beadási csomagra. |
| A skálázási műveletek elkezdődtek | Egy App Service terv ( {0} ) megkezdte a skálázást. Kívánt állapot: {1} {2} feldolgozók.
| A skálázási műveletek befejeződtek | Egy App Service terv ( {0} ) befejezte a skálázást. Jelenlegi állapot: {1} {2} feldolgozók. |
| A skálázási műveletek sikertelenek voltak | Egy App Service-csomag ( {0} ) méretezése nem sikerült. Jelenlegi állapot: {1} {2} feldolgozók. |

A beléptetés beléptetésének engedélyezése:

1. A portálon lépjen a **diagnosztika beállítások** elemre.
1. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.
1. Adja meg a naplózási integráció nevét.
1. Válassza ki és konfigurálja a kívánt napló célhelyeit.
1. Válassza a **AppServiceEnvironmentPlatformLogs** lehetőséget.

![A beadási diagnosztikai napló beállításai][4]

Ha integrálja Log Analyticsokkal, a naplókat a beadási portál **naplók** elemének kiválasztásával, valamint a **AppServiceEnvironmentPlatformLogs**-sel kapcsolatos lekérdezés létrehozásával tekintheti meg. A rendszer csak akkor bocsát ki naplókat, ha a bevezetőnek van olyan eseménye, amely elindítja azt. Ha a kiegészítő szolgáltatás nem rendelkezik ilyen eseménnyel, akkor nem lesz napló. A Log Analytics munkaterületen található naplók gyors megjelenítéséhez hajtson végre egy méretezési műveletet a központhoz tartozó App Service-csomagok egyikével. Ezután futtathat egy lekérdezést a **AppServiceEnvironmentPlatformLogs** a naplók megtekintéséhez. 

**Riasztás létrehozása**

Ha riasztást szeretne létrehozni a naplókban, kövesse a naplók [létrehozása, megtekintése és kezelése a Azure monitor használatával című](../../azure-monitor/platform/alerts-log.md)témakör utasításait. Röviden:

* A beadási portál riasztások oldalának megnyitása
* **Új riasztási szabály** kiválasztása
* Válassza ki az erőforrást, hogy az Log Analytics munkaterület legyen
* Állítsa be a feltételt egy egyéni naplóbeli kereséssel egy olyan lekérdezés használatához, mint például: "AppServiceEnvironmentPlatformLogs | ahol a ResultDescription "megkezdődött a skálázás", vagy bármi más, amire szüksége van. Szükség szerint állítsa be a küszöbértéket. 
* Szükség szerint adjon hozzá vagy hozzon létre egy műveleti csoportot. A műveleti csoport a riasztásra adott válasz, például e-mailek küldése vagy SMS-üzenet megadása.
* Nevezze el a riasztást, és mentse.

## <a name="upgrade-preference"></a>Frissítési beállítások

Ha több ASE is rendelkezik, érdemes lehet néhány ASE frissíteni, mielőtt mások is. A **üzemeltetési Resource Manager** -objektumon belül megadhatja a **upgradePreference** értékét. A **upgradePreference** beállítását sablon, ARMClient vagy a használatával lehet konfigurálni https://resources.azure.com . A három lehetséges érték a következők:

- **Nincs**: az Azure a beadott kötegben nem fogja frissíteni a Bea-t. Ez az alapértelmezett érték.
- **Korai**: a bevezetés a app Service frissítéseinek első felében lesz frissítve.
- **Későn**: a bevezetés a app Service frissítéseinek második felében lesz frissítve.

Ha használja https://resources.azure.com , kövesse az alábbi lépéseket a **upgradePreferences** értékének beállításához:

1. Lépjen a resources.azure.com webhelyre, és jelentkezzen be az Azure-fiókjával.
1. Ugorjon végig az erőforrásokon az előfizetések \/ \[ előfizetés neve \] \/ resourceGroups \/ \[ erőforráscsoport neve \] \/ szolgáltatók \/ Microsoft. Web \/ hostingEnvironments \/ \[ \] .
1. Válassza a felül található **írás/írás** elemet.
1. Válassza a **Szerkesztés** elemet.
1. Állítsa be a **upgradePreference** a kívánt három érték egyikére.
1. Válassza a **javítás** lehetőséget.

![erőforrások Azure com-megjelenítés][5]

A **upgradePreferences** szolgáltatás a legtöbb esetben a több ASE, mivel a "korai" ASE a "késői" ASE előtt frissülni fog. Ha több ASE rendelkezik, a fejlesztési és tesztelési ASE "korai" értékre kell állítania, és az éles ASE "késő" értékűnek kell lennie.

## <a name="pricing"></a>Díjszabás

Az *elkülönített* árképzési SKU csak a ASE-hez használható. A központon belül futtatott összes App Service elkülönített díjszabási SKU-ban található. A App Service csomagok elkülönített díjai régiónként eltérőek lehetnek.

A App Service csomagok díjszabásán felül a beadási csomagra vonatkozó átalány is érvényes. Az átalány nem változik a befektetés méretével. A beosztási infrastruktúra minden 15 App Service csomag példánya esetében egy további előtér alapértelmezett skálázási sebességével fizet.

Ha az egyik előtér alapértelmezett skálázási sebessége minden 15 App Service-csomag esetében nem elég gyors, beállíthatja, hogy a rendszer milyen arányt vegyen fel az előtér-végpontok hozzáadásához, vagy az előtér méretét. Ha módosítja az arányt vagy a méretet, akkor az előtérbeli, alapértelmezés szerint nem hozzáadott előtér-magokért kell fizetnie.

Ha például a skálázási arányt 10 értékre állítja, akkor a rendszer a App Service csomagok minden 10 példányához beadja az előtért. A átalánydíjas díj minden 15 példányra kiterjed az egyik előtér skálázási sebességére. A 10-es skálázási aránnyal a 10 App Service csomag példányaihoz hozzáadott harmadik előtéri díjat kell fizetni. Nem kell fizetnie, ha 15 példányt ér el, mert azt automatikusan hozzáadták.

Ha az előtér méretét két mag fölé állítja, de nem módosítja az arányt, akkor a további magokért kell fizetnie. A rendszer két előtéri végpontot hoz létre, így még az automatikus skálázási küszöbérték alatt is, ha a méretet kétmagos előtér-végpontra növelte.

További információ: [Azure app Service díjszabása][Pricing].

## <a name="delete-an-ase"></a>Bemutató törlése

A bemutató rendszer törlése:

1. Válassza a **Törlés** lehetőséget a **app Service Environment** ablaktábla tetején.

1. Adja meg a beadási hely nevét annak megerősítéséhez, hogy törölni szeretné. Ha töröl egy beadást, akkor a benne található összes tartalmat is törli.

    ![Bemutatás törlése][3]

1. Válassza az **OK** lehetőséget.

## <a name="ase-cli"></a>KIEGÉSZÍTŐ PARANCSSORI FELÜLET

Vannak parancssori képességek a bevezetéshez.  Az az CLI-parancsok alább láthatók.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
