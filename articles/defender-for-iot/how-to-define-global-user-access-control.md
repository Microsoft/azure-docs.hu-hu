---
title: Globális felhasználói hozzáférés-vezérlés meghatározása
description: A nagyméretű szervezeteknél a felhasználói engedélyek bonyolultak lehetnek, és a normál hely és a zóna szerkezete mellett globális szervezeti struktúra alapján is meghatározhatók.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/08/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 81ede3506337e76c84e20203ac27a3d441fdcef8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522274"
---
# <a name="define-global-access-control"></a>Globális hozzáférés-vezérlés meghatározása

A nagyméretű szervezeteknél a felhasználói engedélyek bonyolultak lehetnek, és a normál hely és a zóna szerkezete mellett globális szervezeti struktúra alapján is meghatározhatók.

A globális és összetettebb felhasználói hozzáférési engedélyek iránti igény támogatásához létrehozhat egy üzleti egységeken, régiókban és webhelyeken alapuló globális üzleti topológiát is. Ezután megadhat felhasználói hozzáférési engedélyeket ezen entitások köré.

Az üzleti topológiához való hozzáférési eszközök használata segít a szervezeteknek a nulla megbízhatóságú stratégiák megvalósításában azáltal, hogy jobban szabályozzák, hogy a felhasználók miként kezelhetik és elemezik az eszközöket az Azure Defender for IoT platformon.

## <a name="about-access-groups"></a>Hozzáférési csoportok

A globális hozzáférés-vezérlés felhasználói hozzáférési csoportok létrehozásával jön létre. A hozzáférési csoportok olyan szabályokból állnak, amelyek segítségével a felhasználók hozzáférhetnek bizonyos üzleti entitásokhoz. A csoportok használata lehetővé teszi a Defender számára a IoT megtekintését és konfigurálását a megfelelő üzleti egységekben, régiókban és webhelyeken megadott felhasználói szerepkörökhöz.

Tegyük fel például, hogy a biztonsági elemzők egy Active Directory csoportból férnek hozzá az összes Nyugat-európai autóipari és üveg-termelési sorhoz, valamint egy adott régióban található műanyag-vonalhoz.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="A biztonsági elemző Active Directory csoportjának ábrája.":::

A hozzáférési csoportok létrehozása előtt javasoljuk, hogy:

