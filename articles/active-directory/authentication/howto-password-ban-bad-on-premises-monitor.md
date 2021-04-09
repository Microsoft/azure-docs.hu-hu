---
title: Helyszíni Azure AD jelszavas védelem figyelése
description: Ismerje meg, hogyan figyelheti és tekintheti át az Azure AD jelszavas védelem naplóit egy helyszíni Active Directory Domain Services-környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: edc246a414401c4c1c0248787eda0381fcd63037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741762"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>A helyszíni Azure AD-beli jelszavas védelmi környezetek naplófájljainak figyelése és áttekintése

Az Azure AD jelszavas védelem üzembe helyezése után a figyelés és a jelentéskészítés elengedhetetlen feladat. Ez a cikk részletesen ismerteti a különböző figyelési technikákat, beleértve az egyes szolgáltatások adatainak naplózását és az Azure AD jelszavas védelem használatára vonatkozó jelentések módját.

A figyelést és a jelentéskészítést az Eseménynapló üzenetei vagy a PowerShell-parancsmagok futtatásával végezheti el. A DC-ügynök és a proxy szolgáltatás naplózza az Eseménynapló-üzeneteket. Az alább ismertetett PowerShell-parancsmagok csak a proxykiszolgálón érhetők el (lásd a AzureADPasswordProtection PowerShell-modult). A DC-ügynök szoftvere nem telepít PowerShell-modult.

## <a name="dc-agent-event-logging"></a>TARTOMÁNYVEZÉRLŐ ügynök eseménynaplózása

Minden egyes tartományvezérlőn a DC Agent Service szoftver az egyes jelszó-ellenőrzési műveletek (és más állapotok) eredményeit egy helyi eseménynaplóba írja:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

A tartományvezérlő ügynökének felügyeleti naplója az elsődleges információforrás a szoftver működésének módjával kapcsolatban.

Vegye figyelembe, hogy a nyomkövetési napló alapértelmezés szerint ki van kapcsolva.

A különböző DC-ügynök összetevői által naplózott események a következő tartományokon belül vannak:

|Összetevő |Eseményazonosító tartománya|
| --- | --- |
|TARTOMÁNYVEZÉRLŐi ügynök jelszavas szűrője dll| 10000-19999|
|TARTOMÁNYVEZÉRLŐ ügynök szolgáltatás üzemeltetési folyamata| 20000-29999|
|A tartományvezérlő ügynök szolgáltatási házirendjének érvényesítési logikája| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>TARTOMÁNYVEZÉRLŐi ügynök rendszergazdai eseménynaplója

### <a name="password-validation-outcome-events"></a>Jelszó-ellenőrzés eredményének eseményei

Minden egyes tartományvezérlőn a DC Agent szolgáltatás szoftvere az egyes jelszavak ellenőrzésének eredményét írja a DC-ügynök rendszergazdai eseménynaplóba.

Sikeres jelszó-ellenőrzési művelet esetén a rendszer általában egy eseményt naplóz a TARTOMÁNYVEZÉRLŐi ügynök jelszavas szűrő DLL-fájljában. Sikertelen jelszó-ellenőrzési művelet esetén a rendszer általában két eseményt naplóz, egyet a DC Agent szolgáltatásból, egyet pedig a tartományvezérlő ügynök jelszavas szűrő DLL-fájljában.

Az ilyen helyzetek rögzítésére szolgáló diszkrét események naplózása a következő tényezők alapján történik:

* Azt jelzi, hogy be van-e állítva vagy módosult-e egy adott jelszó.
* Megadja, hogy egy adott jelszó érvényesítése sikeres volt-e vagy sem.
* Azt határozza meg, hogy a Microsoft globális házirendje, a szervezeti házirend vagy egy kombináció miatt sikertelen volt-e az érvényesítés.
* Azt jelzi, hogy a naplózási mód jelenleg be van-e kapcsolva az aktuális jelszóházirend esetében.

A kulcs jelszava – az érvényesítéssel kapcsolatos események a következők:

