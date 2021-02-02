---
title: Azure-alkalmazás konfigurálása – gyakori kérdések
description: Az Azure app Configuration szolgáltatással kapcsolatos gyakori kérdések (GYIK), például a Azure Key Vaulttól való különbségének megismerése.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alkemper
ms.openlocfilehash: 39ad20bd57e3da6345c63d4601f34b19e640c1d6
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256475"
---
# <a name="azure-app-configuration-faq"></a>Azure-alkalmazás konfigurálása – gyakori kérdések

Ez a cikk az Azure-alkalmazás konfigurálásával kapcsolatos gyakori kérdésekre ad választ.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Miben különbözik az alkalmazások konfigurációjának Azure Key Vault?

Az alkalmazás konfigurációja segítségével a fejlesztők kezelhetik az Alkalmazásbeállítások és a vezérlési funkciók rendelkezésre állását. Célja, hogy leegyszerűsítse az összetett konfigurációs adatok használatának számos feladatát.

Az alkalmazás konfigurációja a következőket támogatja:

- Hierarchikus névterek
- Címkézés
- Részletes lekérdezések
- Kötegelt lekérés
- Speciális felügyeleti műveletek
- Egy szolgáltatás-felügyeleti felhasználói felület

Az alkalmazás konfigurációja kiegészíti a Key Vault, a kettőt pedig a legtöbb alkalmazás-telepítés egymás mellett kell használni.

## <a name="should-i-store-secrets-in-app-configuration"></a>A titkokat az alkalmazás konfigurációjában kell tárolni?

Bár az alkalmazás konfigurációja megerősített biztonságot nyújt, Key Vault még mindig az alkalmazási titkok tárolására szolgáló legjobb hely. A Key Vault hardveres titkosítást, részletes hozzáférési házirendeket és felügyeleti műveleteket biztosít, például a tanúsítvány elforgatását.

