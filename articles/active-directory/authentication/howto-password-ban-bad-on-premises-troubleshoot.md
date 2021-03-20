---
title: Helyszíni Azure AD jelszavas védelem – problémamegoldás
description: Ismerje meg, hogyan lehet elhárítani az Azure AD jelszavas védelmét helyszíni Active Directory Domain Services környezetekben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bbc1c555824d4c632c5bf85a9cd0aa83087fc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648725"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Hibakeresés: helyszíni Azure AD jelszavas védelem

Az Azure AD jelszavas védelem üzembe helyezése után szükség lehet a hibaelhárításra. Ez a cikk részletesen ismerteti a gyakori hibaelhárítási lépéseket.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>A tartományvezérlő ügynöke nem talál proxyt a címtárban.

Ennek a problémának a fő tünete a DC-ügynök rendszergazdai eseménynaplójának 30017 eseménye.

A probléma leggyakoribb oka, hogy a proxy még nincs regisztrálva. Ha egy proxy regisztrálva van, előfordulhat, hogy az AD-replikáció késése miatt némi késés történt, amíg egy adott tartományvezérlő ügynöke meg nem látja a proxyt.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>A DC-ügynök nem tud kommunikálni a proxyval

Ennek a problémának a fő tünete a DC-ügynök rendszergazdai eseménynaplójának 30018 eseménye. A probléma több lehetséges oka lehet:

1. A DC-ügynök a hálózat egy elkülönített részében található, amely nem engedélyezi a hálózati kapcsolatot a regisztrált proxy (ka) t. Ez a probléma akkor lehet jóindulatú, ha más TARTOMÁNYVEZÉRLŐk nem tudnak kommunikálni a proxy (k) vel az Azure-beli jelszóházirendek letöltése érdekében. A letöltés után ezeket a házirendeket a rendszer ezután az elkülönített tartományvezérlő szerzi be a SYSVOL-megosztásban lévő házirend-fájlok replikálásával.

1. A proxykiszolgáló blokkolja az RPC Endpoint Mapper-végpont elérését (135-es port).

   Az Azure AD jelszavas védelem proxy telepítője automatikusan létrehoz egy Windows tűzfal bejövő szabályt, amely engedélyezi a hozzáférést az 135-es porthoz. Ha ezt a szabályt később törli vagy letiltja, a DC-ügynökök nem tudnak kommunikálni a proxy szolgáltatással. Ha a beépített Windows tűzfal egy másik tűzfal-termék helyett le van tiltva, akkor a tűzfalat úgy kell konfigurálnia, hogy engedélyezze a hozzáférést a 135-es porthoz.

1. A proxykiszolgáló blokkolja a proxy szolgáltatás által figyelt RPC-végponthoz (dinamikus vagy statikus) való hozzáférést.

   Az Azure AD jelszavas védelmi proxy telepítője automatikusan létrehoz egy Windows tűzfal bejövő szabályt, amely lehetővé teszi az Azure AD jelszavas védelmi proxy szolgáltatás által figyelt bejövő portok elérését. Ha ezt a szabályt később törli vagy letiltja, a DC-ügynökök nem tudnak kommunikálni a proxy szolgáltatással. Ha a beépített Windows tűzfal egy másik tűzfal-termék helyett le van tiltva, akkor a tűzfalat úgy kell beállítania, hogy engedélyezze a hozzáférést az Azure AD jelszavas védelmi proxy szolgáltatás által figyelt bejövő portokhoz. Ez a konfiguráció konkrétabb lehet, ha a proxy szolgáltatás úgy van konfigurálva, hogy egy adott statikus RPC-portot figyeljen (a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmag használatával).

1. A proxykiszolgáló nem engedélyezi, hogy a tartományvezérlők be tudják jelentkezni a gépre. Ezt a viselkedést a "számítógép elérése a hálózatról" felhasználói jogosultságok kiosztása vezérli. Ezt a jogosultságot az erdő összes tartományának összes tartományvezérlőjén meg kell adni. Ezt a beállítást gyakran egy nagyobb hálózati megerősítési tevékenység részeként korlátozzák.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>A proxy szolgáltatás nem tud kommunikálni az Azure-ban

1. Győződjön meg arról, hogy a proxykiszolgáló kapcsolódik a [központi telepítési követelményekben](howto-password-ban-bad-on-premises-deploy.md)felsorolt végpontokhoz.

