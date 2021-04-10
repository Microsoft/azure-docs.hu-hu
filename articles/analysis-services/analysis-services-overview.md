---
title: Mi az az Azure Analysis Services | Microsoft Docs?
description: Ismerkedjen meg Azure Analysis Servicesával, amely egy teljes körűen felügyelt platform, amely nagyvállalati szintű adatmodelleket biztosít a felhőben.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: overview
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: c3b95b92f2b76e669b4e207163f76f8cec2d3244
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731683"
---
# <a name="what-is-azure-analysis-services"></a>Mi az Azure Analysis Services?

![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Az Azure Analysis Services egy teljes körűen felügyelt platformszolgáltatás (PaaS), amely vállalati szintű adatmodellezésre biztosít lehetőséget a felhőben. E szolgáltatás adategyesítési és modellezési funkcióival több forrásból egyesítheti az adatokat, mérőszámokat határozhat meg, és egyetlen, megbízható, táblázatos szemantikai adatmodellben biztosíthatja az adatok védelmét. Az adatmodell megkönnyíti és gyorsabbá teszi a felhasználók számára az ad hoc adatelemzést olyan eszközökkel, mint a Power BI és az Excel.

![Adatforrások](./media/analysis-services-overview/aas-overview-overall.png)

**Videó:** Tekintse meg az [Azure Analysis Services – áttekintés](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) videót arról, hogyan illik az Azure Analysis Services a Microsoft általános BI-képességei közé.

## <a name="get-up-and-running-quickly"></a>Gyors beállítás és használat

Percek alatt [létrehozhat egy kiszolgálót](analysis-services-create-server.md) az Azure Portalon. Az Azure Resource Manager-[sablonok](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) és a PowerShell használatával pedig deklaratív sablonokkal hozhat létre kiszolgálókat. Egyetlen sablonnal kiszolgálói erőforrásokat is üzembe helyezhet, egyéb Azure-összetevőkkel (például tárfiókokkal és az Azure Functions szolgáltatással) együtt. 

**Videó:** Tekintse meg [az üzembe helyezés automatizálásával](https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation) foglalkozó videót arról, hogyan használhatja az Azure Automationt a kiszolgálók létrehozásának felgyorsítására.

Az Azure Analysis Services számos Azure-szolgáltatással integrálható, így kifinomult elemzési megoldásokat hozhat létre. Az [Azure Active Directoryval](../active-directory/fundamentals/active-directory-whatis.md) való integráció biztonságos, szerepköralapú hozzáférést biztosít a kritikus fontosságú adatokhoz. [Azure Data Factory-folyamatokba](../data-factory/introduction.md) integrálható egy olyan tevékenység bevonásával, amely adatokat tölt be a modellbe. Az [Azure Automation](../automation/automation-intro.md) és az [Azure Functions](../azure-functions/functions-overview.md) egyéni kódot használó modellek egyszerűbb vezénylésére használható. 

## <a name="the-right-tier-when-you-need-it"></a>A megfelelő szint – igény szerint

Az Azure Analysis Services **fejlesztői**, **alap-** és **standard** szinten is elérhető. Az egyes csomagokon belül a költségek a feldolgozási teljesítmény, a lekérdezés-feldolgozási egységek (Qpu) és a memória méretétől függően változnak. Amikor létrehoz egy kiszolgálót, egy adott szinten belül választ ki egy csomagot. A csomagokat a szinten belül magasabb vagy alacsonyabb szintre módosíthatja, vagy frissíthet magasabb szintre, de magasabb szintről nem léphet vissza egy alacsonyabbra.

### <a name="developer-tier"></a>Fejlesztői szint

Ezt a szintet kiértékeléshez, valamint fejlesztési és tesztelési forgatókönyvekhez ajánljuk. Egyetlen csomagban tartalmazza a standard szintű csomagéval megegyező funkciókat, de korlátozott feldolgozási teljesítménnyel, QPU-val és memóriamérettel rendelkezik. A lekérdezési replika méretezése *nem érhető el* ehhez a réteghez. Ehhez a szinthez nem tartozik SLA.

|Felkészülés  |QPU-k  |Memória (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Alapszint

Ez a csomag kisebb táblázatos modellekkel, korlátozott felhasználói párhuzamosságtal és egyszerű adatfrissítési követelményekkel rendelkező éles megoldások esetében ajánlott. A lekérdezési replika méretezése *nem érhető el* ehhez a réteghez. A perspektívák, a több partíció használata és a DirectQuery táblázatosmodell-funkciók *nem támogatottak* ezen a szinten.  

|Felkészülés  |QPU-k  |Memória (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    16     |

### <a name="standard-tier"></a>Standard csomag

Ez a szint olyan létfontosságú, éles környezetben használt alkalmazásokhoz ideális, amelyek rugalmasságot követelnek meg a párhuzamos felhasználói tevékenységekre vonatkozóan, és amelyek gyorsan növekvő adatmodelleket használnak. Támogatja a speciális adatfrissítést az adatmodellek közel valós idejű frissítése érdekében, valamint az összes táblázatos modellezési funkciót is.

|Felkészülés  |QPU-k  |Memória (GB)  |
|---------|---------|---------|
|S0    |    40     |    10     |
|S1    |    100     |    25     |
|S2    |    200     |    50     |
|S4    |    400     |    100     |
|S8 <sup>[1](#naar)</sup>, <sup>[2](#rec)</sup>   |    320    |    200     |
|S9 <sup>[1](#naar)</sup>, <sup>[2](#rec)</sup>    |    640    |    400     |
|<sup> [1](#naar) . S8v2</sup>   |    640     |    200     |
|<sup> [1](#naar) . S9v2</sup>    |    1280    |    400     |

<a name="naar">1</a> – nem érhető el minden régióban.   
<a name="rec">2</a> – az S8 és az S9 [elavult](https://azure.microsoft.com/updates/azure-s8-and-s9-analysis-services-skus-retiring-on-31-august-2023/). a v2 ajánlott. 

## <a name="availability-by-region"></a>Rendelkezésre állás régiónként

Az Azure Analysis Services a világ összes régiójában támogatott. A támogatott csomagok és lekérdezésreplikák a választott régiótól függően változnak. A csomagok és lekérdezésreplikák elérhetősége az egyes régiókban jellemző igények és az elérhető erőforrások függvényében változhat. 

### <a name="americas"></a>Észak-, Dél- és Közép-Amerika

|Region  | Támogatott csomagok | Lekérdezésreplikák (csak standard csomagok esetén) |
|---------|---------|:---------:|
|Dél-Brazília     |    B1, B2, S0, S1, S2, S4, D1     |     1    |
|Közép-Kanada    |     B1, B2, S0, S1, S2, S4, D1    |     1    |
|Közép-Kanada     |     S8v2, S9v2   |    1    |
|USA keleti régiója     |     B1, B2, S0, S1, S2, S4, D1    |    1     |
|USA keleti régiója     |     S8v2, S9v2   |    1    |
|USA 2. keleti régiója     |     B1, B2, S0, S1, S2, S4, D1   |    7    |
|USA 2. keleti régiója     |     S8v2, S9v2   |    1    |
|USA északi középső régiója     |     B1, B2, S0, S1, S2, S4, D1     |    1     |
|USA északi középső régiója     |     S8v2, S9v2    |    1     |
|Az USA középső régiója     |    B1, B2, S0, S1, S2, S4, D1     |    1     |
|Az USA középső régiója     |    S8v2, S9v2     |    1     |
|USA déli középső régiója     |    B1, B2, S0, S1, S2, S4, D1     |    1     |
|USA déli középső régiója     |    S8v2, S9v2     |    1     |
|USA nyugati középső régiója   |     B1, B2, S0, S1, S2, S4, D1    |    3     |
|USA nyugati régiója     |    B1, B2, S0, S1, S2, S4, D1    |    7   |
|USA nyugati régiója     |    S8v2, S9v2   |    2  |
|USA 2. nyugati régiója    |    B1, B2, S0, S1, S2, S4, D1    |    3   |
|USA 2. nyugati régiója    |    S8v2, S9v2  |    1     |

### <a name="europe"></a>Európa

|Region  | Támogatott csomagok | Lekérdezésreplikák (csak standard csomagok esetén) |
|---------|---------|:---------:|
|Észak-Európa     |    B1, B2, S0, S1, S2, S4, D1      |    7     |
|Észak-Európa     |    S8v2, S9v2      |    3     |
|Az Egyesült Királyság déli régiója     |    B1, B2, S0, S1, S2, S4, D1      |     1    |
|Nyugat-Európa     |    B1, B2, S0, S1, S2, S4, D1   |    7    |
|Nyugat-Európa    |   S8v2, S9v2  |  1  |

### <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség 

|Region  | Támogatott csomagok | Lekérdezésreplikák (csak standard csomagok esetén) |
|---------|---------|:---------:|
|Kelet-Ausztrália     |    B1, B2, S0, S1, S2, S4     |    3     |
|Kelet-Ausztrália     |    S8v2, S9v2    |    1     |
|Délkelet-Ausztrália     | B1, B2, S0, S1, S2, S4, D1       |    1     |
|Kelet-Japán     |   B1, B2, S0, S1, S2, S4, D1       |    1     |
|Kelet-Japán     |    S8v2, S9v2    |    1     |
|Délkelet-Ázsia     |     B1, B2, S0, S1, S2, S4, D1     |   1      |
|Délkelet-Ázsia     |     S8v2, S9v2     |   1      |
|Nyugat-India     |    B1, B2, S0, S1, S2, S4, D1     |    1     |

## <a name="scale-to-your-needs"></a>Igényei szerint méretezhető

### <a name="scale-updown-pause-and-resume"></a>Vertikális fel- vagy leskálázás, felfüggesztés és folytatás

Fel- vagy leskálázhatja, illetve szüneteltetheti a kiszolgálóját. Ehhez használhatja az Azure Portalt, illetve a PowerShellt, amely azonnali és teljes körű vezérlést biztosít. A fizetés használat alapján történik.  

### <a name="scale-out-resources-for-fast-query-responses"></a>Erőforrások kibővítése a lekérdezési válaszadás felgyorsítása érdekében

Kibővített erőforrások esetében az ügyféllekérdezések több *lekérdezésreplikára* vannak kiosztva az adott lekérdezési készletben. A lekérdezésreplikák a táblázatos modellek szinkronizált másolataival rendelkeznek. A lekérdezésekből származó számítási feladatok felosztásával csökkenthető a válaszidő az erőforrás-igényesebb feladatok esetén. A modellfeldolgozási műveletek elkülöníthetők a lekérdezési készletből, ezáltal biztosítható, hogy a feldolgozási műveletek ne legyenek negatív hatással az ügyféllekérdezésekre. 

A létrehozott lekérdezési készlet legfeljebb hét további lekérdezésreplikát tartalmazhat (a kiszolgálóval együtt összesen nyolcat). A lekérdezési készletben elhelyezhető lekérdezésreplikák száma a kiválasztott csomagtól és régiótól függ. A lekérdezésreplikák nem helyezhetők el a kiszolgáló régióján kívül. A lekérdezésreplikák díjszabása megegyezik a kiszolgálóéval.

A szint módosításához hasonlóan a lekérdezésreplikák is igény szerint bővíthetők. A bővítést a portálon vagy REST API-k használatával konfigurálhatja. További tudnivalókért lásd az [Azure Analysis Services kibővítését](analysis-services-scale-out.md) ismertető témakört.

## <a name="pricing"></a>Díjszabás

A teljes díj számos tényezőtől függ. Például a kiválasztott régió, a szintek, a lekérdezési replikák és a Szüneteltetés/folytatás. Az adott régióra jellemző díjszabást az [Azure Analysis Services díjszabását](https://azure.microsoft.com/pricing/details/analysis-services/) ismertető részben szereplő díjkalkulátorral határozhatja meg. Az eszköz egyetlen kiszolgálóra és régióra számolja ki a díjszabást. Vegye figyelembe, hogy a lekérdezésreplikák díjszabása megegyezik a kiszolgálóéval. 

## <a name="built-on-sql-server-analysis-services"></a>Az SQL Server Analysis Servicesre épül

Az Azure Analysis Services kompatibilis az SQL Server Analysis Services Enterprise Editionben már meglévő számos nagyszerű funkcióval. Az Azure Analysis Services támogatja a táblázatos modelleket az 1200-as és az ennél magasabb [kompatibilitási szinteken](/analysis-services/tabular-models/compatibility-level-for-tabular-models-in-analysis-services). A táblázatos modellek olyan relációs modellezési szerkezetek (modell, táblák, oszlopok), amelyek táblázatos metaadatok objektumdefinícióiban vannak meghatározva a táblázatos modell parancsnyelvén (TMSL) és a táblázatos objektummodell (TOM) kódjában. A partíciók, a perspektívák, a sor szintű biztonság, a kétirányú kapcsolatok és a fordítások mind támogatottak \* . A többdimenziós modellek és a SharePoint rendszerhez készült PowerPivot Azure Analysis Services *nem* támogatottak.

A táblázatos modelleket a memóriában tárolt és a DirectQuery módok egyaránt támogatják. A memóriában tárolt mód (alapértelmezett) táblázatos modelljei több adatforrás használatát is támogatják. Nagy mennyiségű adatok esetében ez a mód biztosítja a leggyorsabb lekérdezési választ, mivel a modell adatai nagymértékben tömörítettek, és a memóriában vannak gyorsítótárazva. Összetett adatkészletek és lekérdezések esetében szintén ez a mód biztosítja a legnagyobb rugalmasságot. 

A particionálás révén elérhetővé válik a növekményes betöltés, növelhető a párhuzamos folyamatkezelés mértéke, és csökkenthető a memóriahasználat. Ezen kívül olyan speciális adatmodellezési funkciók is támogatottak, mint például a számított táblázatok és az összes DAX-függvény. Az adatforrásokból származó, gyorsítótárazott adatok frissítéséhez a memóriában tárolt modelleket frissíteni kell (fel kell dolgozni). Az Azure-szolgáltatás egyszerű támogatása, a PowerShell, a TOM, a TMSL és a REST használatával végzett felügyelet nélküli frissítési műveletekkel rugalmasan biztosítható, hogy a modell adatai mindig naprakészek legyenek. 

A tároláshoz és a lekérdezések futtatásához a DirectQuery mód* a háttérbeli relációs adatbázist használja. A rendkívül nagy adathalmazok egyetlen SQL Serverban, SQL Server adattárházban, Azure SQL Databaseban, az Azure szinapszis Analyticsben, az Oracle-ben és az Teradata-adatforrásokban is támogatottak. A háttérbeli adatkészletek mérete meghaladhatja a rendelkezésre álló kiszolgálói erőforrás memóriájának méretét. Nincs szükség az összetett adatmodellek frissítésére. Vannak olyan korlátozások is, mint például a korlátozott adatforrások, a DAX-képlet korlátozásai, és egyes speciális adatmodellezési funkciók nem támogatottak. Az Ön számára legmegfelelőbb mód kiválasztása előtt tekintse át a [DirectQuery mód](/analysis-services/tabular-models/directquery-mode-ssas-tabular) szakaszban leírtakat.

\* A rendelkezésre álló funkciók köre a választott szinttől függ.

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az Azure Analysis Services táblázatos modelljei számos különféle adatforrást támogatnak, az egyszerű szöveges fájloktól kezdve az Azure Data Lake Store big data típusú adataiig. További információ: [Támogatott adatforrások az Azure Analysis Servicesben](analysis-services-datasource.md).

## <a name="compatibility-level"></a>Kompatibilitási szint

A kompatibilitási szint a Analysis Services motor kiadás-specifikus viselkedésére utal. Az Azure Analysis Services támogatja a táblázatos modelleket az 1200-as és az ennél magasabb kompatibilitási szinteken. További információért lásd: [a táblázatos modellek kompatibilitási szintje](/analysis-services/tabular-models/compatibility-level-for-tabular-models-in-analysis-services).


## <a name="your-data-is-secure"></a>Az adatok biztonságban vannak

Az Azure Analysis Services többszintű biztonságot nyújt bizalmas adatai számára. Azure-szolgáltatásként a Analysis Services biztosítja az elosztott szolgáltatásmegtagadási (DDoS) támadások **alapszintű** védelmét, amely az Azure platform részeként automatikusan engedélyezve van. További információ: [Azure DDoS Protection standard Overview](../ddos-protection/ddos-protection-overview.md). 

A kiszolgáló szintjén a Analysis Services tűzfalat, Azure-hitelesítést, kiszolgálói rendszergazdai szerepköröket és Server-Side titkosítást biztosít. Az adatmodell szintjén a felhasználói szerepkörök, a sorcsoport és az objektumorientált biztonság gondoskodik az adatai biztonságáról, és azokat csak azok a felhasználók látják, akik számára a célja.

### <a name="firewall"></a>Firewall

Az Azure Analysis Services tűzfala minden olyan ügyfélkapcsolatot blokkol, amelyek IP-címe nincs megadva a szabályokban. Alapértelmezés szerint a tűzfalbeállítások nincs engedélyezve az új kiszolgálókon. Az ajánlott tűzfalbeállítások engedélyezve van, és a szabályok konfigurálása a kiszolgáló létesítési parancsfájljának részeként vagy a portálon közvetlenül a kiszolgáló létrehozása után történik. Az engedélyezett IP-címeket meghatározó szabályok egyesével, ügyfél IP-címenként vagy tartományonként is konfigurálhatók. A Power BI- (szolgáltatási) kapcsolatok szintén engedélyezhetők vagy blokkolhatók. A tűzfalat és a szabályokat a portálon vagy a PowerShell használatával konfigurálhatja. További információ: [Kiszolgálószintű tűzfal konfigurálása](analysis-services-qs-firewall.md).

### <a name="authentication"></a>Hitelesítés

A felhasználóhitelesítést az [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) kezeli. A bejelentkezéshez a felhasználók céges fiókidentitást használnak, amely szerepköralapú hozzáféréssel rendelkezik az adatbázishoz. A felhasználói identitásoknak a kiszolgálót tartalmazó előfizetés alapértelmezett Azure Active Directoryja tagjainak kell lenniük. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

### <a name="data-security"></a>Adatbiztonság

Az Azure Analysis Services az Azure Blob Storage segítségével őrzi meg az Analysis Services-adatbázisok tárterületét és metaadatait. A Blobon belüli adatfájlok az [Azure Blob kiszolgálóoldali titkosításával (SSE)](../storage/common/storage-service-encryption.md) vannak titkosítva. Közvetlen lekérdezési mód használatakor a rendszer csak a metaadatokat tárolja. A tényleges adatok az adatforrásból érhetőek el a lekérdezés során egy titkosított protokollon keresztül.

A [helyszíni adatátjáró](analysis-services-gateway.md) telepítésével és konfigurálásával biztosíthatja a helyszíni céges adatforrásokhoz való biztonságos hozzáférést. Az átjárók hozzáférést biztosítanak az adatokhoz DirectQuery és memóriában tárolt módban is.

### <a name="roles"></a>Szerepkörök

Az Analysis Services [szerepköralapú hitelesítés](/analysis-services/tabular-models/roles-ssas-tabular) használatával biztosítja a kiszolgálóhoz, valamint a modelladatbázis műveleteihez, objektumaihoz és adataihoz való hozzáférést. A felhasználók a hozzájuk rendelt szerepkörön belüli Azure AD-felhasználói fiókjaikkal férnek hozzá az adott kiszolgálóhoz vagy adatbázishoz A kiszolgálói rendszergazdai szerepkör a kiszolgálói erőforrás szintjén van meghatározva. Alapértelmezés szerint a kiszolgáló létrehozásakor használt fiók automatikusan bekerül a Kiszolgálói rendszergazdák szerepkörbe. További felhasználói vagy csoportfiókok a portál, SSMS vagy PowerShell használatával vehetők fel.
  
Az adatlekérdezéseket végző nem rendszergazda végfelhasználók hozzáférését a rendszer adatbázis-szerepkörökön keresztül biztosítja. A rendszer az adatbázis-szerepkört külön objektumként hozza létre az adatbázisban, és csak arra az adatbázisra vonatkozik, amelyben az adott szerepkör létre lett hozva. Az adatbázis-szerepkörök (adatbázis-) Rendszergazda, Olvasás, valamint Olvasás és feldolgozás engedélyekkel vannak definiálva. Felhasználói vagy csoportfiókok SSMS vagy PowerShell használatával vehetők fel.

### <a name="row-level-security"></a>Sorszintű biztonság

A táblázatos modellek minden kompatibilitási szinten támogatják a sorszintű biztonságot. A sorszintű biztonság modellbeli konfigurálása olyan DAX-kifejezések használatával történik, amelyek egy adott tábla sorait és egy, a felhasználók által lekérdezhető kapcsolódó tábla tetszőleges irányban lévő sorait határozzák meg. A DAX-kifejezéseket használó sorszűrők Olvasás és Olvasás és feldolgozás engedélyekkel vannak definiálva. 

### <a name="object-level-security"></a>Objektumszintű biztonság 

A táblázatos modellek az 1400-as kompatibilitási szinten támogatják az objektumszintű biztonságot, amely magában foglalja a táblázat- és az oszlopszintű biztonságot is. Az objektumszintű biztonság beállítása a Model.bim fájl JSON-alapú metaadataiban történik, TMSL vagy TOM használatával. További információ: [Objektumszintű biztonság](/analysis-services/tabular-models/object-level-security).

### <a name="automation-through-service-principals"></a>Automatizálás az egyszerű szolgáltatásokon keresztül

A szolgáltatásnevek olyan Azure Active Directory-alkalmazáserőforrások, amelyeket felügyelet nélküli erőforrás- vagy szolgáltatásszintű műveletek végrehajtásához hozhat létre a bérlőn belül. A szolgáltatásnevek az Azure Automationben, a PowerShell felügyelet nélküli módjában, egyéni ügyfélalkalmazásokban és webalkalmazásokban használhatók gyakori feladatok (például adatfrissítés, vertikális fel- vagy leskálázás, szüneteltetés/folytatás) automatizálására. Az engedélyek szerepkörtagságon keresztül rendelhetők hozzá a szolgáltatásnevekhez További információ: [Automatizálás szolgáltatásnevekkel](analysis-services-service-principal.md).

### <a name="azure-governance"></a>Azure Governance

Az Azure Analysis Servicesre a [Microsoft Online Services feltételei](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) és a [Microsoft adatvédelmi nyilatkozata](https://privacy.microsoft.com/privacystatement) vonatkozik.
További információk az Azure Securityről: [Microsoft biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter).

## <a name="use-the-tools-you-already-know"></a>A már ismerős eszközöket használhatja

![BI-fejlesztői eszközök](./media/analysis-services-overview/aas-overview-dev-tools.png)

### <a name="visual-studio"></a>Visual Studio

Modellek fejlesztése és üzembe helyezése a Visual Studióval Analysis Services-projektekkel. A Analysis Services projects bővítmény olyan sablonokat és varázslókat tartalmaz, amelyekkel gyorsan elvégezheti a munkát. A Visual Studióban a Model authoring Environment mostantól tartalmazza a modern Get adatforrások lekérdezését és az adategyesítési funkciókat a táblázatos 1400 és a magasabb modellekhez. Ha már ismeri a Power BI Desktop és az Excel 2016 Adatok lekérése funkcióját, akkor tudja, milyen egyszerűen hozhat létre nagymértékben testre szabott adatforrás-lekérdezéseket. 

A Microsoft Analysis Services projektek ingyenes telepíthető VSIX-csomagként érhetők el. [Letöltés a Marketplace-ről](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects). A bővítmény a Visual Studio 2017-es és újabb verzióival működik együtt, beleértve az ingyenes közösségi kiadást is.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Felügyelheti kiszolgálóit és modelladatbázisait az [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) segítségével. Kapcsolódhat a felhőalapú kiszolgálóihoz. Közvetlenül az XMLA lekérdezési ablakból futtathat TMSL-szkripteket. A feladatokat e szkriptekkel és a PowerShell használatával automatizálhatja. Gyakran jelennek meg új szolgáltatások és funkciók – az SSMS havonta frissül.

### <a name="open-source-tools"></a>Nyílt forráskódú eszközök

Analysis Services az eszközöket létrehozó fejlesztők vibráló közösségét. Tekintse meg a táblázatos [szerkesztőt](https://tabulareditor.github.io/), amely egy nyílt forráskódú eszköz a táblázatos modellek létrehozásához, karbantartásához és kezeléséhez egy intuitív, egyszerűsített szerkesztő használatával. A [DAX Studio](https://daxstudio.org/)egy nagyszerű, nyílt forráskódú eszköz a DAX létrehozásához, diagnosztizálásához, teljesítményének finomhangolásához és elemzéséhez.

### <a name="powershell"></a>PowerShell

A kiszolgálói erőforrás-kezelési feladatok, például a kiszolgálói erőforrások létrehozása, a kiszolgálói műveletek felfüggesztése vagy folytatása, vagy a szolgáltatási szint (szint) módosítása Azure PowerShell parancsmagokat használ. Az olyan egyéb adatbázis-kezelési feladatok, mint például a szerepkörtagok hozzáadása vagy eltávolítása, illetve a TMSL-szkriptek feldolgozása vagy futtatása az SqlServer modul parancsmagjait használják. További információ: [Az Azure Analysis Services kezelése a PowerShell-lel](analysis-services-powershell.md).

### <a name="object-model-and-scripting"></a>Objektummodell és parancsprogramok használata

A táblázatos modellek gyors fejlesztést biztosítanak, és nagymértékben testre szabhatók. A táblázatos modellek közül a [táblázatos objektummodell](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (TOM) is használható a modellobjektumok leírásához. A TOM a JSON-ban van közzétéve a [Táblázatos modell parancsnyelv (TMSL)](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) és az AMO adatdefiníciós nyelv segítségével a [Microsoft.AnalysisServices.Tabular](/dotnet/api/microsoft.analysisservices.tabular) névtérben. 

## <a name="supports-the-latest-client-tools"></a>Támogatja a legújabb ügyféleszközöket

![Adatmegjelenítések](./media/analysis-services-overview/aas-overview-clients.png)

Az olyan modern adatáttekintési és vizualizációs eszközök, mint például a Power BI, az Excel, a Reporting Services, illetve a harmadik felektől származó eszközök egyaránt támogatottak, így interaktív és vizuálisan gazdag modelladat-elemzéseket biztosítanak a felhasználóknak. 

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

A Azure Analysis Services Azure Monitor metrikákkal van integrálva, és számos erőforrás-specifikus mérőszámot biztosít a kiszolgálók teljesítményének és állapotának figyeléséhez. További tudnivalókért lásd: [A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md). Metrikák rögzítése az [erőforrás-platform naplóival](../azure-monitor/essentials/platform-logs-overview.md). Naplózhatja és elküldheti a naplókat az [Azure Storage](https://azure.microsoft.com/services/storage/)-ba, továbbíthatja őket az [Azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket az [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite)-szolgáltatásba [Azure monitor naplókba](https://azure.microsoft.com/services/log-analytics/). További információ: [Diagnosztikai naplózás beállítása](analysis-services-logging.md).

Az Azure Analysis Services támogatja a [dinamikus felügyeleti nézetek (DMV-k)](/analysis-services/instances/use-dynamic-management-views-dmvs-to-monitor-analysis-services) használatát is. A DMV-k az SQL-szintaxis alapján kapcsolódnak azokhoz a sémasorkészletekhez, amelyek a kiszolgálópéldány metaadatait és monitorozási információit adják vissza.

## <a name="documentation"></a>Dokumentáció

Itt található az Azure Analysis Services dokumentációja. A cikkeket a böngésző képernyőjének bal oldalán látható tartalomjegyzékben keresheti meg. 

Mivel a Azure Analysis Services táblázatos modelljei sokkal ugyanazok, mint a SQL Server Analysis Services és Power BI Premium adatkészletek táblázatos modelljei, a közös adatmodellezési útmutatók széles skáláját mutatja be [Analysis Services dokumentációjában](/analysis-services/?view=azure-analysis-services-current&preserve-view=true). A megosztott Analysis Services dokumentációjában szereplő cikkek azt mutatják, hogy a Azure Analysis Servicesre a cím alatt érvényes szalagcím vonatkozik-e. A tartalomjegyzék fölött a Version választót is használhatja, hogy csak azokat a cikkeket tekintse meg, amelyek az Ön által használt platformra vonatkoznak.

![Közös dokumentáció](./media/analysis-services-overview/aas-overview-applies-to.png)

### <a name="contribute"></a>Számítunk közreműködésére!

Analysis Services dokumentáció, mint a jelen cikk, nyílt forráskódú. Ha többet szeretne megtudni arról, Hogyan járulhat hozzá, tekintse meg a [docs közreműködői útmutatóját](/contribute/). 

Az Analysis Services dokumentációja is használja a [GitHub-problémák](/teamblog/a-new-feedback-system-is-coming-to-docs) fogalmát. Visszajelzés adhat a termékről vagy a dokumentációról. Ehhez használja a cikk alján látható **Visszajelzés** gombot. A GitHub-problémák nincsenek engedélyezve a megosztott Analysis Services dokumentációjában. 

## <a name="blogs"></a>Blogok

A dolgok gyorsan változnak. Szerezze be a legfrissebb információkat a [Power bi blogon](https://powerbi.microsoft.com/blog/category/analysis-services/) és az [Azure blogon](https://azure.microsoft.com/blog/).

## <a name="community"></a>Közösség

Az Analysis Services felhasználói pezsgő közösséget alkotnak. Csatlakozzon a párbeszédhez az [Azure Analysis Services fórumon](https://aka.ms/azureanalysisservicesforum).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure ingyenes próbaverziójának kipróbálása](https://azure.microsoft.com/offers/ms-azr-0044p/)   

> [!div class="nextstepaction"]
> [Gyors útmutató: Kiszolgáló létrehozása – Portal](analysis-services-create-server.md)   

> [!div class="nextstepaction"]
> [Gyors útmutató: Kiszolgáló létrehozása – PowerShell](analysis-services-create-powershell.md)
