---
title: Microsoft kereskedelmi piactér – Transact-képességek
description: Ez a cikk a kereskedelmi Piactéri Transact lehetőség díjszabását, számlázását, számlázását és kifizetési szempontjait ismerteti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9797401fd96bf052fdc9de0c2c2ceaf88b3d46b6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504209"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Kereskedelmi Piactéri Transact-képességek

Ez a cikk a Microsoft kereskedelmi piactér díjszabási, számlázási, számlázási és kifizetési szempontjait ismerteti.

## <a name="transactions-by-listing-option"></a>Tranzakciók listázási beállítás szerint

A kiadó vagy a Microsoft felelős a kereskedelmi piactéren található ajánlatokhoz tartozó szoftverlicenc-tranzakciók kezeléséért. Az ajánlathoz választott listázási lehetőség határozza meg, hogy ki felügyeli a tranzakciót. Az egyes közzétételi lehetőségek elérhetőségével és magyarázatával kapcsolatban lásd: [Bevezetés a listaelemek](determine-your-listing-type.md) használatába

### <a name="contact-me-free-trial-and-byol-options"></a>Kapcsolatfelvétel, ingyenes próbaverzió és BYOL lehetőségek

A kiadók választhatják a _kapcsolatfelvételi_ és az _ingyenes próbaverziót_, a promóciós és a felhasználói beszerzési lehetőségeket. Egyes ajánlati típusok esetén a kiadók választhatják a _saját licenc_ használata (BYOL) lehetőséget, amellyel az ügyfelek vásárolhatnak előfizetést az ajánlathoz az Ön által közvetlenül megvásárolt licenccel. Ezekkel a lehetőségekkel a Microsoft nem vesz részt közvetlenül a kiadó szoftverlicenc-tranzakciójában, és nincs kapcsolódó tranzakciós díj, így a kiadók megtartják az összes bevételt.

Ezekkel a lehetőségekkel a közzétevők felelősek a szoftverlicenc-tranzakció valamennyi aspektusának támogatásához. Ez magában foglalja a következőket: de nem korlátozódik a rendelésre, a teljesítésre, a mérésre, a számlázásra, a számlázásra, a fizetésre és a gyűjtésre. A kapcsolattartási adatok listázása lehetőséggel a közzétevők az ügyféltől gyűjtött kiadói szoftverlicenc-díjak 100%-át őrzik meg.

### <a name="transact-publishing-option"></a>Transact közzétételi lehetőség

A Microsoft kereskedelmi képességeinek kihasználása a Microsoft kereskedelmi lehetőségeinek kihasználásával lehetővé teszi, hogy teljes körű tapasztalatot biztosítson a felderítéstől és az értékeléstől a vásárlásig és a megvalósításig. Egy olyan _, a Microsoft által biztosított ajánlat,_ amelynek keretében a Microsoft megkönnyíti a szoftvergyártók számára a licencek cseréjét a kiadó nevében. A Transact-ajánlatok számlázása egy meglévő Microsoft-előfizetés vagy hitelkártya alapján történik, amely lehetővé teszi a Microsoft számára, hogy a közzétevő nevében üzemeltetje a Cloud Marketplace-tranzakciókat.

Akkor válassza a Transact lehetőséget, amikor új ajánlatot hoz létre a partner Centerben. Ez a beállítás csak akkor fog megjelenni, ha a Transact elérhető az ajánlat típusaként.

## <a name="transact-overview"></a>A Transact áttekintése

A Transact kapcsoló használata esetén a Microsoft engedélyezi harmadik féltől származó szoftverek értékesítését és néhány ajánlat típusának telepítését az ügyfél Azure-előfizetésében. A kiadónak meg kell fontolnia az infrastruktúra-díjak és a saját szoftverlicenc-díjak számlázását az ajánlat díjszabási modelljének kiválasztásakor.

A Transact Publishing lehetőség jelenleg a következő típusú ajánlatok esetében támogatott:

