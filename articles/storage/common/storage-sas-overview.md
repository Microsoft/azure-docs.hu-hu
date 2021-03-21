---
title: Korlátozott hozzáférés biztosítása a közös hozzáférési aláírásokkal (SAS) rendelkező adathoz
titleSuffix: Azure Storage
description: További információ a közös hozzáférésű aláírások (SAS) használatáról az Azure Storage-erőforrásokhoz való hozzáférés delegálásához, beleértve a blobokat, a várólistákat, a táblákat és a fájlokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 51e73222233602491b0c8ed3835d032610c68e0d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722786"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával

A közös hozzáférésű aláírás (SAS) biztonságos delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz. A SAS segítségével részletesen szabályozhatja, hogy az ügyfél Hogyan férhet hozzá az adataihoz. Például:
 
- Az ügyfél által elérhető erőforrások.

- Milyen engedélyekkel rendelkeznek ezekhez az erőforrásokhoz.

- Az SAS érvényességi ideje.

## <a name="types-of-shared-access-signatures"></a>A közös hozzáférésű jogosultságkódok típusai

Az Azure Storage három különböző típusú közös hozzáférési aláírást támogat:

- Felhasználói delegálás SAS

- Szolgáltatás SAS

- Fiók SAS

### <a name="user-delegation-sas"></a>Felhasználói delegálás SAS

A felhasználói delegálási SAS Azure Active Directory (Azure AD) hitelesítő adatokkal, valamint a SAS számára megadott engedélyekkel van védve. A felhasználói delegálási SAS csak a blob Storage-ra vonatkozik.

A felhasználói delegálási SAS-vel kapcsolatos további információkért lásd: [felhasználói delegálási sas létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas).

### <a name="service-sas"></a>Szolgáltatás SAS

A Storage-fiók kulcsa a Service SAS védelmét biztosítja. A szolgáltatás-SAS delegál egy erőforráshoz való hozzáférést csak az egyik Azure Storage-szolgáltatásban: blob Storage, üzenetsor-tároló, Table Storage vagy Azure Files.

További információ a szolgáltatás SAS-ről: [Service sas létrehozása (REST API)](/rest/api/storageservices/create-service-sas).

### <a name="account-sas"></a>Fiók SAS

A fiók SAS védelmét a Storage-fiók kulcsa biztosítja. A fiókalapú SAS egy vagy több tárolószolgáltatás erőforrásaihoz nyújt hozzáférést. A szolgáltatáson vagy a felhasználói delegálási SAS-n keresztül elérhető összes művelet a fiók SAS-n keresztül is elérhető. 

A következőhöz való hozzáférés delegálása is megtehető:

- Szolgáltatási szintű műveletek (például a **szolgáltatás beolvasása/beállítása** és a **szolgáltatás statisztikáinak beolvasása** ). 

- Olyan olvasási, írási és törlési műveletek, amelyek nem engedélyezettek a szolgáltatás SAS-vel. 

