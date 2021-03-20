---
title: Az Azure DS Domain Services engedélyezése sablon használatával | Microsoft Docs
description: Útmutató Azure Active Directory Domain Services konfigurálásához és engedélyezéséhez Azure Resource Manager sablon használatával
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e18825da64d0d200f55ce72985ac843b93b1e612
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618790"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Azure Active Directory Domain Services felügyelt tartomány létrehozása Azure Resource Manager sablon használatával

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi, hogy a felhasználók a vállalati hitelesítő adataikkal jelentkezzenek be, és meglévő csoportokat és felhasználói fiókokat is használhatnak az erőforrásokhoz való hozzáférés biztosításához.

Ebből a cikkből megtudhatja, hogyan hozhat létre felügyelt tartományt egy Azure Resource Manager sablon használatával. A támogatási erőforrások Azure PowerShell használatával jönnek létre.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Az Azure PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az utasításokat a [Azure PowerShell modul telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz](/powershell/azure/install-az-ps).
    * Győződjön meg róla, hogy bejelentkezik az Azure-előfizetésbe a [AzAccount][Connect-AzAccount] parancsmag használatával.
* Az Azure AD PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az [Azure ad PowerShell-modul telepítésére és az Azure ad-hez való kapcsolódásra](/powershell/azure/active-directory/install-adv2)vonatkozó utasításokat.
    * Győződjön meg arról, hogy bejelentkezik az Azure AD-bérlőbe a [AzureAD][Connect-AzureAD] parancsmag használatával.
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.

## <a name="dns-naming-requirements"></a>DNS-elnevezési követelmények

Azure AD DS felügyelt tartomány létrehozásakor meg kell adnia egy DNS-nevet. A DNS-név kiválasztásakor a következő szempontokat kell figyelembe venni:

* **Beépített tartomány neve:** Alapértelmezés szerint a rendszer a könyvtár beépített tartománynevét használja (a *. onmicrosoft.com* utótagot). Ha engedélyezni szeretné a biztonságos LDAP-hozzáférést a felügyelt tartományhoz az interneten keresztül, nem hozhat létre digitális tanúsítványt a kapcsolat védelméhez ezzel az alapértelmezett tartománnyal. A Microsoft tulajdonosa a *. onmicrosoft.com* tartomány, így a hitelesítésszolgáltató (CA) nem bocsát ki tanúsítványt.
* **Egyéni tartománynevek:** A leggyakoribb módszer egy egyéni tartománynév megadása, amely általában az egyik, amelyet már birtokol, és irányítható. Ha irányítható, egyéni tartományt használ, a forgalom az alkalmazások támogatásához szükséges módon képes megfelelően átáramlani.
* **Nem irányítható tartomány utótagja:** Általában azt javasoljuk, hogy kerülje a nem átirányítható tartománynevek utótagját, például a *contoso. local* nevet. A *. local* utótag nem irányítható, és a DNS-feloldással kapcsolatos problémákat okozhat.

> [!TIP]
> Ha egyéni tartománynevet hoz létre, gondoskodjon a meglévő DNS-névterekről. Azt javasoljuk, hogy a tartománynevet a meglévő Azure-beli vagy helyszíni DNS-névtértől elkülönítve használja.
>
> Ha például meglévő DNS- *contoso.com* rendelkezik, hozzon létre egy felügyelt tartományt a *aaddscontoso.com* egyéni tartománynevével. Ha biztonságos LDAP-t kell használnia, regisztrálnia kell az egyéni tartománynevet a szükséges tanúsítványok létrehozásához.
>
> Előfordulhat, hogy létre kell hoznia néhány további DNS-rekordot a környezetében lévő más szolgáltatásokhoz, vagy feltételes DNS-továbbítókat a környezet meglévő DNS-neve között. Ha például olyan webkiszolgálót futtat, amely a gyökér DNS-nevet használja, akkor olyan elnevezési ütközések lehetnek, amelyek további DNS-bejegyzéseket igényelnek.
>
> Ebben a példában és útmutatókban a *aaddscontoso.com* egyéni tartományát használjuk rövid példaként. Az összes parancsban adja meg a saját tartománynevét.

A DNS-név következő korlátozásai is érvényesek:

