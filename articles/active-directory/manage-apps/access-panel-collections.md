---
title: Gyűjtemények létrehozása a saját alkalmazások portálok számára Azure Active Directoryban | Microsoft Docs
description: A saját alkalmazások gyűjteményekkel testre szabhatja az alkalmazások lapjait a végfelhasználók számára az alkalmazások egyszerűbb felhasználói élménye érdekében. Az alkalmazások csoportokba rendezhetők külön lapokkal.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7743a1af54b01f848c4ac6f0cb1d4526d66f8132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254982"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Gyűjtemények létrehozása a Saját alkalmazások portálon

A felhasználók a saját alkalmazások portálon tekinthetik meg és indíthatják el azokat a felhőalapú alkalmazásokat, amelyekhez hozzáféréssel rendelkeznek. Alapértelmezés szerint az összes felhasználó számára elérhető alkalmazás egyetlen oldalon van felsorolva. Ha prémium szintű Azure AD P1 vagy P2 licenccel rendelkezik a felhasználók számára, hogy jobban meg tudja szervezni ezt a lapot, létrehozhat gyűjteményeket. A Gyűjtemény segítségével csoportosíthatja a kapcsolódó alkalmazásokat (például feladat-szerepkör, feladat vagy projekt), és külön lapon jelenítheti meg őket. A gyűjtemény lényegében egy szűrőt alkalmaz a felhasználók által már elérhető alkalmazásokra, így a felhasználó csak azokat az alkalmazásokat látja, amelyeket a gyűjteményhez rendeltek.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan engedélyezheti és hozhat létre gyűjteményeket a rendszergazda. További információ a végfelhasználók számára a saját alkalmazások portál és gyűjtemények használatáról: a [gyűjtemények elérése és használata](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>A legújabb saját alkalmazások funkcióinak engedélyezése

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be felhasználói rendszergazdaként vagy globális rendszergazdaként.

2. Lépjen **Azure Active Directory**  >  **felhasználói beállítások menüpontra**.

3. A **felhasználói** szolgáltatások előzetesei területen válassza a **felhasználói szolgáltatás előnézeti beállításainak kezelése** lehetőséget.

4. A **felhasználók a saját alkalmazások előnézeti funkcióit használhatják** a következő lehetőségek közül:
   * **Kiválasztott** – egy adott csoport funkcióinak engedélyezése. A **csoport kiválasztása** lehetőség kiválasztásával válassza ki azt a csoportot, amelyhez engedélyezni kívánja a szolgáltatásokat.  
   * **All** – engedélyezi az összes felhasználó funkcióit.

> [!NOTE]
> A saját alkalmazások portál megnyitásához a felhasználók használhatják a hivatkozást `https://myapps.microsoft.com` vagy a testre szabott hivatkozást a szervezet számára, például: `https://myapps.microsoft.com/contoso.com` . Miután engedélyezte az új saját alkalmazások felületét, a **frissített My Applications szolgáltatás elérhető** szalagcím **jelenik meg a** saját alkalmazások oldal tetején, a felhasználók pedig kiválaszthatják, hogy megtekintsék az új felhasználói élményt. Az új felület használatának leállításához a felhasználók az **új élmény kihagyása** szalagcímből választhatják az **Igen** lehetőséget az oldal tetején.

## <a name="create-a-collection"></a>Gyűjtemény létrehozása

Gyűjtemény létrehozásához rendelkeznie kell egy prémium szintű Azure AD P1 vagy P2 licenccel.

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be rendszergazdaként egy prémium szintű Azure ad P1 vagy P2 licenccel.

2. Nyissa meg **Azure Active Directory**  >  **vállalati alkalmazásokat**.

3. A **kezelés** területen válassza a **gyűjtemények** elemet.

4. Válassza az **új gyűjtemény** lehetőséget. Az **új gyűjtemény** lapon adja meg a gyűjtemény **nevét** (azt javasoljuk, hogy ne használja a "Collection" nevet a névben. Ezután adjon meg egy **leírást**.

   ![Új gyűjtemény lap](media/acces-panel-collections/new-collection.png)

5. Válassza az **alkalmazások** fület. Válassza az **+ alkalmazás hozzáadása** lehetőséget, majd az **Alkalmazások hozzáadása** oldalon válassza ki az összes olyan alkalmazást, amelyet hozzá szeretne adni a gyűjteményhez, vagy használja a **keresőmezőt** az alkalmazások kereséséhez.

   ![Alkalmazás hozzáadása a gyűjteményhez](media/acces-panel-collections/add-applications.png)

6. Ha elkészült az alkalmazások hozzáadásával, válassza a **Hozzáadás** lehetőséget. Megjelenik a kiválasztott alkalmazások listája. A felfelé mutató nyíl használatával módosíthatja a listában szereplő alkalmazások sorrendjét. Ha lejjebb szeretné helyezni az alkalmazást, vagy törölni szeretné a gyűjteményből, válassza a **továbbiak** menüt (**...**).

7. Válassza a **tulajdonosok** lapot. Válassza a **+ felhasználók és csoportok hozzáadása** lehetőséget, majd a **felhasználók és csoportok hozzáadása** lapon válassza ki azokat a felhasználókat vagy csoportokat, amelyekhez hozzá szeretné rendelni a tulajdonost. Ha végzett a felhasználók és csoportok kijelölésével, válassza a **kiválasztás** lehetőséget.

9. Válassza a **felhasználók és csoportok** lapot. Válassza a **+ felhasználók és csoportok hozzáadása** lehetőséget, majd a **felhasználók és csoportok hozzáadása** lapon válassza ki azokat a felhasználókat vagy csoportokat, amelyekhez hozzá szeretné rendelni a gyűjteményt. Vagy használja a **keresőmezőt** a felhasználók vagy csoportok kereséséhez. Ha végzett a felhasználók és csoportok kijelölésével, válassza a **kiválasztás** lehetőséget.

   ![Felhasználók és csoportok hozzáadása](media/acces-panel-collections/add-users-and-groups.png)

11. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Megjelenik az új gyűjtemény tulajdonságai.


## <a name="view-audit-logs"></a>Auditnaplók megtekintése

A naplók rögzítik az alkalmazás-gyűjtemények műveleteit, beleértve a gyűjtemény létrehozásának végfelhasználói műveleteit is. Az alkalmazásokból a következő események jönnek létre:

* Gyűjtemény létrehozása
* Gyűjtemény szerkesztése
* Gyűjtemény törlése
* Alkalmazás elindítása (végfelhasználó)
* Önkiszolgáló alkalmazás hozzáadása (végfelhasználó)
* Önkiszolgáló alkalmazás törlése (végfelhasználó)

A naplókat a [Azure Portal](https://portal.azure.com) **Azure Active Directory** vállalati alkalmazások naplóinak kiválasztásával érheti el  >    >   a tevékenység szakaszban. A **szolgáltatás** területen válassza **a saját alkalmazások** lehetőséget.

## <a name="get-support-for-my-account-pages"></a>Fiókhoz tartozó lapok támogatásának beolvasása

A My apps (saját alkalmazások) lapon a felhasználó **kiválaszthatja a fiókom**  >  **nézet** saját fiók lehetőséget a Fiókbeállítások megnyitásához. **Az Azure ad fiókom** oldalon a felhasználók kezelhetik biztonsági adataikat, eszközeiket, jelszavait és egyebeket. Az Office-fiókok beállításait is elérheti.

Ha támogatási kérelmet kell benyújtania az Azure AD-fiók vagy az Office-fiók oldalával kapcsolatos problémához, kövesse az alábbi lépéseket, hogy a kérés megfelelően legyen irányítva: 

* Az **Azure ad "Fiókom"** oldalával kapcsolatos problémák esetén nyisson meg egy támogatási kérést a Azure Portalon belül. Nyissa meg **Azure Portal**  >  **Azure Active Directory**  >  **új támogatási kérést**.

* Az **Office "Fiókom"** oldalával kapcsolatos problémák esetén nyisson meg egy támogatási kérést a Microsoft 365 felügyeleti központban. Lépjen a **Microsoft 365 felügyeleti központ**  >  **támogatási szolgálatához**. 

## <a name="next-steps"></a>Következő lépések
[A Azure Active Directory alkalmazások végfelhasználói élményei](end-user-experiences.md)