---
title: Biztonsági áttekintés
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Azure Cognitive Search biztonsági funkcióit a végpontok, a tartalmak és a műveletek védelméhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 954d08fa163b481393df28ae22016859badea694
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537306"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Az Azure Cognitive Search biztonsági áttekintése

Ez a cikk az Azure Cognitive Search biztonsági funkcióit ismerteti, amelyek védik a tartalmat és a műveleteket.

A keresési szolgáltatás felé irányuló bejövő kérelmek esetében a keresési szolgáltatás végpontját védő biztonsági intézkedések haladnak át: a kérelemben szereplő API-kulcsoktól a tűzfal bejövő szabályaiig a szolgáltatás teljes körű védelmét a nyilvános internetről.

A más szolgáltatásoknak küldött kimenő kérésekhez az indexelő kéri a külső forrásokból származó tartalom beolvasását. A hitelesítő adatokat megadhatja a kapcsolatok karakterláncán. A felügyelt identitást úgy is beállíthatja, hogy megbízható szolgáltatást keressen, amikor Azure Storage, Azure SQL, Cosmos DB vagy más Azure-adatforrásokból fér hozzá az adatokhoz. A felügyelt identitás a hitelesítő adatok vagy a hozzáférési kulcsok helyettesítése a kapcsolaton. További információ erről a képességről: [Csatlakozás adatforráshoz felügyelt identitás használatával](search-howto-managed-identities-data-sources.md).

A külső szolgáltatásokhoz való írási műveletek kevések: a keresési szolgáltatás a naplófájlba írja a fájlokat, és az Azure Storage-ba fogja írni, amikor az adattárakat létrehozza, megőrzi a gyorsítótárazott bővítéseket, és megőrzi a hibakeresési munkameneteket. A szolgáltatások közötti egyéb hívások, például a Cognitive Services a belső hálózaton történnek.

Tekintse meg ezt a gyors iramú videót a biztonsági architektúra és az egyes szolgáltatások kategóriáinak áttekintéséhez.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Hálózati biztonság

<a name="service-access-and-authentication"></a>

A bejövő biztonsági funkciók a keresési szolgáltatás végpontját a biztonság és a bonyolultság szintjének növelésével védik. Először is minden kérelemhez API-kulcs szükséges a hitelesített hozzáféréshez. Másodszor beállíthatja a tűzfalszabályok számára, hogy meghatározott IP-címekre korlátozza a hozzáférést. A speciális védelem érdekében a harmadik lehetőség az, hogy engedélyezze az Azure privát hivatkozását, hogy a szolgáltatás végpontját minden internetes forgalomból megvédje.

### <a name="public-access-using-api-keys"></a>Nyilvános hozzáférés API-kulcsok használatával

Alapértelmezés szerint a keresési szolgáltatás a nyilvános felhőn keresztül érhető el, kulcs alapú hitelesítéssel, rendszergazdai vagy lekérdezési hozzáféréssel a keresési szolgáltatás végpontja számára. Az érvényes kulcs beküldése igazolja, hogy a kérelem megbízható entitásból származik. A következő szakaszban a kulcs alapú hitelesítés szerepel.

### <a name="configure-ip-firewalls"></a>IP-tűzfalak konfigurálása

A keresési szolgáltatáshoz való hozzáférés további szabályozásához olyan bejövő tűzfalszabályok hozhatók létre, amelyek lehetővé teszik az adott IP-cím vagy IP-címtartomány elérését. Az összes ügyfélkapcsolatot egy engedélyezett IP-címen keresztül kell elvégezni, vagy a kapcsolat megtagadva.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Példa architektúra-diagramra az IP korlátozott hozzáféréshez":::

A portál használatával [konfigurálhatja a bejövő hozzáférést](service-configure-firewall.md).

Másik lehetőségként használhatja a felügyeleti REST API-kat is. A 2020-03-13-es API-verziótól kezdődően a [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) paraméterrel a szolgáltatáshoz való hozzáférést úgy is korlátozhatja, ha az IP-címeket, egyenként vagy egy tartományban azonosítja, és amelyekhez hozzáférést szeretne biztosítani a keresési szolgáltatáshoz.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Hálózati elkülönítés privát végponton keresztül (internetes forgalom nélkül)

