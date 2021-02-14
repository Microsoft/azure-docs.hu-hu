---
title: Az Azure Application Gateway használatával biztosíthatja webalkalmazásait az Azure VMware-megoldásban
description: Az Azure Application Gateway konfigurálása az Azure VMware-megoldáson futó webalkalmazások biztonságos megjelenítéséhez.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 9b10c206114ca922cc11bd8cb0321941b8ba672c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384197"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Az Azure Application Gateway használatával biztosíthatja webalkalmazásait az Azure VMware-megoldásban

Az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) egy 7. rétegbeli webes forgalmi terheléselosztó, amely lehetővé teszi a webalkalmazások forgalmának kezelését. A szolgáltatás az Azure VMware-megoldás 1.0-s és 2.0-s verziójában is elérhető. Mindkét verzió az Azure VMware-megoldáson futó webalkalmazásokkal lett tesztelve.

A képességek a következők: 
- Cookie-alapú munkamenet-affinitás
- URL-alapú útválasztás
- Webalkalmazási tűzfal (WAF)

A szolgáltatások teljes listájáért tekintse meg az [Azure Application Gateway szolgáltatásai](../application-gateway/features.md)című témakört. 

Ez a cikk bemutatja, hogyan használhatja a Application Gatewayt a webkiszolgáló-farmok előtt az Azure VMware-megoldáson futó webalkalmazások elleni védelemhez. 

## <a name="topology"></a>Topológia
A diagram bemutatja, hogyan használhatók a Application Gateway az Azure IaaS Virtual Machines (VM), az Azure virtuálisgép-méretezési csoportok és a helyszíni kiszolgálók elleni védelemhez. Application Gateway az Azure VMware megoldás virtuális gépeket helyszíni kiszolgálókként kezeli. 

