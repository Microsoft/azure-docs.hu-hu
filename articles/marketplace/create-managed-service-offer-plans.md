---
title: Csomagok létrehozása a felügyelt szolgáltatási ajánlathoz az Azure Marketplace-en
description: Ismerje meg, hogyan hozhat létre terveket a felügyelt szolgáltatáshoz az Azure Marketplace-en a Microsoft partner Center használatával.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383228"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Csomagok létrehozása a felügyelt szolgáltatási ajánlathoz

A felügyelt szolgáltatásoknak a Microsoft kereskedelmi Piactéren keresztül eladott szolgáltatásainak legalább egy csomaggal kell rendelkezniük. Több különböző csomagot is létrehozhat ugyanazon az ajánlaton belül. Ezek a csomagok (más néven SKU-ként) eltérőek lehetnek a verzió, a monetizálása vagy a szolgáltatási szintek tekintetében. A csomagokkal kapcsolatos részletes útmutatásért lásd: a [kereskedelmi piactéren kínált csomagok és díjszabások](./plans-pricing.md).

## <a name="create-a-plan"></a>Csomag létrehozása

1. A partner Center ajánlatának **terv áttekintés** lapján válassza az **+ új csomag létrehozása** lehetőséget.
2. A megjelenő párbeszédpanelen a **terv azonosítója** területen adjon meg egy egyedi díjcsomag-azonosítót. Legfeljebb 50 kisbetűs alfanumerikus karaktert, kötőjelet vagy aláhúzást használhat. A terv azonosítója a **Létrehozás** gombra kattintva nem módosítható. Ez az azonosító az ügyfelek számára látható lesz.
3. A **terv neve** mezőbe írjon be egy egyedi nevet ehhez a csomaghoz. Legfeljebb 50 karaktert használhat. Ez a név jelenik meg az ügyfelek számára.
4. Válassza a **Létrehozás** lehetőséget.

## <a name="define-the-plan-listing"></a>A csomag listájának megadása

A **csomag listázása** lapon adja meg a csomag nevét és leírását, ahogy szeretné, hogy megjelenjenek a kereskedelmi piactéren.

1. A **terv neve** mező megjeleníti a csomaghoz korábban megadott nevet. Bármikor megváltoztathatja. Ez a név fog megjelenni a kereskedelmi piactéren az ajánlat tervének címén.
2. A **terv összegzése** mezőben adja meg a csomag rövid leírását, amelyet a piactér keresési eredményei között használhat.
3. A **terv leírása** mezőben magyarázza el, hogy mi teszi ezt a csomagot egyedivé és más csomagokon belül.
4. A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="define-pricing-and-availability"></a>A díjszabás és a rendelkezésre állás meghatározása

A felügyelt szolgáltatásokhoz elérhető díjszabási modell a **saját licencét (BYOL)** biztosítja. Ez azt jelenti, hogy az ügyfeleket közvetlenül az ajánlathoz kapcsolódó költségekkel számoljuk fel, és a Microsoft nem számít fel díjat.

Beállíthatja, hogy az egyes tervek mindenki számára (nyilvános) vagy csak egy adott célközönségre (privát) legyenek láthatók.

> [!NOTE]
> A Private-csomagok nem támogatottak a Cloud Solution Provider (CSP) program viszonteladóján keresztül létesített előfizetésekkel.

> [!IMPORTANT]
> Miután közzétett egy csomagot nyilvánosként, nem módosíthatja magánjellegűre. Egy privát csomag használatával szabályozhatja, hogy mely ügyfelek fogadhatják el az ajánlatot, és hogyan delegálhat erőforrásokat. Egy nyilvános csomaggal nem korlátozhatja a rendelkezésre állást bizonyos ügyfelek számára, vagy akár bizonyos számú ügyfél számára is (bár ha úgy dönt, hogy teljesen megszüntetheti a csomag értékesítését). A delegáláshoz való hozzáférést csak akkor távolíthatja el, ha az ügyfél csak akkor fogadja el az ajánlatot, ha az ajánlat közzétételekor a felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét beállította a szerepkör-definícióban. Az ügyfelet is elérheti, és megkérheti, hogy távolítsa el a hozzáférését.