1. Győződjön meg arról, hogy az erdő és az összes proxykiszolgáló regisztrálva van ugyanazon az Azure-bérlőn.

   Ezt a követelményt a  `Get-AzureADPasswordProtectionProxy` és a PowerShell- `Get-AzureADPasswordProtectionDCAgent` parancsmagok futtatásával, majd az `AzureTenant` egyes visszaadott elemek tulajdonságának összehasonlításával is megtekintheti. A megfelelő működés érdekében a jelentett bérlő nevének meg kell egyeznie az összes tartományvezérlő-ügynök és proxykiszolgáló között.

   Ha egy Azure-bérlő regisztrációjának eltérési feltétele létezik, ezt a problémát a `Register-AzureADPasswordProtectionProxy` és/vagy a PowerShell-parancsmagok igény szerint történő futtatásával lehet megállapítani `Register-AzureADPasswordProtectionForest` , hogy az összes regisztrációhoz ugyanazt az Azure-bérlő hitelesítő adatait használja.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>A tartományvezérlő ügynöke nem tudja titkosítani vagy visszafejteni a jelszóházirend fájljait

Az Azure AD jelszavas védelme kritikus függőséggel rendelkezik a Microsoft Key Distribution szolgáltatás által biztosított titkosítási és visszafejtési funkcióktól. A titkosítási és a visszafejtési hibák számos tünettel rendelkezhetnek, és számos lehetséges oka lehet.

1. Győződjön meg arról, hogy a KDS szolgáltatás engedélyezve van és működőképes a tartomány minden Windows Server 2012-es és újabb tartományvezérlőjén.

   Alapértelmezés szerint a KDS szolgáltatás szolgáltatás indítási módja manuálisan (trigger Start) van konfigurálva. Ez a konfiguráció azt jelenti, hogy az ügyfél első alkalommal próbálja meg használni a szolgáltatást, igény szerint elindítva. Ez az alapértelmezett szolgáltatás indítási módja elfogadható az Azure AD jelszavas védelem működéséhez.

   Ha a KDS szolgáltatás indítási módja le van tiltva, akkor ezt a konfigurációt meg kell oldani az Azure AD jelszavas védelem megfelelő működéséhez.

   A probléma egyszerű tesztelése a KDS szolgáltatás manuális elindítása a Service Management MMC konzolon vagy más felügyeleti eszközök használatával (például "net start kdssvc" futtatása egy parancssori konzolról). A KDS szolgáltatás várhatóan sikeresen elindul és fut.

   A KDS szolgáltatás nem indítható el a leggyakoribb alapvető oka az, hogy az Active Directory tartományvezérlő objektum az alapértelmezett tartományvezérlők szervezeti egységén kívül található. Ezt a konfigurációt a KDS szolgáltatás nem támogatja, és az Azure AD jelszavas védelme nem kényszeríti. Ennek a feltételnek a kijavítása a tartományvezérlő objektum áthelyezése egy helyre az alapértelmezett tartományvezérlők szervezeti egységben.

1. Inkompatibilis KDS – a titkosított puffer formátuma a Windows Server 2012 R2-ről Windows Server 2016-re módosult

   A Windows Server 2016 olyan KDS-biztonsági javítást vezetett be, amely módosítja a KDS titkosított pufferek formátumát; Ezek a pufferek néha nem lesznek visszafejtve a Windows Server 2012 és a Windows Server 2012 R2 rendszeren. A fordított irány rendben van – a Windows Server 2012-es és a Windows Server 2012 R2-KDS titkosított pufferek a Windows Server 2016-es és újabb verzióiban mindig sikeresen visszafejtik. Ha a Active Directory tartományban lévő tartományvezérlők ezen operációs rendszerek kombinációját futtatják, akkor előfordulhat, hogy az Azure AD jelszavas védelemének esetleges visszafejtési hibáit is jelenteni kell. A biztonsági javítás jellegéből adódóan nem lehet pontosan megjósolni a hibák időzítését vagy tüneteit, és mivel nem determinisztikus, hogy melyik Azure AD jelszavas védelmet biztosító tartományvezérlő ügynöke fogja titkosítani az adatait egy adott időpontban.

   Ezt a problémát nem lehet megkerülő megoldás, mert nem a Active Directory tartomány (ok) ban lévő nem kompatibilis operációs rendszerek kombinációját futtatja. Ez azt jelenti, hogy csak Windows Server 2012 és Windows Server 2012 R2 rendszerű tartományvezérlőket kell futtatnia, vagy csak Windows Server 2016 vagy újabb rendszerű tartományvezérlőket kell futtatnia.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>A DC Agent azt gondolja, hogy az erdő nincs regisztrálva

