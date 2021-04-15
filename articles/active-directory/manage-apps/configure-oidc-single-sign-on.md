---
title: Az OIDC-alapú egyszeri bejelentkezés (SSO) a Azure Active Directory
description: Az OIDC-alapú egyszeri bejelentkezés (SSO) használata a Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: iangithinji
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: 990e0c09f8a49b83bc68d7123f5f8146a3551575
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374539"
---
# <a name="understand-oidc-based-single-sign-on"></a>Az OIDC-alapú egyszeri bejelentkezés
Az [alkalmazáskezelés](view-applications-portal.md) gyorsútmutató-sorozatában megtanulta, hogyan használhatja az Azure AD-t identitásszolgáltatóként (IdP) egy alkalmazáshoz. Ez a cikk részletesebben ismerteti az egyszeri bejelentkezést OpenID Connect szabványos alkalmazásokat. 

## <a name="before-you-begin"></a>Előkészületek
Az alkalmazásnak a Azure Active Directory való hozzáadásának folyamata a megvalósított alkalmazás egyszeri bejelentkezésének típusától függ. Az Azure AD-t identitáskezelésre használható alkalmazásokhoz elérhető egyszeri bejelentkezési lehetőségekkel kapcsolatos további információkért lásd: Egyszeri [bejelentkezési lehetőségek.](sso-options.md) Ez a cikk az OIDC-alapú alkalmazásokat tartalmazza.


## <a name="basic-oidc-configuration"></a>Alapszintű OIDC-konfiguráció
A [gyorsútmutató-sorozatban](add-application-portal-setup-oidc-sso.md)az egyszeri bejelentkezés konfigurálását egy cikk írja le. Ebből megtudhatja, hogyan adhat hozzá OIDC-alapú alkalmazást az Azure-bérlőhöz.

Az OIDC szabványt az egyszeri bejelentkezéshez használó alkalmazások hozzáadásával az a jó, hogy a konfiguráció minimális. Az alábbi rövid videó bemutatja, hogyan adhat hozzá OIDC-alapú alkalmazást a bérlőhöz.

OIDC-alapú alkalmazás hozzáadása a Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

A felhasználói és rendszergazdai hozzájárulással kapcsolatos további információkért lásd: A felhasználói és rendszergazdai [hozzájárulás megismerve.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

## <a name="next-steps"></a>Következő lépések

- [Gyorsútmutató-sorozat az alkalmazáskezelésről](add-application-portal-setup-oidc-sso.md)
- [Az egyszeri bejelentkezés beállításai](sso-options.md)
- [Útmutató: Azure Active Directory-felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával](../develop/howto-convert-app-to-be-multi-tenant.md)