A fiók SAS-vel kapcsolatos további információkért [hozzon létre egy FIÓKOT sas (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait akkor használja, ha a fiók kulcsa helyett biztonsági szempontból ajánlott, ami könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség a blob Storage-hoz való hozzáféréshez, az Azure AD-beli hitelesítő adatok használatával hozzon létre egy felhasználói delegálási SAS-t, ha lehetséges, További információ: a [blobok és várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).

A közös hozzáférésű aláírás a következő két űrlap egyikét hajthatja végre:

- **Ad hoc sas**. Ha ad hoc SAS-t hoz létre, a kezdő időpont, a lejárati idő és az engedélyek meg vannak adva a SAS URI-ban. Bármilyen típusú SAS lehet ad hoc SAS.

- **A tárolt hozzáférési házirenddel rendelkező szolgáltatás sas**-je. Egy tárolt hozzáférési szabályzat definiálva van egy erőforrás-tárolón, amely lehet blob-tároló,-tábla,-várólista vagy-fájlmegosztás. A tárolt hozzáférési szabályzat segítségével kezelheti a korlátozásokat egy vagy több szolgáltatás közös hozzáférési aláírásakor. Ha egy szolgáltatás SAS-t társít egy tárolt hozzáférési szabályzathoz, a SAS örökli a megkötéseket &mdash; a tárolt hozzáférési házirend kezdési időpontja, lejárati ideje és engedélyei alapján &mdash; .

> [!NOTE]
> A felhasználói delegálási SAS vagy a fiók SAS-nak ad hoc SAS-nek kell lennie. A tárolt hozzáférési szabályzatok nem támogatottak a felhasználói delegálási SAS vagy a fiók SAS számára.

## <a name="how-a-shared-access-signature-works"></a>Hogyan működik a közös hozzáférés aláírása?

A közös hozzáférésű aláírás egy aláírt URI, amely egy vagy több tárolási erőforrásra mutat. Az URI olyan tokent tartalmaz, amely a lekérdezési paraméterek speciális készletét tartalmazza. A jogkivonat azt jelzi, hogy az ügyfél milyen módon férhet hozzá az erőforrásokhoz. A lekérdezési paraméterek egyike, az aláírás a SAS-paraméterekből lett kialakítva, és az SAS létrehozásához használt kulccsal van aláírva. Az Azure Storage ezt az aláírást használja a tárolási erőforráshoz való hozzáférés engedélyezéséhez.

> [!NOTE]
> Az SAS-tokenek generációjának naplózása nem lehetséges. Minden olyan felhasználó, akinek van jogosultsága SAS-token létrehozásához a fiók kulcsa vagy egy Azure RBAC szerepkör-hozzárendelés segítségével, a Storage-fiók tulajdonosának ismerete nélkül is megteheti. Ügyeljen arra, hogy korlátozza az engedélyeket, amelyek lehetővé teszik, hogy a felhasználók SAS-tokeneket készítsenek. Annak megakadályozása érdekében, hogy a felhasználók a blob-és üzenetsor-számítási feladatokhoz tartozó fiók kulcsával aláírt SAS-t hozzanak létre, letilthatja a megosztott kulcs elérését a Storage-fiókhoz. További információ: az [engedélyezés letiltása megosztott kulccsal](shared-key-authorization-prevent.md).

### <a name="sas-signature-and-authorization"></a>SAS-aláírás és-engedélyezés

Az SAS-tokent egy felhasználói delegálási kulccsal vagy egy Storage-fiók kulcsával (megosztott kulccsal) is aláírhatja. 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>SAS-token aláírása felhasználói delegálási kulccsal

Az SAS-tokent egy Azure Active Directory (Azure AD) hitelesítő adatok használatával létrehozott *felhasználói delegálási kulccsal* lehet aláírni. Felhasználói delegálási SAS van aláírva a felhasználói delegálási kulccsal.

A kulcs lekéréséhez, majd az SAS létrehozásához egy Azure AD rendszerbiztonsági tag számára olyan Azure-szerepkört kell hozzárendelni, amely magában foglalja a `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` műveletet. További információ: [felhasználói delegálási sas létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas).

#### <a name="signing-a-sas-token-with-an-account-key"></a>SAS-token aláírása egy fiók kulcsával

A Service SAS és a fiók SAS is a Storage-fiók kulcsával van aláírva. A fiók kulccsal aláírt SAS létrehozásához az alkalmazásnak hozzá kell férnie a fiók kulcsához.

Ha egy kérelem SAS-jogkivonatot tartalmaz, a kérést a SAS-jogkivonat aláírásának módja alapján engedélyezi a rendszer. Az Azure Storage az SAS-token létrehozásához használt hozzáférési kulcsot vagy hitelesítő adatokat is használja az SAS-t tartalmazó ügyfélhez való hozzáférés biztosításához.

Az alábbi táblázat összefoglalja, hogy az egyes SAS-jogkivonatok hogyan engedélyezettek.

| SAS típusa | Engedélyezési típus |
|-|-|
| Felhasználói delegálási SAS (csak blob Storage) | Azure AD |
| Szolgáltatás SAS | Megosztott kulcsos |
| Fiók SAS | Megosztott kulcsos |

A Microsoft azt javasolja, hogy a felhasználói delegálás SAS-t használja, ha lehetséges, a kiváló biztonság érdekében.

### <a name="sas-token"></a>SAS-jogkivonat

Az SAS-jogkivonat egy olyan karakterlánc, amelyet az ügyfél oldalán állít elő, például az egyik Azure Storage ügyféloldali kódtára használatával. A SAS-tokent semmilyen módon nem követik nyomon az Azure Storage. Korlátlan számú SAS-tokent hozhat létre az ügyféloldali oldalon. Miután létrehozott egy SAS-t, terjesztheti azt olyan ügyfélalkalmazások számára, amelyek hozzáférést igényelnek a Storage-fiók erőforrásaihoz.

Az ügyfélalkalmazások a kérelem részeként biztosítják az SAS URI-JÁT az Azure Storage-hoz. Ezután a szolgáltatás ellenőrzi a SAS-paramétereket és az aláírást annak ellenőrzéséhez, hogy érvényes-e. Ha a szolgáltatás ellenőrzi, hogy érvényes-e az aláírás, akkor a kérelem engedélyezve lesz. Ellenkező esetben a kérelem elutasítása a 403-as hibakódtal (tiltott) történik.

Az alábbi példa egy szolgáltatás SAS URI-JÁT mutatja be, amely az erőforrás URI-JÁT és az SAS-tokent jeleníti meg. Mivel az SAS-jogkivonat az URI-lekérdezési karakterláncot tartalmazza, az erőforrás-URI-t először egy kérdőjel, majd az SAS-token követi:

![A Service SAS URI összetevői](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Mikor kell megosztott hozzáférési aláírást használni

A SAS használatával biztonságos hozzáférést biztosíthat a Storage-fiók erőforrásaihoz olyan ügyfelek számára, akik egyébként nem rendelkeznek engedéllyel az adott erőforrásokhoz.

Gyakori eset, ha egy SAS hasznos szolgáltatás, ahol a felhasználók a saját adataikat olvassák és írhatják a Storage-fiókjába. Az olyan esetekben, amikor a tárfiók felhasználói adatokat tárol, két tipikus kialakítási minta létezik:

1. Az ügyfelek egy hitelesítést végző, előtérbeli proxyszolgáltatással töltik fel- és le az adatokat. Ez az előtér-proxy szolgáltatás lehetővé teszi az üzleti szabályok érvényesítését. A nagy adatmennyiségek vagy a nagy mennyiségű tranzakciók esetében azonban költséges vagy nehézkes lehet az igényeknek megfelelően méretezhető szolgáltatás létrehozása.

   ![Forgatókönyv diagramja: előtér-proxy szolgáltatás](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. Egy egyszerű szolgáltatás hitelesíti az ügyfelet, majd létrehoz egy SAS-t. Miután az ügyfélalkalmazás megkapja az SAS-t, közvetlenül hozzáférhet a Storage-fiók erőforrásaihoz. A hozzáférési engedélyeket az SAS és a SAS által engedélyezett intervallum határozza meg. Az SAS-szel nincs szükség az összes adat az előtérbeli proxyszolgáltatáson keresztül történő átirányítására.

   ![Forgatókönyv-diagram: SAS-szolgáltatói szolgáltatás](./media/storage-sas-overview/sas-storage-provider-service.png)

Számos valós szolgáltatás a két módszer hibrid használatát is felhasználhatja. Előfordulhat például, hogy egyes adatforrásokat az előtér-proxyn keresztül lehet feldolgozni és érvényesíteni. A többi adatmentést és/vagy közvetlenül az SAS használatával olvashatja el.

Emellett az SAS-nek egy másolási műveletben a Forrásobjektum elérésének engedélyezéséhez bizonyos helyzetekben a következő esetekben van szükség:

- Ha egy blobot egy másik, eltérő Storage-fiókban található blobba másol. 
  
  A cél blobhoz való hozzáférés engedélyezéséhez használhat SAS-t is.

- Egy másik Storage-fiókban található fájl másolásakor. 

  Igény szerint az SAS használatával is engedélyezheti a célfájl elérését.

- Amikor egy blobot egy fájlba másol, vagy egy fájlt egy blobba. 

  Ha a forrás-és a célobjektum ugyanabban a Storage-fiókban található, akkor akkor is SAS-t kell használnia.

## <a name="best-practices-when-using-sas"></a>A SAS használatával kapcsolatos ajánlott eljárások

Ha közös hozzáférési aláírásokat használ az alkalmazásokban, a következő két lehetséges kockázattal kell tisztában lennie:

- Ha egy SAS kiszivárgott, azt bárki felhasználhatja, aki beolvassa azt, ami potenciálisan veszélyeztetheti a Storage-fiókját.

- Ha egy ügyfélalkalmazás számára megadott SAS lejár, és az alkalmazás nem tud beolvasni egy új SAS-t a szolgáltatásból, akkor az alkalmazás működésének akadálya lehet.

A közös hozzáférési aláírások használatára vonatkozó alábbi javaslatok segíthetnek a kockázatok enyhítésében:

- A SAS-t **mindig a https használatával** hozzon létre vagy terjesszen. Ha a SAS átadása HTTP-n keresztül történik, és a rendszer elvégezte a támadót, egy ember általi támadást végző támadó elolvashatja az SAS-t. Ezt követően az SAS-t ugyanúgy használhatja, mint a kívánt felhasználó. Ez potenciálisan megsértheti a bizalmas adatokat, vagy engedélyezheti az adatsérülést a rosszindulatú felhasználó számára.

- **Ha lehetséges, használjon felhasználói delegálási SAS-t.** A felhasználói delegálási SAS magas szintű biztonságot nyújt a szolgáltatás SAS vagy egy fiók SAS számára. A felhasználói delegálási SAS védett az Azure AD hitelesítő adataival, így nem kell a fiókja kulcsát a kóddal tárolnia.

- **Egy SAS-re vonatkozó visszavonási terv van érvényben.** Győződjön meg arról, hogy készen áll a válaszadásra, ha a SAS biztonsága sérül.

- **Definiáljon egy tárolt hozzáférési szabályzatot egy szolgáltatás SAS számára.** A tárolt hozzáférési szabályzatok lehetővé teszik a Service SAS engedélyeinek visszavonását anélkül, hogy újra kellene generálni a Storage-fiók kulcsait. Állítsa a lejáratot a jövőben (vagy a végtelenre), és győződjön meg róla, hogy rendszeresen frissült, hogy a későbbiekben áthelyezze a szolgáltatást.

- **A közeljövőben lejárati idő használata egy ad hoc SAS-szolgáltatás SAS vagy fiók SAS esetében.** Ily módon, még akkor is, ha egy SAS biztonsága sérül, csak rövid ideig érvényes. Ez a gyakorlat különösen akkor fontos, ha nem hivatkozhat tárolt hozzáférési szabályzatra. A közeljövőben lejárati idő a blobba írt adatok mennyiségét is korlátozza a feltöltéshez rendelkezésre álló idő korlátozásával.

- **Ha szükséges, az ügyfelek automatikusan megújítják az SAS-t.** Az ügyfeleknek a lejárat előtt is meg kell újítaniuk a SAS-t, hogy újra lehessen próbálkozni az újrapróbálkozások idejével, ha az SAS-t biztosító szolgáltatás nem érhető el. Bizonyos esetekben ez szükségtelen lehet. Előfordulhat például, hogy az SAS-t kis számú azonnali, rövid életű művelethez kívánja használni. Ezeket a műveleteket a lejárati időszakon belül el kell végezni. Ennek eredményeképpen nem kell megújítani az SAS-t. Ha azonban olyan ügyfele van, amely az SAS-n keresztül rendszeresen kéri a kérelmeket, akkor a lejárati lehetőség a lejátszásra kerül. 

- **Ügyeljen rá, hogy az SAS indítási ideje legyen.** Ha a kezdő időpontot az aktuális időpontra állítja be, a hibák időnként előfordulhatnak az első pár percben. Ennek oka, hogy a különböző gépek némileg eltérő aktuális időpontokban vannak (azaz az óra elferdítve). Általában úgy állítsa be a kezdési időpontot, hogy legalább 15 perccel korábbi legyen. Vagy ne állítsa be egyáltalán, hogy minden esetben azonnal érvényes lesz. Ugyanez általában érvényes a lejárati időre is – ne feledje, hogy a kérések bármelyik irányában akár 15 percet is megfigyelheti. Az 2012-02-12-nál korábbi REST-verziót használó ügyfelek esetében a tárolt hozzáférési szabályzatra nem hivatkozó SAS maximális időtartama 1 óra. Az 1 óránál hosszabb időtartamot megadó házirendek sikertelenek lesznek.

- **Legyen óvatos az SAS datetime formátumával.** Egyes segédprogramok (például a AzCopy) esetében a dátum és idő formátuma csak "+% Y-% m-% dT% H:%M:% SZ" lehet. Ez a formátum kifejezetten tartalmazza a másodperceket. 

- **Legyen egyedi az elérni kívánt erőforrással.** Az ajánlott biztonsági eljárás a minimálisan szükséges jogosultságokkal rendelkező felhasználó biztosítása. Ha egy felhasználónak csak olvasási hozzáférésre van szüksége egyetlen entitáshoz, akkor olvasási hozzáféréssel kell rendelkeznie az adott entitáshoz, és nem kell olvasási/írási/törlési hozzáférést adni az összes entitáshoz. Ez segít csökkenteni a károkat, ha a SAS biztonsága sérül, mert az SAS a támadók kezében kevesebb árammal rendelkezik.

- **Ismerje meg, hogy a fiók számlázása minden használat után történik, beleértve a SAS-n keresztül is.** Ha írási hozzáférést biztosít egy blobhoz, a felhasználó a 200 GB-os blob feltöltését is választhatja. Ha olvasási hozzáféréssel is rendelkezik, dönthetnek úgy is, hogy 10 alkalommal töltik le, ami 2 TB-ot jelent a kimenő forgalomért. A korlátozott engedélyek megadásával csökkentheti a rosszindulatú felhasználók lehetséges műveleteit. Használja a rövid élettartamú SAS-t a fenyegetés csökkentése érdekében (de legyen szem előtt tartva a Befejezés időpontját).

- **A SAS használatával írt adatellenőrzés.** Ha egy ügyfélalkalmazás adatot ír a Storage-fiókjába, vegye figyelembe, hogy problémák merülhetnek fel az adott adattal kapcsolatban. Ha az adatok érvényesítését tervezi, hajtsa végre az ellenőrzést az adatok írása után, és mielőtt az alkalmazás használja azt. Ez a gyakorlat védelmet biztosít a fiókba írt sérült vagy rosszindulatú adatbevitelek ellen, akár egy olyan felhasználó, aki megfelelően megszerezte az SAS-t, vagy egy kiszivárgott SAS-t használó felhasználó.

- **Ismerje meg, ha nem használ SAS-t.** Előfordulhat, hogy egy adott művelethez kapcsolódó kockázatok a Storage-fiókkal szemben meghaladják a SAS használatának előnyeit. Ilyen műveletekhez hozzon létre egy olyan középszintű szolgáltatást, amely az üzleti szabályok érvényesítése, hitelesítés és naplózás végrehajtása után a Storage-fiókjába ír. Időnként más módokon is egyszerűbb a hozzáférés kezelése. Ha például a tárolóban lévő összes blobot nyilvánosan olvashatóként szeretné végezni, akkor a tárolót nyilvánosra helyezheti ahelyett, hogy minden ügyfél számára elérhetővé tegye a hozzáférést.

- **Az alkalmazás figyeléséhez használja a Azure Monitor és az Azure Storage-naplókat.** Az engedélyezési hibák az SAS-szolgáltató szolgáltatásbeli leállás miatt léphetnek fel. A tárolt hozzáférési szabályzatok véletlen eltávolításával is előfordulhatnak. A Azure Monitor és a Storage Analytics naplózási szolgáltatásával figyelheti az ilyen típusú engedélyezési hibákhoz tartozó szegeket. További információ: [Azure Storage-metrikák Azure monitor](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) és [Azure Storage Analytics naplózás](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> A Storage nem tudja nyomon követni a Storage-fiókhoz generált megosztott hozzáférési aláírások számát, és az API-k nem biztosíthatják ezt a részleteket. Ha tudnia kell a Storage-fiókhoz generált megosztott hozzáférési aláírások számát, manuálisan kell nyomon követnie a számot.

## <a name="get-started-with-sas"></a>Az SAS első lépései

A közös hozzáférésű aláírások megkezdéséhez tekintse meg az egyes SAS-típusok következő cikkeit.

### <a name="user-delegation-sas"></a>Felhasználói delegálás SAS

- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz a PowerShell használatával](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI-vel](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz .NET-tel](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Szolgáltatás SAS

- [Service SAS létrehozása tárolóhoz vagy blobhoz .NET-tel](../blobs/sas-service-create.md)

### <a name="account-sas"></a>Fiók SAS

- [Fiók SAS létrehozása a .NET-tel](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférés delegálása közös hozzáférési aláírással (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Szolgáltatás SAS létrehozása (REST API)](/rest/api/storageservices/create-service-sas)
- [Fiók SAS létrehozása (REST API)](/rest/api/storageservices/create-account-sas)