| Csomag típusa | Számlázási lépésszám | Mért számlázás | Díjszabási modell |
| ------------ | ------------- | ------------- | ------------- |
| Azure-alkalmazás<br>(Felügyelt alkalmazás) | Havonta | Yes | Használat-alapú |
| Azure-beli virtuális gép | Havi | No | Használat-alapú, BYOL |
| Szolgáltatott szoftver (SaaS) | Havi és éves | Yes | Átalány, felhasználónként, használat alapján. |
|||||

`*` Az Azure-beli virtuális gépek támogatják a használaton alapuló számlázási terveket. A csomagok számlázása havonta történik az előfizetés óránkénti használatáért, a mag méretétől, illetve a piac és az alapméret használata alapján.

### <a name="metered-billing"></a>Mért számlázás

A _piactér-mérési szolgáltatás_ lehetővé teszi, hogy a szerződésben foglalt havi vagy éves költségek mellett megadhatja az utólagos elszámolású (fogyasztáson alapuló) díjakat is. A piactér-mérési szolgáltatási dimenziók használati költségeit, például a sávszélességet, a jegyeket vagy a feldolgozott e-maileket használhatja fel. Az SaaS-ajánlatok díjas számlázásával kapcsolatos további információkért lásd: [az SaaS díjszabása a kereskedelmi Piactéri mérési szolgáltatás használatával](./partner-center-portal/saas-metered-billing.md). Az Azure-alkalmazások díjszabásával kapcsolatos további információkért lásd: [felügyelt alkalmazás mért számlázása](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Számlázási infrastruktúra költségei

A **Virtual Machines** és az **Azure-alkalmazások** esetében az Azure-infrastruktúra használati díjait az ügyfél Azure-előfizetése alapján számoljuk el. Az infrastruktúra használati díjainak kiszámítása és megjelenítése az ügyfél számláján a szoftverszolgáltató licencdíjaitól elkülönítve történik.

Az **SaaS-alkalmazások** esetében a közzétevőnek az Azure-infrastruktúra használati díjait és szoftverlicenc-díját kell megadnia egyetlen Cost-elemként. Az ügyfél számára átalány-díj formájában történik. Az Azure-infrastruktúra használatát a rendszer felügyeli, és közvetlenül a közzétevőnek számlázza. Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat. A kiadók általában az Azure-infrastruktúra használati díjait az szoftverlicenc díjszabása szerint csomagolják. A szoftver licencelési díjait nem számítjuk fel, vagy nem a felhasználói felhasználás alapján.

## <a name="pricing-models"></a>Díjszabási modellek

A használt tranzakciós lehetőségtől függően az előfizetés díja a következő:

- Letöltés **(ingyenes)**: szoftverlicenc díjmentes. Az ingyenes ajánlatokat nem lehet fizetős ajánlatra konvertálni. Az ügyfeleknek fizetős ajánlatot kell rendelniük.
- **Saját licenc** használata (BYOL): Ha egy ajánlat szerepel a kereskedelmi piactéren, a szoftveres licencekre vonatkozó díjak közvetlenül a közzétevő és az ügyfél között kezelhetők. A Microsoft csak az Azure-infrastruktúra használati díjait terheli az ügyfél Azure-előfizetési fiókjával.
- **Előfizetés díjszabása**: a szoftverlicenc-díjak havi vagy éves, ismétlődő előfizetési díjat számítanak fel, amelyet átalányként vagy ülőhelyként számítunk fel. Az ismétlődő előfizetési díjakat nem számítjuk fel a félidős ügyfelek lemondása vagy a használaton kívüli szolgáltatások esetében. Ha az ügyfél frissíti vagy visszaminősíti az előfizetést az előfizetési időszak közepére, akkor az ismétlődő előfizetési díjak is eltérhetnek.
- **Használaton alapuló díjszabás**: az Azure-beli virtuális gépekkel kapcsolatos ajánlatok esetén az ügyfeleket az ajánlat használati mértéke alapján számítjuk fel. A virtuálisgép-lemezképek esetében az ügyfeleknek a közzétevő által beállított, óránkénti Azure Marketplace-díjat kell fizetniük a virtuálisgép-lemezképből üzembe helyezett virtuális gépek használatához. Az óradíj a virtuális gépek méretétől függetlenül egységes vagy eltérő lehet. A percek után a részleges órákat számítjuk fel. A csomagok számlázása havonta történik.
- Forgalmi díjas **díjszabás**: az Azure-alkalmazások és az SaaS-ajánlatok esetében a kiadók a [piactér-mérési szolgáltatás](./partner-center-portal/marketplace-metering-service-apis.md) segítségével számlázzák a használatban lévő egyéni mérési méretek alapján. Ezek a változások a szerződésben foglalt havi vagy éves költségek mellett (jogosultság) is szerepelnek. Az egyéni mérési méretek például a sávszélesség, a jegyek vagy a feldolgozott e-mailek. A kiadók meghatározhatnak egy vagy több mért dimenziót az egyes csomagokhoz, de legfeljebb 30 per ajánlatot. A kiadók feladata az egyes ügyfelek általi használat nyomon követése, az ajánlatban meghatározott mérőszámok alapján. Az eseményeket egy órán belül jelenteni kell a Microsoftnak. A Microsoft a megfelelő számlázási időszakra vonatkozóan a kiadók által jelentett használati információk alapján számítja fel az ügyfeleket.
- **Ingyenes próbaverzió**: az ajánlat típusától függően legfeljebb 30 napig terjedő szoftverlicenc-díjat számítunk fel. Ha a kiadók ingyenes próbaidőszakot biztosítanak több csomagra ugyanazon az ajánlaton belül, az ügyfelek egy másik csomagra válthatnak egy ingyenes próbaverzióra, de a próbaidőszak nem indul újra. A virtuális gépekkel kapcsolatos ajánlatok esetében az ügyfeleknek az Azure-infrastruktúra költségeit kell megfizetniük az ajánlat próbaidőszakos használata esetén. A próbaidőszak lejárta után a rendszer automatikusan felszámítja az ügyfeleket a standard díjszabás alapján, kivéve, ha a próbaidőszak vége előtt lemondják.

> [!NOTE]
> Az olyan ajánlatok, amelyeknek a használata a megoldás alkalmazása után történik, nem jogosultak a visszatérítésre.

Azok a kiadók, akik szeretnék módosítani az ajánlathoz kapcsolódó használati díjakat, először el kell távolítaniuk az ajánlatot (vagy az ajánlaton belüli csomagot) a kereskedelmi piactéren. Az eltávolítást a [Microsoft kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560)követelményeinek megfelelően kell elvégezni. Ezután a közzétevő közzétehet egy új ajánlatot (vagy megtervezheti az ajánlaton belül), amely tartalmazza az új használati díjakat is. További információ az ajánlat vagy a csomag eltávolításáról: [ajánlat vagy csomag eladásának leállítása](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Ingyenes, kapcsolatfelvétel és saját licencek (BYOL) díjszabása

Ha a letöltés után (ingyenes), Kapcsolatfelvétel vagy BYOL lehetőséggel tesz közzé ajánlatot, a Microsoft nem játszik szerepet a szoftverlicenc-díjak értékesítési tranzakciójának megkönnyítésében. A közzétevő megtartja a szoftverlicenc-díjak 100%-át.

### <a name="usage-based-and-subscription-pricing"></a>A használat és az előfizetés díjszabása

Ha használati vagy előfizetési tranzakcióként tesz közzé ajánlatot, a Microsoft biztosítja a technológiát és szolgáltatásokat a szoftverlicenc-vásárlások, a visszaadott adatok és a díjak feldolgozásához. Ebben a forgatókönyvben a közzétevő engedélyezi a Microsoft számára, hogy ügynökként működjön ezen célokból. A közzétevő lehetővé teszi a Microsoft számára, hogy elősegítse a szoftverlicencelési tranzakciót. A kiadó, megtarthatja a megjelölését eladóként, szolgáltatóként, terjesztőként és licencelőként.

A Microsoft lehetővé teszi, hogy az ügyfelek a Microsoft kereskedelmi piactérének és a végfelhasználói licencszerződésének feltételeitől függően megrendelést, licencet és használati feltételeket alkalmazzanak. Meg kell adnia a saját végfelhasználói licencszerződését, vagy az ajánlat létrehozásakor ki kell választania a [standard szerződést](./standard-contract.md) .

### <a name="free-software-trials"></a>Ingyenes szoftveres próbaverziók

A Transact Publishing forgatókönyvek esetében az előfizetéstől függően 30 – 120 napig ingyenesen elérhetővé teheti a szoftverlicenc-t. Az ügyfelek a megfelelő Azure-infrastruktúra-használatot fogják módosítani.

### <a name="examples-of-pricing-and-store-fees"></a>Példák a díjszabásra és a tárolási díjakra

**Használat-alapú**

A használat alapú díjszabás a következő felépítésű:

| **A licenc díja** | **$1,00/óra** |
|---------|---------|
| Azure-használati díj (D1/1-Core) | $0,14/óra |
| _Az ügyfelet a Microsoft számlázza_ | _$1,14/óra_ |
||

Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $1,14-ra.

| **Microsoft-számlák** | **$1,14/óra**  |
|---------|---------|
| A Microsoft a licencelési díj 80%-ában fizet | $0,80/óra |
| A Microsoft a licencelési díj 20%-át megtartja  |  $0,20/óra |
| A Microsoft az Azure használati díja 100%-át őrzi meg | $0,14/óra |
||

**Saját licenc használata (BYOL)**

A BYOL a következő Cost-struktúrával rendelkezik:

| **A licenc díja** | **Licencszerződés megtárgyalása és számlázása** |
|---------|---------|
|Azure-használati díj (D1/1-Core)    |   $0,14/óra     |
| _Az ügyfelet a Microsoft számlázza_ | _$0,14/óra_ |
||

Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $0,14-ra.

| **Microsoft-számlák** | **$0,14/óra** |
|---------|---------|
| A Microsoft megtartja az Azure használati költségeit | $0,14/óra |
| A Microsoft a licencelési díj 0%-át őrzi meg | $0,00/óra |
||

**SaaS-alkalmazás előfizetése**

Az SaaS-előfizetések díjszabása átalány vagy felhasználónként havi vagy éves alapon történhet. Ha a SaaS-ajánlathoz engedélyezi az  **értékesítés a Microsoft** számára lehetőséget, a következő Cost-struktúra áll rendelkezésére:

| **A licenc díja** | **$100,00/hó** |
|--------------|---------|
| Azure-használati díj (D1/1-Core) | A számlázás közvetlenül a közzétevőre történik, nem az ügyfél |
| _Az ügyfelet a Microsoft számlázza_ | _$100,00/hó (a kiadónak az összes felmerült vagy továbbított infrastruktúra-költséget kell figyelembe vennie a licenc díja alapján)_ |
||

Ebben az esetben a Microsoft $100,00-es számlákat állít be a szoftverlicenc számára, és kifizeti az $80,00-es vagy a $90,00-as összeget attól függően, hogy az ajánlat a csökkentett tárolási szolgáltatási díjat is megfelel-e.

| **Microsoft-számlák** | **$100,00/hó** |
|---------|---------|
| A Microsoft a licencelési díj 80%-ában fizet <br> \* A Microsoft a licencelési díj 90%-át fizet minden minősített SaaS-alkalmazáshoz | $80,00/hó <br> \* $90,00/hó |
| A Microsoft a licencelési díj 20%-át megtartja <br> \* A Microsoft minden minősített SaaS-alkalmazás esetében megőrzi a licencek költségeit. | $20,00/hó <br> \* $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Kereskedelmi Piactéri szolgáltatási díjak

20%-os standard tárolási szolgáltatási díjat számítunk fel, amikor az ügyfelek a kereskedelmi piactéren vásárolják meg a Transact ajánlatot. A díj részleteiért lásd: a [Microsoft kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560)5c. szakasza.

A kereskedelmi piactéren közzétett egyes Transact-ajánlatok esetében 10%-os kedvezményes tárolási szolgáltatási díjat igényelhet. Ahhoz, hogy az ajánlat megfelelő legyen, a Microsoft számára meg kell jelölnie az _Azure IP közös értékesítésének incentivized_. A jogosultságot az egyes naptári hónapok végét megelőzően legalább öt munkanapon belül teljesíteni kell, hogy megkapják a Piactéri szolgáltatási díjat. Ha a jogosultság teljesül, a csökkentett szolgáltatási díjat az összes tranzakció a következő hónap első napján érvényesnek ítéli meg, amíg az _Azure-beli IP-címek közös értékesítésének incentivized_ állapota elvész. Az IP-címek közös értékesítésének támogathatóságával kapcsolatos további információkért lásd: [a közös értékesítésre vonatkozó követelmények](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

A Piactéri szolgáltatási díj az Azure IP-incentivized SaaS, virtuális gépek, felügyelt alkalmazások és minden más, a kereskedelmi piactéren elérhetővé tett IaaS-megoldásra vonatkozik. Az egy Microsoft Teams-alkalmazáshoz vagy legalább két Microsoft 365-bővítményhez (Excel, PowerPoint, Word, Outlook és SharePoint) társított fizetős SaaS-ajánlatok, amelyeket a Microsoft AppSource tesznek közzé, a kedvezményhez is bejelentkezhetnek.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Ügyfél-számlázás, fizetés, Számlázás és gyűjtemények

**Számlázás és fizetés**: az ügyfél által előnyben részesített számlázási módszert használhatja előfizetés vagy [áttéréshez](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) szoftverlicenc-díjak továbbítására.

**Nagyvállalati szerződés**: Ha az ügyfél előnyben részesített számlázási módszere a Microsoft nagyvállalati szerződés, a szoftverlicenc-díjat a jelen számlázási módszer alapján részletezett költségként számítjuk fel, az Azure-specifikus használati költségektől elkülönítve.

**Bankkártyák és havi számla**: az ügyfelek bankkártyával és havi számlával is fizethetnek. Ebben az esetben a szoftverlicenc-díjat a Nagyvállalati Szerződés-forgatókönyvhöz hasonlóan számítjuk fel, amely az Azure-specifikus használati költségektől függ.

**Ingyenes kreditek és pénzügyi kötelezettségvállalás**: egyes ügyfelek a nagyvállalati szerződés pénzügyi kötelezettségvállalásával, vagy az Azure-használathoz biztosított ingyenes Kredittel választhatnak előre fizetést az Azure-ban. Bár ezek a kreditek felhasználhatók az Azure-használat megfizetésére, nem használhatók a kiadói szoftverlicenc-díjakért.

**Számlázás és gyűjtemények**: a közzétevő szoftverlicenc-számlázása az ügyfél által választott számlázási módszer használatával történik, és a számlázási ütemtervet követi. A Nagyvállalati Szerződés nélküli ügyfelek számlázása havonta történik a piactér szoftverlicenc esetében. A Nagyvállalati Szerződés rendelkező ügyfelekért havonta kell fizetnie egy negyedévente bemutatott számlán keresztül.

Ha az előfizetés vagy az utólagos elszámolású (más néven használaton alapuló) díjszabási modell van kiválasztva, a Microsoft a közzétevő ügynöke működik, és felelős a számlázás, a fizetés és a gyűjtés minden vonatkozásában.

### <a name="publisher-payout-and-reporting"></a>Kiadói kifizetés és jelentéskészítés

A Microsoft által ügynökként gyűjtött licencelési díjakat 20%-os tranzakciós díj terheli, kivéve, ha másként van megadva, és a kiadói kifizetés időpontjában le vannak vonva.

Az ügyfelek általában a Nagyvállalati Szerződés vagy egy hitelkártya-alapú utólagos elszámolású szerződés használatával vásárolnak. A szerződés típusa határozza meg a számlázást, a számlázást, a gyűjtést és a kifizetés időzítését.

>[!NOTE]
>A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója a partner Center Analytics szakaszán keresztül érhető el.

#### <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

További információ és jogi szabályzatok: [Microsoft kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560). A számlázási kérdésekkel kapcsolatos segítségért forduljon a [kereskedelmi piactér közzétevője támogatási szolgálatához](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-követelmények

Ez a szakasz a különböző ajánlati típusok Transact-követelményeit ismerteti.

### <a name="requirements-for-all-offer-types"></a>Az összes ajánlat típusára vonatkozó követelmények

- A Transact közzétételi lehetőséghez Microsoft-fiók és pénzügyi információk szükségesek, az ajánlat díjszabási modelljétől függetlenül.
- A kötelező pénzügyi információk közé tartozik a kifizetési fiók és az adó profil.

A fiókok beállításával kapcsolatos további információkért lásd: [a kereskedelmi piactér fiókjának kezelése a partner Centerben](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Az egyes ajánlatok típusaira vonatkozó követelmények

A Microsofton keresztüli Transact-képesség csak a következő kereskedelmi piactér-ajánlati típusok esetében érhető el. Ez a lista azokat a követelményeket ismerteti, amelyekkel az ajánlati típusok a kereskedelmi piactéren elérhetővé tételéhez szükségesek.

- **Azure-alkalmazás (megoldás-sablon és felügyelt alkalmazási csomagok**: bizonyos esetekben az Azure-infrastruktúra használati díjait a rendszer külön adja át az ügyfélnek a szoftverlicenc-díjaktól, de ugyanarra a számlázási utasításra. Ha azonban a felügyelt alkalmazáscsomag az ISV-infrastruktúra díjaihoz van konfigurálva, az Azure-erőforrások számlázása a kiadóra történik, és az ügyfél egy átalánydíjas díjat kap, amely magában foglalja az infrastruktúra, a szoftver licencek és a felügyeleti szolgáltatások költségeit.

- **Azure virtuális gép**: válassza az ingyenes, a BYOL vagy a használati alapú árképzési modellek lehetőséget. Az ügyfél Azure-számláján a Microsoft a mögöttes Azure-infrastruktúra díjaitól függetlenül bemutatja a kiadói szoftverlicenc-díjat. Az Azure-infrastruktúra díjait a közzétevő szoftverei vezérlik.

- **SaaS-alkalmazás**: több-bérlős megoldásnak kell lennie, a hitelesítéshez [Azure Active Directoryt](https://azure.microsoft.com/services/active-directory/) kell használnia, és integrálni kell a [SaaS-megvalósítási API](partner-center-portal/pc-saas-fulfillment-api-v2.md)-kkal. Az Azure-infrastruktúra használatának felügyelete és számlázása közvetlenül az Ön számára történik (a kiadó), ezért az Azure-infrastruktúra használati díjait és a szoftveres licencelési díjakat egyetlen költségként kell elszámolni. Részletes útmutatás: SaaS- [ajánlat tervezése a kereskedelmi piactéren](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Saját csomagok

Létrehozhat egy egyéni csomagot az ajánlathoz, amely megfelel az egyeztetett, az alku-specifikus díjszabásnak vagy az egyedi konfigurációknak.

A privát csomagok lehetővé teszik, hogy a nyilvánosan elérhető ajánlatnál magasabb vagy alacsonyabb díjszabást nyújtson bizonyos ügyfeleknek. A Private-csomagok kedvezményes vagy prémium szintű ajánlathoz adhatók. Az Azure-előfizetésük a csomag szintjén történő listázásával egy vagy több ügyfél számára is elérhetővé tehető.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a közzétételi mintákat az online áruházban példákat arra, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.
- [Közzétételi útmutató az ajánlat típusa alapján](publisher-guide-by-offer-type.md).
