---
title: SSH-hitelesítés Azure Active Directory
description: Építészeti útmutató az SSH-integráció megvalósításához Azure Active Directory
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
ms.openlocfilehash: 3b77ab0832fa19149c270d6ba5a6641069548cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172720"
---
# <a name="ssh"></a>SSH  

A Secure Shell (SSH) egy hálózati protokoll, amely titkosítást biztosít az operációs hálózati szolgáltatásokhoz a nem biztonságos hálózaton keresztül. Az SSH parancssori bejelentkezést is biztosít, végrehajtja a távoli parancsokat, és biztonságosan továbbítja a fájlokat. Ez általában a UNIX-alapú rendszerekben, például a Linux®ban használatos. Az SSH lecseréli a Telnet protokollt, amely nem biztosít titkosítást egy nem biztonságos hálózatban. 

Azure Active Directory (Azure AD) virtuálisgép-bővítményt biztosít az Azure-ban futó Linux®-alapú rendszerekhez. 

## <a name="use-when"></a>A következő esetekben használja 

* Távoli bejelentkezést igénylő linuxos®-alapú virtuális gépek használata

* Távoli parancsok végrehajtása Linux®-alapú rendszerekben

* Fájlok biztonságos átvitele nem biztonságos hálózatban

![Az Azure AD és az SSH protokoll diagramja](./media/authentication-patterns/ssh-auth.png)

SSH az Azure AD-vel

## <a name="components-of-system"></a>A System összetevői 

* **Felhasználó**: elindítja az SSH-ügyfelet a Linux® virtuális gépekkel létesített kapcsolatok beállítására, valamint hitelesítő adatokat biztosít a hitelesítéshez.

* **Webböngésző**: az az összetevő, amellyel a felhasználó kommunikál. Az identitás-szolgáltatóval (Azure AD) kommunikál a felhasználó biztonságos hitelesítéséhez és engedélyezéséhez.

* **SSH-ügyfél**: a kapcsolatok telepítési folyamatát vezérli.

* **Azure ad**: hitelesíti a felhasználó identitását az eszköz folyamata segítségével, és a Linux rendszerű virtuális gépekhez jogkivonatot bocsát ki.

* **Linuxos virtuális gép**: fogadja a tokent, és sikeres kapcsolatokat biztosít.

## <a name="implement-ssh-with-azure-ad"></a>SSH implementálása az Azure AD-vel 

* [Jelentkezzen be egy linuxos® virtuális gépre Azure Active Directory hitelesítő adatokkal – Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2,0-es eszköz kódjának folyamata – Microsoft Identity platform ](../develop/v2-oauth2-device-code.md)

* [Integrálás a Azure Active Directoryrel (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

