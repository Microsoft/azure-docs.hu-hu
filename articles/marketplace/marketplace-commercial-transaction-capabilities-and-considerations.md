---
title: Microsoft kereskedelmi piactér – Transact-képességek
description: Ez a cikk a kereskedelmi Piactéri Transact lehetőség díjszabását, számlázását, számlázását és kifizetési szempontjait ismerteti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 59768e4fc04613a5ecac6a76059382499b7aaa86
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986462"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Kereskedelmi Piactéri Transact-képességek

Ez a cikk a Microsoft kereskedelmi piactér díjszabási, számlázási, számlázási és kifizetési szempontjait ismerteti.

## <a name="transactions-by-listing-option"></a>Tranzakciók listázási beállítás szerint

A kiadó vagy a Microsoft felelős a kereskedelmi piactéren található ajánlatokhoz tartozó szoftverlicenc-tranzakciók kezeléséért. Az ajánlathoz választott listázási lehetőség határozza meg, hogy ki felügyeli a tranzakciót. Az egyes közzétételi lehetőségek elérhetőségével és magyarázatával kapcsolatban lásd: [Bevezetés a listaelemek](determine-your-listing-type.md) használatába

### <a name="contact-me-free-trial-and-byol-options"></a>Kapcsolatfelvétel, ingyenes próbaverzió és BYOL lehetőségek

A kiadók választhatják a _kapcsolatfelvételi_ és az _ingyenes próbaverziót_, a promóciós és a felhasználói beszerzési lehetőségeket. Egyes ajánlatok esetében a kiadók választhatják a saját licenc használata (BYOL) lehetőséget, amely lehetővé teszi, hogy az ügyfelek az Ön által közvetlenül vásárolt licenccel vásárolják meg az ajánlat előfizetését. Ezekkel a beállításokkal a Microsoft nem vesz részt közvetlenül a kiadó szoftverlicenc-tranzakciójában, és nincs társított tranzakciós díj.

A közzétevők felelősek a szoftverlicenc-tranzakció valamennyi aspektusának támogatásához. Ez magában foglalja a következőket: de nem korlátozódik a rendelésre, a teljesítésre, a mérésre, a számlázásra, a számlázásra, a fizetésre és a gyűjtésre. A kapcsolattartási adatok listázása lehetőséggel a közzétevők az ügyféltől gyűjtött kiadói szoftverlicenc-díjak 100%-át őrzik meg.

### <a name="transact-publishing-option"></a>Transact közzétételi lehetőség

A Microsoft kereskedelmi képességeinek kihasználása a Microsoft kereskedelmi lehetőségeinek kihasználásával lehetővé teszi, hogy teljes körű tapasztalatot biztosítson a felderítéstől és az értékeléstől a vásárlásig és a megvalósításig. Egy olyan ajánlat, amely a Microsoft számára lehetővé teszi, hogy egy szoftverlicenc-licencet a kiadó nevében biztosítson. A visszagörgethető ajánlatok számlázása egy meglévő Microsoft-előfizetés vagy egy hitelkártya alapján történik, amely lehetővé teszi a Microsoft számára, hogy a közzétevő nevében üzemeltetje a felhőalapú Piactéri tranzakciókat.

Akkor válassza a Transact lehetőséget, amikor új ajánlatot hoz létre a partner Centerben. Ez a beállítás csak akkor jelenik meg, ha a Transact elérhető az ajánlat típusaként.

## <a name="transact-overview"></a>A Transact áttekintése

A Transact kapcsoló használata esetén a Microsoft engedélyezi harmadik féltől származó szoftverek értékesítését és néhány ajánlat típusának telepítését az ügyfél Azure-előfizetésében. A kiadónak meg kell fontolnia az infrastruktúra-díjak és a saját szoftverlicenc-díjak számlázását az ajánlat díjszabási modelljének kiválasztásakor.

