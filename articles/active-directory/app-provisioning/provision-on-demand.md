---
title: Igény szerinti felhasználó kiépítése Azure Active Directory használatával
description: Szinkronizálás kényszerítése
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: be03a149f34c16621905081a2f9bb663d85bc53c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255661"
---
# <a name="on-demand-provisioning"></a>Igény szerinti üzembe helyezés
Igény szerinti kiépítés használatával másodpercek alatt kiépítheti a felhasználókat egy alkalmazásba. A funkció többek között a következőkre használható:

* A konfigurációs problémák gyors elhárítása.
* Érvényesítse a definiált kifejezéseket.
* Tesztelési hatóköri szűrők.

## <a name="how-to-use-on-demand-provisioning"></a>Igény szerinti kiépítés használata

1. Jelentkezzen be az **Azure Portalra**.
1. Lépjen az **összes szolgáltatás**  >  **vállalati alkalmazáshoz**.
1. Válassza ki az alkalmazást, majd nyissa meg a létesítési konfiguráció lapot.
1. Konfigurálja úgy a kiépítés konfigurálását, hogy megadja a rendszergazdai hitelesítő adatait.
1. Válassza **a kiépítés igény szerint** lehetőséget.
1. Keressen egy felhasználót az utónév, a vezetéknév, a megjelenítendő név, az egyszerű felhasználónév vagy az e-mail-cím alapján.
   > [!NOTE]
   > A Felhőbeli HR-kiépítési alkalmazásokhoz (munkanap/SuccessFactors AD/Azure AD) a bemeneti érték eltér. A munkanap forgatókönyv esetén adja meg a felhasználó "WID" számát a munkanapokon. SuccessFactors forgatókönyv esetén adja meg a felhasználó "personIdExternal" a SuccessFactors-ben. 
 
1. Válassza a **kiépítés** elemet az oldal alján.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Képernyőfelvétel: a felhasználó igény szerinti üzembe helyezésének Azure Portal felhasználói felületét jeleníti meg.":::

## <a name="understand-the-provisioning-steps"></a>A kiépítési lépések ismertetése

Az igény szerinti kiépítési folyamat megkísérli megmutatni, hogy a kiépítési szolgáltatás milyen lépéseket hajtson végre a felhasználók kiépítés során. A felhasználók kiépítése általában öt lépésből áll. A következő részekben ismertetett lépések közül egy vagy több az igény szerinti üzembe helyezési élményben jelenik meg.

### <a name="step-1-test-connection"></a>1. lépés: a kapcsolatok tesztelése

A kiépítési szolgáltatás megkísérli engedélyezni a hozzáférést a célalkalmazás számára egy "tesztelési felhasználó" kérésének megtételével. A kiépítési szolgáltatás olyan választ vár, amely azt jelzi, hogy a szolgáltatás a kiépítési lépésekkel való folytatásra van engedélyezve. Ez a lépés csak akkor jelenik meg, ha sikertelen. Az igény szerinti üzembe helyezés során nem jelenik meg, amikor a lépés sikeres.

#### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

