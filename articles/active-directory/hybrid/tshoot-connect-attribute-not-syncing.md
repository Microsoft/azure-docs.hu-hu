---
title: Azure AD Connectban nem szinkronizált attribútumok hibáinak megoldása | Microsoft Docs "
description: Ez a témakör azt ismerteti, hogyan lehet elhárítani az attribútumok szinkronizálásával kapcsolatos problémákat a hibaelhárítási feladat használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317487"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>A Azure AD Connect nem szinkronizált attribútumok hibáinak megoldása

## <a name="recommended-steps"></a>**Javasolt lépések**

Az attribútumok szinkronizálásával kapcsolatos problémák megismerése előtt tekintsük át a **Azure ad Connect** szinkronizálási folyamatot:

  ![Azure AD Connect szinkronizálási folyamat](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminológia**

* **CS:** Összekötő terület, adatbázisbeli tábla.
* **MV:** Metaverse, egy tábla az adatbázisban.
* **Ad:** Active Directory
* **HRE:** Azure Active Directory

### <a name="synchronization-steps"></a>**Szinkronizálás lépései**

* Importálás az AD-ből: Active Directory objektumok bekerülnek az Active Directory TANÚSÍTVÁNYSZOLGÁLTATÁSOKba.

* Importálás a HRE-ből: Azure Active Directory objektumokat a rendszer a HRE CS-ba helyezi át.

* Szinkronizálás: a **bejövő szinkronizálási szabályok** és a **kimenő szinkronizálási szabályok** sorrendben futnak, és a prioritások száma nem haladhatja meg a magasabb értéket. A szinkronizálási szabályok megtekintéséhez nyissa meg a **szinkronizálási szabályok szerkesztőjét** az asztali alkalmazásokban. A **bejövő szinkronizálási szabályok** a CS-től MV-ig terjedő adatforrásokat eredményeznek. A **kimenő szinkronizálási szabályok** MV-ból cs-ra helyezi át az adatforgalmat.

* Exportálás az AD-be: a szinkronizálás futtatása után az objektumok az AD CS-ből **Active Directoryba** lesznek exportálva.

* Exportálás HRE-be: a szinkronizálást követően a rendszer az objektumokat a HRE CS-ból exportálja **Azure Active Directoryba**.

### <a name="step-by-step-investigation"></a>**Lépésenkénti vizsgálat**

* Megkezdjük a keresést a **metaverse** -ból, és megtekintjük a forrás és a cél közötti hozzárendelés attribútumát.

* Indítsa el **synchronization Service Manager** az asztali alkalmazásokból az alábbi ábrán látható módon:

  ![Synchronization Service Manager elindítása](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* A **synchronization Service Manager** válassza ki a **metaverse-keresést**, válassza ki a **hatókör objektumtípus alapján** elemet, jelölje ki az objektumot egy attribútum használatával, majd kattintson a **Keresés** gombra.

  ![Metaverse-keresés](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Kattintson duplán a **metaverse** keresésben található objektumra az összes attribútum megtekintéséhez. A Connectors ( **Összekötők** ) lapra kattintva megtekintheti az összes **összekötő területének** megfelelő objektumot.

  ![Metaverse-objektum összekötői](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* A **Active Directory-összekötőre** duplán kattintva megtekintheti az **összekötő terület** attribútumait. Kattintson a **Preview (előnézet** ) gombra, és a következő párbeszédablakban kattintson az **előnézet előállítása** gombra.

  ![Képernyőkép, amely az összekötői terület objektum tulajdonságai képernyőt jeleníti meg az előnézet gombbal kiemelve.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Most kattintson az **attribútum importálása folyamatra**, ez az attribútumok áramlását mutatja **Active Directory összekötő területéről** a **metaverse**-ra. A **szinkronizálási szabály** oszlopban látható, hogy melyik **szinkronizálási szabály** járul hozzá az attribútumhoz. Az **adatforrás** oszlopban az **összekötő terület** attribútumai láthatók. A **metaverse attribútum** oszlop a **metaverse** attribútumait jeleníti meg. Itt megkeresheti, hogy az attribútum ne legyen szinkronizálva. Ha itt nem találja az attribútumot, akkor ez nincs leképezve, és létre kell hoznia egy új egyéni **szinkronizálási szabályt** az attribútum leképezéséhez.

  ![Összekötő terület attribútumai](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* A bal oldali ablaktáblán kattintson az **attribútum exportálása folyamatra** , hogy megtekintse az attribútumot a **Metaverse** -ből **Active Directory összekötő területére** a **kimenő szinkronizálási szabályok** használatával.

  ![Képernyőfelvétel: az attribútum a metaverse-ből Active Directory az összekötő területére a kimenő szinkronizálási szabályok használatával.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Hasonlóképpen, megtekintheti az **Azure Active Directory-összekötő terület** objektumát, és előkészítheti az **előzetes** verziót, amellyel megtekintheti a **metaverse** -ből az **összekötő területéhez** tartozó attribútum-folyamatokat, és fordítva, így megvizsgálhatja, hogy egy attribútum miért nem szinkronizálható.

## <a name="recommended-documents"></a>**Ajánlott dokumentumok**
* [Azure AD Connect Sync: technikai fogalmak](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect Sync: az architektúra megismerése](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect szinkronizálás: a deklaratív kiépítés ismertetése](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect szinkronizálás: a deklaratív kiépítési kifejezések ismertetése](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect szinkronizálás: a felhasználók, csoportok és névjegyek ismertetése](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect Sync: Shadow attributes](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Következő lépések

- [Azure ad Connect szinkronizálás](how-to-connect-sync-whatis.md).
- [Mi az a hibrid identitás?](whatis-hybrid-identity.md).