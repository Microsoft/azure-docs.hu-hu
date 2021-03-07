---
title: Azure nagyvállalati regisztrációs számlák
description: Ez a cikk az Azure Nagyvállalati számlák kezelését ismerteti.
author: bandersmsft
ms.author: banders
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: ruturajd
ms.custom: contperf-fy21q1
ms.openlocfilehash: 71ba1d6e1e45c5c2e72e2be620158b1aa4b71582
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430809"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure nagyvállalati regisztrációs számlák

Ez a cikk az Azure Nagyvállalati Szerződés (Azure EA) számláinak kezelését ismerteti. A számla a számlájának felel meg. Ellenőrizze a pontosságát. Azokkal a feladatokkal is érdemes megismerkednie, amelyekre szükség lehet a számla kezeléséhez.

## <a name="view-usage-summary-and-download-reports"></a>Használati adatok összegzésének megtekintése és jelentések letöltése

A vállalati rendszergazdák az Azure Enterprise portálon megtekinthetik a használati adataik összegzését, a felhasznált Azure-előrefizetési keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

Az egyes fiókok részletes használati adatainak megtekintéséhez töltse le a használati adatokról szóló jelentést:

1. Jelentkezzen be az Azure Enterprise portálra.
1. Válassza a **Jelentések** elemet.
1. Válassza a **Használati adatok letöltése** fület.
1. A jelentések listáján válassza a **Letöltés** elemet annál a havi jelentésnél, amelyet le szeretne tölteni.

   > [!NOTE]
   > A használati adatokról szóló jelentés nem tartalmazza a vonatkozó adókat.
   >
   > A használat felmerülésének időpontja és a jelentésben való megjelenése között akár nyolc órás késés is lehet.

A használati adatokat összegző jelentések és ábrák megtekintése:

