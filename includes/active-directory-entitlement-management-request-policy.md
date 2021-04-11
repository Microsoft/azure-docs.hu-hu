---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 1bbc549a9ba457d5f515df25d6fdce0c2ede6f2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582677"
---
## <a name="for-users-in-your-directory"></a>A címtárban lévő felhasználók számára

Kövesse az alábbi lépéseket, ha engedélyezni szeretné a címtárban lévő felhasználók számára a hozzáférési csomag igénylését. A kérelem szabályzatának meghatározásakor megadhat egyéni felhasználókat vagy gyakrabban felhasználói csoportokat. Előfordulhat például, hogy a szervezet már rendelkezik egy csoporttal, például az **összes alkalmazottal**.  Ha ez a csoport hozzá van adva a szabályzatban azon felhasználók számára, akik hozzáférést igényelhetnek, akkor a csoport bármelyik tagja hozzáférhet a hozzáféréshez.

1. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban lévő felhasználók** elemre.

    Ha ezt a beállítást választja, az új beállítások úgy jelennek meg, hogy tovább pontosítsa, hogy kik a címtárban is igényelhetik ezt a hozzáférési csomagot.

    ![Hozzáférési csomag – kérelmek – a címtárban lévő felhasználók számára](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Válasszon az alábbi lehetőségek közül:

    |  | Leírás |
    | --- | --- |
    | **Adott felhasználók és csoportok** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy csak a címtárban lévő felhasználók és csoportok férhessenek hozzá a hozzáférési csomaghoz. |
    | **Minden tag (a vendégek kivételével)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtárban lévő összes tag felhasználó kérje ezt a hozzáférési csomagot. Ez a lehetőség nem tartalmazza a címtárban esetleg meghívott vendég felhasználókat. |
    | **Minden felhasználó (beleértve a vendégeket is)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtár összes tagja és vendég felhasználója hozzáférhessen a hozzáférési csomaghoz. |

    A vendég felhasználók a címtárban az [Azure ad B2B](../articles/active-directory/external-identities/what-is-b2b.md)használatával meghívott külső felhasználókra vonatkoznak. A felhasználók és a vendég felhasználók közötti különbségekről a [Mi az alapértelmezett felhasználói engedélyek a Azure Active Directoryban?](../articles/active-directory/fundamentals/users-default-permissions.md)című témakörben talál további információt.

1. Ha **meghatározott felhasználókat és csoportokat adott** meg, kattintson a **felhasználók és csoportok hozzáadása** lehetőségre.

1. A felhasználók és csoportok kiválasztása panelen válassza ki a hozzáadni kívánt felhasználókat és csoportokat.

    ![Hozzáférési csomag – kérelmek – felhasználók és csoportok kiválasztása](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. A felhasználók és csoportok hozzáadásához kattintson a **kiválasztás** gombra.

1. Ugorjon a [jóváhagyás](#approval) szakaszra.

## <a name="for-users-not-in-your-directory"></a>A címtárban nem szereplő felhasználók számára

 A **címtárban lévő felhasználók** egy másik Azure ad-címtárban vagy-tartományban lévő felhasználókra hivatkoznak. Előfordulhat, hogy ezek a felhasználók még nem voltak meghívva a címtárba. Az Azure AD-címtárakat úgy kell konfigurálni, hogy az **együttműködési korlátozásokban** engedélyezze a meghívásokat. További információkért tekintse meg a [külső B2B-együttműködés engedélyezése és a vendégek meghívására alkalmas személyek kezelése](../articles/active-directory/external-identities/delegate-invitations.md)című témakört.

> [!NOTE]
> Egy vendég felhasználói fiók jön létre egy olyan felhasználó számára, aki még nem szerepel a címtárban, amelynek a kérelmét jóváhagyták vagy automatikusan jóváhagyták. A vendég meghívásra kerül, de nem kap meghívót e-mailben. Ehelyett e-mailt kapnak a hozzáférési csomag hozzárendelésének megérkezése után. Alapértelmezés szerint később, amikor a vendég felhasználó már nem rendelkezik hozzáférési csomagbeli hozzárendelésekkel, mert az utolsó hozzárendelésük lejárt vagy meg lett szakítva, a vendég felhasználói fiók le lesz tiltva a bejelentkezésből, és azt követően törlődik. Ha azt szeretné, hogy a vendég felhasználók határozatlan ideig maradjanak a címtárban, akkor is, ha nem rendelkeznek hozzáférési csomag hozzárendeléseivel, módosíthatja a jogosultsági felügyeleti konfiguráció beállításait. További információ a vendég felhasználói objektumról: [Azure Active Directory B2B együttműködési felhasználó tulajdonságai](../articles/active-directory/external-identities/user-properties.md).

Kövesse az alábbi lépéseket, ha engedélyezni szeretné a címtárában lévő felhasználók számára a következő hozzáférési csomag kérését:

1. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban nem szereplő felhasználók** elemre.

    Ha ezt a beállítást választja, az új beállítások jelennek meg.

    ![Hozzáférési csomag – kérelmek – a címtárban nem szereplő felhasználók számára](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Válasszon az alábbi lehetőségek közül:

    |  | Leírás |
    | --- | --- |
    | **Adott csatlakoztatott szervezetek** | Akkor válassza ezt a lehetőséget, ha ki szeretné választani a rendszergazda által korábban hozzáadott szervezetek listáját. A kiválasztott szervezetek összes felhasználója kérheti ezt a hozzáférési csomagot. |
    | **Minden csatlakoztatott szervezet** | Akkor válassza ezt a lehetőséget, ha az összes csatlakoztatott szervezet összes felhasználója ezt a hozzáférési csomagot kéri. |
    | **Minden felhasználó (minden csatlakoztatott szervezet + minden új külső felhasználó)** | Akkor válassza ezt a lehetőséget, ha az összes csatlakoztatott szervezet összes felhasználója kérheti ezt a hozzáférési csomagot, és a VÁLLALATKÖZI engedélyezési vagy megtagadási lista beállításai elsőbbséget élveznek az új külső felhasználók számára. |

    A csatlakoztatott szervezet egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van.

1. Ha az **adott csatlakoztatott szervezetek** lehetőséget választotta, kattintson a **címtárak hozzáadása** lehetőségre a rendszergazda által korábban hozzáadott csatlakoztatott szervezetek listájából való kiválasztáshoz.

1. A korábban csatlakoztatott szervezet kereséséhez írja be a név vagy a tartomány nevét.

    ![Hozzáférési csomag – kérelmek – könyvtárak kiválasztása](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Ha a szervezetet, amelyekkel együtt szeretne működni, nem szerepel a listán, megkérheti a rendszergazdát, hogy a hozzá csatlakoztatott szervezetként adja hozzá. További információt a [csatlakoztatott szervezet hozzáadása](../articles/active-directory/governance/entitlement-management-organization.md)című témakörben talál.

1. Miután kiválasztotta az összes csatlakoztatott szervezetet, kattintson a **kiválasztás** elemre.

    > [!NOTE]
    > A kiválasztott csatlakoztatott szervezetek összes felhasználója ezt a hozzáférési csomagot fogja kérni. Ez magában foglalja az Azure AD-beli felhasználókat a szervezethez társított összes altartományból, kivéve, ha ezeket a tartományokat az Azure B2B engedélyezési vagy megtagadási listája blokkolja. További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](../articles/active-directory/external-identities/allow-deny-list.md).

1. Ugorjon a [jóváhagyás](#approval) szakaszra.

## <a name="none-administrator-direct-assignments-only"></a>Nincs (csak a rendszergazdai közvetlen hozzárendelések)

Kövesse az alábbi lépéseket, ha szeretné megkerülni a hozzáférési kérelmeket, és lehetővé teszi, hogy a rendszergazdák közvetlenül rendeljenek hozzá konkrét felhasználókat a hozzáférési csomaghoz. A felhasználóknak nem kell a hozzáférési csomagot igényelnie. Továbbra is beállíthatja az életciklus-beállításokat, de nincsenek a kérelmek beállításai.

1. A **hozzáférést kérő felhasználók** részen kattintson a **nincs (csak rendszergazdai közvetlen hozzárendelések** elemre.

    ![Hozzáférési csomag – kérelmek – nincsenek rendszergazdai közvetlen hozzárendelések](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    A hozzáférési csomag létrehozása után közvetlenül rendelhet hozzá konkrét belső és külső felhasználókat a hozzáférési csomaghoz. Ha külső felhasználót ad meg, a rendszer létrehoz egy vendég felhasználói fiókot a címtárban. A felhasználók közvetlen hozzárendelésével kapcsolatos információkért tekintse meg a [hozzáférési csomag hozzárendelésének megtekintése, hozzáadása és eltávolítása](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)című témakört.

1. Ugorjon le a [kérelmek engedélyezése](#enable-requests) szakaszra.

## <a name="approval"></a>Jóváhagyás

A jóváhagyás szakaszban megadhatja, hogy szükség van-e jóváhagyásra, ha a felhasználók ezt a hozzáférési csomagot kérik. A jóváhagyási beállítások a következő módon működnek:

- Csak az egyik kiválasztott jóváhagyó vagy tartalék jóváhagyónak kell jóváhagynia egy kérést az egyfázisú jóváhagyáshoz. 
- Az egyes fázisok közül csak az egyik kiválasztott jóváhagyónak kell jóváhagynia a 2. szintű jóváhagyásra vonatkozó kérelmet.
- A jóváhagyó lehet felettes, belső szponzor vagy külső szponzor, attól függően, hogy a házirend milyen módon szabályozza a hozzáférést.
- Az egyetlen vagy 2 fázisú jóváhagyáshoz nem szükséges minden kiválasztott jóváhagyó jóváhagyása.
- A jóváhagyási döntés azon alapul, hogy bármelyik jóváhagyó megtekinti-e először a kérést.

Az alábbi videóból megtudhatja, hogyan adhat hozzá jóváhagyókat a kérelmek házirendjéhez:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Az alábbi videóban megtudhatja, hogyan adhat hozzá többfázisú jóváhagyást a kérelmek házirendjéhez:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

A következő lépésekkel adhatja meg a hozzáférési csomagra vonatkozó kérelmek jóváhagyási beállításait:

1. A kiválasztott felhasználóktól érkező kérések jóváhagyásának megköveteléséhez állítsa az **Igen** értékre a **jóváhagyás megkövetelése** kapcsolót. Vagy a kérések automatikus jóváhagyásához állítsa a kapcsolót a **nem** értékre.

1. Ha szeretné megkövetelni, hogy a felhasználók indoklást szolgáltassanak a hozzáférési csomag igényléséhez, állítsa a **kérelmező indoklásának megkövetelése** **beállítást igen** értékre.
    
1. Most állapítsa meg, hogy a kérések csak egyszeres vagy 2 fázisú jóváhagyást igényelnek. Állítsa be, hogy az egyfázisú jóváhagyás hány **szakasza** legyen **1** , vagy a 2. fázis jóváhagyása esetén állítsa a váltást **2** -re.

    ![Hozzáférési csomag – kérelmek – jóváhagyási beállítások](./media/active-directory-entitlement-management-request-policy/approval.png)

A következő lépésekkel adhatja hozzá a jóváhagyókat, miután kiválasztotta, hogy hány lépést kell megadnia: 

### <a name="single-stage-approval"></a>Egyfázisú jóváhagyás

1. Adja hozzá az **első jóváhagyót**:
    
    Ha a házirend úgy van beállítva, hogy a címtárban lévő felhasználók hozzáférését szabályozza, válassza a **felettes jóváhagyóként** lehetőséget. Vagy egy adott felhasználó hozzáadásához kattintson a **Jóváhagyók hozzáadása** lehetőségre, miután kiválasztotta a legördülő menüből a megadott jóváhagyók kiválasztása lehetőséget.
    
    ![Hozzáférési csomag – kérelmek – a címtárbeli felhasználók számára – első jóváhagyó](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Ha a házirend úgy van beállítva, hogy a címtárban nem szereplő felhasználók hozzáférését szabályozza, válassza a **külső szponzor** vagy a **belső szponzor** lehetőséget. Vagy vegyen fel egy adott felhasználót úgy, hogy a **jóváhagyók** vagy csoportok hozzáadása lehetőségre kattint az adott jóváhagyók kiválasztása alatt.
    
    ![Hozzáférési csomag – kérelmek – a címtárban lévő felhasználók számára – első jóváhagyó](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Ha az első jóváhagyóként a **kezelőt** választotta, kattintson a **tartalék hozzáadása** lehetőségre egy vagy több felhasználó vagy csoport tartalék jóváhagyóként való kiválasztásához a címtárban. A tartalék jóváhagyók megkapják a kérést, ha a jogosultságok kezelése nem találja a hozzáférést kérő felhasználó felettesét.

    A kezelőt a jogosultságok kezelése a **Manager** attribútum használatával találja. Az attribútum a felhasználó profiljában szerepel az Azure AD-ben. További információ: [felhasználói profil adatainak hozzáadása vagy frissítése Azure Active Directory használatával](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Ha az **egyes jóváhagyók kiválasztása** lehetőséget választotta, kattintson a **Jóváhagyók hozzáadása** lehetőségre a címtárban lévő felhasználók vagy csoportok jóváhagyásának kiválasztásához.

1. Hány nap múlva **kell** megadnia a jelölőnégyzetet, hogy hány napig kell a jóváhagyónak áttekintenie a hozzáférési csomagra vonatkozó kérelmet.

    Ha egy kérelem nem lett jóváhagyva az adott időszakon belül, a rendszer automatikusan letiltja. A felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz.

1. Ahhoz, hogy a jóváhagyók indoklást szolgáltassanak a döntésük meghozatalához, az **Igen** értékre kell állítani a jóváhagyó indoklását.

    Az indoklás a többi jóváhagyó és a kérelmező számára látható.

### <a name="2-stage-approval"></a>2 – fázis jóváhagyása

Ha kétlépcsős jóváhagyást választott, hozzá kell adnia egy második jóváhagyót.

1. Adja hozzá a **második jóváhagyót**: 
    
    Ha a felhasználók a címtárban vannak, vegyen fel egy adott felhasználót a második jóváhagyóként. ehhez kattintson a **Jóváhagyók hozzáadása** lehetőségre a megadott jóváhagyók területen.

    ![Hozzáférési csomag – kérelmek – a címtárban található felhasználókhoz – második jóváhagyó](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Ha a felhasználók nem a címtárban vannak, válassza a **belső szponzor** vagy **külső szponzor** lehetőséget a második jóváhagyóként. A jóváhagyó kiválasztása után adja hozzá a tartalék jóváhagyókat.

    ![Hozzáférési csomag – kérelmek – a címtáron kívüli felhasználók számára – második jóváhagyó](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Itt adhatja meg, hogy hány nap elteltével kell jóváhagyni a második jóváhagyónak a kérést a mezőben a döntés alapján, **hány nap múlva kell meghoznia?**. 

1. Állítsa be a jóváhagyó indoklásának megkövetelése **beállítást igen** vagy **nem** értékre.

### <a name="alternate-approvers"></a>Alternatív jóváhagyók

Megadhat alternatív jóváhagyókat is, amelyek a kérelmeket jóváhagyó első és második jóváhagyóhoz hasonlóak. Az alternatív jóváhagyók segítségével biztosítható, hogy a kérések jóváhagyása vagy elutasítása előtt lejárjon (időtúllépés). A kétlépcsős jóváhagyáshoz az első jóváhagyót és a második jóváhagyót is listázhatja. 

Alternatív jóváhagyók megadásával abban az esetben, ha az első vagy a második jóváhagyó nem tudta jóváhagyni vagy megtagadni a kérést, a függőben lévő kérést a rendszer a házirend beállítása során megadott továbbítási ütemterv szerint továbbítja a másodlagos jóváhagyóknak. E-mailt kapnak a függőben lévő kérelem jóváhagyásához vagy elutasításához.

A kérésnek a másodlagos jóváhagyóknak való továbbítása után az első vagy a második jóváhagyó továbbra is jóváhagyhatja vagy megtagadhatja a kérelmet. A másodlagos jóváhagyók ugyanazt a saját hozzáférési helyet használják a függőben lévő kérelem jóváhagyásához vagy elutasításához.

Megadhatjuk, hogy mely személyek és csoportok legyenek jóváhagyók, és hogy az alternatív jóváhagyók. Győződjön meg arról, hogy az első, a második és a másodlagos jóváhagyók különböző készleteit sorolja fel.
Ha például az Alice és a Bob, mint az első jóváhagyó (k) szerepel, a Carol és a Dave listázása alternatív jóváhagyóként. A következő lépésekkel adhat hozzá alternatív jóváhagyókat egy hozzáférési csomaghoz:

1. Az első jóváhagyó, második jóváhagyó vagy mindkettő alatt kattintson a **speciális kérelmek beállításainak megjelenítése** lehetőségre.

    ![Hozzáférési csomag – szabályzat – speciális kérelmek beállításainak megjelenítése](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Állítsa be **, hogy nem történt-e művelet, továbbítsa a másodlagos jóváhagyóknak?** váltás az **Igen** értékre.

1. Kattintson az **alternatív Jóváhagyók hozzáadása** lehetőségre, és válassza ki a listából a másodlagos jóváhagyó (ka) t.

    ![Hozzáférési csomag – házirend – alternatív Jóváhagyók hozzáadása](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

    Ha az első jóváhagyóhoz kiválasztja a felettest, akkor a másodlagos jóváhagyó mezőben választhat egy további lehetőséget, a **második szintű felettest** is. Ha ezt a beállítást választja, egy tartalék jóváhagyót kell hozzáadnia a kérelem továbbításához, ha a rendszer nem találja a második szintű kezelőt.

1. A **továbbítás a másodlagos jóváhagyó (ok)** hoz a napok száma után, hogy a jóváhagyóknak hány napig kell jóváhagyni vagy megtagadni a kérést. Ha a kérelem időtartama előtt egyetlen jóváhagyó sem hagyta jóvá vagy nem utasította el a kérelmet, a kérelem lejár (időtúllépés), és a felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz. 

    A kérések továbbítása csak a kérelem időtartamát követő nap után lehetséges, és a fő jóváhagyó (k) döntését legalább 4 nappal később el kell végezni. Ha a kérelem időtúllépése kisebb vagy egyenlő, mint 3, nincs elegendő idő arra, hogy a kérést a másodlagos jóváhagyó (k) számára továbbítsa. Ebben a példában a kérelem időtartama 14 nap. Így a kérelem időtartama a 7. napon eléri a felezési időt. Így a kérés nem továbbítható a 8. napnál korábbi időpontig. A kérések időtartamának utolsó napján nem továbbítható kérelmek. Így a példában a legutóbbi kérelem továbbítása a 13. nap.

## <a name="enable-requests"></a>Kérelmek engedélyezése

1. Ha azt szeretné, hogy a hozzáférési csomag azonnal elérhető legyen a kérési házirendben szereplő felhasználók számára a kérelemhez, helyezze át az engedélyezés kapcsolót **Igen** értékre.

    A jövőben bármikor engedélyezheti azt a hozzáférési csomag létrehozása után.

    Ha a nincs lehetőséget választotta **(csak a rendszergazda közvetlen hozzárendelései)** , és az engedélyezés a **nem** értékre van állítva, akkor a rendszergazdák nem tudják közvetlenül hozzárendelni ezt a hozzáférési csomagot.

    ![Képernyőkép, amely megjeleníti az új kérések és hozzárendelések engedélyezésének lehetőségét.](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Kattintson a **Tovább** gombra.

## <a name="add-requestor-information-preview-to-an-access-package"></a>A kérelmező adatainak (előzetes verzió) hozzáadása egy hozzáférési csomaghoz

1. Lépjen a **kérelmező adatai** lapra, és kattintson a **kérdések** allapra.
 
1. Írja be, hogy mit szeretne kérni a kérelmezőnek, más néven a lekérdezési karakterláncot a **kérdés** mezőben.

    ![Hozzáférési csomag – házirend – a kérelmező adatainak engedélyezése beállítás](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Ha hozzá szeretné adni a saját honosítási beállításait, kattintson a **honosítás hozzáadása** lehetőségre.
    1. Miután a **honosítás hozzáadása a kérdés** panelhez lehetőségre kattint, válassza ki annak a nyelvnek a nyelvi **kódját** , amelyben a kérdést honosítja.
    1. A konfigurált nyelven írja be a kérdést a **honosított szövegmezőbe** .
    1. Miután hozzáadta az összes szükséges honosítást, kattintson a **Mentés** gombra.

    ![Hozzáférési csomag – házirend – honosított szöveg konfigurálása](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Válassza ki a **Válasz formátumát** , amelyben a kérelmezőknek válaszolniuk kell. A válaszok formátuma a következőkből áll: *rövid szöveg*, *több választási lehetőség* és *hosszú szöveg*.
 
    ![Hozzáférési csomag – házirend – több választási választ tartalmazó formátum megtekintése és szerkesztése](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Ha több lehetőséget választ, kattintson a **megtekintés és szerkesztés** gombra a válasz beállításainak konfigurálásához.
    1. A nézet kiválasztása és a **kérdés** szerkesztése ablaktábla megnyitása után megnyílik.
    1. Írja be azokat a válaszokat, amelyeket a kérelmezőnek meg szeretne adni, amikor választ ad a kérdésre a **Válasz értékek** mezőiben.
    1. Annyi választ adjon meg, amennyire szüksége van, majd kattintson a **Mentés** gombra.
    
    ![Hozzáférési csomag – házirend – több választási lehetőség megadása](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Ha szeretné megkövetelni, hogy a kérelmező megválaszolja ezt a kérdést, amikor hozzáférést kér egy hozzáférési csomaghoz, kattintson a **kötelező** jelölőnégyzetre.

1. Kattintson a Next (Tovább) gombra.