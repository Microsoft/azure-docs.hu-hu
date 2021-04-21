---
title: SSH-hitelesítés Azure Active Directory
description: Architekturális útmutató az SSH és a Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 315fe35a79ade39de9f541504fc2fe52754614de
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749347"
---
# <a name="ssh"></a>SSH  

A Secure Shell (SSH) egy hálózati protokoll, amely titkosítást biztosít a hálózati szolgáltatások biztonságos, nem biztonságos hálózaton való üzemeltetése érdekében. Az SSH parancssori bejelentkezést is biztosít, távoli parancsokat hajt végre, és biztonságosan továbbít fájlokat. Általában OLYAN UNIX-alapú rendszerekben használják, mint a Linux®. Az SSH felváltja a Telnet protokollt, amely nem biztosít titkosítást nem biztonságos hálózaton. 

Azure Active Directory (Azure AD) virtuálisgép-bővítményt biztosít az Azure-on futó Linux®alapú rendszerekhez. 

## <a name="use-when"></a>A következő esetekben használja 

* Távoli bejelentkezést ® igénylő Linux-alapú virtuális gépek

* Távoli parancsok végrehajtása Linux®-alapú rendszereken

* Fájlok biztonságos átvitele nem biztonságos hálózaton

![az Azure AD ábrája SSH protokollal](./media/authentication-patterns/ssh-auth.png)

SSH az Azure AD-val

## <a name="components-of-system"></a>A rendszer összetevői 

* **Felhasználó:** Elindítja az SSH-ügyfelet, hogy kapcsolatot létesítsen a Linux® virtuális gépekkel, és hitelesítő adatokat biztosít a hitelesítéshez.

* **Webböngésző:** Az az összetevő, amely a felhasználóval kommunikál. Az identitásszolgáltatóval (Azure AD) kommunikál a felhasználó biztonságos hitelesítése és hitelesítése érdekében.

* **SSH-ügyfél:** A kapcsolat beállításának folyamatát vezeti.

* **Azure AD:** Eszközfolyam használatával hitelesíti a felhasználó identitását, és jogkivonatot ad ki a Linux rendszerű virtuális gépeknek.

* **Linux rendszerű virtuális gép:** Fogadja a jogkivonatot, és biztosítja a sikeres csatlakozást.

## <a name="implement-ssh-with-azure-ad"></a>SSH implementálja az Azure AD-t 

* [Jelentkezzen be egy Linux® virtuális gépre Azure Active Directory hitelesítő adataival – Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2.0-eszközkódfolyam – Microsoft Identitásplatform ](../develop/v2-oauth2-device-code.md)

* [Integrálás Azure Active Directory (akamai.com)](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

