---
title: Közös értékesítés konfigurálása kereskedelmi Piactéri ajánlathoz | Azure piactér
description: A Microsoft értékesítési csapatai az ajánlathoz tartozó Microsoft lapján megjelenő információkat használják, és az ügyfelek igényeinek megfelelő kiértékelésével többet tudhatnak meg az ajánlatról.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: palanis-MSFT
ms.author: palanis
ms.date: 2/24/2021
ms.openlocfilehash: 6fdae505a4c2d05dc3e19d01ed52b0ef44ae3a40
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699011"
---
# <a name="configure-co-sell-for-a-commercial-marketplace-offer"></a>Közös értékesítés konfigurálása kereskedelmi Piactéri ajánlathoz

Ez a cikk azt ismerteti, hogyan konfigurálható a **közös értékesítés a Microsoft** lapján a kereskedelmi Piactéri ajánlathoz. Az ezen a lapon található információk megadása teljes mértékben nem kötelező, de a [közös értékesítésre kész és az IP-incentivized állapot](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status)eléréséhez szükséges. Az Ön által megadott információkat a Microsoft értékesítési csapatai fogják használni, hogy az Ön ajánlatával kapcsolatos további információkat kapjanak az ügyfelek igényeinek megfelelő kiértékelése során. Ez az információ nem érhető el közvetlenül az ügyfeleknek. További információ a közös értékesítéssel kapcsolatban: [közös értékesítés a Microsoft-értékesítők és-partnerek áttekintése](marketplace-co-sell.md) és [a Microsoft együttes értékesítése](https://partner.microsoft.com/membership/co-sell-with-microsoft).

A közös értékesítés lehetőség a következő típusú ajánlatokhoz érhető el.

- Azure-alkalmazás
- Azure-tároló
- Azure-beli virtuális gép
- Tanácsadási szolgáltatás
- Dynamics 365 a Customer engagement & PowerApps
- A Dynamics 365 for Operations
- Dynamics 365 Business Central
- IoT Edge modul
- Felügyelt szolgáltatás
- Power BI alkalmazás
- Szolgáltatott szoftver (SaaS)

## <a name="go-to-the-co-sell-with-microsoft-tab"></a>Ugrás a közös értékesítés a Microsoft lapra

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
1. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.
    > [!TIP]
    > Ha nem látja a **kereskedelmi piactért** a bal oldali navigációs sávon, [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](./partner-center-portal/create-account.md) , és ellenőrizze, hogy a fiókja regisztrálva van-e a kereskedelmi piactér programban.
1. Az **Áttekintés** lapon válassza ki a közös értékesítéshez használni kívánt ajánlatot.
    > [!NOTE]
    > A közös értékesítést konfigurálhatja egy olyan új ajánlathoz, amely még nem lett közzétéve, vagy egy már közzétett ajánlattal.

1. A bal oldali menüben válassza a **közös értékesítés a Microsofttal** lehetőséget.

    [![A bal oldali navigációs sávon mutatja be a Microsoft-hivatkozással való közös értékesítést.](./media/co-sell/co-sell-with-microsoft-tab.png)](./media/co-sell/co-sell-with-microsoft-tab.png#lightbox)

## <a name="co-sell-listings"></a>Listák közös értékesítése

A közös értékesítéssel foglalkozó listák segítenek a Microsoft értékesítési csapatoknak, hogy az ajánlatát szélesebb közönség számára forgalmazzák. A következő információkat kell megadnia a közös értékesítésre kész állapot eléréséhez:

- Microsoft-platformok (válasszon ki egyet vagy többet)
- Szegmensek (válasszon ki egyet vagy többet)
- Megoldás típusa (válasszon egyet)
- Megoldás almezője (válasszon egyet vagy kettőt)

### <a name="select-microsoft-platforms-and-segments"></a>Microsoft-platformok és-szegmensek kiválasztása

1. A **Listázás** területen válasszon ki egy vagy több olyan Microsoft-platformot, amelyhez az ajánlata épül, kiterjeszti vagy integrálja a-t.
1. Válasszon ki egy vagy több olyan piaci szegmenst, amelyet az ajánlat céloz.

### <a name="select-solution-types"></a>Megoldások típusának kiválasztása

A megoldások típusai segítenek meghatározni azokat a forgatókönyveket, amelyeket az ajánlat célja a megoldáshoz.

- A **megoldás típusának kiválasztása** listából válassza ki az ajánlatához legjobban illő megoldást. Ez a táblázat az elérhető megoldások típusait ismerteti.

***1. táblázat: elérhető megoldások típusai***

| **Megoldás típusa**    | **Leírás**  |
| :------------------- | :-------------------|
| Eszköz (hardver) | Olyan ajánlat, amely az eszköz gyártójától származó hardverek létrehozását és értékesítését foglalja magában. |
| IP (alkalmazás) | Az ügyfél általi használatra jogosult alkalmazások vagy más, szerzői joggal rendelkező anyagok. Ilyen lehet például egy olyan CRM-program, amely licenccel és telepíthető a helyszínen. |
| Felügyelt szolgáltatás | Gyakorlati szakértelem egy felhőalapú projekthez, általában folyamatosan. Például platformot és eszközöket biztosít egy online adatbázis futtatásához, a felügyelt szolgáltató által biztosított folyamatos felügyelettel. |
| Szolgáltatás | Gyakorlati szakértelem egy adott egyszeri projekthez, amely gyakran tanácsadók útján valósul meg. Például egy ügyfélhez tartozó ügyfél-adatbázis beállítása (az ügyfél felelőssége, hogy az adatbázist kézbesítés után elvégezte). |
|||

### <a name="select-solution-areas"></a>Megoldási területek kiválasztása

A megoldási területek segítenek a megoldás további meghatározásában. Ez segít a Microsoft értékesítési csapatoknak megtalálni és értelmezni a megoldás értékének kiosztását. Legalább egy értéket ki kell választania, és legfeljebb három megoldási területet kell kijelölnie az ajánlathoz. Minden egyes megoldási területen további két megoldás alterületét is kiválaszthatja.

1. Válassza a **+ megoldás hozzáadása (3 max)** hivatkozást.
1. Válasszon ki egy megoldást a megjelenő legördülő listából.
1. Válasszon ki legalább egyet és legfeljebb két megoldást alterületet. Több alterület kiválasztásához használja a `Ctrl` kulcsot (Windows rendszeren) vagy a `Command` kulcsot (MacOS rendszeren).
1. Egy másik megoldási rész hozzáadásához ismételje meg az 1 – 3. lépést.

## <a name="upload-documents"></a>Dokumentumok feltöltése

Meg kell adnia az ajánlat részleteit biztosító biztosítéki dokumentumokat. A Microsoft értékesítési csapatai ezt az információt használják annak kiértékeléséhez, hogy az ajánlat megfelel-e az ügyfél igényeinek az ajánlat ajánlása és értékesítése érdekében. Minél több információt ad meg, annál több információra van szüksége a Microsoft értékesítési csapatoknak a termék megismeréséhez és népszerűsítéséhez.

A támogatott fájltípusok:. pdf,. ppt,. pptx,. doc,. docx,. xls,. xlsx,. jpg,. png és. MP4. Egyes dokumentumok sablonjai az alábbi 2. táblázatban találhatók.

> [!NOTE]
> A **megoldás/ajánlat egy-pager** és **megoldás/ajánlat pitch Deck** szükséges a közös értékesítésre kész állapot eléréséhez. Emellett az Azure-beli IP-címek közös értékesítésének incentivized is előfeltételei. A hivatkozási architektúra diagramja az Azure IP-incentivized állapotához is szükséges. Az ebben a táblázatban szereplő többi dokumentum nem kötelező, de ajánlott.

***2. táblázat: a közös értékesítést támogató dokumentumok***

| **Dokumentumok**    | **Leírás**  |
| :------------------- | :-------------------|
| *Megoldás/ajánlat – egy pager (kötelező)* | A potenciális ügyfelek számára egy professzionális módon tervezett egy-pager, amely a megoldás értékének kiosztását mutatja be.<br><br>A megfelelő sablonok egyikével megadhatja az ajánlat ügyfél általi használatra kész leírását:<br><ul><li> [Microsoft Azure egy pager-sablon](https://aka.ms/Customer-One-Pager_MicrosoftAzure)</li><li>[Microsoft Dynamics 365 1 – pager sablon](https://aka.ms/Customer-One-Pager_MicrosoftDynamics365)</li> <li>[Microsoft 365 1 – pager sablon](https://aka.ms/Customer-One-Pager_MicrosoftOffice365) </li><li>[Windows 10 1 – pager sablon](https://aka.ms/Customer-One-Pager_Windows)</li></ul> <br> A Microsoft értékesítési csapatai megoszthatják ezeket az információkat az ügyfelekkel annak megállapítása érdekében, hogy az ajánlat megfelelő-e, és hogy az ügyfél készen áll-e. |
| *Megoldás/ajánlat pitch Deck (kötelező)* | A Pitch Deck létrehozásához használhatja az [ügyfél-bemutató sablont](https://aka.ms/GTMServices_CustomerPresentation) is. A paklinak a [hivatkozási architektúra diagramra](reference-architecture-diagram.md)kell hivatkoznia. Ennek a dia-feladatnak a célja az ajánlat és a hozzá tartozó érték kiosztása. Miután meggyőződött arról, hogy ajánlata az ügyfél számára elkészült, a Microsoft értékesítési csapatai megoszthatják ezt a bemutatót az ügyfelekkel, hogy fejezzék ki a vállalat és a Microsoft által a közös megoldás telepítésekor használt értéket. A bemutatónak ki kell terjednie az Ön ajánlatára, hogy miként segíthet az ügyfeleknek, hogy milyen iparágakra vonatkozik az ajánlat, és hogyan hasonlítható össze a versengő megoldásokkal. |
| *Ügyfél-esettanulmány* (nem kötelező)| Az [esettanulmány sablon](https://aka.ms/GTM_Case_Study_Template) segítségével hozza létre az ügyfél-esettanulmányt. Ez az információ egy potenciális ügyfelet mutat be, hogy az Ön és a Microsoft hogyan helyezte üzembe az ajánlatot az előzetes esetekben. |
| *Ellenőrizhető ügyfél-WINS* (nem kötelező) | Adjon meg konkrét példákat az ügyfelek sikereire az ajánlat üzembe helyezése után. |
| *Channel pitch Deck* (nem kötelező) | Egy olyan információkkal, amelyek segítségével a csatornák viszonteladói többet tudhatnak meg az ajánlatról, és elérhetik az értékesítési csapatait. Ez a pakli általában tartalmaz egy liftes hanganyagot, a megcélzott ügyfelekkel kapcsolatos információkat, az ügyfelek felkérésére, a beszélő pontokat, valamint a videókra, dokumentációra és támogatási információkra mutató hivatkozásokat. |
| *Hivatkozási architektúra diagramja* (az Azure IP közös értékesítésű incentivized állapotához szükséges) | Az ajánlatot és a Microsoft Cloud Services-kapcsolatát ábrázoló diagram. Azt is bemutatják, hogy az ajánlat hogyan teljesíti az Azure-beli IP-incentivized állapotának technikai követelményeit. [További információ a hivatkozási architektúra diagramról.](reference-architecture-diagram.md) |
| *Egyéb dokumentumok* (nem kötelező) | Akár öt további dokumentumot vagy videót is feltölthet, hogy segítse a Microsoft értékesítési csapatait és a csatornák viszonteladóit, hogy többet tudjon meg az ajánlatáról, a szervezetéről, valamint arról, hogy miben különbözik a többi ajánlattól. |
|||

- Miután létrehozta a dokumentumokat, húzza őket a megfelelő mezőbe a **dokumentumok** területen, vagy válassza a **fájl (ok) tallózása** a dokumentumok a számítógépről való feltöltéséhez lehetőséget.

    [![A közös értékesítés a Microsoft lapon című témakör közös értékesítésre vonatkozó dokumentációját mutatja be.](./media/co-sell/co-sell-documents-section.png)](./media/co-sell/co-sell-documents-section.png#lightbox)

## <a name="product-landing-page"></a>Termék kezdőlapja

- A **dokumentumok** elemnél, a **Product beszálló lapon** adja meg a termék webhelyére mutató hivatkozást, ahol a Microsoft értékesítési csapatok és a csatornák viszonteladói további információt kaphatnak az ajánlatról, és megtekinthetik a legújabb frissítéseket.

## <a name="enter-your-contacts"></a>Adja meg a névjegyeket

A közös értékesítésre kész állapot eléréséhez minden olyan földrajzi helyhez meg kell adni egy kapcsolattartót, ahol az ajánlat elérhető. Ha úgy dönt, hogy az ajánlatot elérhetővé teszi a CSP programban, a kapcsolattartási adatok a csatorna-viszonteladók számára is elérhetők.

A kapcsolattartási adatok lehetővé teszi, hogy a Microsoft értékesítési csoportjai és a csatornák viszonteladói további információkat kérjenek a szervezet megfelelő erőforrásáról. A kapcsolattartási adatok minden Microsoft értékesítési csapat számára elérhetők.

> [!NOTE]
> Fontos, hogy naprakészen tartsa a kapcsolattartási adatait.

1. Ha le szeretné tölteni a sablont a kapcsolattartási adatok megadásához, a **névjegyek** területen válassza a **névjegyek letöltése (. csv)** lehetőséget a képernyőképen látható módon. Ha korábban már feltöltött névjegyeket, exportálhatja a meglévő névjegyek listáját egy ajánlathoz, és módosíthatja is azokat. CSV-fájl.

    [![A közös értékesítés és a Microsoft lap névjegyek szakaszát mutatja be.](./media/co-sell/co-sell-contacts-section.png)](./media/co-sell/co-sell-contacts-section.png#lightbox)

1. Nyissa meg a t. CSV-fájl egy alkalmazásban, például a Microsoft Excelben, majd töltse ki az egyes sorokat a partner adataival.

    - Név (kötelező): a partner neve.
    - E-mail (kötelező): a partner e-mail-címe.
    - Beosztás (kötelező): beosztás.
    - Szerepkör (kötelező): használja a következő szerepkörök bármelyikét.

    ***3. táblázat: szerepkörök leírása***

    | **Szerepkör**    | **Leírás**  |
    | :------------------- | :-------------------|
    | Partner marketing | Ez a szerepkör az ajánlat marketingjét, valamint a Microsoft értékesítési csapatokkal és a csatorna-viszonteladókkal folytatott marketing-erőfeszítésekkel foglalkozik. A marketing-bevezetések és az ajánlat tartalmának, például a termékleírások, a képek és a videók listázásának fő pontja. |
    | Partneri értékesítés | Ez a szerepkör az ajánlat értékesítésére és a Microsoft értékesítési csapatával és a csatorna-viszonteladókkal való együttműködésre koncentrál. Adjon meg legalább egy partneri értékesítési kapcsolatot az egyes régiókban, amelyekben ajánlatát értékesíteni szeretné. Ugyanaz a partner értékesítési kapcsolat több régióra is kiterjed. |
    | Partneri műszaki értékesítés | A technikai architektúrát és üzembe helyezési szempontokat az értékesítési ciklus, az értékesítés utáni integráció és a telepítési időszakok során támogatja. |
    | Partneri ügyfelek sikerességének kezelője | A általában a telepítés után is támogatja az ügyfeleket, így a lehető legtöbbet hozhatja ki az ajánlatból, és növelheti annak használatát az ügyfél szervezetén belül. |
    |||

    - Országok/régiók (kötelező): a sablon kitöltésekor a két levél [közös értékesítési ország-és régiókódját](commercial-marketplace-co-sell-countries.md)használja. Ha a kapcsolattartó az összes országot és régiót tartalmazza, használja az "OOO" hárombetűs kódot. Ha egy névjegy egynél több országot vagy régiót érint, adja meg az egyes két betűs kódokat vesszővel elválasztva. Adja meg például a "US, CA, FR" értéket idézőjelek nélkül a sablonba.

        Az országoknak és a régióknak minden partner területét tükröznie kell. A Microsoft értékesítési csapatai és a csatorna-viszonteladók ezeket az információkat fogják használni, amikor adatokat kérnek, vagy az adott országon vagy régióban belüli értékesítésekkel foglalkoznak.

    - Államok/tartományok (nem kötelező): a sablon kitöltésekor használja az XX-XX formátumot az [állapotok, tartományok és területek táblákban](commercial-marketplace-co-sell-states.md)felsoroltak szerint.

1. Mentse és zárjuk be a t. CSV-fájl.

1. Az importálásához. CSV-fájl, válassza a **Névjegyek importálása (. csv)** hivatkozást.
    > [!NOTE]
    > A importálása. A CSV-fájl felülírja a meglévő névjegyeket.

1. Válassza a elemet. CSV-fájl, majd válassza a **Megnyitás** lehetőséget. Megjelenik egy üzenet, amely tájékoztatja, hogy a Névjegyek importálása sikeres volt.

## <a name="save-and-republish-the-offer"></a>Az ajánlat mentése és újbóli közzététele

1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget a módosítások mentéséhez.
1. Miután elvégezte az ajánlat összes szükséges részét, elküldheti azt véleményezésre és közzétételre. Válassza **a felülvizsgálat és közzététel** lehetőséget.
1. Tegye a következők egyikét:

    - Ha befejezte vagy frissítette a **közös értékesítés a Microsofttal** lapot egy korábban közzétett ajánlathoz, és nem frissített más lapokat, javasoljuk, hogy csak a **közös értékesítés** jelölőnégyzetet jelölje be.

    - Ha ez egy új vagy vázlatos ajánlat, amely még soha nem lett közzétéve, javasoljuk, hogy jelölje be az összes jelölőnégyzetet. Az **összehasonlítás** gombra kattintva összehasonlíthatja a jelenlegi verziót a közzé nem tett változásokkal.

1. Az érvényesítési fázis megkezdéséhez válassza a **Közzététel** lehetőséget. Vegye figyelembe, hogy az ajánlat nem jelenik meg élőben, amíg az ajánlatot nem vizsgálták, majd az érvényesítési fázis befejezése után válassza az **élő indítás** lehetőséget. Ha az ajánlat már közzé lett téve, és a közös értékesítést konfigurálta, akkor az ajánlat élő marad, miközben érvényesítjük a közös értékesítés állapotát. Az ajánlatok áttekintésével és közzétételével kapcsolatos részletekért lásd: az [ajánlat áttekintése és közzététele a kereskedelmi piactéren](review-publish-offer.md).

> [!NOTE]
> Az ajánlat együttes értékesítésre történő jelöléséhez többé nem kell felvennie velünk a kapcsolatot. Miután kitöltötte az összes kötelező mezőt az Együttes értékesítés a Microsofttal oldalon, és újból közzétette az ajánlatot, áttekintjük az ajánlatot annak megállapításához, hogy megfelel-e az együttes értékesítési állapotra vonatkozó követelményeknek.

## <a name="next-steps"></a>Következő lépések

- Az ajánlatok ismételt közzétételével kapcsolatos részletekért lásd: az [ajánlat áttekintése és közzététele a kereskedelmi piactéren](review-publish-offer.md).
- A kereskedelmi Marketplace-díjakkal és a technikai előnyökkel kapcsolatos információkért tekintse [meg a kereskedelmi Marketplace előnyeit](gtm-your-marketplace-benefits.md).
