---
title: Azure Cloud Services (klasszikus) migrálása az Azure Cloud Servicesba (bővített támogatás)
description: A Cloud Services (klasszikus) és a Cloud Service közötti áttelepítés áttekintése (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286913"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Azure Cloud Services (klasszikus) migrálása az Azure Cloud Servicesba (bővített támogatás)

Ez a cikk áttekintést nyújt a platform által támogatott áttelepítési eszközről, valamint arról, hogyan használható az [azure Cloud Services (klasszikus)](../cloud-services/cloud-services-choose-me.md) az [Azure Cloud Services (bővített támogatás)](overview.md)való áttelepítésére.

Az áttelepítési eszköz ugyanazokat az API-kat használja, és ugyanazzal a tapasztalattal rendelkezik, mint a [virtuális gép (klasszikus) áttelepítése](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview). 

> [!IMPORTANT]
> Az áttelepítési eszköz jelenleg nyilvános előzetes verzióban érhető el a Cloud Services (klasszikus) rendszerről a Cloud Servicesra (kiterjesztett támogatás). Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha segítségre van szüksége az áttelepítéssel kapcsolatban, tekintse meg a következő forrásokat: 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): Microsoft és közösségi támogatás az áttelepítéshez.
- [Azure-áttelepítési támogatás](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): az áttelepítés során technikai segítséget nyújtó dedikált támogatási csoport. A technikai támogatás nélküli ügyfelek igénybe vehetik az [ingyenes támogatási képességet](https://aka.ms/cs-migration-errors) , amely kifejezetten ehhez az áttelepítéshez biztosított.
- Ha a vállalata vagy szervezete a Microsofttal vagy a Microsoft képviselőivel, például a Cloud Solution-építészekkel vagy a technikai menedzserekkel együttműködik, a Migrálás további erőforrásaihoz juthat hozzájuk.
- Fejezze be [ezt a kérdőívet](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) , és küldjön visszajelzést, vagy tegyen fel problémákat a Cloud Services (bővített támogatás) termékért felelős csapat számára. 

## <a name="migration-benefits"></a>Áttelepítési előnyök
A platform által támogatott áttelepítés a következő főbb előnyöket nyújtja:
- Az áttelepítés teljes mértékben a platform, a teljes üzembe helyezés és a kapcsolódó erőforrások Azure Resource Managerba való áthelyezésével történik.
- Nincs leállás.
- Könnyű és gyors áttelepítés más áttelepítési útvonalakhoz képest a manuális feladatok minimalizálása révén. 
- A Migrálás részeként megőrzi Cloud Services IP-címet és a DNS-címkét. 

Egyéb előnyök esetén, és miért érdemes áttelepítenie a következőt: [Cloud Services (bővített támogatás)](overview.md) és [Azure Resource Manager](../azure-resource-manager/management/overview.md). 

## <a name="setup-access-for-migration"></a>Hozzáférés beállítása áttelepítéshez

Az áttelepítés végrehajtásához hozzá kell adni az előfizetés és a szükséges szolgáltatók regisztrálásához. 

1. Jelentkezzen be az Azure Portalra.
3. A központi menüben válassza az előfizetés lehetőséget. Ha nem látja, válassza a minden szolgáltatás lehetőséget.
3. Keresse meg a megfelelő előfizetési bejegyzést, majd tekintse meg a saját SZEREPKÖR mezőt. A rendszergazdák számára az értéknek a fiók rendszergazdája kell lennie. Ha nem tud felvenni egy társ-rendszergazda szerepkört, forduljon a szolgáltatás rendszergazdájához vagy a társ-rendszergazdához, és kérje a hozzáadást.

4. Előfizetés regisztrálása a Microsoft. ClassicInfrastructureMigrate névtérhez a [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), a [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) vagy a [parancssori](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) felület használatával

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Előfizetés regisztrálása a Cloud Services Migration Preview szolgáltatáshoz a [portál](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), a [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) vagy a [parancssori](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) felület használatával

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. A regisztráció állapotának ellenőrzését. A regisztráció több percet is igénybe vehet. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Miben különbözik az áttelepítési Cloud Services (klasszikus) Virtual Machines (klasszikus)?
Az Azure Service Manager két különböző számítási terméket, az [azure Virtual Machines (klasszikus)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) és az [Azure Cloud Services (klasszikus)](../cloud-services/cloud-services-choose-me.md) vagy a webes/feldolgozói szerepköröket támogatja. A két termék a felhőalapú szolgáltatásban található központi telepítési típustól függően eltérő. Az Azure Cloud Services (klasszikus) olyan felhőalapú szolgáltatást használ, amely webes/feldolgozói szerepkörökkel üzemelő üzembe helyezéseket tartalmaz. Az Azure Virtual Machines (klasszikus) olyan felhőalapú szolgáltatást használ, amely IaaS virtuális gépekkel üzemelő példányokat tartalmaz.

A támogatott forgatókönyvek listája különbözik a Cloud Services (klasszikus) és a Virtual Machines (klasszikus) között a központi telepítési típusok különbségei miatt.

## <a name="migration-steps"></a>A migrálás lépései
 
Az ügyfelek áttelepíthetik Cloud Services (klasszikus) telepítéseket a Virtual Machines (klasszikus) áttelepítéséhez használt négy művelettel. 

1. **Áttelepítés ellenőrzése** – ellenőrzi, hogy az áttelepítést nem fogja-e megakadályozni közös, nem támogatott forgatókönyvek.
2. **Áttelepítés előkészítése** – duplikálja az erőforrás-metaadatokat Azure Resource Manager. Minden erőforrás zárolva van a létrehozási/frissítési/törlési műveletekhez, hogy az erőforrás-metaadatok szinkronban legyenek az Azure Kiszolgálókezelő és a Azure Resource Manager között. Minden olvasási művelet a Cloud Services (klasszikus) és a Cloud Services (bővített támogatás) API-k használatával fog működni.
3. **Áttelepítés megszakítása** – eltávolítja az erőforrás-metaadatokat a Azure Resource Managerból. Az összes erőforrás zárolásának feloldása a létrehozási/frissítési/törlési műveletekhez.
4. **Áttelepítés elkövetése** – eltávolítja az erőforrás-metaadatokat az Azure Service Managerból. Feloldja az erőforrást a létrehozási/frissítési/törlési műveletekhez. A megszakítás már nem engedélyezett a véglegesítés megkísérlése után.

>[!NOTE]
> Az előkészítés, a megszakítás és a végrehajtás idempotens, ezért ha nem sikerül, próbálkozzon újra a probléma megoldásával.

:::image type="content" source="media/in-place-migration-1.png" alt-text="A kép az áttelepítéshez kapcsolódó lépések diagramját mutatja.":::

További információ: [a IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Managerra](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>A Cloud Services (klasszikus) szolgáltatáshoz kapcsolódó áttelepítéshez rendelkezésre álló támogatott erőforrások és szolgáltatások
-   Storage-fiókok
-   Virtuális hálózatok
-   Network Security Groups (Hálózati biztonsági csoportok)
-   Fenntartott nyilvános IP-címek
-   Végpontok Access Control listája
-   Felhasználó által megadott útvonalak
-   Belső terheléselosztó
-   Tanúsítvány áttelepítése a Key vaultba
-   Beépülő modulok és bővítmény (XML és JSON-alapú)
-   Indításkor/leállítási feladatok esetén
-   Üzembe helyezés gyorsított hálózatkezeléssel
-   Egy vagy több szerepkört használó központi telepítések
-   Alapszintű Load Balancer
-   Bemenet, példány bemenete, belső végpontok
-   Dinamikus nyilvános IP-címek
-   DNS-név 
-   Hálózati forgalmi szabályok
-   Hypernet virtuális hálózat

## <a name="supported-configurations--migration-scenarios"></a>Támogatott konfigurációk/áttelepítési forgatókönyvek
Ezek a leggyakoribb forgatókönyvek az erőforrások, funkciók és Cloud Services kombinációit érintik. Ez a lista nem teljes.

| Szolgáltatás | Konfiguráció | Megjegyzések | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Azure Active Directory tartományi szolgáltatásokat tartalmazó virtuális hálózatok. | A Cloud Service-telepítést és az Azure AD tartományi szolgáltatásokat egyaránt tartalmazó virtuális hálózat támogatott. Az ügyfélnek először külön kell áttelepítenie az Azure AD tartományi szolgáltatásokat, majd a virtuális hálózatot csak a Cloud Service üzembe helyezésével kell áttelepítenie |
| Felhőszolgáltatás | Felhőalapú szolgáltatás csak egyetlen tárolóhelyen üzemelő példányokkal. | A gyártási vagy előkészítési tárolóhelyet tartalmazó Cloud Services is áttelepíthetők |
| Felhőszolgáltatás | Nem nyilvánosan látható virtuális hálózatban üzemelő példány (alapértelmezett virtuális hálózati telepítés) | A felhőalapú szolgáltatás lehet egy nyilvánosan látható virtuális hálózatban, egy rejtett virtuális hálózaton, vagy egyetlen virtuális hálózatban sem.  A rejtett virtuális hálózatokon és a nyilvánosan látható virtuális hálózatokban való Cloud Services a Migrálás támogatott. Az ügyfél a validate API használatával állapíthatja meg, hogy egy központi telepítés egy alapértelmezett virtuális hálózaton belül van-e, vagy sem, és így határozza meg, hogy áttelepíthető-e. |
|Felhőszolgáltatás | XML-bővítmények (BGInfo, Visual Studio Debugger, web Deploy és távoli hibakeresés). | Az összes XML-bővítmény támogatott az áttelepítéshez 
| Virtual Network | Több Cloud Servicest tartalmazó virtuális hálózat. | A virtuális hálózat több felhőalapú szolgáltatást is tartalmaz, amelyek migrálása támogatott. A virtuális hálózat és a benne található összes Cloud Services együtt lesz áttelepítve a Azure Resource Managerra. |
| Virtual Network | A portálon keresztül létrehozott virtuális hálózatok áttelepítése (a. cscfg fájl "csoport erőforrás-csoport-név VNet" használatával szükséges)  | A Migrálás részeként a virtuális hálózat neve a cscfg-ben a virtuális hálózat Azure Resource Manager-AZONOSÍTÓjának használatára lesz módosítva. (előfizetés/előfizetés-azonosító/erőforrás-csoport/erőforrás-csoport-név/erőforrás/vnet-név) <br><br>Ha a telepítést az áttelepítés után szeretné kezelni, frissítse a. cscfg fájl helyi példányát, hogy a virtuális hálózat neve helyett a Azure Resource Manager ID-t használja. <br><br>A régi elnevezési sémát használó. cscfg-fájlok nem fogják átadni az érvényesítést. 
| Virtual Network | Központi telepítés áttelepítése eltérő alhálózaton lévő szerepkörökkel. | A különböző alhálózatokban lévő különböző szerepkörökkel rendelkező felhőalapú szolgáltatások áttelepítése támogatott. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Az áttelepítéshez nem elérhető erőforrások és szolgáltatások
Ezek az erőforrások, funkciók és Cloud Services kombinációit érintő leggyakoribb forgatókönyvek. Ez a lista nem teljes. 

| Erőforrás | Következő lépések/munka – körül | 
|---|---|
| Automatikus skálázási szabályok | Az áttelepítés áthalad, de a szabályok el lesznek dobva. [Hozza létre újra a szabályokat](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) a Cloud Services áttelepítése után (kiterjesztett támogatás). | 
| Riasztások | Az áttelepítés áthalad, de a riasztások el lesznek dobva. [Hozza létre újra a szabályokat](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) a Cloud Services áttelepítése után (kiterjesztett támogatás). | 
| VPN Gateway | A Migrálás megkezdése előtt távolítsa el a VPN Gateway, majd hozza létre újra a VPN Gateway az áttelepítés befejeződése után. | 
| Express Route Gateway (ugyanabban az előfizetésben, mint Virtual Network csak) | Távolítsa el az Express Route-átjárót az áttelepítés megkezdése előtt, majd hozza létre újra az átjárót az áttelepítés befejeződése után. | 
| Kvóta  | A kvóta nem települ át. A Migrálás megkezdése előtt [igényeljen új kvótát](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) Azure Resource Manager az érvényesítés sikeres végrehajtásához. | 
| Affinitáscsoportok | Nem támogatott. Távolítsa el az összes affinitási csoportot az áttelepítés előtt.  | 
| Virtuális [hálózatokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) használó virtuális hálózatok| Egy másik virtuális hálózathoz tartozó virtuális hálózat áttelepítése előtt törölje a társítást, telepítse át a virtuális hálózatot a Resource Managerbe, és hozza létre újból a társítást. Ez az architektúrától függően állásidőt eredményezhet. | 
| App Service környezeteket tartalmazó virtuális hálózatok | Nem támogatott | 
| HDInsight szolgáltatásokat tartalmazó virtuális hálózatok | Nem támogatott. 
| Azure API Management üzemelő példányokat tartalmazó virtuális hálózatok | Nem támogatott. <br><br> A virtuális hálózat áttelepítéséhez módosítsa a API Management üzemelő példány virtuális hálózatát. Ez nem leállási művelet. | 
| Klasszikus expressz Route-áramkörök | Nem támogatott. <br><br>Ezeket az áramköröket át kell telepíteni Azure Resource Manager a Pásti-áttelepítés megkezdése előtt. További információ: [ExpressRoute-áramkörök áthelyezése a Klasszikusból a Resource Manager](../expressroute/expressroute-howto-move-arm.md)-alapú üzemi modellbe. |  
| Szerepköralapú hozzáférés-vezérlés | Az áttelepítés után az erőforrás URI-JÁT az `Microsoft.ClassicCompute` `Microsoft.Compute` áttelepítést követően frissíteni kell az RBAC-szabályzatokból. | 
| Application Gateway | Nem támogatott. <br><br> Az áttelepítés megkezdése előtt távolítsa el a Application Gateway, majd hozza létre újra a Application Gateway az áttelepítés befejezése után Azure Resource Manager | 

## <a name="unsupported-configurations--migration-scenarios"></a>Nem támogatott konfigurációk/áttelepítési forgatókönyvek

| Konfiguráció/forgatókönyv  | Következő lépések/munka – körül | 
|---|---|
| Nem virtuális hálózatban lévő régebbi központi telepítések áttelepítése | Egyes felhőalapú szolgáltatások nem virtuális hálózatban üzemelő példányai migrálása nem támogatott. <br><br> 1. az API ellenőrzése paranccsal ellenőrizze, hogy a központi telepítés jogosult-e az áttelepítésre. <br> 2. ha jogosult, a központi telepítések a "DefaultRdfeVnet" előtaggal rendelkező virtuális hálózatban Azure Resource Managerra kerülnek. | 
| Üzemi és előkészítési tárolóhelyet tartalmazó központi telepítések áttelepítése dinamikus IP-címek használatával | A két tárolóhelyes felhőalapú szolgáltatás áttelepítése az átmeneti tárolóhely törlését igényli. Az előkészítési pont törlése után telepítse át az üzemi tárolóhelyet független felhőalapú szolgáltatásként (kiterjesztett támogatás) a Azure Resource Manager. Ezután telepítse újra az átmeneti környezetet új felhőalapú szolgáltatásként (kiterjesztett támogatás), és végezze el az első cserét. | 
| Üzemi és előkészítési tárolóhelyet tartalmazó központi telepítések áttelepítése Fenntartott IP címek használatával | Nem támogatott. | 
| Üzemi és előkészítési központi telepítés áttelepítése különböző virtuális hálózatokban|A két tárolóhelyes felhőalapú szolgáltatás áttelepítése az átmeneti tárolóhely törlését igényli. Az előkészítési pont törlése után telepítse át az üzemi tárolóhelyet független felhőalapú szolgáltatásként (kiterjesztett támogatás) a Azure Resource Manager. Ezután egy új Cloud Services (kiterjesztett támogatás) üzemelő példány társítható az áttelepített üzemelő példányhoz, és engedélyezhető a cserélhető tulajdonság. A régi előkészítési tárolóhely központi telepítésének fájljai újra felhasználhatók az új cserélhető üzembe helyezés létrehozásához. | 
| Üres felhőalapú szolgáltatás áttelepítése (felhőalapú szolgáltatás központi telepítés nélkül) | Nem támogatott. | 
| A távoli asztali beépülő modult és a távoli asztali bővítményeket tartalmazó üzemelő példány áttelepítése | 1. lehetőség: távolítsa el a távoli asztali beépülő modult az áttelepítés előtt. Ehhez a központi telepítési fájloknak módosításokat kell megadnia. A Migrálás ezután megtörténik. <br><br> 2. lehetőség: távolítsa el a távoli asztali bővítményt, és telepítse át az üzemelő példányt. Áttelepítés után távolítsa el a beépülő modult, és telepítse a bővítményt. Ehhez a központi telepítési fájloknak módosításokat kell megadnia. <br><br> Az áttelepítés előtt távolítsa el a beépülő modult és a bővítményt. A [beépülő modulok használata nem ajánlott](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) Cloud Services esetén (kiterjesztett támogatás).| 
| A Pásti és a IaaS üzemelő példányokkal rendelkező virtuális hálózatok |Nem támogatott <br><br> Helyezze át a Pásti vagy a IaaS üzemelő példányait egy másik virtuális hálózatba. Ez az állásidőt eredményezi. | 
A felhőalapú szolgáltatások telepítése örökölt szerepkör-méretekkel (például kis-vagy ExtraLarge). | Az áttelepítés befejeződik, de a szerepkörök méretei a modern szerepkörök méretének használatával frissülnek. Nincs változás a Cost vagy az SKU tulajdonságaiban, és a virtuális gép nem lesz újraindítva ehhez a változáshoz. Frissítse az összes üzembe helyezési összetevőt az új modern szerepkör-méretekre való hivatkozáshoz. További információ: [elérhető VM-méretek](available-sizes.md)|
| A felhőalapú szolgáltatás áttelepítése különböző virtuális hálózatra | Nem támogatott <br><br> 1. áttelepítés előtt helyezze át az üzemelő példányt egy másik klasszikus virtuális hálózatra. Ez az állásidőt eredményezi. <br> 2. telepítse át az új virtuális hálózatot Azure Resource Managerra. <br><br> Vagy <br><br> 1. telepítse át a virtuális hálózatot Azure Resource Manager <br>2. Helyezze át a Cloud Service-t egy új virtuális hálózatra. Ez az állásidőt eredményezi. | 
| Felhőalapú szolgáltatás egy virtuális hálózaton, de nincs hozzárendelve explicit alhálózat | Nem támogatott. A megoldás azt jelenti, hogy a szerepkört egy alhálózatba helyezi át, amelyhez szerepkör-újraindítás szükséges (leállás) | 


## <a name="post-migration-changes"></a>Áttelepítés utáni módosítások
A Cloud Services (klasszikus) üzemelő példány a Cloud Service (bővített támogatás) üzembe helyezésére lett konvertálva. További részletekért tekintse meg a [Cloud Services (bővített támogatás) dokumentációját](deploy-prerequisite.md) .  

### <a name="changes-to-deployment-files"></a>A központi telepítési fájlok módosításai 

Kisebb módosítások történnek az ügyfél. csdef és. cscfg fájljában, hogy a telepítési fájlok megfeleljenek a Azure Resource Manager és Cloud Services (kiterjesztett támogatás) követelményeinek. Az áttelepítés után lekéri az új központi telepítési fájlokat, vagy frissíti a meglévő fájlokat. Erre szükség lesz a frissítési/törlési műveletekhez.  

- A Virtual Network teljes Azure Resource Manager erőforrás-azonosítót használ a. cscfg fájl NetworkConfiguration szakaszának erőforrás neve helyett. Például: `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. A felhőalapú szolgáltatással azonos erőforráscsoporthoz tartozó virtuális hálózatok esetében úgy is dönthet, hogy a. cscfg fájlt visszahelyezi, hogy csak a virtuális hálózat nevét használja.  

- A klasszikus méreteket, például a kis-és nagyméretű ExtraLarge az új méretek, a Standard_A * érték váltja fel. A méret nevét a. csdef fájlban lévő új nevekre kell módosítani. További információ: [Cloud Services (bővített támogatás) központi telepítésének előfeltételei](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- A telepítési fájlok legújabb példányának beszerzéséhez használja a Get API-t. 
    - Sablon beszerzése a [portál](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal), a [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates), a [CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)és a [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) használatával 
    - Szerezze be a. csdef fájlt a [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) vagy a [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package)használatával. 
    - Szerezze be a. cscfg fájlt a [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) vagy a [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package)használatával. 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Az ügyfél automatizálási, CI/CD-folyamatai, egyéni parancsfájljai, egyéni irányítópultok, egyéni eszközök stb. változásai  

Az ügyfeleknek frissíteniük kell az eszközeiket és az automatizálást, hogy az új API-k/parancsok használatával kezelhesse az üzembe helyezést. A módosítás részeként az ügyfél könnyedén fogadhatja Azure Resource Manager/Cloud Services (kiterjesztett támogatás) új szolgáltatásait és képességeit. 

- Az erőforrás-és erőforráscsoport-nevek változásai az áttelepítés után
    - A Migrálás részeként néhány erőforrás neve, például a Cloud Service, a nyilvános IP-címek stb. változik. A Cloud Service frissítése előtt ezeket a módosításokat a telepítési fájlokban is meg kell jelenniük. [További információ a változó erőforrások neveiről](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- A Cloud Services kezeléséhez és méretezéséhez szükséges szabályok és szabályzatok újbóli létrehozása 
    - Az [automatikus skálázási szabályok](configure-scaling.md) nem települnek át. Az áttelepítés után hozza létre újra az automatikus skálázási szabályokat.  
    - A [riasztásokat](enable-alerts.md) a rendszer nem telepíti át. Az áttelepítés után hozza létre újra a riasztásokat.
    - A Key Vault hozzáférési szabályzatok nélkül jön létre. [Hozza létre a megfelelő szabályzatokat](../key-vault/general/assign-access-policy-portal.md) a Key Vault a tanúsítványok megtekintéséhez és kezeléséhez. A tanúsítványok megjelennek a titkok nevű lapon a beállítások területen.

## <a name="next-steps"></a>Következő lépések
- [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrálás Cloud Servicesre (kiterjesztett támogatás) a [Azure Portal](in-place-migration-portal.md) használatával
- Migrálás Cloud Servicesre (kiterjesztett támogatás) a [PowerShell](in-place-migration-powershell.md) használatával