1. Jelentkezzen be az Azure Enterprise portálra.
1. Válasszon ki egy előrefizetési időszakot.
   Váltson az **M** (Havi) és a **C** (Egyéni) módok között az oldal jobb felső részén, hogy módosítsa a **Használati adatok összegzésének** dátumtartományát, majd adjon meg egyéni kezdő és záró dátumokat.  
   ![Használati adatok összegzésének létrehozása és megtekintése, illetve jelentések letöltése egyéni nézetben](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Jelöljön ki egy időtartamot vagy egy hónapot a grafikonon a további részletek megtekintéséhez.
   - A grafikon a havi használatot mutatja a használati adatok, a szolgáltatások túlhasználata, a külön számlázott díjak és az Azure Marketplace-költségek megjelenítésével.
   - A kiválasztott hónapra vonatkozóan a grafikon alatti mezők segítségével részlegek, fiókok és előfizetések alapján szűrheti az adatokat.
   - Válthat a díjak **szolgáltatások** és **hierarchia** szerinti megjelenítése között.
   - A részletek megtekintéséhez kibonthatja vagy összecsukhatja az **Azure-szolgáltatások**, a **Külön számlázott díjak** és az **Azure Marketplace** területet.

Tekintse meg az alábbi videót a használati adatok megtekintési módjának megismeréséhez:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV-jelentések letöltése

A vállalati rendszergazdák a Havi jelentés letöltése lapot használhatják az alábbi jelentések CSV-fájlként való letöltéséhez:

- Egyenleg és díjak
- Használati adatok
- Az Azure Marketplace díjai
- Árlista

A jelentések letöltéséhez:

1. Az Azure Enterprise portálon válassza a **Jelentések** elemet.
2. Válassza a lap tetején található **Használati adatok letöltése** elemet.
3. Válassza a **Letöltés** elemet a havi jelentés mellett.

   > [!NOTE]
   > A használat felmerülésének időpontja és a jelentésben való megjelenése között akár 72 óra késés is lehet.
   >
   > A CSV-fájlokat a Safari böngészőből az Excelbe letöltő felhasználók formázási hibákat tapasztalhatnak. A hibák elkerülése érdekében szövegszerkesztővel nyissa meg a fájlt.

![A Használati adatok letöltése oldalt bemutató példa](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

Tekintse meg az alábbi videót a használati adatok letöltési módjának megismeréséhez:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Speciális jelentés letöltése

Az adott dátumtartományokkal vagy fiókokkal kapcsolatos jelentések elkészítéséhez a speciális jelentések letöltése használható. A kimeneti fájl formátuma a nagyobb rekordhalmazok befogadása érdekében a .csv.

1. Az Azure Enterprise portálon válassza a **Speciális jelentés letöltése** lehetőséget.
1. Válassza ki a megfelelő dátumtartományt és a megfelelő fiókokat.
1. Válassza a **Használati adatok lekérése** lehetőséget.
1. Használja a **Frissítés** gombot, amíg a jelentés állapota **Letöltés** értékűre nem frissül.
1. Töltse le a jelentést.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Használati jelentések és a számlázási információk letöltése egy korábbi regisztrációhoz

A regisztrációt követően letölthet a korábbi regisztrációk egyikére vonatkozó használati jelentéseket és számlázási információkat. Az előzményeket feldolgozó jelentések az Azure Enterprise portálon és a Cost Management felületén egyaránt elérhetők.

Az Azure Enterprise portál kiszűri az inaktív regisztrációkat. Az inaktív átvitt regisztrációk megtekintéséhez törölnie kell az **Aktív** jelölőnégyzet jelölését.  

![Az aktív jelölőnégyzet jelölésének törlésével a felhasználó láthatja az inaktív regisztrációkat is](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Jövőbeli túlhasználati számla rendelésszámának módosítása

Az Azure Enterprise Portal automatikusan létrehoz egy alapértelmezett rendelési (PO-) számot, ha a vállalati rendszergazda nem állít be egyet a számlázási dátum előtt. A vállalati rendszergazda az automatikus számlaértesítő e-mail beérkezése utáni hét napban frissítheti a rendelési számot. 

Ha meg szeretné akadályozni, hogy minden hónapban automatikusan generálja a PO-számokat, zárolhatja a PO-számot. Lásd: [postafiók zárolásának száma](#lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles).

### <a name="update-the-azure-services-purchase-order-number"></a>Az Azure Services beszerzési rendelési számának frissítése

1. Az Azure Enterprise Portalon válassza a **Jelentés** > **Használati adatok összegzése** lapot.
1. Válassza a jobb felső sarokban található, **rendelési számok szerkesztésére** szolgáló lehetőséget.
1. Jelölje be az **Azure-szolgáltatások** választógombot.
1. Válasszon ki egy **számlázási időszakot** a dátumtartományok legördülő menüjéből.
   A rendelésszámot hét napig szerkesztheti a számláról szóló értesítést követően, addig, amíg nem egyenlíti ki a számlát.
1. Adja meg az új rendelési számot a **Rendelésszám** mezőben.
1. Válassza a **Mentés** parancsot a módosítások küldéséhez.

### <a name="update-the-azure-marketplace-purchase-order-number"></a>Az Azure Marketplace-beli beszerzési rendelés számának frissítése

1. Az Azure Enterprise Portalon válassza a **Jelentés** > **Használati adatok összegzése** lapot.
1. Válassza a jobb felső sarokban található, **rendelési számok szerkesztésére** szolgáló lehetőséget.
1. Jelölje be a **Marketplace** választógombot.
1. Válasszon ki egy **számlázási időszakot** a dátumtartományok legördülő menüjéből.  
    A rendelésszámot hét napig szerkesztheti a számláról szóló értesítést követően, addig, amíg nem egyenlíti ki a számlát.
1. Adja meg az új rendelési számot a **Rendelésszám** mezőben.
1. Válassza a **Mentés** parancsot a módosítások küldéséhez.

### <a name="lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles"></a>A következő számlázási ciklusok automatikus frissítésének megakadályozása a PO-szám zárolásával

A postafiók számának zárolása után az összes új számla zárolva marad, és nem kell frissítenie a PO-számot.

1.  Az Azure Enterprise Portalon válassza a **jelentés**  >  **használat összegzése** elemet.
2.  Válassza a jobb felső sarokban található, **rendelési számok szerkesztésére** szolgáló lehetőséget.
3.  Írjon be egy új PO-számot a **Po number (postafiók száma** ) mezőbe.
4.  Válassza a **zárolási postafiók száma** mezőt.
5.  Válassza a **Mentés** parancsot a módosítások küldéséhez.  
    :::image type="content" source="./media/ea-portal-enrollment-invoices/lock-po.png" alt-text="A PO-számok megtekintése/szerkesztése panelt ábrázoló képernyőfelvétel" lightbox="./media/ea-portal-enrollment-invoices/lock-po.png" :::


## <a name="azure-enterprise-billing-frequency"></a>Az Azure Enterprise számlázási gyakorisága

A Microsoft a regisztráció hatályba lépésének dátumától kezdve évente számlázza a Microsoft Azure-szolgáltatások előrefizetési keretének terhére történő vásárlásait. Az előrefizetés összegét meghaladó használatot a Microsoft utólagosan számlázza.

- Az előrefizetés díja havidíj alapján van kiszabva, és előre, évente számlázzuk ki.
- A keretet meghaladó szolgáltatások díját havonta számítjuk ki, és a számlázási időszak végén számlázzuk.

### <a name="billing-intervals"></a>Számlázási időközök

A számlázási intervallum attól függ, hogyan kívánja teljesíteni az előrefizetéseit. Az Ön éves előrefizetése egybeesik az alábbiak valamelyikével:

- A regisztráció évfordulójának dátuma
- Az egyéves módosítási előfizetés hatályba lépésének időpontja.

A túlhasználatról szóló számla kézhezvételének dátuma a regisztráció kezdési dátumától és a beállítástól függ:

- **Közvetlen regisztrációk 2018. május 1. előtti kezdési dátummal**:
  - Ha Ön közvetlen nagyvállalati Azure-ügyfél, akkor éves számlázási ciklusa van az Azure-szolgáltatásokhoz, kivéve az Azure Marketplace-szolgáltatásokat. Számlázási ciklusa az évforduló dátumán alapul: azon az időponton, amikor a szerződése hatályba lépett.
  - Ha túllépte az Azure EA-előrefizetés küszöbének 150%-át, akkor a rendszer automatikusan negyedéves számlázási ciklusra vált, amely az évforduló dátumán alapul. Emellett kapni fog egy, az Azure-szolgáltatások túlhasználatáról szóló számlát is.
  - Ha nem haladja meg az Azure-előrefizetés küszöbének 150%-át, a regisztráció az egyéves számlázási ciklusban marad. A túlhasználatról szóló számlát az előrefizetési év végén kapja meg.

- **Közvetlen regisztrációk 2018. május 1. utáni kezdési dátummal**:
  - Az Ön Azure-használatát és -díjait külön, havi számlázási ciklusban kiállított számlákon számlázzuk.
  - Az Azure-előrefizetés által nem fedezett díjakat túlhasználati díjként számoljuk fel.  

- **Közvetett regisztrációk 2018. május 1. előtti kezdési dátummal rendelkező regisztrációval**:

  Ha Ön közvetett vállalati megállapodással (EA) rendelkező ügyfél, és a szerződés kezdési dátuma 2018. május 1. elé esik, akkor negyedéves számlázási ciklussal rendelkezik majd. A csatornapartner (CP) közvetlenül Önnek állítja ki a számlát.  

- **Közvetett regisztrációk 2018. május 1. utáni kezdési dátummal**:

  Költségei havi elszámolási időszakra lesznek vetítve.  

### <a name="increase-your-azure-prepayment"></a>Növelje Azure-előrefizetési keretét

Előrefizetési kerete bármikor megnövelhető. Ilyenkor az adott év előrefizetési időszakából fennmaradó hónapok száma után állítjuk ki a számlát. Ha például egyéves módosítási előfizetést regisztrál, és a hatodik hónap során növeli az előrefizetési keretet, akkor az adott időszak hátralévő hat hónapjára szólóan kap számlát. Az előrefizetésben foglalt mennyiségeket a rendszer ezután frissíti az előrefizetési időszak utolsó hat hónapjára vonatkozóan. A rendszer ezeket az új mennyiségeket használja fel a túlhasználati díjak meghatározásakor.

### <a name="overage"></a>Overage (Kereten túli díjak)

Túlhasználat esetén fizetnie kell azért a használatért vagy azokért a foglalásokért, amelyek a számlázási időszak során meghaladták az előrefizetési keretet. Ha meg szeretné tekinteni az egyes tételekhez kapcsolódó túlhasználati mennyiségek számításának részletezését, tekintse meg a használati adatokat összegző jelentést, vagy forduljon csatornapartneréhez.

A számla minden tételénél látni fogja:

- **Bővített összeg**: a teljes díj
- **Előrefizetési összeg**: a díjak fedezésére felhasznált előrefizetési keret
- **Nettó összeg**: az előrefizetési keretet meghaladó díjak összege

A vonatkozó adókat csak az előrefizetési keretet meghaladó nettó összegre számítjuk ki.

A túlhasználat számlázása automatikusan történik. Az értesítések és a számlák érkezésének ideje az Ön számlázási időszakának záródátumától függ.

- A túlhasználatról szóló értesítést általában hét nappal az ügyfél számlázási időszakának záródátuma után küldi el a rendszer.
- A túlhasználatról szóló számlákat a rendszer 7–9 nappal az értesítés után küldi el.
- A túlhasználatról szóló értesítés és a számlázás közötti hét nap során áttekintheti a díjakat, és frissítheti a rendszer által létrehozott rendelésszámokat.

### <a name="azure-marketplace"></a>Azure Piactér

A 2019. áprilisi számlázási ciklustól kezdve az ügyfelek egyetlen Azure-számlát fognak kapni, mert az Azure és az Azure Marketplace díjainak mindegyikét egyetlen számlán egyesítettük, két külön számla kiküldése helyett. Ez a változás nem érinti az ausztrál, japán vagy szingapúri ügyfeleket.

Az összevont számlára való áttérés során egy részleges Azure Marketplace-számlát fog kapni. Ez az utolsó külön számla a számlázás frissítésének dátuma előtti Azure Marketplace-díjakat tartalmazza. A frissítés dátumát követően keletkező Azure Marketplace-díjak az Azure-számlán lesznek láthatók. Az áttérési időszak után az Azure- és Azure Marketplace-díjak mindegyikét az összevont számlán fogja látni.  

### <a name="adjust-billing-frequency"></a>Számlázási gyakoriság módosítása

Az ügyfél számlázási gyakorisága lehet éves, negyedéves vagy havi. A számlázási ciklus akkor lesz meghatározva, amikor az ügyfél aláírja a szerződést. A havi számlázás a legkisebb választható számlázási időszak.

- A közvetlen regisztrációk számlázási ciklusának évesről negyedévesre történő módosításához a vállalati rendszergazda **jóváhagyása** szükséges. A közvetett regisztrációk esetén a partnerrendszergazda jóváhagyására van szükség. A módosítás az aktuális számlázási negyedév végén lép életbe.
- A számlázási ciklus évesről vagy negyedévesről havira való megváltoztatásához **módosítani** kell a szerződést.  A meglévő regisztrációs számlázási ciklus módosításához a vállalati rendszergazda, vagy a „Számlázási kapcsolattartó” jóváhagyása szükséges.
- **Küldje be** jóváhagyását az [Azure Enterprise Portal ügyfélszolgálatához](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Válassza ki a problémakategóriát: **Számlázás és számlakiállítás**.

A módosítás az aktuális számlázási negyedév végén lép életbe.

Ha aláírt egy M503-as módosítást, bármely szerződés gyakoriságát módosíthatja havi számlázásra. 

### <a name="request-an-invoice-copy"></a>Számlamásolat kérése

A számlamásolatok kéréséhez forduljon a partneréhez.

## <a name="credits-and-adjustments"></a>Jóváírások és helyesbítések

A regisztrációra alkalmazott jóváírások vagy helyesbítések az [Azure Enterprise Portal](https://ea.azure.com) **Jelentések** területén láthatók.

Jóváírások megtekintése:

1. Az [Azure Enterprise Portalon](https://ea.azure.com) válassza a **Jelentések** szakaszt.
1. Válassza a **Használati adatok összegzése** lehetőséget.
1. A jobb felső sarokban módosítsa az **M** nézetet **C** nézetre.
1. Bontsa ki a helyesbítési mezőt az Azure-szolgáltatás előrefizetési táblájában.
1. Láthatja a regisztrációra alkalmazott jóváírásokat és azok rövid magyarázatát. Például: Szolgáltatói szerződésben szereplő jóváírás.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>A túlhasználat kiegyenlítése az Azure-előrefizetési keretből

Ahhoz, hogy az Azure-előrefizetési keretből fizesse ki a túlhasználatot, meg kell felelnie a következő feltételeknek:

- A kiszámlázott szolgáltatás záró dátumától számított egy éven belüli, felmerült, de ki nem fizetett többletköltségekkel kell rendelkeznie.
- A rendelkezésre álló Azure-előrefizetési keretnek az összes felmerült költségét fedeznie kell, a régebbi ki nem fizetett Azure-számláit is beleértve.
- A teljesítendő számlázási időszaknak teljesen lezártnak kell lennie. A számlázás teljes lezárása minden hónap ötödik napja után történik.
- A kiegyenlíteni kívánt számlázási időszaknak teljesen lezártnak kell lennie.
- Azure előrefizetési kedvezménye (Azure Prepayment Discount, APD) a tényleges új előrefizetésen alapul a korábbi felhasználásra szánt összegek levonásával. Ez a követelmény csak a felmerült többletköltségekre vonatkozik. Csak az Azure-előrefizetési keretet használó szolgáltatásoknál működik, ezért az Azure Marketplace díjai esetén nem érvényes. Az Azure Marketplace díjait külön számlázzuk.

Ha ki szeretné egyenlíteni a többletköltségeket, Ön vagy az ügyfélkapcsolati csapat megnyithat egy támogatási kérést. Szükség van egy e-mailes jóváhagyásra az EA-rendszergazdájától vagy a számlázási kapcsolattartótól.

## <a name="move-charges-to-another-enrollment"></a>Díjak áthelyezése másik regisztrációba

A rendszer csak akkor helyezi át a használati adatokat, ha az átvitel visszakeltezett. Két lehetőség van a használati adatok egyik regisztrációból a másikba való átvitelére:

- Regisztrációk közötti fiókátvitel
- Regisztrációk közötti regisztrációátvitelek

Mindkét megoldásnál küldenie kell egy [támogatási kérést](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) az EA támogatási csapatnak. 

## <a name="enterprise-agreement-usage-calculations"></a>Nagyvállalati szerződések használati díjának számítása

Az egyes szolgáltatások alapvető nyilvános díjszabási információit, mértékegységeit, gyakori kérdéseit és használati jelentéskészítési útmutatóit az [Azure-szolgáltatások](https://azure.microsoft.com/services/) és [Az Azure díjszabása](https://azure.microsoft.com/pricing/) című szakaszokban találja. A következő szakaszokban talál további információt az EA-számításokról.

### <a name="enterprise-agreement-units-of-measure"></a>Nagyvállalati szerződések mértékegységei

A nagyvállalati szerződések mértékegységei gyakran eltérnek a többi programban, például a Microsoft Online Services szerződés programban (MOSA) látottaktól. Ez az eltérés azt jelenti, hogy néhány szolgáltatás esetében a rendszer összesíti a mértékegységeket egy normalizált díjszabás biztosítása érdekében. Az Azure Enterprise Portal Használati adatok összegzése nézetében látható mértékegység mindig a nagyvállalati mértékegység. Az egyes szolgáltatásokhoz tartozó aktuális mértékegységek és átváltások teljes listájáért küldjön [támogatási kérést](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Konverzió a használati adatokról szóló jelentés és a használati adatokat összegző oldal között

A letöltött használatiadat-jelentésen látható, hogy a nyers erőforrás-használat adatai legfeljebb hat tizedesjegy pontossággal jelennek meg. Az Azure Enterprise Portal azonban négy tizedesjegyre kerekíti a használatot az előrefizetési egységek esetében, a túlhasználati egységek esetében pedig nulla tizedesjegyre rövidít. Az Azure Enterprise Portalon használt egységekre való átalakítás előtt a rendszer először négy számjegyre kerekíti a nyers használatot. Ezt követően az eredményül kapott nagyvállalati egységeket ismét négy számjegyre kerekíti. Az átalakítás előtt ténylegesen felhasznált órák csak a használati adatokat tartalmazó letöltött jelentésben jelennek meg, magán az Azure Enterprise Portalon nem.

Például: 694,533404 tényleges SQL Server-óra szerepel a használati adatokat tartalmazó jelentésben. Ezeket az egységeket a rendszer 6,94533404 egységére alakítja át 100 üzemórára vonatkozóan, amelyet aztán 6,9453-ra kerekítve jeleníti meg az Azure Enterprise Portalon.

- Ezeket az egységeket a rendszer ezután megszorozza az előrefizetési egység árával, és az eredményt két tizedesre csonkolja. A japán yen (JPY) és a dél-koreai won (KRW) esetében a kiterjesztett mennyiség nulla tizedesjegyre van kerekítve.
- A túlhasználati díjak esetén a számlázható egységeket hat számjegyre csonkolja a rendszer, majd a kiterjesztett számlázási mennyiség meghatározása érdekében megszorozza a túlhasználati díjak egységárával.
- Felügyelt szolgáltatás szolgáltatójának (MSP) számlázása esetén az MSP-ként megjelölt részleghez tartozó összes használat nulla tizedesjegyre van csonkolva az EA mértékegységre való átalakítás után. Ennek eredményeképpen a használat összege alacsonyabb lehet, mint az Azure Enterprise Portalon jelentett összes használat összege. Ez attól függ, hogy az MSP még az Azure-előrefizetés egyenlegén belül vagy már a kereten túl van.

### <a name="graduated-pricing"></a>Sávos díjszabás

A nagyvállalati szerződés díjszabása jelenleg nem tartalmaz sávos díjszabást, ahol az egységenkénti díjszabás a használat növekedésével csökken. Amikor sávos díjszabással rendelkező MOSA-programból vált nagyvállalati szerződésre, a díjszabás megállapítása a szolgáltatás alapszintje szerint történik, a nagyvállalati szerződés esetleges kedvezményeire vonatkozó kiigazítás levonásával.

### <a name="partial-hour-billing"></a>Nem teljes órák számlázása

A teljes órában számolt növekmények helyett az összes használat részleges órákká alakított percek szerint van számlázva. A felsorolt óránkénti nagyvállalati díjakat az összes órára és a részleges órákra is alkalmazni kell.

### <a name="average-daily-consumption"></a>Átlagos napi felhasználás

Vannak olyan, havi díjjal rendelkező szolgáltatások, ahol a használatról napi rendszerességgel készül jelentés. Ebben az esetben a rendszer ezt a használatot naponta egyszer kiértékeli, elosztja 31-gyel, és a számlázási hónap napjainak száma alapján összesíti. Így a díjszabás egy hónapban sem haladja meg a várt értéket, és a 31 napnál rövidebb hónapokban kissé alacsonyabb is.

### <a name="compute-hours-conversion"></a>Üzemórák átalakítása

2016. január 28. előtt minden A0, A2, A3 és A4 Standard és alapszintű virtuális gép és felhőszolgáltatás használatát az A1 virtuális gépek percenkénti egységeiként bocsátottuk rendelkezésre. Az A0 virtuális gépek használatát az A1 virtuális gépek percenkénti egységeinek törtrészének számítottuk, míg az A2-k, az A3-ak és az A4-ek többszörösekké alakultak. Mivel ez a szabályzat összezavarta a vásárlókat, a külön percalapú A0, A2, A3 és A4 mérőszámok használatára kezdünk áttérni.

Az új mérés a 2016. január 27. és január 28. között lépett érvénybe. A letöltött CSV-ben, amely az átmeneti időszak felhasználást mutatja, egyaránt láthatja a:

- Az A1 mérőszám alapján kibocsátott használatot
- Az üzemelő példány méretének megfelelő új, dedikált mérőszám alapján kibocsátott használatot.

| **Üzemelő példány mérete** | **Az A1 többszöröseként kibocsátott használat 2016. január 26-ig** | **Dedikált mérőszám alapján kibocsátott használat 2016. január 27-től** |
| --- | --- | --- |
| A0 | Az A1 óraszám 0,25-szöröse | Az A0 óraszám 1-szerese |
| A2 | Az A1 óraszám 2-szerese | Az A2 óraszám 1-szerese |
| A3 | Az A1 óraszám 4-szerese | Az A3 óraszám 1-szerese |
| A4 | Az A1 óraszám 8-szorosa | Az A4 óraszám 1-szerese |

### <a name="regions"></a>Régiók

Azon szolgáltatások esetében, ahol a zóna és a régió befolyásolja a díjszabást, az alábbi táblázatban látható a földrajzi térségek és régiók leképezése:

| Térség | Adatátviteli régiók | CDN-régiók |
| --- | --- | --- |
| 1\. zóna | Észak-Európa <br> Nyugat-Európa <br> USA nyugati régiója <br> USA keleti régiója <br> USA északi középső régiója <br> USA déli középső régiója <br> USA 2. keleti régiója <br> USA középső régiója | Észak-Amerika <br> Európa |
| 2\. zóna | Ázsia és a csendes-óceáni térség keleti régiója <br> Ázsia és a csendes-óceáni térség délkeleti régiója <br> Kelet-Japán <br> Nyugat-Japán <br> Kelet-Ausztrália <br> Délkelet-Ausztrália | Ázsia és a Csendes-óceáni térség <br> Japán <br> Latin-Amerika <br> Közel-Kelet / Afrika <br> Kelet-Ausztrália <br> Délkelet-Ausztrália |
| 3\. zóna | Dél-Brazília |   |

A szolgáltatások közötti, ugyanazon az adatközponton belüli adatforgalomért nem számítunk fel díjat. Például a Microsoft 365 és az Azure esetében.

### <a name="azure-prepayment-and-unbilled-usage"></a>Az Azure-előrefizetés és a ki nem számlázott használat

Az Azure-előrefizetés az az összeg, amelyet előre kifizetnek az Azure-szolgáltatásokért. A szolgáltatások használata során az Azure-előrefizetési keret felhasználódik. A belső Azure-szolgáltatások díja az Azure-előrefizetési keretből vonódik le. Néhány díjat azonban külön számlázunk ki, és az Azure Marketplace-szolgáltatások használati díja nem vonható le az Azure-előrefizetési keretből.

### <a name="charges-billed-separately"></a>Külön számlázott tételek

Vannak olyan külső féltől származó termékek és szolgáltatások, amelyeknek a használati díja nem vonható le az Azure-előrefizetési keretből. Ehelyett az ilyen tételek esetében a számlázás külön történik a standard számlázási ciklus túlhasználati díjának részeként.

Az összes Azure- és Azure Marketplace-díjat egyetlen, a regisztráció számlázási ciklusával összhangban álló számlán egyesítettük. Az összevont számla nem vonatkozik az ausztrál, japán vagy szingapúri ügyfelekre.

Az összevont számla feltünteti az összes Azure-használatot és az esetleges Azure Marketplace-beli díjakat. Az ausztrál, japán vagy szingapúri ügyfelek továbbra is külön számlán tekinthetik meg az Azure Marketplace-díjakat.

Ha a standard számlázási ciklus végén nincs túlhasználat, a külön számlázott díjak külön számlán jelennek meg. Ez a folyamat az ausztrál, japán és szingapúri ügyfelekre vonatkozik.

A következő szolgáltatásokat külön számlázzuk:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (havi)
- Visual Studio Enterprise (éves)
- Visual Studio Professional (havi)
- Visual Studio Professional (éves)

## <a name="what-to-expect-after-change-of-channel-partner"></a>Mire számíthat, miután értékesítési partnert váltott?

Ha az értékesítési partner váltása (COCP) a hónap közepén történik, az ügyfél kap egy számlát a korábbi partnerhez kapcsolódó használatról, valamint egy másikat az új partnerhez kapcsolódó használatról.

A számlák kiküldése a számlázási időszak vége utáni hónapban történik. Ha a számlázás havonta történik, a szeptemberi használathoz kapcsolódó számlát októberben küldjük el mindkét partnerre vonatkozóan. Ha a számlázási ciklus negyedéves vagy éves, az ügyfél kap egy számlát a korábbi partnerhez kapcsolódó használati időszakról, valamint egyet az új partnerhez kapcsolódó fennmaradó időszakról a számlázási gyakoriságtól függően.

## <a name="next-steps"></a>További lépések

- A számla és a díjak értelmezésével kapcsolatos további információkért lásd: [Az Azure Nagyvállalati Szerződés számlájának ismertetése](../understand/review-enterprise-agreement-bill.md).
- Az Azure Enterprise Portal használatának kezdő lépéseit az [Ismerkedés az Azure EA Portallal](ea-portal-get-started.md) című rész írja le.
