---
title: Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálatot az Azure Resource roles szolgáltatásban Azure AD Privileged Identity Management (PIM) alkalmazásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 110a94c78427087f4ca5555f59055ab8e3bebcee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592671"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management

A Kiemelt Azure-beli erőforrás-szerepkörökhöz való hozzáférésre van szükség az alkalmazottak változásainak időbeli változása miatt. Az elavult szerepkör-hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében rendszeresen tekintse át a hozzáférést. A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával hozzáférési felülvizsgálatokat hozhat létre az Azure-erőforrás szerepköreihez való jogosultsági szintű hozzáféréshez. Az ismétlődő hozzáférési felülvizsgálatokat is konfigurálhatja, amelyek automatikusan megtörténnek. Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot.

## <a name="prerequisite-role"></a>Előfeltételként szükséges szerepkör

 Hozzáférési felülvizsgálatok létrehozásához hozzá kell rendelni a [tulajdonos](../../role-based-access-control/built-in-roles.md#owner) vagy a [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) Azure-szerepkört az erőforráshoz.

## <a name="open-access-reviews"></a>Hozzáférési felülvizsgálatok megnyitása

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki hozzá van rendelve az egyik előfeltételi szerepkörhöz.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. A bal oldali menüben válassza az **Azure-erőforrások** lehetőséget.

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést.

1. A kezelés területen válassza a **hozzáférési felülvizsgálatok** lehetőséget.

    ![Azure-erőforrások – hozzáférési felülvizsgálatok listája az összes felülvizsgálat állapotának megjelenítéséhez](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. Új hozzáférési felülvizsgálat létrehozásához kattintson az **új** elemre.

1. Nevezze el a hozzáférési felülvizsgálatot. Szükség esetén adja meg a Leírás áttekintését. A név és a leírás a felülvizsgálók számára jelenik meg.

    ![Hozzáférési felülvizsgálat létrehozása – a név és a Leírás áttekintése](./media/pim-resource-roles-start-access-review/name-description.png)

1. Állítsa be a **kezdő dátumot**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer is megtörténik, és egy hónapon belül megkezdődik. A kezdő és a záró dátum módosításával megkezdheti a hozzáférési felülvizsgálat megkezdését a jövőben, és több napot is megadhat.

    ![Kezdési dátum, gyakoriság, időtartam, Befejezés, ennyiszer és befejezési dátum](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődő legyen, módosítsa a **gyakoriság** beállítását **egy alkalommal** **hetente**, **havonta**, **negyedévente**, **évente** vagy **félévben**. Az **időtartam** csúszka vagy a szövegmező segítségével megadhatja, hogy a rendszer hány napig nyissa meg az ismétlődő adatsorozatok összes felülvizsgálatát a véleményezők számára. Például a havi felülvizsgálathoz beállítható maximális időtartam 27 nap, az átfedő felülvizsgálatok elkerülése érdekében.

1. A **befejezési** beállítással adhatja meg az ismétlődő hozzáférés-felülvizsgálati sorozat befejezésének módját. A sorozat három módon végződhet: a folyamatosan futtatott értékelések határozatlan ideig, egy adott dátumig vagy egy meghatározott számú előfordulás után is megkezdődhetnek. Ön, egy másik felhasználó rendszergazdája vagy egy másik globális rendszergazda állíthatja le a sorozatot a létrehozás után, hogy megváltoztatta a dátumot a **beállításokban**, hogy az adott időpontban véget vessen.

1. A **felhasználók** szakaszban válasszon ki egy vagy több olyan szerepkört, amelynek tagságát át szeretné tekinteni.

    ![Felhasználói hatókör a szerepkör tagságának áttekintéséhez](./media/pim-resource-roles-start-access-review/users.png)

    > [!NOTE]
    > - Az itt kiválasztott szerepkörök [állandó és jogosult szerepköröket](../privileged-identity-management/pim-how-to-add-role-to-user.md)is tartalmaznak.
    > - Egynél több szerepkör kiválasztásával több hozzáférési felülvizsgálat jön létre. Ha például öt szerepkört választ ki, öt külön hozzáférési felülvizsgálatot fog létrehozni.
    Ha **Azure ad-szerepkörök** hozzáférési felülvizsgálatát hozza létre, a következő példa a tagság áttekintése listára mutat.

    ![Tekintse át a tagság ablaktáblát, amely felsorolja a választható Azure AD-szerepköröket](./media/pim-resource-roles-start-access-review/review-membership.png)

    Ha az **Azure-erőforrás szerepköreinek** hozzáférési felülvizsgálatát hozza létre, a következő képen egy példa látható a tagság áttekintése listára.

    ![Tekintse át a tagság ablaktáblát, amely felsorolja a választható Azure-erőforrás-szerepköröket](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. A felülvizsgálók szakaszban válasszon ki egy vagy több személyt **az összes** felhasználó áttekintéséhez. Azt is megteheti, hogy a tagok a saját hozzáférését vizsgálják felül.

    ![Kijelölt felhasználók vagy tagok (saját) véleményezők listája](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **Kiválasztott felhasználók** – ezt a beállítást akkor használja, ha nem tudja, kinek van hozzáférése. Ezzel a beállítással a felülvizsgálatot hozzárendelheti egy erőforrás-tulajdonoshoz vagy a csoport kezelőjéhez a befejezéshez.
    - **Tagok (önálló)** – ezzel a beállítással a felhasználók áttekinthetik saját szerepkör-hozzárendeléseiket. 
    - **Kezelő** – ezzel a beállítással a felhasználó felettese áttekintheti a szerepkör-hozzárendelését. A Manager kiválasztásakor lehetősége van tartalék felülvizsgáló megadására is. A tartalék felülvizsgálók arra kérik, hogy tekintsék át a felhasználót, ha a felhasználó nem ad meg felettest a címtárban. 

### <a name="upon-completion-settings"></a>Befejezési beállítások

1. Ha meg szeretné határozni, hogy mi történik egy ellenőrzés befejezése után, bontsa ki a **befejezési beállítások** szakaszban.

    ![A befejezési beállítások automatikus alkalmazása és az ellenőrzés nem válaszol](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. Ha azt szeretné, hogy a rendszer automatikusan eltávolítsa a megtagadott felhasználók hozzáférését, állítsa be az **eredmények automatikus alkalmazása az erőforrásra** lehetőséget az **engedélyezéshez**. Ha a felülvizsgálat befejeződése után manuálisan szeretné alkalmazni az eredményeket, állítsa a kapcsolót a **Letiltás** lehetőségre.

1. Az ajánlott felülvizsgáló **nem válaszoló** listával adhatja meg, hogy mi történik azon felhasználók esetében, akiket a felülvizsgálati időszakon belül nem tekintenek át a véleményező. Ez a beállítás nem érinti azokat a felhasználókat, akiket manuálisan ellenőriztek a véleményezők. Ha megtagadja a végső felülvizsgáló döntését, a rendszer eltávolítja a felhasználó hozzáférését.

    - **Nincs változás** – a felhasználó hozzáférése változatlan marad
    - **Hozzáférés eltávolítása** – a felhasználó hozzáférésének eltávolítása
    - **Hozzáférés jóváhagyása** – a felhasználó hozzáférésének jóváhagyása
    - **Javaslatok készítése** – a rendszer javaslata a felhasználó folyamatos hozzáférésének megtagadására vagy jóváhagyására

### <a name="advanced-settings"></a>Speciális beállítások

1. További beállítások megadásához bontsa ki a **Speciális beállítások** szakaszt.

    ![Speciális beállítások a javaslatok megjelenítéséhez, indoklás kérése jóváhagyásra, e-mail értesítések és emlékeztetők](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. Állítsa be a **javaslatok megjelenítése** lehetőséget **, hogy** megjelenjenek a felülvizsgálók a felhasználó hozzáférési adatai alapján.

1. Ha **engedélyezni** szeretné, hogy a felülvizsgáló megkövetelje a jóváhagyás okát, meg kell adnia a **jóváhagyáshoz szükséges okot** .

1. E- **mail-értesítések** beállításával **engedélyezheti** , hogy az Azure ad e-mailes értesítéseket küldjön a felülvizsgálók számára a hozzáférési felülvizsgálat indításakor, valamint a rendszergazdáknak a felülvizsgálat befejezésekor.

1. Az **emlékeztetők** beállításával **engedélyezheti** , hogy az Azure ad emlékeztetőket küldjön a folyamatban lévő hozzáférési felülvizsgálatokról azon felülvizsgálók számára, akik nem fejezték be a felülvizsgálatot.
1. A felülvizsgálók számára küldött e-mailek tartalma automatikusan létrejön a felülvizsgálati adatok alapján, például a felülvizsgálat neve, az erőforrás neve, a határidő stb. Ha további információk, például további utasítások vagy kapcsolattartási adatok közlésére van szüksége, megadhatja ezeket a részleteket a **felülvizsgáló e-mail** -címéhez, amely szerepelni fog a meghívóban és a hozzárendelt felülvizsgálók számára küldött emlékeztető e-mailekben. A Kiemelt szakasz az alábbi információkat jeleníti meg.

    ![A felülvizsgálók számára a Highlights által küldött e-mailek tartalma](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat elindítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start** gombra. A hozzáférési felülvizsgálat megjelenik a listában az állapotának jelzésével.

![Az elindított felülvizsgálati állapotot megjelenítő hozzáférési felülvizsgálatok listája](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD egy e-mailt küld a felülvizsgálók számára röviddel a felülvizsgálat elindítása után. Ha úgy dönt, hogy nem szeretné elküldeni az Azure AD-t az e-mail-címre, tájékoztassa a véleményezőket arról, hogy a hozzáférési felülvizsgálat a befejezésre vár. Megtekintheti azokat az utasításokat, amelyekkel [áttekintheti az Azure-erőforrás szerepköreinek elérését](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Nyomon követheti a folyamat előrehaladását, mivel a felülvizsgálók a hozzáférési felülvizsgálat **Áttekintés** lapján tölthetik le az értékeléseket. A címtárban nem módosulnak hozzáférési jogosultságok, amíg a felülvizsgálat be nem [fejeződik](pim-resource-roles-complete-access-review.md).

![A felülvizsgálat részleteit megjelenítő hozzáférési felülvizsgálatok áttekintése lap](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Ha ez egy egyszeri felülvizsgálat, akkor a hozzáférési felülvizsgálati időszak lejárta után, vagy a rendszergazda leállítja a hozzáférési felülvizsgálatot, kövesse az [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md) az eredmények megtekintéséhez és alkalmazásához című témakör lépéseit.  

A hozzáférési felülvizsgálatok sorozatának kezeléséhez navigáljon a hozzáférési felülvizsgálathoz, és keresse meg az ütemezett felülvizsgálatok közelgő eseményeit, és szerkessze a befejezési dátumot, vagy adja hozzá/távolítsa el a felülvizsgálók ennek megfelelően.

A **befejezési beállításokban megadott beállítások** alapján az automatikus alkalmazás a felülvizsgálat befejezési dátuma vagy a felülvizsgálat manuális leállítása után lesz végrehajtva. A felülvizsgálat állapota a **befejezéstől** kezdve a közbenső állapotok, például az **alkalmazás** és végül az állapot **alkalmazásával változik.** A megtagadott felhasználókat, ha vannak ilyenek, néhány percen belül el kell távolítani a szerepkörökből.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrás szerepköreihez való hozzáférés áttekintése](pim-resource-roles-perform-access-review.md)
- [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása](pim-how-to-start-security-review.md)
