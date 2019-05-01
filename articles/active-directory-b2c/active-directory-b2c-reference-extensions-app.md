---
title: Az Azure Active Directory B2C-ben bővítményalkalmazás |} A Microsoft Docs
description: A b2c-kiterjesztések alkalmazását visszaállítása.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 9/06/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: aee21e6ea3c598818091aa11c3d13b9d6172743c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703439"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Bővítményalkalmazás

Az Azure AD B2C-címtár jön létre, amikor egy alkalmazás nevű `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` belül az új címtár automatikusan létrejön. Ez az alkalmazás a továbbiakban a **b2c-kiterjesztések alkalmazását**, látható az *alkalmazásregisztrációk*. Azt az Azure AD B2C-szolgáltatás által tárolására szolgál felhasználók és az egyéni attribútumokat. Ha az alkalmazást törlik, Azure AD B2C-vel nem fognak megfelelően működni, és hatással lesz az éles környezetben.

> [!IMPORTANT]
> Ne törölje a b2c-kiterjesztések alkalmazását, kivéve, ha azt tervezi, azonnal törli a bérlőn. Ha az alkalmazás több mint 30 napig törölt marad, felhasználói adatok végleg elvesznek.

## <a name="verifying-that-the-extensions-app-is-present"></a>Annak ellenőrzése, hogy jelen-e a bővítményalkalmazás

Annak ellenőrzése, hogy jelen-e a b2c-kiterjesztések alkalmazását:

1. Az Azure AD B2C-bérlő, belül kattintson az **minden szolgáltatás** a bal oldali navigációs menü.
1. Keresse meg és nyissa meg a **alkalmazásregisztrációk**.
1. Keresse meg, hogy a következővel kezdődik: **b2c-kiterjesztések alkalmazását**

## <a name="recover-the-extensions-app"></a>A bővítmények app helyreállítása

Ha véletlenül törölte a b2c-kiterjesztések alkalmazását, hogy 30 napig, amellyel a helyreállítást. Visszaállíthatja az alkalmazást a Graph API-val:

1. Keresse meg a [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/).
1. Jelentkezzen be globális rendszergazdaként az Azure AD B2C-címtár, amely szeretné visszaállítani a törölt alkalmazás esetében a hely. A globális rendszergazdának rendelkeznie kell egy e-mail-címet a következőhöz hasonló: `username@{yourTenant}.onmicrosoft.com`.
1. Adja ki az URL-cím alapján egy HTTP GET `https://graph.windows.net/myorganization/deletedApplications` az api-version = 1.6-os. Ez a művelet listázza az összes alkalmazást, ami az elmúlt 30 napon belül törölve lett.
1. Keresse meg az alkalmazást a listában, ahol "b2c-bővítmény-app" és a példány kezdődik-e a neve annak `objectid` tulajdonság értéke.
1. Adja ki az URL-cím alapján egy HTTP POST `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Cserélje le a `{OBJECTID}` az URL-címrészt az `objectid` az előző lépésben. 

Most meg kell tudni [tekintse meg a visszaállított app](#verifying-that-the-extensions-app-is-present) az Azure Portalon.

> [!NOTE]
> Egy alkalmazás csak akkor állíthatók vissza, ha törölték az elmúlt 30 napban. Ha több mint 30 napig volt, adatok végleg elvesznek. További segítséget küldjön egy támogatási jegyet.
