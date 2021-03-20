---
title: Mi az a hibrid Azure AD-hez csatlakoztatott eszköz?
description: Megtudhatja, hogyan segítheti a környezet erőforrásaihoz hozzáférő eszközök felügyeletét.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85554924"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-csatlakoztatott eszközök

Több mint egy évtizede számos cég használja a tartományi csatlakoztatást a helyszíni Active Directoryhoz, mivel az a következő előnyökkel jár:

- Az informatikai részlegek egy központi helyről felügyelhetik a vállalati tulajdonú eszközöket.
- A felhasználók bejelentkezhetnek az eszközeiken a munkahelyi vagy iskolai Active Directory-fiókokkal.

A helyszíni lábnyommal rendelkező szervezetek általában az eszközök kiépítéséhez használják a képmódszereket, és gyakran **Configuration Manager** vagy **Csoportházirend (GP)** használatával kezelik azokat.

Ha az Ön környezetének van helyszíni AD-lábnyoma, ugyanakkor az Azure Active Directory nyújtotta lehetőségeket is ki szeretné használni, implementálhat hibrid Azure AD-csatlakoztatott eszközöket. Ezek az eszközök a helyszíni Active Directoryhoz csatlakoztatott és a Azure Active Directory regisztrált eszközök.

| Hibrid Azure AD-csatlakozás | Description |
| --- | --- |
| **Definíció** | Csatlakozott a helyszíni AD-hez és az Azure AD-hez, amely szervezeti fiókot igényel az eszközre való bejelentkezéshez |
| **Elsődleges célközönség** | Alkalmas a meglévő helyszíni AD-infrastruktúrával rendelkező hibrid szervezetek számára |
|   | Egy szervezet összes felhasználójára érvényes |
| **Az eszközök tulajdonjoga** | Szervezet |
| **Operációs rendszerek** | Windows 10, 8,1 és 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 és 2019 |
| **Telepítés** | Windows 10, Windows Server 2016/2019 |
|   | Tartományhoz való csatlakozás, valamint Azure AD Connect vagy ADFS-konfiguráción keresztüli autojoin |
|   | Tartományhoz való csatlakozás a Windows Autopilot és az autojoin használatával Azure AD Connect vagy ADFS-konfiguráció segítségével |
|   | Windows 8,1, Windows 7, Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2 – MSI megkövetelése |
| **Eszköz bejelentkezési beállításai** | Szervezeti fiókok a használatával: |
|   | Jelszó |
|   | Vállalati Windows Hello Win10 |
| **Eszközfelügyelet** | Csoportházirend |
|   | Önálló vagy közös felügyelet Configuration Manager Microsoft Intune |
| **Főbb képességek** | Egyszeri bejelentkezés mind a Felhőbeli, mind a helyszíni erőforrásokhoz |
|   | Feltételes hozzáférés tartományhoz illesztéssel vagy az Intune-on keresztül, ha közösen felügyelt |
|   | Önkiszolgáló jelszó-visszaállítás és a Windows Hello PIN-kód alaphelyzetbe állítása a zárolási képernyőn |
|   | Enterprise State Roaming eszközök között |

![Hibrid Azure AD-csatlakoztatott eszközök](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Forgatókönyvek

Használja az Azure AD Hybrid-hez csatlakoztatott eszközöket, ha:

- Ha Win32-alkalmazások vannak telepítve azokon az eszközökön, amelyek az Active Directory-géphitelesítést használják.
- Továbbra is használni szeretné a Csoportházirend az eszköz konfigurációjának kezeléséhez.
- Továbbra is meglévő lemezkép-készítési megoldásokat kíván használni az eszközök telepítéséhez és konfigurálásához.
- A Windows 10 rendszeren kívül a Windows 7 és a 8,1 rendszerű eszközök támogatását is támogatnia kell

## <a name="next-steps"></a>Következő lépések

- [A hibrid Azure AD-csatlakozás implementálásának megtervezése](hybrid-azuread-join-plan.md)
- [Eszközidentitások kezelése az Azure Portal használatával](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
