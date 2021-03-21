---
title: Önálló felügyelt szolgáltatásfiókok biztonságossá tétele | Azure Active Directory
description: Útmutató az önálló felügyelt szolgáltatásfiókok biztonságossá tételéhez.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2b3079407774c3d36fe5515b39e964018f9087e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548852"
---
# <a name="securing-standalone-managed-service-accounts"></a>Önálló felügyelt szolgáltatásfiókok biztonságossá tétele

A különálló felügyelt szolgáltatásfiókok (sMSAs) olyan felügyelt tartományi fiókok, amelyek egy vagy több kiszolgálón futó szolgáltatás biztonságossá tételére szolgálnak. Nem használhatók fel újra több kiszolgáló között. a sMSAs biztosítja az automatikus jelszavas kezelést, az egyszerű egyszerű szolgáltatásnév (SPN) felügyeletét, valamint a felügyelet más rendszergazdák számára való delegálásának lehetőségét. 

Active Directory a sMSAs egy szolgáltatást futtató adott kiszolgálóhoz vannak kötve. Ezeket a fiókokat a Microsoft Management Console Active Directory felhasználók és számítógépek beépülő moduljában találja.

![Képernyőfelvétel a felügyelt szolgáltatásfiókok szervezeti egységét megjelenítő Active Directory felhasználók és számítógépek beépülő modulról.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

A felügyelt szolgáltatásfiókok a Windows Server 2008R2 Active Directory sémával lettek bevezetve, és minimális operációsrendszer-szintet igényelnek a Windows Server 2008R2. 

## <a name="benefits-of-using-smsas"></a>A sMSAs használatának előnyei

a sMSAs nagyobb biztonságot kínál, mint a szolgáltatásfiókokként használt felhasználói fiókok, miközben csökkenti az adminisztrációs terhelést:

* Erős jelszavak beállítása. a sMSAs 240 bájtos véletlenszerűen generált összetett jelszavakat használ. A önállóan felügyelt szolgáltatásfiókot-jelszavak összetettsége és hossza minimálisra csökken annak valószínűsége, hogy egy szolgáltatás feltörte a találgatásos vagy a szótári támadásokat.

* A kerékpárosok jelszavainak rendszeres használata. A Windows 30 naponként automatikusan módosítja a önállóan felügyelt szolgáltatásfiókot jelszavát. A szolgáltatás-és tartományi rendszergazdáknak nem kell ütemezniük a jelszó módosítását, vagy a kapcsolódó állásidőt kell kezelnie.

* Az SPN-kezelés egyszerűsítése. A szolgáltatás egyszerű nevei automatikusan frissülnek, ha a tartomány működési szintje (DFL) Windows Server 2008 R2. Az egyszerű szolgáltatásnév például a következő helyzetekben automatikusan frissül:

   * A gazdagép számítógépfiókja át lett nevezve. 

   * A gazdaszámítógép DNS-neve módosítva.

   * További Sam-accountname vagy DNS-állomásnév paraméterek hozzáadásakor vagy eltávolításakor a [PowerShell](/powershell/module/addsadministration/set-adserviceaccount) használatával

## <a name="when-to-use-smsas"></a>Mikor kell használni a sMSAs

a sMSAs egyszerűbbé teheti a felügyeleti és biztonsági feladatokat. Akkor használja a sMSAs, ha egy vagy több, egyetlen kiszolgálóra telepített szolgáltatást használ, és nem használhat gMSA. 

> [!NOTE] 
> Habár a sMSAs-t több szolgáltatáshoz is használhatja, javasoljuk, hogy mindegyik szolgáltatásnak saját identitása legyen a naplózási célokra. 

Ha a szoftver létrehozója nem tudja megállapítani, hogy tud-e MSA használni, tesztelje az alkalmazást. Ehhez hozzon létre egy tesztkörnyezet, és győződjön meg arról, hogy az összes szükséges erőforráshoz hozzáfér. Részletes útmutatásért lásd: [önállóan felügyelt szolgáltatásfiókot létrehozása és telepítése](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) .

### <a name="assess-security-posture-of-smsas"></a>A sMSAs biztonsági helyzetének felmérése

a sMSAs eleve biztonságosabbak, mint a normál felhasználói fiókok, amelyek folyamatos jelszavas kezelést igényelnek. Fontos azonban figyelembe venni a sMSAs hatókörét az általános biztonsági helyzetük részeként.

Az alábbi táblázat bemutatja, hogyan csökkentheti a sMSAs által jelentett lehetséges biztonsági problémákat.

| Biztonsági problémák| Kezelési lehetőségek |
| - | - |
| a önállóan felügyelt szolgáltatásfiókot a Kiemelt csoportok tagja|Távolítsa el a önállóan felügyelt szolgáltatásfiókot a emelt szintű jogosultságú csoportokból (például Tartománygazdák). <br> Használja a legkevésbé privilegizált modellt, és csak a szolgáltatás (ok) futtatásához szükséges jogosultságokat és engedélyeket adja meg a önállóan felügyelt szolgáltatásfiókot. <br> Ha nem biztos a szükséges engedélyekben, tekintse meg a szolgáltatás létrehozóját. |
| a önállóan felügyelt szolgáltatásfiókot olvasási/írási hozzáféréssel rendelkezik a bizalmas erőforrásokhoz.|Bizalmas erőforrásokhoz való hozzáférés naplózása. Archiválja a naplókat egy SIEM-re (Azure Log Analytics vagy Azure Sentinel) elemzésre. <br> Erőforrás-engedélyek szervizelése, ha nemkívánatos hozzáférési szint észlelhető. |
| Alapértelmezés szerint a önállóan felügyelt szolgáltatásfiókot jelszó-átváltási gyakorisága 30 nap| A csoportházirend segítségével beállíthatja az időtartamot a vállalati biztonsági követelményektől függően. <br> * A jelszó lejárati idejét a következő elérési úttal állíthatja be. <br>Számítógép konfigurációja \ biztonsági beállítások \ felhasználói Options\Domain tag: a számítógépfiók jelszavának maximális kora |



### <a name="challenges-with-smsas"></a>Kihívások a sMSAs

A sMSAs kapcsolatos kihívások a következők:

| Problémák| Kezelési lehetőségek |
| - | - |
| Egyetlen kiszolgálón is használhatók.| Használja a csoportosan felügyelt szolgáltatásfiókokat, ha a fiókot a kiszolgálók között kell használnia. |
| Nem használhatók tartományon belül.| Használja a csoportosan felügyelt szolgáltatásfiókokat-t, ha tartományokon keresztül kell használnia a fiókot. |
| Nem minden alkalmazás támogatja a sMSAs.| Ha lehetséges, használja a csoportosan felügyelt szolgáltatásfiókokat-t. Ha nem az alkalmazás létrehozója által ajánlott általános jogú felhasználói fiókot vagy számítógépfiókot használ. |


## <a name="find-smsas"></a>SMSAs keresése

Bármelyik tartományvezérlőn futtassa a DSA. msc fájlt, és bontsa ki a felügyelt szolgáltatásfiókok tárolót az összes sMSAs megtekintéséhez. 

A következő PowerShell-parancs visszaadja a Active Directory tartomány összes sMSAs és csoportosan felügyelt szolgáltatásfiókokat. 

`Get-ADServiceAccount -Filter *`

A következő parancs csak a Active Directory tartományban lévő sMSAs adja vissza.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>SMSAs kezelése

A sMSAs kezeléséhez a következő Active Directory PowerShell-parancsmagokat használhatja:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Áthelyezés a sMSAs

Ha egy Application Service támogatja a önállóan felügyelt szolgáltatásfiókot-t, de nem csoportosan felügyelt szolgáltatásfiókokat, és jelenleg felhasználói fiókot vagy számítógépfiókot használ a biztonsági környezethez, [hozzon létre és telepítsen egy önállóan felügyelt szolgáltatásfiókot](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) a kiszolgálón. 

Ideális esetben helyezze át az erőforrásokat az Azure-ba, és használja az Azure által felügyelt identitásokat vagy egyszerű szolgáltatásokat.

 

## <a name="next-steps"></a>Következő lépések
A szolgáltatásfiókok biztonságossá tételéről a következő cikkekben olvashat.

* [A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)

* [Biztonságos csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md)

* [Önálló felügyelt szolgáltatásfiókok biztonságossá tétele](service-accounts-standalone-managed.md)

* [Számítógép-fiókok biztonságossá tétele](service-accounts-computer.md)

* [Biztonságos felhasználói fiókok](service-accounts-user-on-premises.md)

* [Helyszíni szolgáltatásfiókok szabályozása](service-accounts-govern-on-premises.md)