Létrehozhat egy [privát végpontot](../private-link/private-endpoint-overview.md) az Azure Cognitive Search számára, amely lehetővé teszi, hogy egy [virtuális hálózaton](../virtual-network/virtual-networks-overview.md) lévő ügyfél biztonságosan hozzáférhessen egy keresési indexben lévő adathoz egy [privát hivatkozáson](../private-link/private-link-overview.md)keresztül.

A magánhálózati végpont a virtuális hálózati címtartomány IP-címét használja a keresési szolgáltatáshoz való kapcsolódáshoz. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán található privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. A VNET lehetővé teszi az erőforrások, a helyszíni hálózat és az Internet közötti biztonságos kommunikációt.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Példa architektúra-diagramra a privát végpontok eléréséhez":::

Habár ez a megoldás a legbiztonságosabb, a további szolgáltatások használata további költségeket jelent, ezért ügyeljen arra, hogy egyértelművé váljon a kimerülés előtti előnyök. a költségekkel kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/private-link/). Ha többet szeretne megtudni arról, hogy ezek az összetevők hogyan működnek együtt, tekintse meg a cikk tetején található videót. A privát végponti beállítás lefedettsége 5:48-kor kezdődik a videóban. A végpont beállításával kapcsolatos útmutatásért lásd: [privát végpont létrehozása az Azure Cognitive Searchhoz](service-create-private-endpoint.md).

## <a name="authentication"></a>Hitelesítés

A keresési szolgáltatás felé irányuló bejövő kérelmek esetében a hitelesítés egy [kötelező API-kulcson](search-security-api-keys.md) (véletlenszerűen generált számokból és betűkből álló karakterlánc) keresztül történik, amely igazolja, hogy a kérés megbízható forrásból származik. A Cognitive Search jelenleg nem támogatja a bejövő kérelmek Azure Active Directory hitelesítését.