A Transact Publishing lehetőség jelenleg a következő típusú ajánlatok esetében támogatott:

- Virtual machines (Virtuális gépek)
- Azure-alkalmazások
- SaaS-alkalmazás

### <a name="billing-infrastructure-costs"></a>Számlázási infrastruktúra költségei

A **Virtual Machines** és az **Azure-alkalmazások** esetében az Azure-infrastruktúra használati díjait az ügyfél Azure-előfizetése alapján számoljuk el. Az infrastruktúra-használati díjak díjszabása és a szoftveres szolgáltató licencelési díjaitól függetlenül, az ügyfél számláján kell megjelenni.

**SaaS-alkalmazások** esetén a közzétevőnek az Azure-infrastruktúra használati díjait és a szoftverlicenc-díjat egyetlen Cost-elemként kell megadnia.  Az ügyfél számára átalány-díj formájában történik. Az Azure-infrastruktúra használatát a rendszer felügyeli, és közvetlenül a közzétevőnek számlázza. Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat. A kiadók általában az Azure-infrastruktúra használati díjait az szoftverlicenc díjszabása szerint csomagolják. A szoftver licencelési díjait nem számítjuk fel, vagy nem a felhasználói felhasználás alapján.

## <a name="pricing-models"></a>Díjszabási modellek

A használt tranzakciós lehetőségtől függően az előfizetés díja a következő:

- Azonnali letöltés **(ingyenes)** – szoftverlicenc díjmentes. Az ügyfelek díjmentesen vehetik igénybe az Azure Marketplace díját. Az ingyenes ajánlatokat nem lehet fizetős ajánlatra konvertálni. Az ügyfeleknek fizetős ajánlatot kell rendelniük.
- **Saját licenc** használata (BYOL) – a licenccel kapcsolatos összes díjat közvetlenül a közzétevő és az ügyfél között kezelheti. A Microsoft csak az Azure-infrastruktúra használati díjain halad át. Ha egy ajánlat szerepel a kereskedelmi piactéren, a kereskedelmi piactéren kívüli ajánlathoz hozzáférő vagy azt használó ügyfelek nem számítanak fel kereskedelmi Piactéri díjakat.
- **Előfizetés díjszabása** – a szoftverlicenc-díjak havi vagy éves, ismétlődő előfizetési díjat számítanak fel, amely átalányként vagy ülőhelyként van kiszámlázva. Az ismétlődő előfizetési díjakat nem számítjuk fel a félidős ügyfelek lemondása vagy a használaton kívüli szolgáltatások esetében. Ha az ügyfél frissíti vagy visszaminősíti az előfizetést az előfizetési időszak közepére, akkor az ismétlődő előfizetési díjak is eltérhetnek.
- **Használaton alapuló díjszabás** – az Azure-beli virtuális gépekkel kapcsolatos ajánlatok esetén az ügyfeleket az ajánlat használati mértéke alapján számítjuk fel. A virtuálisgép-lemezképek esetén az ügyfeleket a kiadók által beállított, óránkénti Azure Marketplace-díjat számítjuk fel a virtuálisgép-lemezképből üzembe helyezett virtuális gépek használatára. Az óradíj a virtuális gépek méretétől függetlenül egységes vagy eltérő lehet. A percek után a részleges órákat számítjuk fel. A csomagok számlázása havonta történik.
- Forgalmi díjas **díjszabás** – az Azure-alkalmazások és az SaaS-ajánlatok esetében a kiadók a [Piactéri mérési szolgáltatás](./partner-center-portal/marketplace-metering-service-apis.md) alapján számlázzák a felhasznált mérési méretek alapján. Például a sávszélesség, a jegyek és a feldolgozott e-mailek. A közzétevők meghatározhatnak egy vagy több mérési dimenziót az egyes csomagokhoz. A kiadók felelősek az egyes ügyfelek használatának nyomon követéséhez, az ajánlatban meghatározott minden mérőszámnál. Az eseményeket egy órán belül jelenteni kell a Microsoftnak. A Microsoft a megfelelő számlázási időszakra vonatkozóan a kiadók által jelentett használati információk alapján számítja fel az ügyfeleket.
- **Ingyenes próbaverzió** – az ajánlat típusától függően legfeljebb 30 napig terjedő szoftverlicenc-díjat számítunk fel. Ha a kiadók ingyenes próbaidőszakot biztosítanak több csomagra ugyanazon az ajánlaton belül, az ügyfelek egy másik csomagra válthatnak egy ingyenes próbaverzióra, de a próbaidőszak nem indul újra. A virtuális gépekkel kapcsolatos ajánlatok esetében az ügyfeleknek az Azure-infrastruktúra költségeit kell megfizetniük az ajánlat próbaidőszakos használata esetén. A próbaidőszak lejárta után a rendszer automatikusan felszámítja az ügyfeleket a standard díjszabás alapján, kivéve, ha a próbaidőszak vége előtt lemondják.