- Körültekintően állítsa be az üzleti topológiát. További információ az üzleti topológiáról: [a webhelyszerkezet-nézetek használata](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Tervezze meg, hogy mely felhasználók vannak társítva a létrehozott hozzáférési csoportokhoz. A felhasználók csoporthoz való hozzárendeléséhez két lehetőség áll rendelkezésre:

  - **Active Directory csoportok csoportjának társítása**: Ellenőrizze, hogy be van-e állítva egy Active Directory-példány a helyszíni felügyeleti konzollal való integráláshoz.
  
  - **Helyi felhasználók kiosztása**: Ellenőrizze, hogy létrehozott-e felhasználókat. További információ: [felhasználók definiálása](how-to-create-and-manage-users.md#define-users).

A rendszergazdai felhasználók nem rendelhetők hozzá hozzáférési csoportokhoz. Ezek a felhasználók alapértelmezés szerint minden üzleti topológiai entitáshoz hozzáférnek.

## <a name="create-access-groups"></a>Hozzáférési csoportok létrehozása

Ez a szakasz a hozzáférési csoportok létrehozását ismerteti. Az első létrehozott csoport alapértelmezett globális üzleti egységeit és régióit hozza létre. Az alapértelmezett entitásokat az első csoport definiálásakor módosíthatja.

Csoportok létrehozása:

1. A helyszíni felügyeleti konzol oldalsó menüjéből válassza a **hozzáférési csoportok** lehetőséget.

2. Válassza a(z) :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false"::: lehetőséget. A **hozzáférési csoport hozzáadása** párbeszédpanelen adja meg a hozzáférési csoport nevét. A konzol 64 karaktert támogat. Rendelje hozzá a nevet oly módon, hogy könnyebben megkülönböztesse ezt a csoportot más csoportoktól.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="A hozzáférési csoport hozzáadása párbeszédpanel, ahol hozzáférési csoportokat hoz létre.":::

3. Ha megjelenik az **Active Directory csoport kiosztása** lehetőség, akkor egy Active Directory felhasználói csoportot rendelhet hozzá ehhez a hozzáférési csoporthoz.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Rendeljen Active Directory csoportot a hozzáférési csoport létrehozása párbeszédpanelen.":::

   Ha a lehetőség nem jelenik meg, és Active Directory csoportokat szeretne szerepeltetni a hozzáférési csoportokban, válassza a **Rendszerbeállítások** lehetőséget. Az **integrációk** ablaktáblán adja meg a csoportokat. Pontosan úgy adja meg a csoportnév nevét, ahogy az a Active Directory konfigurációkban jelenik meg, és kisbetűvel.

5. A **felhasználók** ablaktáblán rendelje hozzá a kívánt számú felhasználót a csoporthoz. A felhasználókat különböző csoportokhoz is hozzárendelheti. Ha így dolgozik, létre kell hoznia és el kell mentenie a hozzáférési csoportot és a szabályokat, majd a **felhasználók ablaktáblában** hozzá kell rendelnie a felhasználókat a csoporthoz.

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Kezelje a felhasználói szerepköröket, és szükség szerint rendelje hozzá őket.":::

6. Hozzon létre szabályokat a **szabályok hozzáadása a *név*** párbeszédpanelen az üzleti topológia hozzáférési követelményei alapján. Az itt megjelenő beállítások a **vállalati nézet** és a **hely kezelése** ablakokban megtervezett topológián alapulnak. 

   Csoportonként több szabályt is létrehozhat. Több szabályt is létre kell hoznia csoportonként, ha összetett hozzáférési részletességgel dolgozik több helyen. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Adjon hozzá egy szabályt a rendszerhez.":::

A létrehozott szabályok megjelennek a **hozzáférési csoport hozzáadása** párbeszédpanelen. Itt törölheti vagy szerkesztheti is azokat.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Az összes hozzáférési csoport megtekintése és szerkesztése ebben az ablakban.":::

### <a name="about-rules"></a>Tudnivalók a szabályokról

Szabályok létrehozásakor vegye figyelembe a következő információkat:

- Ha egy hozzáférési csoport több szabályt tartalmaz, a szabály logikája összesíti az összes szabályt. Például a szabályok a és a Logic, a not vagy a Logic.

- Ahhoz, hogy egy szabály alkalmazása sikeres legyen, az érzékelőket a **hely kezelése** ablakban kell hozzárendelni a zónákhoz.

- Egy szabályhoz csak egy elemet rendelhet hozzá. Például hozzárendelhet egy üzleti egységet, egy régiót és egy helyet az egyes szabályokhoz. Hozzon létre több szabályt a csoport számára, ha például azt szeretné, hogy egy Active Directory csoportba tartozó felhasználók különböző régiókban lévő üzleti egységekhez férhessenek hozzá.

- Ha módosít egy entitást, és a módosítás hatással van a szabály logikára, a rendszer törli a szabályt. Ha a topológiai entitás módosításai hatással vannak a szabály logikára, például a szabályok törlésére, a hozzáférési csoport marad, de a felhasználók nem tudnak bejelentkezni a helyszíni felügyeleti konzolra. A rendszer értesíti a felhasználót, hogy lépjen kapcsolatba a rendszergazdával a bejelentkezéshez.

- Ha nincs kiválasztva üzleti egység vagy régió, a felhasználók hozzáférhetnek az összes meghatározott üzleti egységhez és régióhoz.

## <a name="next-steps"></a>Következő lépések

[A IoT-konzol felhasználói](how-to-create-and-manage-users.md)
