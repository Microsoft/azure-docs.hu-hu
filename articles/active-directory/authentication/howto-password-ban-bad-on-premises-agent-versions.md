---
title: A jelszavas védelem ügynökének kiadási előzményei – Azure Active Directory
description: A dokumentumok verziójának kiadása és a viselkedés változási előzményei
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ad7199360ca0acc8674f7a4e34bd206f8b335f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648765"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Az Azure AD jelszavas védelmi ügynök korábbi verziói

## <a name="121720"></a>1.2.172.0

Kiadás dátuma: február 22 2021

Csaknem két év telt el, mivel a helyszíni Azure AD-beli jelszavas védelem ügynökének GA-verziója megjelent. Mostantól elérhető egy új frissítés – lásd az alábbi módosítási leírást. Köszönjük mindenkinek, aki visszajelzést adott nekünk a termékről. 

* A DC-ügynök és a proxy-ügynök szoftverének most már telepítve kell lennie a .NET-4.7.2.
  * Ha a .NET 4.7.2 még nincs telepítve, töltse le és futtassa a [Windows rendszerhez készült .NET-keretrendszer 4.7.2 offline telepítője által](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)talált telepítőt.
* A AzureADPasswordProtection PowerShell-modult mostantól a DC Agent szoftver is telepíti.
* Két új, egészségüggyel kapcsolatos PowerShell-parancsmag lett hozzáadva: Test-AzureADPasswordProtectionDCAgent és test-AzureADPasswordProtectionProxy.
* A AzureADPasswordProtection DC Agent jelszavas szűrő DLL-fájlja most betöltődik és fut azon gépeken, amelyeken a lsass.exe PPL módban való futtatásra van konfigurálva.
* Hibajavítás a jelszó-algoritmushoz, amely lehetővé teszi, hogy az öt karakternél rövidebb letiltott jelszavakat a rendszer helytelenül fogadja el.
  * Ez a hiba csak akkor alkalmazható, ha a helyszíni AD-jelszóhoz tartozó minimális időtartamú házirend úgy van konfigurálva, hogy az első helyen kevesebb mint öt karakterből álló jelszót engedélyezzen.
* Egyéb kisebb hibajavítások.

Az új telepítők automatikusan frissítik a szoftver régebbi verzióit. Ha a tartományvezérlő-ügynököt és a proxy szoftvert is telepítette egyetlen gépre (csak tesztelési környezetekhez ajánlott), akkor mindkettőt egyszerre kell frissítenie.

A tartományvezérlői ügynök és a proxy szoftver régebbi és újabb verzióinak futtatása támogatott a tartományon vagy erdőben, de ajánlott eljárásként javasoljuk, hogy az összes ügynököt a legújabb verzióra frissítse. Az ügynök frissítéseinek bármilyen sorrendje támogatott – az új DC-ügynökök a régebbi proxy ügynökökön keresztül kommunikálhatnak, a régebbi TARTOMÁNYVEZÉRLŐk pedig újabb proxy ügynökökkel kommunikálhatnak.

## <a name="121250"></a>1.2.125.0

Kiadás dátuma: március 22nd 2019

* Kisebb typo hibák javítása az Eseménynapló üzeneteiben
* Végfelhasználói licencszerződés frissítése a végleges általánosan elérhető verzióra

> [!NOTE]
> A build 1.2.125.0 az általánosan elérhető Build. Köszönjük, hogy mindenki visszajelzést kapott a termékről!

## <a name="121160"></a>1.2.116.0

Kiadás dátuma: 3/13/2019

* A Get-AzureADPasswordProtectionProxy és Get-AzureADPasswordProtectionDCAgent parancsmagok mostantól a szoftver verzióját és az aktuális Azure-bérlőt jelentik a következő korlátozásokkal:
  * A szoftver verziója és az Azure-bérlői adatközpont csak a 1.2.116.0 vagy újabb verziót futtató TARTOMÁNYVEZÉRLŐk és proxyk esetében érhető el.
  * Előfordulhat, hogy az Azure-bérlői adatforgalmi jelentés nem jelent meg, amíg a proxy vagy az erdő újbóli regisztrálása (vagy megújítása) be nem következik.
* A proxy szolgáltatáshoz most már telepítve kell lennie a .NET 4,7-nek.
  * Ha a .NET 4,7 még nincs telepítve, töltse le és futtassa a [Windows rendszerhez készült .NET-keretrendszer 4,7 offline telepítőjének](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)telepítőjét.
  * A Server Core rendszereken szükség lehet a/q jelző megadására a .NET 4,7 telepítőre, hogy a sikeres legyen.
