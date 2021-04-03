---
title: Mi az a HR-alapú kiépítés a Azure Active Directory? | Microsoft Docs
description: A HR-alapú kiépítés áttekintését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ffe8c1397525348e472e965a407909fee36152d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168447"
---
# <a name="what-is-hr-driven-provisioning"></a>Mi az a HR-alapú kiépítés?

![HR-kiépítés](./media/what-is-hr-driven-provisioning/cloud2a.png)

A HR-alapú kiépítés a digitális identitások emberi erőforrások rendszer alapján történő létrehozásának folyamata.  A HR rendszerek az újonnan létrehozott digitális identitások kezdő-és szolgáltatói lesznek, és gyakran a kiindulási pont a számos kiépítési folyamathoz.  Ha például egy új alkalmazott csatlakozik a vállalathoz, akkor azokat a rendszer az emberi erőforrásrendszer keretében hozza létre.  A létrehozással egy felhasználói fiók kiépítését Active Directoryba, majd Azure AD Connect a fiókot az Azure AD-be, stb.).

A HR-vezérelt kiépítés történhet helyszíni vagy Felhőbeli alapú kiépítéssel.

## <a name="on-premises-based-hr-provisioning"></a>Helyszíni alapú HR-kiépítés
A helyszíni HR-kiépítés egy helyi HR-rendszer használatával és új digitális identitások kiépítésével valósítható meg.

A HR rendszerek számos különböző csomagot, szoftvercsomag használatát, valamint SQL-kiszolgálókat, LDAP-címtárakat stb. is használhatnak.

A Microsoft helyszíni HR-létesítési megoldásai jelenleg a Microsoft Identity Manager használatával aktiválják az üzembe helyezést, amikor új identitás jön létre ezekben a HR-rendszerekben.

A webszolgáltatások használatával a helyi HR-rendszerek felhasználóit Active Directory vagy az Azure AD-be is kiépítheti.

A Microsoft Identity Manager és az általa támogatott rendszerekről a [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) dokumentációjában talál további információt.

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Következő lépések 
- [Mi az Identity Lifecycle Management?](what-is-identity-lifecycle-management.md)
- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az alkalmazás-üzembehelyezés?](what-is-app-provisioning.md)
- [Mi az a címtárak közötti üzembe helyezés?](what-is-inter-directory-provisioning.md)