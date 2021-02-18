---
title: A Nagyvállalati Szerződések (EA) rendszergazdai szerepkörei az Azure-ban
description: Megismerheti a vállalati rendszergazdai szerepköröket az Azure-ban. Öt különböző adminisztratív szerepkört rendelhet hozzá.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 1ceed171b0516e293ffe58bca0225d3d3dfdb414
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094655"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>Az Azure Nagyvállalati Szerződés (EA) szerepköreinek kezelése

A szervezet használati adatainak és költségeinek kezeléséhez a Nagyvállalati Szerződéssel rendelkező Azure-ügyfelek öt különféle rendszergazdai szerepkört rendelhetnek felhasználóikhoz:

- Vállalati rendszergazda
- Vállalati rendszergazda (csak olvasási)<sup>1</sup>
- Részlegszintű rendszergazda
- Részlegszintű rendszergazda (csak olvasási)
- Fióktulajdonos<sup>2</sup>

<sup>1</sup>A nagyvállalati szerződés számlázási kapcsolattartója lesz ehhez a szerepkörhöz rendelve.

<sup>2</sup>A számlázási kapcsolattartót nem lehet hozzáadni vagy módosítani az Azure EA portálon, és az EA-regisztrációhoz a szerződés szintjén hozzáadott számlázási kapcsolattartóként megadott felhasználó lesz hozzáadva. A számlázási kapcsolattartó módosításához regionális műveleti központ (ROC) partner-/szoftvertanácsadójához kell kérelmet benyújtani.

A regisztráció kiépítése során megadott első regisztrációs rendszergazda határozza meg a számlázási kapcsolattartó fiókja hitelesítésének típusát. Ha csak olvasási jogosultsággal rendelkező rendszergazdaként adják hozzá a számlázási kapcsolattartót az EA Portalhoz, akkor a kapcsolattartó hitelesítésének típusa Microsoft-fiókkal történő hitelesítés lesz. 

Ha például a kezdeti hitelesítési típus vegyesre van állítva, akkor az EA Microsoft-fiókként lesz hozzáadva, és a számlázási kapcsolattartó csak olvasási EA-rendszergazdai jogosultságokkal fog rendelkezni. Ha az EA-rendszergazda nem hagyja jóvá egy meglévő számlázási kapcsolattartó Microsoft-fiókkal történő hitelesítését, az EA-rendszergazda törölheti a szóban forgó felhasználót, és megkérheti az ügyfelet, hogy újból adja hozzá a felhasználót csak olvasási jogosultsággal rendelkező rendszergazdaként, munkahelyi vagy iskolai fiókkal, amely csak a regisztráció szintjén van megadva az EA Portalon.

Ezek a szerepkörök kifejezetten az Azure Nagyvállalati Szerződések kezelésére szolgálnak, és kiegészítik az Azure beépített szerepköreit, amelyek az erőforrásokhoz való hozzáférést szabályozzák. További információ: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md).

## <a name="azure-enterprise-portal-hierarchy"></a>Az Azure Enterprise portál hierarchiája

Az Azure Enterprise portál hierarchiája az alábbiakat tartalmazza:

- Az **Azure Enterprise portál** egy online felügyeleti portál, amely segít az Azure EA-szolgáltatások költségeinek kezelésében. A következőket teheti:

  - Hozzon létre egy Azure EA-hierarchiát részlegekkel, fiókokkal és előfizetésekkel.
  - Összeegyeztetheti az igénybe vett szolgáltatások költségeit, letöltheti a használati jelentéseket és megtekintheti az árlistákat.
  - Létrehozhat API-kulcsokat a regisztrációjához.

- A **részlegek** segítenek a költségek logikai csoportosítás szerinti szegmentálásában. A részlegek lehetővé teszik, hogy megadjon egy költségkeretet vagy kvótát a részlegszinten.

- A **fiókok** az Azure Enterprise portálon található szervezeti egységek. A fiókok segítségével kezelheti az előfizetéseket és hozzáférhet a jelentésekhez.

- Az **előfizetések** az Azure Enterprise portálon található legkisebb egységek. A szolgáltatásadminisztrátorok által kezelt Azure-szolgáltatások tárolóiként szolgálnak.

