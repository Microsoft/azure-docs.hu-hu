---
title: A felhasználók nincsenek kiépítve az alkalmazásban
description: Gyakori hibák elhárítása, ha nem jelenik meg a felhasználók számára az Azure AD-vel való üzembe helyezéshez konfigurált Azure AD Gallery-alkalmazásban megjelenő felhasználó
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 429f993764273d6b6eb23d75d0132b374d5d0ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256354"
---
# <a name="no-users-are-being-provisioned"></a>Egyetlen felhasználó létrehozása sem történt meg 
>[!NOTE]
>A 04/16/2020-es naptól kezdve módosult az alapértelmezett hozzáférési szerepkörhöz rendelt felhasználók viselkedése. A részletekért tekintse meg az alábbi szakaszt. 
>
Miután az automatikus kiépítés konfigurálva lett egy alkalmazáshoz (beleértve annak ellenőrzését, hogy az Azure AD-hoz az alkalmazáshoz való kapcsolódáshoz megadott hitelesítő adatok érvényesek-e), a felhasználók és/vagy csoportok pedig az alkalmazáshoz vannak kiépítve. Az üzembe helyezést a következő dolgok határozzák meg:

-   Mely felhasználók és csoportok lettek **hozzárendelve** az alkalmazáshoz. Vegye figyelembe, hogy a beágyazott csoportok kiépítés nem támogatott. További információ a hozzárendelésről: [felhasználó vagy csoport hozzárendelése vállalati alkalmazásokhoz Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Az attribútum- **hozzárendelések** engedélyezve vannak-e, és úgy vannak konfigurálva, hogy az Azure ad-ből az alkalmazáshoz való érvényes attribútumokat szinkronizálják. Az attribútumok hozzárendelésével kapcsolatos további információkért lásd: a [felhasználói kiépítési attribútumok leképezésének testreszabása az SaaS-alkalmazásokhoz Azure Active Directoryban](customize-application-attributes.md).
-   Azt határozza meg, hogy van-e olyan **hatóköri szűrő** , amely adott attribútumérték alapján szűri a felhasználókat. A szűrők hatókörével kapcsolatos további információkért lásd: [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Ha azt észleli, hogy a felhasználók nincsenek kiépítve, tekintse meg az Azure AD-beli [kiépítési naplókat (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) . Egy adott felhasználó bejegyzéseinek keresése.

A Azure Portal kiépítési naplóit a  &gt; tevékenység szakaszban Azure Active Directory **vállalati alkalmazások** &gt; **kiépítési naplói (előzetes verzió)**  lehetőség kiválasztásával érheti el. A kiépítési adat a felhasználó neve vagy a forrásrendszer vagy a célként megadott rendszer alapján is megkereshető. Részletekért lásd: [kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

A kiépítési naplók rögzítik a kiépítési szolgáltatás által végrehajtott összes műveletet, beleértve az Azure AD lekérdezését olyan hozzárendelt felhasználók számára, akik a kiépítés hatókörét képezik, lekérdezik a felhasználók létezését, és összehasonlítják a rendszer közötti felhasználói objektumokat. Ezt követően az összehasonlítás alapján hozzáadhatja, frissítheti vagy letilthatja a felhasználói fiókot a megcélzott rendszeren.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Általános problémás területek az üzembe helyezéssel
Az alábbi lista az általános problémás területeket sorolja fel, amelyeket részletesen bemutathat, ha van ötlete, hogy hol kezdjen hozzá.

- [A kiépítési szolgáltatás nem jelenik meg a Start menüben](#provisioning-service-does-not-appear-to-start)
- [A naplók kiosztása azt jelenti, hogy a felhasználók kimaradnak, és nincsenek kiépítve, még akkor is, ha hozzájuk vannak rendelve](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>A kiépítési szolgáltatás nem jelenik meg a Start menüben
Ha úgy állítja be a **kiépítési állapotot** , hogy be legyen **kapcsolva** a Azure Portal **Azure Active Directory &gt; vállalati alkalmazások &gt; \[ alkalmazásának neve \] &gt; kiépítés** szakaszában. A további állapotok azonban nem jelennek meg az oldalon az újratöltést követően, ezért valószínű, hogy a szolgáltatás fut, de még nem hajtotta végre a kezdeti ciklust. Ellenőrizze a fentiekben ismertetett **kiépítési naplókat (előzetes verzió)** , és állapítsa meg, hogy milyen műveleteket végez a szolgáltatás, és hogy vannak-e hibák.

>[!NOTE]
>Egy kezdeti ciklus akár 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. A következő szinkronizálások gyorsabbak lesznek, mivel a kiépítési szolgáltatás a kezdeti ciklus után mindkét rendszer állapotát jelképező vízjeleket tárolja. A kezdeti ciklus javítja a későbbi szinkronizálások teljesítményét.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>A naplók kiosztása azt jelenti, hogy a felhasználók kimaradnak, és nem lettek kiépítve annak ellenére, hogy hozzájuk vannak rendelve

Ha egy felhasználó "kihagyva" állapotba kerül a kiépítési naplókban, fontos, hogy a napló **lépések** lapján ellenőrizze az okot. Az alábbi gyakori okok és megoldások:

- **Egy hatókör-szűrő konfigurálva** **van, amely egy attribútumérték alapján szűri ki a felhasználót**. A szűrők hatókörével kapcsolatos további információkért lásd: [hatóköri szűrők](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **A felhasználó "nem lesz ténylegesen jogosult".** Ha ezt a hibaüzenetet látja, az azért van, mert probléma van az Azure AD-ben tárolt felhasználói hozzárendelési rekorddal. A probléma megoldásához szüntesse meg a felhasználó (vagy csoport) hozzárendelését az alkalmazásból, majd ismét rendelje hozzá újra. További információ a hozzárendelésről: [felhasználók vagy csoportok hozzáférésének hozzárendelése](../manage-apps/assign-user-or-group-access-portal.md).
- **Egy kötelező attribútum hiányzik vagy nincs feltöltve a felhasználó számára.** A kiépítés beállításakor megfontolandó szempont, hogy áttekintse és konfigurálja azokat az attribútum-hozzárendeléseket és munkafolyamatokat, amelyek meghatározzák, hogy mely felhasználói (vagy csoport-) tulajdonságokat kell az Azure AD-ből az alkalmazásba áthelyezni. Ez a konfiguráció magában foglalja a "megfelelő tulajdonság" beállítását, amely a felhasználók és csoportok egyedi azonosítására és a két rendszer közötti egyeztetésére szolgál. A fontos folyamattal kapcsolatos további információkért lásd: a [felhasználói kiépítési attribútumok leképezésének testreszabása az SaaS-alkalmazásokhoz Azure Active Directoryban](customize-application-attributes.md).
- **Csoportok attribútumainak hozzárendelése:** A csoport neve és a csoport adatainak kiépítés a tagokon kívül, ha egyes alkalmazások esetében támogatott. A funkció engedélyezéséhez vagy letiltásához engedélyezze vagy tiltsa le a **kiépítés** lapon megjelenő csoportosítási objektumok **leképezését** . Ha a létesítési csoportok engedélyezve vannak, tekintse át az attribútumok leképezéseit, és győződjön meg arról, hogy a megfelelő mező használatban van a "megfeleltetési azonosító" beállításnál. A megfelelő azonosító lehet a megjelenítendő név vagy az e-mail-alias. A csoport és a tagjai nincsenek kiépítve, ha a megfelelő tulajdonság üres vagy nincs feltöltve egy csoportra az Azure AD-ben.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Az alapértelmezett hozzáférési szerepkörhöz hozzárendelt felhasználók kiépítés
A katalógus egyik alkalmazásának alapértelmezett szerepkörét az "alapértelmezett hozzáférés" szerepkörnek nevezzük. Az ehhez a szerepkörhöz hozzárendelt felhasználókat nem lehet kiépíteni, és a [kiépítési naplókból](../reports-monitoring/concept-provisioning-logs.md) kihagyva vannak megjelölve, mivel "nem lesz ténylegesen jogosult". 

**A 04/16/2020 után létrehozott kiépítési konfigurációk viselkedése:** Az alapértelmezett hozzáférési szerepkörhöz hozzárendelt felhasználók ugyanúgy lesznek kiértékelve, mint az összes többi szerepkör. Az alapértelmezett hozzáféréshez hozzárendelt felhasználó nem lesz kihagyva "nem megfelelő jogosultsággal". 

**A 04/16/2020 előtt létrehozott kiépítési konfigurációk viselkedése:** A következő 3 hónapban a viselkedés továbbra is a mai napig folytatódik. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók kimaradnak, és nem lesznek ténylegesen jogosultak. Július 2020 után a viselkedés minden alkalmazás esetében egységes lesz. Nem fogjuk kihagyni a felhasználókat az alapértelmezett hozzáférési szerepkörrel, mert "nem lehet ténylegesen jogosult". Ezt a módosítást a Microsoft végzi, és nincs szükség felhasználói beavatkozásra. Ha szeretné biztosítani, hogy ezek a felhasználók továbbra is kimaradnak, még a módosítás után is alkalmazza a megfelelő hatókörű szűrőket, vagy szüntesse meg a felhasználó hozzárendelését az alkalmazásból, hogy azok ne legyenek a hatókörön belül.  

A változásokkal kapcsolatos kérdéseit a következő címen érheti el: provisioningfeedback@microsoft.com
## <a name="next-steps"></a>Következő lépések

[Azure AD Connect szinkronizálás: a deklaratív kiépítés ismertetése](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
