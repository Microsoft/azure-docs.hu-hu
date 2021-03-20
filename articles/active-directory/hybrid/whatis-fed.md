---
title: Mi az az Azure AD-vel való összevonás? | Microsoft Docs
description: Az Azure AD-vel való összevonást ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb1c3bc9f89db3f4b694803a63293a5537d4b98b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278752"
---
# <a name="what-is-federation-with-azure-ad"></a>Mi az az Azure AD-vel való összevonás?

Az összevonás olyan tartományok gyűjteménye, amelyek megbízhatósági kapcsolatot létesítettek. A megbízhatósági szint eltérő lehet, de általában magában foglalja a hitelesítést, és szinte mindig tartalmazza az engedélyezést. Egy tipikus összevonás több olyan szervezetet is tartalmazhat, amelyek megbízhatósági kapcsolatot létesítettek egy adott erőforráshoz való közös hozzáféréshez.

A helyszíni környezetet az Azure AD-vel összevonása, és ezt az összevonást használhatja hitelesítéshez és engedélyezéshez.  Ez a bejelentkezési módszer biztosítja, hogy minden felhasználói hitelesítés a helyszínen történjen.  Ez a módszer lehetővé teszi, hogy a rendszergazdák szigorúbb szintű hozzáférés-vezérlést alkalmazzanak. A AD FS és a PingFederate összevonása elérhető.

![Összevont identitások](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Ha az összevont identitáskezelést az Active Directory összevonási szolgáltatásokkal (AD FS) együtt használja, dönthet úgy, hogy biztonsági tartalékként a jelszókivonat-szinkronizálást is konfigurálja, hogy lehessen mit használni, ha az AD FS-infrastruktúra leáll.


## <a name="next-steps"></a>Következő lépések

- [Mi az a hibrid identitás?](./whatis-hybrid-identity.md)
- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md)
- [Mi az a jelszókivonat-szinkronizálás?](whatis-phs.md)
- [Mi az összevonás?](whatis-fed.md)
- [Mi az az egyszeri bejelentkezés?](how-to-connect-sso.md)
- [Az összevonás működése](how-to-connect-fed-whatis.md)
- [Összevonás a PingFederate-tel](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)