---
title: 'Azure AD Connect: ADSyncTools PowerShell-hivatkozás | Microsoft Docs'
description: Ez a dokumentum a ADSyncTools. psm1 PowerShell-modulra vonatkozó információkat tartalmaz.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1652c52dcc6870e396d74a2f38fe63c304d37df5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672268"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell-hivatkozás
Az alábbi dokumentáció a Azure AD Connect ADSyncTools. psm1 PowerShell-moduljának hivatkozásait tartalmazza.

## <a name="install-the-adsynctools-powershell-module"></a>A ADSyncTools PowerShell-modul telepítése
A ADSyncTools PowerShell-modul telepítéséhez tegye a következőket:

1.  A Windows PowerShell megnyitása rendszergazdai jogosultságokkal
2.  Írja be vagy másolja be a következőt: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Nyomja meg az ENTER billentyűt.
4.  A modul telepítésének ellenőrzéséhez írja be vagy másolja és illessze be a következőt:
    ```powershell
    Get-module AdSyncTools
    ```
5.  Ekkor látnia kell a modul információit.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SZINOPSZIS
Az mS-DS-ConsistencyGuid törlése az AD-felhasználótól

### <a name="syntax"></a>SZINTAXIS

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A cél AD-felhasználóhoz tartozó mS-DS-ConsistencyGuid értékének törlése

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
A célként megadott felhasználó

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SZINOPSZIS
{{Az Áttekintés}} kitöltése

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>LEÍRÁS
{{A Leírás}} kitöltése

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1\. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Példa a Leírás hozzáadására}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SZINOPSZIS
{{Az Áttekintés}} kitöltése

