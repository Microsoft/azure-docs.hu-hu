---
title: Mi az az identitás kiépítés az Azure AD-vel? | Microsoft Docs
description: Az identitás kiépítés áttekintését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614821"
---
# <a name="what-is-identity-provisioning"></a>Mi az az identitáslétesítés?

Napjainkban a vállalatok és a vállalatok egyre többen használják a helyszíni és a felhőalapú alkalmazásokat.  A felhasználóknak a helyszíni és a Felhőbeli alkalmazásokhoz is hozzá kell férniük. A különböző alkalmazásokban (a helyszínen és a felhőben) egyetlen identitással kell rendelkeznie.

A kiépítés során a rendszer bizonyos feltételek alapján hozza létre az objektumot, és az objektumot naprakészen tartja, és törli az objektumot, amikor a feltételek már nem teljesülnek. Ha például egy új felhasználó csatlakozik a szervezethez, a felhasználó bekerül a HR rendszerbe.  Ezen a ponton a kiépítés létrehozhat egy megfelelő felhasználói fiókot a felhőben, Active Directoryban és különböző alkalmazásokban, amelyekhez a felhasználónak hozzá kell férnie.  Ez lehetővé teszi a felhasználó számára, hogy megkezdje a munkát, és hozzáférhessen az első napon szükséges alkalmazásokhoz és rendszerekhez. 

![A felhőbe való kiépítés Azure Active Directorysal való kihelyezését bemutató diagram.](media/what-is-provisioning/cloud-1.png)

Azure Active Directory tekintetében a kiépítés a következő főbb forgatókönyvek szerint bontható.  

- **[HR-vezérelt kiépítés](#hr-driven-provisioning)**  
- **[Alkalmazás kiépítés](#app-provisioning)**  
- **[Címtár kiépítés](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR-vezérelt kiépítés

![Diagram, amely a HR-alapú üzembe helyezést mutatja a Cloud HR, a helyszíni HR és a Azure Active Directory alapján.](media/what-is-provisioning/cloud-2.png)

A HR-ből a felhőbe való kiépítés magában foglalja az adott HR-rendszeren található információk alapján objektumok (felhasználók, szerepkörök, csoportok stb.) létrehozását.  

A leggyakoribb forgatókönyv a következő: amikor egy új alkalmazott csatlakozik a vállalathoz, azok bekerülnek a HR-rendszerbe.  Ha ez bekövetkezik, a rendszer kiépíti a felhőbe.  Ebben az esetben az Azure AD-t.  A HR-ből való kiépítés a következő helyzetekben lehetséges. 

- **Új alkalmazottak felvétele** – ha új alkalmazottat ad hozzá a Cloud HR szolgáltatáshoz, a rendszer automatikusan létrehoz egy felhasználói fiókot Active Directory, Azure Active Directory és opcionálisan Microsoft 365 és más SaaS-alkalmazásokban, amelyeket az Azure ad támogat, és az e-mail-címet a Cloud HR-re írja vissza.
- **Alkalmazotti attribútumok és a profil frissítései** – ha egy alkalmazotti rekord frissül a Felhőbeli HR-ben (például a nevük, a cím vagy a felettes), a felhasználói fiókja automatikusan frissül Active Directory, Azure Active Directory és opcionálisan Microsoft 365 és más, az Azure ad által támogatott SaaS-alkalmazásokban is.
- **Alkalmazotti megszakítások** – ha egy alkalmazott leáll a Cloud HR-ben, a felhasználói fiókja automatikusan le van tiltva Active Directory, Azure Active Directory és opcionálisan az Office 365 és az Azure ad által támogatott egyéb SaaS-alkalmazások esetében.
- **Alkalmazottak** újratelepítése – ha egy alkalmazottat a Felhőbeli HR-ben helyeztek üzembe, a régi fiók automatikusan újraaktiválható vagy újra kiépíthető (az Ön igényeitől függően), hogy Active Directory, Azure Active Directory, és opcionálisan Microsoft 365 és más, az Azure ad által támogatott SaaS-alkalmazásokat.


## <a name="app-provisioning"></a>Alkalmazás kiépítés

![Diagram, amely az alkalmazások kiépítését mutatja be helyszíni alkalmazásokkal, nem a Microsofttól származó felhőalapú alkalmazásokkal és Azure Active Directoryokkal.](media/what-is-provisioning/cloud-3.png)

Azure Active Directory (Azure AD) esetében az **[alkalmazás üzembe](../app-provisioning/user-provisioning.md)** helyezésének kifejezése arra a felhasználói identitások és szerepkörök automatikus létrehozására utal, amelyekhez a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés a felhasználói identitások karbantartását és eltávolítását is magában foglalja az állapot vagy a szerepkörök módosításakor. Gyakori forgatókönyvek például az Azure AD-felhasználók üzembe helyezése olyan alkalmazásokban, mint a [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), a [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), a [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)és még sok más.

## <a name="directory-provisioning"></a>Címtár kiépítés

![Felhőbeli kiépítés](media/what-is-provisioning/cloud-4.png)

A helyszíni kiépítés magában foglalja a helyszíni forrásokból (például Active Directory) az Azure AD-be történő kiépítést.  

A leggyakoribb forgatókönyv a következő: Ha Active Directory (AD) felhasználója az Azure AD-ben van kiépítve.

Ezt a Azure AD Connect Sync, Azure AD Connect a felhőalapú kiépítés és a Microsoft Identity Manager használatával hajtották végre. 
 
## <a name="next-steps"></a>Következő lépések 

- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [A felhő kiépítés telepítése](how-to-install.md)