Az alábbi ábra egyszerű Azure EA-hierarchiákat mutat be.

![Egyszerű Azure EA-hierarchiák ábrája](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az alábbi rendszergazdai felhasználói szerepkörök a vállalati regisztráció részei:

- Vállalati rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos
- Szolgáltatás-rendszergazda
- Értesítendő fél

A szerepkörök két különböző portálon végzett feladatok végrehajtásához használhatók. Az [Azure Enterprise portált](https://ea.azure.com) használhatja a számlázás és a költségek kezeléséhez, az [Azure Portalt](https://portal.azure.com) pedig az Azure-szolgáltatások kezeléséhez.

A felhasználói szerepkörök felhasználói fiókhoz vannak rendelve. A felhasználók hitelességének ellenőrzéséhez minden felhasználónak érvényes munkahelyi, iskolai vagy Microsoft-fiókkal kell rendelkeznie. Győződjön meg arról, hogy mindegyik fiókhoz olyan e-mail-cím tartozik, amely rendszeresen ellenőrizve van. A fiókértesítéseket az e-mail-címekre küldi a rendszer.

A felhasználók beállításakor több fiókot is hozzárendelhet a vállalati rendszergazdai szerepkörhöz. Azonban csak egy fióktulajdonos engedélyezett. Emellett egyszerre egyetlen fiók rendelkezhet a vállalati rendszergazdai és a fióktulajdonosi szerepkörrel.

### <a name="enterprise-administrator"></a>Vállalati rendszergazda

Az ilyen szerepkörrel felruházott felhasználók rendelkeznek a legmagasabb szintű hozzáféréssel. Ezek a következők:

- A fiókok és fióktulajdonosok kezelése.
- Más vállalati rendszergazdák kezelése.
- Részlegszintű rendszergazdák kezelése.
- Értesítendő felek kezelése.
- A fiókok használati adatainak megtekintése.
- A fiókok nem számlázott díjainak megtekintése.
- Minden foglalási rendelés és foglalás megtekintése és kezelése, amelyre a Nagyvállalati Szerződés vonatkozik.
  - A vállalati rendszergazda (csak olvasási) megtekintheti a foglalási rendeléseket és foglalásokat. Nem kezelheti őket.

Több vállalati rendszergazda is lehet egy vállalati regisztrációban. A vállalati rendszergazdákhoz rendelhet csak olvasási hozzáférést is. Mindegyikük örökli a részlegszintű rendszergazdai szerepkört.

### <a name="department-administrator"></a>Részlegszintű rendszergazda

A szerepkörrel rendelkező felhasználók képesek:

- Részlegek létrehozására és kezelésére.
- Új fióktulajdonosok létrehozására.
- Az általuk kezelt részlegek használati adatainak megtekintésére.
- A szükséges engedélyek birtokában a költségek megtekintésére.

Több részlegszintű rendszergazdája is lehet az egyes vállalati regisztrációkban.

A részlegszintű rendszergazdákhoz rendelhet csak olvasási hozzáférést is, amikor részlegszintű rendszergazdát módosít vagy hoz létre. Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre.

### <a name="account-owner"></a>Fióktulajdonos

A szerepkörrel rendelkező felhasználók képesek:

- Előfizetések létrehozására és kezelésére.
- Szolgáltatásadminisztrátorok kezelésére.
- Előfizetések használati adatainak megtekintésére.

Mindegyik fiókhoz szükség van egyedi munkahelyi, iskolai vagy Microsoft-fiókra. Az Azure Enterprise portál rendszergazdai szerepköreivel kapcsolatos további információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-ea-roles.md).

### <a name="service-administrator"></a>Szolgáltatás-rendszergazda

A szolgáltatásadminisztrátor jogosult a szolgáltatások kezelésre az Azure Portalon, és a társadminisztrátori szerepkör felhasználókhoz való hozzárendelésére.

### <a name="notification-contact"></a>Értesítendő fél

Az értesítendő fél a regisztrációval kapcsolatos használati értesítéseket kap.

A következő szakaszok az egyes szerepkörök korlátait és képességeit mutatják be.

## <a name="user-limit-for-admin-roles"></a>A rendszergazdai szerepkörök felhasználói korlátai

|Szerepkör| Felhasználói korlát|
|---|---|
|Vállalati rendszergazda|Korlátlan|
|Vállalati rendszergazda (csak olvasási)|Korlátlan|
|Részlegszintű rendszergazda|Korlátlan|
|Részlegszintű rendszergazda (csak olvasási)|Korlátlan|
|Fióktulajdonos|Fiókonként 1<sup>3</sup>|

<sup>3</sup>Mindegyik fiókhoz szükség van egyedi Microsoft-fiókra, illetve munkahelyi vagy iskolai fiókra.

## <a name="organization-structure-and-permissions-by-role"></a>Szervezeti felépítés és engedélyek szerepkörönként

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasási)|Részlegszintű rendszergazda|Részlegszintű rendszergazda (csak olvasási)|Fióktulajdonos| Partner|
|---|---|---|---|---|---|---|
|Vállalati rendszergazdák megtekintése|✔|✔|✘|✘|✘|✔|
|Vállalati rendszergazdák hozzáadása vagy eltávolítása|✔|✘|✘|✘|✘|✘|
|Értesítési kapcsolattartók megtekintése<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Értesítési kapcsolattartók hozzáadása vagy eltávolítása<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Részlegek létrehozására és kezelésére |✔|✘|✘|✘|✘|✘|
|Részlegszintű rendszergazdák megtekintése|✔|✔|✔|✔|✘|✔|
|Részlegszintű rendszergazdák hozzáadása vagy eltávolítása|✔|✘|✔|✘|✘|✘|
|A regisztrációhoz tartozó fiókok megtekintése |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Fiókok hozzáadása a regisztrációhoz és a fióktulajdonos módosítása|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Előfizetések és előfizetési engedélyek létrehozása és kezelése|✘|✘|✘|✘|✔|✘|

