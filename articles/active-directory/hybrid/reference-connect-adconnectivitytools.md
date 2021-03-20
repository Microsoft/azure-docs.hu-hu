---
title: 'Azure AD Connect: ADConnectivityTools PowerShell-hivatkozás | Microsoft Docs'
description: Ez a dokumentum a ADConnectivityTools. psm1 PowerShell-modulra vonatkozó információkat tartalmaz.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5e06d0a6f7693ca9cc69f05f6b2318d6b60b5e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070802"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: ADConnectivityTools PowerShell-hivatkozás

Az alábbi dokumentáció a Azure AD Connect ADConnectivityTools. psm1 PowerShell-moduljának hivatkozásait tartalmazza.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>SZINOPSZIS

Észleli a helyi DNS-problémákat.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi DNS-kapcsolati teszteket futtat.
Az Active Directory-összekötő konfigurálásához a felhasználónak mindkét nevet resolutionthe kell adnia ahhoz az erdőhöz, amelyhez csatlakozni próbál, valamint az ehhez az erdőhöz társított tartományvezérlőkön is.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Meghatározza a vizsgálandó erdő nevét.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

A teszteléshez használandó tartományvezérlőket kell megadnia.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

A diagnózis eredményét adja vissza PSObject formájában.
Nem szükséges az eszközzel végzett manuális interakció során.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>SZINOPSZIS

Meghatározza, hogy létezik-e egy adott erdő.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Egy erdőhöz társított IP-címek DNS-kiszolgálójának lekérdezése.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Meghatározza a vizsgálandó erdő nevét.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>SZINOPSZIS

Az AD-erdő működési szintjének ellenőrzése.

### <a name="syntax"></a>SZINTAXIS

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Ellenőrzi, hogy az AD-erdő működési szintje egyenlő vagy nagyobb-e, mint egy adott MinAdForestVersion (WindowsServer2003).
A fiók (tartomány \ Felhasználónév) és a jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>3. PÉLDA

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél erdő.
Az alapértelmezett érték az aktuálisan bejelentkezett felhasználó erdője.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>– ForestFQDN

Cél ForestFQDN objektum.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény azon felhasználó hitelesítő adatait fogja használni, amely jelenleg be van jelentkezve a számítógépen, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SZINOPSZIS

Észleli a helyi hálózati kapcsolati problémákat.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi hálózati kapcsolati teszteket futtat.

A helyi hálózati tesztek esetében a HRE-csatlakozásnak képesnek kell lennie kommunikálni a nevesített tartományvezérlőkkel az 53 (DNS), a 88 (Kerberos) és a 389 (LDAP) használatával, amely a legtöbb szervezet DNS-t futtat a tartományvezérlőn, ezért a teszt jelenleg integrálva van.
Ha egy másik DNS-kiszolgáló van megadva, a 53-es portot ki kell hagyni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-dcs"></a>-DCs

A teszteléshez használandó tartományvezérlőket kell megadnia.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Ha a felhasználó nem az AD-hely/bejelentkezési tartományvezérlő által biztosított DNS-szolgáltatásokat használja, érdemes lehet kihagyni a 53-es portot.
A felhasználónak továbbra is képesnek kell lennie _.ldap._tcp feloldására.\<forestfqdn\>
ahhoz, hogy a Active Directory-összekötő konfigurációja sikeres legyen.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

A diagnózis eredményét adja vissza PSObject formájában.
Nem szükséges az eszközzel végzett manuális interakció során.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>SZINOPSZIS

Meghatározza, hogy a megadott erdő és a hozzá tartozó tartományvezérlők elérhetők-e.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Futtatja a "ping" teszteket (azt, hogy a számítógép elérheti-e a célszámítógépet a hálózaton és/vagy az interneten keresztül)

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Meghatározza a vizsgálandó erdő nevét.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

A teszteléshez használandó tartományvezérlőket kell megadnia.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>SZINOPSZIS

Annak ellenőrzése, hogy a beszerzett erdő teljes tartománynevében lévő tartományok elérhetők-e

