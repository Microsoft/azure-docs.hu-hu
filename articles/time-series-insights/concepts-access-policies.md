---
title: Az adathozzáférés biztosítására szolgáló biztonság konfigurálása – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat biztonsági, engedélyezési és kezelési házirendeket az Azure Time Series Insights-környezetben.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780858"
---
# <a name="grant-data-access-to-an-environment"></a>Az adathozzáférés biztosítása egy környezethez

Ez a cikk a Azure Time Series Insights-hozzáférési szabályzatok két típusát tárgyalja.

## <a name="sign-in-to-azure-time-series-insights"></a>Bejelentkezés Azure Time Series Insights

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg a Azure Time Series Insights környezetet `Time Series Insights environments` a **keresőmezőbe** való belépéssel. `Time Series Insights environments`A keresési eredmények között válassza a lehetőséget.
1. Válassza ki a Azure Time Series Insights környezetet a listából.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Az alábbi lépések végrehajtásával biztosíthatja az adathozzáférést egy felhasználói tag számára.

1. Válassza **az adatelérési házirendek**, majd a **+ Hozzáadás** lehetőséget.

    [![Adatelérési házirend kiválasztása és hozzáadása](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Válassza a **felhasználó kiválasztása** lehetőséget. Keresse meg a felhasználónevet vagy az e-mail-címet, hogy megkeresse a hozzáadni kívánt felhasználót. Válassza **a kijelölés lehetőséget a** kijelölés megerősítéséhez.

    [![Válassza ki a hozzáadni kívánt felhasználót](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Válassza a **szerepkör kiválasztása** lehetőséget. Válassza ki a megfelelő hozzáférési szerepkört a felhasználó számára:

    * Válassza a **közreműködő** lehetőséget, ha engedélyezni szeretné a felhasználó számára, hogy módosítsa a hivatkozásokat, és megossza a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza az **olvasó** lehetőséget, ha lehetővé szeretné tenni a felhasználó számára az adatlekérdezést a környezetben, és személyes, nem megosztott, lekérdezéseket menteni a környezetben.

   A szerepkör választásának megerősítéséhez kattintson **az OK gombra** .

    [![A kiválasztott szerepkör megerősítése](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. A **felhasználói szerepkör kiválasztása** lapon kattintson az **OK gombra** .

    [![A felhasználói szerepkör kiválasztása lapon kattintson az OK gombra.](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Győződjön meg arról, hogy az **adatelérési házirendek** lap felsorolja a felhasználókat és az egyes felhasználók szerepköreit.

    [![A megfelelő felhasználók és szerepkörök ellenőrzése](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Vendég hozzáférés biztosítása másik Azure AD-bérlőtől

A `Guest` szerepkör nem felügyeleti szerepkör. Ez egy olyan fiók, amelyet az egyik bérlőtől a másikba hívnak meg. A vendég fióknak a bérlő címtárba való meghívása után ugyanazzal a hozzáférés-vezérléssel is rendelkezhet, mint bármely más fiók esetében. Azure Time Series Insights-környezethez a Access Control (IAM) panelen adhat felügyeleti hozzáférést. Emellett az adatelérési szabályzatok panelen is megadhat hozzáférést a környezetben található adathoz. A Azure Active Directory (Azure AD) bérlői vendég hozzáférésével kapcsolatos további információkért olvassa el [a Azure Active Directory B2B együttműködési felhasználók hozzáadása a Azure Portal](../active-directory/external-identities/add-users-administrator.md).

Kövesse az alábbi lépéseket, ha vendég hozzáférést szeretne biztosítani egy Azure Time Series Insights-környezethez egy másik bérlő Azure AD-felhasználója számára.

1. Lépjen a Azure Portal, majd a  **Azure Active Directory** elemre, görgessen le az **Áttekintés** lapon, majd válassza a **vendég felhasználó** lehetőséget.

    [![Válassza az Adathozzáférési házirendek, majd a + meghívás lehetőséget.](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Adja meg a meghívni kívánt felhasználó e-mail-címét. Ezt az e-mail-címet hozzá kell rendelni az Azure AD-hez. Igény szerint személyes üzenetet is hozzáadhat a meghívóhoz.

    [![Adja meg az e-mail-címet a kiválasztott felhasználó megkereséséhez](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Keresse meg a képernyőn megjelenő megerősítő buborékot. Az **értesítések** lehetőségre kattintva ellenőrizheti, hogy a vendég felhasználó hozzá lett-e adva.

    [![Keresse meg a megerősítő buborékot](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Lépjen vissza a Time Series Insights-környezetbe az újonnan létrehozott vendég felhasználó hozzáadásához. Kattintson az adathozzáférés **engedélyezése** elemre az adatelérési **szabályzatok** szakaszban leírtak szerint. **Válassza a felhasználó lehetőséget**. Keresse meg a vendég felhasználó e-mail-címét, amelyet a felvenni kívánt felhasználó megtalálására meghívott. Ezután **válassza a lehetőséget** a kijelölés megerősítéséhez.

    [![Válassza ki a felhasználót, és erősítse meg a kijelölést](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Válassza a **szerepkör kiválasztása** lehetőséget. Válassza ki a megfelelő hozzáférési szerepkört a vendég felhasználó számára:

    * Válassza a **közreműködő** lehetőséget, ha engedélyezni szeretné a felhasználó számára, hogy módosítsa a hivatkozásokat, és megossza a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza az **olvasó** lehetőséget, ha lehetővé szeretné tenni a felhasználó számára az adatlekérdezést a környezetben, és személyes, nem megosztott, lekérdezéseket menteni a környezetben.

   A szerepkör választásának megerősítéséhez kattintson **az OK gombra** .

    [![A szerepkör megválasztásának megerősítése](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. A **felhasználói szerepkör kiválasztása** lapon kattintson az **OK gombra** .

1. Győződjön meg arról, hogy az **adatelérési házirendek** lap felsorolja a vendég felhasználót és az egyes vendég felhasználók szerepköreit.

    [![Annak ellenőrzése, hogy megfelelően vannak-e hozzárendelve a felhasználók és a szerepkörök](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. A vendég felhasználó ekkor egy meghívót tartalmazó e-mailt fog kapni a fent megadott e-mail-címen. A vendég felhasználó az első **lépések** lehetőség kiválasztásával erősítse meg az elfogadását és az Azure-felhőhöz való kapcsolódást.

    [![A vendég kiválasztja az elfogadás megkezdése lehetőséget.](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Miután kiválasztotta az első **lépéseket**, a vendég felhasználó a rendszergazda szervezetéhez tartozó engedélyekkel fog megjelenni. Az **elfogadás** lehetőség kiválasztásával a rendszer bejelentkezik az engedélyek megadása után.

    [![Vendég Vélemények engedélyei és elfogadása](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. A rendszergazda [megosztja a környezet URL-címét](time-series-insights-parameterized-urls.md) a vendégük számára.

1. Miután bejelentkezett a vendég felhasználó a meghívni kívánt e-mail-címre, és elfogadják a meghívót, a rendszer átirányítja Azure Portalra.

1. A vendég most már hozzáférhet a megosztott környezethez a rendszergazda által biztosított környezeti URL-címmel. Az URL-címet megadhatják a böngészőjében azonnali hozzáférés céljából.

1. A rendszergazda bérlője megjelenik a vendég felhasználó számára, miután kiválasztja a profil ikont a Time Series Explorer jobb felső sarkában.

    [![Avatár kiválasztása a insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Miután a vendég felhasználó kiválasztja a rendszergazda bérlőjét, kiválaszthatja a megosztott Azure Time Series Insights környezetet.

    Most már az **5. lépésben** megadott szerepkörhöz tartozó összes képességgel rendelkeznek.

    [![A vendég felhasználó kiválasztja az Azure-bérlőt a legördülő menüből](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* Olvasási [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md) Azure Active Directory alkalmazás regisztrációs lépéseihez.

* Tekintse [meg a környezetet a Azure Time Series Insights Explorerben](./concepts-ux-panels.md).