* Győződjön meg arról, hogy érvényes hitelesítő adatokat adott meg, például a titkos jogkivonatot és a bérlői URL-címet a célalkalmazás számára. A szükséges hitelesítő adatok az alkalmazástól eltérőek. A részletes konfigurációs oktatóanyagokat az [oktatóanyag listáját](../saas-apps/tutorial-list.md)ismertető szakaszban tekintheti meg. 
* Győződjön meg arról, hogy a célalkalmazás támogatja a szűrést az **attribútum-hozzárendelések** ablaktáblán definiált egyező attribútumok esetében. Előfordulhat, hogy ellenőriznie kell az alkalmazás fejlesztője által biztosított API-dokumentációt a támogatott szűrők megismeréséhez.
* A tartományok közötti Identitáskezelés-felügyeleti (SCIM) alkalmazások esetében olyan eszközt is használhat, mint például a Poster. Ezek az eszközök segítenek biztosítani, hogy az alkalmazás az Azure Active Directory (Azure AD) kiépítési szolgáltatás által várt módon válaszoljon az engedélyezési kérelmekre. Tekintsen meg egy példát a [kérelemre](./use-scim-to-provision-users-and-groups.md#request-3).

### <a name="step-2-import-user"></a>2. lépés: felhasználó importálása

Ezután a kiépítési szolgáltatás lekéri a felhasználót a forrás rendszerből. A szolgáltatás által lekért felhasználói attribútumok később a következőre lesznek felhasználva:

* Annak kiértékelése, hogy a felhasználó hatókörben van-e az üzembe helyezéshez.
* Egy meglévő felhasználó megkeresése a megcélzott rendszeren.
* Határozza meg, hogy mely felhasználói attribútumok legyenek exportálva a célként megadott rendszerbe.

#### <a name="view-details"></a>Részletek megtekintése


A **részletek megtekintése** szakasz a forrás rendszerből (például az Azure ad-ből) importált felhasználó tulajdonságait jeleníti meg.

#### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

* A felhasználó importálása sikertelen lehet, ha a forrásoldali rendszer felhasználói objektumában hiányzik a megfelelő attribútum. A hiba elhárításához próbálja ki a következő módszerek egyikét:

  * Frissítse a felhasználói objektumot a megfelelő attribútum értékével.
  * Módosítsa a megfelelő attribútumot a létesítési konfigurációban.

* Ha egy várt attribútum hiányzik az importált listából, győződjön meg arról, hogy az attribútum értéke a forrásoldali rendszer felhasználói objektumában van. A kiépítési szolgáltatás jelenleg nem támogatja a null attribútumok üzembe helyezését.
* Győződjön meg arról, hogy a kiépítési konfiguráció **attribútum-hozzárendelés** lapja tartalmazza a várt attribútumot.

### <a name="step-3-determine-if-user-is-in-scope"></a>3. lépés: annak megállapítása, hogy a felhasználó hatókörben van-e

Ezután a kiépítési szolgáltatás határozza meg, hogy a felhasználó a kiépítés [hatókörében](./how-provisioning-works.md#scoping) van-e. A szolgáltatás a következőkkel kapcsolatos szempontokat tekinti át:

* Azt határozza meg, hogy a felhasználó hozzá van-e rendelve az alkalmazáshoz.
* Azt határozza meg, hogy a hatókör **szinkronizálva** van-e, vagy **az összes szinkronizálása**.
* A létesítési konfigurációban definiált hatóköri szűrők.  

#### <a name="view-details"></a>Részletek megtekintése

A **részletek megtekintése** szakasz a kiértékelt hatóköri feltételeket mutatja. Előfordulhat, hogy a következő tulajdonságok közül egy vagy több látható:

* Az **aktív a forrásrendszer** azt jelzi, hogy a felhasználó rendelkezik az `IsActive` Azure ad-ben **true** tulajdonsággal.
* Az **alkalmazáshoz hozzárendelt** érték azt jelzi, hogy a felhasználó hozzá van rendelve az alkalmazáshoz az Azure ad-ben.
* **Hatókör-szinkronizálás az összes** érték azt jelzi, hogy a hatókör beállítás lehetővé teszi a bérlő összes felhasználóját és csoportját.
* A **felhasználó rendelkezik a szükséges szerepkörrel** , amely azt jelzi, hogy a felhasználó rendelkezik az alkalmazásba való kiosztáshoz szükséges szerepkörökkel. 
* A **hatóköri szűrők** akkor is megjelennek, ha az alkalmazáshoz definiált hatóköri szűrők vannak megadva. A szűrő a következő formátumban jelenik meg: {hatóköri szűrő címe} {hatóköri szűrő attribútum} {hatókör-szűrési operátor} {hatóköri szűrő értéke}.

#### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

* Ellenőrizze, hogy érvényes hatóköri szerepkört adott-e meg. Ne használja például a [Greater_Than operátort](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) nem egész értékkel.
* Ha a felhasználó nem rendelkezik a szükséges szerepkörrel, tekintse át az [alapértelmezett hozzáférési szerepkörhöz hozzárendelt felhasználók üzembe helyezésével kapcsolatos tippeket](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>4. lépés: a felhasználó egyeztetése a forrás és a cél között

Ebben a lépésben a szolgáltatás megkeresi az importálás lépésben beolvasott felhasználót a megcélzott rendszeren lévő felhasználóval.

#### <a name="view-details"></a>Részletek megtekintése

A **részletek megtekintése** oldalon a célként megadott felhasználók tulajdonságai láthatók. A környezeti ablaktáblán látható tulajdonságok a következőképpen változnak:

* Ha egyetlen felhasználó sem felel meg a célként megadott rendszernek, akkor nem fog tudni tulajdonságokat látni.
* Ha egy felhasználó megfelel a célként megadott rendszernek, akkor a rendszer a megfeleltetett felhasználó tulajdonságait fogja látni.
* Ha több felhasználó is megfelel, akkor a rendszer a megfeleltetett felhasználók tulajdonságait fogja látni.
* Ha több egyező attribútum szerepel az attribútum-hozzárendelések részeként, a rendszer minden egyező attribútumot egymás után kiértékel, és az adott attribútumhoz tartozó egyező felhasználók jelennek meg.

#### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

* Előfordulhat, hogy a kiépítési szolgáltatás nem képes a forrásrendszer felhasználói számára egyedi módon egyeztetni a célként megadott felhasználóval. A probléma megoldásához gondoskodjon arról, hogy a megfelelő attribútum egyedi legyen.
* Győződjön meg arról, hogy a célalkalmazás támogatja a megfelelő attribútumként definiált attribútum szűrését.  

### <a name="step-5-perform-action"></a>5. lépés: művelet végrehajtása

Végül a kiépítési szolgáltatás végrehajt egy műveletet, például a felhasználó létrehozását, frissítését, törlését vagy kihagyását.

Íme egy példa arra, hogy mit láthat a felhasználó sikeres igény szerinti kiépítés után:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="A felhasználó sikeres igény szerinti üzembe helyezését bemutató képernyőkép.":::

#### <a name="view-details"></a>Részletek megtekintése

A **részletek megtekintése** szakasz a célalkalmazás alkalmazásban módosított attribútumokat jeleníti meg. Ez a kijelző a kiépítési szolgáltatás tevékenységének végső kimenetét és az exportált attribútumok számát jelöli. Ha ez a lépés meghiúsul, a megjelenített attribútumok a kiépítési szolgáltatás által a módosításra megkísérelt attribútumokat jelölik.

#### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

* A módosítások exportálásának hibái nagyban változhatnak. A gyakori hibákért olvassa el az üzembe helyezési [naplók dokumentációját](../reports-monitoring/concept-provisioning-logs.md#error-codes) .

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **Ki kell kapcsolni az üzembe helyezést az igény szerinti kiépítés igénybe vételéhez?** A hosszú élettartamú tulajdonosi jogkivonatot vagy felhasználónevet és jelszót használó alkalmazások engedélyezéséhez nincs szükség további lépésekre. Az OAuth-t használó alkalmazások számára jelenleg szükség van a kiépítési feladatok leállítására az igény szerinti kiépítés használata előtt. A G Suite, a Box, a munkahely a Facebook és a Slack alkalmazások ebbe a kategóriába tartoznak. Folyamatban van az igény szerinti kiépítés támogatása az összes alkalmazáshoz anélkül, hogy le kellene állítania a kiépítési feladatokat.

* **Mennyi ideig tart az igény szerinti kiépítés?** Az igény szerinti kiépítés általában kevesebb, mint 30 másodpercet vesz igénybe.

## <a name="known-limitations"></a>Ismert korlátozások

Jelenleg van néhány ismert korlátozás az igény szerinti kiépítés során. Küldje el [javaslatait és visszajelzéseit](https://aka.ms/appprovisioningfeaturerequest) , hogy jobban meghatározhassa a következő fejlesztéseket.

> [!NOTE]
> Az igény szerinti kiépítési képességre az alábbi korlátozások vonatkoznak. Arról, hogy egy alkalmazás támogatja-e a kiépítési csoportokat, a törlést vagy más képességeket, keresse meg az adott alkalmazáshoz tartozó oktatóanyagot.

* Az Amazon Web Services (AWS) alkalmazás nem támogatja az igény szerinti kiépítés használatát. 
* A csoportok és szerepkörök igény szerinti üzembe helyezése nem támogatott.
* Az igény szerinti kiépítés az alkalmazásból ki nem osztott felhasználók letiltását támogatja. Azonban nem támogatja az Azure AD-ből letiltott vagy törölt felhasználók letiltását vagy törlését. Ezek a felhasználók nem jelennek meg a felhasználó keresésekor.

## <a name="next-steps"></a>Következő lépések

* [A kiépítés hibaelhárítása](./application-provisioning-config-problem.md)