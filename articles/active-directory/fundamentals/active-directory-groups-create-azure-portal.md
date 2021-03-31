---
title: Hozzon létre egy alapszintű csoportot, és vegyen fel tagokat – Azure Active Directory | Microsoft Docs
description: Útmutató egy alapszintű csoport létrehozásához a Azure Active Directory használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8041656a11689d0cca64c3edae1af2e70409028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97027713"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Hozzon létre egy alapszintű csoportot, és vegyen fel tagokat Azure Active Directory használatával
Az Azure Active Directory (Azure AD) portálon létrehozhat alapszintű csoportot. A cikk céljaira az erőforrás tulajdonosa (rendszergazda) egy alapszintű csoportot ad az erőforráshoz, amely azokat a tagokat tartalmazza (alkalmazottak), akiknek az adott erőforrást el kell érniük. Az összetettebb eseteknél, beleértve a dinamikus csoporttagságot és szabályok létrehozását is, lásd az [Azure Active Directory felhasználókezelés dokumentációt](../enterprise-users/index.yml).

## <a name="group-and-membership-types"></a>Csoport-és tagsági típusok
Több csoport-és tagsági típus van. Az alábbi információk ismertetik az egyes csoportok és tagságok típusát, valamint a használatuk okát, hogy segítsen eldönteni, hogy mely beállításokat kell használni a csoportok létrehozásakor.

