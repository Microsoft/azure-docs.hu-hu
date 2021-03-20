---
title: Azure AD Connect Health – duplikált attribútum-szinkronizálási hibák diagnosztizálása | Microsoft Docs
description: Ez a dokumentum ismerteti a duplikált attribútum-szinkronizálási hibák diagnosztizálási folyamatát, valamint az árva objektumok lehetséges javítását közvetlenül a Azure Portal.
services: active-directory
documentationcenter: ''
author: billmath
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fe6af43c9ca44095c328356e8171da10717875e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98728237"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Duplikált attribútumok szinkronizálási hibáinak diagnosztizálása és javítása

## <a name="overview"></a>Áttekintés
A szinkronizálási hibák kiemelésének egyik lépése a Azure Active Directory (Azure AD) kapcsolódási állapota bevezeti az önkiszolgáló szervizelést. Ezzel elhárítja a duplikált attribútum-szinkronizálási hibákat, és az Azure AD-ből származó árva objektumokat javít.
A diagnosztikai funkció a következő előnyöket nyújtja:
- Olyan diagnosztikai eljárást biztosít, amely leszűkíti a duplikált attribútum-szinkronizálási hibákat. És adott javításokat is biztosít.
- Az Azure AD dedikált forgatókönyvekre vonatkozó javítását alkalmazza, amely egyetlen lépésben elhárítja a hibát.
- A funkció engedélyezéséhez nincs szükség frissítésre vagy konfigurálásra.
Az Azure AD-vel kapcsolatos további információkért lásd: [identitásszinkronizálás és duplikált attribútum rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problémák
### <a name="a-common-scenario"></a>Gyakori forgatókönyv
Ha a **QuarantinedAttributeValueMustBeUnique** és a **AttributeValueMustBeUnique** szinkronizálási hibák történnek, gyakori, hogy a **userPrincipalName** vagy a **proxy címe** ütközik az Azure ad-ben. A szinkronizálási hibák megoldásához frissítse az ütköző forrásoldali objektumot a helyszíni oldalról. A szinkronizálási hiba a következő szinkronizálás után lesz feloldva. Ez a rendszerkép például azt jelzi, hogy két felhasználó ütközik a **userPrincipalName**. Mindkettő **Joe. J \@ contoso.com**. Az ütköző objektumok az Azure AD-ben vannak karanténba helyezve.

![Szinkronizálási hiba diagnosztizálása – gyakori forgatókönyv](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Árva objektum forgatókönyve
Időnként előfordulhat, hogy egy meglévő felhasználó elveszíti a forrás- **horgonyt**. A forrásoldali objektum törlése a helyszíni Active Directoryban történt. A törlési jel változása azonban soha nem lett szinkronizálva az Azure AD-vel. Ez a veszteség olyan esetekben fordul elő, mint a szinkronizálási motorral kapcsolatos problémák vagy a tartomány áttelepítése. Ha ugyanazt az objektumot visszaállítja vagy újra létrehozza, logikailag egy meglévő felhasználónak kell lennie, hogy szinkronizáljon a forrás- **horgonyból**. 

Ha egy meglévő felhasználó csak felhőalapú objektum, az ütköző felhasználót az Azure AD-vel szinkronizálva is megtekintheti. A felhasználó nem egyeztethető össze a meglévő objektum szinkronizálásával. Nincs közvetlen módszer a **forrás-horgony** újrakérésére. További információ a [meglévő Tudásbázisról](https://support.microsoft.com/help/2647098). 

Az Azure AD-beli meglévő objektum például megőrzi a Joe-licencet. Egy olyan újonnan szinkronizált objektum, amely más **forrás-horgonysal** rendelkezik, duplikált attribútum-állapotba kerül az Azure ad-ben. A helyszíni Active Directory Joe-beli módosításait nem alkalmazza a rendszer az Azure AD-ben a Joe-beli eredeti felhasználóra (meglévő objektumra).  

![Szinkronizálási hiba diagnosztizálása árva objektum forgatókönyve](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnosztikai és hibaelhárítási lépések a kapcsolat állapota részben 
A diagnosztizálási funkció a következő duplikált attribútumokkal rendelkező felhasználói objektumokat támogatja:

| Attribútum neve | Szinkronizálási hibák típusai|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> A funkció eléréséhez **globális rendszergazdai** engedély vagy **közreműködői** engedély szükséges az Azure RBAC.
>

Kövesse a Azure Portal lépéseit, és Szűkítse le a szinkronizálási hiba részleteit, és adjon meg konkrétabb megoldásokat:

![Szinkronizálási hibák diagnosztizálásának lépései](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

A Azure Portal hajtson végre néhány lépést az egyes javítható helyzetek azonosításához:  
1.  Keresse meg a **Diagnosztizálás állapota** oszlopot. Az állapot azt mutatja, hogy lehetséges-e a szinkronizálási hiba kijavítása közvetlenül a Azure Active Directoryról. Ez azt jelenti, hogy létezik egy hibaelhárítási folyamat, amely leszűkítheti a hiba esetét, és esetleg javíthatja azt.

| Állapot | Mit jelent? |
| ------------------ | -----------------|
| Nincs elindítva | Nem látogatta meg ezt a diagnosztikai folyamatot. A diagnosztikai eredményektől függően lehetőség van arra, hogy a szinkronizálási hibát közvetlenül a portálról javítsa ki. |
| Manuális javítás szükséges | A hiba nem felel meg a portálon elérhető javítások feltételeinek. Az ütköző objektumtípusok nem a felhasználók, vagy már elvégezte a diagnosztikai lépéseket, és a portálon nem érhető el javítás. Az utóbbi esetben a helyszíni oldalról származó javítás még mindig az egyik megoldás. [További információ a helyszíni javításokról](https://support.microsoft.com/help/2647098). | 
| Szinkronizálás függőben | Egy javítás lett alkalmazva. A portál a következő szinkronizálási ciklusra vár a hiba törléséhez. |

  >[!IMPORTANT]
  > A diagnosztikai állapot oszlop az egyes szinkronizálási ciklusok után alaphelyzetbe áll. 
  >

1. Kattintson a **diagnosztika** gombra a hiba részletei alatt. Válaszoljon néhány kérdésre, és azonosítsa a szinkronizálási hiba részleteit. A kérdésekre adott válaszok segítenek azonosítani az árva objektum esetét.

1. Ha a diagnosztika végén megjelenik egy **Bezárás** gomb, a válaszok alapján nem érhető el gyors javítás a portálon. Tekintse át az utolsó lépésben megjelenő megoldást. A helyszíni javítások még a megoldások. Kattintson a **Bezárás** gombra. Az aktuális szinkronizálási hiba állapota **kézi javításra** vált. Az állapot az aktuális szinkronizálási ciklusban marad.

1. Az árva objektumokra vonatkozó esetek azonosítása után a duplikált attribútumok szinkronizálási hibáit közvetlenül a portálról javíthatja. A folyamat elindításához kattintson a **javítás alkalmazása** gombra. Az aktuális szinkronizálási hiba állapota **függőben lévő szinkronizálásra** frissül.

1. A következő szinkronizálási ciklus után el kell távolítani a hibát a listából.

## <a name="how-to-answer-the-diagnosis-questions"></a>A diagnosztikai kérdések megválaszolása 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Létezik a felhasználó a helyszíni Active Directory?

Ez a kérdés megpróbálja azonosítani a meglévő felhasználó forrás objektumát a helyszíni Active Directory.  
1. Ellenőrizze, hogy Azure Active Directory rendelkezik-e a megadott **userPrincipalName** rendelkező objektummal. Ha nem, válaszoljon a **nem** elemre.
2. Ha igen, ellenőrizze, hogy az objektum továbbra is a szinkronizálási hatókörben van-e.  
   - Keressen rá az Azure AD-összekötő területére a DN használatával.
   - Ha az objektum a **függőben lévő hozzáadási** állapotban található, válaszoljon a **nem** értékre. Azure AD Connect nem tud csatlakozni az objektumhoz a megfelelő Azure AD-objektumhoz.
   - Ha az objektum nem található, válasz **Igen**.

Ezekben a példákban a kérdés megpróbálja megállapítani, hogy a **Joe Jackson** továbbra is létezik-e a helyszíni Active Directoryban.
A **gyakori forgatókönyv** esetén a **Joe Johnson** és a **Joe Jackson** egyaránt megtalálható a helyszíni Active Directoryban. A karanténba helyezett objektumok két különböző felhasználó.

![Szinkronizálási hiba diagnosztizálása – gyakori forgatókönyv](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Az **árva objektum** esetében csak az egyetlen felhasználó, **Joe Johnson** van jelen a helyszíni Active Directoryban:

![Szinkronizálási hiba diagnosztizálása árva objektum * a felhasználó létezése * forgatókönyv](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Mindkét fiók ugyanahhoz a felhasználóhoz tartozik?
Ez a kérdés egy bejövő ütköző felhasználót és az Azure AD meglévő felhasználói objektumát ellenőrzi, hogy a felhasználók ugyanahhoz a felhasználóhoz tartoznak-e.  
1. Az ütköző objektum új szinkronizálása Azure Active Directory. Az objektumok attribútumainak összehasonlítása:  
   - Megjelenítendő név
   - Felhasználó egyszerű neve
   - Objektumazonosító
2. Ha az Azure AD nem tudja összehasonlítani őket, ellenőrizze, hogy Active Directory rendelkezik-e a megadott **UserPrincipalNames** rendelkező objektumokkal. Válasz **nem** , ha mindkettőt keresi.

A következő példában a két objektum ugyanahhoz a felhasználóhoz tartozik, **Joe Johnson**.

![A szinkronizálási hibák diagnosztizálása árva objektum * ugyanaz a felhasználó * forgatókönyv](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Mi történik a javítás alkalmazása után az árva objektum esetén
Az előző kérdésekre adott válaszok alapján a **javítás alkalmazása** gombra kattintva megtekintheti az Azure ad-ből elérhető javításokat. Ebben az esetben a helyszíni objektum egy váratlan Azure AD-objektummal van szinkronizálva. A két objektum a **forrás-horgony** használatával van leképezve. A **hibajavítás alkalmazása** a következő vagy hasonló lépéseket hajtja végre:
1. Frissíti a **forrás-horgonyt** a megfelelő objektumra az Azure ad-ben.
2. Törli az ütköző objektumot az Azure AD-ben, ha a jelen van.

![Szinkronizálási hiba diagnosztizálása a javítás után](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> A **javítási módosítások alkalmazása** csak az árva objektumokra vonatkozik.
>

Az előző lépések után a felhasználó elérheti az eredeti erőforrást, amely egy meglévő objektumra mutató hivatkozás. A listában lévő **állapot diagnosztizálása** **függőben lévő szinkronizálásra** frissül. A szinkronizálási hiba a következő szinkronizálás után lesz feloldva. A kapcsolat állapota többé nem jelenik meg a megoldott szinkronizálási hiba a listanézetban.

## <a name="failures-and-error-messages"></a>Hibák és hibaüzenetek
**Az ütköző attribútummal rendelkező felhasználó nem törölhető a Azure Active Directory. Győződjön meg arról, hogy a felhasználó nehezen törölve van az újrapróbálkozás előtt.**  
Az Azure AD-ben ütköző attribútummal rendelkező felhasználót meg kell tisztítani a javítás alkalmazása előtt. Tekintse meg [, hogyan törölheti a felhasználót véglegesen az Azure ad-ben](../fundamentals/active-directory-users-restore.md) a javítás megkísérlése előtt. A felhasználó automatikusan törölve lesz, és a rendszer véglegesen törli a törölt állapotot 30 nap után is. 

**A forrás-és a bérlőn lévő felhőalapú felhasználóra történő frissítés nem támogatott.**  
Az Azure AD-beli felhőalapú felhasználónak nem lehet forrás-horgonya. Ebben az esetben a forrás-horgony frissítése nem támogatott. A helyszínen manuális javítást kell végezni. 

**A javítási folyamat nem tudta frissíteni az értékeket.**
A [Azure ad Connectban](./how-to-connect-preview.md#user-writeback) megadott beállítások (például a UserWriteback) nem támogatottak. Tiltsa le a beállításokat a beállításokban. 

## <a name="faq"></a>GYIK
**K.** Mi történik, ha az **Apply javítás** végrehajtása sikertelen?  
**Egy.** Ha a végrehajtás meghiúsul, lehetséges, hogy Azure AD Connect exportálási hibát futtat. Frissítse a portál oldalt, és próbálkozzon újra a következő szinkronizálás után. Az alapértelmezett szinkronizálási ciklus 30 perc. 


**Q.** Mi a teendő, ha a **meglévő objektum** a törlendő objektum?  
**Egy.** Ha a **meglévő objektumot** törölni kell, a folyamat nem vonja maga után a **forrás-horgony** változását. Általában a helyszíni Active Directory javíthatja. 


**Q.** Milyen engedélyre van szükség a felhasználónak a javítás alkalmazásához?  
**Egy.** Az Azure RBAC **globális rendszergazdája** vagy **közreműködője** jogosult a diagnosztikai és hibaelhárítási folyamat elérésére.


**Q.** Be kell állítania Azure AD Connect vagy frissítenie kell a Azure AD Connect Health ügynököt ehhez a szolgáltatáshoz?  
**Egy.** Nem, a diagnosztikai folyamat egy teljes felhőalapú szolgáltatás.


**Q.** Ha a meglévő objektum nem törölhető, akkor a diagnosztika folyamata ismét aktív lesz?  
**Egy.** Nem, a javítás nem frissíti az objektum attribútumait a **forrás-horgonytól** eltérő értékkel.