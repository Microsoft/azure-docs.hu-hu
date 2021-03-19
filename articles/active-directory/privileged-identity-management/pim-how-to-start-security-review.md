---
title: Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan hozhatja létre az Azure AD-szerepkörök hozzáférési felülvizsgálatát Azure AD Privileged Identity Management (PIM) alkalmazásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 3/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 310122177d4bd1603f5f498aa2a51620eeda4a20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592796"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management

Az elavult szerepkör-hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében rendszeresen tekintse át a hozzáférést. Azure AD Privileged Identity Management (PIM) használatával hozzáférési felülvizsgálatokat hozhat létre az emelt szintű Azure AD-szerepkörökhöz. Az ismétlődő hozzáférési felülvizsgálatokat is konfigurálhatja, amelyek automatikusan megtörténnek.

Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot az emelt szintű Azure AD-szerepkörökhöz.

## <a name="prerequisites"></a>Előfeltételek

[Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Hozzáférési felülvizsgálatok megnyitása

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a Kiemelt szerepkörű rendszergazda szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** elemet.

1. A kezelés területen válassza a **hozzáférési felülvizsgálatok** lehetőséget, majd kattintson az **új** elemre.

    ![Azure AD-szerepkörök – hozzáférési felülvizsgálatok listája az összes felülvizsgálat állapotának megjelenítéséhez](./media/pim-how-to-start-security-review/access-reviews.png)

Új hozzáférési felülvizsgálat létrehozásához kattintson az **új** elemre.

1. Nevezze el a hozzáférési felülvizsgálatot. Szükség esetén adja meg a Leírás áttekintését. A név és a leírás a felülvizsgálók számára jelenik meg.

    ![Hozzáférési felülvizsgálat létrehozása – a név és a Leírás áttekintése](./media/pim-how-to-start-security-review/name-description.png)

1. Állítsa be a **kezdő dátumot**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer is megtörténik, és egy hónapon belül megkezdődik. A kezdő és a záró dátum módosításával megkezdheti a hozzáférési felülvizsgálat megkezdését a jövőben, és több napot is megadhat.

    ![Kezdési dátum, gyakoriság, időtartam, Befejezés, ennyiszer és befejezési dátum](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődő legyen, módosítsa a **gyakoriság** beállítását **egy alkalommal** **hetente**, **havonta**, **negyedévente**, **évente** vagy **félévben**. Az **időtartam** csúszka vagy a szövegmező segítségével megadhatja, hogy a rendszer hány napig nyissa meg az ismétlődő adatsorozatok összes felülvizsgálatát a véleményezők számára. Például a havi felülvizsgálathoz beállítható maximális időtartam 27 nap, az átfedő felülvizsgálatok elkerülése érdekében.

1. A **befejezési** beállítással adhatja meg az ismétlődő hozzáférés-felülvizsgálati sorozat befejezésének módját. A sorozat három módon végződhet: a folyamatosan futtatott értékelések határozatlan ideig, egy adott dátumig vagy egy meghatározott számú előfordulás után is megkezdődhetnek. Ön, egy másik felhasználó rendszergazdája vagy egy másik globális rendszergazda állíthatja le a sorozatot a létrehozás után, hogy megváltoztatta a dátumot a **beállításokban**, hogy az adott időpontban véget vessen.

1. A **felhasználók** szakaszban válasszon ki egy vagy több olyan szerepkört, amelynek tagságát át szeretné tekinteni.

    ![Felhasználói hatókör a szerepkör tagságának áttekintéséhez](./media/pim-how-to-start-security-review/users.png)

    > [!NOTE]
    > - Az itt kiválasztott szerepkörök [állandó és jogosult szerepköröket](../privileged-identity-management/pim-how-to-add-role-to-user.md)is tartalmaznak.
    > - Egynél több szerepkör kiválasztásával több hozzáférési felülvizsgálat jön létre. Ha például öt szerepkört választ ki, öt külön hozzáférési felülvizsgálatot fog létrehozni.
    > - A hozzájuk rendelt csoportokkal rendelkező szerepkörök esetében a hozzáférés felülvizsgálatának részeként a felülvizsgálat alatt álló szerepkörhöz csatolt egyes csoportok hozzáférését a rendszer felülvizsgálja.
    Ha **Azure ad-szerepkörök** hozzáférési felülvizsgálatát hozza létre, a következő példa a tagság áttekintése listára mutat.

    ![Tekintse át a tagság ablaktáblát, amely felsorolja a választható Azure AD-szerepköröket](./media/pim-how-to-start-security-review/review-membership.png)

    Ha az **Azure-erőforrás szerepköreinek** hozzáférési felülvizsgálatát hozza létre, a következő képen egy példa látható a tagság áttekintése listára.

    ![Tekintse át a tagság ablaktáblát, amely felsorolja a választható Azure-erőforrás-szerepköröket](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. A felülvizsgálók szakaszban válasszon ki egy vagy több személyt **az összes** felhasználó áttekintéséhez. Azt is megteheti, hogy a tagok a saját hozzáférését vizsgálják felül.

    ![Kijelölt felhasználók vagy tagok (saját) véleményezők listája](./media/pim-how-to-start-security-review/reviewers.png)

    - **Kiválasztott felhasználók** – ezt a beállítást akkor használja, ha nem tudja, kinek van hozzáférése. Ezzel a beállítással a felülvizsgálatot hozzárendelheti egy erőforrás-tulajdonoshoz vagy a csoport kezelőjéhez a befejezéshez.
    - **Tagok (önálló)** – ezzel a beállítással a felhasználók áttekinthetik saját szerepkör-hozzárendeléseiket. A szerepkörhöz hozzárendelt csoportok nem lesznek a felülvizsgálat részét képezik, ha ez a beállítás ki van választva.
    - **Kezelő** – ezzel a beállítással a felhasználó felettese áttekintheti a szerepkör-hozzárendelését. A Manager kiválasztásakor lehetősége van tartalék felülvizsgáló megadására is. A tartalék felülvizsgálók arra kérik, hogy tekintsék át a felhasználót, ha a felhasználó nem ad meg felettest a címtárban. A szerepkörhöz rendelt csoportokat a tartalék felülvizsgáló is ellenőrzi, ha van ilyen. 

### <a name="upon-completion-settings"></a>Befejezési beállítások

1. Ha meg szeretné határozni, hogy mi történik egy ellenőrzés befejezése után, bontsa ki a **befejezési beállítások** szakaszban.

    ![A befejezési beállítások automatikus alkalmazása és az ellenőrzés nem válaszol](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Ha azt szeretné, hogy a rendszer automatikusan eltávolítsa a megtagadott felhasználók hozzáférését, állítsa be az **eredmények automatikus alkalmazása az erőforrásra** lehetőséget az **engedélyezéshez**. Ha a felülvizsgálat befejeződése után manuálisan szeretné alkalmazni az eredményeket, állítsa a kapcsolót a **Letiltás** lehetőségre.

1. Az ajánlott felülvizsgáló **nem válaszoló** listával adhatja meg, hogy mi történik azon felhasználók esetében, akiket a felülvizsgálati időszakon belül nem tekintenek át a véleményező. Ez a beállítás nem érinti azokat a felhasználókat, akiket manuálisan ellenőriztek a véleményezők. Ha megtagadja a végső felülvizsgáló döntését, a rendszer eltávolítja a felhasználó hozzáférését.

    - **Nincs változás** – a felhasználó hozzáférése változatlan marad
    - **Hozzáférés eltávolítása** – a felhasználó hozzáférésének eltávolítása
    - **Hozzáférés jóváhagyása** – a felhasználó hozzáférésének jóváhagyása
    - **Javaslatok készítése** – a rendszer javaslata a felhasználó folyamatos hozzáférésének megtagadására vagy jóváhagyására

### <a name="advanced-settings"></a>Speciális beállítások

1. További beállítások megadásához bontsa ki a **Speciális beállítások** szakaszt.

    ![Speciális beállítások a javaslatok megjelenítéséhez, indoklás kérése jóváhagyásra, e-mail értesítések és emlékeztetők](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Állítsa be a **javaslatok megjelenítése** lehetőséget **, hogy** megjelenjenek a felülvizsgálók a felhasználó hozzáférési adatai alapján.

1. Ha **engedélyezni** szeretné, hogy a felülvizsgáló megkövetelje a jóváhagyás okát, meg kell adnia a **jóváhagyáshoz szükséges okot** .

1. E- **mail-értesítések** beállításával **engedélyezheti** , hogy az Azure ad e-mailes értesítéseket küldjön a felülvizsgálók számára a hozzáférési felülvizsgálat indításakor, valamint a rendszergazdáknak a felülvizsgálat befejezésekor.

1. Az **emlékeztetők** beállításával **engedélyezheti** , hogy az Azure ad emlékeztetőket küldjön a folyamatban lévő hozzáférési felülvizsgálatokról azon felülvizsgálók számára, akik nem fejezték be a felülvizsgálatot.
1. A felülvizsgálók számára küldött e-mailek tartalma automatikusan létrejön a felülvizsgálati adatok alapján, például a felülvizsgálat neve, az erőforrás neve, a határidő stb. Ha további információk, például további utasítások vagy kapcsolattartási adatok közlésére van szüksége, megadhatja ezeket a részleteket a **felülvizsgáló e-mail** -címéhez, amely szerepelni fog a meghívóban és a hozzárendelt felülvizsgálók számára küldött emlékeztető e-mailekben. A Kiemelt szakasz az alábbi információkat jeleníti meg.

    ![A felülvizsgálók számára a Highlights által küldött e-mailek tartalma](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat elindítása

Miután megadta a hozzáférési felülvizsgálat beállításait, válassza az **Indítás** lehetőséget. A hozzáférési felülvizsgálat megjelenik a listában az állapotának jelzésével.

![Az elindított felülvizsgálatok állapotát megjelenítő hozzáférési felülvizsgálatok listája](./media/pim-how-to-start-security-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD egy e-mailt küld a felülvizsgálók számára röviddel a felülvizsgálat elindítása után. Ha úgy dönt, hogy nem szeretné elküldeni az Azure AD-t az e-mail-címre, tájékoztassa a véleményezőket arról, hogy a hozzáférési felülvizsgálat a befejezésre vár. Megtekintheti azokat az utasításokat, amelyekkel [ellenőrizheti az Azure ad-szerepkörök elérését](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Nyomon követheti a folyamat előrehaladását, mivel a felülvizsgálók a hozzáférési felülvizsgálat **Áttekintés** lapján tölthetik le az értékeléseket. A címtárban nem módosulnak hozzáférési jogosultságok, amíg a felülvizsgálat be nem [fejeződik](pim-how-to-complete-review.md).

![A felülvizsgálat részleteit megjelenítő hozzáférési felülvizsgálatok áttekintése lap](./media/pim-how-to-start-security-review/access-review-overview.png)

Ha ez egy egyszeri felülvizsgálat, akkor a hozzáférési felülvizsgálati időszak lejárta után, vagy a rendszergazda leállítja a hozzáférési felülvizsgálatot, kövesse az [Azure ad-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-how-to-complete-review.md) az eredmények megtekintéséhez és alkalmazásához című témakör lépéseit.  

A hozzáférési felülvizsgálatok sorozatának kezeléséhez navigáljon a hozzáférési felülvizsgálathoz, és keresse meg az ütemezett felülvizsgálatok közelgő eseményeit, és szerkessze a befejezési dátumot, vagy adja hozzá/távolítsa el a felülvizsgálók ennek megfelelően.

A **befejezési beállításokban megadott beállítások** alapján az automatikus alkalmazás a felülvizsgálat befejezési dátuma vagy a felülvizsgálat manuális leállítása után lesz végrehajtva. A felülvizsgálat állapota a **befejezéstől** kezdve a közbenső állapotok, például az **alkalmazás** és végül az állapot **alkalmazásával változik.** A megtagadott felhasználókat, ha vannak ilyenek, néhány percen belül el kell távolítani a szerepkörökből.

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök hozzáférésének áttekintése](pim-how-to-perform-security-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-how-to-complete-review.md)
- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása](pim-resource-roles-start-access-review.md)