* **Tartományi előtagra vonatkozó korlátozások:** Nem hozhat létre 15 karakternél hosszabb előtaggal rendelkező felügyelt tartományt. A megadott tartománynév előtagja (például a *aaddscontoso.com* tartomány *aaddscontoso* ) legalább 15 karakterből állhat.
* **Hálózati név ütközése:** A felügyelt tartomány DNS-tartományneve még nem létezik a virtuális hálózaton. Konkrétan a következő helyzetekben érdemes megkeresni a nevek ütközését:
    * Ha már rendelkezik egy Active Directory tartománnyal ugyanazzal a DNS-tartománynévvel az Azure-beli virtuális hálózaton.
    * Ha a virtuális hálózat, amelyen engedélyezni szeretné a felügyelt tartományt, VPN-kapcsolattal rendelkezik a helyszíni hálózattal. Ebben az esetben győződjön meg arról, hogy nem rendelkezik ugyanazzal a DNS-tartománynévvel rendelkező tartománnyal a helyszíni hálózaton.
    * Ha az Azure-beli virtuális hálózaton már van ilyen nevű Azure Cloud Service-szolgáltatás.

## <a name="create-required-azure-ad-resources"></a>Szükséges Azure AD-erőforrások létrehozása

Az Azure AD DS használatához egy egyszerű szolgáltatásnév és egy Azure AD-csoport szükséges. Ezek az erőforrások lehetővé teszik, hogy a felügyelt tartomány szinkronizálja az adatokat, és meghatározza, hogy mely felhasználók rendelkeznek rendszergazdai jogosultságokkal a felügyelt tartományban.

Először regisztrálja a Azure AD Domain Services erőforrás-szolgáltatót a [Register-AzResourceProvider][Register-AzResourceProvider] parancsmag használatával:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Hozzon létre egy Azure AD-szolgáltatásnevet az Azure AD DS [új-azureadserviceprincipal parancsmagot][New-AzureADServicePrincipal] parancsmagjának használatával, hogy kommunikáljon és hitelesítse magát. A rendszer egy adott alkalmazásspecifikus azonosítót használ a *6BA9A5D4-8456-4118-B521-9C5CA10CDF84* azonosítóval rendelkező *tartományvezérlői szolgáltatások* névvel. Ne módosítsa az alkalmazás AZONOSÍTÓját.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Most hozzon létre egy *HRE DC-rendszergazdák* nevű Azure ad [-csoportot a New-AzureADGroup][New-AzureADGroup] parancsmag használatával. A csoportba felvett felhasználók engedélyeket kapnak az adminisztrációs feladatok végrehajtásához a felügyelt tartományon.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Ha létrehozta a *HRE DC-rendszergazdák* csoportot, adjon hozzá egy felhasználót a csoporthoz az [Add-AzureADGroupMember][Add-AzureADGroupMember] parancsmag használatával. Először a Get [-AzureADGroup][Get-AzureADGroup] parancsmaggal szerezheti be a *HRE DC rendszergazdák* csoportjának azonosítóját, majd a kívánt felhasználó objektumazonosítót a [Get-AzureADUser][Get-AzureADUser] parancsmag használatával.

A következő példában a fiók felhasználói objektumának azonosítója egy egyszerű felhasználónévvel `admin@contoso.onmicrosoft.com` . Cserélje le ezt a felhasználói fiókot annak a felhasználónak a UPN-fiókjára, amelyet hozzá szeretne adni a *HRE DC-rendszergazdák* csoporthoz:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Végül hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup] parancsmag használatával. A következő példában az erőforráscsoport neve *myResourceGroup* , és a *westus* régióban jön létre. Saját név és kívánt régió használata:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Ha olyan régiót választ, amely támogatja az Availability Zones-t, az Azure AD DS erőforrásai a további redundancia érdekében a zónák között oszlanak meg. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található.