## <a name="make-your-plan-public"></a>A csomag nyilvánosvé tétele

1. A **terv láthatósága** területen válassza a **nyilvános** lehetőséget.
2. Válassza a **Piszkozat mentése** lehetőséget. Ha vissza szeretne térni a terv áttekintése lapra, kattintson a bal felső sarokban található **terv áttekintése** elemre.
3. Ha másik csomagot szeretne létrehozni ehhez az ajánlathoz, válassza az **+ új csomag létrehozása** lehetőséget a **terv áttekintése** lapon.

## <a name="make-your-plan-private"></a>Saját csomag készítése

Az Azure-előfizetési azonosítók használatával hozzáférést biztosíthat egy privát csomaghoz. A használatával legfeljebb 10 előfizetés-azonosítót adhat meg manuálisan vagy akár 10 000 előfizetés-azonosítóhoz. CSV-fájl.

Legfeljebb 10 előfizetési azonosító hozzáadása manuálisan:

1. A **terv láthatósága** területen válassza a **magánjellegű** lehetőséget.
2. Adja meg annak a célközönségnek az Azure-előfizetési AZONOSÍTÓját, amelyhez hozzáférést szeretne biztosítani.
3. Megadhatja a célközönség leírását a **Leírás** mezőben.
4. Másik azonosító hozzáadásához válassza az **azonosító hozzáadása (maximum 10)** lehetőséget.
5. Ha elkészült az azonosítók hozzáadásával, kattintson a **Piszkozat mentése** gombra.

Legfeljebb 10 000 előfizetés-azonosító hozzáadásával a következővel:. CSV-fájl:

1. A **terv láthatósága** területen válassza a **magánjellegű** lehetőséget.
2. Válassza ki a **célközönség exportálása (CSV)** hivatkozást. Ezzel letölti a-t. CSV-fájl.
3. Nyissa meg a t. CSV-fájl. Az **azonosító** oszlopban adja meg azokat az Azure-előfizetési azonosítókat, amelyekhez hozzáférést szeretne biztosítani.
4. A **Leírás**   oszlopban lehetősége van az egyes bejegyzések leírásának hozzáadására.
5. A **Type (típus**   ) oszlopban adja hozzá a **SubscriptionId**   minden olyan sorhoz, amely rendelkezik azonosítóval.
6. Mentse a fájlt. CSV-fájl.
7. A partner Centerben kattintson a **célközönség importálása (CSV)** hivatkozásra.
8. A **megerősítés**   párbeszédpanelen válassza az **Igen**, majd a feltöltés lehetőséget. CSV-fájl.
9. Válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Ez a szakasz egy olyan jegyzékfájlt hoz létre, amely engedélyezési információkat tartalmaz az ügyfelek erőforrásainak kezeléséhez. Ez az információ az Azure-beli [delegált erőforrás-kezelés](../lighthouse/concepts/azure-delegated-resource-management.md)engedélyezéséhez szükséges.

Tekintse át a [bérlőket, a szerepköröket és a felhasználókat az Azure Lighthouse-forgatókönyvekben](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) , amelyekkel megismerheti, hogy mely szerepkörök támogatottak, valamint az engedélyek meghatározásának ajánlott eljárásai.

> [!NOTE]
> Az engedélyezési bejegyzéseiben szereplő felhasználók és szerepkörök minden olyan ügyfélre érvényesek lesznek, aki aktiválja a csomagot. Ha korlátozni szeretné egy adott ügyfél hozzáférését, közzé kell tennie egy privát csomagot a kizárólagos használatra.

### <a name="manifest"></a>Jegyzék

1. A **jegyzékfájl** alatt adja meg a jegyzékfájl **verzióját** . Használja az n. n. n formátumot (például 1.2.5).
2. Adja meg a **bérlői azonosítóját**. Ez a szervezet Azure Active Directory (Azure AD) bérlői azonosítójával társított GUID; Ez azt a bérlőt kezeli, amelyről el fogja érni az ügyfelek erőforrásait. Ha nem rendelkezik ezzel a hasznos megoldással, a fiók neve fölé helyezve a Azure Portal jobb felső sarkában vagy a **címtár váltása** lehetőségre kattintva.

