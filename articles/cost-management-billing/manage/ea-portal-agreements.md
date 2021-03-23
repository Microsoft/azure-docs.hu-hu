---
title: Azure nagyvállalati szerződések és a módosításaik
description: Ez a cikk azt ismerteti, hogy milyen hatással vannak az Azure nagyvállalati szerződések és a módosításaik az Azure EA Portal használatára.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: d03ae5062cc81b3e042ed3b714838e14929dbba2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778219"
---
# <a name="azure-ea-agreements-and-amendments"></a>Azure nagyvállalati szerződések és a módosításaik

Ez a cikk bemutatja, hogyan milyen hatással lehetnek az Azure nagyvállalati szerződések és a módosításaik az Azure-szolgáltatások hozzáférésére, használatára és kifizetésére.

## <a name="enrollment-provisioning-status"></a>Regisztráció kiépítési állapota

Az új Azure-előrefizetés (korábbi nevén pénzügyi keret) kezdő dátumát a regionális műveleti központ általi feldolgozás dátuma határozza meg. Mivel az Azure EA Portalon keresztül leadott Azure-előrefizetési megrendelések feldolgozása az UTC időzónában történik, némi késést tapasztalhat, ha az Azure-előrefizetés megrendelését egy másik régióban dolgozzák fel. A megrendelésen az érvényesség kezdő dátuma az Azure-előrefizetés kezdetét mutatja. Az érvényesség kezdő dátuma az az időpont, amikor az Azure-előrefizetés megjelenik az Azure EA Portalon.

