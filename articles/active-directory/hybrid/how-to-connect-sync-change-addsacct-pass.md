---
title: 'Azure AD Connect Sync: a AD DS fiók jelszavának módosítása | Microsoft Docs'
description: Ez a témakör azt ismerteti, hogyan frissítheti Azure AD Connect a AD DS fiók jelszavának módosítása után.
services: active-directory
keywords: AD DS fiók, Active Directory fiók, jelszó
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4077146292db1266d5dbc51cc577f952b2bff191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357511"
---
# <a name="changing-the-ad-ds-account-password"></a>Az AD DS-fiók jelszavának módosítása
A AD DS fiók a Azure AD Connect által a helyszíni Active Directoryekkel való kommunikációhoz használt felhasználói fiókra hivatkozik. Ha módosítja a AD DS fiók jelszavát, akkor frissítenie kell Azure AD Connect szinkronizációs szolgáltatást az új jelszóval. Ellenkező esetben a szinkronizálás már nem tud megfelelően szinkronizálni a helyszíni Active Directoryval, és a következő hibákba ütközik:

* A Synchronization Service Managerben a helyszíni AD-val végzett importálási vagy exportálási műveletek sikertelenek, a **nem szükséges hitelesítő adatokkal** kapcsolatos hiba miatt.

* A Windows Eseménynapló alatt az alkalmazás eseménynaplója hibát tartalmaz a 6000-es azonosítójú **eseménynél** , a " **felügyeleti ügynök" contoso.com "üzenetet pedig nem sikerült futtatni, mert a hitelesítő adatok érvénytelenek voltak"**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>A szinkronizálási szolgáltatás frissítése új jelszóval AD DS fiókhoz
A szinkronizálási szolgáltatás frissítése az új jelszóval:

1. Indítsa el a Synchronization Service Managert (START → szinkronizációs szolgáltatás).
</br>![Service Manager szinkronizálása](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Nyissa meg az **Összekötők** lapot.

3. Válassza ki azt az **ad-összekötőt** , amely ahhoz a AD DS-fiókhoz tartozik, amelynek a jelszavát módosították.

4. A **műveletek** területen válassza a **Tulajdonságok** lehetőséget.

5. Az előugró párbeszédpanelen válassza a **kapcsolódás Active Directory erdőhöz** lehetőséget:

6. Adja meg a **jelszó** szövegmezőben a AD DS fiók új jelszavát.

7. Az új jelszó mentéséhez és az előugró ablak bezárásához kattintson **az OK** gombra.

8. Indítsa újra a Azure AD Connect szinkronizációs szolgáltatást a Windows szolgáltatásvezérlő kezelőjében. Ezzel biztosíthatja, hogy a rendszer eltávolítsa a régi jelszóra mutató hivatkozásokat a memória-gyorsítótárból.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
