---
title: Attribútumok szinkronizálása az Azure AD-vel leképezéshez
description: Ismerje meg, hogyan szinkronizálhatja a helyszíni Active Directory attribútumait az Azure AD-vel. Ha az SaaS-alkalmazásokhoz konfigurálja a felhasználók üzembe helyezését, a címtár-kiterjesztési funkcióval adhat hozzá olyan forrás-attribútumokat, amelyek alapértelmezés szerint nincsenek szinkronizálva.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 62d035b85850f8ac455a85fd93e4d081bbd386e1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256085"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Egy attribútum szinkronizálása a helyszíni Active Directory az Azure AD-be az alkalmazásba való kiépítéshez

Ha testreszabja a felhasználók kiosztásához szükséges attribútum-hozzárendeléseket, akkor előfordulhat, hogy a leképezni kívánt attribútum nem jelenik meg a **forrás attribútum** listában. Ebből a cikkből megtudhatja, hogyan adhatja hozzá a hiányzó attribútumot a helyszíni Active Directory (AD) és a Azure Active Directory (Azure AD) közötti szinkronizálással.

Az Azure AD-nek tartalmaznia kell az összes olyan adatforrást, amely felhasználói profil létrehozásához szükséges az Azure AD-ből egy SaaS-alkalmazásba való kiépítés során. Bizonyos esetekben előfordulhat, hogy az adatok elérhetővé tételéhez szükség lehet az attribútumok szinkronizálására a helyszíni AD-ből az Azure AD-be. Azure AD Connect automatikusan szinkronizál bizonyos attribútumokat az Azure AD-be, de nem minden attribútumot. Továbbá előfordulhat, hogy egyes attribútumok (például a SAMAccountName) alapértelmezés szerint szinkronizálva vannak a Microsoft Graph API használatával. Ezekben az esetekben a Azure AD Connect Directory-bővítmény funkcióval szinkronizálhatja az attribútumot az Azure AD-vel. Így az attribútum látható lesz a Microsoft Graph API és az Azure AD kiépítési szolgáltatás számára.

Ha az üzembe helyezéshez szükséges adatmennyiség Active Directory, de a fent ismertetett okok miatt nem érhető el a kiépítés során, kövesse az alábbi lépéseket.
 
## <a name="sync-an-attribute"></a>Attribútum szinkronizálása 

1. Nyissa meg a Azure AD Connect varázslót, válassza a feladatok, majd a **szinkronizálási beállítások testreszabása lehetőséget**.

   ![Azure Active Directory Connect varázsló további feladatok lapja](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Jelentkezzen be Azure AD globális rendszergazdaként. 

3. A **választható szolgáltatások** lapon válassza a **címtár-kiterjesztés attribútumának szinkronizálása** lehetőséget.
 
   ![Azure Active Directory Connect varázsló választható funkciók lapja](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Válassza ki az Azure AD-ra kiterjeszteni kívánt attribútum (oka) t.
   > [!NOTE]
   > A Keresés az **elérhető attribútumok** területen kis-és nagybetűket érint.

   ![A "Directory-bővítmények" kiválasztási lapot megjelenítő képernyőkép](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Fejezze be a Azure AD Connect varázslót, és engedélyezze a teljes szinkronizálási ciklus futtatását. A ciklus befejezésekor a séma ki van bővítve, és a rendszer szinkronizálja az új értékeket a helyszíni AD és az Azure AD között.
 
6. Ha a Azure Portal a [felhasználói attribútumok leképezéseit szerkeszti](customize-application-attributes.md), a **forrás attribútum** lista mostantól a hozzáadott attribútumot fogja tartalmazni a formátumban `<attributename> (extension_<appID>_<attributename>)` . Válassza ki az attribútumot, és rendelje hozzá a kiépítés céljának alkalmazásához.

   ![Azure Active Directory Connect varázsló Directory-bővítmények kiválasztási lapja](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A helyszíni AD-ből (például **többé** vagy **DN/DistinguishedName**) származó hivatkozási attribútumok kiépítésének lehetősége jelenleg nem támogatott. Ezt a funkciót [felhasználói hangon](https://feedback.azure.com/forums/169401-azure-active-directory)is kérheti. 

## <a name="next-steps"></a>Következő lépések

* [A kiépítés hatókörében lévő felhasználók meghatározása](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
