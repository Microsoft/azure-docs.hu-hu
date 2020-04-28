---
title: 'Azure AD Connect: mi a ADConnectivityTool PowerShell-modulja | Microsoft Docs'
description: Ez a dokumentum bemutatja az új ADConnectivity PowerShell-modult, valamint azt, hogy miként használható a hibák megoldásához.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "64571115"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Az Azure AD-kapcsolat hibáinak megoldása a ADConnectivityTool PowerShell-modullal

A ADConnectivity eszköz egy PowerShell-modul, amely a következők egyikében használható:

- A telepítés során, amikor egy hálózati kapcsolattal kapcsolatos probléma meggátolja a Active Directory hitelesítő adatainak sikeres érvényesítését a varázslóban megadott felhasználó számára.
- A telepítést egy olyan felhasználó küldi, aki egy PowerShell-munkamenetből hívja meg a függvényeket.

Az eszköz a ben található: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool. psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool a telepítés során

Ha hálózati probléma lép fel a **címtárak Összekapcsolása** Azure ad Connect lapon, akkor a ADConnectivityTool automatikusan az egyik funkciója alapján határozza meg, hogy mi történik.  A következők bármelyike hálózati problémáknak minősülhet:

- A felhasználó által megadott erdő neve helytelenül lett beírva, vagy azt mondta, hogy az erdő nem létezik 
- Az 389-es UDP-port be van zárva a felhasználó által megadott erdőhöz társított tartományvezérlőkön.
- Az "AD Forest-fiók" ablakban megadott hitelesítő adatok nem rendelkeznek jogosultsággal a célként kijelölt erdőhöz társított tartományvezérlők beolvasásához.
- A 53, 88 vagy 389 TCP-portok bármelyike le van zárva a felhasználó által megadott erdőhöz társított tartományvezérlőkön. 
- Mind az UDP 389, mind a TCP-port (vagy portok) be van zárva
- A DNS-t nem sikerült feloldani a megadott erdőhöz tartozó and\or a társított tartományvezérlőkre

Ha bármelyik probléma megtalálható, a AADConnect varázslóban megjelenik egy kapcsolódó hibaüzenet:


![Hiba](media/how-to-connect-adconnectivitytools/error1.png)

Ha például egy könyvtárat próbálunk felvenni a **Csatlakozás a címtárakhoz** képernyőn, Azure ad Connect ellenőriznie kell ezt, és tudnia kell, hogy képes-e kommunikálni a tartományvezérlővel a 389-es porton keresztül.  Ha nem, akkor a fenti képernyőképen látható hibaüzenet jelenik meg.  

Valójában a háttérben történik, hogy Azure AD Connect meghívja a `Start-NetworkConnectivityDiagnosisTools` függvényt.  Ezt a függvényt akkor hívja meg a rendszer, ha a hitelesítő adatok érvényesítése a hálózati kapcsolat hibája miatt meghiúsul.

Végül egy részletes naplófájl jön létre, amikor az eszközt meghívja a varázsló. A napló a **C:\ProgramData\AADConnect\ADConnectivityTool-\<dátum>\<idő>. log** mappában található.

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools utáni telepítés
Azure AD Connect telepítését követően a ADConnectivityTools PowerShell-modul egyik funkciója is használható.  

A [ADConnectivityTools-hivatkozásban](reference-connect-adconnectivitytools.md) található függvényekkel kapcsolatos információkat a következő témakörben talál:.

### <a name="start-connectivityvalidation"></a>Start – ConnectivityValidation

Meghívjuk ezt a függvényt, mert **csak** manuálisan hívható meg, ha a ADConnectivityTool. Psm1 a powershellbe lett importálva. 

Ez a függvény ugyanazt a logikát hajtja végre, amelyet a Azure AD Connect varázsló futtat a megadott AD hitelesítő adatok ellenőrzéséhez.  Ugyanakkor részletesebb magyarázatot nyújt a problémáról és a javasolt megoldásról. 

A kapcsolat ellenőrzése a következő lépésekből áll:
-   Tartomány FQDN-objektumának beolvasása (teljes tartománynév)
-   Annak ellenőrzése, hogy ha a felhasználó az "új AD-fiók létrehozása" lehetőséget választotta, akkor ezek a hitelesítő adatok a vállalati rendszergazdák csoportba tartoznak.
-   Erdő FQDN-objektumának beolvasása
-   Győződjön meg arról, hogy legalább egy, a korábban beszerzett erdő FQDN-objektumhoz társított tartomány elérhető
-   Ellenőrizze, hogy az erdő működési szintje Windows Server 2003 vagy újabb.

A felhasználó hozzáadhat egy könyvtárat, ha az összes művelet végrehajtása sikeresen befejeződött.

Ha a felhasználó a probléma megoldása után futtatja ezt a funkciót (vagy ha egyáltalán nem található probléma), a kimenet azt jelzi, hogy a felhasználó visszatérhet a Azure AD Connect varázslóhoz, majd próbálja újra beszúrni a hitelesítő adatokat.



## <a name="next-steps"></a>Következő lépések
- [Azure AD Connect: fiókok és engedélyek](reference-connect-accounts-permissions.md)
- [Expressz telepítés](how-to-connect-install-express.md)
- [Egyéni telepítés](how-to-connect-install-custom.md)
- [Az ADConnectivityTools referenciája](reference-connect-adconnectivitytools.md)