### <a name="syntax"></a>SZINTAXIS

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
{{A Leírás}} kitöltése

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1\. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Példa a Leírás hozzáadására}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-database"></a>– Adatbázis
{{Fill adatbázis leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>– Példány
{{Fill példány leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Fill Password Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Kiszolgáló
{{Fill kiszolgáló leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Fill UserName Description}}

```yaml
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

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SZINOPSZIS
ConsistencyGuid-jelentés exportálása

### <a name="syntax"></a>SZINTAXIS

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
ConsistencyGuid-jelentést hoz létre a CSV-fájl importálásával Import-ADSyncToolsImmutableIdMigration

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Alternatív bejelentkezési azonosító (e-mail) használata

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>– UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Kimenet
Kimeneti fájlnév a CSV-fájlhoz és a NAPLÓFÁJLokhoz

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SZINOPSZIS
{{Az Áttekintés}} kitöltése

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>LEÍRÁS
{{A Leírás}} kitöltése

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1\. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Példa a Leírás hozzáadására}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>– Állomásnév
{{Fill hostName Leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SZINOPSZIS
Felhasználó beolvasása az AD-ből

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Egy AD-objektumot ad vissza: több erdő támogatása

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
A ConsistencyGuid beállítására szolgáló felhasználó az AD-ben

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SZINOPSZIS
Az mS-DS-ConsistencyGuid beszerzése az AD-felhasználótól

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A célként megadott AD-felhasználó mS-DS-ConsistencyGuid attribútumában lévő értéket adja vissza GUID formátumban

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
A célként megadott felhasználó

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SZINOPSZIS
ObjectGuid beolvasása az AD-felhasználótól

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A cél AD-felhasználó ObjectGUID attribútumában lévő értéket adja vissza GUID formátumban

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
A célként megadott felhasználó

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SZINOPSZIS
HRE-kapcsolat futtatási előzményeinek beolvasása

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Függvény, amely visszaadja a HRE-kapcsolat futtatási előzményeit XML formátumban

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>2. PÉLDA
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-days"></a>-Nap
{{Fill napok leírása}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SZINOPSZIS
SourceAnchor-módosítási hibákkal rendelkező felhasználók beolvasása

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A függvény lekérdezi a HRE csatlakozási előzményeit, és exportálja az összes olyan felhasználót, aki a következő hibát jelentette: "a SourceAnchor attribútum megváltozott".

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
#Required Parameters
```

$sourcePath = Read-Host-prompt "adja meg a naplófájl elérési útját a következő fájlnévvel:" # "" \<Source_Path\> $outputPath = Read-Host-prompt "adja meg a fájl elérési útját a" # "fájlnévvel \<Out_Path\>

 Get-ADSyncToolsUsersSourceAnchorChanged-sourcePath $sourcePath-outputPath $outputPath

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-sourcepath"></a>– sourcePath
{{Fill sourcePath leírása}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>– outputPath
{{Fill outputPath leírása}}

```yaml
Type: Object
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

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SZINOPSZIS
ImmutableID importálása a HRE-ből

### <a name="syntax"></a>SZINTAXIS

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Létrehoz egy fájlt az összes Azure AD szinkronizált felhasználóval, amely a ImmutableID értéket tartalmazza GUID formátumban követelmények: MSOnline PowerShell-modul

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-output"></a>-Kimenet
Kimeneti CSV-fájl

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Szinkronizált felhasználók beolvasása az Azure AD Lomtárból

```yaml
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


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SZINOPSZIS
{{Az Áttekintés}} kitöltése

### <a name="syntax"></a>SZINTAXIS

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
{{A Leírás}} kitöltése

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1\. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Példa a Leírás hozzáadására}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-query"></a>– Lekérdezés
{{Kitöltési lekérdezés leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>– SqlConnection
{{Fill SqlConnection leírása}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SZINOPSZIS
Parancsfájl a lejárt tanúsítványok eltávolításához a UserCertificate attribútumból

### <a name="syntax"></a>SZINTAXIS

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Ez a szkript a Active Directory tartományban található cél szervezeti egységből származó összes objektumot az Object osztály (felhasználó/számítógép) alapján szűri, és törli a UserCertificate attribútum összes lejárt tanúsítványát.
Alapértelmezés szerint (BackupOnly mód) csak a lejárt tanúsítványokat fogja menteni egy fájlba, és nem hajt végre semmilyen változást az AD-ben.
Ha a-BackupOnly $false használja, akkor a UserCertificate attribútumban jelen lévő összes lejárt tanúsítvány törlődni fog az AD-ből a fájlba való másolás után.
Minden tanúsítvány biztonsági mentése egy külön fájlnévre történik: ObjectClass_ObjectGUID_CertThumprint. cer a parancsfájl CSV formátumban is létrehoz egy naplófájlt, amely tartalmazza az összes olyan tanúsítványt, amely vagy amely érvényes vagy lejárt, beleértve az aktuálisan végrehajtott műveletet (kihagyott/exportált/törölt).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = felhasználók, OU = Corp, DC = contoso, DC = com" – ObjectClass felhasználó

#### <a name="example-2"></a>2. PÉLDA
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = számítógépek, OU = Corp, DC = contoso, DC = com" – ObjectClass számítógép – BackupOnly $false

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-targetou"></a>-TargetOU
Cél szervezeti egység az AD-objektumok kereséséhez

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
A BackupOnly nem törli az AD-ből származó tanúsítványokat

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>– ObjectClass
Objektumosztály-szűrő

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SZINOPSZIS
Rövid leírás

### <a name="syntax"></a>SZINTAXIS

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>LEÍRÁS
Hosszú leírás

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>SZINOPSZIS
{{Az Áttekintés}} kitöltése

### <a name="syntax"></a>SZINTAXIS

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>LEÍRÁS
{{A Leírás}} kitöltése

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1\. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Példa a Leírás hozzáadására}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>– Állomásnév
{{Fill hostName Leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SZINOPSZIS
(TEENDŐk) Az AD UserCertificate attribútumának visszaállítása tanúsítványfájl-fájlból

### <a name="syntax"></a>SZINTAXIS

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>LEÍRÁS
Hosszú leírás

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SZINOPSZIS
MS-DS-ConsistencyGuid beállítása az AD-felhasználón

### <a name="syntax"></a>SZINTAXIS

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Érték beállítása az mS-DS-ConsistencyGuid attribútumban a cél AD-felhasználó számára

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
A ConsistencyGuid beállítására szolgáló felhasználó az AD-ben

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>– Érték
ImmutableId (bájt tömb, GUID, GUID karakterlánc vagy Base64 karakterlánc)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SZINOPSZIS
{{Az Áttekintés}} kitöltése

### <a name="syntax"></a>SZINTAXIS

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>LEÍRÁS
{{A Leírás}} kitöltése

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1\. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Példa a Leírás hozzáadására}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>– Állomásnév
{{Fill hostName Leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>– Port
{{Fill port leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>SZINOPSZIS
Nyomkövetési fájl létrehozása a és az AD importálási lépésből

### <a name="syntax"></a>SZINTAXIS

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Egy HRE-összekötő AD-alkalmazás összes LDAP-lekérdezését nyomon követi egy adott AD-vízjel ellenőrzőpontján (partíció cookie). Létrehoz egy ". \ ADimportTrace_yyyyMMddHHmmss. log" nyomkövetési fájlt az aktuális mappában.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>– DC
Az AD Connector exportálási XML-fájlja

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>– rootDN
Cél tartományvezérlő

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>– szűrő
Erdő gyökérszintű megkülönböztető neve

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
A nyomon követett AD-objektumok típusai \> * = minden objektumtípus

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
Ha már fut tartományi rendszergazdaként, nincs szükség AD hitelesítő adatok megadására.
Vízjel manuális bevitele XML-fájl helyett például: $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA (...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SZINOPSZIS
Rövid leírás

### <a name="syntax"></a>SZINTAXIS

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Hosszú leírás

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-context"></a>– Környezet
A param1 súgójának leírása

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Kiszolgáló
A param2 súgójának leírása

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
A param2 súgójának leírása

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>– Szűrő
A param2 súgójának leírása

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SZINOPSZIS
A felhasználók frissítése az új ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SZINTAXIS

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Frissíti a felhasználókat a ConsistencyGuid jelentésből származó új ConsistencyGuid (ImmutableId) értékkel. Ez a függvény támogatja a WhatIf kapcsoló megjegyzését: a ConsistencyGuid jelentést a TAB Demiliter kell importálni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>2. PÉLDA
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-distinguishedname"></a>– DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>– Művelet
Művelet

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Kimenet
NAPLÓFÁJLok kimeneti fájlneve

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>– WhatIf
Bemutatja, mi történne a parancsmag futtatásakor.
A parancsmag nem fut.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Jóváhagyást kér a parancsmag futtatása előtt.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
