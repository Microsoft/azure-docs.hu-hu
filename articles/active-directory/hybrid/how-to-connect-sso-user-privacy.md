---
title: Felhasználói adatvédelem és az Azure AD zökkenőmentes egyetlen Sign-On | Microsoft Docs
description: Ez a cikk a Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezéses és GDPR megfelelőségét tárgyalja.
services: active-directory
keywords: Mi a Azure AD Connect, GDPR, szükséges összetevők az Azure AD-hez, egyszeri bejelentkezéshez, egyszeri bejelentkezéshez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa1a30c548ef60cd9b596031f4115297dd20844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278598"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Felhasználói adatok védelme és az Azure AD közvetlen egyszeri bejelentkezése

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Áttekintés


Az Azure AD zökkenőmentes SSO a következő naplóbejegyzést hozza létre, amely személyes adattípusokat tartalmazhat: 

- Azure AD Connect nyomkövetési naplófájlok.

A zökkenőmentes SSO felhasználói adatainak fejlesztése kétféleképpen történhet:

1.  Kérelem esetén kinyerheti egy személy adatait, és eltávolíthatja az adott személy adatait a telepítésből.
2.  Ügyeljen arra, hogy a 48 órán túli adatmegőrzés ne legyen megtartva.

Erősen ajánljuk a második lehetőséget, mivel egyszerűbb megvalósítani és karbantartani. Tekintse meg az alábbi utasításokat minden naplózási típushoz:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect nyomkövetési naplófájlok törlése

Tekintse át a **%ProgramData%\AADConnect** mappa tartalmát, és törölje a mappához tartozó nyomkövetési napló tartalmát (**nyomkövetési \*** naplófájlok) a Azure ad Connect telepítésének vagy frissítésének 48 órán belül, illetve a zökkenőmentes SSO-konfiguráció módosításával, mivel ez a művelet a GDPR által érintett adatmennyiséget hozhat létre.

>[!IMPORTANT]
>Ne törölje a mappában található **PersistedState.xml** fájlt, mivel ez a fájl a Azure ad Connect előző telepítésének állapotát fogja használni, és a rendszer a frissítés telepítésekor használja. Ez a fájl soha nem tartalmaz semmilyen információt egy személyről, és soha nem törölhető.

Áttekintheti és törölheti ezeket a nyomkövetési naplófájlokat a Windows Intézőben, vagy a következő PowerShell-parancsfájl használatával elvégezheti a szükséges műveleteket:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Mentse a parancsfájlt egy fájlban a következővel:. PS1 "bővítmény. Szükség szerint futtassa ezt a parancsfájlt.

Ha többet szeretne megtudni a kapcsolódó Azure AD Connect GDPR vonatkozó követelményekről, tekintse meg [ezt a cikket](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Megjegyzés a tartományvezérlő naplóiról

Ha a naplózás engedélyezve van, ez a termék biztonsági naplókat hozhat a tartományvezérlőkhöz. Ha többet szeretne megtudni a naplózási házirendek konfigurálásáról, olvassa el ezt a [cikket](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Következő lépések

* [A Microsoft adatvédelmi szabályzatának áttekintése a megbízhatósági központban](https://www.microsoft.com/trustcenter)
  - [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.