Ennek a hibának a tünete a 30016-as számú, a DC Agent\Admin csatornán bejelentkezett esemény, amely az alábbi részben látható:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Ennek a hibának két lehetséges oka van.

1. Az erdő valóban nincs regisztrálva. A probléma megoldásához futtassa a Register-AzureADPasswordProtectionForest parancsot a [telepítési követelmények](howto-password-ban-bad-on-premises-deploy.md)című témakörben leírtak szerint.
1. Az erdő regisztrálva van, de a tartományvezérlő ügynöke nem tudja visszafejteni az erdő regisztrációs információit. Ebben az esetben ugyanaz az alapvető ok, mint a fentiekben felsorolt #2 a [DC Agent nem tudja titkosítani vagy visszafejteni a jelszóházirend-fájlokat](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files). Az elmélet megerősítésének egyszerű módja, hogy ezt a hibaüzenetet csak a Windows Server 2012 vagy a Windows Server 2012R2 rendszerű tartományvezérlők esetében fogja látni, míg a Windows Server 2016-es és újabb rendszerű tartományvezérlők által futtatott tartományvezérlői ügynökök rendben vannak. A megkerülő megoldás ugyanaz: frissítse az összes tartományvezérlőt a Windows Server 2016-es vagy újabb verziójára.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>A rendszer nem fogadja el a gyenge jelszavakat, de nem feltétlenül kell

A probléma több oka is lehet.

