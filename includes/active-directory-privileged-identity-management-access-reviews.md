---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: ajburnle
ms.service: active-directory
ms.topic: include
ms.date: 12/07/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 9c91eb6cfa18c5302a83347f671e4552befcf3e2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623222"
---
## <a name="create-one-or-more-access-reviews"></a>Egy vagy több hozzáférési felülvizsgálat létrehozása

1. Új hozzáférési felülvizsgálat létrehozásához kattintson az **új** elemre.

1. Nevezze el a hozzáférési felülvizsgálatot. Szükség esetén adja meg a Leírás áttekintését. A név és a leírás a felülvizsgálók számára jelenik meg.

    ![Hozzáférési felülvizsgálat létrehozása – a név és a Leírás áttekintése](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Állítsa be a **kezdő dátumot**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer is megtörténik, és egy hónapon belül megkezdődik. A kezdő és a záró dátum módosításával megkezdheti a hozzáférési felülvizsgálat megkezdését a jövőben, és több napot is megadhat.

    ![Kezdési dátum, gyakoriság, időtartam, Befejezés, ennyiszer és befejezési dátum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődő legyen, módosítsa a **gyakoriság** beállítását **egy alkalommal** **hetente**, **havonta**, **negyedévente**, **évente** vagy **félévben**. Az **időtartam** csúszka vagy a szövegmező segítségével megadhatja, hogy a rendszer hány napig nyissa meg az ismétlődő adatsorozatok összes felülvizsgálatát a véleményezők számára. Például a havi felülvizsgálathoz beállítható maximális időtartam 27 nap, az átfedő felülvizsgálatok elkerülése érdekében.

1. A **befejezési** beállítással adhatja meg az ismétlődő hozzáférés-felülvizsgálati sorozat befejezésének módját. A sorozat három módon végződhet: a folyamatosan futtatott értékelések határozatlan ideig, egy adott dátumig vagy egy meghatározott számú előfordulás után is megkezdődhetnek. Ön, egy másik felhasználó rendszergazdája vagy egy másik globális rendszergazda állíthatja le a sorozatot a létrehozás után, hogy megváltoztatta a dátumot a **beállításokban**, hogy az adott időpontban véget vessen.

1. A **felhasználók** szakaszban válasszon ki egy vagy több olyan szerepkört, amelynek tagságát át szeretné tekinteni.

    ![Felhasználói hatókör a szerepkör tagságának áttekintéséhez](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - Az itt kiválasztott szerepkörök [állandó és jogosult szerepköröket](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)is tartalmaznak.
    > - Egynél több szerepkör kiválasztásával több hozzáférési felülvizsgálat jön létre. Ha például öt szerepkört választ ki, öt külön hozzáférési felülvizsgálatot fog létrehozni.
    > - A hozzájuk rendelt csoportokkal rendelkező szerepkörök esetében a hozzáférés felülvizsgálatának részeként a felülvizsgálat alatt álló szerepkörhöz csatolt egyes csoportok hozzáférését a rendszer felülvizsgálja.

    Ha **Azure ad-szerepkörök** hozzáférési felülvizsgálatát hozza létre, a következő példa a tagság áttekintése listára mutat.

    ![Tekintse át a tagság ablaktáblát, amely felsorolja a választható Azure AD-szerepköröket](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Ha az **Azure-erőforrás szerepköreinek** hozzáférési felülvizsgálatát hozza létre, a következő képen egy példa látható a tagság áttekintése listára.

    ![Tekintse át a tagság ablaktáblát, amely felsorolja a választható Azure-erőforrás-szerepköröket](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. A felülvizsgálók szakaszban válasszon ki egy vagy több személyt **az összes** felhasználó áttekintéséhez. Azt is megteheti, hogy a tagok a saját hozzáférését vizsgálják felül.

    ![Kijelölt felhasználók vagy tagok (saját) véleményezők listája](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Kiválasztott felhasználók** – ezt a beállítást akkor használja, ha nem tudja, kinek van hozzáférése. Ezzel a beállítással a felülvizsgálatot hozzárendelheti egy erőforrás-tulajdonoshoz vagy a csoport kezelőjéhez a befejezéshez.
    - **Tagok (önálló)** – ezzel a beállítással a felhasználók áttekinthetik saját szerepkör-hozzárendeléseiket. A szerepkörhöz hozzárendelt csoportok nem lesznek a felülvizsgálat részét képezik, ha ez a beállítás ki van választva.
    - **Kezelő** – ezzel a beállítással a felhasználó felettese áttekintheti a szerepkör-hozzárendelését. A Manager kiválasztásakor lehetősége van tartalék felülvizsgáló megadására is. A tartalék felülvizsgálók arra kérik, hogy tekintsék át a felhasználót, ha a felhasználó nem ad meg felettest a címtárban. A szerepkörhöz rendelt csoportokat a tartalék felülvizsgáló is ellenőrzi, ha van ilyen. 

### <a name="upon-completion-settings"></a>Befejezési beállítások

1. Ha meg szeretné határozni, hogy mi történik egy ellenőrzés befejezése után, bontsa ki a **befejezési beállítások** szakaszban.

    ![A befejezési beállítások automatikus alkalmazása és az ellenőrzés nem válaszol](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Ha azt szeretné, hogy a rendszer automatikusan eltávolítsa a megtagadott felhasználók hozzáférését, állítsa be az **eredmények automatikus alkalmazása az erőforrásra** lehetőséget az **engedélyezéshez**. Ha a felülvizsgálat befejeződése után manuálisan szeretné alkalmazni az eredményeket, állítsa a kapcsolót a **Letiltás** lehetőségre.

1. Az ajánlott felülvizsgáló **nem válaszoló** listával adhatja meg, hogy mi történik azon felhasználók esetében, akiket a felülvizsgálati időszakon belül nem tekintenek át a véleményező. Ez a beállítás nem érinti azokat a felhasználókat, akiket manuálisan ellenőriztek a véleményezők. Ha megtagadja a végső felülvizsgáló döntését, a rendszer eltávolítja a felhasználó hozzáférését.

    - **Nincs változás** – a felhasználó hozzáférése változatlan marad
    - **Hozzáférés eltávolítása** – a felhasználó hozzáférésének eltávolítása
    - **Hozzáférés jóváhagyása** – a felhasználó hozzáférésének jóváhagyása
    - **Javaslatok készítése** – a rendszer javaslata a felhasználó folyamatos hozzáférésének megtagadására vagy jóváhagyására

### <a name="advanced-settings"></a>Speciális beállítások

1. További beállítások megadásához bontsa ki a **Speciális beállítások** szakaszt.

    ![Speciális beállítások a javaslatok megjelenítéséhez, indoklás kérése jóváhagyásra, e-mail értesítések és emlékeztetők](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Állítsa be a **javaslatok megjelenítése** lehetőséget **, hogy** megjelenjenek a felülvizsgálók a felhasználó hozzáférési adatai alapján.

1. Ha **engedélyezni** szeretné, hogy a felülvizsgáló megkövetelje a jóváhagyás okát, meg kell adnia a **jóváhagyáshoz szükséges okot** .

1. E- **mail-értesítések** beállításával **engedélyezheti** , hogy az Azure ad e-mailes értesítéseket küldjön a felülvizsgálók számára a hozzáférési felülvizsgálat indításakor, valamint a rendszergazdáknak a felülvizsgálat befejezésekor.

1. Az **emlékeztetők** beállításával **engedélyezheti** , hogy az Azure ad emlékeztetőket küldjön a folyamatban lévő hozzáférési felülvizsgálatokról azon felülvizsgálók számára, akik nem fejezték be a felülvizsgálatot.
1. A felülvizsgálók számára küldött e-mailek tartalma automatikusan létrejön a felülvizsgálati adatok alapján, például a felülvizsgálat neve, az erőforrás neve, a határidő stb. Ha további információk, például további utasítások vagy kapcsolattartási adatok közlésére van szüksége, megadhatja ezeket a részleteket a **felülvizsgáló e-mail** -címéhez, amely szerepelni fog a meghívóban és a hozzárendelt felülvizsgálók számára küldött emlékeztető e-mailekben. A Kiemelt szakasz az alábbi információkat jeleníti meg.

    ![A felülvizsgálók számára a Highlights által küldött e-mailek tartalma](./media/active-directory-privileged-identity-management-access-reviews/email-info.png)