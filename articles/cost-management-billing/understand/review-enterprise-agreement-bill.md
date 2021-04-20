---
title: Az Azure Nagyvállalati Szerződés számlájának áttekintése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők az Azure Nagyvállalati Szerződésekhez tartozó használati adatok és a hozzájuk tartozó számlák.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5d945c421dc0a4abed17b3a355339c131dd922df
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728049"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Az Azure Nagyvállalati Szerződés számlájának ismertetése

A Nagyvállalati Szerződéssel rendelkező Azure-ügyfelek akkor kapnak számlát, amikor túllépik a vállalat kreditkeretét, vagy olyan szolgáltatást használnak, amelyet nem fedez a kredit.

A szervezet kreditjébe beleszámít az Azure-előrefizetés (korábbi nevén pénzügyi keret) is. Az Azure-előrefizetés az az összeg, amelyet a szervezet előre befizet az Azure-szolgáltatások használatára. Amennyiben Azure-előrefizetési befizetéseket kíván hozzáadni a Nagyvállalati Szerződéséhez, lépjen kapcsolatba a Microsoft ügyfélkapcsolati munkatársával vagy viszonteladójával.

Ez az oktatóanyag csak azokra az Azure-ügyfelekre vonatkozik, akik Azure Nagyvállalati Szerződéssel rendelkeznek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A számlázott díjtételek áttekintése
> * Szolgáltatási kerettúllépési díjtételek áttekintése
> * A Marketplace-számla áttekintése

## <a name="prerequisites"></a>Előfeltételek

A számlán szereplő díjak áttekintésére és ellenőrzésére a vállalati rendszergazdáknak van lehetőségük. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](../manage/understand-ea-roles.md). Ha nem tudja, hogy ki a szervezet vállalati rendszergazdája, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Számlázott díjtételek áttekintése a legtöbb ügyfél esetén

Az alábbi szakasz nem vonatkozik az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre.

Akkor kap Azure-számlát, ha sor kerül a következő események egyikére a számlázási ciklus folyamán:

- **Szolgáltatási keret túllépése**: A szervezet használati díjai meghaladják a kreditegyenlegét.
- **Külön számlázott tételek**: Olyan szolgáltatások, amelyeket nem fed le a szervezeti kredit. A következő szolgáltatások számlázása mindig külön történik, a szervezet kreditegyenlegétől függetlenül:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (havi)
    - Visual Studio Enterprise (éves)
    - Visual Studio Professional (havi)
    - Visual Studio Professional (éves)
- **A Marketplace díjai**: Az Azure Marketplace-beli vásárlásokat és használatot nem fedezi a szervezeti kredit. Ezért a Marketplace díjairól mindig számlát kap, a kreditegyenlegétől függetlenül. Az Enterprise Portalon egy vállalati rendszergazda engedélyezheti vagy tilthatja le a Marketplace-beli vásárlásokat.