Nem kell konfigurálnia az Azure AD DS a zónák közötti elosztására. Az Azure platform automatikusan kezeli az erőforrások zónájának eloszlását. További információért és a régiók rendelkezésre állásának megtekintéséhez lásd: [Mi a Availability Zones az Azure-ban?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Az Azure AD DS erőforrás-definíciója

A Resource Manager erőforrás-definíciójának részeként a következő konfigurációs paramétereket kell megadni:

| Paraméter               | Érték |
|-------------------------|---------|
| domainName              | A felügyelt tartomány DNS-tartományneve, amely figyelembe veszi az előtagok és az ütközések elnevezésére vonatkozó korábbi pontokat. |
| filteredSync            | Az Azure AD DS lehetővé teszi az Azure AD-ben elérhető *összes* felhasználó és csoport szinkronizálását, vagy csak bizonyos csoportok *hatókörön* belüli szinkronizálását.<br /><br /> A hatókörön belüli szinkronizálással kapcsolatos további információkért lásd: [Azure ad Domain Services hatókörön belüli szinkronizálás][scoped-sync].|
| notificationSettings    | Ha a felügyelt tartományban bármilyen riasztás keletkezik, a rendszer e-mailes értesítéseket küldhet. <br /><br />Az Azure-bérlő *globális rendszergazdái* és a *HRE DC-rendszergazdák* csoport tagjai is *engedélyezhetők* ezekhez az értesítésekhez.<br /><br /> Ha kívánja, további címzetteket is hozzáadhat az értesítésekhez, ha olyan riasztásokra van szükség, amelyeknek figyelmet igényelnek.|
| domainConfigurationType | Alapértelmezés szerint a felügyelt tartomány *felhasználói* erdőként jön létre. Ez a típusú erdő az Azure AD összes objektumát szinkronizálja, beleértve a helyszíni AD DS környezetben létrehozott felhasználói fiókokat is. Felhasználói erdő létrehozásához nem kell *tartománykonfigurációt* értéket megadnia.<br /><br /> Az *erőforrás* -erdő csak a közvetlenül az Azure ad-ben létrehozott felhasználókat és csoportokat szinkronizálja. Állítsa a *ResourceTrusting* értéket az erőforrás-erdő létrehozásához.<br /><br />Az *erőforrás* -erdőkkel kapcsolatos további információkért, beleértve az egyiket, és hogyan hozhat létre erdőszintű megbízhatósági kapcsolatot a helyszíni AD DS-tartományokkal, tekintse meg az [Azure AD DS Resource Forests – áttekintés][resource-forests]című témakört.|

A következő tömörített paraméterek definíciója az értékek deklarált módját mutatja be. Létrejön egy *aaddscontoso.com* nevű felhasználói erdő az Azure ad-vel a felügyelt tartományba szinkronizált összes felhasználóval:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

A következő összefoglalt Resource Manager-sablon erőforrástípus a felügyelt tartomány definiálásához és létrehozásához használatos. Egy Azure-beli virtuális hálózatnak és alhálózatnak már léteznie kell, vagy a Resource Manager-sablon részeként kell létrehoznia. A felügyelt tartomány ehhez az alhálózathoz van csatlakoztatva.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Ezek a paraméterek és erőforrástípus egy szélesebb körű Resource Manager-sablon részeként használhatók a felügyelt tartományok üzembe helyezéséhez, ahogy az a következő szakaszban is látható.

## <a name="create-a-managed-domain-using-sample-template"></a>Felügyelt tartomány létrehozása sablon használatával

A következő teljes Resource Manager-sablon létrehoz egy felügyelt tartományt és a támogató virtuális hálózat, az alhálózat és a hálózati biztonsági csoport szabályait. A hálózati biztonsági csoport szabályai a felügyelt tartomány biztonságossá tételéhez szükségesek, és gondoskodni kell a forgalom megfelelő áramlását. Létrejön egy *aaddscontoso.com* DNS-névvel rendelkező felhasználói erdő, amely az Azure ad-ből szinkronizált összes felhasználóval rendelkezik:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Ez a sablon az előnyben részesített telepítési módszer, például a [Azure Portal][portal-deploy], [Azure POWERSHELL][powershell-deploy]vagy egy CI/CD-folyamat használatával telepíthető. A következő példa a [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] parancsmagot használja. Adja meg a saját erőforráscsoport nevét és sablonjának fájlnevét:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Az erőforrás létrehozása és a vezérlés visszaküldése a PowerShell-parancssorba néhány percet vesz igénybe. A felügyelt tartomány továbbra is a háttérben lesz kiépítve, és akár egy óráig is eltarthat a telepítés befejezéséhez. A Azure Portal a felügyelt tartomány **Áttekintés** lapja a telepítési fázis aktuális állapotát jeleníti meg.

Ha a Azure Portal azt mutatja, hogy a felügyelt tartomány befejezte az üzembe helyezést, a következő feladatokat kell elvégezni:

* A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez.
    * A DNS konfigurálásához válassza ki a felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) így a végfelhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba.

## <a name="next-steps"></a>Következő lépések

A felügyelt tartomány működés közbeni megtekintéséhez [tartományhoz csatlakoztathat egy Windows rendszerű virtuális gépet][windows-join], [konfigurálhatja a biztonságos LDAP][tutorial-ldaps]-t, és [konfigurálhatja a jelszó-kivonatok szinkronizálását][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment