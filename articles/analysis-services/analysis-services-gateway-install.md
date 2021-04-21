---
title: Helyszíni adatátjáró telepítése Azure Analysis Services | Microsoft Docs
description: Ismerje meg, hogyan telepíthet és konfigurálhat helyszíni adatátjárót helyszíni adatforrások csatlakoztatására egy Azure Analysis Services-kiszolgálóról.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43e5b64d06a6ec145876798b2e0da6499ab94bfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769228"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Helyszíni adatátjáró telepítése és konfigurálása

Helyszíni adatátjáróra akkor van szükség, ha Azure Analysis Services régióban található egy vagy több kiszolgáló helyszíni adatforráshoz csatlakozik.  Bár a telepített átjáró ugyanaz, mint más szolgáltatások, például az Power BI, Power Apps és Logic Apps, a Azure Analysis Services-hoz való telepítéskor néhány további lépést is végre kell majd végrehajtania. Ez a telepítési cikk csak a **Azure Analysis Services.** 

Az átjáróval való Azure Analysis Services kapcsolatos további információkért lásd: [Csatlakozás helyszíni adatforráshoz.](analysis-services-gateway.md) A speciális telepítési forgatókönyvekkel és az átjáróval kapcsolatos további információkért lásd a helyszíni adatátjárók [dokumentációját.](/data-integration/gateway/service-gateway-onprem)

## <a name="prerequisites"></a>Előfeltételek

**Minimumkövetelmények:**

* .NET-keretrendszer 4.5-ös verziója
* A Windows 8 64 bites verziója / Windows Server 2012 R2 (vagy újabb)

**Ajánlott:**

* 8 magos processzor
* 8 GB memória
* A Windows 8 64 bites verziója / Windows Server 2012 R2 (vagy újabb)

**Fontos szempontok:**

* A telepítés során, amikor regisztrálja az átjárót az Azure-ban, az előfizetés alapértelmezett régiója lesz kiválasztva. Másik előfizetést és régiót is választhat. Ha több régióban is vannak kiszolgálók, minden régióhoz telepítenie kell egy átjárót. 
* Az átjáró nem telepíthető tartományvezérlőre.
* Egy számítógépre csak egy átjáró telepíthető.
* Telepítse az átjárót egy olyan számítógépre, amely nem alvó üzemmódban marad.
* Ne telepítse az átjárót olyan számítógépre, amely csak vezeték nélküli kapcsolattal rendelkezik a hálózatához. A teljesítmény csökkenhet.
* Az átjáró telepítésekor a számítógépre bejelentkezett felhasználói fióknak szolgáltatásként kell bejelentkeznie. A telepítés befejezése után a helyszíni adatátjáró szolgáltatás az NT SERVICE\PBIEgwService fiókkal jelentkezik be szolgáltatásként. A telepítés során vagy a Szolgáltatások között a telepítés befejezése után másik fiók is meg lehet adni. Győződjön Csoportházirend, hogy a beállítások engedélyezik a telepítéskor bejelentkezett fiókot, és hogy a választott szolgáltatásfiók rendelkezik-e Bejelentkezés szolgáltatásként jogosultságokkal.
* Jelentkezzen be az Azure-ba egy Azure [](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) AD-fiókkal ugyanannak a bérlőnek a fiókjával, amelybe az átjárót regisztrálja. Az Azure B2B-fiókok (vendégfiókok) nem támogatottak az átjárók telepítése és regisztrálása során.
* Ha az adatforrások azure Virtual Network (VNet) vannak, konfigurálnia kell az [AlwaysUseGateway](analysis-services-vnet-gateway.md) kiszolgálótulajdonságokat.

