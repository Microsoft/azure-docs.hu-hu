---
title: Marketing tulajdonságai
description: A portálon összegyűjtött különböző értékesítési mezők leírása, valamint azok megjelenítése az Azure Certified-eszköz katalógusában
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 262616ca82e9c06baec0d7a1b81a0e3dff2ed8db
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304511"
---
# <a name="marketing-properties"></a>Marketing tulajdonságai

Az [eszköz adatainak hozzáadási](tutorial-02-adding-device-details.md)folyamata során meg kell adnia a marketing-információkat, amelyeket az [Azure Certified-eszköz katalógusában](https://devicecatalog.azure.com)fog megjelenni. Ezeket az információkat a rendszer a minősítési beküldési folyamat során az Azure Certified Device Portalon gyűjti össze, és a katalógusban szűrési paramétereket használ. Ez a cikk a portálon összegyűjtött, a katalógusban látható mezők közötti leképezést ismerteti. A cikk elolvasása után a partnereknek jobban meg kell érteniük, hogy milyen információkat kell megadniuk a minősítési folyamat során, hogy a legjobban képviseljék termékeiket a katalógusban.

![A PDP áttekintése](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Az Azure Certified Device Catalog termék csempe

A katalógus látogatói először a Search oldalon a katalógus termék csempéjét használják majd. Ez alapvető áttekintést nyújt a kapott eszközről és tanúsítványokról.

![Termék csempe sablon](./media/concepts-marketing/product-tile.png)

| Mező | Leírás                  | A portálon való hozzáadás helye                |
|---------------|-------------------------|----------------------------------|
| Eszköz neve | A minősített eszköz nyilvános neve         | Az eszköz részleteinek alapjai lap|
| Cég neve| A vállalat nyilvános neve  | Nem szerkeszthető a portálon. Az MPN-fiók nevéből kinyerve |
| Termék fényképe  | Az eszköz képe a minimális felbontás 200p x 200p  | Marketing részletei |
| Minősítés besorolása  | Kötelező Azure Certified tanúsítvány címkéje és opcionális minősítési jelvények  | Az eszköz részleteinek alapjai lap. Megfelelő tesztelést kell átadnia a csatlakozási & tesztelési szakaszban. |

## <a name="product-description-page-information"></a>Termékleírás lap adatai

Ha az ügyfél az eszköz csempére kattintott a katalógus keresése oldalon, a rendszer megnyitja az eszköz Product Description (termékleírás) lapját. Ebben az esetben a rendszer a minősítési folyamat során megadott információk nagy részét fogja találni.

A termékleírás oldal tetején a legfontosabb jellemzők szerepelnek, amelyek némelyikét már használták a termék csempén.

![PDP felső sáv](./media/concepts-marketing/pdp-top.png)

| Mező | Leírás                  | A portálon való hozzáadás helye                |
|---------------|-------------------------|----------------------------------|
| Eszköz osztálya | Az eszköz és az eszköz elsődleges céljának besorolása (további[információ](./resources-glossary.md))       | Az eszköz részleteinek alapjai lap|
| Eszköz típusa | Az eszköz besorolása a megvalósítási készültség alapján (további[információ](./resources-glossary.md)) | Az eszköz részleteinek alapjai lap |
| Földrajzi elérhetőség | Azok a régiók, amelyekhez az eszköz megvásárolható  | Marketing részletei |
| Operációs rendszerek  | Az eszköz által támogatott operációs rendszer (ek)  | Az eszköz részletes termék részletei lap |
| Megcélzott iparágak  | A 3 legfontosabb iparág, amelyet az eszköz a következőhöz optimalizált:  | Marketing részletei |
| Termék leírása  | Ingyenes szövegmező a termék marketing-leírásának megírásához. Ez rögzítheti a portálon nem szereplő adatokat, vagy további kontextust is hozzáadhat az eszköz használatának előnyeihez. | Marketing részletei|

A lap hátralévő része az eszköz technikai specifikációjának táblázatos formátumban való megjelenítésére összpontosít, amely segít az ügyfeleknek a termék jobb megismerésében. A kényelem érdekében az oldal tetején megjelenő információk itt is szerepelnek. A táblázat további részeit a portálon megadott összetevők határozzák meg.

![PDP – alsó oldal](./media/concepts-marketing/pdp-bottom.png)

| Mező | Leírás                  | A portálon való hozzáadás helye                |
|---------------|-------------------------|----------------------------------|
| Összetevő típusa | Az eszköz és az eszköz elsődleges céljának besorolása (további[információ](./resources-glossary.md))       | Az eszköz részletei|
| Összetevő neve| A leíró összetevő neve | Az eszköz részletei |
| További összetevő-információk | További hardver-specifikációk, például érzékelők, kapcsolatok, Gyorssegédek stb.  | További összetevő-információk az eszköz adatairól (további[információ](./how-to-using-the-components-feature.md))  |
| Eszköz függőségi szövege | Partner által megadott szöveg, amely leírja a terméknek az Azure-hoz való kapcsolódáshoz szükséges különböző függőségeit (további[információ](./how-to-indirectly-connected-devices.md))   | Az eszköz függőségeinek a függőségek lapján megjelenő, ügyfelek felé irányuló megjegyzések szakasza |
| Eszköz függőségi hivatkozása  | Az aktuális termék által igényelt hitelesített eszközre mutató hivatkozás  | Az eszköz részletei lap függőségei |

## <a name="shop-links"></a>Shop-hivatkozások
A termék csempéje és a termékleírás oldalon a Shop gomb is elérhető. Amikor az ügyfél rákattintott, megnyílik egy ablak, amely lehetővé teszi a terjesztők kiválasztását (legfeljebb 5 terjesztőt listázhat). A kiválasztást követően a rendszer átirányítja az ügyfelet a partner által megadott URL-címre.

![Az áruház előugró felületének képe](./media/concepts-marketing/shop.png)

| Mező | Leírás                  | A portálon való hozzáadás helye                |
|---------------|-------------------------|----------------------------------|
| Terjesztő neve | A terméket értékesítő terjesztő neve | Marketing részletei|
| Eszköz beolvasása| Külső webhelyre mutató hivatkozás az eszköz megvásárlásához (vagy árajánlat kérése a terjesztőtől). Ez lehet ugyanaz, mint a gyártó lapja, ha a terjesztő ugyanaz, mint az eszköz gyártója. Ha a vásárlási oldal nem érhető el, a rendszer átirányítja a terjesztő oldalára, hogy az ügyfél közvetlenül kapcsolatba lépjen velük.  | A forgalmazói termék oldalának URL-címe a marketing részleteiben. Ha nem áll rendelkezésre vásárlási oldal, a hivatkozás alapértelmezés szerint a terjesztő URL-címére mutat a marketing részleteiben. |

## <a name="external-links"></a>Külső hivatkozások
A Product Description (termék leírása) oldalon található hivatkozások a partner által biztosított webhelyekre vagy fájlokra is kiterjednek, amelyek segítenek az ügyfélnek a termék jobb megismerésében. Az oldal tetején jelennek meg a termékleírás szövege alatt. A megjelenített hivatkozások eltérőek lesznek a különböző típusú eszközök és a minősítési programok esetében.

| Hivatkozás | Description                  | A portálon való hozzáadás helye                |
|---------------|-------------------------|----------------------------------|
| Útmutató az első lépésekhez * | Az eszköz Azure-szolgáltatásokkal való csatlakoztatására és használatára vonatkozó felhasználói utasításokat tartalmazó PDF-fájl | A portál első lépések útmutató szakaszának hozzáadása|
| Gyártó lapja *|A gyártó oldalára mutató hivatkozás. Ez az oldal lehet az eszközhöz tartozó termék oldal vagy a vállalat kezdőlapja, ha a marketing lap nem érhető el. | A gyártó marketing lapja a marketing részleteiben |
| Eszközmodell | Nyilvános DTDL modellek IoT Plug and Play megoldásokhoz  | Nem szerkeszthető a portálon. Az eszköz modelljét fel kell tölteni a ([nyilvános modell tárházba](https://aka.ms/modelrepo) )  |
| Eszköz forráskódja | A fejlesztői csomaghoz tartozó eszközök forráskódjának URL-címe| Az eszköz részleteinek alapjai lap  |

 **Minden közzétett eszközhöz szükséges*

## <a name="next-steps"></a>Következő lépések
Most, hogy megértette, hogyan használjuk fel a minősítés során megadott információkat, most már készen áll az eszköz tanúsítására! Indítsa el a minősítési projektet, vagy ugorjon vissza az eszköz részletei szakaszba, és adja hozzá saját marketing-információit.

- [A minősítési út elindítása](./tutorial-00-selecting-your-certification.md)
- [Eszköz adatainak hozzáadása](./tutorial-02-adding-device-details.md)
