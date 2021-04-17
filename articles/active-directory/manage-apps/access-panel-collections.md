---
title: Gyűjtemények létrehozása Saját alkalmazások portálhoz a Azure Active Directory | Microsoft Docs
description: A Saját alkalmazások segítségével testre szabhatja a Saját alkalmazások oldalakat, így egyszerűbben Saját alkalmazások a végfelhasználók számára. Alkalmazások csoportokba rendezése külön lapokkal.
services: active-directory
documentationcenter: ''
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc79e8026cb91b8ef3eac2addbb097b9db83afa7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377683"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Gyűjtemények létrehozása a Saját alkalmazások portálon

A felhasználók a Saját alkalmazások portálon megtekinthetik és elindítják a felhőalapú alkalmazásokat, amelyekhez hozzáféréssel rendelkezik. Alapértelmezés szerint a felhasználó által elérhető összes alkalmazás egyetlen lapon van felsorolva. Ha P1 vagy P2 licenccel rendelkezik prémium szintű Azure AD, akkor gyűjteményeket állíthat be, hogy jobban rendszerezze ezt az oldalt a felhasználók számára, ha P1 vagy P2 licenccel rendelkezik. A gyűjteményekkel csoportosíthatja a kapcsolódó alkalmazásokat (például feladatszerepkére, tevékenységére vagy projektjére), és megjelenítheti őket egy külön lapon. A gyűjtemény lényegében szűrőt alkalmaz azokra az alkalmazásokra, amelyekhez a felhasználó már hozzáférhet, így a felhasználó csak azokat az alkalmazásokat látja a gyűjteményben, amelyek hozzá vannak rendelve.

> [!NOTE]
> Ez a cikk bemutatja, hogyan engedélyezhetik és hozhatnak létre gyűjteményeket a rendszergazdák. A végfelhasználóknak a portál és a gyűjtemények használatával kapcsolatos Saját alkalmazások lásd: [Gyűjtemények elérése és használata.](../user-help/my-applications-portal-workspaces.md)

## <a name="enable-the-latest-my-apps-features"></a>A legújabb Saját alkalmazások engedélyezése

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be felhasználói rendszergazdaként vagy globális rendszergazdaként.

2. A Felhasználói **Azure Active Directory**  >  **lapra.**

3. A **Felhasználói funkciók előnézete alatt** válassza a Felhasználói funkciók **előnézeti beállításainak kezelése lehetőséget.**

4. A **Users can use preview features for Saját alkalmazások**(A felhasználók használhatnak előzetes verziójú funkciókat a Saját alkalmazások) alatt válasszon az alábbi lehetőségek közül:
   * **Kiválasztva** – Engedélyezi a funkciókat egy adott csoport számára. A **Csoport kiválasztása lehetőséggel** válassza ki azt a csoportot, amelyhez engedélyezni szeretné a szolgáltatásokat.  
   * **All** (Mind) – Engedélyezi a funkciókat az összes felhasználó számára.

> [!NOTE]
> A Saját alkalmazások megnyitásához a felhasználók a szervezet hivatkozását vagy testreszabott hivatkozását (például) `https://myapps.microsoft.com` is `https://myapps.microsoft.com/contoso.com` használhatják. Az új Saját alkalmazások engedélyezése után az Saját alkalmazások oldal tetején megjelenik az An **updated My Applications** experience is available (Frissített saját alkalmazások elérhetővé válik) szalagcím, és a felhasználók a Try **it** (Kipróbálom) választva megtekinthetik az új felhasználói élményt. Az új felhasználói élmény használatának  leállítása érdekében a felhasználók az Oldal tetején található **Új** felhasználói élmény elhagyva szalagcím igen gombot választva állíthatják be.

## <a name="create-a-collection"></a>Gyűjtemény létrehozása

Gyűjtemény létrehozásához P1 vagy P2 prémium szintű Azure AD kell lennie.

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be rendszergazdaként P1 vagy P2 prémium szintű Azure AD licenccel.

2. Ugrás a **Azure Active Directory**  >  **alkalmazásokra.**

3. A **Kezelés alatt** válassza a **Gyűjtemények lehetőséget.**

