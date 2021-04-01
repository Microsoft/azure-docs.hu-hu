---
title: Bővítmények alkalmazás a Azure Active Directory B2Cban | Microsoft Docs
description: A B2C-Extensions-app visszaállítása.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94560854"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: bővítmények alkalmazás

Egy Azure AD B2C könyvtár létrehozásakor a rendszer automatikusan létrehoz egy nevű alkalmazást `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` az új könyvtáron belül. Ez az alkalmazás a **B2C-Extensions-app** néven látható *Alkalmazásregisztrációkban*. A Azure AD B2C szolgáltatás a felhasználókra és az egyéni attribútumokra vonatkozó információk tárolására szolgál. Az alkalmazás törlése esetén az Azure AD B2C nem fog megfelelően működni, ami hatással lesz az éles környezetre.

> [!IMPORTANT]
> Ne törölje a B2C-Extensions-app eszközt, ha nem tervezi azonnal törölni a bérlőjét. Ha az alkalmazás 30 napnál hosszabb ideig törölve marad, a felhasználói adatok véglegesen el lesznek veszítve.

## <a name="verifying-that-the-extensions-app-is-present"></a>Annak ellenőrzése, hogy a bővítmények alkalmazás megtalálható-e

Annak ellenőrzése, hogy a B2C-Extensions-app megtalálható-e:

1. A Azure AD B2C-bérlőn belül kattintson a bal oldali navigációs menü **összes szolgáltatás** elemére.
1. **Alkalmazásregisztrációk** keresése és megnyitása.
1. Keresse meg a **B2C-Extensions-app** kezdetű alkalmazást

## <a name="recover-the-extensions-app"></a>A bővítmények alkalmazás helyreállítása

Ha véletlenül törölte a B2C-Extensions-app-t, 30 napja van a helyreállításhoz. Az alkalmazást a Graph API használatával állíthatja vissza:

1. Lépjen a [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/) lapra.
1. Jelentkezzen be a webhelyre azon Azure AD B2C könyvtár globális rendszergazdájaként, amely számára vissza kívánja állítani a törölt alkalmazást. A globális rendszergazdának az alábbihoz hasonló e-mail-címmel kell rendelkeznie: `username@{yourTenant}.onmicrosoft.com` .
1. Adjon meg egy HTTP GET-t az URL `https://graph.windows.net/myorganization/deletedApplications` -cím és az API-Version = 1.6 között. Ez a művelet felsorolja az elmúlt 30 napban törölt összes alkalmazást.
1. Keresse meg az alkalmazást a listában, ahol a név "B2C-Extensions-app" kezdetű, és másolja a `objectid` tulajdonság értékét.
1. Adjon ki egy HTTP-BEJEGYZÉST az URL-címen `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` . Cserélje le az `{OBJECTID}` URL-cím részét az `objectid` előző lépésből származóra.

Ekkor [láthatja a visszaállított alkalmazást](#verifying-that-the-extensions-app-is-present) a Azure Portalban.

> [!NOTE]
> Egy alkalmazás csak akkor állítható vissza, ha az elmúlt 30 napban törölve lett. Ha 30 napnál hosszabb ideig tart, az adatvesztés megszűnik. További segítségért nyújtsa be a támogatási jegyet.
