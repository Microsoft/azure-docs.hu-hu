---
title: Adatforrások dokumentálása Azure Data Catalog
description: Útmutató az adategységek Azure Data Catalogban való dokumentálására.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 21168e403ffa8fb1472a5bfcac1fcd2079a52e2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674920"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Adatforrások dokumentálása Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés
A **Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőalapú szolgáltatás, amely a vállalati adatforrások regisztrálási és felderítési rendszereként szolgál. Ez azt jelenti, hogy **Azure Data Catalog** az a célja, hogy segítsen az embereknek felderíteni, *megérteni* és használni az adatforrásokat, és hogy segítse a szervezeteket abban, hogy a meglévő adatokból több értéket kapjanak.

Ha egy adatforrás regisztrálva van **Azure Data Catalog**, a metaadatokat a szolgáltatás átmásolja és indexeli, de a történet nem fejeződik be. **Azure Data Catalog** azt is lehetővé teszi, hogy a felhasználók saját teljes dokumentációt adjanak meg, amely leírja az adatforrás használati és általános forgatókönyveit.

Az [adatforrások feliratozásával](data-catalog-how-to-annotate.md)megismerheti, hogy az adatforrást ismerő szakértők megjegyzésekkel láthatják el a címkéket és a leírást. A **Azure Data Catalog** -portál egy Rich Text Editort tartalmaz, így a felhasználók teljes mértékben dokumentálják az adategységeket és a tárolókat. A szerkesztő tartalmazza a bekezdések formázását, például a fejléceket, a szövegformázást, a listajeles listákat, a számozott listákat és a táblákat.

A címkék és a leírások kiválóan használhatók az egyszerű jegyzetekhez. Ahhoz azonban, hogy az adatfogyasztók jobban megértsék az adatforrások használatát és az adatforráshoz kapcsolódó üzleti forgatókönyveket, a szakértők teljes körű és részletes dokumentációt is biztosíthatnak. Az adatforrások egyszerű dokumentálása. Válasszon ki egy adategységet vagy tárolót, és válassza a **dokumentáció** elemet.

![Dokumentációs lap Data Catalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Adategységek dokumentálása
Az **Azure Data Catalog** dokumentáció előnyei lehetővé teszik, hogy az adategységek teljes elbeszélésének létrehozásához a Data Catalog használja a tartalom tárházában. Megtekintheti a tárolókat és táblákat leíró részletes tartalmakat. Ha már rendelkezik tartalommal egy másik adattárban, például a SharePointban vagy egy fájlmegosztásban, hozzáadhat az eszköz dokumentációs hivatkozásaihoz, és hivatkozhat erre a meglévő tartalomra. Ez a funkció lehetővé teszi, hogy a meglévő dokumentumok felderíthetők legyenek.

> [!NOTE]
> A keresési index nem tartalmazza a dokumentációt.
>

![Dokumentáció lap és hivatkozás a webes hivatkozásra](media/data-catalog-documentation/data-catalog-documentation2.png)

A dokumentáció szintje az adategység-tároló jellemzőinek és értékének leírására terjedhet ki egy tárolóban lévő Table Schema részletes leírására. A dokumentáció szintjét az üzleti igényeknek megfelelően kell megadnia. Az adategységek dokumentálása azonban általában néhány előnye és hátránya:

* Csak egy tároló dokumentálása: az összes tartalom egy helyen található, de nem feltétlenül szükséges adatok a felhasználók számára a tájékozott döntés meghozatalához.
* Csak a táblákat dokumentálja: a tartalom az adott objektumra vonatkozik, de a felhasználók több hellyel rendelkeznek a dokumentumokhoz.
* Document containers and Tables: legátfogóbb megközelítés, de a dokumentumok további karbantartását is bevezethetik.

## <a name="summary"></a>Összefoglalás
Az adatforrások **Azure Data Catalogsal** való dokumentálása a szükséges részletességgel részletes információkkal szolgálhat az adategységekről.  A hivatkozások használatával egy meglévő tartalom-tárházban tárolt tartalomhoz csatolhat, amely a meglévő docs-és adategységeket együtt hozza össze. Miután a felhasználók felderítik a megfelelő adategységeket, a dokumentáció teljes készletét használhatja.