| Esemény |Jelszó módosítása |Jelszó beállítva|
| --- | :---: | :---: |
|Sikeres |10014 |10015|
|Sikertelen (az ügyfél jelszavas házirendje miatt)| 10016, 30002| 10017, 30003|
|Sikertelen (a Microsoft jelszavas házirend miatt)| 10016, 30004| 10017, 30005|
|Sikertelen (a Microsoft és az ügyfél jelszavas házirendjének együttese miatt)| 10016, 30026| 10017, 30027|
|Sikertelen (Felhasználónév miatt)| 10016, 30021| 10017, 30022|
|Csak naplózási pass (sikertelen ügyfél-jelszavas házirend)| 10024, 30008| 10025, 30007|
|Csak naplózási pass (sikertelen volt a Microsoft jelszavas házirendje)| 10024, 30010| 10025, 30009|
|Csak naplózási pass (a Microsoft és az ügyfél jelszavas házirendjeit nem sikerült egyesíteni)| 10024, 30028| 10025, 30029|
|Csak naplózási pass (a Felhasználónév miatt sikertelen volt)| 10016, 30024| 10017, 30023|

A fenti táblázatban a "kombinált házirendek" kifejezésre hivatkozó esetek olyan helyzetekben fordulnak elő, amikor egy felhasználó jelszava legalább egy jogkivonatot tartalmaz a Microsoft által tiltott jelszavak listájáról és az ügyfél által tiltott jelszavak listájáról.

A fenti táblázatban a "Felhasználónév" kifejezésre hivatkozó esetek olyan helyzetekre utalnak, amikor a felhasználó jelszava megtalálható a felhasználó fiókjának nevével és/vagy a felhasználó egy felhasználóbarát nevével. Bármelyik forgatókönyv hatására a rendszer elutasítja a felhasználó jelszavát, ha a házirend kényszerített értékre van állítva, vagy ha a házirend naplózási módban van.

Ha a rendszer egyszerre több eseményt naplóz, mindkét eseményt explicit módon társítja ugyanahhoz a CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Jelszó-ellenőrzés összegző jelentése a PowerShell használatával

A `Get-AzureADPasswordProtectionSummaryReport` parancsmag használható a jelszó-ellenőrzési tevékenységek összegző nézetének előállítására. A parancsmag kimenete például a következő:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

A parancsmag jelentéskészítési hatóköre befolyásolhatja az – erdő, a tartomány vagy a – tartományvezérlő paraméterek egyikét. A paraméter nem adható meg – erdő.

A `Get-AzureADPasswordProtectionSummaryReport` parancsmag a tartományvezérlői ügynök rendszergazdai eseménynaplójának lekérdezésével működik, majd megszámolja az összes megjelenített eredmény kategóriának megfelelő események teljes számát. A következő táblázat az egyes eredmények és a hozzá tartozó eseményazonosító közötti leképezéseket tartalmazza:

|Get-AzureADPasswordProtectionSummaryReport tulajdonság |Megfelelő eseményazonosító|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Vegye figyelembe, hogy a `Get-AzureADPasswordProtectionSummaryReport` parancsmag PowerShell-parancsfájl formájában van elküldve, és szükség esetén közvetlenül a következő helyen lehet hivatkozni:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Ez a parancsmag úgy működik, hogy egy PowerShell-munkamenetet nyit meg az egyes tartományvezérlők számára. A sikeres végrehajtás érdekében engedélyezni kell a PowerShell távoli munkamenet-támogatást minden egyes tartományvezérlőn, és az ügyfélnek megfelelő jogosultságokkal kell rendelkeznie. A PowerShell távoli munkamenetekre vonatkozó követelményeivel kapcsolatos további információkért futtassa a "Get-Help about_Remote_Troubleshooting" parancsot egy PowerShell-ablakban.