Ha közzéteszi az ajánlat új verzióját, és egy frissített jegyzékfájlt kell létrehoznia, válassza az **+ új jegyzékfájl** lehetőséget. Ügyeljen arra, hogy az előző jegyzékfájl verziószámát növelje.

### <a name="authorizations"></a>Engedélyek

Az engedélyek határozzák meg a felügyeleti bérlő azon entitásait, akik hozzáférhetnek a csomagot megvásárló ügyfelek erőforrásaihoz és előfizetésekhez. Ezek az entitások egy beépített szerepkörhöz vannak hozzárendelve, amely bizonyos hozzáférési szinteket biztosít.

Minden csomaghoz legfeljebb 20 engedélyt hozhat létre.

> [!TIP]
> A legtöbb esetben a szerepköröket egy Azure AD-felhasználói csoporthoz vagy egy egyszerű szolgáltatáshoz szeretné rendelni, nem pedig az egyes felhasználói fiókokhoz. Ez lehetővé teszi az egyes felhasználók hozzáférésének hozzáadását vagy eltávolítását anélkül, hogy a hozzáférési követelmények változásakor frissítenie és újból közzé kellene tennie a tervet. Amikor szerepköröket rendel az Azure AD-csoportokhoz, [a csoport típusának biztonsági és nem Office 365-nek kell lennie](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). További javaslatokért lásd: [bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben](../lighthouse/concepts/tenants-users-roles.md).

Minden egyes engedélyezéshez meg kell adnia a következő információkat. Ezután több felhasználó és szerepkör-definíció hozzáadásához több alkalommal is kiválaszthatja az **Engedélyezés** lehetőséget.

* **HRE objektum azonosítója**: egy felhasználó, felhasználói csoport vagy alkalmazás Azure ad-azonosítója, amely bizonyos engedélyeket kap (a szerepkör definíciója szerint) az ügyfelek erőforrásai számára.
* **HRE-objektum megjelenített neve**: egy rövid név, amely segít az ügyfélnek megérteni az engedélyezés célját. Az ügyfél ezt a nevet fogja látni az erőforrások delegálásakor.
* **Szerepkör-definíció**: válassza ki az elérhető Azure ad beépített szerepkörök egyikét a listából. Ez a szerepkör határozza meg azokat az engedélyeket, amelyeket a felhasználó a **résztvevő azonosító** mezőjében az ügyfelek erőforrásain fog tartalmazni. A szerepkörök leírását lásd: [beépített szerepkörök](../role-based-access-control/built-in-roles.md) és [szerepkör-támogatás az Azure Lighthouse szolgáltatáshoz](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> A megfelelő új beépített szerepkörök az Azure-ba való hozzáadásakor itt lesznek elérhetők. Előfordulhat, hogy néhány késleltetéssel megjelenhet.

* **Hozzárendelhető szerepkörök**: Ez a beállítás csak akkor jelenik meg, ha a **szerepkör-definícióban** a felhasználói hozzáférés rendszergazdája lehetőséget választotta az engedélyezéshez. Ha igen, hozzá kell adnia egy vagy több hozzárendelhető szerepkört. Az **Azure ad-objektum azonosító** mezőjében szereplő felhasználó hozzá tudja rendelni ezeket a szerepköröket a felügyelt identitásokhoz, ami a [szervizelhető házirendek telepítéséhez](../lighthouse/how-to/deploy-policy-remediation.md)szükséges. Erre a felhasználóra nem vonatkozik a felhasználói hozzáférés rendszergazdai szerepkörhöz tartozó egyéb engedélyek.

> [!TIP]
> Ha meg szeretné győződni arról, hogy szükség esetén [el tudja távolítani a hozzáférést a delegáláshoz](../lighthouse/how-to/remove-delegation.md) , adjon **meg egy, a** [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköréhez](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)beállított **szerepkör-definíciót** . Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak egy felhasználó távolíthatja el az ügyfél bérlője számára.

Miután befejezte a csomaghoz tartozó összes szakaszt, további csomagok létrehozásához válassza az **+ új terv létrehozása** lehetőséget. Ha elkészült, válassza a **Piszkozat mentése** a folytatás előtt lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Felülvizsgálat és közzététel](review-publish-offer.md)
