---
title: Korlátozott hozzáférés biztosítása a közös hozzáférési aláírásokkal (SAS) rendelkező adathoz
titleSuffix: Azure Storage
description: További információ a közös hozzáférésű aláírások (SAS) használatáról az Azure Storage-erőforrásokhoz való hozzáférés delegálásához, beleértve a blobokat, a várólistákat, a táblákat és a fájlokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 218946fcfbf2268ff3b06eab839d69e6b370e891
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367907"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával

A közös hozzáférésű aláírás (SAS) biztonságos delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz anélkül, hogy veszélyeztetné az adatai biztonságát. A SAS segítségével részletesen szabályozhatja, hogy az ügyfél Hogyan férhet hozzá az adataihoz. Megadhatja, hogy az ügyfél milyen erőforrásokhoz férhet hozzá, milyen engedélyekkel rendelkezik ezekhez az erőforrásokhoz, és hogy mennyi ideig érvényes az SAS érvényessége, más paraméterek között.

## <a name="types-of-shared-access-signatures"></a>A közös hozzáférésű jogosultságkódok típusai

Az Azure Storage három különböző típusú közös hozzáférési aláírást támogat:

- **Felhasználói delegálás SAS.** A felhasználói delegálási SAS Azure Active Directory (Azure AD) hitelesítő adatokkal, valamint a SAS számára megadott engedélyekkel van védve. A felhasználói delegálási SAS csak a blob Storage-ra vonatkozik.

    A felhasználói delegálási SAS-vel kapcsolatos további információkért lásd: [felhasználói delegálási sas létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- **Szolgáltatás SAS.** A Storage-fiók kulcsa a Service SAS védelmét biztosítja. A szolgáltatás-SAS delegál egy erőforráshoz való hozzáférést csak az egyik Azure Storage-szolgáltatásban: blob Storage, üzenetsor-tároló, Table Storage vagy Azure Files.

    További információ a szolgáltatás SAS-ről: [Service sas létrehozása (REST API)](/rest/api/storageservices/create-service-sas).

- **Fiók SAS.** A fiók SAS védelmét a Storage-fiók kulcsa biztosítja. A fiókalapú SAS egy vagy több tárolószolgáltatás erőforrásaihoz nyújt hozzáférést. A szolgáltatáson vagy a felhasználói delegálási SAS-n keresztül elérhető összes művelet a fiók SAS-n keresztül is elérhető. Emellett a fiók SAS használatával delegálhatja a hozzáférést a szolgáltatás szintjén érvényes műveletekhez, például **lekérheti/beállíthatja a szolgáltatás tulajdonságait** , és **lekérheti a szolgáltatás-statisztika** műveleteit. A blobtárolók, táblák, üzenetsorok és fájlmegosztások olvasási, írási és törlési műveleteihez is hozzáférést biztosíthat, amelyeket a szolgáltatásalapú SAS nem engedélyez. 

    A fiók SAS-vel kapcsolatos további információkért [hozzon létre egy FIÓKOT sas (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait akkor használja, ha a fiók kulcsa helyett biztonsági szempontból ajánlott, ami könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség a blob Storage-hoz való hozzáféréshez, az Azure AD-beli hitelesítő adatok használatával hozzon létre egy felhasználói delegálási SAS-t, ha lehetséges, További információ: a [blobok és várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).

A közös hozzáférésű aláírás két űrlap egyikét veheti igénybe:

- **Ad hoc sas:** Ha ad hoc SAS-t hoz létre, a kezdési idő, a lejárati idő és az SAS-engedélyek mind meg vannak adva az SAS URI-ban (vagy hallgatólagosan, ha a kezdési idő nincs megadva). Bármilyen típusú SAS lehet ad hoc SAS.
- **A tárolt hozzáférési házirenddel rendelkező Service sas:** Egy tárolt hozzáférési szabályzat definiálva van egy erőforrás-tárolón, amely lehet blob-tároló,-tábla,-várólista vagy-fájlmegosztás. A tárolt hozzáférési szabályzat segítségével kezelheti a korlátozásokat egy vagy több szolgáltatás közös hozzáférési aláírásakor. Ha egy szolgáltatás SAS-t társít egy tárolt hozzáférési szabályzathoz, a SAS örökli a megkötéseket &mdash; a tárolt hozzáférési házirend kezdési időpontja, lejárati ideje és engedélyei alapján &mdash; .

> [!NOTE]
> A felhasználói delegálási SAS vagy a fiók SAS-nak ad hoc SAS-nek kell lennie. A tárolt hozzáférési szabályzatok nem támogatottak a felhasználói delegálási SAS vagy a fiók SAS számára.

## <a name="how-a-shared-access-signature-works"></a>Hogyan működik a közös hozzáférés aláírása?

A közös hozzáférésű aláírás egy aláírt URI, amely egy vagy több tárolási erőforrásra mutat, és olyan tokent tartalmaz, amely a lekérdezési paraméterek speciális készletét tartalmazza. A jogkivonat azt jelzi, hogy az ügyfél milyen módon férhet hozzá az erőforrásokhoz. A lekérdezési paraméterek egyike, az aláírás a SAS-paraméterekből lett kialakítva, és az SAS létrehozásához használt kulccsal van aláírva. Az Azure Storage ezt az aláírást használja a tárolási erőforráshoz való hozzáférés engedélyezéséhez.

### <a name="sas-signature-and-authorization"></a>SAS-aláírás és-engedélyezés

Az SAS-tokent kétféleképpen lehet aláírni:

- Azure Active Directory (Azure AD) hitelesítő adatok használatával létrehozott *felhasználói delegálási kulccsal* . Felhasználói delegálási SAS van aláírva a felhasználói delegálási kulccsal.

    A felhasználói delegálási kulcs beszerzéséhez és az SAS létrehozásához egy Azure AD rendszerbiztonsági tag számára olyan Azure-szerepkört kell hozzárendelni, amely tartalmazza a **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A felhasználói delegálási kulcs beszerzéséhez szükséges engedélyekkel rendelkező Azure-szerepkörökkel kapcsolatos részletes információkért lásd: [felhasználói delegálási sas létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- A Storage-fiók kulcsával (megosztott kulcs). A Service SAS és a fiók SAS is a Storage-fiók kulcsával van aláírva. A fiók kulccsal aláírt SAS létrehozásához az alkalmazásnak hozzá kell férnie a fiók kulcsához.

Ha egy kérelem SAS-jogkivonatot tartalmaz, a kérést a SAS-jogkivonat aláírásának módja alapján engedélyezi a rendszer. Az Azure Storage az SAS-token létrehozásához használt hozzáférési kulcsot vagy hitelesítő adatokat is használja az SAS-t tartalmazó ügyfélhez való hozzáférés biztosításához.

Az alábbi táblázat összefoglalja, hogy az egyes SAS-jogkivonatok hogyan engedélyezettek, ha az Azure Storage-ba irányuló kérelemben szerepel:

| SAS típusa | Engedélyezési típus |
|-|-|
| Felhasználói delegálási SAS (csak blob Storage) | Azure AD |
| Szolgáltatás SAS | Megosztott kulcsos |
| Fiók SAS | Megosztott kulcsos |

A Microsoft azt javasolja, hogy a felhasználói delegálás SAS-t használja, ha lehetséges, a kiváló biztonság érdekében.

### <a name="sas-token"></a>SAS-jogkivonat

Az SAS-jogkivonat egy olyan karakterlánc, amelyet az ügyfél oldalán állít elő, például az egyik Azure Storage ügyféloldali kódtára használatával. A SAS-tokent semmilyen módon nem követik nyomon az Azure Storage. Korlátlan számú SAS-tokent hozhat létre az ügyféloldali oldalon. Miután létrehozott egy SAS-t, terjesztheti azt olyan ügyfélalkalmazások számára, amelyek hozzáférést igényelnek a Storage-fiók erőforrásaihoz.

Ha egy ügyfélalkalmazás egy kérelem részeként egy SAS URI-t biztosít az Azure Storage-hoz, a szolgáltatás ellenőrzi a SAS paramétereit és aláírását annak ellenőrzéséhez, hogy érvényes-e a kérelem engedélyezésére. Ha a szolgáltatás ellenőrzi, hogy érvényes-e az aláírás, akkor a kérelem engedélyezve lesz. Ellenkező esetben a kérelem elutasítása a 403-as hibakódtal (tiltott) történik.

Az alábbi egy példa a szolgáltatásban használt SAS URI-kra, amelyek magukban foglalják az adott erőforrás URI-azonosítóját és SAS-jogkivonatát:

![A Service SAS URI összetevői](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Mikor kell megosztott hozzáférési aláírást használni

Használjon SAS-t, ha biztonságos hozzáférést szeretne biztosítani a Storage-fiók erőforrásaihoz bármely olyan ügyfél számára, aki egyébként nem rendelkezik engedéllyel az adott erőforrásokhoz.

Gyakori eset, ha egy SAS hasznos szolgáltatás, ahol a felhasználók a saját adataikat olvassák és írhatják a Storage-fiókjába. Az olyan esetekben, amikor a tárfiók felhasználói adatokat tárol, két tipikus kialakítási minta létezik:

1. Az ügyfelek egy hitelesítést végző, előtérbeli proxyszolgáltatással töltik fel- és le az adatokat. Az előtér-proxy szolgáltatásnak megvan az előnye, hogy lehetővé teszi az üzleti szabályok érvényesítését, de nagy mennyiségű vagy nagy mennyiségű adatforgalom esetén az igényeknek megfelelően méretezhető szolgáltatás létrehozása költséges vagy nehézkes lehet.

   ![Forgatókönyv diagramja: előtér-proxy szolgáltatás](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Egy egyszerű szolgáltatás hitelesíti az ügyfelet, majd létrehoz egy SAS-t. Miután az ügyfélalkalmazás megkapja az SAS-t, közvetlenül hozzáférhetnek a Storage-fiók erőforrásaihoz a SAS által meghatározott engedélyekkel és az SAS által engedélyezett időtartammal. Az SAS-szel nincs szükség az összes adat az előtérbeli proxyszolgáltatáson keresztül történő átirányítására.

   ![Forgatókönyv-diagram: SAS-szolgáltatói szolgáltatás](./media/storage-sas-overview/sas-storage-provider-service.png)

Számos valós szolgáltatás a két módszer hibrid használatát is felhasználhatja. Előfordulhat például, hogy egyes adatforrásokat az előtér-proxyn keresztül lehet feldolgozni és érvényesíteni, míg más adatmentést és/vagy közvetlenül az SAS használatával.

Emellett az SAS-nek egy másolási műveletben a Forrásobjektum elérésének engedélyezéséhez bizonyos helyzetekben a következő esetekben van szükség:

- Ha egy másik Storage-fiókban található blobba másol egy blobot, a forrás blob elérésének engedélyezéséhez SAS-t kell használnia. A cél blobhoz való hozzáférés engedélyezéséhez használhat SAS-t is.
- Ha egy másik Storage-fiókban található fájlra másol egy fájlt, a forrásfájl elérésének engedélyezéséhez SAS-t kell használnia. Igény szerint az SAS használatával is engedélyezheti a célfájl elérését.
- Amikor fájlokat másol egy fájlba, vagy egy fájlt egy blobba, egy SAS-t kell használnia a Forrásobjektum hozzáférésének engedélyezéséhez, még akkor is, ha a forrás-és a célhely ugyanazon a Storage-fiókon belül található.

## <a name="best-practices-when-using-sas"></a>A SAS használatával kapcsolatos ajánlott eljárások

Ha közös hozzáférési aláírásokat használ az alkalmazásokban, a következő két lehetséges kockázattal kell tisztában lennie:

- Ha egy SAS kiszivárgott, azt bárki felhasználhatja, aki beolvassa azt, ami potenciálisan veszélyeztetheti a Storage-fiókját.
- Ha egy ügyfélalkalmazás számára megadott SAS lejár, és az alkalmazás nem tud beolvasni egy új SAS-t a szolgáltatásból, akkor az alkalmazás működésének akadálya lehet.

A közös hozzáférési aláírások használatára vonatkozó alábbi javaslatok segíthetnek a kockázatok enyhítésében:

- A SAS-t **mindig a https használatával** hozzon létre vagy terjesszen. Ha a SAS átadása HTTP-n keresztül történik, és a rendszer elvégzi a támadók számára a támadók által végrehajtott támadásokat, megtekintheti az SAS-t, majd ugyanúgy használhatja azt, mint amennyire a kívánt felhasználó rendelkezhet, esetleg veszélyezteti a bizalmas adatokat, vagy engedélyezheti az adatsérülést a rosszindulatú felhasználótól.
- **Ha lehetséges, használjon felhasználói delegálási SAS-t.** A felhasználói delegálási SAS magas szintű biztonságot nyújt a szolgáltatás SAS vagy egy fiók SAS számára. A felhasználói delegálási SAS védett az Azure AD hitelesítő adataival, így nem kell a fiókja kulcsát a kóddal tárolnia.
- **Egy SAS-re vonatkozó visszavonási terv van érvényben.** Győződjön meg arról, hogy készen áll a válaszadásra, ha a SAS biztonsága sérül.
- **Definiáljon egy tárolt hozzáférési szabályzatot egy szolgáltatás SAS számára.** A tárolt hozzáférési szabályzatok lehetővé teszik a Service SAS engedélyeinek visszavonását anélkül, hogy újra kellene generálni a Storage-fiók kulcsait. Állítsa a lejáratot a jövőben (vagy a végtelenre), és győződjön meg róla, hogy rendszeresen frissült, hogy a későbbiekben áthelyezze a szolgáltatást.
- **A közeljövőben lejárati idő használata egy ad hoc SAS-szolgáltatás SAS vagy fiók SAS esetében.** Ily módon, még akkor is, ha egy SAS biztonsága sérül, csak rövid ideig érvényes. Ez a gyakorlat különösen akkor fontos, ha nem hivatkozhat tárolt hozzáférési szabályzatra. A közeljövőben lejárati idő a blobba írt adatok mennyiségét is korlátozza a feltöltéshez rendelkezésre álló idő korlátozásával.
- **Ha szükséges, az ügyfelek automatikusan megújítják az SAS-t.** Az ügyfeleknek a lejárat előtt is meg kell újítaniuk a SAS-t, hogy újra lehessen próbálkozni az újrapróbálkozások idejével, ha az SAS-t biztosító szolgáltatás nem érhető el. Ha a SAS-t kis számú azonnali, rövid életű művelethez kívánja használni, amelyeket a lejárati időszakon belül el kell végezni, akkor ez szükségtelen lehet, mivel a SAS-t nem kell megújítani. Ha azonban olyan ügyfele van, amely az SAS-n keresztül rendszeresen kéri a kérelmeket, akkor a lejárati lehetőség a lejátszásra kerül. A legfontosabb szempont, hogy az SAS-nek rövid életűnek kell lennie (ahogy azt korábban már említettük) annak biztosításához, hogy az ügyfél kellő időben megújítsa a megújítást (a sikeres megújítás előtt lejáró SAS okozta fennakadások elkerülése érdekében).
- **Ügyeljen rá, hogy az SAS indítási ideje legyen.** Ha a kezdő időpontot az aktuális időpontra állítja be, akkor az első pár percen belül időnként megfigyelheti a hibákat, mivel a különböző gépek némileg eltérő aktuális időpontokban (más néven az óra döntse el). Általában úgy állítsa be a kezdési időpontot, hogy legalább 15 perccel korábbi legyen. Vagy ne állítsa be egyáltalán, hogy minden esetben azonnal érvényes lesz. Ugyanez általában érvényes a lejárati időre is – ne feledje, hogy a kérések bármelyik irányában akár 15 percet is megfigyelheti. Az 2012-02-12-nál korábbi REST-verziót használó ügyfelek esetében a tárolt hozzáférési szabályzatra nem hivatkozó SAS maximális időtartama 1 óra, és a szabályzatok által meghiúsult hosszabb időszakot megadó házirendek.
- **Legyen óvatos az SAS datetime formátumával.** Ha egy SAS esetében beállítja a kezdési időt és/vagy a lejáratot, egyes segédprogramok esetében (például a parancssori segédprogram AzCopy), akkor a dátum és idő formátuma csak "+% Y-% m-% dT% H:%M:% SZ" lehet.
- **Legyen egyedi az elérni kívánt erőforrással.** Az ajánlott biztonsági eljárás a minimálisan szükséges jogosultságokkal rendelkező felhasználó biztosítása. Ha egy felhasználónak csak olvasási hozzáférésre van szüksége egyetlen entitáshoz, akkor olvasási hozzáféréssel kell rendelkeznie az adott entitáshoz, és nem kell olvasási/írási/törlési hozzáférést adni az összes entitáshoz. Ez segít csökkenteni a károkat, ha a SAS biztonsága sérül, mert az SAS a támadók kezében kevesebb árammal rendelkezik.
- **Ismerje meg, hogy a fiók számlázása minden használat után történik, beleértve a SAS-n keresztül is.** Ha írási hozzáférést biztosít egy blobhoz, a felhasználó a 200 GB-os blob feltöltését is választhatja. Ha olvasási hozzáféréssel is rendelkezik, dönthetnek úgy is, hogy 10 alkalommal töltik le, ami 2 TB-ot jelent a kimenő forgalomért. A korlátozott engedélyek megadásával csökkentheti a rosszindulatú felhasználók lehetséges műveleteit. Használja a rövid élettartamú SAS-t a fenyegetés csökkentése érdekében (de legyen szem előtt tartva a Befejezés időpontját).
- **A SAS használatával írt adatellenőrzés.** Ha egy ügyfélalkalmazás adatot ír a Storage-fiókjába, vegye figyelembe, hogy problémák merülhetnek fel az adott adattal kapcsolatban. Ha az alkalmazás működéséhez az szükséges, hogy az adatok a használatra kész állapotban legyenek, akkor ezt az ellenőrzést az adatok írása után kell végrehajtani, mielőtt az alkalmazás használni fogja őket. Ez a gyakorlat védelmet biztosít a fiókba írt sérült vagy rosszindulatú adatbevitelek ellen, akár egy olyan felhasználó, aki megfelelően megszerezte az SAS-t, vagy egy kiszivárgott SAS-t használó felhasználó.
- **Ismerje meg, ha nem használ SAS-t.** Előfordulhat, hogy egy adott művelethez kapcsolódó kockázatok a Storage-fiókkal szemben meghaladják a SAS használatának előnyeit. Ilyen műveletekhez hozzon létre egy olyan középszintű szolgáltatást, amely az üzleti szabályok érvényesítése, hitelesítés és naplózás végrehajtása után a Storage-fiókjába ír. Időnként más módokon is egyszerűbb a hozzáférés kezelése. Ha például a tárolóban lévő összes blobot nyilvánosan olvashatóként szeretné végezni, akkor a tárolót nyilvánosra helyezheti ahelyett, hogy minden ügyfél számára elérhetővé tegye a hozzáférést.
- **Az alkalmazás figyeléséhez használja a Azure Monitor és az Azure Storage-naplókat.** A Azure Monitor és a Storage Analytics naplózásával megfigyelheti az engedélyezési hibákhoz tartozó összes tüskét az SAS-szolgáltató szolgáltatás kimaradása vagy a tárolt hozzáférési szabályzat véletlen eltávolítása miatt. További információ: [Azure Storage-metrikák Azure monitor](monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) és [Azure Storage Analytics naplózás](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Az SAS első lépései

A közös hozzáférésű aláírások megkezdéséhez tekintse meg az egyes SAS-típusok következő cikkeit.

### <a name="user-delegation-sas"></a>Felhasználói delegálás SAS

- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz a PowerShell használatával](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI-vel](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz .NET-tel](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Szolgáltatás SAS

- [Service SAS létrehozása tárolóhoz vagy blobhoz .NET-tel](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Fiók SAS

- [Fiók SAS létrehozása a .NET-tel](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférés delegálása közös hozzáférési aláírással (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Szolgáltatás SAS létrehozása (REST API)](/rest/api/storageservices/create-service-sas)
- [Fiók SAS létrehozása (REST API)](/rest/api/storageservices/create-account-sas)