## <a name="download"></a>Letöltés

 [Az átjáró letöltése](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Telepítés

1. Futtassa a telepítőt.

2. Válassza **a Helyszíni adatátjáró lehetőséget.**

   ![Válassza ezt:](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Válasszon ki egy helyet, fogadja el a feltételeket, majd kattintson a **Telepítés gombra.**

   ![Telepítési hely és licenc feltételei](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Jelentkezzen be az Azure-ba. A fióknak a bérlői fiók Azure Active Directory. Ezt a fiókot az átjáró rendszergazdája használja. Az Átjáró telepítése és regisztrálása nem támogatja az Azure B2B- (vendégfiókok) fiókokat.

   ![Bejelentkezés az Azure-ba](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Ha tartományi fiókkal jelentkezik be, az az Azure AD-beli szervezeti fiókjára lesz leképezve. A szervezeti fiók lesz az átjáró rendszergazdája.

## <a name="register"></a>Regisztráció

Ahhoz, hogy átjáró-erőforrást hoz létre az Azure-ban, regisztrálnia kell a telepített helyi példányt az átjáró felhőszolgáltatásával. 

1.  Válassza **az Új átjáró regisztrálása ezen a számítógépen lehetőséget.**

    ![Képernyőkép az Új átjáró regisztrálása ezen a számítógépen lehetőségről.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Adja meg az átjáró nevét és helyreállítási kulcsát. Alapértelmezés szerint az átjáró az előfizetés alapértelmezett régióját használja. Ha másik régiót kell választania, válassza a **Régió módosítása lehetőséget.**

    > [!IMPORTANT]
    > Mentse a helyreállítási kulcsot egy biztonságos helyre. A helyreállítási kulcs szükséges az átjárók átvételéhez, áttelepítéséhez vagy visszaállításához. 

   ![Regisztráció](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Azure Gateway-erőforrás létrehozása

Miután telepítette és regisztrálta az átjárót, létre kell hoznia egy átjáró-erőforrást az Azure-ban. Jelentkezzen be az Azure-ba ugyanazokkal a fiókkal, mint az átjáró regisztrálásakor.

1. A Azure Portal kattintson **az Erőforrás létrehozása** elemre, keressen rá a **Helyszíni adatátjáró** kifejezésre, majd kattintson a **Létrehozás elemre.**

   ![Átjáró-erőforrás létrehozása](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. A **Kapcsolatátjáró létrehozása lapon** adja meg a következő beállításokat:

   * **Név:** Adja meg az átjáró-erőforrás nevét. 

   * **Előfizetés:** Válassza ki az átjáró-erőforráshoz társítani kívánt Azure-előfizetést. 
   
     Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.

   * **Hely:** Válassza ki azt a régiót, ahol az átjárót regisztrálta.

   * **Telepítés neve:** Ha az átjáró telepítése még nincs bejelölve, válassza ki a számítógépre telepített és regisztrált átjárót. 

     Ha végzett, kattintson a **Létrehozás gombra.**

## <a name="connect-gateway-resource-to-server"></a>Átjáróerőforrás csatlakoztatása kiszolgálóhoz

> [!NOTE]
> A kiszolgálótól eltérő előfizetésben található átjáróerőforráshoz való csatlakozás nem támogatott a portálon, de a PowerShell használatával támogatott.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Analysis Services áttekintésében kattintson a **Helyszíni adatátjáró elemre.**

   ![Kiszolgáló csatlakoztatása átjáróhoz](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. A **Csatlakozás helyszíni adatátjáró kiválasztása** lehetőségben válassza ki az átjáró-erőforrást, majd kattintson a Kiválasztott átjáró **csatlakoztatása elemre.**

   ![Kiszolgáló csatlakoztatása átjáró-erőforráshoz](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Ha az átjáró nem jelenik meg a listában, akkor a kiszolgáló valószínűleg nem ugyanabban a régióban van, mint az átjáró regisztrálásakor megadott régió.

    Ha a kiszolgáló és az átjáró-erőforrás közötti kapcsolat sikeres, az állapot a Connected (Csatlakoztatva) **állapotot mutatja.**


    ![A kiszolgáló csatlakoztatása az átjáró-erőforrás sikerességéhez](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A [Get-AzResource használatával](/powershell/module/az.resources/get-azresource) szerezze be az átjáró ResourceID-ját. Ezután csatlakoztassa az átjáró-erőforrást egy meglévő vagy új kiszolgálóhoz a **-GatewayResourceID** megadásával a [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) vagy [a New-AzAnalysisServicesServer esetében.](/powershell/module/az.analysisservices/new-azanalysisservicesserver)

Az átjáró erőforrás-azonosítójának lekért száma:

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

Ennyi az egész. Ha portokat kell megnyitnia vagy hibaelhárítást kell eszközlnie, tekintse meg a [helyszíni adatátjárót.](analysis-services-gateway.md)

## <a name="next-steps"></a>Következő lépések

* [Az Analysis Services felügyelete](analysis-services-manage.md)   
* [Adatlekérdezés az Azure Analysis Servicesből](analysis-services-connect.md)   
* [Átjáró használata az adatforrásokhoz egy Azure-beli virtuális hálózaton](analysis-services-vnet-gateway.md)
