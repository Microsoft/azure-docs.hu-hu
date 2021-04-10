---
title: App Service-környezet használata
description: Megtudhatja, hogyan használhatja a App Service Environment az elkülönített alkalmazások üzemeltetéséhez.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d4cd673b5029d8379a699becd7339a265c787390
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586396"
---
# <a name="using-an-app-service-environment"></a>App Service Environment használata

A App Service Environment (bevezetési) a Azure App Service egybérlős telepítése, amely közvetlenül a választott Azure-Virtual Networkba (VNet) szúr be. Ez egy olyan rendszer, amelyet csak egy ügyfél használ. A központba telepített alkalmazások a bevezetési alhálózatra alkalmazott hálózati szolgáltatások hatálya alá esnek. Nincsenek olyan további funkciók, amelyeket engedélyezni kell az alkalmazásokban, hogy azok a hálózati szolgáltatások hatálya alá esnek. 

## <a name="create-an-app-in-an-ase"></a>Alkalmazás létrehozása a központban

Ha egy alkalmazást szeretne létrehozni egy kiegészítő szolgáltatásban, ugyanazt a folyamatot kell használnia, mint amikor általában létrehoz egy alkalmazást, de néhány kisebb eltéréssel. Új App Service terv létrehozásakor:

- Ahelyett, hogy egy földrajzi helyet válasszon az alkalmazás üzembe helyezéséhez, válasszon egy bevezetési helyet.
- A beApp Serviceban létrehozott összes terv csak elkülönített v2-es árképzési szinten lehet.

Ha nem rendelkezik beadással, a [létrehozásához kövesse az App Service Environment létrehozása][MakeASE]című témakör utasításait.

Alkalmazás létrehozása a kiegészítőben:

1. Válassza **az erőforrás létrehozása**  >  **web és mobil**  >  **webalkalmazás** lehetőséget.

1. Válasszon egy előfizetést.

1. Adja meg az új erőforráscsoport nevét, vagy válassza a **meglévő használata** lehetőséget, és válasszon egyet a legördülő listából.

1. Adja meg az alkalmazás nevét. Ha már kiválasztott egy App Service-csomagot a központhoz, az alkalmazás tartományneve a kiegészítő csomag tartománynevét tükrözi:

    ![alkalmazás létrehozása a központban][1]

1. Válassza ki a közzétételi típust, a Veremet és az operációs rendszert.

1.  Válasszon régiót. Itt ki kell választania egy korábban létező App Service Environment v3 elemet.  Az alkalmazások létrehozása során nem hozhat létre ASEv3 

1. Válasszon ki egy meglévő App Service-csomagot a beadásban, vagy hozzon létre egy újat. Új alkalmazás létrehozásakor válassza ki a App Service tervhez használni kívánt méretet. Az alkalmazáshoz csak az elkülönített v2 árképzési SKU-t lehet kijelölni.

    ![Elkülönített v2 díjszabási szintek][2]

    > [!NOTE]
    > A Linux-alkalmazások és a Windows-alkalmazások nem lehetnek ugyanabban a App Servicei csomagban, de ugyanabban a App Service Environmentban lehetnek.
    >

1. Válassza a **Tovább: figyelés**  elemet, ha az alkalmazással szeretné engedélyezni az alkalmazás-megállapításokat, itt megteheti a létrehozási folyamat során. 

1.  Válassza a Next (tovább) lehetőséget **: címkék** adja meg az alkalmazáshoz használni kívánt címkéket  

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, győződjön meg arról, hogy az adatok helyesek, majd válassza a **Létrehozás** lehetőséget.

## <a name="how-scale-works"></a>A skálázás működése

Minden App Service alkalmazás egy App Service tervben fut. App Service környezetek App Service terveket tartanak fenn, és a App Service csomagok megtartják az alkalmazásokat. Az alkalmazások skálázásakor a App Service tervet és az ugyanazon a csomagban található összes alkalmazást is át kell méreteznie.

App Service csomag skálázásakor a rendszer automatikusan hozzáadja a szükséges infrastruktúrát. Az infrastruktúra hozzáadásakor idő telik el a műveletek méretezése során. App Service terv méretezése esetén az azonos operációs rendszerre és méretre vonatkozó egyéb skálázási műveletek megvárja az első befejezést. A blokkolási skálázási művelet befejezése után az összes várólistán lévő kérelem feldolgozása egyszerre történik. Egy méretezési művelet egyetlen méretben és operációs rendszeren nem blokkolja a méret és az operációs rendszer egyéb kombinációinak méretezését. Ha például méretezést végez egy Windows I2v2 App Service tervtel, akkor az adott központhoz tartozó Windows I2v2 skálázására irányuló minden más kérelem várólistára kerül, amíg a folyamat be nem fejeződik.   

