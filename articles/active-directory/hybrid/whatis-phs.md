---
title: Mi a jelszó-kivonatoló szinkronizálás az Azure AD-vel? | Microsoft Docs
description: Leírja a jelszó-kivonatok szinkronizálását.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23f88a783ca257090fc607cc9a16ec457c7e9c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90016588"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Mi a jelszó-kivonatoló szinkronizálás az Azure AD-vel?
A jelszó-kivonatolás szinkronizálása a hibrid identitások megvalósításához használt bejelentkezési módszerek egyike. Azure AD Connect szinkronizálja egy felhasználó jelszavának kivonatát egy helyszíni Active Directory példányból egy felhőalapú Azure AD-példányba.

A jelszó-kivonatolási szinkronizálás a Azure AD Connect Sync által megvalósított címtár-szinkronizálási szolgáltatás kiterjesztése. Ezzel a szolgáltatással bejelentkezhet az Azure AD-szolgáltatásokba, például a Microsoft 365ba. A szolgáltatásba való bejelentkezéshez ugyanazt a jelszót használja, amelyet a helyszíni Active Directory-példányba való bejelentkezéshez használ.

![Mi az az Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

A jelszó-kivonatolás szinkronizálása a jelszavak számának csökkentésével segíti a felhasználókat, hogy csak eggyel maradjanak. A jelszó-kivonatolás szinkronizálása a következőket teheti:

* Javítsa a felhasználók termelékenységét.
* Csökkentse ügyfélszolgálati költségeit.  

A jelszó-kivonatolási szinkronizálás lehetővé teszi a [kiszivárgott hitelesítő adatok észlelését](../identity-protection/concept-identity-protection-risks.md#user-risk) is a hibrid fiókoknál. A Microsoft együtt működik a sötét webes kutatókkal és a bűnüldöző szervekkel nyilvánosan elérhető felhasználónevek és jelszavak párok kereséséhez. Ha ezek közül bármelyik pár megegyezik a felhasználókkal, a rendszer a társított fiókot magas kockázatra helyezi át. 

>[!NOTE]
> A PHS engedélyezése után csak az új kiszivárgott hitelesítő adatok lesznek feldolgozva a bérlőn. A rendszer nem végez ellenőrzést a korábban megtalált hitelesítő adatokkal szemben.


Szükség esetén beállíthatja a jelszó-kivonat szinkronizálását biztonsági másolatként, ha úgy dönt, hogy a bejelentkezési módszerként [Active Directory összevonási szolgáltatások (AD FS) (AD FS) összevonást](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) használ.

A jelszó-kivonatoló szinkronizálás a környezetben való használatához a következőket kell tennie:

* Telepítse a Azure AD Connect.  
* Konfigurálja a címtár-szinkronizálást a helyszíni Active Directory-példány és a Azure Active Directory-példány között.
* Jelszó-kivonat szinkronizálásának engedélyezése.



További információ: [Mi az a Hybrid Identity?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Következő lépések

- [Mi az a hibrid identitás?](whatis-hybrid-identity.md)
- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md)
- [Mi az átmenő hitelesítés (PTA)?](how-to-connect-pta.md)
- [Mi az összevonás?](whatis-fed.md)
- [Mi az az egyszeri bejelentkezés?](how-to-connect-sso.md)
- [A jelszó-kivonatolási szinkronizálás működése](how-to-connect-password-hash-synchronization.md)