Az indexelő által kezdeményezett kimenő kérelmeket a külső szolgáltatás hitelesíti. Az Cognitive Search indexelő alszolgáltatása megbízható szolgáltatást tud készíteni az Azure-ban, és egy felügyelt identitás használatával csatlakozik más szolgáltatásokhoz. További információ: [Indexer-kapcsolatok beállítása egy adatforráshoz felügyelt identitás használatával](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Engedélyezés

A Cognitive Search különböző engedélyezési modelleket biztosít a tartalomkezeléshez és a szolgáltatások kezeléséhez. 

### <a name="authorization-for-content-management"></a>Tartalomkezelés engedélyezése

A tartalomhoz való engedélyezés és a tartalomhoz kapcsolódó műveletek írási hozzáféréssel rendelkeznek a kérelemben megadott [API-kulccsal](search-security-api-keys.md) . Az API-kulcs egy hitelesítési mechanizmus, de a hozzáférés az API-kulcs típusától függően is engedélyezett.

+ Rendszergazdai kulcs (olvasási és írási hozzáférés engedélyezése a keresési szolgáltatásban a Create-Read-Update-delete műveletekhez), amelyet a szolgáltatás kiépítés után hozott létre.

+ Lekérdezési kulcs (csak olvasási hozzáférést biztosít az indexek dokumentumainak gyűjteményéhez), igény szerint létrehozva, és olyan ügyfélalkalmazások számára lett tervezve, amelyek lekérdezések kibocsátását teszik lehetővé

Az alkalmazás kódjában a végpontot és egy API-kulcsot kell megadnia a tartalomhoz és a beállításokhoz való hozzáférés engedélyezéséhez. A végpont lehet maga a szolgáltatás, az indexek gyűjteménye, egy adott index, egy dokumentum gyűjtemény vagy egy adott dokumentum. Ha összeláncolva, a végpont, a művelet (például egy létrehozási vagy frissítési kérelem) és a jogosultsági szint (a kulcs alapján teljes vagy csak olvasási jogosultság) képezi a tartalmat és a műveleteket védő biztonsági képletet.

### <a name="controlling-access-to-indexes"></a>Az indexekhez való hozzáférés szabályozása

Az Azure Cognitive Searchban az egyes indexek nem biztonságos objektumok. Ehelyett az indexhez való hozzáférést a szolgáltatási réteg határozza meg (olvasási vagy írási hozzáférés az Ön által megadott API-kulcs alapján), egy művelet kontextusával együtt.

A csak olvasási hozzáféréshez a lekérdezési kérelmeket a [lekérdezési kulccsal](search-security-rbac.md)lehet összekötni, és az alkalmazás által használt adott indexet is felhasználhatja. Egy lekérdezési kérelemben nincs olyan fogalom, amely az indexek összekapcsolását vagy egyszerre több index elérését teszi elérhetővé, így az összes kérelem egyetlen indexet céloz meg definíció szerint. Ennek megfelelően a lekérdezési kérelem (egy kulcs plusz egy célként megadott index) kialakítása határozza meg a biztonsági határt.

Az indexekhez való rendszergazdai és fejlesztői hozzáférés nem különbözik: a szolgáltatás által felügyelt objektumok létrehozásához, törléséhez és frissítéséhez egyaránt írási hozzáférésre van szükség. A szolgáltatáshoz tartozó [rendszergazdai kulccsal](search-security-rbac.md) bárki megtekintheti, módosíthatja vagy törölheti ugyanazon szolgáltatás bármelyik indexét. Az indexek véletlen vagy rosszindulatú törlésével szembeni védelem érdekében a belső verziókövetés a kód eszközeire a nemkívánatos indexek törlésének vagy módosításának megfordítására szolgáló megoldás. Az Azure Cognitive Search a rendelkezésre állás biztosítása érdekében feladatátvételt hajt végre a fürtön belül, de nem tárolja és nem hajtja végre az indexek létrehozásához vagy betöltéséhez használt tulajdonosi kódot.

Az index szintjén biztonsági határokat igénylő bérlős megoldások esetében az ilyen megoldások jellemzően olyan középső szintet tartalmaznak, amelyet az ügyfelek az indexek elkülönítésének kezelésére használnak. További információ a több-bérlős használati esetről: [tervezési minták a több-bérlős SaaS-alkalmazásokhoz és az Azure Cognitive Search](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Dokumentumokhoz való hozzáférés szabályozása

Ha részletes, felhasználónkénti vezérlést igényel a keresési eredmények között, biztonsági szűrőket készíthet a lekérdezésekhez, és visszaküldheti az adott biztonsági identitáshoz társított dokumentumokat. 

A "sor szintű biztonság" fogalmával egyenértékű, az indexen belüli tartalomra való jogosultság nem támogatott natív módon olyan előre definiált szerepkörök vagy szerepkör-hozzárendelések használatával, amelyek a Azure Active Directory entitásokra vannak leképezve. A külső rendszerekben található adatokra vonatkozó felhasználói engedélyek, például a Cosmos DBek, nem továbbítják azokat az adatokkal, amelyek Cognitive Search szerint indexelve vannak.

A "sor szintű biztonságot" igénylő megoldások megkerülő megoldásai közé tartozik egy olyan mező létrehozása az adatforrásban, amely egy biztonsági csoportot vagy felhasználói identitást jelöl, majd a Cognitive Search szűrők használatával szelektíven metszi a dokumentumok és tartalmak keresési eredményeit az identitások alapján. Az alábbi táblázat két módszert ismertet a jogosulatlan tartalom keresési eredményeinek kivágására.

| Módszer | Leírás |
|----------|-------------|
|[Biztonsági körülvágás identitás-szűrők alapján](search-security-trimming-for-azure-search.md)  | Dokumentálja a felhasználói identitás hozzáférés-vezérlésének megvalósításához szükséges alapszintű munkafolyamatot. Ismerteti a biztonsági azonosítók indexbe való hozzáadását, majd a tiltott tartalom eredményének kivágására szolgáló mező szűrését ismerteti. |
|[Biztonsági kivágás Azure Active Directory identitások alapján](search-security-trimming-for-azure-search-with-aad.md)  | Ez a cikk az előző cikkben található, amely a Azure Active Directory (Azure AD) identitások beolvasásának lépéseit ismerteti az Azure Cloud platform egyik [ingyenes szolgáltatásával](https://azure.microsoft.com/free/) . |

### <a name="authorization-for-service-management"></a>A szolgáltatás felügyeletének engedélyezése

A szolgáltatás-kezelési műveleteket az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)engedélyezik. Az Azure RBAC egy engedélyezési rendszer, amely az Azure-erőforrások kiépítésére [Azure Resource Manager](../azure-resource-manager/management/overview.md) . 

Az Azure Cognitive Search a Resource Manager használatával létrehozhatja vagy törölheti a szolgáltatást, kezelheti az API-kulcsokat, és méretezheti a szolgáltatást. Az Azure-beli szerepkör-hozzárendelések határozzák meg, hogy kik tudják elvégezni ezeket a feladatokat, függetlenül attól, hogy a [portált](search-manage.md), a [PowerShellt](search-manage-powershell.md)vagy a [felügyeleti REST API-kat](/rest/api/searchmanagement/search-howto-management-rest-api)használják.

A keresési szolgáltatás felügyeletéhez [három alapvető szerepkör](search-security-rbac.md#management-tasks-by-role) van definiálva. A szerepkör-hozzárendelések a támogatott módszerek (portál, PowerShell stb.) használatával tehetők elérhetővé, és a szolgáltatásra is érvényesek. A tulajdonos és a közreműködő szerepkör számos felügyeleti funkciót képes végrehajtani. Az olvasó szerepkört hozzárendelheti azokhoz a felhasználókhoz, akik csak az alapvető információkat tekintik meg.

> [!Note]
> Az Azure-ra kiterjedő mechanizmusok használatával zárolhatja az előfizetést vagy az erőforrást, így megakadályozhatja a keresési szolgáltatás véletlen vagy jogosulatlan törlését rendszergazdai jogosultságokkal rendelkező felhasználók számára. További információ: [erőforrások zárolása a váratlan törlés megakadályozása érdekében](../azure-resource-manager/management/lock-resources.md).

## <a name="threat-protection"></a>Fenyegetések elleni védelem

A keresési szolgáltatásokban lévő tartalmakhoz való hozzáférés kizárólag lekérdezéseken keresztül történik. Ha a keresési szolgáltatás a lekérdezési támadás célja, a rendszer levonja a lekérdezéseket, mivel a rendszer megközelíti a maximális kapacitást. 

A szabályozás eltérően viselkedik a különböző API-kkal. A szolgáltatás terhelése alapján a lekérdezési API-k (keresés/javaslat/automatikus kiegészítés) és az indexelő API-k szabályozása dinamikusan történik. Az API-k és a Service Operations API indexe statikus kérelmekre vonatkozó korlátozásokat tartalmaz. A statikus díjszabásra vonatkozó kérelmek korlátozásait a [szabályozási korlátok](search-limits-quotas-capacity.md#throttling-limits)között tekintheti át. A szabályozás működésével kapcsolatos további információk: a [lekérdezési kérelmek figyelése](search-monitor-queries.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Adatvédelem

A tárolási rétegben az adattitkosítás a lemezre mentett összes szolgáltatás által felügyelt tartalomra épül, beleértve az indexeket, a szinonimákat tartalmazó térképeket, valamint az indexelő, az adatforrások és a szakértelmével definícióit. Az indexelt tartalom kiegészítő titkosításához ügyfelek által felügyelt kulcsokat (CMK) is hozzáadhat. A 1 2020 augusztusában létrehozott szolgáltatások esetében a CMK titkosítás az ideiglenes lemezeken lévő adatokat az indexelt tartalom teljes "kettős titkosítása" érdekében terjeszti ki.

### <a name="data-in-transit"></a>Átvitt adatok

Az Azure Cognitive Search a titkosítás a kapcsolatokkal és az átvitelekkel kezdődik, és a lemezen tárolt tartalomra terjed ki. A nyilvános interneten található keresési szolgáltatások esetében az Azure Cognitive Search a 443-es HTTPS-portot figyeli. Az összes ügyfél és szolgáltatás közötti kapcsolat TLS 1,2 titkosítást használ. A korábbi verziók (1,0 vagy 1,1) nem támogatottak.

### <a name="encrypted-data-at-rest"></a>Titkosított adatok a nyugalmi állapotban

A Search szolgáltatás által belsőleg kezelt [adattitkosítási modelleket](../security/fundamentals/encryption-models.md)az alábbi táblázat ismerteti. Bizonyos funkciók, például a Tudásbázis, a növekményes bővítés és az indexelő alapú indexelés, a más Azure-szolgáltatások adatstruktúráinak olvasására vagy írására. Ezek a szolgáltatások az Azure Cognitive Searchtól eltérő titkosítási támogatással rendelkeznek.

| Modellezés | Kulcsok&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Követelmények&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Korlátozások | A következőre érvényes: |
|------------------|-------|-------------|--------------|------------|
| kiszolgálóoldali titkosítás | Microsoft által felügyelt kulcsok | Nincs (beépített) | Nincs, az összes régióban elérhető minden szinten, a január 24 2018. után létrehozott tartalomhoz. | Tartalom (indexek és szinonima térképek) és definíciók (indexelő, adatforrások, szakértelmével) |
| kiszolgálóoldali titkosítás | ügyfél által felügyelt kulcsok | Azure Key Vault | Minden régióban elérhető számlázandó szinten, a január 2019. után létrehozott tartalomhoz. | Az adatlemezeken található tartalom (indexek és szinonimák) |
| kiszolgálóoldali dupla titkosítás | ügyfél által felügyelt kulcsok | Azure Key Vault | Elérhető a számlázási szinten, a kiválasztott régiókban, a keresési szolgáltatásokban augusztus 1 2020. után. | Az adatlemezeken és az ideiglenes lemezeken található tartalom (indexek és szinonimák) |

### <a name="service-managed-keys"></a>Szolgáltatás által felügyelt kulcsok

A szolgáltatás által felügyelt titkosítás egy Microsoft-belső művelet, amely az [Azure Storage Service encryption](../storage/common/storage-service-encryption.md)alapján 256 bites [AES-titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ. Automatikusan megtörténik az összes indexelésnél, beleértve a nem teljes mértékben titkosított indexekhez tartozó növekményes frissítéseket (a 2018. január előtt létrehozva).

### <a name="customer-managed-keys-cmk"></a>Ügyfél által felügyelt kulcsok (CMK)

Az ügyfél által felügyelt kulcsokhoz további számlázható szolgáltatásra van szükség, Azure Key Vault, amely egy másik régióban, de ugyanabban az előfizetésben található, mint az Azure Cognitive Search. A CMK titkosításának engedélyezése növeli az index méretét és csökkenti a lekérdezési teljesítményt. Az eddigi megfigyelések alapján a lekérdezési időpontokban 30%-60%-os növekedés várható, bár a tényleges teljesítmény az index definíciója és a lekérdezések típusaitól függően változhat. A teljesítményre gyakorolt hatás miatt javasoljuk, hogy ezt a funkciót csak olyan indexeken engedélyezze, amelyekhez valóban szükség van. További információ: [az ügyfél által felügyelt titkosítási kulcsok konfigurálása az Azure Cognitive Searchban](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Dupla titkosítás

Az Azure Cognitive Search a kettős titkosítás a CMK kiterjesztése. A rendszer az adatlemezre írt hosszú távú tárolást, valamint az ideiglenes lemezekre írt rövid távú tárolást magában foglaló, a CMK és az azokban lévő teljes körű titkosítást is magába foglalja. A CMK közötti különbség a 1 2020 augusztusa előtt, illetve azt követően, és mi teszi a CMK egy dupla titkosítási funkciót az Azure Cognitive Search-ban, az adatok további titkosítása az ideiglenes lemezeken.

A kettős titkosítás jelenleg az alábbi régiókban létrehozott új szolgáltatásokban érhető el augusztus 1-től:

+ USA 2. nyugati régiója
+ USA keleti régiója
+ USA déli középső régiója
+ USA-beli államigazgatás – Virginia
+ USA-beli államigazgatás – Arizona

## <a name="security-management"></a>Biztonsági felügyelet

### <a name="api-keys"></a>API-kulcsok

Az API-kulcson alapuló hitelesítésre való támaszkodás azt jelenti, hogy a felügyeleti kulcs rendszeres időközönként történő újragenerálásának tervét kell megtervezni az Azure-beli biztonsági ajánlott eljárások alapján. Keresési szolgáltatásokban legfeljebb két rendszergazdai kulcs lehet. Az API-kulcsok biztonságossá tételével és kezelésével kapcsolatos további információkért lásd: [API-kulcsok létrehozása és kezelése](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Tevékenység és diagnosztikai naplók

A Cognitive Search nem naplózza a felhasználói identitásokat, ezért nem hivatkozhat a naplókra egy adott felhasználóval kapcsolatos információkért. A szolgáltatás azonban naplózza a Create-Read-Update-delete műveleteket, amelyekkel összekapcsolhatók más naplókkal, hogy megértsék az adott műveletek Ügynökségét.

A riasztások és a naplózási infrastruktúra használata az Azure-ban a lekérdezési mennyiségi csúcsokon vagy más, a várt számítási feladatoktól eltérő műveleteken is felveheti a munkát. A naplók beállításával kapcsolatos további információkért lásd: a [naplófájlok adatainak összegyűjtése és elemzése](search-monitor-logs.md) és a [lekérdezési kérelmek figyelése](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Minősítések és megfelelőség

Az Azure Cognitive Search a rendszeres auditálásokban részt vesz, és számos globális, regionális és iparági szabványnak megfelelő, a nyilvános felhőre és a Azure Governmentra vonatkozó előírással igazolt. A teljes listához töltse le a [ **Microsoft Azure megfelelőségi ajánlatokat**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) a hivatalos naplózási jelentések oldaláról.

A megfelelőség érdekében a [Azure Policy](../governance/policy/overview.md) segítségével megvalósíthatja az [Azure biztonsági teljesítményteszt](../security/benchmarks/introduction.md)magas biztonsággal kapcsolatos ajánlott eljárásait. Az Azure Security benchmark olyan biztonsági javaslatok gyűjteménye, amelyek a szolgáltatásokra és az adatmennyiségre vonatkozó fenyegetések enyhítése érdekében a biztonsági ellenőrzésekben foglaltak szerint vannak leképezve. Jelenleg 11 biztonsági vezérlő van, beleértve a [hálózati biztonságot](../security/benchmarks/security-control-network-security.md), a [naplózást és a figyelést](../security/benchmarks/security-control-logging-monitoring.md), és az [Adatvédelem](../security/benchmarks/security-control-data-protection.md) csak néhányat említsünk.

A Azure Policy az Azure-ba épített képesség, amely lehetővé teszi több szabvány megfelelőségének kezelését, beleértve az Azure biztonsági teljesítménytesztet is. A jól ismert referenciaértékek esetében Azure Policy olyan beépített definíciókat biztosít, amelyek mindkét feltételt, valamint a nem megfelelőséggel kapcsolatos, végrehajtható választ biztosítanak.

Az Azure Cognitive Search esetében jelenleg egy beépített definíció van. Diagnosztikai naplózásra szolgál. A beépített használatával olyan házirendet rendelhet hozzá, amely azonosítja a diagnosztikai naplózásból hiányzó keresési szolgáltatásokat, majd bekapcsolja azt. További információ: [Azure Policy Azure Cognitive Search szabályozási megfelelőségi szabályozása](security-controls-policy.md).

## <a name="see-also"></a>Lásd még

+ [Azure-biztonság – Alapismeretek](../security/fundamentals/index.yml)
+ [Azure-biztonság](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)