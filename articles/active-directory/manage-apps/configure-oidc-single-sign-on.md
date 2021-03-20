---
title: A OIDC-alapú egyszeri bejelentkezés (SSO) megismerése az Azure Active Directoryban lévő alkalmazásokhoz
description: Az OIDC-alapú egyszeri bejelentkezés (SSO) megismerése Azure Active Directory alkalmazásokban.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: ffaa55d4aa482e8f0eda93b1b32db6310d17e2a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255249"
---
# <a name="understand-oidc-based-single-sign-on"></a>A OIDC-alapú egyszeri bejelentkezés ismertetése
Az alkalmazások felügyeletének rövid útmutató [sorozatában](view-applications-portal.md) megtudhatta, hogyan használhatja az Azure ad-t az alkalmazás identitás-szolgáltatója (identitásszolgáltató) használatával. Ez a cikk részletesebben ismerteti azokat az alkalmazásokat, amelyek az OpenID Connect standardot használják az egyszeri bejelentkezés megvalósításához. 

## <a name="before-you-begin"></a>Előkészületek
Az alkalmazások Azure Active Directory bérlőhöz való hozzáadásának folyamata az alkalmazás által megvalósított egyszeri bejelentkezés típusától függ. Ha többet szeretne megtudni az Azure AD-t használó alkalmazásokhoz használható egyszeri bejelentkezési lehetőségekről, tekintse meg az [egyszeri bejelentkezés lehetőségeit](sso-options.md). Ez a cikk a OIDC-alapú alkalmazásokat ismerteti.


## <a name="basic-oidc-configuration"></a>Alapszintű OIDC-konfiguráció
A gyors üzembe helyezési [sorozatban](add-application-portal-setup-oidc-sso.md)van egy cikk, amely az egyszeri bejelentkezés konfigurálását ismerteti. Ebben a témakörben megtudhatja, hogyan adhat hozzá egy OIDC-alapú alkalmazást az Azure-bérlőhöz.

A jó megoldás, ha olyan alkalmazást ad hozzá, amely az egyszeri bejelentkezéshez a OIDC standardot használja, a konfiguráció minimális. Íme egy rövid videó, amely bemutatja, hogyan adhat hozzá egy OIDC-alapú alkalmazást a bérlőhöz.

OIDC-alapú alkalmazás hozzáadása Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

A felhasználói és rendszergazdai engedélyekkel kapcsolatos további információkért lásd: [felhasználói és rendszergazdai jogosultságok megismerése](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Következő lépések

- [Gyorsindítás sorozat az alkalmazás-kezelésben](add-application-portal-setup-oidc-sso.md)
- [Az egyszeri bejelentkezés beállításai](sso-options.md)
- [Útmutató: Azure Active Directory-felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával](../develop/howto-convert-app-to-be-multi-tenant.md)
