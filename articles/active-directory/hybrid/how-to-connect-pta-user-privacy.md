---
title: Felhasználói adatvédelem és Azure Active Directory átmenő hitelesítés | Microsoft Docs
description: Ez a cikk Azure Active Directory (Azure AD) átmenő hitelesítéssel és GDPR-megfelelőséggel foglalkozik.
services: active-directory
keywords: Azure AD Connect átmenő hitelesítés, GDPR, szükséges összetevők az Azure AD-hez, egyszeri bejelentkezéshez, egyszeri bejelentkezéshez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996576"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Felhasználói adatok védelme és az Azure Active Directory átmenő hitelesítése


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Áttekintés

Az Azure AD átmenő hitelesítés a következő napló típusát hozza létre, amely személyes adattartalmakat tartalmazhat:

- Azure AD Connect nyomkövetési naplófájlok.
- A hitelesítési ügynök nyomkövetési naplófájljai.
- Windows-Eseménynapló fájljai.

Az átmenő hitelesítés felhasználói adatainak fejlesztése kétféleképpen történhet:

1.  Kérelem esetén kinyerheti egy személy adatait, és eltávolíthatja az adott személy adatait a telepítésből.
2.  Ügyeljen arra, hogy a 48 órán túli adatmegőrzés ne legyen megtartva.

Erősen ajánljuk a második lehetőséget, mivel egyszerűbb megvalósítani és karbantartani. Az egyes naplókhoz az alábbi utasításokat kell megírni:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect nyomkövetési naplófájlok törlése

Tekintse át a **%ProgramData%\AADConnect** mappa tartalmát, és törölje a mappához tartozó nyomkövetési napló tartalmát (**nyomkövetési \*** naplófájlok) a Azure ad Connect telepítésének vagy frissítésének 48 órán belül, illetve az átmenő hitelesítési konfiguráció módosításával, mivel ez a művelet a GDPR által érintett adatmennyiséget hozhat létre.

>[!IMPORTANT]
>Ne törölje a mappában található **PersistedState.xml** fájlt, mivel ez a fájl a Azure ad Connect előző telepítésének állapotát fogja használni, és a rendszer a frissítés telepítésekor használja. Ez a fájl soha nem tartalmaz semmilyen információt egy személyről, és soha nem törölhető.

Áttekintheti és törölheti ezeket a nyomkövetési naplófájlokat a Windows Intézőben, vagy a következő PowerShell-parancsfájl használatával elvégezheti a szükséges műveleteket:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Mentse a parancsfájlt egy fájlban a következővel:. PS1 "bővítmény. Szükség szerint futtassa ezt a parancsfájlt.

Ha többet szeretne megtudni a kapcsolódó Azure AD Connect GDPR vonatkozó követelményekről, tekintse meg [ezt a cikket](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Hitelesítési ügynök eseménynaplóinak törlése

Ez a termék Windows- **eseménynaplókat** is létrehozhat. További információért olvassa el [ezt a cikket](/windows/win32/wes/windows-event-log).

A továbbított hitelesítési ügynökhöz kapcsolódó naplók megtekintéséhez nyissa meg a **Eseménynapló** alkalmazást a kiszolgálón, és keresse meg az **alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>A hitelesítési ügynök nyomkövetési naplófájljainak törlése

Rendszeresen ellenőriznie kell a **%PROGRAMDATA%\MICROSOFT\AZURE ad-kapcsolat hitelesítési Agent\Trace** tartalmát, és 48 óránként törölni kell a mappa tartalmát. 

>[!IMPORTANT]
>Ha a hitelesítési ügynök szolgáltatás fut, nem fogja tudni törölni az aktuális naplófájlt a mappában. Próbálkozzon újra a szolgáltatás leállításával. Ha el szeretné kerülni a felhasználók bejelentkezési hibáit, akkor a [magas rendelkezésre állás](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)érdekében már be kell állítani az átmenő hitelesítést.

Ezeket a fájlokat áttekintheti és törölheti a Windows Intézőben, vagy a következő parancsfájl használatával elvégezheti a szükséges műveleteket:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

A parancsfájl 48 óránkénti futtatásához kövesse az alábbi lépéseket:

1.  Mentse a parancsfájlt egy fájlban a következővel:. PS1 "bővítmény.
2.  Nyissa meg a **Vezérlőpultot** , és kattintson a **rendszer és biztonság** elemre.
3.  A **felügyeleti eszközök** fejléc alatt kattintson a **feladatok ütemezhetnek** elemre.
4.  A **Feladatütemezőben** kattintson a jobb gombbal a "**feladatütemezés könyvtára**" elemre, majd kattintson az "**alapszintű feladat létrehozása..**." elemre.
5.  Adja meg az új feladat nevét, majd kattintson a **tovább** gombra.
6.  Válassza a "**napi**" lehetőséget a **feladat-triggerhez** , majd kattintson a **tovább** gombra.
7.  Állítsa be az ismétlődést két napra, és kattintson a **tovább** gombra.
8.  Válassza a **program indítása** műveletet, és kattintson a **tovább** gombra.
9.  Írja be a "**PowerShell**" kifejezést a program/parancsfájl mezőjébe, és az "**argumentumok hozzáadása (nem kötelező)**" feliratú mezőben adja meg a korábban létrehozott parancsfájl teljes elérési útját, majd kattintson a **tovább** gombra.
10. A következő képernyőn a létrehozandó feladat összegzése látható. Ellenőrizze az értékeket, és kattintson a **Befejezés** gombra a feladat létrehozásához:
 
### <a name="note-about-domain-controller-logs"></a>Megjegyzés a tartományvezérlő naplóiról

Ha a naplózás engedélyezve van, ez a termék biztonsági naplókat hozhat a tartományvezérlőkhöz. Ha többet szeretne megtudni a naplózási házirendek konfigurálásáról, olvassa el ezt a [cikket](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Következő lépések
* [A Microsoft adatvédelmi szabályzatának áttekintése a megbízhatósági központban](https://www.microsoft.com/trustcenter)
* [**Hibaelhárítás**](tshoot-connect-pass-through-authentication.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.