---
title: Szinkronizálási szabály testreszabása Azure AD Connectban | Microsoft Docs "
description: Megtudhatja, hogyan szerkesztheti vagy hozhat létre új szinkronizálási szabályt a szinkronizációs szabály szerkesztőjével.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530488"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Szinkronizálási szabály testre szabása

## <a name="recommended-steps"></a>**Javasolt lépések**

A szinkronizálási szabály szerkesztőjével szerkesztheti vagy létrehozhatja az új szinkronizálási szabályt. A szinkronizálási szabályok módosításához speciális felhasználónak kell lennie. Előfordulhat, hogy a helytelen módosítások miatt törölheti az objektumokat a cél könyvtárából. Kérjük, olvassa el az [ajánlott dokumentumokat](#recommended-documents) a szinkronizálási szabályokkal kapcsolatos szakértelem beszerzéséhez. A szinkronizálási szabály módosításához kövesse az alábbi lépéseket:

* Indítsa el a szinkronizációs szerkesztőt az asztal alkalmazás menüjében az alábbi ábrán látható módon:

    ![Szinkronizációs szabály szerkesztője menü](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Az alapértelmezett szinkronizálási szabály testreszabásához a szinkronizálási szabályok szerkesztőjének Szerkesztés gombjára kattintva hozza létre a meglévő szabályt, amely létrehozza a szabványos alapértelmezett szabály másolatát, és letiltja azt. Mentse a klónozott szabályt a 100-nál kisebb prioritással.  A prioritás határozza meg, hogy milyen szabály nyeri a WINS (alacsonyabb numerikus érték) ütközéses feloldást, ha az attribútum folyamata ütközik.

    ![Szinkronizációs szabály szerkesztője](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Egy adott attribútum módosításakor ideális esetben csak a módosított attribútumot kell megőrizni a klónozott szabályban.  Ezután engedélyezze az alapértelmezett szabályt, hogy a módosított attribútum klónozott szabályból származik, és a többi attribútumot az alapértelmezett standard szabályból válassza ki. 

* Vegye figyelembe, hogy abban az esetben, ha a módosított attribútum számított értéke NULL értékű a klónozott szabályban, és nem NULL az alapértelmezett standard szabályban, akkor a nem NULL érték fog megjelenni, és a NULL értéket fogja cserélni. Ha nem szeretné, hogy NULL értékű helyett null értéket adjon meg, akkor a AuthoritativeNull a klónozott szabályban kell kiosztania.

* Egy **kimenő** szabály módosításához módosítsa a szűrést a szinkronizálási szabály szerkesztőjéből.

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