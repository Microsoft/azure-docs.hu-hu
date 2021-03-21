---
title: Csomagok létrehozása az Azure-alkalmazás ajánlatához
description: Megtudhatja, hogyan hozhat létre terveket az Azure-alkalmazás ajánlatához a partner Centerben.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370263"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Csomagok létrehozása az Azure-alkalmazás ajánlatához

A Microsoft kereskedelmi Piactéren keresztül eladott ajánlatoknak legalább egy csomaggal kell rendelkezniük az ajánlat kereskedelmi piactéren való listázásához. Több különböző csomagot is létrehozhat ugyanazon az ajánlaton belül. A csomagok (más néven SKU-ként) a csomag típusa (_megoldás sablonja_ vagy _felügyelt alkalmazás_), a monetizálása vagy a célközönség szempontjából eltérőek lehetnek. A csomagokkal kapcsolatos általános útmutatásért lásd: a [kereskedelmi piactéren elérhető csomagok és díjszabások](plans-pricing.md).

## <a name="create-a-plan"></a>Csomag létrehozása

1. A **terv áttekintése** lap tetején válassza az **+ új terv létrehozása** lehetőséget.
1. A megjelenő párbeszédpanel **terv azonosítója** mezőjében adjon meg egy egyedi csomagot. Ez az azonosító a termék URL-címében szereplő ügyfelek számára jelenik meg. Legfeljebb 50 kisbetűs alfanumerikus karaktert, kötőjelet vagy aláhúzást használhat. A terv azonosítója a **Létrehozás** gombra kattintva nem módosítható.
1. A **terv neve** mezőbe írjon be egy egyedi nevet ehhez a csomaghoz. Az ügyfelek ezt a nevet fogják látni, amikor azt döntik el, hogy melyik tervet kell kiválasztani az ajánlaton belül. Legfeljebb 50 karaktert használhat.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="define-the-plan-setup"></a>A terv beállításának megadása

A **terv beállítása** lapon beállíthatja a terv típusát, attól függetlenül, hogy egy másik csomag technikai konfigurációját használja-e, és hogy a terv milyen Azure-régiókban legyen elérhető. Az ezen a lapon megjelenő válaszok hatással lesznek arra, hogy mely mezők jelenjenek meg a csomag más lapjain.

### <a name="select-the-plan-type"></a>Válassza ki a csomag típusát

- A **terv típusa** listából válassza a **megoldás sablon** vagy a **felügyelt alkalmazás** lehetőséget.

A **megoldás sablonjait** teljes mértékben az ügyfél kezeli. A **felügyelt alkalmazási** csomag lehetővé teszi, hogy a kiadók az alkalmazást az ügyfél nevében kezeljék. A két csomag típusával kapcsolatos részletekért lásd: [csomagok típusai](plan-azure-application-offer.md#types-of-plans).

### <a name="re-use-technical-configuration-optional"></a>Technikai konfiguráció újbóli használata (nem kötelező)

Ha több azonos típusú csomagot hozott létre ezen az ajánlaton belül, és a technikai konfiguráció azonos egymás között, a technikai konfigurációt más csomagból is felhasználhatja. Ez a beállítás a terv közzététele után nem módosítható.

#### <a name="to-re-use-technical-configuration"></a>A technikai konfiguráció újbóli használata

1. Jelölje be a **csomag újrahasznosítja a technikai konfigurációt egy másik, azonos típusú csomag esetén** jelölőnégyzetből.
1. A megjelenő listában válassza ki a kívánt alaptervet.

> [!NOTE]
> Ha egy másik csomagból újrahasznál csomagokat, a teljes **technikai konfiguráció** lap eltűnik ebből a csomagból. A csomagra vonatkozó technikai konfigurációs adatokat, beleértve a jövőben végzett frissítéseket is, ezt a csomagot is használják.

### <a name="select-azure-regions-clouds"></a>Azure-régiók kiválasztása (felhők)

A tervet legalább egy Azure-régióban elérhetővé kell tenni. Miután közzétette és elérhetővé tette a csomagot egy adott Azure-régióban, az ajánlatból nem távolíthatja el ezt a régiót.

#### <a name="azure-global-region"></a>Azure globális régió

Alapértelmezés szerint az **Azure Global** jelölőnégyzet be van jelölve. Így a terv minden olyan globális Azure-régióban elérhetővé válik az ügyfelek számára, amely kereskedelmi Piactéri integrációt is tartalmaz. A felügyelt alkalmazási csomagok esetében kiválaszthatja azokat a piacokat, amelyekkel elérhetővé szeretné tenni a tervét.

Ha el szeretné távolítani az ajánlatot ebből a régióból, törölje az **Azure globális** jelölőnégyzet jelölését.

#### <a name="azure-government-region"></a>Azure Government régió

Ez a régió szabályozott hozzáférést biztosít az Egyesült Államok szövetségi, állami, helyi vagy törzsi entitásai ügyfeleinek, valamint az azok kiszolgálására jogosult partnereinknek. Ön, mint közzétevő, felelős a megfelelőségi ellenőrzésért, a biztonsági intézkedésekért és az ajánlott eljárásokhoz. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).

Azure Government a szolgáltatások bizonyos kormányzati szabályozások és követelmények hatálya alá esnek. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amely leírja azokat. Ezek lehetnek közvetlenül a programra mutató hivatkozások vagy a saját webhelyein való megfelelőség leírására mutató hivatkozások. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

##### <a name="to-select-the-azure-government-region"></a>A Azure Government régió kiválasztása

1. Jelölje be a **Azure Government** jelölőnégyzetet.
1. A **Azure Government minősítések** területen válassza a **+ tanúsítvány hozzáadása lehetőséget (max. 100)**.
1. A megjelenő mezőkben adja meg a tanúsítvány nevét és hivatkozását.
1. Egy másik minősítés hozzáadásához ismételje meg a 2. és a 3. lépést.

A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: Tervezze meg a listát.

## <a name="define-the-plan-listing"></a>A csomag listájának megadása

A **csomag listázása** lapon konfigurálhatja a csomag részleteit. Ezen a lapon láthatók azok a konkrét információk, amelyek az azonos ajánlatban található csomagok különbségeit mutatják. Megadhatja a terv nevét, összegzését és leírását, ahogy szeretné, hogy megjelenjenek a kereskedelmi piactéren.

1. A **terv neve** mezőben a tervhez korábban megadott név jelenik meg. Bármikor megváltoztathatja. Ez a név fog megjelenni a kereskedelmi piactéren az ajánlat szoftvercsomag címeként, és 100 karakterre van korlátozva.
1. A **terv összegzése** mezőben adja meg a csomag rövid összefoglalását (nem az ajánlatot). Ez az Összegzés legfeljebb 100 karakter hosszú lehet.
1. A **terv leírása** mezőben magyarázza el, hogy mi teszi ezt a szoftvercsomagot egyedivé, és hogy az ajánlaton belül más csomagoktól milyen különbségek vannak. Ne írja le az ajánlatot, csak a csomagot. Ez a Leírás legfeljebb 2 000 karaktert tartalmazhat.
1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Tegye a következők egyikét:

- Ha megoldást konfigurál a megoldáshoz, nyissa meg a [megoldási sablon beállítása](create-new-azure-apps-offer-solution.md)című témakört.
- Ha felügyelt alkalmazási csomagot konfigurál, lépjen a [felügyelt alkalmazáscsomag konfigurálása](create-new-azure-apps-offer-managed.md)című témakörre.