1. A tartományvezérlő ügynöke a nyilvános előzetes verziót futtatja, amely lejárt. [A nyilvános előzetes VERZIÓJÚ DC-ügynök szoftvere lejárt](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. A tartományvezérlő ügynöke nem tud letölteni egy házirendet, vagy nem tudja visszafejteni a meglévő szabályzatokat. A fenti témakörökben a lehetséges okokat érdemes megkeresni.

1. A jelszóházirend kényszerített módja továbbra is naplózásra van beállítva. Ha ez a konfiguráció van érvényben, konfigurálja újra úgy, hogy az az Azure AD jelszavas védelmi portál használatával érvénybe lépjen. További információ: [működési módok](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. A jelszó-házirend le van tiltva. Ha ez a konfiguráció van érvényben, konfigurálja újra a beállítást az Azure AD jelszavas védelem portál használatával. További információ: [működési módok](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Nem telepítette a tartományvezérlő-ügynök szoftverét a tartomány összes tartományvezérlőjén. Ebben az esetben nehéz biztosítani, hogy a távoli Windows-ügyfelek egy adott tartományvezérlőt célozzanak meg a jelszó-módosítási művelet során. Ha úgy gondolja, hogy sikeresen megcélozta azt a TARTOMÁNYVEZÉRLŐt, ahol a DC-ügynök szoftvere telepítve van, akkor a TARTOMÁNYVEZÉRLŐi ügynök rendszergazdai eseménynaplójának dupla ellenőrzésével ellenőrizheti, hogy az eredménytől függetlenül megtörtént-e legalább egy esemény a jelszó-ellenőrzés eredményének dokumentálására. Ha nincs olyan esemény a felhasználó számára, akinek a jelszava megváltozik, a jelszó módosítását valószínűleg egy másik tartományvezérlő dolgozza fel.

   Alternatív tesztként próbálja meg setting\changing a jelszavakat, miközben közvetlenül egy olyan TARTOMÁNYVEZÉRLŐre jelentkezett be, amelyen telepítve van a DC Agent szoftver. Ez a technika éles Active Directory-tartományokhoz nem ajánlott.

   A DC-ügynök szoftverének növekményes üzembe helyezése a korlátozások hatálya alá esik, a Microsoft határozottan azt javasolja, hogy a tartományvezérlő ügynök szoftverét a lehető leghamarabb telepítse a tartomány összes tartományvezérlőjén.

1. Előfordulhat, hogy a jelszó-ellenőrzési algoritmus ténylegesen a várt módon működik. Tekintse meg [, hogyan történik a jelszavak kiértékelése](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>A Ntdsutil.exe nem tudja beállítani a gyenge címtárszolgáltatások helyreállító jelszavát

A Active Directory mindig érvényesíti az új címtárszolgáltatások helyreállító módjának jelszavát, hogy az megfeleljen a tartomány jelszó-bonyolultsági követelményeinek. Ez az ellenőrzés a jelszó-szűrési DLL-eket, például az Azure AD jelszavas védelmet is kéri. Ha a rendszer visszautasítja az új címtárszolgáltatások helyreállító módjának jelszavát, a következő hibaüzenet jelenik meg:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Ha az Azure AD jelszavas védelem egy Active Directory címtárszolgáltatások helyreállító módjához tartozó jelszó-ellenőrzési esemény (eke) t naplóz, akkor az Eseménynapló üzenetei nem tartalmazzák a felhasználónevet. Ez a viselkedés azért fordul elő, mert a Címtárszolgáltatások helyreállító fiókja olyan helyi fiók, amely nem része a tényleges Active Directory tartománynak.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>A tartományvezérlő replikájának előléptetése sikertelen, mert gyenge címtárszolgáltatások helyreállító jelszava

A tartományvezérlő-előléptetési folyamat során az új címtárszolgáltatások helyreállító módjának jelszava a tartomány egy meglévő TARTOMÁNYVEZÉRLŐje számára lesz elküldve érvényesítésre. Ha a rendszer visszautasítja az új címtárszolgáltatások helyreállító módjának jelszavát, a következő hibaüzenet jelenik meg:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

A fenti probléma megoldásához hasonlóan az Azure AD jelszavas védelemhez tartozó jelszó-ellenőrzés eredményének eseménye üres felhasználóneveket fog tartalmazni ehhez a forgatókönyvhöz.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>A tartományvezérlő lefokozása nem sikerült, mert gyenge a helyi rendszergazda jelszava

Ez a tartományvezérlő lefokozását támogatja, amely még mindig futtatja a tartományvezérlő-ügynök szoftverét. A rendszergazdáknak tisztában kell lenniük azzal, hogy a tartományvezérlő ügynöke továbbra is érvényesíti a jelenlegi jelszóházirend-házirendet a lefokozási eljárás során. A rendszer az új helyi rendszergazdai fiók jelszavát (a lefokozási művelet részeként adja meg) érvényesíti, mint bármely más jelszó. A Microsoft azt javasolja, hogy a tartományvezérlő lefokozási eljárásának részeként a helyi rendszergazdai fiókok számára a biztonságos jelszavakat kell kiválasztani.

Ha a lefokozás sikeres volt, és a tartományvezérlőt újraindították, és a rendszer újraindul, és ismét normál tagkiszolgálóként fut, a tartományvezérlői ügynök szoftvere visszavált a passzív módban történő futtatásra. Ezt követően bármikor el lehet távolítani.

## <a name="booting-into-directory-services-repair-mode"></a>A Címtárszolgáltatások helyreállító módjának indítása

Ha a tartományvezérlő címtárszolgáltatás-javító módban van elindítva, a tartományvezérlői ügynök jelszavas szűrője dll észleli ezt a feltételt, és az összes jelszó-ellenőrzési vagy kényszerítési tevékenységet le fogja tiltani, az aktuálisan aktív házirend-konfigurációtól függetlenül. A DC Agent jelszavas szűrő DLL-je egy 10023-es figyelmeztetési eseményt naplóz a felügyeleti eseménynaplóba, például:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>A nyilvános előzetes verziójú DC-ügynök szoftvere lejárt

Az Azure AD jelszavas védelem nyilvános előzetes verziójában a DC-ügynök szoftverét nem lehetett a következő dátumokon leállítani a jelszó-ellenőrzési kérelmek feldolgozásának leállítására:

* A 1.2.65.0 verziója leállítja a jelszó-ellenőrzési kérelmek feldolgozását szeptember 1 2019-én.
* A 1.2.25.0 verzió és a korábban leállított jelszó-ellenőrzési kérések feldolgozása július 1 2019-én.

A határidő megközelítése szerint az összes időkorlátos DC-ügynök verziója 10021 eseményt bocsát ki a DC-ügynök rendszergazdai eseménynaplójában a következőhöz hasonló rendszerindítási időpontban:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

A határidő lejárta után az összes időkorlátos DC-ügynök verziója 10022 eseményt bocsát ki a DC-ügynök rendszergazdai eseménynaplójában a következőhöz hasonló rendszerindítási időpontban:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Mivel a határidőt csak a kezdeti rendszerindítás során ellenőrzi a rendszer, előfordulhat, hogy a naptári határidő lejárta után nem látja ezeket az eseményeket. A határidő felismerése után a rendszer nem gyakorol negatív hatást a tartományvezérlőre vagy a nagyobb környezetre, mint az összes jelszó, automatikusan jóvá lesz hagyva.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a lejárt nyilvános előzetes tartományvezérlő ügynökök azonnal frissítve legyenek a legújabb verzióra.

A parancsmag futtatásával egyszerűen felderítheti az olyan TARTOMÁNYVEZÉRLŐi ügynököket, amelyeket frissíteni kell `Get-AzureADPasswordProtectionDCAgent` , például a következő módon:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Ebben a témakörben a SoftwareVersion mező nyilvánvalóan a legfontosabb tulajdonság, amellyel megtekintheti. A PowerShell-szűrés használatával kiszűrheti az olyan egyenáramú ügynököket, amelyek már megtalálhatók a szükséges alapkonfiguráció-verziónál, például:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Az Azure AD jelszavas védelmi proxy szoftver semmilyen verzióban nem korlátozott. A Microsoft továbbra is javasolja a DC és a proxy ügynökök frissítését a legújabb verzióra, amint azok megjelentek. A `Get-AzureADPasswordProtectionProxy` parancsmagot a DC-ügynököknél a fenti példában szereplő, frissítést igénylő proxy ügynökök keresésére lehet használni.

Az adott frissítési eljárásokkal kapcsolatos további részletekért tekintse meg a [DC Agent frissítését](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) és [a proxy szolgáltatás frissítését](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) ismertető témakört.

## <a name="emergency-remediation"></a>Vészhelyzeti szervizelés

Ha olyan helyzet történik, ahol a tartományvezérlő ügynöke problémát okoz, előfordulhat, hogy a tartományvezérlő ügynök szolgáltatása azonnal leáll. A DC Agent jelszó-szűrő DLL-fájlja továbbra is megkísérli hívni a nem futó szolgáltatást, és naplózza a figyelmeztetési eseményeket (10012, 10013), de az összes bejövő jelszót elfogadja a rendszer. A DC Agent szolgáltatás ezután a Windows szolgáltatásvezérlő kezelőjén keresztül is konfigurálható a "Letiltva" indítási típussal, igény szerint.

Egy másik szervizelési mérték az engedélyezés mód beállítása a nem értékre az Azure AD jelszavas védelem portálon. Miután letöltötte a frissített szabályzatot, minden egyes tartományvezérlő ügynök-szolgáltatás nyugalmi módba kerül, ahol az összes jelszó elfogadva van. További információ: [működési módok](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Eltávolítása

Ha úgy döntött, hogy eltávolítja az Azure AD jelszavas védelem szoftverét, és az összes kapcsolódó állapotot a tartomány (ok) és az erdő alapján végzi el, ez a feladat a következő lépések végrehajtásával valósítható meg:

> [!IMPORTANT]
> Fontos, hogy ezeket a lépéseket sorrendben hajtsa végre. Ha a proxy szolgáltatás bármelyik példánya fut, akkor a rendszer rendszeres időközönként újra létrehozza a serviceConnectionPoint objektumot. Ha a DC Agent szolgáltatás bármelyik példánya fut, a rendszer rendszeres időközönként újra létrehozza a serviceConnectionPoint-objektumot és a SYSVOL-állapotot.

1. Távolítsa el a proxy szoftvert az összes gépről. Ez a lépés **nem** igényel újraindítást.
2. Távolítsa el a DC Agent szoftverét az összes tartományvezérlőről. Ehhez a lépéshez újraindítás **szükséges** .
3. Manuálisan távolítsa el az összes proxy Service-csatlakozási pontot az egyes tartománynév-névhasználati környezetekben. Az objektumok helye a következő Active Directory PowerShell-paranccsal deríthető fel:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Ne hagyja ki a csillagot ("*") a $keywords változó értékének végén.

   Az eredményül kapott objektum (ok) megtalálhatók a `Get-ADObject` parancs segítségével `Remove-ADObject` , vagy manuálisan is törölhetők.

4. Manuálisan távolítsa el az összes tartományvezérlői ügynök csatlakoztatási pontját az egyes tartománynév-névhasználati környezetekben. A szoftver központi telepítésének módjától függően előfordulhat, hogy az erdőn belül egy adott objektum egy tartományvezérlőn található. Az objektum helye a következő Active Directory PowerShell-paranccsal deríthető fel:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Az eredményül kapott objektum (ok) megtalálhatók a `Get-ADObject` parancs segítségével `Remove-ADObject` , vagy manuálisan is törölhetők.

   Ne hagyja ki a csillagot ("*") a $keywords változó értékének végén.

5. Távolítsa el manuálisan az erdő szintű konfigurációs állapotot. Az erdő konfigurációs állapotát a rendszer a Active Directory konfigurációjának névhasználati környezetében lévő tárolóban tartja karban. A következő módon deríthető fel és törölhető:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Manuálisan távolítsa el az összes SYSVOL-hez kapcsolódó állapotot úgy, hogy manuálisan törli a következő mappát és annak tartalmát:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Ha szükséges, az elérési út helyileg is elérhető egy adott tartományvezérlőn. az alapértelmezett hely a következő elérési úthoz hasonló:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ez az elérési út eltérő, ha a SYSVOL-megosztás nem alapértelmezett helyen van konfigurálva.

## <a name="health-testing-with-powershell-cmdlets"></a>Állapot-tesztelés PowerShell-parancsmagokkal

A AzureADPasswordProtection PowerShell-modul két állapottal kapcsolatos parancsmagot tartalmaz, amelyek alapszintű ellenőrzést végeznek a szoftver telepítésének és működésének ellenőrzéséhez. Érdemes futtatni ezeket a parancsmagokat az új központi telepítés beállítása után, később, és a probléma kivizsgálásakor.

Az egyes állapot-tesztek egy alapszintű átadott vagy sikertelen eredményt adnak vissza, és nem kötelező üzenetet. Azokban az esetekben, amikor a hiba oka nem egyértelmű, keresse meg a hiba eseménynaplójának üzeneteit, amelyek magyarázatot kaphatnak a hibára. A szöveges üzenetek engedélyezése is hasznos lehet. További részletekért tekintse meg az [Azure ad jelszavas védelem figyelése](howto-password-ban-bad-on-premises-monitor.md)című témakört.

## <a name="proxy-health-testing"></a>Proxy állapotának tesztelése

A Test-AzureADPasswordProtectionProxyHealth parancsmag két, egyenként futtatható állapot-tesztet támogat. A harmadik mód lehetővé teszi az összes olyan teszt futtatását, amelyek nem igénylik a paraméterek bevitelét.

### <a name="proxy-registration-verification"></a>Proxy regisztrációjának ellenőrzése

Ez a teszt ellenőrzi, hogy a proxy ügynök megfelelően van-e regisztrálva az Azure-ban, és képes-e hitelesíteni az Azure-ban. A sikeres Futtatás a következőképpen fog kinézni:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Passed
```

Ha a rendszer hibát észlel, a teszt sikertelen eredményt ad vissza, és egy nem kötelező hibaüzenetet kap. Példa az egyik lehetséges hibára:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Failed No proxy certificates were found - please run the Register-AzureADPasswordProtectionProxy cmdlet to register the proxy.
```

### <a name="proxy-verification-of-end-to-end-azure-connectivity"></a>Végpontok közötti Azure-kapcsolat proxy-ellenőrzése

Ez a teszt a-VerifyProxyRegistration teszt egy kibővített változata. Ehhez az szükséges, hogy a proxy ügynök megfelelően legyen regisztrálva az Azure-ban, képes legyen hitelesíteni az Azure-ban, végül pedig ellenőrizze, hogy az üzenet sikeresen elküldhető-e az Azure-ba, így ellenőrizheti a teljes végpontok közötti kommunikáció működését.

A sikeres Futtatás a következőképpen fog kinézni:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyAzureConnectivity

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyAzureConnectivity Passed
```

### <a name="proxy-verification-of-all-tests"></a>Az összes teszt proxy-ellenőrzése

Ez a mód lehetővé teszi a parancsmag által támogatott összes teszt tömeges futtatását, amelyek nem igénylik a paraméterek bevitelét. A sikeres Futtatás a következőképpen fog kinézni:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -TestAll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyTLSConfiguration  Passed
VerifyProxyRegistration Passed
VerifyAzureConnectivity Passed
```

## <a name="dc-agent-health-testing"></a>DC-ügynök állapotának tesztelése

A Test-AzureADPasswordProtectionDCAgentHealth parancsmag több, egyenként futtatható állapot-ellenőrzést is támogat. A harmadik mód lehetővé teszi az összes olyan teszt futtatását, amelyek nem igénylik a paraméterek bevitelét.

### <a name="basic-dc-agent-health-tests"></a>Alapszintű tartományvezérlő ügynökének állapot-tesztek

Az alábbi tesztek mindegyikét egyenként is futtathatja, és nem fogadja el. Rövid leírás

|DC-ügynök állapotának tesztelése|Description|
| --- | :---: |
|-VerifyPasswordFilterDll|Ellenőrzi, hogy a jelszó-szűrő DLL-fájlja jelenleg be van-e töltve, és képes-e hívni a DC Agent szolgáltatást.|
|-VerifyForestRegistration|Ellenőrzi, hogy az erdő jelenleg regisztrálva van-e|
|-VerifyEncryptionDecryption|Ellenőrzi, hogy az alapszintű titkosítás és a visszafejtés működik-e a Microsoft KDS szolgáltatás használatával|
|-VerifyDomainIsUsingDFSR|Ellenőrzi, hogy az aktuális tartomány DFSR használ-e a SYSVOL-replikációhoz|
|-VerifyAzureConnectivity|A végpontok közötti kommunikáció ellenőrzése az Azure-ban bármely elérhető proxy használatával működik|

Íme egy példa a-VerifyPasswordFilterDll teszt átadására; a többi teszt a sikerhez hasonlóan fog kinézni:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyPasswordFilterDll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyPasswordFilterDll Passed
```

### <a name="dc-agent-verification-of-all-tests"></a>Az összes teszt tartományvezérlő-ügynök általi ellenőrzése

Ez a mód lehetővé teszi a parancsmag által támogatott összes teszt tömeges futtatását, amelyek nem igénylik a paraméterek bevitelét. A sikeres Futtatás a következőképpen fog kinézni:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -TestAll

DiagnosticName             Result AdditionalInfo
--------------             ------ --------------
VerifyPasswordFilterDll    Passed
VerifyForestRegistration   Passed
VerifyEncryptionDecryption Passed
VerifyDomainIsUsingDFSR    Passed
VerifyAzureConnectivity    Passed
```

### <a name="connectivity-testing-using-specific-proxy-servers"></a>Kapcsolat tesztelése adott proxykiszolgálók használatával

Számos hibaelhárítási helyzet magában foglalja a DC-ügynökök és-proxyk közötti hálózati kapcsolat kivizsgálását. Két egészségügyi teszt áll rendelkezésre, amelyek kifejezetten az ilyen jellegű kérdésekre koncentrálnak. Ezek a tesztek megkövetelik, hogy egy adott proxykiszolgáló legyen megadva.

#### <a name="verifying-connectivity-between-a-dc-agent-and-a-specific-proxy"></a>A DC-ügynök és egy adott proxy közötti kapcsolat ellenőrzése

Ez a teszt ellenőrzi a kapcsolatot a DC-ügynöktől a proxy felé irányuló első kommunikációs szakaszon. Ellenőrzi, hogy a proxy megkapja-e a hívást, azonban az Azure-val való kommunikációt nem vesz részt. A sikeres Futtatás a következőképpen néz ki:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Passed
```

Az alábbi példa egy olyan meghibásodási állapotot jelez, amelyben a célkiszolgálón futó proxy szolgáltatás le lett állítva:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Failed The RPC endpoint mapper on the specified proxy returned no results; please check that the proxy service is running on that server.
```

#### <a name="verifying-connectivity-between-a-dc-agent-and-azure-using-a-specific-proxy"></a>A DC-ügynök és az Azure közötti kapcsolat ellenőrzése (adott proxy használatával)

Ez a teszt a DC-ügynök és az Azure közötti teljes végpontok közötti kapcsolatot ellenőrzi egy adott proxy használatával. A sikeres Futtatás a következőképpen néz ki:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyAzureConnectivityViaSpecificProxy bpl2.bpl.com

DiagnosticName                          Result AdditionalInfo
--------------                          ------ --------------
VerifyAzureConnectivityViaSpecificProxy Passed
```

## <a name="next-steps"></a>Következő lépések

[Gyakori kérdések az Azure AD jelszavas védelméről](howto-password-ban-bad-on-premises-faq.md)

A globális és az egyéni tiltott jelszavakkal kapcsolatos további információkért tekintse meg a [helytelen jelszavakat](concept-password-ban-bad.md) tartalmazó cikket.
