---
title: Azure-adatközpontok fizikai biztonsága – Microsoft Azure | Microsoft Docs
description: A cikk azt ismerteti, hogy mit tesz a Microsoft az Azure-adatközpontok biztonságossá tételéhez, beleértve a fizikai infrastruktúrát, a biztonságot és a megfelelőségi ajánlatokat.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 3eac3c9abda74f0414fc97512f011d235d3991f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076321"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure-létesítmények,-telephelyek és fizikai biztonság
Ez a cikk azt ismerteti, hogy mit tesz a Microsoft az Azure-infrastruktúra biztonságossá tételéhez.

## <a name="datacenter-infrastructure"></a>Adatközpont-infrastruktúra
Az Azure egy [globálisan elosztott adatközpont-infrastruktúrából](https://azure.microsoft.com/global-infrastructure/)áll, amely több ezer online szolgáltatások támogat, és világszerte több mint 100 igen biztonságos szolgáltatást biztosít.

Az infrastruktúra úgy lett kialakítva, hogy az alkalmazások a világ minden pontján közelebb kerüljenek a felhasználókhoz, megőrizve az adattárolást, és átfogó megfelelőségi és rugalmassági lehetőségeket biztosítanak az ügyfeleknek. Az Azure világszerte 58 régióval rendelkezik, és 140 országban/régióban érhető el.

A régiók olyan adatközpontok összessége, amelyek nagy és rugalmas hálózaton keresztül kapcsolódnak egymáshoz. A hálózat magában foglalja a tartalom terjesztését, a terheléselosztást, a redundanciát és az [adatkapcsolati réteg titkosítását alapértelmezés szerint](encryption-overview.md#encryption-of-data-in-transit) a régión belüli összes Azure-forgalom esetében, vagy a régiók közötti utazást. Az Azure több globális régióval rendelkezik, mint bármely más felhőalapú szolgáltató, így rugalmasságot biztosít az alkalmazások üzembe helyezéséhez, ahol szüksége van rájuk.

Az Azure-régiók földrajzilag vannak rendszerezve. Az Azure földrajza biztosítja, hogy az adattárolással, a szuverenitással, a megfelelőséggel és a rugalmassággal kapcsolatos követelmények tiszteletben maradnak a földrajzi határokon belül.

A régiócsoportok lehetővé teszik, hogy a speciális adattárolási és megfelelőségi igényekkel rendelkező ügyfelek földrajzilag közel tárolhassák adataikat és alkalmazásaikat. A földrajzi területek hibatűrők a teljes régió meghibásodása érdekében, a dedikált, nagy kapacitású hálózati infrastruktúrával való kapcsolaton keresztül.

A rendelkezésre állási zónák fizikailag különálló helyek az Azure-régiókban. Az egyes rendelkezésre állási zónák egy vagy több, önálló áramellátással, hűtéssel, és hálózattal rendelkező adatközpontból állnak. A rendelkezésre állási zónák lehetővé teszik a magas rendelkezésre állású és alacsony késésű replikációt biztosító, kritikus fontosságú alkalmazások futtatását.

Az alábbi ábra azt mutatja be, hogy az Azure globális infrastruktúra-párok régiója és rendelkezésre állási zónái a magas rendelkezésre állást, a vész-helyreállítást és a biztonsági mentést is magukban foglalják.

![Az adattárolási határt ábrázoló diagram](./media/physical-security/data-residency-boundary.png)

A földrajzilag elosztott adatközpontok lehetővé teszik, hogy a Microsoft közel legyen az ügyfelekhez, hogy csökkentse a hálózati késést, és lehetővé tegye a Geo-redundáns biztonsági mentést és feladatátvételt

## <a name="physical-security"></a>Fizikai biztonság
A Microsoft adatközpontokat tervez, épít és működtet olyan módon, amely szigorúan szabályozza a fizikai hozzáférést azokhoz a területekhez, ahol az adatait tárolják. A Microsoft tisztában van az adatok védelmének fontosságával, és elkötelezett az adatok tárolására szolgáló adatközpontok biztonságossá tételében. A Microsoft teljes részlege az Azure-t támogató fizikai létesítmények tervezésével, létrehozásával és működtetésével foglalkozik. A csapat a legkorszerűbb fizikai biztonság fenntartásával lett befektetve.

A Microsoft elvégzi a fizikai biztonság rétegzett megközelítését, hogy csökkentse annak a kockázatát, hogy a jogosulatlan felhasználók fizikai hozzáférést szerezzenek az adatközpont erőforrásaihoz. A Microsoft által felügyelt adatközpontok széles körű védelmet biztosítanak: hozzáférés jóváhagyása a létesítmény kerületében, az épület peremén, az épületen belül és az adatközpont szintjén. A fizikai biztonság rétegei a következők:

- **Hozzáférési kérelem és jóváhagyás.** A hozzáférést az adatközpontba való érkezés előtt kell kérnie. Érvényes üzleti indoklást kell megadnia a látogatáshoz, például megfelelőségi vagy naplózási célokra. A Microsoft alkalmazottai igény szerint férhetnek hozzá az összes kérelemhez. Az igény szerinti hozzáférés segít megőrizni az adatközpontokban a feladatok elvégzéséhez szükséges személyek számát. Miután a Microsoft engedélyezte az engedélyt, egy személy csak az adatközpont elkülönített területéhez férhet hozzá a jóváhagyott üzleti indoklás alapján. Az engedélyek meghatározott időtartamra vannak korlátozva, majd lejárnak.

- **A létesítmény peremhálózati.** Amikor megérkezik egy adatközpontba, egy jól meghatározott hozzáférési ponton kell átesnie. Jellemzően az acélból és a betonból álló magas kerítések a peremhálózat minden centiméterét felölelik. Az adatközpontok körül kamerák találhatók, és a biztonsági csapat folyamatosan figyeli a videókat.

- **Építési bejárat.** Az adatközpont bejárata olyan szakmai biztonsági tisztviselőkkel rendelkezik, akik alapos képzésen és háttér-ellenőrzésen estek át. Ezek a biztonsági tisztviselők is rendszeresen őrjáratot végeznek az adatközpontban, és folyamatosan figyelik az adatközpontban található kamerák videóit.

- **Az épületen belül.** Az építési folyamat megkezdése után kétfaktoros hitelesítést kell átadni a biometria használatával, hogy továbbra is áthaladjon az adatközponton. Ha a személyazonosságát érvényesíti, csak az adatközpont azon részét adhatja meg, amelyhez jóváhagyta a hozzáférést. Csak a jóváhagyott idő tartama alatt maradhat.

- **Adatközpont-padló.** Csak az Ön által jóváhagyott padlóra engedélyezett. Egy teljes test Metal észlelési szűrővizsgálatot kell átadnia. Annak érdekében, hogy az adatközpontnak az ismerete nélkül belépjenek vagy hagyják el a jogosulatlan adatokat, csak a jóváhagyott eszközök végezhetik el az adatközpont emeletét. Emellett a videokamerák figyelik az összes kiszolgáló állványának elöl és hátulját. Amikor kilép az adatközpont fenekén, újra kell haladnia a test Metal észlelésének teljes szűrésével. Az adatközpont elhagyásához egy további biztonsági vizsgálatot kell átadnia.

A Microsoft megköveteli, hogy a látogatók bármely Microsoft-létesítménytől kijelentkezéskor beadják a jelvényeket.

## <a name="physical-security-reviews"></a>Fizikai biztonsági felülvizsgálatok
Időnként a létesítmények fizikai biztonsági felülvizsgálatait végezheti el, hogy az adatközpontok megfelelően megfeleljenek az Azure biztonsági követelményeinek. Az adatközpont szolgáltatójának tagjai nem biztosítanak Azure-szolgáltatások felügyeletét. A személyzet nem tud bejelentkezni az Azure-rendszerbe, és nincs fizikai hozzáférése az Azure létrehozásakor közös elhelyezés-helyiséghez és-ketrecekhez.

## <a name="data-bearing-devices"></a>Adattárolási eszközök
A Microsoft az ajánlott eljárásokat, valamint egy olyan törlési megoldást használ, amely a [NIST 800-88-kompatibilis](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). A nem törölhető merevlemezek esetében olyan megsemmisítési folyamatot használunk, amely megsemmisíti, és lehetetlenné teszi az adatok helyreállítását. Ez a megsemmisítési folyamat lehet a kivonás, a foszlány, a porlasztó vagy a hulladékégető művelet. A kivezetés módját az eszköz típusa szerint határozzuk meg. Megőrzi a megsemmisítési rekordokat.  

## <a name="equipment-disposal"></a>Berendezések kivezetése
A rendszer élettartama után a Microsoft operatív személyzete szigorú adatkezelési és hardver-kivezetési eljárásokat követve biztosítja, hogy az adatait tartalmazó hardverek ne legyenek elérhetők a nem megbízható felek számára. Biztonságos törlési módszert használunk az azt támogató merevlemez-meghajtókon. A nem törölhető merevlemezek esetében a rendszer megsemmisítési folyamatot használ, amely megsemmisíti a meghajtót, és lehetetlenné teszi az adatok helyreállítását. Ez a megsemmisítési folyamat lehet a kivonás, a foszlány, a porlasztó vagy a hulladékégető művelet. A kivezetés módját az eszköz típusa szerint határozzuk meg. Megőrzi a megsemmisítési rekordokat. Minden Azure-szolgáltatás jóváhagyta a Media Storage és az ártalmatlanítási felügyeleti szolgáltatásokat.

## <a name="compliance"></a>Megfelelőség
Az Azure-infrastruktúrát úgy tervezjük és felügyeljük, hogy megfeleljen az olyan nemzetközi és iparági megfelelőségi szabványoknak, mint az ISO 27001, a HIPAA, a FedRAMP, a SOC 1 és a SOC 2. Az ország-vagy régióspecifikus szabványokat is kielégítjük, beleértve az ausztráliai IRAP, az Egyesült királyságbeli G-Cloud-t és a szingapúri MTCS is. A harmadik féltől származó ellenőrzések – például a British Standards Institute által végzett – szigorú biztonsági szabályozások betartásának ellenőrzése a szabványoknak.

Az Azure által a megfelelőségi szabványok teljes listájáért tekintse meg a [megfelelőségi ajánlatokat](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy mit tesz a Microsoft az Azure-infrastruktúra biztonságossá tételéhez, tekintse meg a következőt:

- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)