> [!NOTE]
> Ez a parancsmag a TARTOMÁNYVEZÉRLŐi ügynök szolgáltatásának rendszergazdai eseménynaplójának távoli lekérdezésével működik. Ha az eseménynaplók nagy számú eseményt tartalmaznak, a parancsmag hosszú időt is igénybe vehet. Emellett a nagyméretű adatkészletek tömeges hálózati lekérdezései befolyásolhatják a tartományvezérlő teljesítményét. Ezért ezt a parancsmagot körültekintően kell használni éles környezetekben.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Példa Eseménynapló-üzenetre az 10014-as AZONOSÍTÓJÚ eseményhez (sikeres jelszó-módosítás)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Példa Eseménynapló-üzenetre a 10017-es és 30003-es azonosítójú eseményhez (sikertelen jelszó beállítása)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Példa Eseménynapló-üzenetre az 30001-as AZONOSÍTÓJÚ eseményhez (jelszó elfogadva, mert nincs elérhető házirend)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Példa Eseménynapló-üzenetre a 30006-as AZONOSÍTÓJÚ eseményhez (az új szabályzat érvénybe lép)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Példa a 30019-as AZONOSÍTÓJÚ esemény Eseménynapló-üzenetére (az Azure AD jelszavas védelme le van tiltva)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>TARTOMÁNYVEZÉRLŐi ügynök működési naplója

A DC Agent szolgáltatás az operatív eseményeket is naplózza a következő naplóba:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>TARTOMÁNYVEZÉRLŐ ügynök nyomkövetési naplója

A DC Agent szolgáltatás a következő naplóba is naplózhatja a részletes hibakeresési szintű nyomkövetési eseményeket:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

A nyomkövetési naplózás alapértelmezés szerint le van tiltva.

> [!WARNING]
> Ha engedélyezve van, a nyomkövetési napló nagy mennyiségű eseményt kap, és hatással lehet a tartományvezérlő teljesítményére. Ezért ezt a kibővített naplót csak akkor szabad engedélyezni, ha egy probléma mélyebb vizsgálatot igényel, majd csak minimális ideig.

## <a name="dc-agent-text-logging"></a>DC-ügynök szöveges naplózása

A DC Agent szolgáltatás beállítható úgy, hogy szöveges naplóba írja a következő beállításazonosító beállításával:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

A szöveges naplózás alapértelmezés szerint le van tiltva. Az érték érvénybe léptetéséhez újra kell indítani a DC Agent szolgáltatást. Ha engedélyezve van, a DC Agent szolgáltatás a következő helyen fogja írni a naplófájlba:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> A szöveges napló ugyanazokat a hibakeresési szintű bejegyzéseket kapja meg, amelyek a nyomkövetési naplóba naplózhatók, de általában könnyebben áttekinthetők és elemezhetők.

> [!WARNING]
> Ha engedélyezve van, ez a napló nagy mennyiségű eseményt fogad, és hatással lehet a tartományvezérlő teljesítményére. Ezért ezt a kibővített naplót csak akkor szabad engedélyezni, ha egy probléma mélyebb vizsgálatot igényel, majd csak minimális ideig.

## <a name="dc-agent-performance-monitoring"></a>DC-ügynök teljesítményének figyelése

A DC Agent szolgáltatás szoftvere egy **Azure ad jelszavas védelem** nevű teljesítményszámláló objektumot telepít. Jelenleg a következő teljesítményszámláló-számlálók érhetők el:

