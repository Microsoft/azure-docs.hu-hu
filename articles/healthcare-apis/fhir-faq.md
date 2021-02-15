---
title: Az Azure-beli FHIR-szolgáltatásokkal kapcsolatos gyakori kérdések – Azure API for FHIR
description: Válaszok a FHIR készült Azure API-val kapcsolatos gyakori kérdésekre, például a FHIR API-k mögötti adattárolási helyhez és a verziók támogatásához.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: d83bc653ae8c3ff5a9553de568bc6f2355f18760
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104620"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>A FHIR készült Azure API-val kapcsolatos gyakori kérdések

## <a name="azure-api-for-fhir-the-basics"></a>Azure API a FHIR-hez: az alapjai

### <a name="what-is-fhir"></a>Mi az a FHIR?
A gyors egészségügyi együttműködési erőforrások (FHIR – ejtsd: "Fire") egy együttműködési szabvány, amely lehetővé teszi az egészségügyi információk különböző egészségügyi rendszerek közötti cseréjét. Ezt a standardot a HL7-szervezet fejlesztette ki, és az egészségügyi szervezetek az egész világon elfogadják. A FHIR legújabb verziója az R4 (4. kiadás). A FHIR készült Azure API támogatja az R4-et, és az előző verzió STU3 is támogatja (a standard próbaverziós használatra 3). A FHIR kapcsolatos további információkért látogasson el a [HL7.org](http://hl7.org/fhir/summary.html)webhelyre.

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Az Azure-ban tárolt FHIR API-k mögött vannak az adathalmazok?

Igen, az Azure-ban felügyelt adatbázisok tárolják az adattárolást. A FHIR készült Azure API nem biztosít közvetlen hozzáférést a mögöttes adattárhoz.

### <a name="what-identity-provider-do-you-support"></a>Milyen identitás-szolgáltatót támogat?

Jelenleg az identitás-szolgáltatóként támogatja a Microsoft Azure Active Directory.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Mi a helyreállítási pont célkitűzése (RPO) a FHIR készült Azure API-hoz?
A FHIR készült Azure API-t a Cosmos DB tekintjük meg, mint az adatmegőrzési szolgáltató. Emiatt a szolgáltatás RPO egyenlő [Cosmos db (egyetlen régió)](../cosmos-db/consistency-levels.md) , és < 240 perc.

### <a name="what-fhir-version-do-you-support"></a>Milyen FHIR-verziót támogat?

A 4.0.0 és az 3.0.1-es verziókat a FHIR (FHIR-kiszolgáló) Azure API-ra és az Azure-ra (nyílt forráskód) is támogatja.

Részletekért lásd: [támogatott szolgáltatások](fhir-features-supported.md). Olvassa el, hogy mi változott a FHIR-verziók (azaz a STU3 – R4) között a [HL7 FHIR korábbi](https://hl7.org/fhir/R4/history.html)verzióiban.

A FHIR készült Azure IoT-összekötő (előzetes verzió) jelenleg csak az R4-es FHIR-verziót támogatja, és csak az Azure API FHIR-hoz készült R4-példányain látható.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Mi a különbség a "Microsoft FHIR Server for Azure" és az "Azure API for FHIR" között?

A FHIR készült Azure API az Azure-beli nyílt forráskódú Microsoft FHIR-kiszolgáló üzemeltetett és felügyelt verziója. A felügyelt szolgáltatásban a Microsoft biztosítja az összes karbantartást és frissítést. 

Ha futtatja az Azure-hoz készült FHIR-kiszolgálót, akkor közvetlen hozzáférést biztosít a mögöttes szolgáltatásokhoz, de felelős a kiszolgáló fenntartásáért és frissítésével, valamint az összes szükséges megfelelőségi munkával, ha a PHI-adatokat tárolja.

Fejlesztési szempontból minden olyan funkció, amely nem csak a felügyelt szolgáltatásra vonatkozik, először az Azure-hoz készült nyílt forráskódú Microsoft FHIR-kiszolgálóra telepíti. A nyílt forráskódú hitelesítés után a rendszer felveszi a FHIR-megoldáshoz tartozó, a Pásti Azure API-ra. A nyílt forráskódú és a Pásti kiadás közötti idő a szolgáltatás összetettsége és az ütemterv egyéb prioritásaitól függ. Ez ugyanaz a folyamat, mint az összes szolgáltatás, például az Azure IoT Connector for FHIR (előzetes verzió).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Hol láthatom, hogy mi történik a FHIR készült Azure API-ban?

Ha szeretné megtekinteni, hogy mi történik a FHIR készült Azure API-ban, tekintse meg a nyílt forráskódú FHIR-kiszolgáló [kiadását](https://github.com/microsoft/fhir-server/releases) . November 2020-től kezdődően az Azure-API-for-FHIR címkével ellátott elemeket címkézjük, ha a nyílt forráskódú elem a felügyelt szolgáltatásba kerül kiadásra. Ezek a funkciók általában két héttel a nyílt forráskódú kiadás oldalon jelennek meg. Emellett a Build [ide] tesztelésére vonatkozó utasításokat is tartalmaz ( https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) Ha saját környezetében szeretné tesztelni. A felügyelt szolgáltatások további frissítéseinek legjobb megosztását értékeljük.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>Mely régiókban érhetők el az Azure API a FHIR számára?

Jelenleg általánosan elérhető a nyilvános és a kormányzati szolgáltatás a [különböző földrajzi régiókban](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). A Microsoft Government Cloud Services szolgáltatással kapcsolatos információkért tekintse meg az [Azure-szolgáltatásokat a FedRAMP](../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Hol láthatom, hogy mi történik a FHIR készült Azure API-ban?

Ha szeretné megtekinteni, hogy mi történik a FHIR készült Azure API-ban, tekintse meg a nyílt forráskódú FHIR-kiszolgáló [kiadását](https://github.com/microsoft/fhir-server/releases) . Dolgozunk az Azure-API-for-FHIR elemek címkézésén, ha azok a felügyelt szolgáltatásba kerülnek, és általában két héttel a nyílt forráskódú kiadási oldalon jelennek meg. Az [itt](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) leírt lépéseket is bemutatjuk, ha a saját környezetében szeretné tesztelni az összeállítást. A felügyelt szolgáltatások további frissítéseinek legjobb megosztását értékeljük.

### <a name="what-is-smart-on-fhir"></a>Mi a FHIR intelligens?

Az intelligens (behelyettesíthető orvosi alkalmazások és újrafelhasználható technológia) a FHIR-ben olyan Nyílt specifikációk összessége, amelyek a FHIR-kiszolgálókkal és más egészségügyi informatikai rendszerekkel, például az elektronikus egészségügyi adatokkal és az állapotadatok cseréjével integrálják a partneri alkalmazásokat. Egy intelligens on FHIR alkalmazás létrehozásával gondoskodhat arról, hogy az alkalmazás elérhető legyen, és számos különböző rendszerből is kihasználható legyen.
Hitelesítés és Azure API a FHIR-hez. Ha többet szeretne megtudni az intelligens szolgáltatásról, látogasson el az [intelligens állapotba](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Hol találhatom meg, hogy a FHIR melyik verziója fut az adatbázison. 

A "fhirVersion" tulajdonság alatt megtalálhatja a képességek utasításban közzétett pontos FHIR-verziót.

## <a name="fhir-implementations-and-specifications"></a>FHIR implementációk és specifikációk

### <a name="can-i-create-a-custom-fhir-resource"></a>Létrehozhatok egyéni FHIR-erőforrásokat?

Nem engedélyezzük az egyéni FHIR-erőforrások használatát. Ha egyéni FHIR-erőforrásra van szüksége, hozzon létre egy egyéni erőforrást az [alapszintű erőforráshoz](http://www.hl7.org/fhir/basic.html) a bővítmények használatával. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Támogatottak-e a [bővítmények](https://www.hl7.org/fhir/extensibility.html) a FHIR készült Azure API-ban?

Lehetővé tesszük, hogy bármilyen érvényes FHIR JSON-adatkészletet töltsön be a-kiszolgálóra. Ha a bővítményt definiáló struktúrát szeretné tárolni, akkor azt struktúra-definíciós erőforrásként mentheti. Jelenleg nem kereshet bővítményeket.

### <a name="what-is-the-limit-on-_count"></a>Mi a korlát a _count?

A _count jelenlegi korlátja 100. Ha a _count több mint 100-ra állítja be, akkor a kötegben figyelmeztetés jelenik meg, amely csak 100 rekordokat fog megjeleníteni.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Vannak korlátozások a csoportos exportálási funkciókhoz?

A csoportos exportálás esetében csak a csoporton belül található hivatkozásokat exportáljuk, nem a [csoport erőforrásainak](https://www.hl7.org/fhir/group.html)minden jellemzőjét.

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Küldhetek egy köteget az Azure API FHIR?

Jelenleg támogatjuk a [Batch-csomagok](https://www.hl7.org/fhir/valueset-bundle-type.html) közzétételét, de nem támogatják a tranzakciós kötegek feladását a FHIR készült Azure API-ban. Az SQL által támogatott nyílt forráskódú FHIR-kiszolgálót tranzakciós kötegek küldésére használhatja.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Hogyan szerezhetem be az összes erőforrást egyetlen beteg számára a FHIR készült Azure API-ban?

Az FHIR-hez készült Azure API-ban támogatjuk az [adatrekeszek keresését](https://www.hl7.org/fhir/compartmentdefinition.html) . Ez lehetővé teszi, hogy egy adott beteghez kapcsolódó összes erőforrást lekérje. Vegye figyelembe, hogy a jobb oldali rekesz magában foglalja az összes olyan erőforrást, amely a beteghez kapcsolódik, de maga nem maga a beteg, így a beteg számára is keresnie kell, ha az eredmények között a beteg erőforrásra van szüksége.

Néhány példa erre:

* Beteg/<id>/*
* Beteg//Observation beolvasása <id>
* Beolvassa a beteg/ <id> /Observation? Code = 8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Mi az alapértelmezett rendezés, ha erőforrásokat keres az Azure API FHIR?

A rendezést a legutóbb frissített dátum szerint támogatjuk: _sort = _lastUpdated. A támogatott keresési paraméterekkel kapcsolatos további információkért tekintse meg a [támogatott funkciók lapot](./fhir-features-supported.md#search).

### <a name="does-the-azure-api-for-fhir-support-everything"></a>Támogatja a FHIR az Azure API-t a $everything? 

Nem. Jelenleg nem támogatjuk a $everything. Azonban két API-hívással is megvalósítható. Például a beteg $ minden adatának beszerzéséhez először a/Patient/[ID] használatával fogja megragadni a beteg rekordját, majd egy második hívást, amely a/Patient/[ID]/* használatával kéri le az összes pácienst.

További részletekért tekintse meg ezt a [közösségi bejegyzést](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/.24everything.20with.20_type). 

### <a name="how-does-export-work"></a>Hogyan működik $export?

$export a FHIR-specifikáció része: https://hl7.org/fhir/uv/bulkdata/export/index.html . Ha a FHIR szolgáltatás egy felügyelt identitással és egy Storage-fiókkal van konfigurálva, és ha a felügyelt identitás hozzáfér ehhez a Storage-fiókhoz, akkor egyszerűen hívja meg a $exportt a FHIR API-ban, és az összes FHIR-erőforrás exportálva lesz a Storage-fiókba. További információkért tekintse [meg a $exportról szóló cikket](./export-data.md).

### <a name="is-de-identified-export-available-at-patient-and-group-level-as-well"></a>A azonosított exportálás a beteg és a csoport szintjén is elérhető?
Az anonim exportálás jelenleg csak a teljes rendszer-exportáláson (/$export), és nem a beteg-exportálásnál (/Patient/-$export) támogatott. Dolgozunk azon, hogy elérhető legyen a beteg szintjén is.

## <a name="using-azure-api-for-fhir"></a>Az Azure API használata a FHIR-hez

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Hogyan engedélyezi a log Analytics szolgáltatást az Azure API-hoz a FHIR számára?

Engedélyezjük a diagnosztikai naplózást, és lehetővé tesszük a lekérdezések lekérdezésének áttekintését. A naplók és a lekérdezési lekérdezések engedélyezésével kapcsolatos részletekért tekintse meg [ezt a szakaszt](./enable-diagnostic-logging.md). Ha további információkat szeretne felvenni a naplókba, tekintse meg az [egyéni HTTP-fejlécek használatát](./use-custom-headers.md)ismertető témakört.

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Hol láthatok néhány példát a FHIR készült Azure API használatára a munkafolyamaton belül?

Az állapotfigyelő architektúrák gyűjteménye az [Health Architecture GitHub oldalán](https://github.com/microsoft/health-architectures)érhető el.

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Hol láthatom a webalkalmazások Azure API-hoz való csatlakoztatásának példáját a FHIR-hoz?

Van egy, az [architektúrát használó GitHub-oldal](https://aka.ms/health-architectures) , amely példákat és forgatókönyveket tartalmaz. Bemutatja, hogyan csatlakoztatható egy webalkalmazás az Azure API-hoz a FHIR-hez.  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API a FHIR szolgáltatásaihoz és szolgáltatásaihoz 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Van mód arra, hogy az adataimat a személyes kulcsom alapján titkosítsa, nem alapértelmezett kulcs?

Igen, az Azure API for FHIR lehetővé teszi az ügyfél által felügyelt kulcsok konfigurálását, a Cosmos DB támogatásának kihasználásával. Az adatok személyes kulccsal történő titkosításával kapcsolatos további információkért tekintse meg [ezt a szakaszt](./customer-managed-key.md).

## <a name="azure-api-for-fhir-preview-features"></a>Azure API a FHIR-hez: előzetes funkciók

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Konfigurálható a méretezési kapacitás az Azure IoT Connectorhoz a FHIR (előzetes verzió) esetében?

Mivel a FHIR készült Azure IoT-összekötő díjmentesen használható a nyilvános előzetes verzióban, a skálázási kapacitása rögzített és korlátozott. A nyilvános előzetes verzióban elérhető FHIR-konfiguráció Azure IoT-összekötője várhatóan körülbelül 200 üzenet másodpercenkénti átviteli sebességét biztosítja. Az erőforrás-kapacitás konfigurációjának valamilyen formája elérhetővé válik az általános elérhetőségen (GA).

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Miért nem tudom telepíteni az Azure IoT Connectort a FHIR (előzetes verzió) szolgáltatásra, ha a privát hivatkozás engedélyezve van a FHIR készült Azure API-ban?

A FHIR készült Azure IoT-összekötő jelenleg nem támogatja a privát kapcsolati funkciót. Ezért ha a FHIR-hez készült Azure API-ban engedélyezve van a privát hivatkozás, nem telepítheti az Azure IoT Connectort a FHIR-hez, és fordítva. Ez a korlátozás várhatóan megszűnik, ha a FHIR készült Azure IoT Connector általánosan elérhető (GA).
