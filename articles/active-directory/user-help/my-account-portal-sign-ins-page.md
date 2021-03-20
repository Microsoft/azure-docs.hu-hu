---
title: A legutóbbi bejelentkezési tevékenység megtekintése és keresése a saját bejelentkezési lapról – Azure Active Directory | Microsoft Docs
description: Részletes információ arról, hogyan tekintheti meg és keresheti meg a legutóbbi bejelentkezési tevékenységeit a fiókom portál saját bejelentkezések lapján.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096072"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>A legutóbbi bejelentkezési tevékenység megtekintése és keresése a saját bejelentkezési oldalról

A legutóbbi munkahelyi vagy iskolai fiók bejelentkezési tevékenységeit a **saját fiók** portál **saját bejelentkezések** lapján tekintheti meg. A bejelentkezési előzmények áttekintésével a következő módon tekintheti meg a szokatlan tevékenységeket:

- Ha valaki megpróbálja kitalálni a jelszavát.
- Ha egy támadó sikeresen bejelentkezett a fiókjába, és az adott helyről.
- Milyen alkalmazások próbáltak hozzáférni a támadóhoz.

>[!Note]
> Ha a személyes Microsoft-fiók való bejelentkezéskor hibaüzenet jelenik meg, akkor is bejelentkezhet a szervezet tartományneve (például contoso.com) vagy a szervezete **bérlői azonosítójának** használatával a következő URL-címek egyikében:
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>A legutóbbi bejelentkezési tevékenység megtekintése

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myaccount.microsoft.com/ lapra.

2. Válassza a **saját bejelentkezések** lehetőséget a bal oldali navigációs ablaktáblán, vagy a **saját bejelentkezési** blokkból válassza a **Legutóbbi tevékenység áttekintése** hivatkozást.

    ![Fiókom oldal, amely a legutóbbi tevékenységek Kiemelt hivatkozásait mutatja](media/my-account-portal/my-account-portal-sign-ins.png)

3. Bontsa ki és tekintse át az egyes bejelentkezési elemeket, és ügyeljen rá, hogy mindegyiket felismerje. Ha olyan bejelentkezési tételt talál, amely nem ismerősnek tűnik, módosítsa a jelszavát, hogy megvédje a fiókot abban az esetben, ha az sérült.

    ![Legutóbbi tevékenység lap bővített bejelentkezési adataival](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Ha sikeres bejelentkezést lát

Időnként előfordulhat, hogy a saját normál bejelentkezési tevékenységének áttekintése során egy ismeretlen helyről, böngészőből vagy operációs rendszerből sikeres bejelentkezés jelenik meg. Az ismeretlen bejelentkezések azt jelenthetik, hogy egy támadó hozzáfért a fiókjához. Ha olyan tevékenység jelenik meg, amelyet nem engedélyezett, javasoljuk, hogy azonnal módosítsa a jelszavát, majd lépjen a [biztonsági adatok](https://mysignins.microsoft.com/security-info) menüpontra a biztonsági beállítások frissítéséhez.

A probléma meghatározása előtt győződjön meg arról, hogy nem jelenik meg hamis pozitív (ahol az elem megkérdőjelezhető, de rendben van). Meghatározhatja például az IP-címe alapján a hozzávetőleges helyet és a leképezést. A mobileszközök különösen nehezen azonosíthatók, mivel időnként távoli telephelyeken keresztül irányítják a forgalmat. Még ha Washington államban is bejelentkezik a mobileszköz használatával, előfordulhat, hogy a hely a California-ból érkező bejelentkezést is megjeleníti. Nyomatékosan javasoljuk, hogy csak a helyről tekintse meg a részleteket. Győződjön meg arról, hogy az operációs rendszer, a böngésző és az alkalmazás is minden szempontból fontos.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Ha sikertelen bejelentkezést lát

Ha nem sikerül bejelentkeznie, az azt jelentheti, hogy elvégezte a hitelesítő adatok beírását. Azt is jelentheti, hogy egy támadó megpróbálta kitalálni a jelszavát. Ennek a kockázatnak a megválaszolásához nem kell módosítania a jelszavát, de javasoljuk, hogy regisztráljon az Azure AD Multi-Factor Authentication (MFA) szolgáltatásra. A többtényezős hitelesítéssel akkor is, ha a hacker kitalálta a jelszavát, nem lesz elég a fiók eléréséhez.

![Sikertelen bejelentkezési csempe](media/my-account-portal-sign-ins-page/unsuccessful.png)

Ha nem sikerül bejelentkeznie, és a **munkamenet-tevékenység** alatt egy megjegyzés jelenik meg, az azt `Additional verification failed, invalid code` jelenti, hogy az elsődleges hitelesítési hitelesítő adatai sikeresek, de a többtényezős hitelesítés sikertelen volt. Ez az állapot azt jelentheti, hogy egy támadó helyesen kitalálta a jelszavát, de nem tudta átadni a multi-Factor Authentication kérdését. Javasoljuk, hogy továbbra is változtassa meg a jelszavát, mert lehetséges, hogy a támadó már rendelkezik ezzel, majd lépjen a [biztonsági adatok](https://mysignins.microsoft.com/security-info) lapra a biztonsági beállítások frissítéséhez.

## <a name="search-for-specific-sign-in-activity"></a>Adott bejelentkezési tevékenység keresése

A legutóbbi bejelentkezési tevékenységeit bármely elérhető információ alapján keresheti meg. Megkeresheti például a legutóbbi bejelentkezési tevékenységeit operációs rendszer, hely, alkalmazás stb. alapján.

1. A **Legutóbbi tevékenység áttekintése** lapon adja **meg a keresési sávon keresendő** adatokat. Írja be például a következőt: a fiókom `Unsuccessful` alkalmazás által gyűjtött összes sikertelen bejelentkezési tevékenység keresése.

2. Kattintson a **Keresés** gombra a keresés megkezdéséhez.

    ![Közelmúltbeli tevékenység lap, a Kiemelt keresősáv, a Keresés gomb és az eredmények megjelenítése](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Szokatlan tevékenység megerősítése

A szokatlan tevékenységként megjelölt bejelentkezések az adott tevékenység csempéje megerősíthető a **saját bejelentkezési** oldalon.

![Szokatlan bejelentkezési csempe, amely megerősíti, hogy Ön vagy nem kísérelte meg a bejelentkezést](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Következő lépések

A legutóbbi bejelentkezési tevékenység megtekintése után a következőket teheti:

- [Biztonsági adatok](./security-info-setup-signin.md)megtekintése és kezelése.

- A csatlakoztatott [eszközök](my-account-portal-devices-page.md)megtekintése és kezelése.

- A [szervezetek](my-account-portal-organizations-page.md)megtekintése és kezelése.

- Megtekintheti, hogy a szervezet hogyan [használja az adatvédelmet érintő adatokat](my-account-portal-privacy-page.md).

- A fiókom [portál beállításainak](my-account-portal-settings.md) módosítása