![Ábra, amely bemutatja, hogyan védi Application Gateway az Azure IaaS Virtual Machines (VM), az Azure virtuálisgép-méretezési csoportok vagy a helyszíni kiszolgálók védelmét.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Az Azure Application Gateway jelenleg az egyetlen támogatott módszer az Azure VMware-alapú virtuális gépeken futó webalkalmazások elérhetővé tétele érdekében.

Az ábrán az Azure VMware Solution web Applications használatával végzett Application Gateway ellenőrzéséhez használt tesztelési forgatókönyv látható.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="A Application Gateway Azure VMware-megoldás webalkalmazásaival való ellenőrzéséhez használt tesztelési forgatókönyvet bemutató ábra." border="false":::

A Application Gateway példány egy dedikált alhálózaton van üzembe helyezve a központban. Azure-beli nyilvános IP-címmel rendelkezik. Ajánlott a virtuális hálózat szabványos DDoS-védelmének aktiválása. A webkiszolgáló egy, a NSX T0 és T1 átjáró mögötti Azure VMware-megoldáshoz tartozó saját felhőben üzemel. Az Azure VMware-megoldás [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) használ az elosztóval és a helyszíni rendszerekkel való kommunikáció engedélyezésére.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók.
- Azure VMware-megoldás saját felhő üzembe helyezése és futtatása.

## <a name="deployment-and-configuration"></a>Üzembe helyezés és konfigurálás

1. A Azure Portal keresse meg **Application Gateway** és válassza az **Application Gateway létrehozása** lehetőséget.

2. Adja meg az alapszintű részleteket az alábbi ábrán látható módon: Ezután válassza a **Tovább: frontendek>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Képernyőfelvétel: Application Gateway létrehozása lap Azure Portalban.":::

3. Válassza ki az előtérbeli IP-cím típusát. Nyilvános esetén válasszon egy meglévő nyilvános IP-címet, vagy hozzon létre egy újat. Kattintson a **Next (tovább) gombra:>háttérrendszer**.

    > [!NOTE]
    > Privát frontendek esetében csak a standard és a Web Application Firewall (WAF) SKU-ket támogatja a rendszer.

4. Adja hozzá az Azure VMware megoldás-infrastruktúrán futó virtuális gépek háttérbeli készletét. Adja meg az Azure VMware-megoldás saját felhőben futó webkiszolgálók részleteit, és válassza a **Hozzáadás** lehetőséget.  Ezután válassza a **következő: Configuration>**.

5. A **konfiguráció** lapon válassza az **útválasztási szabály hozzáadása** elemet.

6. A **figyelő** lapon adja meg a figyelő részleteit. Ha a HTTPS lehetőséget választja, meg kell adnia egy tanúsítványt a PFX-fájlból vagy egy meglévő Azure Key Vault-tanúsítványból. 

7. Válassza a **háttérbeli célok** fület, és válassza ki a korábban létrehozott háttér-készletet. A **http-beállítások** mezőben válassza az **új hozzáadása** elemet.

8. Konfigurálja a HTTP-beállítások paramétereit. Válassza a **Hozzáadás** lehetőséget.

9. Ha elérésiút-alapú szabályokat szeretne konfigurálni, válassza **a több cél hozzáadása lehetőséget egy elérésiút-alapú szabály létrehozásához**. 

10. Adjon hozzá egy elérésiút-alapú szabályt, és válassza a **Hozzáadás** lehetőséget. További elérésiút-alapú szabályok hozzáadásához ismételje meg a műveletet. 

11. Ha befejezte az elérésiút-alapú szabályok hozzáadását, válassza a **Hozzáadás** újra lehetőséget. Ezután válassza a **Tovább: címkék>** elemet. 

12. Adjon hozzá címkéket, majd kattintson a **Tovább gombra: Review + Create>**.

13. Az érvényesítés a Application Gatewayon fog futni; Ha a művelet sikeres, válassza a **Létrehozás** elemet a telepítéshez.

## <a name="configuration-examples"></a>Konfigurációs példák

Most a következő használati esetekben konfiguráljuk a Application Gateway az Azure VMware-megoldás virtuális gépei háttér-készletekként: 

- [Több webhely üzemeltetése](#hosting-multiple-sites)
- [Útválasztás URL-cím szerint](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Több webhely üzemeltetése

Ez az eljárás bemutatja, hogyan határozhatja meg a háttérbeli címkészletet egy Azure VMware-megoldás privát felhőben futó virtuális gépek használatával egy meglévő Application gatewayen. 

>[!NOTE]
>Ez az eljárás feltételezi, hogy több tartománya van, ezért a www.contoso.com és a www.fabrikam.com példáit fogjuk használni.


1. A saját felhőben hozzon létre két különböző virtuális gépet. Az egyik a contoso és a második Fabrikam-t jelöli. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Képernyőfelvétel: a webkiszolgáló részleteinek összefoglalása a VSphere-ügyfélen.":::

    A Windows Server 2016-et a telepített Internet Information Services (IIS) szerepkörrel használtuk. A virtuális gépek telepítése után futtassa a következő PowerShell-parancsokat az IIS konfigurálásához az egyes virtuális gépeken. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Egy meglévő Application Gateway-példányban válassza ki a bal oldali menüben a **háttér-készletek** elemet, válassza a  **Hozzáadás** lehetőséget, majd adja meg az új készletek részleteit. A jobb oldali ablaktáblán válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="A backend-készletek oldal képernyőképe a háttérbeli készletek hozzáadásához." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. A **figyelők** szakaszban hozzon létre egy új figyelőt az egyes webhelyekhez. Adja meg az egyes figyelők részleteit, és válassza a **Hozzáadás** lehetőséget.

4. A bal oldali ablaktáblán válassza a **http-beállítások** elemet, és válassza a **Hozzáadás** lehetőséget. A részletek kitöltésével hozzon létre egy új HTTP-beállítást, és kattintson a **Mentés** gombra.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="A HTTP-beállítások lap képernyőképe új HTTP-beállítás létrehozásához." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Hozza létre a szabályokat a bal oldali menü **szabályok** szakaszában. Társítsa az egyes szabályokat a megfelelő figyelőhöz. Válassza a **Hozzáadás** lehetőséget.

6. Konfigurálja a megfelelő háttér-készletet és a HTTP-beállításokat. Válassza a **Hozzáadás** lehetőséget.

7. A kapcsolatok tesztelése. Nyissa meg az előnyben részesített böngészőt, és navigáljon az Azure VMware-megoldási környezetében üzemeltetett különböző webhelyekre, például: http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="A kapcsolatok sikeres tesztelését bemutató böngésző oldal képernyőképe.":::

### <a name="routing-by-url"></a>Útválasztás URL-cím szerint

A következő lépések a háttérbeli címkészlet használatát határozzák meg egy Azure VMware-megoldás privát felhőben futó virtuális gépek használatával. A privát felhő egy meglévő Application Gateway-kiszolgálón található. Ezután olyan útválasztási szabályokat hozhat létre, amelyek gondoskodnak arról, hogy a webes forgalom a készletekben lévő megfelelő kiszolgálókon érkezzen.

1. A saját felhőben hozzon létre egy virtuálisgép-készletet a webfarm megjelenítéséhez. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Képernyőkép a VMSphere-ügyfélen lévő oldalról, amely egy másik virtuális gép összegzését jeleníti meg.":::

    Az oktatóanyag szemléltetésére a Windows Server 2016 operációs rendszert futtató IIS-szerepkört használták. A virtuális gépek telepítése után futtassa a következő PowerShell-parancsokat az IIS konfigurálásához az egyes virtuálisgép-oktatóanyagokhoz. 

    Az első virtuális gép, a contoso-web-01, a fő webhelyet fogja üzemeltetni.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    A második virtuális gép, a contoso-web-02 a lemezképek helyét fogja üzemeltetni.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    A külső virtuális gép (contoso-web-03) a videó helyét fogja üzemeltetni.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Vegyen fel három új háttér-készletet egy meglévő Application Gateway-példányba. 

   1. Válassza ki a **háttér-készletek** elemet a bal oldali menüben. 
   1. Válassza a **Hozzáadás** lehetőséget, és adja meg a **contoso-web** első készletének részleteit. 
   1. Adjon hozzá egy virtuális gépet célként. 
   1. Válassza a **Hozzáadás** lehetőséget. 
   1. Ismételje meg ezt a folyamatot a **contoso-images** és a **contoso-video** esetében, és adjon hozzá egy egyedi virtuális gépet a célként. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Képernyőfelvétel a háttérbeli készletek oldalról, amely három új háttér-készlet hozzáadását mutatja be." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. A **figyelők** szakaszban hozzon létre egy alapszintű új figyelőt az 8080-es port használatával.

4. A bal oldali navigációs sávon válassza a **http-beállítások** elemet, és válassza a **Hozzáadás** lehetőséget a bal oldali ablaktáblán. A részletek kitöltésével hozzon létre egy új HTTP-beállítást, és kattintson a **Mentés** gombra.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Képernyőkép a HTTP-beállítások konfigurálása lap a HTTP-beállítások konfigurációjának megjelenítéséhez.":::

5. Hozza létre a szabályokat a bal oldali menü **szabályok** szakaszában. Társítsa az egyes szabályokat a korábban létrehozott figyelőhöz. Ezután konfigurálja a fő háttér-készletet és a HTTP-beállításokat. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Képernyőkép – útválasztási szabály hozzáadása lap az útválasztási szabályok háttérbeli célra való konfigurálásához.":::

6. Tesztelje a konfigurációt. Nyissa meg az Application Gatewayt a Azure Portalon, és másolja a nyilvános IP-címet az **Áttekintés** szakaszban. 

   1. Nyisson meg egy új böngészőablakot, és írja be az URL-címet `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="A konfiguráció sikeres tesztelését bemutató böngésző oldal képernyőképe.":::

   1. Módosítsa az URL-címet a következőre: `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Képernyőkép egy másik sikeres tesztről az új URL-címmel.":::

   1. Módosítsa újra az URL-címet a következőre: `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Képernyőkép a sikeres tesztelésről a végső URL-címmel.":::

## <a name="next-steps"></a>Következő lépések

Most, hogy a Application Gateway használatával gondoskodik az Azure VMware-megoldáson futó webalkalmazások védeleméről, érdemes megismernie az alábbiakat:

- [Az Azure Application Gateway konfigurálása különböző forgatókönyvekhez](../application-gateway/configuration-overview.md).
- [Traffic Manager üzembe helyezése az Azure VMware-megoldás](deploy-traffic-manager-balance-workloads.md)számítási feladatainak terheléselosztásához.
- [Azure NetApp Files integrálása az Azure VMware megoldás-alapú munkaterhelésekkel](netapp-files-with-azure-vmware-solution.md).
