---
title: Cloud Solution Provider – Microsoft kereskedelmi piactér
description: Ismerje meg, hogyan értékesítheti ajánlatait a kereskedelmi piactéren a Microsoft Cloud Solution Provider (CSP) program partneri csatornáján keresztül.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93131173"
---
# <a name="cloud-solution-provider-program"></a>Felhőszolgáltató program

Ez a cikk azt ismerteti, hogyan konfigurálható az ajánlat, hogy elérhető legyen a Cloud Solution Provider (CSP) program számára. Az ajánlatoknak a [kereskedelmi piactér online áruházakon](overview.md#commercial-marketplace-online-stores)keresztüli közzétételén kívül a CSP programon keresztül is elérhetővé teheti, hogy a program által kiszolgált, több millió minősített Microsoft-ügyfelet is elérhet.

Az új vagy meglévő ajánlatokat a CSP programban is konfigurálhatja, amely lehetővé teszi, hogy a CSP-partnerek eladják termékeiket, és csomagolt megoldásokat hozzanak létre az ügyfeleknek.

A kiadók feladata, hogy a végfelhasználók szüntesse meg a hibajavítást, és hogy a CSP-programban és/vagy az ügyfeleknél olyan mechanizmust biztosítson, amely segítséget nyújt a partnereknek. Az ajánlott eljárás az, hogy partnereket biztosítson a CSP programban a felhasználói dokumentációval, a képzéssel és a szolgáltatás állapotával/KIMARADÁSÁVAL kapcsolatos értesítésekkel (ha vannak ilyenek), hogy a CSP programban részt vevő partnerek az 1. szintű támogatási kérések kezelésére alkalmasak legyenek.  

A következő ajánlatok jogosultak arra, hogy a partnerek által a CSP programban való értékesítésre legyenek feljogosítva:

- Szolgáltatott szoftveres (SaaS) Transact-ajánlatok
- Virtual Machines (VM)
- Megoldássablonok
- Felügyelt alkalmazások

> [!NOTE]
> A tárolók és a saját licencek (BYOL) virtuálisgép-csomagjait úgy kell megadni, hogy alapértelmezés szerint a kriptográfiai szolgáltatói programban lévő partnerek is eladják azokat.

## <a name="how-to-configure-an-offer"></a>Ajánlat konfigurálása

Az ajánlat a partner Centerben való létrehozásakor adja meg a CSP-program opt-in beállítását. [További információ a kiadói élmény megváltoztatásáról](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Partneri központ – opt

A bejelentkezés a CSP viszonteladói célközönség modul alatt található:

![CSP viszonteladói közönség](media/marketplace-publishers-guide/csp-reseller-audience.png)

Három lehetőség közül választhat:

1. Bármely partner a CSP programban.
2. A CSP programban kiválasztott partnerek.
3. Nem található partner a CSP programban.

#### <a name="option-1-any-partner-in-the-csp-program"></a>1. lehetőség: bármely partner a CSP programban

![A Felhőszolgáltató programban részt vevő bármely partner](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ha ezt a lehetőséget választja, a CSP program összes partnere jogosult az ajánlatának viszonteladására az ügyfelek számára.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>2. lehetőség: meghatározott partnerek a CSP programban Kiválasztom

![A CSP programban kiválasztott partnerek](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ennek a lehetőségnek a kiválasztásával engedélyezheti, hogy a CSP program mely partnerei jogosultak az ajánlat viszonteladására.

Ha engedélyezni szeretné a partnereket, válassza a **CSP-partnerek kiválasztása** lehetőséget, és egy olyan menü jelenik meg, amely lehetővé teszi a partner neve vagy a CSP Azure Active Directory (Azure ad) BÉRLŐi azonosítójának keresését.

![CSP menü kiválasztása](media/marketplace-publishers-guide/csp-pop-up-module.png)

Keresési szűrőket alkalmazhat, például **országokat**, **kompetenciákat** vagy **képességeket**.

![Partneri keresés országa/régiója, kompetenciája és képzettségi szűrői](media/marketplace-publishers-guide/csp-add-resellers.png)

Miután kiválasztotta a partnerek listáját, válassza a **Hozzáadás** lehetőséget.

![A KRIPTOGRÁFIAi programban a jóváhagyott partnerek listája](media/marketplace-publishers-guide/csp-add-resellers-details.png)

A kiválasztott partnerek listáját megjelenítő tábla megjelenik a CSP-viszonteladó célközönsége lapon.

![A CSP viszonteladói célközönségének oldalán található partnerek listáját tartalmazó tábla](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

A módosítások regisztrálásához kattintson a **Piszkozat mentése** gombra.

Ha ez az ajánlat közzé van téve, közzé kell tennie az ajánlatot, hogy elérhetővé tegye a kiválasztott partnerei számára.

>[!NOTE]
>Ha egy adott régióban engedélyez egy partnert a CSP programban, az ajánlatot az adott régióhoz tartozó bármely ügyfélnek is értékesítheti. A CSP-ajánlatok régiók szerinti besorolásáról további információt a [Cloud Solution Provider program regionális piacai és pénzneme](/partner-center/regional-authorization-overview)című témakörben talál.

Ha egy már közzétett ajánlat CSP-listáját frissíti, vegye fel a további partnereket, és válassza a **szinkronizálás CSP-célközönség** lehetőséget.

Ha van olyan ajánlata, amely már rendelkezik a jogosult partnerek listájával, és egy másik ajánlathoz ugyanazt a listát szeretné használni, használja az **Importálás/exportálás** lehetőséget. Navigáljon a CSP-listát tartalmazó ajánlathoz, és válassza a **kriptográfiai szolgáltatók exportálása** lehetőséget. A függvény fejleszt egy. csv-fájlt, amely importálható egy másik ajánlatba.

#### <a name="option-3-no-partners-in-the-csp-program"></a>3. lehetőség: nincsenek partnerek a CSP programban

![Egyetlen partner sem a Felhőszolgáltató programban](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ha ezt a lehetőséget választja, az ajánlatát a CSP programból választhatja ki. Ezt a lehetőséget bármikor módosíthatja.

## <a name="deauthorize-partners-in-the-csp-program"></a>Partnerek dehatalmazása a CSP programban

Ha engedélyezte a partnert a CSP programban, és a partner már eladta a terméket az ügyfeleknek, akkor nem engedélyezheti a partnert.

Ha a CSP program egyik partnere nem adta meg a terméket az ügyfeleknek, és az ajánlat közzétételét követően el szeretné távolítani a CSP-t, kövesse az alábbi utasításokat:

1. Lépjen a [support Request lapra](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése** lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése** lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.

## <a name="navigate-between-options"></a>Navigáljon a beállítások között

Ebben a szakaszban a három CSP-viszonteladói lehetőség közül lehet navigálni.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navigáljon a (z) lehetőség közül: bármelyik partner a CSP programban

Ha az ajánlat jelenleg **1. lehetőség: bármely partner a CSP programban** , és a másik két lehetőség közül bármelyiket el szeretné érni, a következő utasításokat követve hozhat létre egy kérést:

1. Lépjen a [support Request lapra](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése** lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése** lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.

> [!NOTE]
> Ha a két lehetőségre próbál navigálni, és a CSP-program egyik partnere már eladta az ajánlatot az ügyfelek számára, akkor a partner alapértelmezés szerint már szerepel a jóváhagyott partnerek listáján.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navigáljon a második lehetőség közül: válassza ki az adott partnereket a CSP programban.

Ha az ajánlat jelenleg **2. lehetőség: a CSP programban kiválasztott partnerek közül választhatok** , és az egyiket szeretné megkeresni **: bármely partner a CSP programban**, az alábbi utasításokat követve hozzon létre egy kérést:

1. Lépjen a [support Request lapra](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése** lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése** lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.

 Ha az ajánlat jelenleg **2. lehetőség: a CSP programban kiválasztott partnerek közül választhatok** , és a **3. lehetőségre szeretne navigálni: nem található partner a CSP programban**, csak akkor fog tudni erre a lehetőségre navigálni, ha a CSP-program korábban már engedélyezte a partnereket, hogy a végfelhasználóknak ne eladják az ajánlatot. Kérelem létrehozásához kövesse az alábbi utasításokat:

1. Lépjen a [support Request lapra](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése** lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése** lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navigáljon a 3. lehetőséghez: nincs partner a CSP programban

Ha az ajánlat jelenleg **3. lehetőség: nincs partner a CSP programban**, bármikor megnyithatja a másik két lehetőség valamelyikét.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Értékesítési és támogatási anyagok megosztása partnerekkel a CSP programban

Ahhoz, hogy a partnerek a felhőalapú megoldás-szolgáltatói programban leghatékonyabban képviseljék az ajánlatot, és a szervezete is részt vesz, be kell nyújtania a viszonteladók számára elérhető értékesítési és támogatási anyagokat. Ezek az erőforrások nem lesznek elérhetők az online áruházakban lévő ügyfelek számára.

### <a name="partner-center-csp-channel"></a>Partneri központ CSP-csatornája

Ha kiválasztotta a CSP-csatornát a partner Centerben, a kiadónak meg kell adnia egy URL-címet, amely a megfelelő marketinganyagok és csatorna elérhetőségi adatait tárolja az ajánlati lista modulban.

![A partner Center CSP-biztosítékokkal kapcsolatos információi](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Következő lépések

- További információ a [piaci megjelenéssel kapcsolatos szolgáltatásokról](https://partner.microsoft.com/reach-customers/gtm).
- Az ajánlat létrehozásához és konfigurálásához jelentkezzen be a [partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .