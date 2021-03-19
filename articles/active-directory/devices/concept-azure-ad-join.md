---
title: Mi az az Azure AD-hez csatlakoztatott eszköz?
description: Ismerje meg az Azure AD-hez csatlakoztatott eszközöket, valamint azt, hogy az eszköz-identitás kezelése hogyan segítheti a környezet erőforrásaihoz hozzáférő eszközök felügyeletét.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601452"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-hez csatlakoztatott eszközök

Az Azure AD JOIN olyan szervezetek számára készült, amelyek csak felhőalapú vagy csak felhőalapú célokra alkalmasak. Bármely szervezet az Azure AD-hez csatlakoztatott eszközöket a méret vagy az iparág méretétől függetlenül is üzembe helyezheti. Az Azure AD-csatlakozás hibrid környezetben is működik, ami lehetővé teszi a Felhőbeli és a helyszíni alkalmazások és erőforrások elérését.

| Azure AD-csatlakozás | Description |
| --- | --- |
| **Definíció** | Csak az Azure AD-hez csatlakozik, amely szervezeti fiókot igényel az eszközre való bejelentkezéshez |
| **Elsődleges célközönség** | Alkalmas a csak felhőalapú és a hibrid szervezetekhez. |
|   | Egy szervezet összes felhasználójára érvényes |
| **Az eszközök tulajdonjoga** | Szervezet |
| **Operációs rendszerek** | Minden Windows 10 rendszerű eszköz, kivéve a Windows 10 Home rendszert |
|   | [Az Azure-ban futó Windows Server 2019 Virtual Machines](howto-vm-sign-in-azure-ad-windows.md) (Server Core nem támogatott) |
| **Telepítés** | Önkiszolgáló: Windows OOBE vagy beállítások |
|   | Csoportos regisztráció |
|   | Windows Autopilot |
| **Eszköz bejelentkezési beállításai** | Szervezeti fiókok a használatával: |
|   | Jelszó |
|   | Vállalati Windows Hello |
|   | Nem 2.0 biztonsági kulcsok (előzetes verzió) |
| **Eszközfelügyelet** | Mobileszköz-kezelés (példa: Microsoft Intune) |
|   | Közös felügyelet Microsoft Intune és Microsoft végponttal Configuration Manager |
| **Főbb képességek** | Egyszeri bejelentkezés mind a Felhőbeli, mind a helyszíni erőforrásokhoz |
|   | Feltételes hozzáférés a MDM-regisztráció és a MDM-megfelelőség kiértékelése révén |
|   | Önkiszolgáló jelszó-visszaállítás és a Windows Hello PIN-kód alaphelyzetbe állítása a zárolási képernyőn |
|   | Enterprise State Roaming eszközök között |

Az Azure AD-hez csatlakoztatott eszközök bejelentkeznek egy szervezeti Azure AD-fiók használatával. A szervezet erőforrásaihoz való hozzáférés az Azure AD-fiók és az eszköz identitására alkalmazott [feltételes hozzáférési szabályzatok](../conditional-access/howto-conditional-access-policy-compliant-device.md) alapján tovább korlátozható.

A rendszergazdák az Azure AD-hez csatlakoztatott eszközöket a mobileszköz-felügyeleti (MDM) eszközök, például a Microsoft Intune vagy a Microsoft Endpoint Configuration Manager használatával történő közös felügyeleti forgatókönyvek használatával védhetik és vezérelhetik. Ezek az eszközök lehetővé teszik a szervezet által szükséges konfigurációk betartatását, például a tárolás titkosítását, a jelszó bonyolultságát, a szoftverek telepítését és a szoftverfrissítések használatát. A rendszergazdák a vállalati alkalmazások számára elérhetővé tehetik az Azure AD-hez csatlakoztatott eszközöket a Configuration Manager használatával [az üzleti és oktatási Microsoft Store alkalmazások felügyeletéhez](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Az Azure AD JOIN szolgáltatás önkiszolgáló lehetőségekkel is elvégezhető, például a kezdőélmény (OOBE), a csoportos regisztráció vagy a [Windows Autopilot](/intune/enrollment-autopilot)használatával.

Az Azure AD-hez csatlakoztatott eszközök továbbra is használhatják az egyszeri bejelentkezéses hozzáférést a helyszíni erőforrásokhoz, amikor azok a szervezet hálózatán vannak. Az Azure AD-hez csatlakoztatott eszközök továbbra is hitelesíthetők a helyszíni kiszolgálókra, például a fájlokra, a nyomtatásra és az egyéb alkalmazásokra.

## <a name="scenarios"></a>Forgatókönyvek

Habár az Azure AD elsősorban az olyan cégek számára készült, amelyek nem rendelkeznek helyszíni Windows Server Active Directory-infrastruktúrával, a szolgáltatás a következő helyzetekben is használható:

- Ha váltani kíván egy felhőalapú infrastruktúrára az Azure AD és egy mobileszköz-felügyeleti megoldás, például az Intune használatával.
- Ha nem használhat helyszíni tartománycsatlakoztatást, például ha mobileszközöket, táblagépeket és telefonokat szeretne felügyelet alá vonni.
- A felhasználóknak elsősorban az Azure AD-vel integrált Microsoft 365 vagy más SaaS-alkalmazásokhoz kell hozzáférnie.
- Ha felhasználók egy csoportját az Azure AD-ben szeretné felügyelni az Active Directory helyett. Ez a forgatókönyv például a szezonális feldolgozók, a vállalkozók vagy a tanulók számára is alkalmazható.
- Ha csatlakozási lehetőséget kíván nyújtani a korlátozott helyszíni infrastruktúrájú távoli fiókirodákban tartózkodó felhasználóknak.

Az Azure AD-hez csatlakoztatott eszközöket az összes Windows 10-es eszközhöz konfigurálhatja a Windows 10 Home kivételével.

Az Azure AD-hez csatlakoztatott eszközök leegyszerűsítik:

- A Windows üzembe helyezését a vállalati tulajdonú eszközökön
- A hozzáférést a vállalati alkalmazásokhoz és erőforrásokhoz bármilyen Windows-eszközről
- A vállalati tulajdonú eszközök felhőalapú felügyeletét
- A felhasználók az Azure AD-vel vagy szinkronizált Active Directory munkahelyi vagy iskolai fiókkal jelentkezhetnek be az eszközre.

![Azure AD-hez csatlakoztatott eszközök](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Az alábbi módszerekkel létesíthet Azure AD-csatlakozást:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Tömeges üzembe helyezés](/intune/windows-bulk-enroll)
- [Önkiszolgáló megoldás használata](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD-csatlakozás implementálásának megtervezése](azureadjoin-plan.md)
- [A helyi Rendszergazdák csoport kezelése az Azure AD-hez csatlakoztatott eszközökön](assign-local-admin.md)
- [Eszközidentitások kezelése az Azure Portal használatával](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