- <sup>4</sup>Az értesítési kapcsolattartók kapják meg az Azure Nagyvállalati Szerződésre vonatkozó tájékoztató e-maileket.
- <sup>5</sup>A feladat a részlegen belüli fiókokra korlátozódik.

## <a name="add-a-new-enterprise-administrator"></a>Új vállalati rendszergazda hozzáadása

Az Azure EA-regisztrációk kezelésénél a vállalati rendszergazdák rendelkeznek a legtöbb jogosultsággal. Az első Azure EA-rendszergazdát a nagyvállalati szerződés megkötésekor hozták létre. Viszont bármikor hozzá lehet adni új rendszergazdákat, vagy el is lehet távolítani őket. Az új rendszergazdákat csak a meglévő rendszergazdák adhatják hozzá. A vállalati rendszergazdák hozzáadásával kapcsolatos további információkért lásd az [új vállalati rendszergazda létrehozását](ea-portal-administration.md#create-another-enterprise-administrator) ismertető részt. A számlázási profil szerepköreivel és feladataival kapcsolatos további információkért lásd [a számlázási profil szerepköreinek és azok feladatainak ismertetését](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>A fióktulajdonosi állapot frissítése Függőben értékről Aktív értékre

Amikor először hozzáadják az új fióktulajdonosokat (AO) egy Azure EA-regisztrációhoz, a _függőben_ állapot jelenik meg náluk. Amikor az új fióktulajdonosok megkapják az aktiváló e-mailt, be tudnak jelentkezni a fiókjuk aktiválásához. A fiókjuk aktiválásakor a fiók állapota _függőben_ értékről _aktív_ értékre módosul. A fióktulajdonosnak el kell olvasnia a figyelmeztető üzenetet, majd a **Folytatás** lehetőséget kell választania. A rendszer kérheti az új felhasználók vezeték- és utónevét egy Kereskedelmi fiók létrehozásához. Ha kéri, akkor meg kell adniuk a szükséges adatokat a folytatáshoz, majd megtörténik a fiók aktiválása.

## <a name="add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

Ha az egyik Azure EA-rendszergazda létrehozott egy részleget, az Azure vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és hozzárendelheti őket egy-egy részleghez. A részlegszintű rendszergazdák új fiókokat hozhatnak létre. Az Azure EA-előfizetések létrehozásához új fiókokra van szükség.

A részlegszintű rendszergazdák hozzáadásával kapcsolatos további információkért tekintse meg az [Azure EA-részlegszintű rendszergazdák létrehozását](ea-portal-administration.md#add-a-department-administrator) ismertető témakört.

## <a name="usage-and-costs-access-by-role"></a>Szerepkörök szerint hozzáférés a használati adatokhoz és a költségekhez

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasási)|Részlegszintű rendszergazda|Részlegszintű rendszergazda (csak olvasási) |Fióktulajdonos| Partner|
|---|---|---|---|---|---|---|
|Kreditegyenleg megtekintése, beleértve az Azure-előrefizetést|✔|✔|✘|✘|✘|✔|
|Részleg költségkvótáinak megtekintése|✔|✔|✘|✘|✘|✔|
|Részleg költségkvótáinak beállítása|✔|✘|✘|✘|✘|✘|
|Szervezet EA-árlistájának megtekintése|✔|✔|✘|✘|✘|✔|
|Használati adatok és költségek megtekintése|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Erőforrások kezelése az Azure Portalon|✘|✘|✘|✘|✔|✘|

- <sup>6</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a részlegszintű rendszergazda számára** az Enterprise Portalon. A részlegszintű rendszergazda ezután megtekintheti a részleg költségeire vonatkozó adatokat.
- <sup>7</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a fióktulajdonos számára** az Enterprise Portalon. A fióktulajdonos ezután megtekintheti a fiók költségeire vonatkozó adatokat.

## <a name="see-pricing-for-different-user-roles"></a>A különböző felhasználói szerepkörök díjszabásának megtekintése

Azt, hogy az Azure Portalon milyen díjszabás jelenik meg befolyásolja az Ön rendszergazdai szerepköre, valamint az, hogy a vállalati rendszergazda milyen díjtétel-megtekintési szabályzatokat állított be. Az Enterprise Portalon beállítható két szabályzat, amely befolyásolja az Azure Portalon megjelenő díjat:

- Díjtételek megtekintése a részlegszintű rendszergazda számára
- Díjtételek megtekintése a fióktulajdonos számára

A szabályzatok beállításával kapcsolatban tekintse meg [az Azure-beli számlázási információkhoz való hozzáférés kezelését](manage-billing-access.md) ismertető témakört.

A következő táblázat bemutatja a Nagyvállalati Szerződés rendszergazdai szerepkörei, a beállított díjtétel-megtekintési szabályzat, az Azure Portal Azure-szerepköre, valamint az Azure Portalon megjelenő díjak közötti összefüggéseket. A vállalati rendszergazda mindig a szervezet EA-díjszabása szerinti használati adatokat látja. A részlegszintű rendszergazdák és a fióktulajdonosok ugyanakkor eltérő díjakat láthatnak a díjtétel-megtekintési szabályzattól és a saját Azure-szerepkörüktől függően. A következő táblázatban szereplő részlegszintű rendszergazda szerepkör magában foglalja a Részlegszintű rendszergazda és a Részlegszintű rendszergazda (csak olvasási) szerepkört is.

|Nagyvállalati Szerződés rendszergazdai szerepköre|A szerepkörhöz beállított díjtétel-megtekintési szabályzat|Azure-szerepkör|Megjelenő díjszabás|
|---|---|---|---|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✔ Engedélyezve|Tulajdonos|Szervezet EA-díjszabása|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✘ Letiltva|Tulajdonos|Kiskereskedelmi díjszabás|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✔ Engedélyezve |Nincs|Nincs díjszabás|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✘ Letiltva |Nincs|Nincs díjszabás|
|None|Nem alkalmazható |Tulajdonos|Kiskereskedelmi díjszabás|

A vállalati rendszergazdai szerepkört és a díjtétel-megtekintési szabályzatokat az Enterprise Portalon lehet beállítani. Az Azure-szerepkört az Azure Portalon lehet frissíteni. További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>További lépések

- [Az Azure-beli számlázási információkhoz való hozzáférés kezelése](manage-billing-access.md)
- [Azure-szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)
- [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)
