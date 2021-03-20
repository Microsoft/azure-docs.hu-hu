---
title: Első lépések útmutató az Azure-beli fejlesztőknek | Microsoft Docs
description: Ez a cikk az Microsoft Azure platform fejlesztési igényeihez való használatának első lépéseit bemutató fejlesztők számára nyújt alapvető információkat.
author: ggailey777
ms.service: azure
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: 12b90bf709cb20bed844940570663a76052a540d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738939"
---
# <a name="get-started-guide-for-azure-developers"></a>Azure fejlesztői útmutató az első lépésekhez

## <a name="what-is-azure"></a>Mi az Azure?

Az Azure egy teljes körű felhőalapú platform, amely képes a meglévő alkalmazások üzemeltetésére és az új alkalmazások fejlesztésének egyszerűsítésére. Az Azure még a helyszíni alkalmazások fejlesztésére is képes. Az Azure integrálja az alkalmazások fejlesztéséhez, teszteléséhez, üzembe helyezéséhez és kezeléséhez szükséges felhőalapú szolgáltatásokat, miközben kihasználja a felhő-számítástechnika hatékonyságát.

Az alkalmazások az Azure-ban való üzemeltetésével kisméretű és könnyen méretezhető alkalmazást használhat az ügyfelek igényei szerint. Az Azure a magas rendelkezésre állású alkalmazásokhoz szükséges megbízhatóságot is biztosítja, még a különböző régiók közötti feladatátvételt is beleértve. A [Azure Portal](https://portal.azure.com) segítségével könnyedén kezelheti az összes Azure-szolgáltatást. A szolgáltatásokat programozott módon is kezelheti a szolgáltatás-specifikus API-k és sablonok használatával.

Ez az útmutató az alkalmazások fejlesztői számára készült Azure platform bemutatása. Útmutatást és irányt biztosít az új alkalmazások Azure-ban való létrehozásához vagy a meglévő alkalmazások Azure-ba való áttelepítéséhez.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Az Azure által kínált összes szolgáltatással megfélemlítő feladat lehet a megoldás architektúrájának támogatásához szükséges szolgáltatások kitalálása. Ez a szakasz a fejlesztők által gyakran használt Azure-szolgáltatásokat emeli ki. Az összes Azure-szolgáltatás listáját az [Azure dokumentációjában](../../index.yml)találja.

Először is el kell döntenie, hogyan üzemelteti az alkalmazást az Azure-ban. A teljes infrastruktúrát virtuális gépnek (VM) kell kezelnie? Használhatja az Azure által biztosított platform-felügyeleti szolgáltatásokat? Lehet, hogy kiszolgáló nélküli keretrendszerre van szüksége a kód futtatásához?

Az alkalmazásnak Felhőbeli tárhelyre van szüksége, amelyet az Azure számos lehetőséget kínál. Igénybe veheti az Azure vállalati hitelesítését. A felhőalapú fejlesztéshez és monitorozáshoz is vannak eszközök, és a legtöbb üzemeltetési szolgáltatás DevOps-integrációt biztosít.

Most nézzük meg, hogy milyen szolgáltatásokat ajánlunk az alkalmazások vizsgálatához.

### <a name="application-hosting"></a>Alkalmazás üzemeltetése

Az Azure számos felhőalapú számítási ajánlatot biztosít az alkalmazás futtatásához, így nem kell aggódnia az infrastruktúra részleteivel kapcsolatban. Az alkalmazások használatának növekedésével egyszerűen méretezheti vagy bővítheti erőforrásait.

Az Azure olyan szolgáltatásokat kínál, amelyek támogatják az alkalmazás fejlesztési és üzemeltetési igényeit. Az Azure biztosítja az infrastruktúra-szolgáltatást (IaaS), így teljes körűen szabályozhatja az alkalmazás üzemeltetését. Az Azure platform as Service (Pásti) ajánlatai biztosítják az alkalmazások működtetéséhez szükséges teljes körűen felügyelt szolgáltatásokat. Az Azure-ban még igaz kiszolgáló nélküli üzemeltetés is van, ahol mindössze annyit kell tennie, hogy megírja a kódját.

![Azure-alkalmazás üzemeltetési lehetőségei](./media/azure-developer-guide/azure-developer-hosting-options.png)

#### <a name="azure-app-service"></a>Azure App Service

Ha azt szeretné, hogy a leggyorsabb elérési út a webalapú projektek közzétételéhez, érdemes Azure App Service. A App Service megkönnyíti a webalkalmazások bővítését a mobil ügyfelek támogatásához és a könnyen felhasznált REST API-k közzétételéhez. Ez a platform a közösségi szolgáltatók, a forgalomon alapuló automatikus skálázás, az éles környezetben végzett tesztelés, valamint a folyamatos és a tároló alapú üzembe helyezések révén nyújt hitelesítést.

Létrehozhat webalkalmazásokat, Mobile App back-és API-alkalmazásokat.

Mivel mindhárom alkalmazás típusa megosztja a App Service futtatókörnyezetet, üzemeltetheti a webhelyeket, támogatja a mobil ügyfeleket, és elérhetővé teheti az API-kat az Azure-ban, mindezt ugyanabból a projektből vagy megoldásból. Ha többet szeretne megtudni a App Serviceről, tekintse meg [Az Azure Web Apps](../../app-service/overview.md)ismertetése című témakört.

App Service a DevOps szem előtt tartva lett tervezve. Különböző eszközöket támogat a közzétételhez és a folyamatos integrációs telepítésekhez. Ezek az eszközök például a GitHub-webhookok, a Jenkins, az Azure DevOps, a TeamCity és mások.

A meglévő alkalmazásokat áttelepítheti App Servicera az [Online áttelepítési eszköz](https://appmigration.microsoft.com/)használatával.

> **Használat esetén**: app Service használata, amikor meglévő webalkalmazásokat telepít át az Azure-ba, és ha egy teljes körűen felügyelt üzemeltetési platformra van szüksége a Web Apps számára. App Service akkor is használhatja, ha a mobileszközök támogatásához vagy a REST API-k az alkalmazással való eléréséhez szükséges.
>
> Első **lépések**: app Service megkönnyíti az első [webalkalmazás](../../app-service/quickstart-dotnetcore.md), a [Mobile App](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)vagy az [API-alkalmazás](../../app-service/app-service-web-tutorial-rest-api.md)létrehozását és üzembe helyezését.
>
> **Próbálja ki most**: app Service lehetővé teszi egy rövid élettartamú alkalmazás kiépítését a platform kipróbálásához anélkül, hogy regisztrálnia kellene egy Azure-fiókra. Próbálja ki a platformot, és [hozza létre a Azure app Service alkalmazást](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

A IaaS-szolgáltatóként az Azure lehetővé teszi az alkalmazás üzembe helyezését vagy áttelepítését Windows vagy Linux rendszerű virtuális gépekre. Az Azure Virtual Network együtt az Azure Virtual Machines támogatja a Windows vagy Linux rendszerű virtuális gépek Azure-ba való telepítését. A virtuális gépekkel teljes mértékben vezérelheti a gép konfigurációját. Virtuális gépek használatakor az összes kiszolgáló szoftver telepítésére, konfigurálására, karbantartására és az operációs rendszerre vonatkozó javítások felelősek.

A virtuális gépekhez tartozó szabályozás szintje miatt a kiszolgálói munkaterhelések széles körét futtathatja az Azure-ban, amelyek nem illeszkednek a Pásti-modellbe. Ezek a számítási feladatok közé tartoznak az adatbázis-kiszolgálók, a Windows Server Active Directory és a Microsoft SharePoint. További információkért tekintse meg a [Linux](../../virtual-machines/index.yml) vagy a [Windows rendszerhez](../../virtual-machines/index.yml)készült Virtual Machines dokumentációját.

> **Használat esetén**: Virtual Machines használata, ha az alkalmazás-infrastruktúra teljes körű vezérlését szeretné végezni, vagy a helyszíni alkalmazások számítási feladatait az Azure-ba kívánja áttelepíteni anélkül, hogy módosítani kellene.
>
> Első **lépések**: [linuxos virtuális gép](../../virtual-machines/linux/quick-create-portal.md) vagy [Windows rendszerű virtuális gép](../../virtual-machines/windows/quick-create-portal.md) létrehozása a Azure Portalból.

#### <a name="azure-functions-serverless"></a>Azure Functions (kiszolgáló nélküli)

Nem kell aggódnia a teljes alkalmazás vagy az infrastruktúra a kód futtatásához való kiépítésével és kezelésével kapcsolatban, mi a teendő, ha csak megírhatja a kódot, és az eseményekre adott válaszként vagy ütemterv szerint futtatja?  [Azure functions](../../azure-functions/functions-overview.md) egy "kiszolgáló nélküli" stílusú ajánlat, amely lehetővé teszi, hogy csak a szükséges kódot írja. A függvények lehetővé teszi a kód végrehajtását HTTP-kérésekkel, webhookokkal, Cloud Service-eseményekkel vagy ütemezett műveletekkel. Az Ön által választott fejlesztési nyelvben (például C \# , F \# , Node.js, PYTHON vagy php) kódot is használhat. A fogyasztáson alapuló számlázással csak a kód végrehajtásának idejére kell fizetnie, az Azure pedig igény szerint méretezhető.

> **Használat esetén**: Azure functions használata, ha olyan kódot használ, amelyet más Azure-szolgáltatások, webalapú események vagy ütemterv alapján aktiválnak. A függvényeket akkor is használhatja, ha nincs szüksége egy teljes körűen üzemeltetett projekt terhelésére, vagy ha csak a kód futási idejét szeretné kifizetni. További információ: [Azure functions Overview (áttekintés](../../azure-functions/functions-overview.md)).
>
> Első **lépések**: kövesse a functions rövid útmutatóját az [első függvény létrehozásához](../../azure-functions/functions-get-started.md) a portálon.
>
> **Próbálja ki most**: Azure functions lehetővé teszi a kód futtatását anélkül, hogy regisztrálnia kellene egy Azure-fiókra. Próbálja ki most, és [hozza létre első Azure-függvényét](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Az Azure Service Fabric egy elosztott rendszerek platformja. Ez a platform megkönnyíti a méretezhető és megbízható szolgáltatások kiépítését, csomagolását, üzembe helyezését és felügyeletét. Emellett átfogó alkalmazás-felügyeleti képességeket is biztosít, például a következőket:

* Kiépítés
* Telepítése
* Figyelés
* Frissítés/javítás
* Törlés

Az olyan alkalmazások, amelyek egy megosztott készleten futnak, kis méretűek és akár több száz vagy akár több ezer gép számára is elindíthatók.

Service Fabric támogatja a WebAPI-t a .NET (OWIN) és a ASP.NET Core nyílt webes felületével. SDK-kat biztosít a .NET Core és a Java platformon futó Linux-szolgáltatások létrehozásához. Ha többet szeretne megtudni a Service Fabricről, tekintse meg a [Service Fabric dokumentációját](../../service-fabric/index.yml).

> **Mikor kell használni:** A Service Fabric jó választás, ha egy alkalmazást hoz létre, vagy egy meglévő alkalmazást telepít át egy Service-architektúra használatára. A Service Fabric akkor érdemes használni, ha a mögöttes infrastruktúrát nagyobb mértékben kell megadnia, vagy közvetlenül hozzá kell férnie.
>
> Első **lépések:** [hozza létre első Azure Service Fabric alkalmazását](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md).

#### <a name="azure-spring-cloud"></a>Azure Spring Cloud

Az Azure Spring Cloud egy kiszolgáló nélküli, a felhőben futó alkalmazások létrehozására, üzembe helyezésére, méretezésére és figyelésére szolgáló platform. A Spring Cloud segítségével modern, a tavaszi rendszerindítást lehetővé téve a korszerű Service-mintákat, így a szabványos programkódok segítségével gyorsan készíthet robusztus Java-alkalmazásokat.

* Kihasználhatja a Spring Cloud Service Discovery és a config Server felügyelt verzióit, miközben biztosítjuk, hogy ezek a kritikus összetevők optimális feltételek mellett fussanak.
* Koncentráljon az üzleti logikára, és a biztonsági javítások, a megfelelőségi szabványok és a magas rendelkezésre állás érdekében gondoskodunk a szolgáltatás futtatókörnyezetéről.
* Az alkalmazások életciklusának kezelése (például: üzembe helyezés, indítás, Leállítás, méretezés) az Azure Kubernetes szolgáltatáson felül.
* Könnyedén összekapcsolhatja az alkalmazások és az Azure-szolgáltatások, például a Azure Database for MySQL és az Azure cache közötti kapcsolatokat a Redis.
* A nagyvállalati szintű, egységesített figyelési eszközöket használó, nagy teljesítményű és az alkalmazás-függőségekre és az operatív telemetria vonatkozó részletes bepillantást nyújtó, nagyvállalati szintű, egységes monitorozást biztosító

> **Mikor kell használni:** Az Azure Spring Cloud teljes körűen felügyelt szolgáltatásának használata jó választás, ha az Azure-on futó Spring boot/Spring Cloud-alapú Service-szolgáltatások minimálisra csökkentik a működési költségeket. 
>
> Első **lépések:** [az első Azure Spring Cloud-alkalmazás üzembe helyezése](../../spring-cloud/spring-cloud-quickstart.md).


### <a name="enhance-your-applications-with-azure-services"></a>Alkalmazások fejlesztése az Azure-szolgáltatásokkal

Az Azure az alkalmazások üzemeltetése mellett olyan szolgáltatás-ajánlatokat is biztosít, amelyek javítják a funkciókat. Az Azure a felhőben és a helyszínen is fejlesztheti alkalmazásai fejlesztését és karbantartását.

#### <a name="hosted-storage-and-data-access"></a>Üzemeltetett tár és adathozzáférés

A legtöbb alkalmazásnak tárolnia kell az adatait, de úgy dönt, hogy az alkalmazást az Azure-ban üzemelteti, vegye figyelembe a következő tárolási és adatszolgáltatások közül egyet vagy többet.

* **Azure Cosmos db**: globálisan elosztott, többmodelles adatbázis-szolgáltatás. Ez az adatbázis lehetővé teszi az átviteli sebesség és a tárterület rugalmas méretezését tetszőleges számú földrajzi régióban, átfogó SLA-val.

  > **Mikor kell használni:** Ha az alkalmazásnak dokumentum-, tábla-vagy Graph-adatbázisokra, például MongoDB-adatbázisokra van szüksége, több jól meghatározott konzisztencia-modellel.
  >
  > Első **lépések**: [Azure Cosmos db Webalkalmazás létrehozása](../../cosmos-db/create-sql-api-dotnet.md). Ha Ön MongoDB-fejlesztő, tekintse meg [a MongoDB-Webalkalmazás létrehozása a Azure Cosmos DBkal](../../cosmos-db/create-mongodb-dotnet.md)című témakört.

* **Azure Storage**: tartós, magasan elérhető tárhelyet biztosít a Blobok, a várólisták, a fájlok és más típusú nem kapcsolódó adattípusok számára. A Storage biztosítja a virtuális gépek tárolási alapjait.

  > **Mikor kell használni**: Ha az alkalmazás nem kapcsolódó adatait, például kulcs-érték párokat (táblákat), blobokat, fájlokat, megosztásokat vagy üzeneteket (várólistákat) tárol.
  >
  > Első **lépések**: válasszon az alábbi típusok közül: [Blobok](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [táblák](../../cosmos-db/tutorial-develop-table-dotnet.md), [várólisták](../../storage/queues/storage-dotnet-how-to-use-queues.md)vagy [fájlok](../../storage/files/storage-dotnet-how-to-use-files.md).

* **Azure SQL Database**: a Microsoft SQL Server motor Azure-alapú verziója, amely a felhőben lévő, a táblázatos adatokat tárolja. SQL Database az állásidő, az üzletmenet folytonossága és az adatvédelem nélkül biztosít kiszámítható teljesítményt, méretezhetőséget.

  > **Mikor kell használni**: Ha az alkalmazás a hivatkozási integritással, a tranzakciós támogatással és a TSQL-lekérdezések támogatásával igényli az adattárolást.
  >
  > Első **lépések**: [hozzon létre egy adatbázist Azure SQL Database percek alatt a Azure Portal használatával](../../azure-sql/database/single-database-create-quickstart.md).


A meglévő helyszíni és az Azure-ba történő áthelyezéséhez [Azure Data Factory](../../data-factory/introduction.md) is használhat. Ha nem áll készen az adatáthelyezésre a felhőbe, [hibrid kapcsolatok](../../app-service/app-service-hybrid-connections.md) a Azure app Service lehetővé teszi a app Service üzemeltetett alkalmazás helyszíni erőforrásokhoz való összekapcsolását. A helyszíni alkalmazásokból is kapcsolódhat az Azure-beli adatokhoz és a tárolási szolgáltatásokhoz.

#### <a name="docker-support"></a>Docker-támogatás

A Docker-tárolók, az operációs rendszer virtualizálása, lehetővé teszik az alkalmazások hatékonyabb és kiszámítható módon való üzembe helyezését. A tárolóban lévő alkalmazások ugyanúgy működnek éles környezetben, mint a fejlesztési és tesztelési rendszereken. A tárolók a standard Docker-eszközök használatával kezelhetők. Az Azure-beli tároló-alapú alkalmazások üzembe helyezéséhez és kezeléséhez használhatja meglévő ismereteit és népszerű nyílt forráskódú eszközeit.

Az Azure számos lehetőséget kínál a tárolók használatára az alkalmazásokban.


* **Azure Kubernetes szolgáltatás**: lehetővé teszi olyan virtuális gépek fürtjének létrehozását, konfigurálását és kezelését, amelyek tároló alkalmazások futtatására vannak konfigurálva. Az Azure Kubernetes Service-ről további információt az [Azure Kubernetes Service bemutatása](../../aks/intro-kubernetes.md)című témakörben talál.

  > **Mikor érdemes használni**: Ha olyan éles használatra kész, méretezhető környezeteket kell létrehoznia, amelyek további ütemezési és felügyeleti eszközöket biztosítanak, vagy amikor üzembe helyez egy Docker Swarm-fürtöt.
  >
  > Első **lépések**: [Kubernetes Service-fürt üzembe helyezése](../../aks/tutorial-kubernetes-deploy-cluster.md).

* **Docker-gép**: lehetővé teszi Docker-motor telepítését és kezelését a virtuális gazdagépeken a Docker-Machine parancsok használatával.

  >**Mikor érdemes használni**: Ha egy Docker-gazdagép létrehozásával gyorsan kell prototípust készíteni egy alkalmazáshoz.

* **Egyéni Docker-rendszerkép a app Servicehoz**: lehetővé teszi, hogy a Docker-tárolókat egy tároló-beállításjegyzékből vagy egy ügyfél-tárolóból használja a webalkalmazás Linux rendszeren való telepítésekor.

  > **Mikor kell használni**: egy webalkalmazás Linux rendszeren Docker-rendszerképbe való telepítésekor.
  >
  > Első **lépések**: [Egyéni Docker-rendszerkép használata a linuxon futó app Servicehoz](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux).

### <a name="authentication"></a>Hitelesítés

Rendkívül fontos, hogy ne csak tudja, ki használja az alkalmazásokat, hanem megakadályozza az erőforrások jogosulatlan elérését is. Az Azure számos módszert kínál az alkalmazás-ügyfelek hitelesítésére.

* **Azure Active Directory (Azure ad)**: a Microsoft több-bérlős, felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Az Azure AD-val való integrálással hozzáadhat egyszeri bejelentkezést (SSO) az alkalmazásaihoz. A címtár tulajdonságai az Azure AD Graph API közvetlenül vagy a Microsoft Graph API használatával érhetők el. Integrálhatja az Azure AD-támogatást a OAuth 2.0 engedélyezési keretrendszeréhez, és az ID-t a natív HTTP/REST-végpontok és a többplatformos Azure AD-hitelesítési kódtárak használatával is megnyithatja.

  > **Használat esetén**: Ha egyszeri bejelentkezéses felhasználói élményt szeretne biztosítani, használja a Graph-alapú adatmennyiséget, vagy hitelesítse a tartományalapú felhasználókat.
  >
  > Első **lépések**: további információért tekintse meg a [Azure Active Directory fejlesztői útmutatóját](../../active-directory/develop/v2-overview.md).

* **App Service hitelesítés**: Ha úgy dönt, app Service az alkalmazás üzemeltetéséhez, az Azure ad beépített hitelesítési támogatását, valamint a közösségi identitás-szolgáltatókat, például a Facebookot, a Google-t, a Microsoftot és a Twittert is igénybe veheti.

  > **Mikor érdemes használni** a következőt: Ha engedélyezni szeretné a hitelesítést egy app Service alkalmazásban az Azure ad-vel, a közösségi identitás-szolgáltatókkal vagy mindkettővel.
  >
  > Első **lépések**: Ha többet szeretne megtudni a app Service hitelesítéséről, tekintse meg [a hitelesítés és engedélyezés](../../app-service/overview-authentication-authorization.md)című témakört Azure app Service.

Az Azure-ban ajánlott biztonsági eljárásokkal kapcsolatos további információkért lásd az [Azure biztonsági eljárásai és mintái](../../security/fundamentals/best-practices-and-patterns.md)című témakört.

### <a name="monitoring"></a>Figyelés

Az Azure-ban futó alkalmazásával figyelnie kell a teljesítményt, figyelnie kell a problémákat, és látnia kell, hogyan használják az ügyfelek az alkalmazást. Az Azure számos figyelési lehetőséget biztosít.

* **Application Insights**: egy Azure által üzemeltetett bővíthető elemzési szolgáltatás, amely integrálható a Visual Studióval az élő webalkalmazások figyeléséhez. A szolgáltatás folyamatosan biztosítja az alkalmazások teljesítményének és használhatóságának javításához szükséges adatmennyiséget. Ez a fejlesztés azt eredményezi, hogy az alkalmazásokat az Azure-ban üzemelteti-e, vagy sem.

  > Első **lépések**: kövesse az [Application Insights oktatóanyagot](../../azure-monitor/app/app-insights-overview.md).

* **Azure monitor**: olyan szolgáltatás, amely megkönnyíti az Azure-infrastruktúrával és-erőforrásokkal létrehozott mérőszámok és naplók megjelenítését, lekérdezését, továbbítását, archiválását és elvégzését. A figyelő egyetlen forrás az Azure-erőforrások monitorozásához és a Azure Portalban megjelenő adatnézetek megjelenítéséhez.

  > Első **lépések**: [a Azure monitor első lépései](../../azure-monitor/overview.md).

### <a name="devops-integration"></a>DevOps-integráció

Függetlenül attól, hogy a virtuális gépek kiépítése vagy a webalkalmazások folyamatos integrációval való közzététele, az Azure integrálható a legtöbb népszerű DevOps-eszközzel. Használhatja a már meglévő eszközöket, és maximalizálhatja meglévő tapasztalatait a következő eszközök támogatásával:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> Első **lépések**: az App Service alkalmazások DevOps-beállításainak megtekintéséhez lásd: [folyamatos üzembe helyezés Azure app Service](../../app-service/deploy-continuous-deployment.md).
>
> **Próbálja ki most:** [próbálja ki több DevOps-integrációt](https://azure.microsoft.com/try/devops/).

## <a name="azure-regions"></a>Azure-régiók

Az Azure egy globális felhőalapú platform, amely világszerte számos régióban általánosan elérhető. Amikor kiépít egy szolgáltatást, alkalmazást vagy virtuális gépet az Azure-ban, a rendszer megkéri, hogy válasszon ki egy régiót. Ez a régió egy adott adatközpontot jelöl, amelyben az alkalmazás fut, vagy ahol az Ön adatait tárolja. Ezek a régiók az [Azure-régiók](https://azure.microsoft.com/regions/) lapon közzétett bizonyos helyeknek felelnek meg.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Válassza ki az alkalmazás és az adatai számára legmegfelelőbb régiót

Az Azure használatának egyik előnye, hogy alkalmazásai különböző adatközpontokban üzembe helyezhetők világszerte. Az Ön által választott régió befolyásolhatja az alkalmazás teljesítményét. Érdemes lehet például olyan régiót választani, amely közelebb van az ügyfelek többségéhez, hogy csökkentse a hálózati kérelmek késését. Előfordulhat, hogy ki kell választania a régiót, hogy megfeleljen az alkalmazás egyes országokban/régiókban való terjesztésének jogi követelményeinek. Mindig ajánlott eljárás az alkalmazásadatok ugyanabban az adatközpontban vagy egy adatközpontban való tárolására, mint amennyire a lehető legközelebb az alkalmazást üzemeltető adatközponthoz.

### <a name="multi-region-apps"></a>Többrégiós alkalmazások

Habár nem valószínű, hogy egy teljes adatközpont offline állapotba kerül egy esemény, például egy természeti katasztrófa vagy Internet meghibásodása miatt. Az ajánlott eljárás a létfontosságú üzleti alkalmazások üzemeltetése több adatközpontban a maximális rendelkezésre állás biztosítása érdekében. Több régió használata is csökkentheti a globális felhasználók késését, és további lehetőségeket biztosít a rugalmassághoz az alkalmazások frissítésekor.

Egyes szolgáltatások, például a virtuális gépek és a App Services az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) használatával lehetővé teszik a régiók közötti feladatátvételt a régiókban a magas rendelkezésre állású Nagyvállalati alkalmazások támogatásához. Példaként tekintse meg az [Azure Reference Architecture: webalkalmazás futtatása több régióban](/azure/architecture/reference-architectures/app-service-web-app/multi-region)című témakört.

>**Mikor érdemes használni**: Ha olyan nagyvállalati és magas rendelkezésre állású alkalmazásokat használ, amelyek kihasználják a feladatátvételt és a replikálást.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hogyan kezelhetem az alkalmazásaimat és a projektjeimet?

Az Azure számos tapasztalatot biztosít az Azure-erőforrások,-alkalmazások és-projektek létrehozásához és kezeléséhez, akár programozott módon, akár a [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Parancssori felületek és PowerShell

Az Azure két lehetőséget biztosít az alkalmazások és szolgáltatások parancssorból való kezelésére. Használhat olyan eszközöket, mint például a bash, a Terminal, a parancssor vagy az Ön által választott parancssori eszköz. A parancssorból általában ugyanazok a feladatok végezhetők el, mint például a virtuális gépek, Azure Portal a virtuális hálózatok, a Web Apps és más szolgáltatások létrehozása és konfigurálása.

* [Azure Command-Line Interface (CLI)](/cli/azure/install-azure-cli): lehetővé teszi az Azure-előfizetéshez való kapcsolódást és az Azure-erőforrásokkal kapcsolatos különböző feladatok végrehajtását a parancssorból.

* [Azure PowerShell](/powershell/azure/): az Azure-erőforrások Windows PowerShell használatával történő felügyeletét lehetővé tevő parancsmagokkal rendelkező modulok készletét biztosítja.

### <a name="azure-portal"></a>Azure Portal

A [Azure Portal](https://portal.azure.com) egy webalapú alkalmazás. Az Azure-erőforrások és-szolgáltatások létrehozásához, kezeléséhez és eltávolításához használhatja a Azure Portal. A következőket tartalmazza:

* Konfigurálható irányítópult
* Azure Resource Management-eszközök
* Hozzáférés az előfizetési beállításokhoz és a számlázási adatokhoz. További információ: [Azure Portal áttekintése](https://azure.microsoft.com/features/azure-portal/).

### <a name="rest-apis"></a>REST API-k

Az Azure a Azure Portal felhasználói felületét támogató REST API-k készletére épül. A REST API-k többsége támogatja az Azure-erőforrások és-alkalmazások programozott módon történő üzembe helyezését és kezelését bármely internetalapú eszközről. Az REST API dokumentációjának teljes készletét az [Azure Rest SDK-referenciában](/rest/api/)találja.

### <a name="apis"></a>API-k

A REST API-kkal együtt számos Azure-szolgáltatás is lehetővé teszi, hogy programozott módon kezelhesse az alkalmazások erőforrásait a platform-specifikus Azure SDK-k használatával, beleértve az alábbi fejlesztői platformokhoz készült SDK-kat:

* [.NET](/dotnet/api/)
* [Node.js](/azure/developer/javascript/)
* [Java](/java/azure)
* [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
* [Python](/azure/python/)
* [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
* [Ugrás](/azure/go)

Az olyan szolgáltatások, mint például a [Mobile apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) és a [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) ügyféloldali SDK-kat biztosítanak, amelyek lehetővé teszik a webes és mobil ügyfélalkalmazások szolgáltatásainak elérését.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Az alkalmazás Azure-beli futtatása valószínűleg több Azure-szolgáltatással is működik. Ezek a szolgáltatások ugyanazt az életciklust követik, és logikai egységnek is tekinthetők. Előfordulhat például, hogy egy webalkalmazás használhatja a Web Apps, a SQL Database, a Storage, az Azure cache for Redis és az Azure Content Delivery Network Services szolgáltatást. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) lehetővé teszi, hogy az alkalmazásban lévő erőforrásokat csoportként működjön. Egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti az összes erőforrást.

A kapcsolódó erőforrások logikai csoportosítása és kezelése mellett Azure Resource Manager olyan központi telepítési képességeket is tartalmaz, amelyek lehetővé teszik a kapcsolódó erőforrások üzembe helyezésének és konfigurálásának testreszabását. Használhatja például a Resource Manager alkalmazást az alkalmazások üzembe helyezéséhez és konfigurálásához. Ez az alkalmazás több virtuális gépről, egy terheléselosztóból és Azure SQL Database egyetlen egységként elérhető adatbázisból állhat.

Ezeket az üzembe helyezéseket egy Azure Resource Manager sablon használatával fejlesztheti, amely egy JSON-formátumú dokumentum. A sablonok lehetővé teszik, hogy a parancsfájlok helyett deklaratív sablonok használatával definiálja az alkalmazásokat, és kezelje az alkalmazásait. A sablonok különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhetnek. A sablonok használatával például hozzáadhat egy gombot egy GitHub-tárházhoz, amely egyetlen kattintással üzembe helyezi a tárházban lévő kódot egy Azure-szolgáltatásba.

> **Használat esetén**: használjon Resource Manager-sablonokat, ha olyan sablon alapú üzembe helyezést szeretne alkalmazni az alkalmazáshoz, amely a REST API-k, az Azure CLI és a Azure PowerShell használatával felügyelhető.
>
> Első **lépések**: a sablonok használatának megkezdéséhez lásd: [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/templates/template-syntax.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>A fiókok, az előfizetések és a számlázás ismertetése

A fejlesztőknek jobb, ha a kódot vesszük figyelembe, és a lehető leggyorsabban igyekszünk elindítani az alkalmazásokat. Javasoljuk, hogy a lehető legkönnyebben megkezdje az Azure-ban való munkát. Az Azure [ingyenes próbaidőszakot](https://azure.microsoft.com/free/)biztosít a könnyebb kezelhetőség érdekében. Egyes szolgáltatások esetében a "kipróbálás ingyenes" funkciót is használhatja, például a [Azure app Servicet](https://tryappservice.azure.com/), amelyhez még nem kell fiókot létrehoznia. Olyan szórakozást, mint a kódolás és az alkalmazás üzembe helyezése az Azure-ban, fontos, hogy elvégezze az Azure működésének megértését is. Pontosabban tisztában kell lennie azzal, hogyan működik a felhasználói fiókok, előfizetések és a számlázás szempontjából.

### <a name="what-is-an-azure-account"></a>Mi az az Azure-fiók?

Azure-előfizetések létrehozásához vagy használatához Azure-fiókkal kell rendelkeznie. Az Azure-fiók egyszerűen identitás az Azure AD-ben vagy egy címtárban, például munkahelyi vagy iskolai szervezet, amelyet az Azure AD megbízhatónak tart. Ha nem tartozik ilyen szervezethez, bármikor létrehozhat egy előfizetést az Azure AD által megbízhatóként használt Microsoft-fiókkal. Ha többet szeretne megtudni a helyszíni Windows Server Active Directory Azure AD-vel való integrálásáról, tekintse [meg a helyszíni identitások integrálása a Azure Active Directorykal](../../active-directory/hybrid/whatis-hybrid-identity.md)című témakört.

Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. További információkért lásd: [Az Azure-előfizetések társítása Azure Active Directoryhoz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Az egyes Azure-fiókok identitásait, más néven *felhasználókat* is definiálhat, és *csoportokat* ADHAT meg az Azure ad-ben. A felhasználói csoportok létrehozása jó módja a szerepköralapú hozzáférés-vezérlés (RBAC) használatával az előfizetések erőforrásaihoz való hozzáférés kezelésére. A csoportok létrehozásával kapcsolatos további információkért lásd: [csoport létrehozása Azure Active Directory előzetes](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)verzióban. Csoportokat a [PowerShell használatával](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md)is létrehozhat és kezelhet.

### <a name="manage-your-subscriptions"></a>Feliratkozások kezelése

Az előfizetés egy Azure-fiókhoz kapcsolódó Azure-szolgáltatások logikai csoportosítása. Egyetlen Azure-fiók több előfizetést is tartalmazhat. Az Azure-szolgáltatások számlázása előfizetési alapon történik. Az elérhető előfizetési ajánlatok listáját a következő típus szerint tekintheti meg: [Microsoft Azure ajánlat részletei](https://azure.microsoft.com/support/legal/offer-details/). Az Azure-előfizetések olyan fiók-rendszergazdával rendelkeznek, amely teljes körű hozzáféréssel rendelkezik az előfizetéshez. Emellett olyan szolgáltatás-rendszergazdával is rendelkeznek, aki az előfizetés összes szolgáltatását felügyeli. További információ a klasszikus előfizetési rendszergazdákról: [Azure-előfizetések rendszergazdáinak hozzáadása vagy módosítása](../../cost-management-billing/manage/add-change-subscription-administrator.md). Az egyes fiókok az Azure [-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)segítségével részletesen vezérelhetik az Azure-erőforrásokat.

#### <a name="resource-groups"></a>Erőforráscsoportok

Új Azure-szolgáltatások kiépítésekor ezt egy adott előfizetésben teheti meg. Az egyes Azure-szolgáltatások, amelyek más néven erőforrások, egy erőforráscsoport kontextusában jönnek létre. Az erőforráscsoportok megkönnyítik az alkalmazás erőforrásainak üzembe helyezését és kezelését. Az erőforráscsoport az alkalmazás összes olyan erőforrását tartalmazza, amelyet egységként szeretne használni. Az erőforrásokat az erőforráscsoportok és a különböző előfizetések között is áthelyezheti. További információ az erőforrások áthelyezéséről: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

A Azure Erőforrás-kezelő nagyszerű eszköz az előfizetésben már létrehozott erőforrások megjelenítéséhez. További információ: az [erőforrások megtekintése és módosítása a Azure erőforrás-kezelő használatával](/rest/api/).

#### <a name="grant-access-to-resources"></a>Hozzáférést biztosít az erőforrásokhoz

Ha engedélyezi az Azure-erőforrásokhoz való hozzáférést, az ajánlott eljárás az, hogy a felhasználók az adott feladat végrehajtásához szükséges legalacsonyabb jogosultsággal lássák el a felhasználókat.

* **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)**: az Azure-ban hozzáférést biztosíthat a felhasználói fiókoknak (rendszerbiztonsági tag) egy adott hatókörben: előfizetés, erőforráscsoport vagy egyedi erőforrás. Az Azure RBAC lehetővé teszi erőforrások üzembe helyezését egy erőforráscsoport számára, és engedélyek megadását egy adott felhasználó vagy csoport számára. Azt is lehetővé teszi, hogy korlátozza a hozzáférést csak a célként megadott erőforráscsoporthoz tartozó erőforrásokhoz. Hozzáférést biztosíthat egyetlen erőforráshoz, például virtuális géphez vagy virtuális hálózathoz is. A hozzáférés engedélyezéséhez hozzá kell rendelnie egy szerepkört a felhasználóhoz, a csoporthoz vagy az egyszerű szolgáltatáshoz. Számos előre definiált szerepkör létezik, és saját egyéni szerepkörök is megadhatók. További információ: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md).

  > **Mikor érdemes használni**: Ha a felhasználóknak és csoportoknak részletes hozzáférés-kezelést kell végeznie, vagy ha a felhasználónak egy előfizetés tulajdonosát kell tennie.
  >
  > Első **lépések**: további információért tekintse meg [Az Azure-szerepkörök a Azure Portal használatával történő hozzárendelését](../../role-based-access-control/role-assignments-portal.md)ismertető témakört.

- **Egyszerű szolgáltatások objektumai**: a felhasználói rendszerbiztonsági tag és a csoportok hozzáférésének biztosításával azonos hozzáférést biztosíthat egy egyszerű szolgáltatáshoz.

  > **Mikor érdemes használni**: Ha programozott módon kezeli az Azure-erőforrásokat, vagy hozzáférést biztosít az alkalmazásokhoz. További információ: [Create Active Directory Application and szolgáltatásnév](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Címkék

Azure Resource Manager lehetővé teszi, hogy egyéni címkéket rendeljen az egyes erőforrásokhoz. A kulcs-érték párokat tartalmazó címkék hasznosak lehetnek, ha erőforrásokat kell szerveznie a számlázáshoz vagy a figyeléshez. A címkék lehetővé teszik az erőforrások több erőforráscsoport közötti nyomon követését. A címkék a következő módokon rendelhetők hozzá:

* A portálon
* A Azure Resource Manager sablonban
* A REST API használata
* Az Azure CLI-vel
* A PowerShell használata

Az egyes erőforrásokhoz több címkét is hozzárendelhet. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/management/tag-resources.md).

### <a name="billing"></a>Számlázás

A helyszíni számítástechnika és a felhőben üzemeltetett szolgáltatások között a szolgáltatás használatának nyomon követése és becslése, valamint a kapcsolódó költségek jelentős jelentőséggel bír. Fontos megbecsülni, hogy milyen új erőforrásokat kell havi rendszerességgel futtatni. Azt is megteheti, hogy a számlázás hogyan néz ki egy adott hónapban az aktuális kiadások alapján.

#### <a name="get-resource-usage-data"></a>Erőforrás-használati adatok beolvasása

Az Azure olyan számlázási REST API-k készletét biztosítja, amelyek hozzáférést biztosítanak az Azure-előfizetések erőforrás-felhasználási és metaadat-adataihoz. Ezek a számlázási API-k lehetővé teszi az Azure-költségek jobb előrejelzését és kezelését. A kiadásokat óránkénti növekedéssel nyomon követheti és elemezheti, és a kiadási riasztásokat is létrehozhatja. A jövőbeli számlázást a jelenlegi használati trendek alapján is előre jelezheti.

>Első **lépések**: Ha többet szeretne megtudni a számlázási API-k használatáról, tekintse meg az Azure-beli [felhasználás API áttekintése](../../cost-management-billing/manage/consumption-api-overview.md) című témakört.

#### <a name="predict-future-costs"></a>Jövőbeli költségek előrejelzése

Bár az Azure-ban az idő előtt megbecsülhető a költségek, az Azure olyan eszközöket tartalmaz, amelyek segíthetnek. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) segítségével megbecsülheti a telepített erőforrások költségét. A portál számlázási erőforrásait és a számlázási REST API-kat is használhatja a jövőbeli költségek becslésére a jelenlegi fogyasztás alapján.

>Első **lépések**: az [Azure-beli felhasználás API áttekintése](../../cost-management-billing/manage/consumption-api-overview.md).