A több-bérlős App Service esetén a skálázás azonnal megtörténik, mivel az erőforrások készlete azonnal elérhetővé válik a támogatásához. Egy központhoz nem tartoznak ilyen pufferek, és az erőforrások igény szerint vannak lefoglalva.

## <a name="app-access"></a>Alkalmazás-hozzáférés

A központilag az alkalmazás létrehozásához használt tartomány utótagja a következő: *. &lt; asename &gt; . appserviceenvironment.net*. Ha a szolgáltató neve My-bekapcsoló, és egy _contoso_ nevű alkalmazást üzemeltet a _kiegészítőben_ , akkor az alábbi URL-címeken érhető el:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

A kiegészítő információk létrehozásával kapcsolatos információkért lásd: [app Service Environment létrehozása][MakeASE].

Az SCM URL-cím a kudu-konzol eléréséhez, illetve az alkalmazás Web Deploy használatával történő közzétételéhez használható. További információ a kudu-konzolról: [kudu-konzol a Azure app Service][Kudu]. A kudu-konzol webes felhasználói felületet biztosít a hibakereséshez, a fájlok feltöltéséhez és a fájlok szerkesztéséhez.

### <a name="dns-configuration"></a>DNS-konfiguráció 

A kiegészítő szolgáltatás saját végpontokat használ a bejövő forgalomhoz. Nincs automatikusan konfigurálva Azure DNS privát zónával. Ha a saját DNS-kiszolgálóját szeretné használni, hozzá kell adnia a következő rekordokat:

1. hozzon létre egy zónát a benyújtó &lt; neve számára &gt; . appserviceenvironment.net
1. hozzon létre egy olyan rekordot az adott zónában, amely a "
1. hozzon létre egy olyan rekordot az adott zónában, amely a "@ to the beérkező" IP-címet a saját kiegészítő szolgáltató privát végpontja használja
1. hozzon létre egy zónát a központhoz tartozó &lt; név &gt; . appserviceenvironment.net neve: SCM
1. hozzon létre egy olyan rekordot az SCM-zónában, amely az *-ra mutat a saját belső beadási végpont által használt IP-címet

A DNS konfigurálása Azure DNS privát zónában:

1. hozzon létre egy appserviceenvironment.net nevű Azure DNS privát zónát <ASE name>
1. hozzon létre egy olyan rekordot az adott zónában, amely * a ILB IP-címére mutat.
1. hozzon létre egy olyan rekordot az adott zónában, amely a @-t a ILB IP-címére mutat.
1. hozzon létre egy olyan rekordot az adott zónában, amely a *. SCM-t a ILB IP-címére mutat.

A beadási csomag alapértelmezett tartományának DNS-beállításai nem korlátozzák, hogy az alkalmazások csak az adott nevek számára legyenek elérhetők. Egyéni tartománynevet úgy is beállíthat, hogy az alkalmazásait nem érvényesíti a központilag. Ha ezután létre szeretne hozni egy *contoso.net* nevű zónát, ezt megteheti, és rámutathat a bejövő IP-címére. Az Egyéni tartománynév az alkalmazásra vonatkozó kérelmek esetében működik, de nem az SCM-helyhez. Az SCM-hely csak a *&lt; AppName &gt; . SCM &lt; -ben érhető el. asename &gt; . appserviceenvironment.net*. 

## <a name="publishing"></a>Közzététel

A több-bérlős App Servicehoz hasonlóan a következő módszerekkel teheti közzé a szolgáltatót:

- Webes telepítés
- Folyamatos integráció (CI)
- Drag and drop a kudu-konzolon
- IDE, például a Visual Studio, az Eclipse vagy a IntelliJ IDEA

A beadási végpontok csak a privát végpont által használt bejövő címen érhetők el. Ha nincs hálózati hozzáférése a magánhálózati végponti címnek, akkor nem tehet közzé alkalmazásokat az adott közforráson.  Az ide-nek a ILB való közvetlen közzétételhez is hálózati hozzáférésre van szükségük.

További változtatások nélkül az internetalapú CI-rendszerek (például a GitHub és az Azure DevOps) nem működnek a ILB-vel, mert a közzétételi végpont nem érhető el az interneten. Engedélyezheti a közzétételt az Azure DevOps származó ILB-előállítók számára, ha telepít egy saját üzemeltetésű kiadási ügynököt a virtuális hálózatban, amely tartalmazza a ILB-javítót. 

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

