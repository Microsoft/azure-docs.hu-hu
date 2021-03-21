---
title: 'Azure AD Connect Sync: címtárszolgáltatás-bővítmények | Microsoft Docs'
description: Ez a témakör a Azure AD Connect Directory Extensions szolgáltatását ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4152783129fa1c5950d6cf6287332bf90d32a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976877"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect Sync: címtárszolgáltatás-bővítmények
A (z) Azure Active Directory (Azure AD) sémájának kiterjesztése a helyi Active Directory saját attribútumaival is elvégezhető. Ez a funkció lehetővé teszi LOB-alkalmazások készítését olyan attribútumok fogyasztásával, amelyeket továbbra is a helyszínen kezelhet. Ezek az attribútumok a [bővítményeken](/graph/extensibility-overview
)keresztül is felhasználhatók. A rendelkezésre álló attribútumok a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)használatával tekinthetők meg. Ezt a funkciót használhatja dinamikus csoportok létrehozására is az Azure AD-ben.

Jelenleg nincs Microsoft 365 munkaterhelés használja ezeket az attribútumokat.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Az Azure AD-vel szinkronizálandó attribútumok testreszabása

Konfigurálja, hogy mely további attribútumokat kívánja szinkronizálni a telepítővarázsló egyéni beállítások elérési útján.

> [!NOTE]
> A 1.2.65.0-nál korábbi Azure AD Connect verziók esetében az **elérhető attribútumok** keresési mezője a kis-és nagybetűk megkülönböztetésére szolgál.

![Séma-kiterjesztés varázsló](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A telepítés a következő attribútumokat jeleníti meg, amelyek érvényes jelöltek:

* Felhasználói és csoport típusú objektumok típusai
* Egyértékű attribútumok: karakterlánc, logikai, egész szám, bináris
* Többértékű attribútumok: karakterlánc, bináris


>[!NOTE]
> Miután Azure AD Connect szinkronizált többértékű Active Directory attribútumot az Azure AD-hez többértékű attribútum-kiterjesztésként, lehetséges, hogy az SAML-jogcím attribútumát is tartalmazza. Ezt az adatgyűjtést azonban nem lehet API-hívással használni.

Az attribútumok listáját a rendszer a Azure AD Connect telepítésekor létrehozott séma-gyorsítótárból olvassa be. Ha további attribútumokkal bővítette a Active Directory sémát, [frissítenie kell a sémát](how-to-connect-installation-wizard.md#refresh-directory-schema) , mielőtt láthatóvá válnak az új attribútumok.

Az Azure AD-beli objektumok legfeljebb 100 attribútumot tartalmazhatnak a címtár-kiterjesztésekhez. A maximális hossz 250 karakter. Ha egy attribútum értéke továbbra is fennáll, a szinkronizálási motor csonkolja azt.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>A varázsló által készített Azure AD-beli konfigurációs változások

Azure AD Connect telepítésekor az alkalmazás regisztrálva lesz, ahol ezek az attribútumok elérhetők. Ezt az alkalmazást a Azure Portal tekintheti meg. A neve mindig a **bérlői séma bővítményének alkalmazás**.

![Séma-bővítmény alkalmazás](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Győződjön meg róla, hogy az **összes alkalmazás** lehetőséget választotta az alkalmazás megtekintéséhez.

Az attribútumok előtaggal vannak ellátva **\_ {ApplicationId} \_ bővítménnyel**. A ApplicationId ugyanazt az értéket adja meg az Azure AD-bérlő összes attribútuma számára. Erre az értékre szüksége lesz az ebben a témakörben található összes többi forgatókönyv esetében.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Attribútumok megtekintése a Microsoft Graph API használatával

Ezek az attribútumok mostantól a Microsoft Graph API-n keresztül érhetők el a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#)használatával.

>[!NOTE]
> A Microsoft Graph API-ban meg kell kérnie a visszaadott attribútumok értékét. Explicit módon válassza ki a következő attribútumokat: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> További információ [: Microsoft Graph: lekérdezési paraméterek használata](/graph/query-parameters#select-parameter).

>[!NOTE]
> Nem támogatott a AADConnect által nem létrehozott AADConnect származó attribútumok szinkronizálása. Így teljesítménnyel kapcsolatos problémákat és nem várt eredményeket eredményezhet. A szinkronizáláshoz csak a fenti ábrán látható kiterjesztési attribútumok támogatottak.

## <a name="use-the-attributes-in-dynamic-groups"></a>A dinamikus csoportok attribútumainak használata

A hasznos forgatókönyvek egyike az, hogy ezeket az attribútumokat a dinamikus biztonsági vagy Microsoft 365 csoportokban használják.

1. Hozzon létre egy új csoportot az Azure AD-ben. Adjon neki egy jó nevet, és győződjön meg arról, hogy a **tagság típusa** **dinamikus felhasználó**.

   ![Képernyőfelvétel új csoporttal](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. **Dinamikus lekérdezés hozzáadásához** válassza a lehetőséget. Ha megtekinti a tulajdonságokat, akkor ezek a kiterjesztett attribútumok nem jelennek meg. Először hozzá kell adnia őket. Kattintson az **Egyéni bővítmény tulajdonságainak beolvasása** elemre, adja meg az alkalmazás azonosítóját, és kattintson a **Tulajdonságok frissítése** elemre.

   ![Képernyőfelvétel a címtár-bővítmények hozzáadásáról](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Nyissa meg a tulajdonság legördülő listát, és figyelje meg, hogy a hozzáadott attribútumok már láthatók.

   ![Képernyőkép a felhasználói felületen megjelenített új attribútumokról](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Fejezze be a kifejezést az igényeinek megfelelően. A példánkban a szabály a következőre van beállítva: **(User.extension_9d98ed114c4840d298fad781915f27e4_division EQ "Sales and marketing")**.

4. A csoport létrehozása után adjon meg némi időt az Azure AD számára a tagok feltöltéséhez, majd tekintse át a tagokat.

   ![Képernyőfelvétel a dinamikus csoport tagjaival](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Következő lépések
További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
