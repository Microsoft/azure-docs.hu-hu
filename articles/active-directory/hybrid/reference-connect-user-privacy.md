---
title: Azure AD Connect és felhasználói adatvédelem | Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan szerezhet be Azure AD Connect GDPR-kompatibilitás.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997698"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Felhasználói adatvédelem és Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk a Azure AD Connect és a felhasználói adatvédelmet tárgyalja.  A Azure AD Connect Health és a felhasználói adatok védelméről a cikk [itt](reference-connect-health-user-privacy.md)található.

A Azure AD Connect-telepítések felhasználói adatainak fejlesztése kétféleképpen történhet:

1.  Kérelem esetén kinyerheti egy személy adatait, és eltávolíthatja az adott személy adatait a telepítésből.
2.  Ügyeljen arra, hogy a 48 órán túli adatmegőrzés ne legyen megtartva.

A Azure AD Connect csapat a második lehetőséget javasolja, mivel sokkal egyszerűbb megvalósítani és karbantartani.

Egy Azure AD Connect szinkronizáló kiszolgáló a következő felhasználói adatvédelmi adatokat tárolja:
1.  A **Azure ad Connect-adatbázisban** található személlyel kapcsolatos információk
2.  A Windows- **eseménynaplók** olyan adatai, amelyek egy személyre vonatkozó információkat tartalmazhatnak
3.  A **Azure ad Connect telepítési naplófájljaiban** lévő, személyre vonatkozó információkat tartalmazó fájlok

Azure AD Connect ügyfeleknek a következő irányelveket kell használniuk a felhasználói adatvesztések eltávolításakor:
1.  Törölje a Azure AD Connect telepítési naplófájlokat rendszeres időközönként tartalmazó mappa tartalmát – legalább 48 óránként
2.  Ez a termék eseménynaplókat is létrehozhat.  Az eseménynaplók naplóival kapcsolatos további tudnivalókért tekintse meg a [dokumentációt itt](/windows/win32/wes/windows-event-log).

Egy személy adatait a rendszer automatikusan eltávolítja a Azure AD Connect-adatbázisból, ha az adott személy adatait eltávolítja a forrásból, ahonnan származik. Nem szükséges, hogy a rendszergazdák GDPR a megfelelő lépéseket.  Szükség van azonban arra, hogy a Azure AD Connect-adatait legalább két naponként szinkronizálja az adatforrással.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>A Azure AD Connect telepítési naplófájl mappa tartalmának törlése
Rendszeresen vizsgálja meg és törölje a **c:\programdata\aadconnect** mappa tartalmát – kivéve a **PersistedState.Xml** fájlt. Ez a fájl fenntartja az Azure-beli kapcsolat előző telepítésének állapotát, és a frissítés telepítésének végrehajtásakor használatos. Ez a fájl nem tartalmaz egy személy adatait, és nem törölhető.

>[!IMPORTANT]
>Ne törölje a PersistedState.xml fájlt.  Ez a fájl nem tartalmaz felhasználói adatokat, és fenntartja az előző telepítés állapotát.

Ezeket a fájlokat áttekintheti és törölheti a Windows Intéző használatával, vagy a szükséges műveletek elvégzéséhez a következőhöz hasonló parancsfájlt használhat:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>A parancsfájl ütemezett futtatása 48 óránként
Az alábbi lépések végrehajtásával ütemezze a parancsfájlt minden 48 óránkénti futásra.

1.  Mentse a parancsfájlt a **&#46;ps1** kiterjesztésű fájlba, majd nyissa meg a Vezérlőpultot, és kattintson a **rendszerek és biztonság** elemre.
    ![Rendszer](./media/reference-connect-user-privacy/gdpr2.png)

2.  A felügyeleti eszközök fejléc alatt kattintson az **ütemezett feladatok** lehetőségre.
    ![Tevékenység](./media/reference-connect-user-privacy/gdpr3.png)
3.  A Feladatütemezőben kattintson a jobb gombbal a **feladat-ütemezési könyvtárra** , majd kattintson az **alapszintű feladat létrehozása..** . elemre.
4.  Adja meg az új feladat nevét, majd kattintson a **tovább** gombra.
5.  Válassza a **napi** lehetőséget a feladat-triggerhez, majd kattintson a **tovább** gombra.
6.  Állítsa be az ismétlődést **2 napra** , és kattintson a **tovább** gombra.
7.  Válassza a **program elindítása** műveletként lehetőséget, majd kattintson a **tovább** gombra.
8.  Írja be a **PowerShell** nevet a program/parancsfájl mezőbe, és az **argumentumok hozzáadása (nem kötelező)** mezőben adja meg a korábban létrehozott parancsfájl teljes elérési útját, majd kattintson a **tovább** gombra.
9.  A következő képernyőn a létrehozandó feladat összegzése látható. Ellenőrizze az értékeket, majd kattintson a **Befejezés** gombra a feladat létrehozásához.



## <a name="next-steps"></a>Következő lépések
* [A Microsoft adatvédelmi szabályzatának áttekintése a megbízhatósági központban](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health és felhasználói adatvédelem](reference-connect-health-user-privacy.md)