Ha integrálja Log Analyticsokkal, a naplókat a beadási portál **naplók** elemének kiválasztásával, valamint a **AppServiceEnvironmentPlatformLogs**-sel kapcsolatos lekérdezés létrehozásával tekintheti meg. A rendszer csak akkor bocsát ki naplókat, ha a bevezetőnek van olyan eseménye, amely elindítja azt. Ha a kiszervezete nem rendelkezik ilyen eseménnyel, nem lesz napló. A Log Analytics munkaterületen található naplók gyors megjelenítéséhez hajtson végre egy méretezési műveletet a központhoz tartozó App Service-csomagok egyikével. Ezután futtathat egy lekérdezést a **AppServiceEnvironmentPlatformLogs** a naplók megtekintéséhez. 

**Riasztás létrehozása**

Ha riasztást szeretne létrehozni a naplókban, kövesse a naplók [létrehozása, megtekintése és kezelése a Azure monitor használatával című](../../azure-monitor/alerts/alerts-log.md)témakör utasításait. Röviden:

* A beadási portál riasztások oldalának megnyitása
* **Új riasztási szabály** kiválasztása
* Válassza ki az erőforrást, hogy az Log Analytics munkaterület legyen
* Állítsa be a feltételt egy egyéni naplóbeli kereséssel egy olyan lekérdezés használatához, mint például: "AppServiceEnvironmentPlatformLogs | ahol a ResultDescription "megkezdődött a skálázás", vagy bármi más, amire szüksége van. Szükség szerint állítsa be a küszöbértéket. 
* Szükség szerint adjon hozzá vagy hozzon létre egy műveleti csoportot. A műveleti csoport a riasztásra adott válasz, például e-mailek küldése vagy SMS-üzenet megadása.
* Nevezze el a riasztást, és mentse.

## <a name="internal-encryption"></a>Belső titkosítás

A App Service Environment fekete Box-rendszerként működik, ahol a belső összetevők vagy a rendszeren belüli kommunikáció nem látható. A nagyobb átviteli sebesség engedélyezéséhez a titkosítás alapértelmezés szerint nincs engedélyezve a belső összetevők között. A rendszer biztonságban van, mivel a forgalom teljesen elérhetetlenné válik a figyeléshez vagy a hozzáféréshez. Ha a megfelelőségi követelmény azonban az, hogy az adatelérési út teljes titkosítását igényli a végpontok közötti titkosításhoz, akkor ezt a szolgáltatói **konfiguráció** felhasználói felületén engedélyezheti.

![Belső titkosítás engedélyezése][5]

Ezzel titkosítja a belső hálózati forgalmat az előtérben és a feldolgozók között, titkosítja a lapozófájlt, és titkosítja a munkavégző lemezeket is. A InternalEncryption-clusterSetting engedélyezése után hatással lehet a rendszer teljesítményére. Ha a módosítást engedélyezi a InternalEncryption engedélyezéséhez, a beadása instabil állapotban lesz, amíg a változást teljes mértékben nem propagálja. A változtatások teljes propagálása eltarthat néhány óráig, attól függően, hogy hány példányt használ a központilag. Kifejezetten javasoljuk, hogy ezt a szolgáltatást a szolgáltatón kívül ne engedélyezze. Ha ezt egy aktívan használt benyújtó eszközön kell engedélyeznie, javasoljuk, hogy a művelet befejeződése előtt irányítsa át a forgalmat egy biztonsági mentési környezetbe.

## <a name="upgrade-preference"></a>Frissítési beállítások

Ha több ASE is rendelkezik, érdemes lehet néhány ASE frissíteni, mielőtt mások is. A **üzemeltetési Resource Manager** -objektumon belül megadhatja a **upgradePreference** értékét. A **upgradePreference** beállítását sablon, ARMClient vagy a használatával lehet konfigurálni https://resources.azure.com . A három lehetséges érték a következők:

- **Nincs**: az Azure a beadott kötegben nem fogja frissíteni a Bea-t. Ez az alapértelmezett érték.
- **Korai**: a bevezetés a app Service frissítéseinek első felében lesz frissítve.
- **Későn**: a bevezetés a app Service frissítéseinek második felében lesz frissítve.

A frissítési beállítások konfigurálásához nyissa meg a következőt: a köznyelvi **konfiguráció** felhasználói felülete. 

A **upgradePreferences** szolgáltatás a legtöbb esetben a több ASE, mivel a "korai" ASE a "késői" ASE előtt frissülni fog. Ha több ASE rendelkezik, a fejlesztési és tesztelési ASE "korai" értékre kell állítania, és az éles ASE "késő" értékűnek kell lennie.

## <a name="delete-an-ase"></a>Bemutató törlése

A bemutató rendszer törlése:

1. Válassza a **Törlés** lehetőséget a **app Service Environment** ablaktábla tetején.

1. Adja meg a beadási hely nevét annak megerősítéséhez, hogy törölni szeretné. Ha töröl egy beadást, akkor a benne található összes tartalmat is törli.

    ![Bemutatás törlése][3]

1. Válassza az **OK** lehetőséget.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
