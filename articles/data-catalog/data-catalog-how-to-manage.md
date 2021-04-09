---
title: Adategységek kezelése Azure Data Catalogban
description: A cikk kiemeli, Hogyan szabályozható a Azure Data Catalogban regisztrált adategységek láthatósága és tulajdonosa.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 0182f744e038bc8080b9fb96bfdb1eeda16b86c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674852"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Adategységek kezelése Azure Data Catalogban

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés
A Azure Data Catalog adatforrások felderítésére szolgál, így könnyen felderítheti és értelmezheti az elemzéshez és a döntések végrehajtásához szükséges adatforrásokat. Ezek a felderítési képességek a legnagyobb hatással lehetnek arra, hogy Ön és más felhasználók megtalálják és megértsék az elérhető adatforrások legszélesebb körét. Ezeket az elemeket szem előtt tartva az Data Catalog alapértelmezett viselkedése minden regisztrált adatforráshoz az összes katalógus-felhasználó számára láthatóvá és felderíthetővé válik.

Data Catalog nem biztosít hozzáférést magához az adatelérési ponthoz. Az adathozzáférést az adatforrás tulajdonosa vezérli. A Data Catalog használatával felderítheti az adatforrásokat, és megtekintheti a katalógusban regisztrált forrásokhoz kapcsolódó metaadatokat.

Előfordulhat azonban, hogy az adatforrásokat csak bizonyos felhasználók, illetve adott csoportok tagjai láthatják. Ilyen esetekben a felhasználók a katalógusban lévő regisztrált adategységek tulajdonjogát vehetik igénybe, majd szabályozhatják a saját eszközök láthatóságát.

> [!NOTE]
> A cikkben ismertetett funkciók csak a Azure Data Catalog standard kiadásában érhetők el. Az ingyenes kiadás nem biztosít lehetőséget az adategység láthatóságának tulajdonlására és korlátozására.
>
>

## <a name="manage-ownership-of-data-assets"></a>Adategységek tulajdonjogának kezelése
Alapértelmezés szerint a Data Catalogban regisztrált adategységek nem tulajdonosai. Minden olyan felhasználó, aki jogosult a katalógus elérésére, felderítheti és jegyzetekkel láthatja el ezeket az eszközöket. A felhasználók tulajdonba vehetik a nem tulajdonban lévő adategységeket, majd korlátozhatják a saját eszközök láthatóságát.

Ha Data Catalogban lévő adategység tulajdonosa, csak a tulajdonosok által jogosult felhasználók felfedezhetik az eszközt, és megtekinthetik a metaadatokat, és csak a tulajdonosok törölhetik az eszközt a katalógusból.

> [!NOTE]
> A Data Catalog tulajdonjoga csak a katalógusban tárolt metaadatokat érinti. A tulajdonjog nem biztosít semmilyen engedélyt az alapul szolgáló adatforráshoz.
>
>

### <a name="take-ownership"></a>Saját tulajdonba vétel
A felhasználók az adategységek tulajdonjogát úgy vehetik igénybe, hogy a Data Catalog portálon a **tulajdonosi** hozzáférés lehetőségre kattintanak. A nem birtokolt adategységek tulajdonjogának átvételéhez nincs szükség különleges engedélyekre. Bármely felhasználó tulajdonba veheti egy nem birtokolt adategységet.

### <a name="add-owners-and-co-owners"></a>Tulajdonosok és közös tulajdonosok hozzáadása
Ha egy adategység már tulajdonosa, a többi felhasználó nem veheti igénybe egyszerűen a tulajdonjogot. Egy meglévő tulajdonosnak közös tulajdonosként kell felvennie őket. Bármely tulajdonos további felhasználókat vagy biztonsági csoportokat adhat hozzá közös tulajdonosként.

> [!NOTE]
> Az ajánlott eljárás az, hogy legalább két személy tulajdonosként rendelkezzen bármely tulajdonban lévő adategység tulajdonosaként.
>
>

### <a name="remove-owners"></a>Tulajdonosok eltávolítása
Ugyanúgy, ahogy bármely eszköz tulajdonosa hozzáadhat közös tulajdonosokat, bármely objektum tulajdonosa eltávolíthat bármely társtulajdonosa.

Az adategység tulajdonosa, aki nem távolítja el magukat tulajdonosként, már nem tudja kezelni az eszközt. Ha az eszköz tulajdonosa eltávolítja magukat tulajdonosként, és nincsenek más közös tulajdonosok, az eszköz visszavált egy nem birtokolt állapotra.

## <a name="control-visibility"></a>Vezérlő láthatósága
Az Adateszközök tulajdonosai vezérelhetik a saját adategységek láthatóságát. Ha korlátozni szeretné a láthatóságot az alapértelmezett értékre, ahol az összes Data Catalog felhasználó képes felderíteni és megtekinteni az adategységet, az eszköz tulajdonosa a **mindenki** számára elérhető láthatósági beállítást válthat a felhasználók számára az objektum tulajdonságainál **&** . A tulajdonosok hozzáadhatnak bizonyos felhasználókat és biztonsági csoportokat.

> [!NOTE]
> Ha lehetséges, az eszköz tulajdonosi és láthatósági engedélyeit biztonsági csoportokhoz kell rendelni, nem pedig az egyes felhasználókhoz.
>
>

## <a name="catalog-administrators"></a>Katalógus-rendszergazdák
Data Catalog a rendszergazdák a katalógusban lévő összes eszköz tulajdonosát implicit módon közösen használják. Az eszközök tulajdonosai nem tudják eltávolítani a rendszergazdák láthatóságát, a rendszergazdák pedig kezelhetik a katalógusban lévő összes adategység tulajdonosát és láthatóságát.

## <a name="summary"></a>Összefoglalás
A Data Catalog közösségi modell metaadatok és adategységek felderítése lehetővé teszi a katalógus összes felhasználója számára, hogy hozzájáruljanak és felfedezzék. A Data Catalog standard kiadása úgy van kialakítva, hogy az adott adategységek láthatóságának és felhasználásának korlátozásával a tulajdonjogot és a felügyeletet.