### <a name="syntax"></a>SZINTAXIS

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Ellenőrizze, hogy a beszerzett erdő teljes TARTOMÁNYNEVÉben lévő összes tartomány elérhető-e a DomainGuid és a DomainDN beolvasására tett kísérlet során.
A fiók (tartomány \ Felhasználónév) és a jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>3. PÉLDA

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél erdő.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>– ForestFQDN

Cél ForestFQDN objektum.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény azon felhasználó hitelesítő adatait fogja használni, amely jelenleg be van jelentkezve a számítógépen, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SZINOPSZIS

Ellenőrzi, hogy a felhasználó rendelkezik-e vállalati rendszergazdai hitelesítő adatokkal.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Megkeresi, hogy a megadott felhasználó rendelkezik-e vállalati rendszergazdai hitelesítő adatokkal.
A fiók (tartomány \ Felhasználónév) és a jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény azon felhasználó hitelesítő adatait fogja használni, amely jelenleg be van jelentkezve a számítógépen, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SZINOPSZIS

Lekéri egy DomainFQDN a fiók és a jelszó kombinációjával.

### <a name="syntax"></a>SZINTAXIS

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Megpróbál beolvasni egy domainFQDN-objektumot a megadott hitelesítő adatokból.
Ha a domainFQDN érvényes, akkor a rendszer a felhasználó által választott DomainFQDNName vagy RootDomainName ad vissza.
A fiók (tartomány \ Felhasználónév) és a jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

A lekérdezni kívánt adattípusok.
Jelenleg csak "DomainFQDNName" vagy "RootDomainName" lehet.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény azon felhasználó hitelesítő adatait fogja használni, amely jelenleg be van jelentkezve a számítógépen, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Start-NetworkConnectivityDiagnosisTools függvény által használt kiegészítő paraméter

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SZINOPSZIS

Lekéri egy ForestFQDN a fiók és a jelszó kombinációjával.

### <a name="syntax"></a>SZINTAXIS

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Megpróbál beolvasni egy ForestFQDN a megadott hitelesítő adatokból.
A fiók (tartomány \ Felhasználónév) és a jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél erdő. Az alapértelmezett érték az aktuálisan bejelentkezett felhasználó tartománya.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény azon felhasználó hitelesítő adatait fogja használni, amely jelenleg be van jelentkezve a számítógépen, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SZINOPSZIS

Fő függvény.

### <a name="syntax"></a>SZINTAXIS

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Futtatja az összes rendelkezésre álló mechanizmust, amely ellenőrzi az AD-hitelesítő adatok érvényességét.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél erdő.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

Egyéni telepítések esetén: jelző, amely akkor $True, ha a felhasználó az "új AD-fiók létrehozása" lehetőséget választotta a AADConnect varázsló AD Forest-fiók ablakában.
$False, ha a felhasználó a "meglévő AD-fiók használata" lehetőséget választotta.
Expressz telepítések esetén: a változó értékének $True kell lennie az expressz telepítések esetében.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

A paraméter, amely előzetesen kitölti a Felhasználónév mezőt, amikor a felhasználó hitelesítő adatait kéri.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SZINOPSZIS

A hálózati kapcsolati tesztek fő funkciója.

### <a name="syntax"></a>SZINTAXIS

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi hálózati kapcsolati teszteket futtat.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Megadja az erdő nevét a teszteléshez.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>– Hitelesítő adatok

A tesztet futtató felhasználó felhasználóneve és jelszava.
A Azure AD Connect varázsló futtatásához azonos szintű engedélyekre van szükség.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Megadja egy naplófájl helyét, amely a függvény kimenetét fogja tartalmazni.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

A teszteléshez használandó tartományvezérlőket kell megadnia.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Az a jelző, amely lehetővé teszi a függvény céljával kapcsolatos üzenet megjelenítését.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

A diagnózis eredményét adja vissza PSObject formájában.
Az eszközzel történő manuális interakció során nem szükséges megadni.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Azt jelzi, hogy a felhasználó által beírt hitelesítő adatok érvényesek-e.
Az eszközzel történő manuális interakció során nem szükséges megadni.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
