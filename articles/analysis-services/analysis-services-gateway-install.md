---
title: Helyszíni adatátjáró telepítése Azure Analysis Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan telepíthet és konfigurálhat helyszíni adatátjárót egy Azure Analysis Services-kiszolgálóról a helyszíni adatforrásokhoz való kapcsolódáshoz.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 64bd9e4a4cf78d2628e946af30c2d290ff002cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081144"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Helyszíni adatátjáró telepítése és konfigurálása

Egy helyszíni adatátjáróra akkor van szükség, ha egy vagy több Azure Analysis Services-kiszolgáló ugyanabban a régióban csatlakozik a helyszíni adatforrásokhoz.  Míg a telepített átjáró ugyanaz, mint az egyéb szolgáltatások, például a Power BI, a Power apps és a Logic Apps, a Azure Analysis Services telepítésekor néhány további lépést is végre kell hajtania. Ez a telepítési cikk a **Azure Analysis Servicesra** vonatkozik. 

Ha többet szeretne megtudni arról, hogy a Azure Analysis Services hogyan működik az átjáróval, tekintse [meg a csatlakozás helyszíni adatforrásokhoz](analysis-services-gateway.md)című témakört. Ha többet szeretne megtudni a speciális telepítési forgatókönyvekről és az átjáróról, tekintse [meg a helyszíni adatátjárók dokumentációját](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Előfeltételek

**Minimális követelmények:**

* .NET-keretrendszer 4.5-ös verziója
* a Windows 8/Windows Server 2012 R2 64 bites verziója (vagy újabb)

**Ajánlott**

* 8 magos processzor
* 8 GB memória
* a Windows 8/Windows Server 2012 R2 64 bites verziója (vagy újabb)

**Fontos szempontok:**

* A telepítés során, amikor az átjárót az Azure-ban regisztrálja, az előfizetéshez tartozó alapértelmezett régió van kiválasztva. Választhat másik előfizetést és régiót is. Ha több régióban is vannak kiszolgálók, mindegyik régióhoz telepítenie kell egy átjárót. 
* Az átjáró nem telepíthető tartományvezérlőre.
* Egyetlen számítógépre egyszerre csak egy átjáró telepíthető.
* Telepítse az átjárót egy olyan számítógépre, amely továbbra is be van kapcsolva, és nem az alvó állapotba lép.
* Ne telepítse az átjárót egy olyan számítógépre, amelyen vezeték nélküli kapcsolat van a hálózattal. Csökkentheti a teljesítményt.
* Az átjáró telepítésekor a számítógépre bejelentkezett felhasználói fióknak szolgáltatás-jogosultságként kell bejelentkeznie. Ha a telepítés befejeződött, a helyszíni adatátjáró szolgáltatás az NT SERVICE\PBIEgwService fiókot használja a szolgáltatásként való bejelentkezéshez. A telepítés befejezése után egy másik fiók is megadható a telepítés során vagy a szolgáltatásokban. Győződjön meg arról, Csoportházirend beállítások lehetővé teszik, hogy az Ön által a telepítéskor bejelentkezett fiók és a kiválasztott szolgáltatásfiók szolgáltatásként történő bejelentkezéssel rendelkezzen.
* Jelentkezzen be az Azure-ba egy Azure AD-fiókkal ugyanahhoz a [bérlőhöz](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) , amelyhez az átjárót regisztrálja. Az Azure B2B-(vendég-) fiókok nem támogatottak átjáró telepítésekor és regisztrálása esetén.
* Ha az adatforrások egy Azure Virtual Networkon (VNet) vannak, akkor konfigurálnia kell a [AlwaysUseGateway](analysis-services-vnet-gateway.md) -kiszolgáló tulajdonságát.

## <a name="download"></a>Letöltés

 [Az átjáró letöltése](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Telepítés

1. Futtassa a telepítőt.

2. Válassza **a helyszíni adatátjáró** lehetőséget.

   ![Válassza ezt:](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Válasszon ki egy helyet, fogadja el a feltételeket, majd kattintson a **telepítés** gombra.

   ![Telepítési hely és licencfeltételek](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Jelentkezzen be az Azure-ba. A fióknak a bérlő Azure Active Directoryjában kell lennie. Ezt a fiókot használja a rendszer az átjáró rendszergazdájának. Az Azure B2B (Guest) fiókok nem támogatottak az átjáró telepítésekor és regisztrálása során.

   ![Bejelentkezés az Azure-ba](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Ha tartományi fiókkal jelentkezik be, az Azure AD-ben a szervezeti fiókjához van hozzárendelve. A szervezeti fiókját az átjáró rendszergazdája használja.

## <a name="register"></a>Regisztráció

Az Azure-beli átjáró-erőforrások létrehozásához regisztrálnia kell az átjáró Cloud Service-ben telepített helyi példányt. 

1.  Válassza **az új átjáró regisztrálása ezen a számítógépen** lehetőséget.

    ![Képernyőfelvétel: az új átjáró regisztrálása ezen a számítógépen beállítás.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Adja meg az átjáró nevét és helyreállítási kulcsát. Alapértelmezés szerint az átjáró az előfizetés alapértelmezett régióját használja. Ha másik régiót kell kiválasztania, válassza a **régió módosítása** lehetőséget.

    > [!IMPORTANT]
    > Mentse a helyreállítási kulcsot biztonságos helyen. A helyreállítási kulcs szükséges az átjáró átvételéhez, átmigrálához vagy visszaállításához. 

   ![Regisztráció](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Azure Gateway-erőforrás létrehozása

Az átjáró telepítése és regisztrálása után létre kell hoznia egy átjáró-erőforrást az Azure-ban. Jelentkezzen be az Azure-ba ugyanazzal a fiókkal, amelyet az átjáró regisztrálásakor használt.

1. Azure Portal kattintson az **erőforrás létrehozása** elemre, majd keresse **meg a helyszíni adatátjárót**, majd kattintson a **Létrehozás** gombra.

   ![Átjáró erőforrásának létrehozása](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Adja meg a következő beállításokat a **kapcsolatok átjárójának létrehozása** területen:

   * **Név**: adja meg az átjáró erőforrásának nevét. 

   * **Előfizetés**: válassza ki az átjáró erőforrásához társítandó Azure-előfizetést. 
   
     Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.

   * **Hely**: válassza ki azt a régiót, amelyen az átjárót regisztrálta.

   * **Telepítési név**: Ha az átjáró telepítése még nincs kiválasztva, válassza ki a számítógépen telepített átjárót, és regisztrálja azt. 

     Ha elkészült, kattintson a **Létrehozás** gombra.

## <a name="connect-gateway-resource-to-server"></a>Átjáró erőforrásának összekötése a kiszolgálóval

> [!NOTE]
> Ha egy másik előfizetésben lévő átjáró-erőforráshoz csatlakozik a-kiszolgálóról, a portálon nem támogatott, de a PowerShell használatával támogatott.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Analysis Services-kiszolgáló áttekintésében kattintson **a helyszíni adatátjáró** elemre.

   ![Kiszolgáló összekötése az átjáróval](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. A **kapcsolódáshoz válasszon egy helyszíni Adatátjárót**, válassza ki az átjáró-erőforrást, majd kattintson a **kijelölt átjáró összekapcsolása** elemre.

   ![Kiszolgáló és átjáró erőforrásának összekötése](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Ha az átjáró nem jelenik meg a listában, akkor a kiszolgáló valószínűleg nem ugyanabban a régióban található, mint az átjáró regisztrálásakor megadott régió.

    Ha a kiszolgáló és az átjáró-erőforrás közötti kapcsolat sikeresen befejeződött, az állapot fog megjelenni.


    ![A kiszolgáló és az átjáró erőforrásának összekötése sikeres](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A [Get-AzResource](/powershell/module/az.resources/get-azresource) használatával szerezze be az átjáró ResourceId. Ezután a **-GatewayResourceID** a [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) vagy a [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)érték megadásával kapcsolja össze az átjáró erőforrását egy meglévő vagy egy új kiszolgálóval.

Az átjáró erőforrás-AZONOSÍTÓjának lekérése:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Meglévő kiszolgáló konfigurálása:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

Ennyi az egész. Ha portok megnyitására vagy hibaelhárításra van szüksége, mindenképpen tekintse [meg a helyszíni adatátjárót](analysis-services-gateway.md).

## <a name="next-steps"></a>Következő lépések

* [Az Analysis Services felügyelete](analysis-services-manage.md)   
* [Adatlekérdezés az Azure Analysis Servicesből](analysis-services-connect.md)   
* [Átjáró használata az adatforrásokhoz egy Azure-beli virtuális hálózaton](analysis-services-vnet-gateway.md)