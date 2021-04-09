---
title: Azure AD FS-támogatás (MSAL Python)
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg Active Directory összevonási szolgáltatások (AD FS) (AD FS) támogatással a Pythonhoz készült Microsoft Authentication Library-ben
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d847e38a6114b0e5f8d56f6e994aa47dfbe929e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583705"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Active Directory összevonási szolgáltatások (AD FS) támogatás a MSAL for Pythonban

A Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) lehetővé teszi az OpenID Connect és a OAuth 2,0-alapú hitelesítés hozzáadását, valamint az alkalmazások engedélyezését a Pythonhoz készült Microsoft Authentication Library (MSAL) használatával. Az alkalmazás a Python-kódtár MSAL használatával közvetlenül hitelesítheti a felhasználókat AD FS. További információ a forgatókönyvekről: [AD FS forgatókönyvek fejlesztőknek](/windows-server/identity/ad-fs/ad-fs-development).

A AD FSon általában kétféleképpen lehet hitelesíteni:

- A MSAL Python a Azure Active Directoryra beszél, amely más identitás-szolgáltatókkal összevont. Az összevonás AD FSon történik. A MSAL Python csatlakozik az Azure AD-hez, amely az Azure AD-ben (felügyelt felhasználók) felügyelt felhasználók vagy más identitás-szolgáltató, például AD FS (összevont felhasználók) által felügyelt felhasználók számára jelentkezik be. A MSAL Python nem tudja, hogy egy felhasználó összevont. Egyszerűen beszél az Azure AD-ről. Az ebben az esetben [használt szolgáltató a](msal-client-application-configuration.md#authority) szokásos hatóság (szolgáltatói állomásnév + bérlő, közös vagy szervezet).
- A MSAL Python közvetlenül egy AD FS-szolgáltatóhoz beszél. Ezt csak a AD FS 2019-es és újabb verziói támogatják.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Kapcsolódás Active Directory összevont AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Jogkivonat interaktív beszerzése egy összevont felhasználó számára

Az alábbiak azt jelzik, hogy közvetlenül a Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy a Active Directoryon keresztül csatlakoznak-e.

A hívásakor `acquire_token_by_authorization_code` `acquire_token_by_device_flow` a felhasználói élmény általában a következő:

1. A felhasználó megadja a fiók AZONOSÍTÓját.
2. Az Azure AD röviden megjeleníti a "saját szervezet lapja" üzenetet, és a felhasználó átirányítja az azonosító bejelentkezési oldalára. A bejelentkezési oldal általában a szervezet emblémájának megfelelően van testreszabva.

Az összevont forgatókönyvben támogatott AD FS verziók a következők:
- Active Directory összevonási szolgáltatások (AD FS) FS v2
- Active Directory összevonási szolgáltatások (AD FS) v3 (Windows Server 2012 R2)
- Active Directory összevonási szolgáltatások (AD FS) v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Token beszerzése Felhasználónév és jelszó használatával

Az alábbiak azt jelzik, hogy közvetlenül a Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy a Active Directoryon keresztül csatlakoznak-e.

Ha a használatával szerez be tokent, a MSAL Python beolvassa `acquire_token_by_username_password` az identitás-szolgáltatót a Felhasználónév alapján. A MSAL Python [SAML 1,1-tokent](reference-saml-tokens.md) kap az identitás-szolgáltatótól, amelyet ezután az Azure ad-nek küld, amely visszaadja a JSON web token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Közvetlen csatlakozás AD FS

Ha a címtárat a AD FShoz kapcsolódik, az alkalmazás létrehozásához használni kívánt szolgáltató a következőhöz hasonló lesz: `https://somesite.contoso.com/adfs/`

A MSAL Python az ADFS 2019-et támogatja.

Nem támogatja a közvetlen kapcsolódást az ADFS 2016 vagy az ADFS v2 szolgáltatáshoz. Az ADFS 2016-hez való közvetlen kapcsolódást igénylő forgatókönyvek támogatásához használja a ADAL Python legújabb verzióját. Miután frissítette a helyszíni rendszert az ADFS 2019-re, használhatja a MSAL Pythont.

## <a name="next-steps"></a>Következő lépések

- Az összevont esetért lásd: [Azure Active Directory bejelentkezési viselkedésének konfigurálása alkalmazáshoz egy otthoni tartomány felderítési házirendjének használatával](../manage-apps/configure-authentication-for-federated-users-portal.md)