## <a name="support-for-enterprise-customers"></a>Vállalati ügyfelek támogatása

 Néhány ügyfél számára elérhető az Azure [Nagyvállalati szerződéshez kapcsolódó támogatási csomagajánlat](https://azure.microsoft.com/offers/enterprise-agreement-support/).

## <a name="enrollment-status"></a>A regisztráció állapota

A regisztrációk az alábbi állapotértékek egyikével rendelkeznek. Ezek az értékek határozzák meg, hogy Ön hogyan használhatja és érheti el a regisztrációt. A regisztráció állapotától függ, hogy melyik szakaszban van jelenleg a regisztrációja. Jelzi, hogy aktiválni kell-e a regisztrációt a használathoz. Arról is tájékoztatja, ha a kezdeti időszak lejárt, és fizetnie kell a túlhasználatért.

**Függőben** – A regisztrációs rendszergazdának be kell jelentkeznie az Azure EA Portalra. A bejelentkezést követően a regisztráció **Aktív** állapotra vált.

**Aktív** – A regisztráció elérhető és használható. Fiókokat és előfizetéseket hozhat létre az Azure EA Portalon. A regisztráció a nagyvállalati szerződés záró dátumáig aktív marad.

**Határozatlan időre meghosszabbítva** – A Határozatlan időre meghosszabbítva állapot a nagyvállalati szerződés záró dátuma után jelenik meg. Mielőtt az EA-regisztráció elérné a nagyvállalati szerződés záró dátumát, a regisztrációs rendszergazdának el kell döntenie, hogy:

- Megújítja a regisztrációt további Azure-előrefizetés hozzáadásával
- Átviszi a meglévő regisztrációt egy új regisztrációba
- Migrálást végez a Microsoft Online Előfizetési Programba (MOSP)
- Jóváhagyja a regisztrációhoz társított szolgáltatások letiltását

**Lejárt** – Ha az EA-regisztráció eléri a nagyvállalati szerződés záró dátumát, a regisztráció lejár. Az EA-ügyfél nem kérte az időtartam meghosszabbítását, ezért az összes szolgáltatása le van tiltva.

2019. augusztus 1-től a rendszer nem fogadja el az új lemondási űrlapokat az Azure kereskedelmi ügyfelek esetében. Ehelyett a regisztrációk határozatlan időre meg lesznek hosszabbítva. Ha abba szeretné hagyni az Azure-szolgáltatások használatát, szüntesse meg az előfizetését az [Azure Portalon](https://portal.azure.com). A partnere is benyújthat felmondási kérelmet. A kormányzati szerződéstípussal rendelkező ügyfelek esetén nincs változás.

**Átvitt** – Az Átvitt állapotot a rendszer azokra a regisztrációkra alkalmazza, amelyeknek a társított fiókjait és szolgáltatásait átvitték egy új regisztrációba. A regisztrációk nem kerülnek át automatikusan, ha a megújítás során új regisztrációs szám jön létre. Az előző regisztrációs számnak szerepelnie kell az ügyfél automatikus átvitelre vonatkozó megújítási kérelmén.

## <a name="partner-markup"></a>Partnerárrés

Az Azure EA Portal Partnerárrés eleme jobb költségjelentést tesz lehetővé az ügyfelek számára. Az Azure EA Portal megjeleníti a partnerek által az ügyfeleik számára konfigurált használatot és árakat.

Az árrés lehetővé teszi, hogy a partnerek rendszergazdái százalékos haszonkulccsal egészítsék ki a közvetett nagyvállalati szerződéseiket. A százalékos haszonkulcs az Azure EA Portalon található belső Microsoft-szolgáltatások adataira vonatkozik, például a fogyasztási díjakra, az Azure-előrefizetésekre és a megrendelésekre. Miután a partner közzétette az árrést, az ügyfél megtekintheti az Azure-költségeket az Azure EA Portalon. Például használat összegzése, árlisták és a letöltött használati jelentések.

2019 szeptemberétől a partnerek az időszak során bármikor alkalmazhatnak árréseket. Nem kell megvárniuk az időszak következő évfordulóját az árrés alkalmazásához.

A Microsoft semmilyen célból sem férhet hozzá a megadott árréshez és a kapcsolódó árakhoz, és nem használhatja fel azokat a csatornapartner kifejezett engedélye nélkül.

### <a name="how-the-calculation-works"></a>A számítás működése

Az LSP megad egy százalékos arányt az EA Portalon.    A portálon található összes kereskedelmi információ az LSP által megadott százalékos arányban lesz megemelve. Példa:

- Az ügyfél aláír egy 100 000 dollár értékű Azure-előrefizetési kerettel rendelkező EA-szerződést.
- Az A szolgáltatás fogyasztási díja 10 dollár/óra.
- Az LSP az EA Portalon 10%-os árrést állít be.
- Az alábbi példában látható, hogy az ügyfél hogyan látja a kereskedelmi információkat:
    - Pénzügyi egyenleg: 110 000 dollár.
    - Az A szolgáltatás fogyasztási díja: 11 dollár / óra.
    - Az A szolgáltatás használati/üzemeltetési információi 100 órás használat esetén: 1100 dollár.
    - Az ügyfél számára elérhető pénzügyi egyenleg az A szolgáltatás fogyasztásának levonása után: 108 900 dollár.

### <a name="when-to-use-a-markup"></a>Mikor érdemes árrést használni?

Akkor használja ezt a funkciót, ha ugyanolyan százalékos arányú árrést állít be az EA ÖSSZES kereskedelmi tranzakciójában, vagyis ha az Azure-előrefizetéssel kapcsolatos információkra, a fogyasztási árakra, a rendelési adatokra stb. ugyanazt az árrést alkalmazza.

Ne használja az árrés funkciót, ha:
- az Azure-előrefizetéshez és a fogyasztási árakhoz eltérő díjszabást használ;
- a különböző fogyasztási árak díjszabása eltérő.

Ha eltérő díjszabást használ a különböző fogyasztási árakhoz, javasoljuk, hogy az ügyfél által is biztosítható API-kulcson alapuló egyéni megoldást fejlesszen, amellyel lekérheti a használati adatokat és jelentéseket készíthet.

### <a name="other-important-information"></a>További fontos információk

A szolgáltatás használatával megbecsülhető az Azure használatának költsége a végfelhasználó oldalán. Az LSP felelős az ügyfél minden pénzügyi EA-tranzakciójáért.

Kérjük, tekintse át a kereskedelmi információkat – pénzügyi egyenleggel kapcsolatos információkat, árlistát stb., mielőtt közzéteszi az árréssel ellátott árakat a végfelhasználók számára.

### <a name="how-to-add-a-price-markup"></a>Árrés hozzáadása

**Első lépés: Árrés hozzáadása**

1. Az Enterprise Portalon kattintson a bal oldali navigációs menüben található **Jelentések** elemre.
1. A _Használati adatok összegzése_ lapon kattintson a kék **Árrés** kifejezésre.
1. Adja meg az árrés százalékos arányát (-100 és 100 között), majd kattintson az **Előnézet** elemre.


**Második lépés: Áttekintés és ellenőrzés**

Ellenőrizze az árrést tartalmazó árat a _Használati adatok összegzése_ lapon az ügyfélnézetben szereplő előrefizetési időszakra vonatkozóan. A Microsoft díjszabása továbbra is elérhető lesz a partnernézetben. A nézetek között a jobb felső sarokban található „személyek” váltókapcsolóval válthat.

1. Tekintse át az árakat az árlistán.
1. A közzététel előtt elvégezheti a módosításokat, ha kiválasztja a **Szerkesztés** lehetőséget a _Használati adatok összegzése > Ügyfélnézet_ lapján. 
   
A szolgáltatási árak és az előrefizetési egyenlegek azonos százalékkal lesznek megemelve. Ha eltérő százalékos arány van megadva a pénzügyi egyenlegnél és a fogyasztási árnál, vagy eltérő százalékos arányok vonatkoznak a különböző szolgáltatásokra, ne használja ezt a funkciót.

**Harmadik lépés: Közzététel**

A díjszabás áttekintése és ellenőrzése után kattintson **Közzététel** elemre.
  
Az árréssel ellátott díjszabás a Közzététel lehetőség kiválasztása után azonnal elérhető lesz a vállalati rendszergazdák számára. Az árrést nem lehet szerkeszteni. Ha módosítani kívánja, le kell tiltania az árrést, és az első lépéstől kell kezdenie a folyamatot.

### <a name="which-enrollments-have-a-markup-enabled"></a>Mely regisztrációk esetén engedélyezett az árrés használata?

Ha szeretné megnézni, hogy egy regisztrációban van-e közzétett árrés, kattintson a bal oldali navigációs menüben található **Kezelés** elemre, majd a **Regisztráció** fülre. Jelölje ki a regisztrációs mezőt, és tekintse meg az árrés állapotát a _Regisztráció részletei_ szakaszban. Az árrés szolgáltatás aktuális állapota Letiltott, Előzetes verzió vagy Közzétett lehet.

### <a name="how-can-the-customer-download-usage-estimates"></a>Hogyan töltheti le az ügyfél a becsült használati adatait?

A partnerárrés közzététele után a közvetett ügyfél hozzáfér a havi egyenleget és díjakat összegző .csv fájlokhoz és a használati adatokat tartalmazó .csv fájlokhoz. A használati adatokat összegző fájlok tartalmazzák az erőforrások díját és a kibővített költségeket is.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Partnerként hogyan adhatok meg árrést olyan meglévő EA-ügyfelekhez, akik korábban más partnerhez voltak rendelve?
A partnerek a csatornapartner módosítása után használhatják az árrés szolgáltatást (az Azure EA Portalon); nem kell kivárniuk a következő évi időszakot.


## <a name="resource-prepayment-and-requesting-quota-increases"></a>Erőforrásokra vonatkozó előrefizetés és kvótanövelések kérése

**A rendszer a következő alapértelmezett kvótákat követeli meg előfizetésenként:**

| **Erőforrás** | **Alapértelmezett kvóta** | **Megjegyzések** |
| --- | --- | --- |
| Microsoft Azure Compute Instances | 20 egyidejű kis méretű számítási példány, vagy ezzel egyenértékű, más méretű számítási példányok. | A következő táblázat bemutatja, hogyan számítható ki a kis méretű példányoknak megfelelő szám:<ul><li> Nagyon kicsi – 1 egyenértékű kis méretű példány </li><li> Kicsi – 1 egyenértékű kis méretű példány </li><li> Közepes – 2 egyenértékű kis méretű példány </li><li> Nagy – 4 egyenértékű kis méretű példány </li><li> Nagyon nagy – 8 egyenértékű kis méretű példány </li> </ul>|
| Microsoft Azure Compute Instances v2-alapú virtuális gépek | EA: 350 mag | GA IaaS v2 virtuális gépek:<ul><li> A0\_A7 család – 350 mag </li><li> B\_A0\_A4 család – 350 mag </li><li> A8\_A9 család – 350 mag </li><li> DF család – 350 mag</li><li> GF – 350 mag </li></ul>|
| Microsoft Azure Hosted Services | 6 üzemeltetett szolgáltatás | Az üzemeltetett szolgáltatások ezen korlátja nem növelhető előfizetésenként hatnál többre. Ha további üzemeltetett szolgáltatásokra van szüksége, adjon hozzá további előfizetéseket. |
| Microsoft Azure Storage | 5 tárfiók, egyenként 100 TB-os maximális méretűek. | A tárfiókok számát előfizetésenként legfeljebb 20-ra növelheti. Ha további tárfiókokra van szüksége, adjon hozzá további előfizetéseket. |
| SQL Azure | 149, bármelyik típusú adatbázis (Web Edition vagy Business Edition). |   |
| Hozzáférés-vezérlés | 50 névtér fiókonként. 100 millió hozzáférés-vezérlési tranzakció havonta |   |
| Service Bus | 50 névtér fiókonként. 40 Service Bus-kapcsolat | Azok az ügyfelek, akik csatlakozási csomagokon keresztül vásárolnak Service Bus-kapcsolatokat, a megvásárolt csatlakozási csomag és a következő legmagasabb csatlakozási csomag közötti középponttal megegyező kvótaszámmal fognak rendelkezni. Az 500-as csomagokat választó ügyfeleknek 750-es kvótája lesz. |

## <a name="resource-prepayment"></a>Erőforrás előrefizetése

A Microsoft a vásárlóknak a megvásárolt havi előrefizetésben (a szolgáltatásokra vonatkozó előrefizetésben) foglalt, legalább a kapcsolódó használatszintnek megfelelő szolgáltatásokat nyújt. Ugyanakkor a szolgáltatásokra vonatkozó erőforrások használatszintjének (például a futó számítási példányok számának vagy a használt tárolókapacitásnak) bármilyen növelése a szolgáltatásokra vonatkozó erőforrások rendelkezésre állásának függvényében teljesíthető.

A fentiekben ismertetett kvóták egyike sem jelent szolgáltatásokra vonatkozó előrefizetést. A Microsoft által a szolgáltatásokra vonatkozó előrefizetés részeként egyidejűleg nyújtott, kis méretű számítási példányok számának meghatározása a következő módon történik: a kötelezettségvállalásnak megfelelő, havonta megvásárolt kis méretű számítási példányokra vonatkozó üzemórák száma osztva az év legrövidebb hónapjának óraszámával (azaz a februárra vonatkozó 672 órával).

## <a name="requesting-a-quota-increase"></a>Kvótanövelés kérése

A kvótanövelést bármikor kérheti egy [online kérés](https://ms.portal.azure.com/) benyújtásával. A kérés feldolgozásához adja meg a következő információkat:

- Az előfizetéséhez tartozó fióktulajdonos Microsoft-fiókja, munkahelyi vagy iskolai fiókja. Ezzel az e-mail-címmel jelentkezhet be a Microsoft Azure Portalra az előfizetés(ek) kezeléséhez. Ellenőrizze azt is, hogy ez a fiók egy EA-regisztrációhoz van-e társítva.
- Az erőforrás(ok) és az összeg, amellyel a kvótáját emelni kívánja.
- A szolgáltatáshoz társított előfizetés-azonosító az Azure Developer Portalon.
  - Az előfizetés-azonosító beszerzésével kapcsolatos információért [forduljon az ügyfélszolgálathoz](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="plan-skus"></a>Csomag termékváltozatai

A csomag termékváltozatai lehetővé teszik, hogy az integrált szolgáltatáscsomagot kedvezményes díjszabással vásárolja meg. A csomag termékváltozatait úgy tervezték, hogy további integrált ajánlatokkal és csomagokkal egészítsék ki egymást, ezzel nagyobb költségmegtakarítást biztosítva.

Erre példa az Operations Management Suite- (OMS-) előfizetés. Az OMS-sel egyszerűen érheti el a felhőalapú felügyeleti funkciók teljes körét, beleértve az elemzéseket, a konfigurálást, az automatizálást, a biztonságot, a biztonsági mentést és a vészhelyreállítást. Az OMS-előfizetések tartalmazzák a System Center-összetevők jogosultságait, hogy teljes körű megoldást nyújtsanak a hibridfelhő-környezetekhez.

A vállalati rendszergazdák a következő lépések végrehajtásával rendelhetik hozzá az Enterprise Portalon a fióktulajdonosokat a korábban megvásárolt csomagok termékváltozatainak kiépítéséhez:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Árlista megtekintése a szolgáltatási keret ellenőrzéséhez

1. Jelentkezzen be vállalati rendszergazdaként.
1. Kattintson a bal oldali navigációs területen lévő **Jelentések** elemre.
1. Kattintson az **Árlista** fülre.
1. Kattintson a jobb felső sarokban lévő Letöltés ikonra.
1. Keresse meg a megfelelő csomag termékváltozatainak cikkszámait a „Szolgáltatási keret” oszlop szűrőjével, és válassza a „0”-nál nagyobb értékeket.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Új előfizetések létrehozása meglévő/új fióktulajdonosként

**Első lépés: Bejelentkezés a fiókba**
1. Az Azure EA Portalon válassza a **Kezelés** fület, majd a felső menüben navigáljon az **Előfizetés** elemre.
1. Ellenőrizze, hogy a fiók tulajdonosaként jelentkezett-e be.
1. Kattintson az **+Előfizetés hozzáadása** elemre.
1. Kattintson a **Purchase** (Vásárlás) gombra.

Amikor első alkalommal ad hozzá egy előfizetést a fiókjához, meg kell adnia a kapcsolattartási adatait. Későbbi előfizetések hozzáadásakor a rendszer kitölti a kapcsolattartási adatait.

Amikor első alkalommal ad előfizetést a fiókjához, el kell fogadnia a MOSA-szerződést és egy díjkonstrukciót. Ezek a szakaszok nem vonatkoznak a Nagyvállalati Szerződéssel rendelkező ügyfelekre, de jelenleg szükségesek az Ön előfizetésének létrehozásához. A Microsoft Azure Nagyvállalati Szerződés regisztrációs kiegészítése felülírja a fenti elemeket, így az Ön szerződéses jogviszonya nem módosul. Jelölje be a feltételek elfogadását jelző jelölőnégyzetet.

**Második lépés: Előfizetés nevének frissítése**

Minden új előfizetés az alapértelmezett „Microsoft Azure Enterprise” előfizetésnévvel jön létre. Fontos, hogy frissítse az előfizetés nevét, hogy megkülönböztesse a vállalati regisztráción belüli többi előfizetéstől, és gondoskodjon arról, hogy a vállalati szintű jelentéseken felismerhető legyen.

Kattintson az **Előfizetések** elemre, a létrehozott előfizetésre, majd az **Előfizetés adatainak szerkesztése** elemre.

Frissítse az előfizetés nevét és a szolgáltatásadminisztrátort, majd jelölje be a jelölőnégyzetet. Az előfizetés neve megjelenik a jelentéseken, és a fejlesztési portálon az előfizetéshez rendelt projekt nevét is ez adja.
Akár 24 órába is telhet, amíg az új előfizetések megjelennek az előfizetések listáján.

Csak a fióktulajdonosok tekinthetik meg és kezelhetik az előfizetéseket.

### <a name="troubleshooting"></a>Hibaelhárítás

**Függő állapotú fióktulajdonos**

Amikor az új fióktulajdonosokat (AO) először hozzáadják a regisztrációhoz, mindig „Függő” állapot jelenik meg náluk. Az aktiválási üdvözlő e-mail fogadása után az AO bejelentkezhet és aktiválhatja a fiókját. Az aktiválással a fiók állapota „Függő” értékről „Aktív” értékre módosul.

**A csomag termékváltozatainak megvásárlása után felszámított használati díjak**

Ez a forgatókönyv akkor fordul elő, ha az ügyfél nem megfelelő regisztrációs szám alatt telepített szolgáltatásokat, vagy nem megfelelő szolgáltatásokat választott.

Annak ellenőrzéséhez, hogy a megfelelő regisztráció alatt végzi-e az üzembe helyezést, megtekintheti a belefoglalt egységekre vonatkozó adatokat az árlistán. Jelentkezzen be vállalati rendszergazdaként, kattintson a bal oldali navigációs sávon lévő **Jelentések** elemre, és válassza az **Árlista** fület. Kattintson a Letöltés ikonra a jobb felső sarokban, keresse meg a megfelelő csomag termékváltozatainak cikkszámait a „Szolgáltatási keret” oszlop szűrőjével, és válassza a „0”-nál nagyobb értékeket.

Győződjön meg arról, hogy az OMS-csomagja látható az árlista belefoglalt egységek területén. Ha az OMS-csomaghoz nem tartoznak belefoglalt egységek az Ön regisztrációjában, akkor lehetséges, hogy az OMS-csomag egy másik regisztrációban található. Forduljon az Azure Enterprise Portal ügyfélszolgálatához itt: [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Ha az árlistán szereplő szolgáltatások belefoglalt egységei nem egyeznek a telepített szolgáltatásokkal, például az Operational Insights Premium Data Analyzed vagy az Operational Insights Standard Data Analyzed szolgáltatással, akkor lehetséges, hogy olyan szolgáltatásokat telepíthetett, amelyeket nem fed le a bővítmény. További segítségért forduljon az Azure Enterprise Portal ügyfélszolgálatához itt: [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

**Rossz regisztrációban kiépített csomagtermékváltozat-szolgáltatások**

Ha több regisztrációval rendelkezik, és olyan regisztrációs szám alatt telepítette a szolgáltatásokat, amely nem rendelkezik OMS-csomaggal, vegye fel a kapcsolatot az Azure Enterprise Portal ügyfélszolgálatával itt: [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>További lépések

- Az Azure EA Portal használatának kezdő lépéseit az [Ismerkedés az Azure EA Portallal](ea-portal-get-started.md) című rész írja le.
- Az Azure EA Portal rendszergazdáinak érdemes elolvasniuk [a nagyvállalati szerződéses Azure Portal adminisztrációjáról](ea-portal-administration.md) szóló cikket a gyakori adminisztratív feladatok megismeréséhez.
