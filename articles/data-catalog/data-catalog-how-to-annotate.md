---
title: Adatforrások megjegyzése Azure Data Catalogban
description: Útmutató az adategységek Azure Data Catalogban való megjegyzésének kiemeléséhez, beleértve a felhasználóbarát neveket, címkéket, leírásokat és szakértőket.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 89fa1b542c54fc962f8c12a2142884c677afb259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674648"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Adatforrások megjegyzése Azure Data Catalogban

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés

A **Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőalapú szolgáltatás, amely a vállalati adatforrások regisztrálási és felderítési rendszereként szolgál. Ez azt jelenti, hogy Data Catalog az a célja, hogy segítsen az embereknek felderíteni, megérteni és használni az adatforrásokat, és hogy segítse a szervezeteket abban, hogy a meglévő adatokból több értéket kapjanak. Ha egy adatforrás regisztrálva van Data Catalog, a metaadatokat a szolgáltatás átmásolja és indexeli, de a történet nem fejeződik be. Data Catalog lehetővé teszi a felhasználók számára, hogy saját leíró metaadatokat – például leírásokat és címkéket – adjanak hozzá az adatforrásból kinyert metaadatokhoz, és hogy az adatforrás jobban érthető legyen több személy számára.

## <a name="annotation-and-crowdsourcing"></a>Jegyzet és közösségi
Mindenkinek van véleménye. És ez egy jó dolog.
Data Catalog felismeri, hogy a különböző felhasználók különböző perspektívákkal rendelkeznek a vállalati adatforrások esetében, és ezek a perspektívák hasznosak lehetnek. Vegyük példaként a következő esetet:

* A rendszergazda ismeri az adatforrást üzemeltető kiszolgálók vagy szolgáltatások szolgáltatási szintjének egyezményét.
* Az adatbázis rendszergazdája ismeri a biztonsági mentési ütemtervet az egyes adatbázisokhoz, valamint az engedélyezett ETL-feldolgozási időszakokat.
* A rendszer tulajdonosa tudja, hogy a felhasználók milyen eljárást igényelhetnek az adatforráshoz való hozzáféréshez.
* Az adatkezelő tudja, hogyan képezi le az adatforrásban lévő eszközöket és attribútumokat a vállalati adatmodellre.
* Az elemző tudja, hogyan használják az adatmennyiséget az általuk támogatott üzleti folyamatok kontextusában.

Ezen perspektívák mindegyike értékes, és Data Catalog egy közösségi megközelítést használ a metaadatokhoz, amelyek lehetővé teszik, hogy mindegyik rögzíthető legyen, és hogy teljes képet nyújtson a regisztrált adatforrásokról. A Data Catalog portál használatával minden felhasználó hozzáadhat és szerkesztheti a saját megjegyzéseit, miközben megtekintheti a más felhasználók által biztosított megjegyzéseket.

## <a name="different-types-of-annotations"></a>Különböző típusú jegyzetek
Data Catalog a következő típusú megjegyzéseket támogatja:

| Jegyzet | Jegyzetek |
| --- | --- |
| Felhasználóbarát név |A felhasználóbarát nevek az adategység szintjén adhatók meg, hogy az adategységek könnyebben érthetőek legyenek. A felhasználóbarát nevek akkor hasznosak, ha az alapul szolgáló objektumnév Crypter, rövidített vagy más módon nem értelmezhető a felhasználók számára. |
| Description |A leírásokat az adategységek és az attribútumok/oszlopok szintjein lehet megadni. A leírások olyan rövid szöveges megjegyzések, amelyek leírják a felhasználó perspektíváját az adategységen vagy annak használatával. |
| Címkék (felhasználói címkék) |A címkéket az adategység és az attribútum/oszlop szintjein lehet megadni. A felhasználói címkék felhasználó által definiált címkék, amelyek az adategységek vagy attribútumok kategorizálására használhatók. |
| Címkék (Szószedet-címkék) |A címkéket az adategység és az attribútum/oszlop szintjein lehet megadni. A Szószedet címkék központilag definiált szószedeti kifejezések, amelyek segítségével kategorizálhatja az adategységeket és az attribútumokat egy közös üzleti besorolással. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása) |
| Szakértők |Szakértőket az adategység szintjén lehet megadni. A szakértők olyan felhasználókat vagy csoportokat azonosítanak az adathoz, amelyek szakértői perspektívával rendelkeznek, és a regisztrált adatforrásokat felderítő felhasználók és a meglévő megjegyzések által nem válaszoló kérdésekre is kiszolgálják. |
| Hozzáférés kérése |Hozzáférési adatok kérése az adategység szintjén biztosítható. Ezek az információk azokra a felhasználókra vonatkoznak, akik olyan adatforrást észlelnek, amelyhez még nem rendelkeznek hozzáférési engedéllyel. A felhasználók megadhatják annak a felhasználónak vagy csoportnak az e-mail-címét, aki hozzáférést biztosít a felhasználóknak, vagy megadhatja, hogy a felhasználó milyen folyamathoz vagy eszközhöz férhet hozzá, vagy akár szövegként is megadhatja a folyamatot. |
| Dokumentáció |A dokumentációt az adategység szintjén lehet megadni. Az eszköz dokumentációja olyan gazdag szöveges adatokat tartalmaz, amelyek hivatkozásokat és képeket tartalmazhatnak, és amelyek a leírások és címkék használatával nem továbbított információkat is megadhatnak. |

## <a name="annotating-multiple-assets"></a>Több eszköz megjegyzése
Ha több adategységet választ ki a Data Catalog-portálon, a felhasználók egyetlen művelettel láthatják az összes kijelölt eszközt. A jegyzetek minden kiválasztott eszközre érvényesek lesznek, így egyszerűen kiválaszthatók és megadhatók a kapcsolódó adategységekhez tartozó címkék és szakértők.

> [!NOTE]
> Címkék és szakértők is megadhatók az adategységek Data Catalog adatforrás-regisztrációs eszköz használatával történő regisztrálása során.
>
>

Több tábla és nézet kiválasztásakor a Data Catalog-portálon csak azok az oszlopok jelennek meg, amelyekben az összes kijelölt adategység közösen fog szerepelni. Ez lehetővé teszi, hogy a felhasználók címkéket és leírásokat adjanak meg az összes kijelölt eszközhöz azonos nevű oszlopokhoz.

## <a name="annotations-and-discovery"></a>Jegyzetek és felderítés
Ahogy a regisztráció során az adatforrásból kinyert metaadatok a Data Catalog keresési indexbe kerülnek, a felhasználó által megadott metaadatok is indexelve vannak. Ez azt jelenti, hogy a jegyzetek nem csupán megkönnyítik a felhasználók számára az észlelt adatok megértését, a jegyzetek azt is megkönnyítik a felhasználók számára, hogy a megjegyzések alapján megkeressék a jegyzett adategységeket.

## <a name="summary"></a>Összefoglalás
Az adatforrások Data Catalogsal való regisztrálása lehetővé teszi, hogy az adatforrásból a katalógus szolgáltatásba másolja a strukturális és leíró metaadatokat az adatokból. Az adatforrások regisztrálását követően a felhasználók a Data Catalog-portálon belül könnyebben felfedezhetik és megismerhetik a megjegyzéseket.

## <a name="see-also"></a>Lásd még
* [Ismerkedjen meg Azure Data Catalog](data-catalog-get-started.md) oktatóanyaggal, amely részletesen ismerteti az adatforrások feliratozásának lépéseit.