* A proxy szolgáltatás mostantól támogatja az automatikus frissítést. Az automatikus frissítés a Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatását használja, amelyet a rendszer a proxy szolgáltatással párhuzamosan telepít. Alapértelmezés szerint az automatikus frissítés be van kapcsolva.
* Az automatikus frissítés a Set-AzureADPasswordProtectionProxyConfiguration parancsmag használatával engedélyezhető vagy letiltható. Az aktuális beállítás a Get-AzureADPasswordProtectionProxyConfiguration parancsmag használatával kérdezhető le.
* A DC Agent szolgáltatáshoz tartozó bináris fájl átnevezve lett AzureADPasswordProtectionDCAgent.exera.
* A proxy szolgáltatáshoz tartozó bináris fájl át lett nevezve AzureADPasswordProtectionProxy.exera. Előfordulhat, hogy a tűzfalszabályok módosítására van szükség, ha egy külső gyártótól származó tűzfal van használatban.
  * Megjegyzés: Ha egy http-proxy konfigurációs fájlját egy korábbi proxy telepítésekor használták, akkor a frissítés után átnevezni kell ( *proxyservice.exe.configról* *AzureADPasswordProtectionProxy.exe.config*).
* Az összes időkorlátos funkció-ellenőrzés el lett távolítva a DC-ügynökből.
* A kisebb hibák javításai és naplózási funkciói.

## <a name="12650"></a>1.2.65.0

Kiadás dátuma: február 1-től 2019

Változások

* A DC-ügynök és a proxy szolgáltatás mostantól támogatott a Server Core-ban. A Mininimum operációs rendszerre vonatkozó követelmények nem változnak a következő előtt: Windows Server 2012 for DC Agents, és Windows Server 2012 R2 for proxys.
* A Register-AzureADPasswordProtectionProxy és Register-AzureADPasswordProtectionForest parancsmag mostantól támogatja az eszköz-kód alapú Azure-hitelesítési módokat.
* Az Get-AzureADPasswordProtectionDCAgent parancsmag figyelmen kívül hagyja az összekeveredett és/vagy a szolgáltatási kapcsolatok érvénytelen pontjait. Ez a változás javítja azt a hibát, amelyben a tartományvezérlők időnként többször is megjelennek a kimenetben.
* Az Get-AzureADPasswordProtectionSummaryReport parancsmag figyelmen kívül hagyja az összekeveredett és/vagy a szolgáltatási kapcsolatok érvénytelen pontjait. Ez a változás javítja azt a hibát, amelyben a tartományvezérlők időnként többször is megjelennek a kimenetben.
* A proxy PowerShell-modul már regisztrálva van a%ProgramFiles%\WindowsPowerShell\Modules. A gép PSModulePath környezeti változója már nem módosul.
* Új Get-AzureADPasswordProtectionProxy parancsmag lett hozzáadva a regisztrált proxyk egy erdőben vagy tartományban való felfedésének támogatásához.
* A tartományvezérlő ügynöke egy új mappát használ a SYSVOL megosztásban a jelszóházirend és más fájlok replikálásához.

   Régi mappa helye:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Új mappa helye:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Ez a változás a hamis pozitív "árva GPO" figyelmeztetések elkerülését eredményezte.)

   > [!NOTE]
   > A régi mappa és az új mappa között nem történik áttelepítés vagy adatmegosztás. A régebbi DC-ügynök verziói továbbra is a régi helyet fogják használni, amíg erre a verzióra vagy később nem frissítik. Ha minden tartományvezérlő ügynöke 1.2.65.0 vagy újabb verziót futtat, lehet, hogy a régi SYSVOL mappát manuálisan törli.

* A DC Agent és a proxy szolgáltatás mostantól felismeri és törli a megfelelő szolgáltatási kapcsolatainak összekeveredett másolatait.
* Minden tartományvezérlő ügynök rendszeresen törli az összekeveredett és elavult szolgáltatási kapcsolati pontokat a tartományában, a DC-ügynök és a proxykiszolgáló kapcsolati pontjai esetében is. A DC-ügynök és a proxy Service-kapcsolati pontok elavultnak tekintendők, ha a szívverési időbélyegzője hét napnál régebbi.
* A DC-ügynök most szükség szerint megújítja az erdő tanúsítványát.
* A proxy szolgáltatás most szükség szerint megújítja a proxy-tanúsítványt.
* A jelszó-ellenőrzési algoritmus frissítései: a globálisan tiltott jelszavak listáját és az ügyfél-specifikus tiltott jelszavak listáját (ha be van állítva) a rendszer a jelszó érvényessége előtt kombinálja. Egy adott jelszó elutasítása elvégezhető (sikertelen vagy csak naplózás), ha a globális és az ügyfél-specifikus listából is tartalmaz jogkivonatokat. Az Eseménynapló dokumentációja frissítve lett, hogy tükrözze ezt; Lásd: az [Azure ad jelszavas védelem figyelése](howto-password-ban-bad-on-premises-monitor.md).
* Teljesítmény-és megbízhatósági javítások
* Továbbfejlesztett naplózás

