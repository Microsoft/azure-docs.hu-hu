---
title: Azure Monitor munkafüzetek használata Azure AD Domain Services használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja Azure Monitor munkafüzeteket a biztonsági naplózás áttekintéséhez és a Azure Active Directory Domain Services felügyelt tartományok problémáinak megismeréséhez.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 9a126fe4676f340fb45677382a3aeeed79291f0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574205"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Biztonsági naplózási események áttekintése Azure Active Directory Domain Services Azure Monitor munkafüzetek használatával

A Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány állapotának megismerése érdekében engedélyezheti a biztonsági naplózási eseményeket. Ezek a biztonsági naplózási események később áttekinthetők a szövegeket, elemzési lekérdezéseket és paramétereket kombináló Azure Monitor munkafüzetek gazdag interaktív jelentésekben való használatával. Az Azure AD DS tartalmaz olyan munkafüzet-sablonokat a biztonsági áttekintéshez és a fiók tevékenységekhez, amelyek lehetővé teszik a naplózási események bevezetését és a környezet kezelését.

Ez a cikk bemutatja, hogyan használhatók Azure Monitor munkafüzetek az Azure AD DS biztonsági naplózási eseményeinek áttekintéséhez.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services felügyelt tartomány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* A biztonsági naplózási események engedélyezve vannak az olyan felügyelt tartománynál, amely Log Analytics munkaterületre továbbítja az adatátvitelt.
    * Ha szükséges, [engedélyezze az Azure AD DS biztonsági naplózását][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Azure Monitor munkafüzetek áttekintése

Ha a biztonsági naplózási események be vannak kapcsolva az Azure AD DSban, nehéz lehet elemezni és azonosítani a felügyelt tartomány hibáit. Azure Monitor segítségével összesítheti ezeket a biztonsági naplózási eseményeket, és lekérdezheti az adatokat. A Azure Monitor munkafüzetek segítségével megjelenítheti ezeket az adatelemeket, hogy gyorsabban és könnyebben azonosíthatók legyenek a problémák.

A munkafüzet-sablonok olyan kurátori jelentések, amelyek több felhasználó és csapat rugalmas újrafelhasználására lettek kialakítva. Munkafüzet-sablon megnyitásakor a rendszer betölti a Azure Monitor-környezetből származó adatok betöltését. A sablonokat a szervezet más felhasználóira gyakorolt hatása nélkül is használhatja, és a sablon alapján mentheti a saját munkafüzeteit.

Az Azure AD DS a következő két munkafüzet-sablont tartalmazza:

* Biztonsági áttekintés jelentés
* Fiók tevékenységi jelentése

A munkafüzetek szerkesztésével és kezelésével kapcsolatos további információkért lásd: [Azure monitor munkafüzetek áttekintése](../azure-monitor/visualize/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>A biztonsági áttekintés jelentés munkafüzetének használata

A biztonsági áttekintési jelentés összefoglalja a bejelentkezési adatokat, és azonosítja azokat a fiókokat, amelyek segítségével jobban megismerheti a használatot, és azonosíthatja a lehetséges biztonsági fenyegetéseket. Megtekintheti egy adott dátumtartomány eseményeit, és részletezheti a megadott bejelentkezési eseményeket, például a hibás jelszavakat, illetve a fiók letiltását.

A biztonsági áttekintés jelentés munkafüzet-sablonjának eléréséhez hajtsa végre a következő lépéseket:

1. Keresse meg és válassza ki **Azure Active Directory Domain Services** a Azure Portal.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*
1. A bal oldali menüben válassza a **figyelés > munkafüzetek** lehetőséget.

    ![Képernyőkép, amely hightlights, hogy hol válassza ki a biztonsági áttekintés jelentést és a fiók tevékenységének jelentését.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Válassza a **biztonsági áttekintés jelentést**.
1. A munkafüzet tetején található legördülő menükből válassza ki az Azure-előfizetését, majd egy Azure Monitor munkaterületet.

    Válasszon egy **időtartományt**, például az *utolsó 7 napot*, ahogy az az alábbi példában látható:

    ![Válassza a munkafüzetek menü lehetőséget a Azure Portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    A **csempék** és a **diagram nézet** beállításait is módosíthatja, hogy a kívánt módon elemezze és megjelenítse az adatmegjelenítést.

1. Egy adott eseménytípus részletezéséhez válassza ki az egyik **bejelentkezési eredmény** kártyát, például a *fiók kizárva* lehetőséget, ahogy az alábbi példában is látható:

    ![Példa a biztonsági áttekintésre Azure Monitor munkafüzetekbe vizualizációzott adatjelentések](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. A diagram alatti biztonsági áttekintés jelentés alsó részében a kiválasztott tevékenységtípus megszakad. A jobb oldalon lévő felhasználónevek alapján szűrheti, ahogy az alábbi példában is látható:

    [![Azure Monitor-munkafüzetekben lévő fiókzárolási adatok részletei.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>A fiók tevékenységi jelentés munkafüzetének használata

Ha segítségre van szüksége egy adott felhasználói fiókkal kapcsolatos problémák elhárításához, akkor a fiók tevékenységi jelentése megszakítja a naplózási Eseménynapló részletes információit. Áttekintheti, hogy mikor adtak meg rossz felhasználónevet vagy jelszót a bejelentkezés során, valamint a bejelentkezési kísérlet forrását.

A fiók tevékenységi jelentéshez tartozó munkafüzet-sablon eléréséhez hajtsa végre a következő lépéseket:

1. Keresse meg és válassza ki **Azure Active Directory Domain Services** a Azure Portal.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*
1. A bal oldali menüben válassza a **figyelés > munkafüzetek** lehetőséget.
1. Válassza ki a **fiók tevékenységének jelentését**.
1. A munkafüzet tetején található legördülő menükből válassza ki az Azure-előfizetését, majd egy Azure Monitor munkaterületet.

    Válassza ki az **időtartományt**, például az *utolsó 30 napot*, majd azt, hogy a **Mozaik nézet** hogyan legyen ábrázolva az adathalmazban.

    Az alábbi példában látható módon szűrheti a **fiók felhasználónevét**, például a *Felix* nevet:

    [![Fiók tevékenységi jelentése Azure Monitor munkafüzetekben.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    A diagram alatti rész az egyes bejelentkezési eseményeket mutatja be, valamint olyan információkat, mint például a tevékenység eredménye és a forrás munkaállomás. Ezek az információk segíthetnek meghatározni a bejelentkezési események ismételt forrásait, amelyek a fiókok zárolását okozhatják, vagy potenciális támadást jelezhetnek.

A biztonsági áttekintés jelentéshez hasonlóan a jelentés tetején található különböző csempéket is megtekintheti, és szükség szerint jelenítheti meg és elemezheti az információkat.

## <a name="save-and-edit-workbooks"></a>Munkafüzetek mentése és szerkesztése

Az Azure AD DS által biztosított két sablonos munkafüzet jó kiindulópont a saját adatelemzéshez. Ha részletesebb információra van szüksége az adatlekérdezésekről és a vizsgálatokról, mentheti a saját munkafüzeteit, és szerkesztheti a lekérdezéseket.

1. Ha menteni szeretné a munkafüzetek egyik sablonjának másolatát, válassza a **szerkesztés > Mentés másként > megosztott jelentések** lehetőséget, majd adjon meg egy nevet, és mentse.
1. A sablon saját példányáról válassza a **Szerkesztés** lehetőséget a szerkesztési mód megadásához. A jelentés bármely része mellett a kék **Szerkesztés** gombot is választhatja, és módosíthatja azt.

Azure Monitor munkafüzetek összes diagramja és táblázata Kusto lekérdezések használatával jön létre. A saját lekérdezések létrehozásával kapcsolatos további információkért lásd: [Azure monitor log-lekérdezések][azure-monitor-queries] és [Kusto-lekérdezések oktatóanyaga][kusto-queries].

## <a name="next-steps"></a>Következő lépések

Ha módosítania kell a jelszó-és a zárolási házirendeket, tekintse [meg a felügyelt tartományokra vonatkozó jelszó-és fiókzárolási házirendeket][password-policy].

A felhasználókkal kapcsolatos problémák esetén megismerheti a [fiókok bejelentkezési problémáinak][troubleshoot-sign-in] vagy [fiókzárolási problémáinak][troubleshoot-account-lockout]elhárítását.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer