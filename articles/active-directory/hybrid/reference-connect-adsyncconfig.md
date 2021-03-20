---
title: 'Azure AD Connect: ADSyncConfig PowerShell-hivatkozás | Microsoft Docs'
description: Ez a dokumentum a ADSyncConfig. psm1 PowerShell-modulra vonatkozó információkat tartalmaz.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2126aceba8724b46de094d14db754d704500c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85850965"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig PowerShell-hivatkozás
Az alábbi dokumentáció a Azure AD Connect ADSyncConfig. psm1 PowerShell-moduljának hivatkozásait tartalmazza.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SZINOPSZIS
Az egyes AD-összekötők által konfigurált fiók nevének és tartományának beolvasása

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>LEÍRÁS
Ez a függvény a "Get-ADSyncConnector" parancsmagot használja, amely megtalálható a HRE csatlakozásban a kapcsolódási paraméterekből való lekéréshez egy olyan tábla, amely az AD-összekötő (k) fiókot mutatja.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SZINOPSZIS
AD-objektumok letiltásának engedélyezése az engedélyek öröklésével

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A SearchBase paramétertől kezdődően megkeresi az AD-t, és visszaadja az összes olyan objektumot, amely a ObjectClass paraméter szerint szűrve van, és az ACL öröklődése jelenleg le

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
A "contoso" tartományban letiltott örökléssel rendelkező objektumok keresése (alapértelmezés szerint csak a "organizationalUnit" objektumokat adja vissza)
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>2. PÉLDA
A "contoso" tartományban letiltott örökléssel rendelkező felhasználói objektumok keresése
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>3. PÉLDA
A szervezeti egységben letiltott örökléssel rendelkező összes típusú objektum megkeresése
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-searchbase"></a>-SearchBase
Az Active Directory tartományi DistinguishedName vagy teljes tartománynevet tartalmazó LDAP-lekérdezés SearchBase

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

#### <a name="-objectclass"></a>– ObjectClass
A keresendő objektumok osztálya lehet "*" (bármely objektumosztály esetében), "user", "Group", "Container" stb. Alapértelmezés szerint ez a függvény a "organizationalUnit" objektum osztályát fogja keresni.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az alapszintű olvasási engedélyek Active Directory erdő és tartomány inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncBasicReadPermissions függvény megadja a szükséges engedélyeket az AD szinkronizációs fióknak, amely a következőket tartalmazza:
1.
Tulajdonság-hozzáférés beolvasása az összes leszármazott számítógép-objektum összes attribútumán
2.
Tulajdonságok elérésének olvasása az összes leszármazott objektum összes attribútumán
3.
Tulajdonságok beolvasása a foreignsecurityprincipal összes leszármazott objektumához
5.
Tulajdonság-hozzáférés beolvasása az összes leszármazott felhasználói objektum összes attribútumán
6.
Tulajdonságok elérésének beolvasása az összes olyan inetOrgPerson objektum összes attribútumán
7.
Tulajdonságok elérésének beolvasása az összes leszármazott csoport objektumaira vonatkozóan
8.
Tulajdonságok elérésének olvasása az összes leszármazott kapcsolati objektum összes attribútumán

Ezek az engedélyek az erdő összes tartományára érvényesek.
Opcionálisan megadhat egy DistinguishedName a ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD-objektumra állítsa be (beleértve az alárendelt objektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Annak a Active Directory fióknak a neve, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
A Active Directory fiók tartománya, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
A Active Directory-fiók DistinguishedName, amelyet Azure AD Connect szinkronizálás használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A cél AD-objektum DistinguishedName az engedélyek beállításához (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Választható paraméter annak jelzésére, hogy a AdminSDHolder-tároló nem frissíthető-e ezekkel az engedélyekkel

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SZINOPSZIS
A Active Directory erdő és tartomány inicializálása az Exchange Hybrid szolgáltatáshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncExchangeHybridPermissions függvény megadja a szükséges engedélyeket az AD szinkronizációs fióknak, amely a következőket tartalmazza:
1.
Olvasási/írási tulajdonságok hozzáférése az összes leszármazott felhasználói objektum összes attribútumához
2.
Olvasási/írási tulajdonság-hozzáférés az összes, a leszármazott inetOrgPerson objektum összes attribútumához
3.
Olvasási/írási tulajdonságok hozzáférése az összes leszármazott csoport objektumainak összes attribútumán
4.
Olvasási/írási tulajdonságok hozzáférése az összes leszármazott kapcsolati objektum összes attribútumához

Ezek az engedélyek az erdő összes tartományára érvényesek.
Opcionálisan megadhat egy DistinguishedName a ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD-objektumra állítsa be (beleértve az alárendelt objektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Annak a Active Directory fióknak a neve, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
A Active Directory fiók tartománya, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
A Active Directory-fiók DistinguishedName, amelyet Azure AD Connect szinkronizálás használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A cél AD-objektum DistinguishedName az engedélyek beállításához (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Választható paraméter annak jelzésére, hogy a AdminSDHolder-tároló nem frissíthető-e ezekkel az engedélyekkel

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SZINOPSZIS
A Active Directory erdő és tartomány inicializálása az Exchange-levelezés nyilvános mappája szolgáltatáshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncExchangeMailPublicFolderPermissions függvény megadja a szükséges engedélyeket az AD szinkronizációs fióknak, amely a következőket tartalmazza:
1.
Tulajdonságok beolvasása a publicfolder összes leszármazott objektumához

Ezek az engedélyek az erdő összes tartományára érvényesek.
Opcionálisan megadhat egy DistinguishedName a ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD-objektumra állítsa be (beleértve az alárendelt objektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Annak a Active Directory fióknak a neve, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
A Active Directory fiók tartománya, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
A Active Directory-fiók DistinguishedName, amelyet Azure AD Connect szinkronizálás használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A cél AD-objektum DistinguishedName az engedélyek beállításához (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Választható paraméter annak jelzésére, hogy a AdminSDHolder-tároló nem frissíthető-e ezekkel az engedélyekkel

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SZINOPSZIS
Inicializálja Active Directory erdőjét és tartományát az mS-DS-ConsistencyGuid szolgáltatáshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncMsDsConsistencyGuidPermissions függvény megadja a szükséges engedélyeket az AD szinkronizációs fióknak, amely a következőket tartalmazza:
1.
Olvasási/írási tulajdonság-hozzáférés az mS-DS-ConsistencyGuid attribútumhoz az összes leszármazott felhasználói objektumhoz

Ezek az engedélyek az erdő összes tartományára érvényesek.
Opcionálisan megadhat egy DistinguishedName a ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD-objektumra állítsa be (beleértve az alárendelt objektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Annak a Active Directory fióknak a neve, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
A Active Directory fiók tartománya, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
A Active Directory-fiók DistinguishedName, amelyet Azure AD Connect szinkronizálás használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A cél AD-objektum DistinguishedName az engedélyek beállításához (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Választható paraméter annak jelzésére, hogy a AdminSDHolder-tároló nem frissíthető-e ezekkel az engedélyekkel

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SZINOPSZIS
Inicializálja Active Directory erdőjét és tartományát a jelszó kivonatának szinkronizálásához.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncPasswordHashSyncPermissions függvény megadja a szükséges engedélyeket az AD szinkronizációs fióknak, amely a következőket tartalmazza:
1.
Címtárbeli módosítások replikálása
2.
A címtár összes módosításának replikálása

Ezeket az engedélyeket az erdő összes tartománya megkapja.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Annak a Active Directory-fióknak a neve, amelyet a Azure AD Connect szinkronizálása a címtárban található objektumok kezeléséhez fog használni.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Annak a Active Directory fióknak a tartománya, amelyet az Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Annak a Active Directory-fióknak a DistinguishedName, amelyet az Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SZINOPSZIS
Inicializálja Active Directory erdőjét és tartományát az Azure AD-vel való jelszó-visszaíráshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncPasswordWritebackPermissions függvény megadja a szükséges engedélyeket az AD szinkronizációs fióknak, amely a következőket tartalmazza:
1.
Jelszó alaphelyzetbe állítása a leszármazott felhasználói objektumokon
2.
Tulajdonság-hozzáférés írása a lockoutTime attribútumhoz az összes leszármazott felhasználói objektumhoz
3.
Tulajdonság-hozzáférés írása a pwdLastSet attribútumhoz az összes leszármazott felhasználói objektumhoz

Ezek az engedélyek az erdő összes tartományára érvényesek.
Opcionálisan megadhat egy DistinguishedName a ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD-objektumra állítsa be (beleértve az alárendelt objektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Annak a Active Directory fióknak a neve, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
A Active Directory fiók tartománya, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
A Active Directory-fiók DistinguishedName, amelyet Azure AD Connect szinkronizálás használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A cél AD-objektum DistinguishedName az engedélyek beállításához (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Választható paraméter annak jelzésére, hogy a AdminSDHolder-tároló nem frissíthető-e ezekkel az engedélyekkel

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SZINOPSZIS
Szigorítsa az engedélyeket olyan AD-objektumon, amelyet semmilyen más AD-védelemmel ellátott biztonsági csoportban nem tartalmaz.
Egy tipikus példa az HRE-kapcsolat által létrehozott AD csatlakozási fiók (MSOL).
Ez a fiók replikálja az engedélyeket az összes tartományra vonatkozóan, azonban könnyen feloldható, mert nem védett.

### <a name="syntax"></a>SZINTAXIS

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncRestrictedPermissions függvény szigorítja az engedélyeket a megadott fiókkal.
Az engedélyek szigorítása a következő lépésekkel jár:
1. Öröklés letiltása a megadott objektumon
2. Távolítsa el az összes Ace-t az adott objektumon, kivéve az önmagukhoz tartozó Ace-ket.
Azt szeretnénk, hogy az alapértelmezett engedélyek érintetlenek maradjanak, ha önmagukban is megmaradnak.
3. Rendelje hozzá ezeket a konkrét engedélyeket:

   | Típus | Name | Access | Érvényesség |
   |------|------|--------|------------|
   | Engedélyezés | RENDSZER | Teljes hozzáférés | Ez az objektum |
   | Engedélyezés | Vállalati rendszergazdák | Teljes hozzáférés | Ez az objektum |
   | Engedélyezés | Tartományi rendszergazdák | Teljes hozzáférés | Ez az objektum | 
   | Engedélyezés | Rendszergazdák | Teljes hozzáférés | Ez az objektum |
   | Engedélyezés | Vállalati tartományvezérlők | Tartalom listázása <br> Az összes tulajdonság olvasása <br> Olvasási engedélyek | Ez az objektum |
   | Engedélyezés | Hitelesített felhasználók | Tartalom listázása <br> Az összes tulajdonság olvasása <br> Olvasási engedélyek | Ez az objektum |

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Annak a Active Directory-fióknak a DistinguishedName, amelynek engedélyeit meg kell szigorítani.
Ez általában az AD-összekötőben konfigurált MSOL_nnnnnnnnnn fiók vagy egyéni tartományi fiók.

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

#### <a name="-credential"></a>-Credential
Rendszergazdai hitelesítő adatok, amelyek rendelkeznek a ADConnectorAccountDN-fiók engedélyeinek korlátozásához szükséges jogosultságokkal. Ez általában a vállalati vagy tartományi rendszergazda. A fiókok keresési hibáinak elkerülése érdekében használja a rendszergazdai fiók teljes tartománynevét.
Példa: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
A DisableCredentialValidation használatakor a függvény nem vizsgálja meg, hogy a hitelesítő adatok megadott hitelesítő adatai érvényesek-e az AD-ben, és ha a megadott fiók rendelkezik a szükséges jogosultságokkal a ADConnectorAccountDN-fiók engedélyeinek korlátozásához.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SZINOPSZIS
A Active Directory erdő és tartomány inicializálása az Azure AD-ből származó csoportos visszaírási.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncUnifiedGroupWritebackPermissions függvény megadja a szükséges engedélyeket az AD szinkronizációs fióknak, amely a következőket tartalmazza:
1.
Általános olvasási/írási, törlési, törlési és Create\Delete gyermek az összes csoport típusú objektumhoz és alobjektumhoz

Ezek az engedélyek az erdő összes tartományára érvényesek.
Opcionálisan megadhat egy DistinguishedName a ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD-objektumra állítsa be (beleértve az alárendelt objektumok öröklését is).
Ebben az esetben a ADobjectDN annak a tárolónak a megkülönböztető neve lesz, amelyet a GroupWriteback szolgáltatással szeretne összekapcsolni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Annak a Active Directory fióknak a neve, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
A Active Directory fiók tartománya, amelyet a Azure AD Connect szinkronizálása használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
A Active Directory-fiók DistinguishedName, amelyet Azure AD Connect szinkronizálás használ a címtárban található objektumok kezeléséhez.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A cél AD-objektum DistinguishedName az engedélyek beállításához (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Választható paraméter annak jelzésére, hogy a AdminSDHolder-tároló nem frissíthető-e ezekkel az engedélyekkel

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SZINOPSZIS
Megjeleníti egy megadott AD-objektum engedélyeit.

### <a name="syntax"></a>SZINTAXIS

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Ez a függvény a-ADobjectDN paraméterben megadott AD-objektumhoz jelenleg beállított AD-engedélyeket adja vissza.
A ADobjectDN DistinguishedName formátumban kell megadni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN leírása}}

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

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információ: about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
