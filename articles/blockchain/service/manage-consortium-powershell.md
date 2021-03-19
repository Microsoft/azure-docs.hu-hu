---
title: Az Azure Blockchain Service Consortium tagjainak kezelése – PowerShell
description: Ismerje meg, hogyan kezelheti az Azure Blockchain Service Consortium-tagokat Azure PowerShell használatával.
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: zeyadr
ms.openlocfilehash: d40e55f177bda9edb40383b6e2c61c32633cd005
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85211340"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>A konzorcium tagjainak kezelése az Azure Blockchain szolgáltatásban a PowerShell használatával

A PowerShell használatával kezelheti az Azure Blockchain szolgáltatás blockchain-konzorciumának tagjait. A rendszergazdai jogosultságokkal rendelkező tagok meghívhatják, hozzáadhatják, eltávolíthatják és módosíthatják a blockchain Consortium összes résztvevőjének szerepkörét. A felhasználói jogosultságokkal rendelkező tagok megtekinthetik a blockchain konzorcium összes résztvevőjét, és megváltoztathatják a tagok megjelenítendő nevét.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy blockchain tagot a [Azure Portal](create-member.md)használatával.
* A konzorciumokkal, tagokkal és csomópontokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

A Azure Cloud Shell egy ingyenes interaktív felület, amellyel a cikkben ismertetett lépéseket futtathatja. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell egy külön böngésző lapon is megnyithatja a [shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be Cloud Shellba, majd válassza az **ENTER billentyűt** a futtatásához.

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Telepítse a Microsoft.AzureBlockchainService.ConsortiumManagement.PS csomagot a PowerShell-galéria.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Az információs beállítások megadása

A parancsmagok végrehajtásával kapcsolatos további információkat az információs preferencia változó beállításával kaphat. Alapértelmezés szerint a *$InformationPreference* a *SilentlyContinue* értékre van állítva.

A parancsmagok részletes információit a következő módon állíthatja be a PowerShellben:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3-kapcsolat létrehozása

A konzorcium tagjainak kezeléséhez hozzon létre egy Web3-kapcsolatot a Blockchain-szolgáltatási tag végpontjának. Ezzel a parancsfájllal globális változókat állíthat be a konzorcium-felügyeleti parancsmagok meghívásához.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Cserélje le a- *\<Member account password\>* t a tag fiók jelszavára, amelyet a tag létrehozásakor használt.

A Azure Portal többi értékének megkeresése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen az alapértelmezett Blockchain-szolgáltatási tag **– Áttekintés** lapra.

    ![Tag áttekintése](./media/manage-consortium-powershell/member-overview.png)

    Cserélje le a *\<Member account\>* és *\<RootContract address\>* értékeket a portálon található értékekre.

1. A végpont címe mezőben válassza a **tranzakciós csomópontok** lehetőséget, majd válassza ki az **alapértelmezett tranzakció csomópontot**. Az alapértelmezett csomópont neve megegyezik a blockchain taggal.
1. Válassza a **kapcsolatok karakterláncok** lehetőséget.

    ![Kapcsolati sztringek](./media/manage-consortium-powershell/connection-strings.png)

    Cserélje le *\<Endpoint address\>* a értéket a **https értékre (1. hozzáférési kulcs)** vagy https-re **(2. hozzáférési kulcs)**.

## <a name="manage-the-network-and-smart-contracts"></a>A hálózat és az intelligens szerződések kezelése

A hálózati és az intelligens szerződési parancsmagokkal kapcsolatot létesíthet a blockchain-végpontnak a konzorciumok felügyeletével kapcsolatos intelligens szerződésekkel.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Ezzel a parancsmaggal csatlakozhat a konzorciumi felügyelet intelligens szerződésekhez. Ezek a szerződések a tagok a konzorciumon belüli felügyeletére és betartatására szolgálnak.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| RootContractAddress | A konzorcium-felügyeleti intelligens szerződések fő szerződési címe | Yes |
| Web3Client | New-Web3Connectionból beszerzett Web3Client objektum | Yes |

#### <a name="example"></a>Példa

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Ezzel a parancsmaggal hozhat létre egy objektumot, amely a távoli csomópont felügyeleti fiókjához tartozó információkat tárolja.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain-tag fiókjának címe | Yes |
| ManagedAccountPassword | Fiók címe jelszó | Yes |

#### <a name="example"></a>Példa

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Ezzel a parancsmaggal kapcsolatot létesíthet egy tranzakciós csomópont RPC-végpontján.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain-tag végpontjának címe | Yes |

#### <a name="example"></a>Példa

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>A konzorcium tagjainak kezelése

A konzorcium tagjai a konzorciumon belül kezelhetők. Az elérhető műveletek a konzorciumi szerepkörtől függenek.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Ezzel a parancsmaggal szerezheti be a tagok adatait, vagy listázhatja a konzorcium tagjait.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Name | Annak a Blockchain-szolgáltatásnak a neve, amelyről adatokat szeretne lekérdezni. Név megadásakor a rendszer a tag adatait adja vissza. Egy név kihagyása esetén a a konzorcium összes tagjának listáját adja vissza. | No |
| Tagok | Import-ConsortiumManagementContractsból beszerzett tagok objektum | Yes |
| Web3Client | New-Web3Connectionból beszerzett Web3Client objektum | Yes |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection változó beállításához.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Példa kimenetre

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Ezzel a parancsmaggal eltávolíthat egy blockchain tagot.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Name | Eltávolítandó tag neve | Yes |
| Tagok | Import-ConsortiumManagementContractsból beszerzett tagok objektum | Yes |
| Web3Account | Import-Web3Accountból beszerzett Web3Account objektum | Yes |
| Web3Client | New-Web3Connectionból beszerzett Web3Client objektum | Yes |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Ezzel a parancsmaggal beállíthatja a blockchain, beleértve a megjelenítendő nevet és a konzorciumi szerepkört.

A konzorcium-rendszergazdák minden tag számára megadhatják a **DisplayName** és a **szerepkört** . A felhasználói szerepkörrel rendelkező konzorcium-tag csak a saját tag megjelenítendő nevét módosíthatja.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Name | A blockchain-tag neve | Yes |
| DisplayName | Új megjelenítendő név | No |
| AccountAddress | Fiók címe | No |
| Tagok | Import-ConsortiumManagementContractsból beszerzett tagok objektum | Yes |
| Web3Account | Import-Web3Accountból beszerzett Web3Account objektum | Yes |
| Web3Client |  New-Web3Connectionból beszerzett Web3Client objektum| Yes |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>A konzorcium tagjai meghívásának kezelése

A konzorciumi tagok meghívójának kezelési parancsmagokkal felügyelheti a konzorcium tagjainak meghívóit. Az elérhető műveletek a konzorciumi szerepkörtől függenek.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Ezzel a parancsmaggal meghívhatja az új tagokat a konzorciumba.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívni kívánt tag Azure-előfizetési azonosítója | Yes |
| Szerepkör | A konzorcium szerepköre. Az értékek lehetnek rendszergazda vagy felhasználó. A rendszergazda a konzorcium rendszergazdai szerepköre. A felhasználó a konzorciumi tag szerepkör. | Yes |
| Tagok | Import-ConsortiumManagementContractsból beszerzett tagok objektum | Yes |
| Web3Account | Import-Web3Accountból beszerzett Web3Account objektum | Yes |
| Web3Client | New-Web3Connectionból beszerzett Web3Client objektum | Yes |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Ezzel a parancsmaggal lekérdezheti vagy listázhatja a konzorciumi tag Meghívási állapotát.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívni kívánt tag Azure-előfizetés azonosítója. Ha az előfizetés-azonosító van megadva, az előfizetés-azonosító meghívásának részleteit adja vissza. Ha az előfizetés-azonosító ki van hagyva, az összes tag meghívásának listáját adja vissza. | No |
| Tagok | Import-ConsortiumManagementContractsból beszerzett tagok objektum | Yes |
| Web3Client | New-Web3Connectionból beszerzett Web3Client objektum | Yes |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection változó beállításához.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Példa kimenetre

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Ezzel a parancsmaggal visszavonhatja a konzorcium tagjainak meghívóját.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A visszavonni kívánt tag Azure-előfizetési azonosítója | Yes |
| Tagok | Import-ConsortiumManagementContractsból beszerzett tagok objektum | Yes |
| Web3Account | Import-Web3Accountból beszerzett Web3Account objektum | Yes |
| Web3Client | New-Web3Connectionból beszerzett Web3Client objektum | Yes |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Ezzel a parancsmaggal állíthatja be egy meglévő meghívás **szerepkörét** . Csak a konzorcium rendszergazdái módosíthatják a meghívókat.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívni kívánt tag Azure-előfizetési azonosítója | Yes |
| Szerepkör | Új konzorciumi szerepkör a meghívóhoz. Az értékek lehetnek **felhasználó** vagy **rendszergazda**. | Yes |
| Tagok |  Import-ConsortiumManagementContractsból beszerzett tagok objektum | Yes |
| Web3Account | Import-Web3Accountból beszerzett Web3Account objektum | Yes |
| Web3Client | New-Web3Connectionból beszerzett Web3Client objektum | Yes |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Következő lépések

A konzorciumokkal, tagokkal és csomópontokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md)