|Teljesítményszámláló neve | Leírás|
| --- | --- |
|Feldolgozott jelszavak |Ez a számláló a legutóbbi újraindítás óta feldolgozott (elfogadott vagy visszautasított) jelszavak teljes számát jeleníti meg.|
|Elfogadott jelszavak |Ez a számláló a legutóbbi újraindítás óta elfogadott jelszavak teljes számát jeleníti meg.|
|Elutasított jelszavak |Ez a számláló a legutóbbi újraindítás óta visszautasított jelszavak teljes számát jeleníti meg.|
|A jelszó-szűrési kérelmek folyamatban vannak |Ez a számláló a jelenleg folyamatban lévő jelszó-szűrési kérelmek számát jeleníti meg.|
|Jelszó-szűrési kérelmek maximális száma |Ez a számláló a legutóbbi újraindítás óta az egyidejű jelszavas szűrési kérelmek maximális számát jeleníti meg.|
|A jelszó-szűrési kérelmekkel kapcsolatos hibák |Ez a számláló a legutóbbi újraindítás óta fellépett hiba miatt sikertelen jelszó-szűrési kérelmek teljes számát jeleníti meg. Hibák akkor fordulhatnak elő, ha az Azure AD jelszavas védelem tartományvezérlő ügynöke nem fut.|
|Jelszó-szűrési kérelmek/másodperc |Ez a számláló a jelszavak feldolgozásának sebességét jeleníti meg.|
|Jelszó-szűrési kérelem feldolgozási ideje |Ez a számláló a jelszó-szűrési kérelem feldolgozásához szükséges átlagos időt jeleníti meg.|
|Jelszó-szűrési kérelem feldolgozási ideje |Ez a számláló a legutóbbi újraindítás óta a maximális jelszó-szűrési kérelmek feldolgozási idejét jeleníti meg.|
|A naplózási üzemmód miatt elfogadott jelszavak |Ez a számláló a szokásosan visszautasított jelszavak teljes számát jeleníti meg, de elfogadták, mert a jelszóházirend naplózási módban lett konfigurálva (a legutóbbi újraindítás óta).|

## <a name="dc-agent-discovery"></a>TARTOMÁNYVEZÉRLŐ ügynökének felderítése

A `Get-AzureADPasswordProtectionDCAgent` parancsmag használható a tartományban vagy erdőben futó különböző tartományvezérlői ügynökök alapvető adatainak megjelenítéséhez. Ezek az információk a futó tartományvezérlő ügynök szolgáltatás (ok) által regisztrált serviceConnectionPoint-objektum (ok) ból kérhetők le.

A parancsmag kimenete például a következő:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

A különböző tulajdonságokat az egyes tartományvezérlő ügynöki szolgáltatások frissítik, óránkénti rendszerességgel. Az adatai továbbra is Active Directory replikációs késésnek vannak kitéve.

Lehetséges, hogy a parancsmag lekérdezésének hatóköre az – erdő vagy – tartomány paraméterek használatával befolyásolható.

Ha a HeartbeatUTC értéke elavult, ez az egyik tünet lehet, hogy az adott tartományvezérlőn az Azure AD jelszavas védelem tartományvezérlő ügynöke nem fut, vagy el lett távolítva, vagy a gép le lett tiltva, és már nem tartományvezérlő.

Ha a PasswordPolicyDateUTC értéke elavult, akkor lehet, hogy az Azure AD jelszavas védelem tartományvezérlő ügynöke nem működik megfelelően.

## <a name="dc-agent-newer-version-available"></a>A DC Agent újabb verziója érhető el

A DC Agent szolgáltatás 30034 figyelmeztetési eseményt naplóz az operatív naplóba, amikor észleli, hogy a tartományvezérlő-ügynök szoftverének újabb verziója érhető el, például:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

A fenti esemény nem határozza meg az újabb szoftver verzióját. Az adott információhoz tartozó üzenetben keresse meg a hivatkozást.

> [!NOTE]
> Annak ellenére, hogy a fenti üzenetben az "autoupgrade" kifejezésre hivatkozik, a DC-ügynök szoftvere jelenleg nem támogatja ezt a funkciót.

## <a name="proxy-service-event-logging"></a>Proxy szolgáltatás eseménynaplózása

A proxy szolgáltatás egy minimális eseményt bocsát ki a következő eseménynaplóba:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Vegye figyelembe, hogy a nyomkövetési napló alapértelmezés szerint ki van kapcsolva.

> [!WARNING]
> Ha engedélyezve van, a nyomkövetési napló nagy mennyiségű eseményt kap, és ez hatással lehet a proxykiszolgáló teljesítményére. Ezért ez a napló csak akkor engedélyezhető, ha egy probléma mélyebb vizsgálatot igényel, majd csak minimális ideig.