> [!NOTE]
> Az olyan ajánlatok, amelyeknek a használata a megoldás alkalmazása után történik, nem jogosultak a visszatérítésre.

Azok a kiadók, akik szeretnék módosítani az ajánlathoz kapcsolódó használati díjakat, először el kell távolítaniuk az ajánlatot (vagy az ajánlaton belüli csomagot) a kereskedelmi piactéren. Az eltávolítást a [Microsoft kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560)követelményeinek megfelelően kell elvégezni. Ezután a közzétevő közzétehet egy új ajánlatot (vagy megtervezheti az ajánlaton belül), amely tartalmazza az új használati díjakat is. További információ az ajánlat vagy a csomag eltávolításáról: [ajánlat vagy csomag eladásának leállítása](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Ingyenes, kapcsolatfelvétel és saját licencek (BYOL) díjszabása

Ha a letöltés után (ingyenes), Kapcsolatfelvétel vagy BYOL lehetőséggel tesz közzé ajánlatot, a Microsoft nem játszik szerepet a szoftverlicenc-díjak értékesítési tranzakciójának megkönnyítésében. A listához és az ingyenes próba-közzétételi lehetőségekhez hasonlóan a kiadó a szoftverlicenc-díjak 100%-át is megtartja.

### <a name="usage-based-and-subscription-pricing"></a>A használat és az előfizetés díjszabása

Az ajánlat felhasználó-vagy előfizetési tranzakciójának közzétételekor a Microsoft biztosítja a technológiát és szolgáltatásokat a szoftverlicenc-vásárlások, a visszaadott adatok és a jóváírások feldolgozásához. Ebben a forgatókönyvben a közzétevő engedélyezi a Microsoft számára, hogy ügynökként működjön ezen célokból. A közzétevő lehetővé teszi a Microsoft számára, hogy elősegítse a szoftverlicencelési tranzakciót, miközben megtartja a megjelölését eladóként, szolgáltatóként, terjesztőként és licencelőként.

A Microsoft lehetővé teszi, hogy az ügyfelek a Microsoft kereskedelmi piactérének és a végfelhasználói licencszerződésének feltételeitől függően megrendelést, licencet és használati feltételeket alkalmazzanak. Meg kell adnia a saját végfelhasználói licencszerződését, vagy az ajánlat létrehozásakor a [standard szerződést](./standard-contract.md) kell választania.

### <a name="free-software-trials"></a>Ingyenes szoftveres próbaverziók

A Transact Publishing forgatókönyvek esetében az előfizetéstől függően 30 – 120 napig ingyenesen elérhetővé teheti a szoftverlicenc-t. Ez a diszkont képesség nem tartalmazza az Azure-infrastruktúra használatának költségeit a partneri megoldás használatával.

### <a name="private-offers"></a>Személyes ajánlatok

Az ajánlati típusok és a számlázási modellek használata mellett egy ajánlat értékesítésére is lehetősége van, és az egyeztetett, az ügylet-specifikus díjszabással vagy az egyéni konfigurációkkal is elvégezheti a tranzakciót. Mind a három Transact közzétételi lehetőség támogatja a privát ajánlatokat.

Ez a lehetőség nagyobb vagy alacsonyabb díjszabást tesz lehetővé, mint a nyilvánosan elérhető ajánlat. Kedvezményes ajánlatokat vagy prémium szintű ajánlatot is használhat az ajánlathoz. A privát ajánlatokat egy vagy több ügyfél számára is elérhetővé teheti, ha allowlisting az Azure-előfizetését az ajánlat szintjén.

### <a name="commercial-marketplace-service-fees"></a>Kereskedelmi Piactéri szolgáltatási díjak

20%-os standard tárolási szolgáltatási díjat számítunk fel, amikor az ügyfelek a kereskedelmi piactéren vásárolják meg a Transact ajánlatot. A díj részleteiért lásd: a [Microsoft kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560)5c. szakasza.

Bizonyos, a kereskedelmi piactéren közzétett Transact-ajánlatok esetén 10%-os csökkentett tárolási szolgáltatási díjat igényelhet. Ahhoz, hogy az ajánlat megfelelő legyen, a Microsoft számára meg kell jelölnie az Azure IP közös értékesítésének incentivized. A jogosultságot az egyes naptári hónapok végét megelőzően legalább öt munkanapon belül teljesíteni kell, hogy megkapják a Piactéri szolgáltatási díjat. Ha a jogosultság teljesül, a csökkentett szolgáltatási díjat az összes tranzakció a következő hónap első napján érvényesnek ítéli meg, amíg az Azure-beli IP-címek közös értékesítésének incentivized állapota elvész. Az IP-címek közös értékesítésének támogathatóságával kapcsolatos további információkért lásd: [a közös értékesítésre vonatkozó követelmények](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

A Piactéri szolgáltatási díj az Azure IP-incentivized SaaS, virtuális gépek, felügyelt alkalmazások és minden más, a kereskedelmi piactéren elérhetővé tett IaaS-megoldásra vonatkozik. Az egy Microsoft Teams-alkalmazáshoz vagy legalább két Microsoft 365-bővítményhez (Excel, PowerPoint, Word, Outlook és SharePoint) társított fizetős SaaS-ajánlatok a következő kedvezményt kapják meg Microsoft AppSource is.

### <a name="examples"></a>Példák

**Használat-alapú** 

A használat alapú díjszabás a következő felépítésű:

|A licenc díja  | $1,00/óra   |
|---------|---------|
|Azure-használati díj (D1/1-Core)    |   $0,14/óra     |
|*Az ügyfelet a Microsoft számlázza*    |  *$1,14/óra*       |
||

Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $1,14-ra.

|Microsoft-számlák  | $1,14/óra  |
|---------|---------|
|A Microsoft a licencelési díj 80%-ában fizet|   $0,80/óra     |
|A Microsoft a licencelési díj 20%-át megtartja  |  $0,20/óra       |
|A Microsoft az Azure használati díja 100%-át őrzi meg | $0,14/óra |
||

**Saját licenc használata (BYOL)**

A BYOL a következő Cost-struktúrával rendelkezik:

|A licenc díja  | Licencszerződés megtárgyalása és számlázása  |
|---------|---------|
|Azure-használati díj (D1/1-Core)    |   $0,14/óra     |
|*Az ügyfelet a Microsoft számlázza*    |  *$0,14/óra*       |
||

Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $0,14-ra.

|Microsoft-számlák  | $0,14/óra  |
|---------|---------|
|A Microsoft megtartja az Azure használati költségeit    |   $0,14/óra     |
|A Microsoft a licencelési díj 0%-át őrzi meg   |  $0,00/óra       |
||

**SaaS-alkalmazás előfizetése**

Ezt a beállítást úgy kell beállítani, hogy a Microsofton keresztül értékesítsen, és akár havi, akár évenkénti díjszabással vagy felhasználónként. Ha a SaaS-ajánlathoz engedélyezi az **értékesítés a Microsoft** számára lehetőséget, a következő Cost-struktúra áll rendelkezésére:

| A licenc díja       | $100,00/hó  |
|--------------|---------|
| Azure-használati díj (D1/1-Core)    | A számlázás közvetlenül a közzétevőre történik, nem az ügyfél |
| *Az ügyfelet a Microsoft számlázza*    |  *$100,00/hó (a kiadónak az összes felmerült vagy továbbított infrastruktúra-költséget kell figyelembe vennie a licenc díja alapján)*  |
||

Ebben az esetben a Microsoft $100,00-es számlát küld a szoftverlicenc számára, és kifizeti a $80,00-et a közzétevőnek.

Ebben az esetben a Microsoft $100,00-es számlákat küld a szoftverlicenc számára, és kifizeti a $90,00-et a közzétevőnek:

|Microsoft-számlák  | $100,00/hó  |
|---------|---------|
|A Microsoft a licencelési díj 80%-ában fizet <br> \* A Microsoft a licencelési díj 90%-át fizet minden minősített SaaS-alkalmazáshoz   |   $80,00/hó <br> \* $90,00/hó    |
|A Microsoft a licencelési díj 20%-át megtartja <br> \* A Microsoft minden minősített SaaS-alkalmazás esetében megőrzi a licencek költségeit.  |  $20,00/hó <br> \* $10,00     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Ügyfél-számlázás, fizetés, Számlázás és gyűjtemények

**Számlázás és fizetés** – az ügyfél által előnyben részesített számlázási módszert használhatja előfizetés vagy áttéréshez szoftverlicenc-díjak továbbítására.

**Nagyvállalati szerződés** – ha az ügyfél előnyben részesített számlázási módszere a Microsoft nagyvállalati szerződés, a szoftverlicenc-díjat a számlázási módszer alapján részletezett költségként számítjuk fel, elkülönítve az Azure-specifikus használati költségektől.

**Bankkártyák és havi számla** – az ügyfelek bankkártyával és havi számlával is fizethetnek. Ebben az esetben a szoftverlicenc-díjat a Nagyvállalati Szerződés-forgatókönyvhöz hasonlóan számítjuk fel, amely az Azure-specifikus használati költségektől függ.

**Ingyenes kreditek és Azure-előfizetések** – egyes ügyfelek az Azure-előfizetéssel (korábbi nevén pénzügyi kötelezettségvállalással), nagyvállalati szerződés az Azure-ban pedig ingyenes krediteket is megválasztanak. Bár ezek a kreditek felhasználhatók az Azure-használat megfizetésére, nem használhatók a kiadói szoftverlicenc-díjakért.

**Számlázás és gyűjtemények** – a kiadói szoftverlicenc számlázása az ügyfél által választott számlázási módszer használatával történik, és a számlázási ütemtervet követi. A Nagyvállalati Szerződés nélküli ügyfelek számlázása havonta történik a piactér szoftverlicenc esetében. A Nagyvállalati Szerződés rendelkező ügyfelekért havonta kell fizetnie egy negyedévente bemutatott számlán keresztül.

Ha az előfizetés vagy az utólagos elszámolású díjszabási modell van kiválasztva, a Microsoft a közzétevő ügynöke működik, és felelős a számlázás, a fizetés és a gyűjtés minden vonatkozásában.

### <a name="publisher-payout-and-reporting"></a>Kiadói kifizetés és jelentéskészítés

A Microsoft által ügynökként gyűjtött licencelési díjakat 20%-os tranzakciós díj terheli, kivéve, ha másként van megadva, és a kiadói kifizetés időpontjában le vannak vonva.

Az ügyfelek általában a Nagyvállalati Szerződés vagy egy hitelkártya-alapú utólagos elszámolású szerződés használatával vásárolnak. A szerződés típusa határozza meg a számlázást, a számlázást, a gyűjtést és a kifizetés időzítését.

>[!NOTE]
>A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója a partner Center Analytics szakaszán keresztül érhető el.

#### <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

További információ és jogi szabályzatok: [Microsoft kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560) (a partner Centerben érhető el).

A számlázási kérdésekkel kapcsolatos segítségért forduljon a [kereskedelmi piactér közzétevője támogatási szolgálatához](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-követelmények

Ez a szakasz a különböző ajánlati típusok Transact-követelményeit ismerteti.

### <a name="requirements-for-all-offer-types"></a>Az összes ajánlat típusára vonatkozó követelmények

- A Transact közzétételi lehetőséghez Microsoft-fiók és pénzügyi információk szükségesek, az ajánlat díjszabási modelljétől függetlenül.
- A kötelező pénzügyi információk közé tartozik a kifizetési fiók és az adó profil.
- A közzétevőnek [támogatott országban vagy régióban](sell-from-countries.md)kell lennie.

A fiókok beállításával kapcsolatos további információkért lásd: [a kereskedelmi piactér fiókjának kezelése a partner Centerben](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Az egyes ajánlatok típusaira vonatkozó követelmények

A Transact Publishing beállítás csak a következő Piactéri ajánlatok esetében használható:

- **Azure-beli virtuális gép** – válassza az ingyenes, a saját licencek vagy a használaton alapuló díjszabási modelleket, és az ajánlati szinten meghatározott csomagokként jelennek meg. Az ügyfél Azure-számláján a Microsoft a mögöttes Azure-infrastruktúra díjaitól függetlenül bemutatja a kiadói szoftverlicenc-díjat. Az Azure-infrastruktúra díjait a közzétevő szoftver használja.

- **Azure-alkalmazás: megoldás-sablon vagy felügyelt** alkalmazás – egy vagy több virtuális gépet kell kiépítenie, és a virtuális gép díjszabásának összegével kell lekérnie. Egyetlen csomagban felügyelt alkalmazások esetén a virtuális gépek díjszabása helyett egy átalánydíjas havi előfizetés is kiválasztható a díjszabási modellként. Bizonyos esetekben az Azure-infrastruktúra használati díjait a rendszer külön adja át az ügyfélnek a szoftverlicenc-díjaktól, de ugyanarra a számlázási utasításra. Ha azonban a felügyelt alkalmazáshoz az ISV-infrastruktúra díjait konfigurálja, az Azure-erőforrások számlázása a kiadóra történik, és az ügyfél egy átalánydíjas díjat kap, amely magában foglalja az infrastruktúra, a szoftverlicenc és a felügyeleti szolgáltatások költségeit.

- **SaaS-alkalmazás** – több-bérlős megoldásnak kell lennie, a hitelesítéshez [Azure Active Directoryt](https://azure.microsoft.com/services/active-directory/) kell használnia, és integrálni kell a [SaaS-megvalósítási API](partner-center-portal/pc-saas-fulfillment-api-v2.md)-kkal. Az Azure-infrastruktúra használatának felügyelete és számlázása közvetlenül Önnek (a partnernek) történik, ezért az Azure-infrastruktúra használati díjait és a szoftveres licencelési díjakat egyetlen költségként kell elszámolni. Részletes útmutatásért lásd: [új SaaS-ajánlat létrehozása a kereskedelmi piactéren](./create-new-saas-offer.md).

## <a name="next-steps"></a>Következő lépések

- Az ajánlat kiválasztásának és konfigurálásának véglegesítéséhez tekintse át a támogathatósági követelményeket a közzétételi beállítások az ajánlat típusa szerint szakaszban.
- Tekintse át a közzétételi mintákat az online áruházban példákat arra, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.