A számla először az összes Azure-beli felhasználási díjat és a hozzájuk kapcsolódó díjakat tünteti fel, és ezt követik a Marketplace-díjak. Ha rendelkezik kreditegyenleggel, a rendszer alkalmazza azt az Azure-használatra, és a számla a díj nélküli Azure-használatot és Marketplace-használatot tünteti fel utoljára.

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget az Azure-számlájával. A **Használat összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Azure EA Portalra](https://ea.azure.com). Ezután válassza a **Jelentések** lehetőséget. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Az **Összes használat** és az **Azure Marketplace** összegének meg kellene egyeznie a számlán szereplő **teljes bővített összeggel**. További részleteket a díjakról a **használati adatok letöltését** ismertető cikkben talál.  

![Képernyőkép a használati adatok letöltésére szolgáló lapról](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Számlázott díjtételek áttekintése egyéb ügyfelek esetén

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre vonatkozik.

Akkor kap egy vagy több Azure-számlát, ha sor kerül a következő események egyikére:

- **Szolgáltatási keret túllépése**: A szervezet használati díjai meghaladják a kreditegyenlegét.
- **Külön számlázott tételek**: Olyan szolgáltatások, amelyeket nem fed le a szervezeti kredit. A következő szolgáltatásokat számlázzuk ki Önnek:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (havi)
    - Visual Studio Enterprise (éves)
    - Visual Studio Professional (havi)
    - Visual Studio Professional (éves)
- **A Marketplace díjai**: Az Azure Marketplace-beli vásárlásokat és használatot nem fedezi a vállalati kredit; ezekről külön számla készül. Az Enterprise Portalon egy vállalati rendszergazda engedélyezheti vagy tilthatja le a Marketplace-beli vásárlásokat.

A szolgáltatási keret túllépéséből adódó díjakról és a számlázási időszak során külön számlázott díjakról egyetlen számlát kap. Ez tartalmazza a fenti két típusba tartozó díjakat. A Marketplace-beli díjakról mindig külön számlát kap.

## <a name="review-service-overage-charges-for-other-customers"></a>Szolgáltatási kerettúllépési díjtételek áttekintése egyéb ügyfelek esetén

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget a szolgáltatási kerettúllépés számlájával. A szolgáltatási kerettúllépés számlája olyan használatra vonatkozik, amely túllépi a szervezeti keretet, és/vagy a kredit által nem fedezett szolgáltatásokra. A **Használati összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Azure EA Portalra](https://ea.azure.com), majd válassza a **Jelentések** lehetőséget. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Az **Összes használat** összegének egyezne kell a szolgáltatási kerettúllépés számláján szereplő **teljes bővített összeggel**. További információt a díjakról a **Használati adatok letöltése** > **Speciális jelentés letöltése** alatt talál. A jelentés nem tartalmazza az adókat és a foglalási vagy piactéri díjakat.  

![Képernyőkép a Használati adatok letöltése lapon található Speciális jelentés letöltése lehetőségről.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

A következő lista számba veszi a számlákon és az Enterprise Portal **Használat összefoglalása** lapon előforduló kifejezéseket és leírásokat:

|Számlázási kifejezés|A Használat összefoglalása lap kifejezései|Leírás|
|---|---|---|
|Teljes bővített összeg|Összes használat|A teljes adók nélküli használati díj az adott időszakban, a kredit alkalmazása előtt.|
|Kötelezettségvállalás felhasználása|Kötelezettségvállalás felhasználása|Az adott időszak során alkalmazott kredit.|
|Összes értékesítés|Kerettúllépés összesen|A teljes, a kredit mennyiségét meghaladó használati díj. Az összeg az adókat nem tartalmazza.|
|Adó összege|Nem alkalmazható|Az adott időszakban keletkező összes értékesítésre vonatkozó adó összege.|
|Teljes összeg|Nem alkalmazható|A számla fizetendő összege a kredit alkalmazása és az adók hozzáadása után.|

### <a name="review-marketplace-invoice"></a>A Marketplace-számla áttekintése

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

Hasonlítsa össze az Azure Marketplace-re vonatkozó teljes összeget a piactéri számlájával az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján. A piactéri számlán csak az Azure Marketplace-beli vásárlások és használat szerepel. A **Használati összefoglalásának** összegei már tartalmazzák az adót, amelyet a közzétevő határoz meg.

Jelentkezzen be az [Enterprise Portalra](https://ea.azure.com), majd válassza a **Jelentések** lehetőséget. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Az **Azure Marketplace-re** vonatkozó teljes összegnek meg kell egyeznie a piactéri számla **összes értékesítésre** vonatkozó összegével. További információt a használaton alapuló díjakról a **használati adatok letöltését** ismertető cikkben talál. A **Marketplace díjai** területen válassza a **Letöltés** lehetőséget. A Marketplace-ár magában foglalja a közzétevő által meghatározott adót. Az ügyfelek nem kapnak külön számlát a közzétevőtől a tranzakciós adó beszedéséhez.

![Képernyőkép a Marketplace díjai alatt megjelenő letöltési lehetőségről.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>Árlista adatainak megtekintése

A vállalati rendszergazdák megtekinthetik az Azure-szolgáltatások regisztrációihoz társított árlistát.

Az aktuális árlista megtekintése:

1. Az Azure Enterprise Portalon válassza a **Jelentések** szakaszt, majd az **Árlista** elemet.
2. Tekintse meg az árlistát, vagy válassza a **Letöltés** lehetőséget.

![Árlista adatait bemutató példa](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

Korábbi árlista letöltése:

1. Az Azure Enterprise Portalon válassza a **Jelentések** szakaszt, majd a **Használati adatok letöltése** elemet.
2. Töltse le az árlistát.

![A régebbi árlisták letöltési helyét bemutató példa](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

Az árképzés eltéréseinek lehetséges okai:

- Előfordulhat, hogy a korábbi regisztráció és az új regisztráció eltérő díjszabással rendelkezik. Az árak azért változhatnak, mert a díjszabás az adott regisztráció esetében szerződéses jellegű, a szerződés kezdő dátumától egészen a záró dátumáig.
- Amikor új regisztrációra vált, az az új szerződés díjszabását követi. Az árakat az árlista határozza meg, ezért előfordulhat, hogy az árak magasabbak lesznek az új regisztrációban.
- Ha egy regisztrációt meghosszabbítanak, a díjszabás is változni fog. Az árak használatalapú fizetésre módosulnak.

## <a name="request-detailed-usage-information"></a>Részletes használati adatok kérése

A vállalati rendszergazdák az Azure Enterprise portálon megtekinthetik a használati adataik összegzését, a felhasznált Azure-előrefizetési keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

Az adott fiókok részletes használatának megtekintéséhez lépjen a **Jelentések** > **Használati adatok letöltése** elemre, és töltse le a Használati adatok jelentést.

> [!NOTE]
> A használati adatokról szóló jelentés nem tartalmazza a vonatkozó adókat. A használat felmerülésének időpontja és a jelentésben való megjelenése között akár nyolc órás késés is lehet.

Közvetett regisztrációk esetén a partnerének engedélyeznie kell az árrés funkciót ahhoz, hogy meg tudja tekinteni a költségekkel kapcsolatos információkat.

## <a name="reports"></a>Jelentések

A vállalati rendszergazdák az Azure Enterprise portálon megtekinthetik a használati adataik összegzését, a felhasznált Azure-előrefizetési keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

### <a name="azure-enterprise-reports"></a>Azure Enterprise-jelentések

- Használati adatok összegzése és grafikonok
- Szolgáltatáshasználati jelentés
- Egyenleg és díjak jelentése
- Használati adatok jelentése
- Az Azure Marketplace díjainak jelentése
- Árlista
- Speciális jelentés letöltése
- CSV-jelentés formázása

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>A használati adatokat összegző jelentések és ábrák megtekintése:

1. Nyissa meg az Azure Enterprise Portalt.
1. Válassza a bal oldali panelen lévő **Jelentések** elemet.
1. Válassza a **Használati adatok összegzése** fület.
1. Válassza ki a kötelezettségvállalási szintet a bal felső sarokban lévő dátumtartományok menüből.
1. Jelöljön ki egy időtartamot vagy egy hónapot a grafikonon további részletek megtekintéséhez.
1. Ezen a lapon:
   - Tekintse meg a grafikont, amely a havi használatot mutatja használati adatok, a szolgáltatások túlhasználata, a külön számlázott díjak és az Azure Marketplace-költségek megjelenítésével.
   - A grafikon alatt részlegek, fiókok és előfizetések alapján szűrheti az adatokat.
   - Válthat a **díjak szolgáltatások szerint** és a **díjak hierarchia szerint** való lebontása között.
   - Részletesen megtekintheti az Azure-szolgáltatásokat, a külön számlázott díjakat és az Azure Marketplace díjait.

## <a name="service-usage-report"></a>Szolgáltatáshasználati jelentés

A Szolgáltatáshasználati jelentés oldalon a vállalati rendszergazdák megtekinthetik a szolgáltatáshasználati adataik összegzését. A használati adatok minden fióknál és előfizetésnél az összegzés szintjén jelennek meg. A jelentést fiókok vagy előfizetések alapján is szűrheti a használat részleteinek megtekintéséhez.

> [!NOTE]
> A használat felmerülésének időpontja és a jelentésben való megjelenése között akár öt nap késés is lehet.

### <a name="to-view-the-report"></a>A jelentés megtekintése:

1. Jelentkezzen be az Azure Enterprise Portalra.
1. Válassza a bal oldali navigációs területen lévő **Jelentések** elemet.
1. Válassza a **Használati adatok összegzése** fület.
1. Válassza ki a dátumtartományt.
1. Válassza ki a megtekinteni kívánt fiókokat vagy előfizetéseket.
1. Lehetősége van az alábbiakra is:
   - A különböző lebontások megtekintéséhez váltson a díjak **szolgáltatások** vagy **hierarchia** szerinti nézetének megjelenítése között.
   - Megtekintheti a részleteket, beleértve a szolgáltatás nevét, a mértékegységet, a felhasznált egységeket, a tényleges díjat és a kiterjesztett költségeket.

## <a name="download-csv-reports"></a>CSV-jelentések letöltése

A vállalati rendszergazdák a Havi jelentés letöltése lapot használhatják több jelentés CSV-fájlként való letöltéséhez. A letölthető jelentések közé tartoznak a következők:

- Egyenleg és díjak jelentése
- Használati adatok jelentése
- Az Azure Marketplace díjainak jelentése
- Árlista

### <a name="to-download-reports"></a>A jelentések letöltéséhez:

1. Az Azure Enterprise Portalon válassza a **Jelentés** lapot.
1. Válassza ki a **Használati adatok letöltése** elemet a felső menüszalagon.
1. Válassza a **Letöltés** elemet a megfelelő havi jelentés mellett.

### <a name="csv-report-formatting-issues"></a>CSV-jelentés formázási problémái

Az Azure Enterprise Portal CSV-jelentéseit euróban megtekintő ügyfelek a vesszők és pontok miatt formázási problémákba ütközhetnek.

A következőket láthatja például:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Óra | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

A következőnek kell megjelennie:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Óra | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Ez a formázási probléma az Excel importálási funkciójának alapértelmezett beállításai miatt merül fel. Az Excel az összes mezőt „Általános” szövegként importálja, és feltételezi, hogy a számok a következő matematikai szabvány szerint vannak elválasztva. Például: „1,000.00”.

Így az ezresek elválasztásához pontot (.) és a tizedesjegyek elválasztásához vesszőt (,) használó európai pénznemek esetén a szám helytelenül fog megjelenni. Például: „1.000,00” Az importálás eredménye a területi nyelvi beállításoktól függően változhat.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>A CSV-fájl formázási problémák nélkül való importálásához:

1. A Microsoft Excelben lépjen a **Fájl** > **Megnyitás** területre.
   Megjelenik a Szöveg importálása varázsló.
1. Az **Eredeti adattípus** területen válassza a **tagolt** lehetőséget.  Az alapértelmezett érték a **Rögzített szélesség**.
1. Kattintson a **Tovább** gombra.
1. Az Elválasztók területen jelölje be a **Vessző** jelölőnégyzetét. Törölje a **Tab** jelölőnégyzetet, ha be van jelölve.
1. Kattintson a **Tovább** gombra.
1. Görgessen a **ResourceRate** és az **ExtendedCost** oszlopokra.
1. Válassza ki a **ResourceRate** oszlopot. Fekete színnel lesz kiemelve.
1. Az **Oszlop adatformátuma** szakaszban az **Általános** helyett válassza a **Szöveg** lehetőséget. Az oszlopfejléc az **Általános** értékről a **Szöveg** értékre változik.
1. Ismételje meg a 8. és a 9. lépést a **Kiterjesztett költség** oszlop esetében is, majd válassza a **Befejezés** lehetőséget.

> [!TIP]
> Ha az Excel a CSV-fájlok automatikus megnyitására van beállítva, akkor ehelyett az Excel **Megnyitás** funkcióját kell használnia. Az Excelben lépjen a **Fájl** > **Megnyitás** területre.

## <a name="balance-and-charge-report"></a>Egyenleg és díjak jelentése

Az Egyenleg és díjak jelentése havi összefoglaló információkat nyújt az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a többletköltségekről.

Az Azure-szolgáltatásokra vonatkozó kötelezettségvállalás összegző táblázatának összes sora statikus marad minden hónapban. Az összes vásárlás és kiigazítás részletei az **Új vásárlások részletei** és a **Kiigazítások részletei** szakaszokban jelennek meg.

### <a name="download-the-balance-and-charge-report"></a>Az egyenleg és díjak jelentésének letöltése

1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
1. Válassza a bal oldali panelen lévő **Jelentések** elemet.
1. Válassza a **Jelentés letöltése** fület.
1. Válassza ki a megfelelő hónapot az **Egyenleg és díjak** oszlopban, és válassza a jelentés letöltése lehetőséget.

## <a name="usage-detail-report"></a>Használati adatok jelentése

A Használati adatok jelentése havi összefoglalást nyújt a regisztráció által felhasznált szolgáltatásokról és mennyiségekről. Ide tartoznak a mérőszámok nevével, a mérőszámok típusával és a felhasznált mennyiségekkel kapcsolatos információk.

### <a name="download-the-usage-detail-report"></a>A használati adatokról szóló jelentés letöltése

1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
1. Válassza a bal oldali navigációs területen lévő **Jelentések** elemet.
1. Válassza a **Használati adatok letöltése** fület.
1. Válassza ki a megfelelő hónapot a **Használati adatok** oszlopban, és válassza a jelentés letöltése lehetőséget.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Az Azure Marketplace díjai az Azure Enterprise Portal jelentéseiben

Az Azure Marketplace-díjainak két típusa van:

- **Használatalapú:** Tranzakciós egységekben mért termékek.  A virtuális gépek például óradíjjal járnak, míg a Bing API-keresések a keresések száma alapján vannak felszámítva.
- **Havi díj:** A számlázás havi használat vagy hozzáférés alapján történik.

Az Azure Marketplace-díjakkal kapcsolatos további információkért lásd: [Azure Marketplace – Gyakori kérdések](https://azure.microsoft.com/marketplace/faq/).

Az Azure Enterprise Portal különböző díjainak megtekintése:

- **Használati adatokat összegző jelentés**: Az Azure Marketplace használatalapú és havi díjait **egyaránt** megjeleníti.
- **A Marketplace díjainak jelentése**: **Csak** a használatalapú Azure Marketplace-díjakat tartalmazza.  Az egyszeri díjak nem jelennek meg.

> [!NOTE]
> Az Azure Marketplace nem érhető el az MPSA-regisztrációkhoz.

## <a name="advanced-report-download"></a>Speciális jelentés letöltése

Az adott dátumtartományokkal vagy fiókokkal kapcsolatos jelentések elkészítéséhez a speciális jelentések letöltése használható. 2016. augusztus 30-tól a kimeneti fájl formátuma a nagyobb rekordhalmazok befogadása érdekében CSV-re változik.

1. Az Azure Enterprise Portalon válassza a **Speciális jelentés letöltése** lehetőséget.
1. Válassza ki a **megfelelő dátumtartományt**.
1. Válassza ki a **megfelelő fiókokat**.
1. Válassza a **Használati adatok lekérése** lehetőséget.
1. Válassza a **Frissítés** gombot, amíg a jelentés állapota **Letöltés** értékűre nem frissül.
1. Töltse le a jelentést.

## <a name="reporting-for-non-enterprise-administrators"></a>Jelentéskészítés nem vállalati rendszergazdák számára

A vállalati rendszergazdák engedélyezhetik a költségek megtekintéséhez való hozzáférést a részlegszintű rendszergazdák (DA) és a fióktulajdonosok (AO) számára egy adott regisztrációban. A fiók tulajdonosa letöltheti a fiókjához és előfizetéseihez tartozó CSV-jelentéseket. Emellett megtekintheti az Azure Enterprise Portal jelentési szakaszaiban lévő információkat is.

### <a name="to-enable-access"></a>A hozzáférés engedélyezése:

 1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
 1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
 1. Válassza a **Regisztráció** fület.
 1. A **Regisztráció részletei** szakaszban válassza a **Díjtételek megtekintése a részlegszintű rendszergazda számára** vagy a **Díjtételek megtekintése a fióktulajdonos számára** melletti ceruza ikont.
 1. Válassza az **Engedélyezve** lehetőséget.
 1. Kattintson a **Mentés** gombra.

### <a name="to-view-reports"></a>Jelentések megtekintése:

1. Jelentkezzen be az Azure Enterprise Portalra részlegszintű rendszergazdaként vagy fióktulajdonosként.
1. Válassza a bal oldali navigációs területen lévő **Jelentések** elemet.
1. A fiókra és az előfizetésre vonatkozó információk vizuális megjelenítéséhez válassza a **Használati adatok összegzése** fület.
1. A CSV-jelentések megtekintéséhez válassza a **Használati adatok letöltése** lehetőséget.

A részlegszintű rendszergazdák és a fióktulajdonosok nem tudják megtekinteni a díjakat, ha a **Speciális jelentés letöltése** funkciót használják. A költségek 0 dollárként jelennek meg.

A Díjtételek megtekintése a fióktulajdonos számára beállítás a fióktulajdonosokra és a társított előfizetéseken engedélyekkel rendelkező összes felhasználóra kiterjed. Ha Ön közvetett ügyfél, a költségfunkciókat a csatornapartnerének kell engedélyeznie.

## <a name="power-bi-reporting"></a>Power BI-jelentések készítése

A Power BI-jelentések készítése olyan közvetlen, partneri és közvetett vállalati szerződéssel rendelkező ügyfelek számára érhető el, akik hozzáféréssel rendelkeznek a számlázási adatok megtekintéséhez.

### <a name="power-bi-pro"></a>Power BI Pro

A Power BI Pro elérhető az EA-ügyfelek számára. A Power BI Pro segítségével jelentéseket hozhat létre és oszthat meg a költségadatok hatékony kezeléséhez. További együttműködési és adatfrissítési funkciókkal is rendelkezik. A Power BI Pro nagyobb adatkapacitást és adatátviteli korlátokat biztosít.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Hozzáférés a Microsoft Azure használati elemzéseihez:

1. Lépjen a [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) területre.
1. Válassza a **Letöltés** elemet.
1. Adja meg a regisztrációs számot és a hónapok számát, majd válassza a **Tovább** elemet.
1. Adja meg API-hozzáférési kulcsát a kapcsolódáshoz. A regisztrációhoz tartozó kulcs az [Enterprise Portalon](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) érhető el.
1. Válassza a **Bejelentkezés** elemet az importálási folyamat automatikus elkezdéséhez.
1. Ha befejeződött, a navigációs panelen megjelenik egy új irányítópult, jelentés és modell. Az importált adatok megtekintéséhez válassza ki az irányítópultot.

> [!TIP]
>
> - A regisztráció API-kulcsának létrehozásával kapcsolatos további információkért tekintse meg az API-jelentések súgófájlját az [Enterprise Portalon](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - A Power BI Azure-fogyasztással való összekapcsolásáról lásd: [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>A korábbi Power BI EA-tartalomcsomag elérése:

1. Lépjen a [Power BI webhelyére](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Jelentkezzen be egy érvényes munkahelyi vagy iskolai fiókkal.

   A munkahelyi vagy iskolai fiók lehet ugyanaz vagy egy másik fiók, mint amelyet a regisztrációnak az Azure Enterprise Portallal történő eléréséhez használ.
1. A szolgáltatások irányítópultján válassza a **Microsoft Azure Enterprise** elemet, majd válassza a **Csatlakozás** elemet.
1. Az **Azure Enterprise-hoz való kapcsolódás** képernyőjén töltse ki az alábbi mezőket:
    - Az Azure-környezet URL-címe: [https://ea.azure.com](https://ea.azure.com/)
    - Hónapok száma: 1 és 36 között
    - Regisztrációs szám: az Ön regisztrációs száma
1. Kattintson a **Tovább** gombra.
1. A **Hitelesítési kulcs mezőjében** adja meg az API-kulcsot.

    Az API-kulcsot az Azure Enterprise Portalon, a **Használati adatok letöltése** lapon szerezheti be. Válassza az **API hozzáférési kulcsa** elemet, majd másolja és illessze be a kulcsot a **Fiókkulcs** mezőbe.
1. Az adathalmaz méretétől függően az adatok betöltése a Power BI-ba 5–30 percet vesz igénybe.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Az erőforrás-díjszabás kiszámítására szolgáló folyamatok szószedete

- **Összes RAW-egység:** A részletes használati jelentésen belül felhasznált mennyiség.
- **MOCP-erőforrás egységenként:** A felsőbb rétegbeli használati rendszer különböző egységekben bocsátja ki az egyes szolgáltatások használatát. (Előre beállított)
- **Egységenkénti felhasználás:** Azure Enterprise-beli mértékegység. (Előre beállított)
- **Ár:** Az Azure Enterprise Portalon lévő egységár.
- **Teljes költség:** A részletes használati jelentésből származó kiterjesztett költség vagy a kerethasználat + túlhasználat az Azure Enterprise Portalról.

### <a name="charges-calculations"></a>Díjszámítások

- **Konvertálás felhasznált MOCP-erőforrásokká** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Konvertálás felhasznált egységekké** = `Consumed MOCP Resources / Consumption per Unit`
- **Összköltség kiszámítása** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>A felhasználás számítási logikája

**Erőforrás-díjszabás** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Az erőforrás díjszabását a díjak alapján állapítjuk meg. Így gyakran nem egyezik meg az árlistán szereplő tényleges egységárral.

A letöltött használatiadat-jelentésen látható, hogy a nyers erőforrás-használat adatai legfeljebb hat tizedesjegy pontossággal jelennek meg. Ezek az adatok a túlhasználati díjak kiszámításához szükségesek. Az Azure Enterprise Portal azonban négy tizedesjegyre kerekíti a használatot a kötelezettségvállalási egységek esetében, a túlhasználati egységek esetében pedig nulla tizedesjegyre rövidít. Az Azure Enterprise Portalon az összes túlhasználati díjat csak a teljes egységek után számítjuk fel. Jelentős különbség észlelhető az egységek ára, valamint a túlhasználatért és a vegyes hónapokban fizetendő erőforrás-használati díj között.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A számlázott díjtételek áttekintése
> * Szolgáltatási kerettúllépési díjtételek áttekintése
> * A Marketplace-számla áttekintése

Folytassa a következő cikkel, ha többet szeretne megtudni az Azure EA Portal használatáról.

> [!div class="nextstepaction"]
> [Ismerkedés az Azure EA Portallal](../manage/ea-portal-get-started.md)