---
title: Azure AD Connect Cloud Sync által támogatott topológiák és forgatókönyvek
description: Ismerje meg a különböző helyszíni és Azure Active Directory (Azure AD) topológiákat, amelyek a Azure AD Connect Cloud Sync szolgáltatást használják.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613543"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect Cloud Sync által támogatott topológiák és forgatókönyvek
Ez a cikk a Azure AD Connect Cloud Sync szolgáltatást használó különböző helyszíni és Azure Active Directory (Azure AD) topológiákat ismerteti. Ez a cikk csak a támogatott konfigurációkat és forgatókönyveket tartalmazza.

> [!IMPORTANT]
> A Microsoft nem támogatja a Azure AD Connect Felhőbeli szinkronizálás módosítását vagy működését a hivatalosan dokumentált konfigurációkon vagy műveleteken kívül. Ezen konfigurációk vagy műveletek bármelyike inkonzisztens vagy nem támogatott állapotba ütközhet Azure AD Connect Cloud Sync szolgáltatásban. Ennek eredményeképpen a Microsoft nem tud technikai támogatást biztosítani az ilyen üzemelő példányokhoz.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Az összes forgatókönyvről és topológiáról megjegyezhető dolgok
A következő lista azokat az információkat tartalmazza, amelyeket érdemes figyelembe venni a megoldás kiválasztásakor.

- A felhasználóknak és a csoportoknak egyedileg azonosíthatóknak kell lenniük az összes erdőben
- Az erdők közötti egyezés nem fordul elő a Cloud Sync szolgáltatásban
- Egy felhasználót vagy csoportot csak egyszer kell megjeleníteni az összes erdőben
- Az objektumok forrás-horgonya automatikusan kiválasztva.  Ha van, MS-DS-ConsistencyGuid-t használ, máskülönben ObjectGUID van használatban.
- A forrás-horgonyhoz használt attribútum nem módosítható.

## <a name="single-forest-single-azure-ad-tenant"></a>Egyetlen erdő, egyetlen Azure AD-bérlő
![Egyetlen erdő és egyetlen bérlő topológiáját bemutató diagram.](media/tutorial-single-forest/diagram-2.png)

A legegyszerűbb topológia egyetlen helyszíni erdő, amely egy vagy több tartománnyal és egyetlen Azure AD-Bérlővel rendelkezik.  Példa erre az esetre [: oktatóanyag: egyetlen erdő egyetlen Azure ad-Bérlővel](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Több erdős, egyetlen Azure AD-bérlő
![Több erdő és egyetlen bérlő topológiája](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

A gyakori topológia több AD-erdő, egy vagy több tartománnyal és egyetlen Azure AD-Bérlővel.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Meglévő erdő Azure AD Connect, új erdővel a Felhőbeli kiépítés során
![Egy meglévő erdő és egy új erdő topológiáját bemutató diagram.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Ez a forgatókönyv a topológia hasonló a többerdős forgatókönyvhöz, azonban ez egy meglévő Azure AD Connect-környezettel rendelkezik, és egy új erdőt hoz üzembe Azure AD Connect Cloud Sync használatával.  Példa erre a forgatókönyvre [: oktatóanyag: egy meglévő erdő egyetlen Azure ad-Bérlővel](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Azure AD Connect Cloud Sync kipróbálása egy meglévő hibrid AD-erdőben
![Egyetlen erdőhöz és egyetlen bérlőhöz tartozó topológia a ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) tesztelési forgatókönyv magában foglalja a Azure ad Connect és az Azure ad Connect Cloud Sync egyazon erdőben való létezését, és ennek megfelelően a felhasználók és csoportok hatókörét. Megjegyzés: egy objektumnak csak az egyik eszköz hatókörében kell lennie. 

Példa erre a forgatókönyvre [: oktatóanyag: kísérleti Azure ad Connect Cloud Sync egy meglévő szinkronizált ad-erdőben](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)