### <a name="group-types"></a>Csoportok típusai:
- **Biztonság**. Segítségével kezelheti a tag és a számítógép hozzáféréseket a megosztott erőforrásokhoz a felhasználók egy csoportjára. Például létrehozhat egy biztonsági csoportot egy meghatározott biztonsági házirendhez. Ha így tesz, az összes tagnak egyszerre oszthatja ki az engedélyeket, nem kell az engedélyeket minden tagnak külön kiadni. Egy biztonsági csoport tagjainak, eszközeinek, csoportjainak és egyszerű szolgáltatásainak tulajdonosaként a felhasználók, a felhasználók és a szolgáltatások lehetnek. Erőforrásokhoz való hozzáférés kezelésével kapcsolatos további információkért lásd: [Erőforrásokhoz való hozzáférés kezelése Azure Active Directory-csoportokkal](active-directory-manage-groups.md).
- **Microsoft 365**. Együttműködési lehetőségeket biztosít a tagok számára rendelkezésre bocsátott megosztott postaládába, naptár, fájlok, SharePoint-webhelyre és sok egyéb révén. Ez a lehetőség lehetővé teszi a szervezetnél kívüli személyek hozzáadását is a csoporthoz. Egy Microsoft 365 csoport tagjai csak felhasználók lehetnek. A felhasználók és az egyszerű szolgáltatások is lehetnek Microsoft 365 csoport tulajdonosainak. Microsoft 365 csoportokkal kapcsolatos további információkért lásd: [Microsoft 365 csoportok megismerése](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Tagsági típusok:
- **Rendelt.** Lehetővé teszi, hogy konkrét felhasználók a csoport tagjai lehessenek és egyedi engedélyekkel rendelkezzenek. A cikk céljaira ezt a lehetőséget használjuk.
- **Dinamikus felhasználó.** Lehetővé teszi a dinamikus tagsági szabályok használatát a tagok automatikus hozzáadásához és eltávolításához. Ha a tag attribútumai változnak, a rendszer megvizsgálja a címtárra vonatkozó dinamikus csoportszabályokat, hogy a tag megfelel-e a szabály követelményeinek (hozzáadja), vagy már nem felel meg a szabályok szerinti követelményeknek (eltávolítja).
- **Dinamikus eszköz.** Lehetővé teszi a dinamikus csoport szabályok segítségével a távoli eszközök automatikus felvételét és eltávolítását. Ha az eszköz attribútumai változnak, a rendszer megvizsgálja a címtárra vonatkozó dinamikus csoportszabályokat, hogy az eszköz megfelel-e a szabály követelményeinek (hozzáadja), vagy már nem felel meg a szabályok szerinti követelményeknek (eltávolítja).

    > [!IMPORTANT]
    > Dinamikus csoportot létre lehet hozni eszközök és tagok számára is, de egyszerre mindkettőhöz nem. Nem lehet eszköz csoportot létrehozni az eszköz tulajdonosok attribútumai alapján sem. Eszköz tagsági szabályok csak eszköz attribútumokra hivatkozhatnak. A dinamikus csoportok felhasználókhoz és eszközökhöz való létrehozásával kapcsolatos további információkért lásd: [dinamikus csoport létrehozása és állapotának ellenõrzése](../enterprise-users/groups-create-rule.md)

## <a name="create-a-basic-group-and-add-members"></a>Alapszintű csoport létrehozása és tagok hozzáadása
Az alapszintű csoport létrehozása és a tagok felvétele történhet egyszerre. Alapszintű csoport létrehozásához és tagok hozzáadásához kövesse az alábbi eljárást:

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Keresse meg és válassza ki az **Azure Active Directoryt**.

1. A **Active Directory** lapon válassza a **csoportok** lehetőséget, majd válassza az **új csoport** lehetőséget.

    ![Azure AD-oldal, amelyben a csoportok láthatók](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Ekkor megjelenik az **új csoport** ablaktábla, és ki kell töltenie a szükséges információkat.

    ![Új csoport oldal, példa adatokkal kitöltve](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. Válassza ki az előre definiált **csoport típusát**. A csoport típusaival kapcsolatos további információkért lásd: [csoport-és tagsági típusok](#group-types).

1. Hozzon létre és adjon hozzá egy **csoportnevet.** Válassza ki a megjegyezni kívánt nevet, és ez a csoport értelme. A rendszer egy ellenőrzés végrehajtásával megállapítja, hogy a nevet már használja-e egy másik csoport. Ha a név már használatban van, a duplikált nevek elkerülése érdekében a rendszer megkéri, hogy változtassa meg a csoport nevét.

1. Vegyen fel egy **csoportbeli e-mail-címet** a csoport számára, vagy tartsa meg az automatikusan kitöltött e-mail-címet.

1. **Csoport leírása** Ha szeretne kiegészítő leírást adni a csoporthoz.

1. Válasszon egy előre definiált **tagsági típust (kötelező).** A tagsági típusokkal kapcsolatos további információkért lásd: [csoport-és tagsági típusok](#membership-types).

1. Válassza a **Létrehozás** lehetőséget. A csoport létrehozása megtörtént és készen áll a tagok hozzáadására.

1. Válassza ki a **Tagok** területet a **Csoport** lapon, és utána kezdje el megkeresni a csoportba felvenni kívánt tagokat a **Tagok kiválasztása** lapon.

    ![Csoport tagjainak kiválasztása a csoport létrehozása során](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Amikor elkészült a tagok hozzáadásával, válassza a **kiválasztást**.

    A **csoport – áttekintés** lap frissül és mutatja a csoportba most felvett tagok számát.

    ![Csoport áttekintés oldal a tagok számával kiemelve](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Csoport üdvözlő e-mail-címének be-és kikapcsolása

Ha új Microsoft 365 csoportot hoz létre, akár dinamikus, akár statikus tagsággal rendelkezik, a rendszer a csoportba felvett összes felhasználóhoz üdvözlő értesítést küld. Ha a felhasználó vagy az eszköz bármely attribútuma megváltozik, a rendszer a szervezet összes dinamikus csoportjának szabályait feldolgozza a lehetséges tagsági változások miatt. A hozzáadott felhasználók megkapják az üdvözlő értesítést is. Ezt a viselkedést kikapcsolhatja az [Exchange PowerShellben](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup). 

## <a name="next-steps"></a>Következő lépések

- [Az SaaS-alkalmazásokhoz való hozzáférés kezelése csoportokkal](../enterprise-users/groups-saasapps.md)
- [Csoportok kezelése PowerShell-parancsokkal](../enterprise-users/groups-settings-v2-cmdlets.md)
