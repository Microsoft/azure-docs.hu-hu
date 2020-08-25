---
title: Az időszakos kimenő kapcsolatok hibáinak elhárítása a Azure App Serviceban
description: Az időszakos kapcsolati hibák és a kapcsolódó teljesítménnyel kapcsolatos problémák elhárítása Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 39073169fbc4558492a47f78f0840a0e314b3ee8
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763558"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Az időszakos kimenő kapcsolatok hibáinak elhárítása a Azure App Serviceban

Ez a cikk segítséget nyújt az időszakos kapcsolati hibák és a [Azure app Service](https://docs.microsoft.com/azure/app-service/overview)kapcsolódó teljesítménybeli problémák elhárításában. Ez a témakör további információkat nyújt a és a hibaelhárítási módszereiről, valamint a forrásoldali hálózati fordítási (SNAT) portok kimerítéséről. Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az [MSDN Azure-beli szakértőkkel és a stack overflow fórumokkal](https://azure.microsoft.com/support/forums/). Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.

## <a name="symptoms"></a>Hibajelenségek

Az Azure app Service-ben üzemeltetett alkalmazások és függvények a következő tünetek közül egyet vagy többet mutathatnak:

* Lassú válaszidő a szolgáltatási csomag összes példányán vagy egy részén.
* Időszakos 5xx vagy **hibás átjáróval** kapcsolatos hibák
* Időtúllépési hibaüzenetek
* Nem lehet csatlakozni külső végpontokhoz (például SQLDB, Service Fabric, egyéb app Services stb.)

## <a name="cause"></a>Ok

Ezen tünetek egyik fő oka, hogy az alkalmazás példánya nem tud új kapcsolódást nyitni a külső végponthoz, mert elérte a következő korlátozások valamelyikét:

* TCP-kapcsolatok: az elvégezhető kimenő kapcsolatok száma korlátozott. Ez a használt munkavégző méretével van társítva.
* SNAT-portok: az Azure-beli [Kimenő kapcsolatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)esetében az Azure a forrás hálózati CÍMFORDÍTÁS (SNAT) és egy Load Balancer (az ügyfelek számára nem elérhető) használatával kommunikál az Azure-on kívüli végpontokkal a nyilvános IP-címeken. Az Azure app Service minden példánya eredetileg előre lefoglalt számú **128** SNAT-portot kap. Ez a korlát befolyásolja a kapcsolatok megnyitását ugyanahhoz a gazdagéphez és port kombinációhoz. Ha az alkalmazás a címek és a portok kombinációinak együttes használatával hoz létre kapcsolatokat, nem fogja használni a SNAT-portokat. A rendszer akkor használja a SNAT-portokat, ha ismétlődő hívásokat végez ugyanahhoz a címnek és port kombinációhoz. A portok felszabadítása után a port igény szerint újra felhasználható. Az Azure hálózati terheléselosztó csak 4 perc várakozás után visszaállítja a SNAT-portot a lezárt kapcsolatoktól.

Amikor az alkalmazások és a függvények gyorsan megnyitnak egy új csatlakozást, gyorsan kihasználhatják az 128-es portok előre lefoglalt kvótáját. Ezután le lesznek tiltva, amíg egy új SNAT-port elérhetővé válik, vagy a további SNAT-portok dinamikusan kiosztásával, vagy egy visszaigényelt SNAT-port újbóli használatával. Azok az alkalmazások vagy függvények, amelyek nem tudnak új kapcsolatokat létrehozni, a jelen cikk a **jelenségek** című szakaszában ismertetett problémák valamelyikével kezdődnek.

## <a name="avoiding-the-problem"></a>A probléma elkerülése

Ha a cél egy olyan Azure-szolgáltatás, amely támogatja a szolgáltatási végpontokat, az SNAT-portok kimerülésével kapcsolatos problémákat a [regionális VNet integrációs](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) és szolgáltatási végpontok vagy privát végpontok használatával lehet elkerülni. Ha regionális VNet-integrációt használ, és az integrációs alhálózaton helyezi el a szolgáltatási végpontokat, az alkalmazás kimenő forgalma ezen szolgáltatásokhoz nem lesz kimenő SNAT-korlátozás. Hasonlóképpen, ha regionális VNet-integrációt és privát végpontokat használ, akkor nem lesz kimenő SNAT portja az adott célhelyre. 

A SNAT-port problémájának elkerülése azt jelenti, hogy az új kapcsolatok ismételt létrehozását nem kell ismétlődő módon létrehozni ugyanahhoz a gazdagéphez és porthoz.

Az SNAT-portok kimerülését csökkentő általános stratégiákat az Azure-dokumentáció **kimenő kapcsolatainak** [problémamegoldó szakasza](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) tárgyalja. Az alábbi stratégiák az Azure app Service-ben üzemeltetett alkalmazásokra és funkciókra vonatkoznak.

### <a name="modify-the-application-to-use-connection-pooling"></a>Az alkalmazás módosítása a kapcsolatok készletezésének használatára

* A HTTP-kapcsolatok készletezéséhez tekintse át [a készlet http-kapcsolatait a HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* A SQL Server kapcsolatok készletezésével kapcsolatos információkért tekintse át [SQL Server kapcsolatok készletezése (ADO.net)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)című témakört.
* Az Entity Framework-alkalmazásokkal való készletezés megvalósításához tekintse át az [DbContext-készletezést](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Az alábbi hivatkozás a kapcsolatok készletezésének különböző megoldási verem általi megvalósítására szolgál.

#### <a name="node"></a>Csomópont

Alapértelmezés szerint a NodeJS-kapcsolatok nem maradnak életben. Az alábbiakban láthatók a kapcsolatok készletezésének népszerű adatbázisai és csomagjai, amelyek példákat tartalmaznak a megvalósítására.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-Alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v 13.9.0 dokumentációja](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Az alábbiakban a JDBC-kapcsolatok készletezéséhez használt népszerű kódtárak találhatók, amelyek példákat tartalmaznak a megvalósítására: JDBC-kapcsolatok készletezése.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP-kapcsolatok készletezése

* [Apache-kapcsolatok kezelése](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [PoolingHttpClientConnectionManager osztály](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Habár a PHP nem támogatja a kapcsolatok készletezését, kipróbálhatja az állandó adatbázis-kapcsolatokat a háttér-kiszolgálóval.
 
* MySQL-kiszolgáló

   * [Mysqli kapcsolatok](https://www.php.net/manual/mysqli.quickstart.connections.php) az újabb verziókhoz
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) a PHP régebbi verzióihoz

* Egyéb adatforrások

   * [PHP-kapcsolatok kezelése](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (Megjegyzés: a SQLAlchemy a MicrosoftSQL-kiszolgálón kívül más adatbázisokkal is használható)
* [Http életben tartás](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(életben tartási munkamenet [-objektumok](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)használata esetén a Keep-Alive automatikus).

Más környezetek esetén tekintse át a szolgáltatót vagy az illesztőprogram-specifikus dokumentumokat a kapcsolatok készletezésének megvalósításához az alkalmazásokban.

### <a name="modify-the-application-to-reuse-connections"></a>Az alkalmazás módosítása a kapcsolatok újrafelhasználásához

*  További mutatókkal és példákkal az Azure functions kapcsolatainak kezeléséhez tekintse át a [Azure functions a kapcsolatok kezelése című részt](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Az alkalmazás módosítása kevésbé agresszív újrapróbálkozási logika használatára

* További útmutatást és példákat az [újrapróbálkozási minta](https://docs.microsoft.com/azure/architecture/patterns/retry)áttekintése című témakörben talál.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>A kimenő Üresjárati időkorlát alaphelyzetbe állítása a Keepalives használatával

* A Node.js alkalmazások Keepalives megvalósításához tekintse át a [saját Node-alkalmazást, amely túlzott kimenő hívásokat tesz szükségessé](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>A App Service vonatkozó további útmutatás:

* A [terhelési tesztnek](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) valós adatátviteli sebességgel kell szimulálnia a valós globális adatértékeket. Az alkalmazások és függvények tesztelése a valós terhelések alatt az idő előtt azonosíthatja és megoldhatja a SNAT-portok kimerülésével kapcsolatos problémákat.
* Győződjön meg arról, hogy a háttér-szolgáltatások gyorsan adnak vissza válaszokat. A Azure SQL Database kapcsolatos teljesítménnyel kapcsolatos problémák elhárításához tekintse át az [Intelligent Insights-Azure SQL Database teljesítményproblémák elhárítása](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)című témakört.
* Bővítse a App Service tervet több példányra. További információ a skálázásról: [alkalmazások méretezése Azure app Serviceban](https://docs.microsoft.com/azure/app-service/manage-scale-up). Az App Service-csomagokban minden feldolgozói példány több SNAT-portot foglal le. Ha több példányon terjeszti a használatot, a SNAT-portok használata a 100-as kimenő kapcsolatok ajánlott korlátja alá kerül, egyedi távoli végponton.
* Érdemes lehet áthelyezni [app Service Environment (](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)beadási), ahol egyetlen kimenő IP-címet adott meg, és a kapcsolatok és SNAT portok korlátai jóval magasabbak. A beosztásban a SNAT-portok száma az [Azure Load Balancer előfoglalási táblázatán](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatporttable) alapul – így például az 1-50-es feldolgozói példányokkal rendelkező központnak 1024 előre lefoglalt 512 51-100 portja van.

A kimenő TCP-korlátok elkerülése könnyebben megoldható, mivel a korlátokat a feldolgozók mérete határozza meg. Megtekintheti a [homokozóban futó virtuális gépek numerikus korlátait – TCP-kapcsolatok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Korlát neve|Leírás|Kicsi (a1)|Közepes (a2)|Nagyméretű (a3)|Elkülönített rétegek|
|---|---|---|---|---|---|
|Kapcsolatok|Kapcsolatok száma a teljes virtuális gépen|1920|3968|8064|16000|

A kimenő TCP-korlátok elkerülése érdekében növelheti a feldolgozók méretét, vagy horizontális felskálázást végez.

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenő kapcsolatok két típusának ismerete, valamint az alkalmazás működésének megismerése megkönnyíti a hibakeresést. Ha tudja, hogy az alkalmazás számos hívást kezdeményez ugyanazon a Storage-fiókon, akkor SNAT-korlátot lehet feltételezni. Ha az alkalmazás nagy mennyiségű hívást hoz létre a végpontok számára az interneten keresztül, akkor azt gyanítja, hogy eléri a virtuális gép korlátját.

Ha nem tudja, hogy az alkalmazás viselkedése elég gyors legyen, néhány eszköz és eljárás is elérhető a App Serviceban, hogy segítse a meghatározást.

### <a name="find-snat-port-allocation-information"></a>SNAT-port foglalási információinak keresése

[App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) segítségével megkeresheti a SNAT-portok foglalási adatait, és megfigyelheti egy app Service-hely SNAT-portok foglalási metrikáját. A SNAT-portok foglalási információinak megkereséséhez kövesse az alábbi lépéseket:

1. App Service diagnosztika eléréséhez navigáljon a App Service webalkalmazáshoz vagy App Service Environment a [Azure Portal](https://portal.azure.com/). A bal oldali navigációs sávon válassza a **problémák diagnosztizálása és megoldása**lehetőséget.
2. Rendelkezésre állás és teljesítmény kategória kiválasztása
3. Válassza a SNAT port kimerülése csempét a kategória alatt található elérhető csempék listájában. A gyakorlat az, hogy 128 alatt maradjon.
Ha szüksége van rá, továbbra is megnyithatja a támogatási jegyet, és a támogatási szakembernek az Ön számára készült mérőszámot fogja kapni.

Vegye figyelembe, hogy mivel a SNAT-port használata nem érhető el metrikaként, nem lehetséges az automatikus skálázás a SNAT-port használata alapján, vagy az SNAT-portok foglalási metrikája alapján.

### <a name="tcp-connections-and-snat-ports"></a>TCP-kapcsolatok és SNAT portok

A TCP-kapcsolatok és a SNAT portok nem közvetlenül kapcsolódnak egymáshoz. A TCP-kapcsolatok használati detektora az App Service-helyek diagnosztizálási és megoldási problémák paneljén található. Keresse meg a "TCP-kapcsolatok" kifejezést a kereséshez.

* A SNAT-portok csak külső hálózati folyamatokhoz használatosak, míg a teljes TCP-kapcsolat helyi visszacsatolási kapcsolatokat tartalmaz.
* Egy SNAT-portot különböző folyamatok oszthatnak meg, ha a folyamatok eltérőek a protokollok, az IP-címek vagy a portok között. A TCP-kapcsolatok metrika minden TCP-kapcsolatot megszámol.
* A TCP-kapcsolatok korlátja a feldolgozói példány szintjén történik. Az Azure hálózati kimeneti terheléselosztás nem használja a TCP-kapcsolatok metrikáját a SNAT-portok korlátozásához.
* A TCP-kapcsolatok korlátozásait a [homokozóban futó virtuális gépek numerikus korlátai – TCP-kapcsolatok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) című cikk írja le.

|Korlát neve|Leírás|Kicsi (a1)|Közepes (a2)|Nagyméretű (a3)|Elkülönített rétegek|
|---|---|---|---|---|---|
|Kapcsolatok|Kapcsolatok száma a teljes virtuális gépen|1920|3968|8064|16000|

### <a name="webjobs-and-database-connections"></a>Webjobs-és adatbázis-kapcsolatok
 
Ha SNAT-portok vannak kimerítve, ahol a webjobs nem tud csatlakozni a SQL Databasehoz, nincs olyan metrika, amely azt mutatja, hogy hány kapcsolat van megnyitva az egyes webalkalmazási folyamatokban. A problémás Webjobs megkereséséhez helyezzen át több webfeladatot egy másik App Service tervbe, és ellenőrizze, hogy a helyzet javul-e, vagy ha a probléma továbbra is a csomagok egyikében marad. Ismételje meg a folyamatot, amíg meg nem találja a problémás Webjobs.

### <a name="using-snat-ports-sooner"></a>SNAT-portok használata hamarabb

Az Azure-beállítások nem módosíthatók úgy, hogy hamarabb kibocsássák a használt SNAT-portokat, mivel az összes SNAT-portot a rendszer az alábbi feltételek szerint szabadítja fel, és a viselkedés a tervezés szerint történik.
 
* Ha a kiszolgáló vagy az ügyfél FINACK küld, a SNAT-port 240 másodperc után lesz [felszabadítva](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) .
* Ha az első látható, a SNAT-port 15 másodperc elteltével fog megjelenni.
* Ha elérte az üresjárati időtúllépést, a rendszer felszabadítja a portot.
 
## <a name="additional-information"></a>További információ

* [SNAT App Service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [A lassú alkalmazások teljesítményével kapcsolatos hibák elhárítása Azure App Service](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