4. Válassza **az Új gyűjtemény lehetőséget.** Az Új **gyűjtemény lapon** adja **meg** a gyűjtemény nevét (javasoljuk, hogy ne használja a "gyűjtemény" nevet a névben. Ezután adjon meg egy **Leírást.**

   ![Új gyűjtemény lap](media/acces-panel-collections/new-collection.png)

5. Válassza az **Alkalmazások** lapot. Válassza **az + Alkalmazás hozzáadása** lehetőséget, majd az Alkalmazások hozzáadása lapon jelölje ki a  gyűjteményhez hozzáadni kívánt összes alkalmazást, vagy használja a Keresőmezőt az alkalmazások kereséséhez. 

   ![Alkalmazás hozzáadása a gyűjteményhez](media/acces-panel-collections/add-applications.png)

6. Ha végzett az alkalmazások hozzáadásával, válassza a Hozzáadás **lehetőséget.** Megjelenik a kiválasztott alkalmazások listája. A felfelé mutató nyilakkal módosíthatja a listában lévő alkalmazások sorrendjét. Egy alkalmazás lejjebb mozgatához vagy a gyűjteményből való törléséhez válassza a **További** menü (**... )** lehetőséget.

7. Válassza a **Tulajdonosok** lapot. Válassza **a + Felhasználók és** csoportok  hozzáadása lehetőséget, majd a Felhasználók és csoportok hozzáadása lapon válassza ki azokat a felhasználókat vagy csoportokat, amelyekhez tulajdonosi jogokat szeretne rendelni. Ha végzett a felhasználók és csoportok kiválasztásával, válassza a **Kiválasztás lehetőséget.**

9. Válassza a **Felhasználók és csoportok lapot.** Válassza **a + Felhasználók és** csoportok  hozzáadása lehetőséget, majd a Felhasználók és csoportok hozzáadása lapon válassza ki azokat a felhasználókat vagy csoportokat, amelyekhez hozzá szeretné rendelni a gyűjteményt. Vagy a Keresőmező **használatával** felhasználókat vagy csoportokat kereshet meg. Ha végzett a felhasználók és csoportok kiválasztásával, válassza a **Kiválasztás lehetőséget.**

   ![Felhasználók és csoportok hozzáadása](media/acces-panel-collections/add-users-and-groups.png)

11. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Megjelenik az új gyűjtemény tulajdonságai.


## <a name="view-audit-logs"></a>Auditnaplók megtekintése

Az auditnaplók Saját alkalmazások a gyűjteményműveleteket, beleértve a gyűjtemény-létrehozási végfelhasználói műveleteket is. A következő események jönnek létre a Saját alkalmazások:

* Gyűjtemény létrehozása
* Gyűjtemény szerkesztése
* Gyűjtemény törlése
* Alkalmazás indítása (végfelhasználó)
* Önkiszolgáló alkalmazás hozzáadása (végfelhasználó)
* Önkiszolgáló alkalmazástörlés (végfelhasználó)

Az auditnaplókat a [](https://portal.azure.com) Azure Portal a Tevékenység Azure Active Directory Enterprise Applications Audit logs (Vállalati alkalmazások auditnaplói)  >    >   lehetőség kiválasztásával. A **Service (Szolgáltatás)** mezőben válassza a **Saját alkalmazások** lehetőséget.

## <a name="get-support-for-my-account-pages"></a>Támogatás a Saját fiók oldalakhoz

A Saját alkalmazások a Saját fiók megtekintése lehetőséget választva megnyithatja a  >   fiókbeállításokat. Az Azure AD **Saját fiók oldalon** a felhasználók kezelhetik a biztonsági adatokat, az eszközöket, a jelszavakat stb. Az Office-fiók beállításait is elérhetik.

Ha támogatási kérést kell benyújtania az Azure AD-fiók oldalával vagy az Office-fiók oldalával kapcsolatban, kövesse az alábbi lépéseket a kérés megfelelő útválasztásához: 

* Az Azure **AD "Saját fiók"** oldalával kapcsolatos problémák esetén nyisson meg egy támogatási kérést a Azure Portal. Ugrás az **Azure Portal**  >  **Azure Active Directory**  >  **támogatási kérelemre.**

* Az Office **"Saját fiók"** oldalával kapcsolatos problémák esetén nyisson meg egy támogatási kérést a Microsoft 365 Felügyeleti központ. Ugrás a  >  **Microsoft 365 Felügyeleti központ:**. 

## <a name="next-steps"></a>Következő lépések
[Végfelhasználói élmények a Azure Active Directory](end-user-experiences.md)