Az eseményeket a különböző proxy-összetevők naplózzák a következő tartományok használatával:

|Összetevő |Eseményazonosító tartománya|
| --- | --- |
|Proxy szolgáltatás üzemeltetési folyamata| 10000-19999|
|Proxy szolgáltatás alapszintű üzleti logikája| 20000-29999|
|PowerShell-parancsmagok| 30000-39999|

## <a name="proxy-service-text-logging"></a>Proxy szolgáltatás szöveges naplózása

A proxy szolgáltatás úgy konfigurálható, hogy egy szöveges naplóba írja a következő beállításazonosító beállításával:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD érték)

A szöveges naplózás alapértelmezés szerint le van tiltva. Az érték érvénybe léptetéséhez a proxy szolgáltatás újraindítása szükséges. Ha engedélyezve van, a proxy szolgáltatás a következő helyen fogja írni a naplófájlba:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> A szöveges napló ugyanazokat a hibakeresési szintű bejegyzéseket kapja meg, amelyek a nyomkövetési naplóba naplózhatók, de általában könnyebben áttekinthetők és elemezhetők.

> [!WARNING]
> Ha engedélyezve van, a napló nagy mennyiségű eseményt kap, és hatással lehet a gép teljesítményére. Ezért ezt a kibővített naplót csak akkor szabad engedélyezni, ha egy probléma mélyebb vizsgálatot igényel, majd csak minimális ideig.

## <a name="powershell-cmdlet-logging"></a>PowerShell-parancsmag naplózása

Az állapot változását eredményező PowerShell-parancsmagok (például a Register-AzureADPasswordProtectionProxy) általában egy eredmény-eseményt naplóznak az operatív naplóba.

Emellett az Azure AD jelszavas védelmi PowerShell-parancsmagjai többsége a következő helyen található szöveges naplóba fog írni:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Ha hiba történik, és a and\or-megoldás oka nem látható, akkor a szöveges naplók is megtekinthetők.

## <a name="proxy-discovery"></a>Proxy felderítése

A `Get-AzureADPasswordProtectionProxy` parancsmag használható a tartományban vagy erdőben futó különböző Azure ad-alapú jelszavas védelmi proxy-szolgáltatásokkal kapcsolatos alapvető információk megjelenítésére. Ezek az adatok a futó proxy szolgáltatás (ok) által regisztrált serviceConnectionPoint-objektum (ok) ból kérhetők le.

A parancsmag kimenete például a következő:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Az egyes proxy szolgáltatások a különböző tulajdonságokat hozzávetőlegesen óránként frissítik. Az adatai továbbra is Active Directory replikációs késésnek vannak kitéve.

Lehetséges, hogy a parancsmag lekérdezésének hatóköre az – erdő vagy – tartomány paraméterek használatával befolyásolható.

Ha a HeartbeatUTC értéke elavult, lehet, hogy az Azure AD jelszavas védelmi proxyja nem fut vagy el lett távolítva a számítógépen.

## <a name="proxy-agent-newer-version-available"></a>A proxy Agent újabb verziója érhető el

A proxy szolgáltatás 20002 figyelmeztetési eseményt fog naplózni az operatív naplóba, amikor észleli, hogy a proxy szoftver újabb verziója érhető el, például:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

A fenti esemény nem határozza meg az újabb szoftver verzióját. Az adott információhoz tartozó üzenetben keresse meg a hivatkozást.

Ezt az eseményt akkor is kibocsátja a rendszer, ha a proxykiszolgáló engedélyezve van az automatikus frissítés beállítással.

## <a name="next-steps"></a>Következő lépések

[Hibaelhárítás az Azure AD jelszavas védelméhez](howto-password-ban-bad-on-premises-troubleshoot.md)

A globális és az egyéni tiltott jelszavakkal kapcsolatos további információkért tekintse meg a [helytelen jelszavakat](concept-password-ban-bad.md) tartalmazó cikket.
