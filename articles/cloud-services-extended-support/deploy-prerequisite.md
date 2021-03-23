---
title: Az Azure Cloud Services üzembe helyezésének előfeltételei (bővített támogatás)
description: Az Azure Cloud Services üzembe helyezésének előfeltételei (bővített támogatás)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 21433e1a0441ef458dd5f8ea4b968211ef82cd46
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865604"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Az Azure Cloud Services üzembe helyezésének előfeltételei (bővített támogatás)

> [!IMPORTANT]
> A Cloud Services (bővített támogatás) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A sikeres Cloud Services (kiterjesztett támogatás) központi telepítésének ellenőrzéséhez tekintse át az alábbi lépéseket, és végezze el az egyes elemek telepítését az üzembe helyezések megkísérlése előtt. 

## <a name="register-the-cloudservices-feature"></a>A CloudServices funkció regisztrálása
Regisztrálja az előfizetéshez tartozó funkciót. A regisztráció elvégzése több percet is igénybe vehet. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

A regisztráció állapotát a következő paranccsal ellenőrizhető:  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Szükséges szolgáltatási konfigurációs (. cscfg) fájlok frissítései

### <a name="1-virtual-network"></a>1) Virtual Network
A Cloud Service (bővített támogatás) központi telepítéseknek virtuális hálózatban kell lenniük. A virtuális hálózat a [Azure Portal](../virtual-network/quick-create-portal.md), a [PowerShell](../virtual-network/quick-create-powershell.md), az [Azure CLI](../virtual-network/quick-create-cli.md) vagy az [ARM sablon](../virtual-network/quick-create-template.md)segítségével hozható létre. A virtuális hálózatot és az alhálózatokat is hivatkozni kell a szolgáltatás konfigurációjában (. cscfg) a [NetworkConfiguration](schema-cscfg-networkconfiguration.md) szakaszban. 

A felhőalapú szolgáltatással azonos erőforráscsoporthoz tartozó virtuális hálózatok esetében a szolgáltatás konfigurációs (. cscfg) fájljában csak a virtuális hálózat nevére lehet hivatkozni. Ha a virtuális hálózat és a felhőalapú szolgáltatás két különböző erőforráscsoporthoz van, akkor a virtuális hálózat teljes Azure Resource Manager AZONOSÍTÓját meg kell adni a szolgáltatás konfigurációs (. cscfg) fájljában.
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Azonos erőforráscsoporthoz tartozó Virtual Network
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Más erőforráscsoporthoz tartozó virtuális hálózat
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2.) távolítsa el a régi beépülő modulokat

Távolítsa el a távoli asztal régi beállításait a szolgáltatás konfigurációs (. cscfg) fájljából.  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
Távolítsa el a régi diagnosztikai beállításokat az egyes szerepkörökhöz a szolgáltatás konfigurációs (. cscfg) fájljában.

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>Szükséges szolgáltatási definíciós fájl (. csdef) frissítései

> [!NOTE]
> A Service Definition (. csdef) fájl változásaihoz a csomagfájl (. cspkg) újbóli létrehozása szükséges. A felhőalapú szolgáltatás legújabb beállításainak beszerzéséhez hozza létre és csomagolja ki a. cspkg bejegyzést a következő módosításokkal a. csdef fájlban

### <a name="1-virtual-machine-sizes"></a>1) a virtuális gépek méretei
A következő méretek elavultak a Azure Resource Managerban. Ha azonban továbbra is szeretné használni őket, frissítse a `vmsize` nevet a társított Azure Resource Manager elnevezési konvencióval.  

| Előző méret neve | Frissített méret neve | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Kicsi | Standard_A1 |
| Közepes | Standard_A2 | 
| Nagy | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Ilyen lehet például a következő: `<WorkerRole name="WorkerRole1" vmsize="Medium"` `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"` .
 
> [!NOTE]
> Az elérhető méretek listájának lekéréséhez tekintse meg az [erőforrás-SKU-lista](/rest/api/compute/resourceskus/list) lehetőséget, és alkalmazza a következő szűrőket: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) a régi távoli asztali bővítmények eltávolítása
A régi távoli asztali bővítményeket használó központi telepítéseknek el kell távolítaniuk a modulokat a szolgáltatás-definíciós (. csdef) fájlból és a kapcsolódó tanúsítványokból. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
A régi diagnosztikai beépülő modulokat használó központi telepítések esetében a szolgáltatás-definíciós (. csdef) fájl minden egyes szerepköre esetében el kell távolítani a beállításokat.

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

## <a name="key-vault-creation"></a>Key Vault létrehozása 

A Key Vault a Cloud Serviceshoz társított tanúsítványok (kiterjesztett támogatás) tárolására szolgál. Adja hozzá a tanúsítványokat a Key Vaulthoz, majd hivatkozzon a tanúsítvány ujjlenyomatai megfelelnek a szolgáltatás konfigurációs fájljában. Emellett engedélyeznie kell Key Vault "hozzáférési szabályzatokat" (a portálon) az "Azure Virtual Machines for Deployment" számára, hogy Cloud Services (kiterjesztett támogatás) erőforrás lekérheti a titkos kulcsként tárolt tanúsítványt Key Vault. Kulcstartót a [Azure Portal](../key-vault/general/quick-create-portal.md) vagy a [PowerShell](../key-vault/general/quick-create-powershell.md)használatával hozhat létre. A Key vaultot ugyanabban a régióban és előfizetésben kell létrehozni, mint a Cloud Service. További információ: [tanúsítványok használata az Azure Cloud Services (bővített támogatás)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- Látogasson el a [Cloud Services (bővített támogatás) minták tárházára](https://github.com/Azure-Samples/cloud-services-extended-support)