> [!WARNING]
> Időkorlátos funkciók: ebben a kiadásban a DC Agent szolgáltatás (1.2.65.0) leállítja a jelszó-ellenőrzési kérések feldolgozását szeptember 1-től 2019.  A korábbi kiadásokban a DC Agent Services (lásd az alábbi listát) az 2019. július 1-től leállítja a feldolgozást. A DC Agent szolgáltatás minden verzióban az 10021-es eseményeket a rendszergazdai eseménynaplóba fogja naplózni a két hónap során, amely a következő határidőket vezeti. A következő kiadásban az összes időkorlát el lesz távolítva. A proxy Agent szolgáltatás semmilyen verzióban nem korlátozott, de továbbra is a legújabb verzióra kell frissíteni ahhoz, hogy kihasználhassa az összes további hibajavítást és egyéb fejlesztést.

## <a name="12250"></a>1.2.25.0

Kiadás dátuma: november 1-től 2018

Hibajavítások

* A tartományvezérlő ügynökének és a proxy szolgáltatásnak a tanúsítvány-megbízhatósági hibák miatt már nem kell meghiúsulnia.
* A DC-ügynök és a proxy szolgáltatás javít az FIPS-kompatibilis gépeken.
* A proxy szolgáltatás mostantól megfelelően fog működni a TLS 1,2-alapú hálózatkezelési környezetben.
* Kisebb teljesítmény és robusztus javítások
* Továbbfejlesztett naplózás

Változások

* A proxy szolgáltatás minimálisan szükséges operációsrendszer-szintje mostantól a Windows Server 2012 R2. A DC Agent szolgáltatás minimálisan szükséges operációsrendszer-szintje a Windows Server 2012-es verzióban marad.
* A proxy szolgáltatáshoz mostantól a .NET-es verzió 4.6.2 szükséges.
* A jelszó-ellenőrzési algoritmus kibontott karakteres normalizáló táblát használ. Ez a módosítás azt eredményezheti, hogy a rendszer elutasítja a jelszavakat, amelyeket a korábbi verziókban elfogadtak.

## <a name="12100"></a>1.2.10.0

Kiadás dátuma: augusztus 17 2018

Hibajavítások

* A Register-AzureADPasswordProtectionProxy és Register-AzureADPasswordProtectionForest mostantól támogatja a többtényezős hitelesítést
* Register-AzureADPasswordProtectionProxy a titkosítási hibák elkerülése érdekében WS2012 vagy újabb tartományvezérlőt igényel a tartományban.
* A DC Agent szolgáltatás megbízhatóbb az Azure-beli új jelszóházirend indításkor történő kérésekor.
* A DC Agent szolgáltatás szükség esetén minden órában új jelszóházirend-szabályzatot kér az Azure-ban, de ezt követően véletlenszerűen kiválasztott kezdési időpontban fog megjelenni.
* A DC Agent szolgáltatás a továbbiakban nem eredményez határozatlan késleltetést az új TARTOMÁNYVEZÉRLŐi hirdetményben, ha az előléptetésük előtt egy kiszolgálóra van telepítve.
* A DC Agent Service mostantól tiszteletben tartja a "jelszavas védelem engedélyezése Windows Server Active Directory" konfigurációs beállítást.
* Mostantól a DC-ügynök és a proxy-telepítők is támogatják a helyi frissítést a későbbi verzióra való frissítéskor.

> [!WARNING]
> A 1.1.10.3 verzióról történő helyszíni frissítés nem támogatott, és telepítési hibát eredményez. A 1.2.10 vagy újabb verzióra történő frissítéshez először teljesen el kell távolítania a DC Agent és a proxy Service szoftvert, majd az új verziót újra kell telepítenie. Az Azure AD jelszavas védelem proxy szolgáltatásának ismételt regisztrálása szükséges.  Az erdő újbóli regisztrálásához nem szükséges.

> [!NOTE]
> A DC-ügynök szoftverének helyben történő frissítése újraindítást igényel.

* A DC Agent és a proxy Service mostantól támogatja a futtatást olyan kiszolgálón, amely úgy van konfigurálva, hogy csak az FIPS-kompatibilis algoritmusokat használja.
* Kisebb teljesítmény és robusztus javítások
* Továbbfejlesztett naplózás

## <a name="11103"></a>1.1.10.3

Kiadás dátuma: június 15., 2018

Kezdeti nyilvános előzetes kiadás

## <a name="next-steps"></a>Következő lépések

[Az Azure AD jelszavas védelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