Létrehozhat olyan alkalmazás-konfigurációs értékeket, amelyek a Key Vaultban tárolt titkokra hivatkoznak. További információ: [Key Vault referenciák használata ASP.net Core alkalmazásban](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Titkosítja az alkalmazás konfigurációja az adataimat?

Igen. Az alkalmazás konfigurációja titkosítja az összes általa birtokolt kulcs értékét, és titkosítja a hálózati kommunikációt. A kulcsok nevei és címkéi indexként használatosak a konfigurációs adatok beolvasásához, és nincsenek titkosítva.

## <a name="where-does-data-stored-in-app-configuration-reside"></a>Hol találhatók az alkalmazások konfigurációjában tárolt adategységek? 

Az alkalmazás konfigurációjában tárolt ügyféladatok abban a régióban találhatók, ahol az ügyfél alkalmazás-konfigurációs tárolóját létrehozták. Az alkalmazás konfigurációja replikálhatja az adataikat a [párosított régiókba](../best-practices-availability-paired-regions.md) az adatrugalmasság érdekében, de nem replikálja vagy nem helyezi át az ügyféladatokat az [Azure-ban tárolt adattárolási](https://azure.microsoft.com/global-infrastructure/data-residency/)tartományon kívülre. Az ügyfelek és a végfelhasználók bármikor áthelyezhetik, másolhatják és érhetik el az ügyféladatokat bármely helyről.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Miben különbözik az alkalmazás konfigurációja a Azure App Service beállításaitól?

Azure App Service lehetővé teszi az egyes App Service-példányok Alkalmazásbeállítások megadását. Ezeket a beállításokat környezeti változókként adja át az alkalmazás kódjának. Ha kívánja, társíthat egy beállítást egy adott üzembe helyezési ponthoz. További információ: az [Alkalmazásbeállítások konfigurálása](../app-service/configure-common.md#configure-app-settings).

Ezzel szemben az Azure-alkalmazás konfigurációja lehetővé teszi olyan beállítások megadását, amelyek több alkalmazás között megoszthatók. Ide tartoznak a App Serviceon futó alkalmazások, valamint a többi platform is. Az alkalmazás kódja az Azure SDK-n keresztül, vagy közvetlenül a REST API-kon keresztül fér hozzá ezekhez a beállításokhoz a .NET és a Java konfigurációs szolgáltatóján keresztül.

A beállításokat App Service és az alkalmazás konfigurációja között is importálhatja és exportálhatja. Ez a funkció lehetővé teszi, hogy gyorsan beállítson egy új alkalmazás-konfigurációs tárolót a meglévő App Service beállítások alapján. A konfigurációt megoszthatja egy meglévő alkalmazással is, amely a App Service beállításokra támaszkodik.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Vannak korlátozások az alkalmazás konfigurációjában tárolt kulcsokra és értékekre?

Egy kulcs-érték elemhez legfeljebb 10 KB mennyiség használható.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hogyan tárolhatom a konfigurációkat több környezethez (tesztelés, előkészítés, gyártás stb.)?

Ön határozza meg, hogy ki férhet hozzá az alkalmazás konfigurálásához egy áruházbeli szinten. Használjon külön tárolót minden olyan környezethez, amelyhez eltérő engedélyek szükségesek. Ez a megközelítés biztosítja a legjobb biztonsági elkülönítést.

Ha nincs szüksége a környezetek közötti biztonság elkülönítésére, a konfigurációs értékek megkülönböztetéséhez címkéket használhat. [Használjon címkéket a különböző környezetek különböző konfigurációinak engedélyezéséhez](./howto-labels-aspnet-core.md) .

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Mik az alkalmazások konfigurációjának használatának ajánlott módjai?

Lásd: [ajánlott eljárások](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Mennyibe kerül az alkalmazás konfigurációja?

Kétféle árképzési szint létezik:

- Ingyenes szint
- Standard szint.

Ha a standard szint bevezetését megelőzően hozta létre a tárolót, a rendszer automatikusan áthelyezi az ingyenes szintet az általánosan elérhetővé tételig. Megadhatja, hogy a standard szintre frissítsen, vagy az ingyenes szinten maradjon.

A standard szintről az ingyenes szintre nem lehet visszaminősíteni egy áruházat. Létrehozhat egy új tárolót az ingyenes szinten, majd importálhatja a konfigurációs adattárakat a tárolóba.

## <a name="which-app-configuration-tier-should-i-use"></a>Melyik alkalmazás-konfigurációs szintet érdemes használni?

Mindkét alkalmazás-konfigurációs szint alapvető funkciókat kínál, beleértve a konfigurációs beállításokat, a funkciók jelzőit, Key Vault hivatkozásokat, az alapszintű felügyeleti műveleteket, a metrikákat és a naplókat.

A következő szempontok a rétegek kiválasztására vonatkoznak.

- **Erőforrások/előfizetés**: az erőforrások egyetlen konfigurációs tárolóból állnak. Az egyes előfizetések az ingyenes szinten egyetlen konfigurációs tárolóra korlátozódnak. Az előfizetések korlátlan számú konfigurációs tárolót használhatnak a standard szinten.
- **Tárterület/erőforrás**: az ingyenes szinten minden konfigurációs tároló 10 MB tárterületre korlátozódik. A standard szinten az egyes konfigurációs tárolók akár 1 GB tárterületet is használhatnak.
- Kiadási **Előzmények**: az alkalmazás konfigurációja tárolja a kulcsokon végrehajtott összes módosítás előzményeit. Az ingyenes szinten az előzményeket hét napig tároljuk. A standard szinten az előzményeket 30 napig tároljuk.
- **Kérelmek kvótája**: az ingyenes szintű áruházak napi 1 000 kérelemre korlátozódnak. Ha egy tároló eléri a 1 000-es kérelmeket, a 429-es HTTP-állapotkódot adja vissza az összes kérelemhez az éjféli UTC előtt.

    A standard szintű tárolók óránként 20 000 kérelemre korlátozódnak. A kvóta kimerítése után a 429-as HTTP-állapotkód az óra végéig minden kérelemnél visszaadja.

- **Szolgáltatói szerződés**: a standard szint 99,9%-os rendelkezésre állást biztosít. Az ingyenes szint nem rendelkezik SLA-val.
- **Biztonsági funkciók**: mindkét réteg alapszintű biztonsági funkciókat tartalmaz, beleértve a Microsoft által felügyelt kulcsokkal való titkosítást, a HMAC vagy Azure Active Directory, az Azure RBAC-támogatását, a felügyelt identitást és a szolgáltatási címkéket. A standard szint fejlettebb biztonsági funkciókat kínál, többek között a privát kapcsolatok támogatását és a titkosítást az ügyfél által felügyelt kulcsokkal.
- **Költség**: a standard szintű tárolók napi használati díjat számítanak fel. Az első 200 000-kérelmeket naponta felszámítjuk. A napi kiosztást megelőzően a kérések túlterhelését is felszámítjuk. Ingyenes szintű tároló használata díjmentes.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Frissíthetek áruházat az ingyenes szintről a standard szintre? Visszaválthatok egy áruházat a standard szintről az ingyenes szintre?

Az ingyenes szintről a standard szintre bármikor frissíthet.

A standard szintről az ingyenes szintre nem lehet visszaminősíteni egy áruházat. Létrehozhat egy új tárolót az ingyenes szinten, majd [importálhatja a konfigurációs adattárakat a tárolóba](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Az alkalmazás-konfigurációra vonatkozó kérelmek száma korlátozva van?

Az alkalmazás konfigurálásakor a kulcs-értékek olvasásakor az adatok oldalszámozással jelennek meg, és minden kérelem legfeljebb 100 kulcs-értéket tud olvasni. A kulcs-érték írásakor minden kérelem létrehozhat vagy frissíthet egy kulcs-értéket. Ezt a REST API, az App Configuration SDK-k és a konfigurációs szolgáltatók támogatják. Az ingyenes szinten lévő konfigurációs áruházak napi 1 000 kérésre korlátozódnak. A standard csomag konfigurációs tárolói átmeneti szabályozást tapasztalhatnak, ha a kérések száma óránként meghaladja a 20 000 kérést.

Ha egy tároló eléri a korlátot, a 429-as HTTP-állapotkódot fogja visszaadni az összes, az adott időszak lejárta után benyújtott kérelemnél. A `retry-after-ms` Válasz fejléce egy javasolt várakozási időt (ezredmásodpercben) tartalmaz a kérelem újrapróbálkozása előtt.

Ha az alkalmazás rendszeresen megtapasztalja a 429-es HTTP-állapotkódot, érdemes lehet újratervezni, hogy csökkentse a kérelmek számát. További információ: az [alkalmazás konfigurálására tett kérelmek csökkentése](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Az alkalmazás megkapja a 429-es HTTP-állapotkódot. Miért?

A 429-es HTTP-állapotkód a következő esetekben fog megjelenni:

* Az ingyenes szinten az áruház napi kérelmi korlátjának túllépése.
* Ideiglenes szabályozás a standard szintű tárolók magas kérelmekre vonatkozó díjszabása miatt.
* Túlzott sávszélesség-használat.
* Kísérlet történt a kulcs létrehozására vagy módosítására a tárolási ajánlat túllépése esetén.

Az 429 válasz törzsében keresse meg a kérelem hibájának okát.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hogyan kaphatok értesítéseket az új kiadásokról és az alkalmazás konfigurálásával kapcsolatos egyéb információkról?

Fizessen elő a [GitHub-hirdetmények](https://github.com/Azure/AppConfiguration-Announcements)tárházában.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hogyan jelenthetem a problémát vagy javaslatot?

Közvetlenül a [githubon](https://github.com/Azure/AppConfiguration/issues)érhet el minket.

## <a name="next-steps"></a>Következő lépések

* [Tudnivalók az Azure App Configurationről](./overview.md)
