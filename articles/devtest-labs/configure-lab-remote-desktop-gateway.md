---
title: Tesztkörnyezet konfigurálása Távoli asztali átjáró használatára Azure DevTest Labs
description: Megtudhatja, hogyan konfigurálhat labort Azure DevTest Labs egy távoli asztali átjáróval, hogy biztosítsa a laboratóriumi virtuális gépek biztonságos elérését anélkül, hogy az RDP-portot fel kellene tenni.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: dcf5191dea64c3d7bf28b9ce1c616d3d2defb73e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97695682"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>A labor konfigurálása Azure DevTest Labs távoli asztali átjáró használatához
Azure DevTest Labs egy távoli asztali átjárót konfigurálhat a laborhoz, így biztosíthatja a labor virtuális gépek (VM-EK) biztonságos elérését anélkül, hogy az RDP-portot fel kellene tenni. A labor központi helyet biztosít a labor felhasználói számára az összes olyan virtuális gép megtekintésére és a hozzájuk való kapcsolódásra, amelyhez hozzáférése van. A **virtuális gép** oldalon a **Kapcsolódás** gomb egy, a géphez való kapcsolódáshoz megnyitható RDP-fájlt hoz létre. Az RDP-kapcsolatot tovább testreszabhatja és biztonságossá teheti úgy, hogy a labort egy távoli asztali átjáróhoz csatlakoztatja. 

Ez a megközelítés biztonságosabb, mert a tesztkörnyezet közvetlenül az átjárót használja, vagy vállalati hitelesítő adatokat használhat a tartományhoz csatlakoztatott átjárón a gépekhez való csatlakozáshoz. A labor a jogkivonat-hitelesítés használatát is támogatja az átjárót futtató géphez, amely lehetővé teszi a felhasználók számára, hogy az internet felé irányuló RDP-port nélkül csatlakozzanak a labor-alapú virtuális gépekhez. Ez a cikk bemutatja, hogyan állíthat be egy olyan labort, amely jogkivonat-hitelesítést használ a labor-gépekhez való kapcsolódáshoz.

## <a name="architecture-of-the-solution"></a>A megoldás architektúrája

![A megoldás architektúrája](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Az [RDP-fájl tartalmának beolvasása](/rest/api/dtl/virtualmachines/getrdpfilecontents) műveletet a **Csatlakozás** gomb kiválasztásakor hívja meg a rendszer. 1. 
1. Az RDP-fájl beolvasása művelet meghívja a `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` hitelesítési jogkivonat igénylését.
    1. `{gateway-hostname}` az átjáró állomásneve van megadva a labor **Beállítások** oldalán a laborhoz a Azure Portal. 
    1. `{lab-machine-name}` annak a gépnek a neve, amelyhez csatlakozni próbál.
    1. `{port-number}` az a port, amelyen a kapcsolódást el kell végezni. Ez a port általában 3389. Ha a labor virtuális gép a DevTest Labs [megosztott IP-](devtest-lab-shared-ip.md) szolgáltatását használja, a port eltérő lesz.
1. A távoli asztali átjáró elhalasztja a hívást `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` egy Azure-függvénynek a hitelesítési jogkivonat létrehozásához. A DevTest Labs szolgáltatás automatikusan tartalmazza a függvény kulcsát a kérelem fejlécében. A funkcióbillentyű a tesztkörnyezet kulcstartójában menthető. Az adott titok neve, amely **átjáró-jogkivonat titka** lesz a labor **Beállítások** lapján a laborban.
1. Az Azure-függvénynek egy jogkivonat-tokent kell visszaadnia a tanúsítványalapú jogkivonat hitelesítéséhez az átjáró számítógépén.  
1. Az RDP-fájl beolvasása művelet ezután visszaadja a teljes RDP-fájlt, beleértve a hitelesítési adatokat is.
1. Az RDP-fájlt az előnyben részesített RDP-kapcsolati program használatával nyithatja meg. Ne feledje, hogy az RDP-kapcsolati programok nem támogatják a jogkivonat-hitelesítést. A hitelesítési jogkivonat lejárati dátummal rendelkezik, amelyet a Function alkalmazás állít be. A jogkivonat lejárta előtt létesítsen kapcsolattal a labor virtuális gépet.
1. Ha a távoli asztali átjáró számítógépe hitelesíti a jogkivonatot az RDP-fájlban, a rendszer továbbítja a kapcsolódást a tesztkörnyezet számítógépének.

### <a name="solution-requirements"></a>A megoldás követelményei
A DevTest Labs-jogkivonat hitelesítési funkciójának használatakor az átjáró gépek, a tartománynév-szolgáltatások (DNS) és a függvények néhány konfigurációs követelményt is igénybe vehetnek.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>A távoli asztali átjáró gépekre vonatkozó követelmények
- A HTTPS-forgalom kezeléséhez a TLS/SSL-tanúsítványt telepíteni kell az átjáró számítógépre. A tanúsítványnak meg kell egyeznie az átjáró-Farm terheléselosztó vagy a gép teljes TARTOMÁNYNEVÉnek teljes tartománynevével (FQDN), ha csak egy gép van. A Wild-Card TLS/SSL-tanúsítványok nem működnek.  
- Az átjárót futtató számítógép (ek) re telepített aláíró tanúsítvány. Hozzon létre egy aláíró tanúsítványt [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) parancsfájl használatával.
- Telepítse a távoli asztali átjáró jogkivonat-hitelesítését támogató [csatlakoztatható hitelesítési](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) modult. Ilyen modul például `RDGatewayFedAuth.msi` [System Center Virtual Machine Manager (VMM) rendszerképekből](/system-center/vmm/install-console?view=sc-vmm-1807)áll. A System centerrel kapcsolatos további információkért lásd: a [System Center dokumentációja](/system-center/) és [díjszabása](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Az átjárókiszolgáló kezelheti a rendszerre irányuló kérelmeket `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    Az átjáró-állomásnév az átjáró-Farm vagy a számítógép teljes tartománynevének teljes TARTOMÁNYNEVE, ha csak egy gép van. A annak a tesztkörnyezet-gépnek a neve, amelyhez `{lab-machine-name}` csatlakozni próbál, és az a `{port-number}` port, amelyen a kapcsolat létrejön.  Alapértelmezés szerint ez a port 3389.  Ha azonban a virtuális gép a DevTest Labs [megosztott IP-](devtest-lab-shared-ip.md) szolgáltatását használja, a port eltérő lesz.
- Az Internet Information Server (IIS) [alkalmazás-útválasztási kérelmi](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) modulja használható az `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` Azure-függvényre irányuló kérelmek átirányításához, amely a kérést kezeli a hitelesítéshez szükséges jogkivonat beszerzéséhez.


## <a name="requirements-for-azure-function"></a>Az Azure Function követelményei
Az Azure Function kezeli a kérelmek formátumát, `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` és visszaadja a hitelesítési jogkivonatot az átjáró gépekre telepített azonos aláíró tanúsítvány alapján. A a `{function-app-uri}` függvény eléréséhez használt URI. A rendszer automatikusan átadja a függvény kulcsát a kérelem fejlécében. A minta függvények esetében lásd: [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>A hálózatra vonatkozó követelmények

- Az átjáró-számítógépeken telepített TLS/SSL-tanúsítványhoz társított teljes tartománynév DNS-jének az átjárót futtató géphez vagy az átjáró-farmhoz tartozó terheléselosztó felé kell irányítani az adatforgalmat.
- Ha a labor gép magánhálózati IP-címeket használ, akkor az átjárót tartalmazó gépről hálózati elérési útnak kell lennie a tesztkörnyezet számítógépének, vagy ugyanazzal a virtuális hálózattal, vagy a virtuális hálózatok használatával.

## <a name="configure-the-lab-to-use-token-authentication"></a>A labor beállítása jogkivonat-hitelesítés használatára 
Ez a szakasz bemutatja, hogyan konfigurálhatja a labort olyan távoli asztali átjáró-gép használatára, amely támogatja a jogkivonat-hitelesítést. Ez a szakasz nem fedi le, hogyan állíthat be egy távoli asztali átjáró-farmot. Ebben az esetben tekintse meg a cikk végén található [Távoli asztali átjáró létrehozásához szükséges mintát](#sample-to-create-a-remote-desktop-gateway) . 

A tesztkörnyezet beállításainak frissítése előtt tárolja a függvény sikeres végrehajtásához szükséges kulcsot a tesztkörnyezet kulcstartójában lévő hitelesítési jogkivonat visszaadásához. A függvény kulcsának értékét a **kezelés** lapon érheti el a Azure Portalban. A titkos kulcsnak a kulcstartóban való mentésével kapcsolatos további információkért lásd: [titkos kód hozzáadása Key Vaulthoz](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Mentse a titok nevét későbbi használatra.

A Lab kulcstartó AZONOSÍTÓjának megkereséséhez futtassa az alábbi Azure CLI-parancsot: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurálja a labort a jogkivonat-hitelesítés használatára az alábbi lépések segítségével:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
1. A Labs listából válassza ki a **labort**.
1. A labor lapon válassza a **konfiguráció és házirendek** lehetőséget.
1. A bal oldali menüben, a **Beállítások** szakaszban válassza a **labor-beállítások** elemet.
1. A **Távoli asztal** szakaszban adja meg a távoli asztali szolgáltatások átjárójának vagy farmjának teljes tartománynevét (FQDN) vagy IP-címét az **átjáró állomásneve** mezőhöz. Ennek az értéknek meg kell egyeznie az átjáró gépeken használt TLS/SSL-tanúsítvány teljes tartománynevével.

    ![Távoli asztal beállításai a labor beállításaiban](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. A **Távoli asztal** szakasz **átjáró-token** titka részében adja meg a korábban létrehozott titok nevét. Ez az érték nem maga a funkcióbillentyű, hanem a labor kulcstárolójában található titkos kulcs neve, amely tartalmazza a funkcióbillentyű.

    ![Átjáró-jogkivonat titka a labor beállításaiban](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Mentés** Változások.

    > [!NOTE] 
    > A **Save (Mentés**) gombra kattintva elfogadja [Távoli asztali átjáró licencfeltételeket](https://www.microsoft.com/licensing/product-licensing/products). A távoli átjáróval kapcsolatos további információkért tekintse meg a távoli asztali környezet [Távoli asztali szolgáltatásokét](/windows-server/remote/remote-desktop-services/Welcome-to-rds) és [üzembe helyezését](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)ismertető témakört.


Ha a labor automatizáláson keresztüli konfigurálását részesíti előnyben, tekintse meg a minta PowerShell-parancsfájl [Set-DevTestLabGateway.ps1át](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) az **átjáró állomásneve** és az **átjáró-jogkivonat titkos** beállításainak beállításához. A [Azure DevTest Labs GitHub-tárház](https://github.com/Azure/azure-devtestlab) egy Azure Resource Manager sablont is biztosít, amely létrehoz vagy frissít egy labort az **átjárói állomásnév** és az **átjáró-jogkivonat titkos** beállításainak használatával.

## <a name="configure-network-security-group"></a>Hálózati biztonsági csoport konfigurálása
A labor további biztonságossá tételéhez hálózati biztonsági csoportot (NSG) adhat hozzá a labor virtuális gépek által használt virtuális hálózathoz. A NSG beállításával kapcsolatos útmutatásért lásd: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](../virtual-network/manage-network-security-group.md).

Íme egy példa a NSG, amely csak az átjárón keresztüli forgalom elérését teszi lehetővé a labor-gépek eléréséhez. Az ebben a szabályban szereplő forrás az egyátjárós gép IP-címe, vagy a terheléselosztó IP-címe az átjáró gépek előtt.

![Hálózati biztonsági csoport – szabályok](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Példa távoli asztali átjáró létrehozására

> [!NOTE] 
> A sablonok használatával elfogadja a [Távoli asztali átjáró licencfeltételeket](https://www.microsoft.com/licensing/product-licensing/products). A távoli átjáróval kapcsolatos további információkért tekintse meg a távoli asztali környezet [Távoli asztali szolgáltatásokét](/windows-server/remote/remote-desktop-services/Welcome-to-rds) és [üzembe helyezését](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)ismertető témakört.

A [Azure DevTest Labs GitHub-tárház](https://github.com/Azure/azure-devtestlab) több mintát is biztosít, amelyek segítségével beállíthatja a token-hitelesítés és a távoli asztali átjáró DevTest Labs szolgáltatással való használatához szükséges erőforrásokat. Ezek a minták Azure Resource Manager sablonokat tartalmaznak az átjáró gépekhez, a labor beállításaihoz és a Function alkalmazáshoz.

Kövesse az alábbi lépéseket a távoli asztali átjáró farmhoz tartozó minta megoldás létrehozásához.

1. Hozzon létre egy aláíró tanúsítványt.  [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)futtatása. Mentse a létrehozott tanúsítvány ujjlenyomatát, jelszavát és Base64-kódolását.
2. TLS/SSL-tanúsítvány beszerzése. A TLS/SSL-tanúsítvánnyal társított teljes tartománynévnek az Ön által vezérelt tartományhoz kell tartoznia. A tanúsítvány ujjlenyomatának, jelszavának és Base64-kódolásának mentése. A PowerShell használatával történő ujjlenyomat beszerzéséhez használja a következő parancsokat.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    A Base64-kódolás PowerShell használatával történő lekéréséhez használja a következő parancsot.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Fájlok letöltése innen: [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    A sablonhoz hozzá kell férni néhány más Resource Manager-sablonhoz és kapcsolódó erőforráshoz ugyanahhoz az alap URI-hoz. Másolja az összes fájlt [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) és RDGatewayFedAuth.msi egy blob-tárolóba egy Storage-fiókban.  
4. Telepítse **azuredeploy.jsalkalmazást a** rendszerből [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . A sablon a következő paramétereket veszi figyelembe:
    - adminUsername – kötelező.  Az átjáró-gépek rendszergazdai felhasználóneve.
    - adminPassword – kötelező. Az átjáró gépekhez tartozó rendszergazdai fiók jelszava.
    - instanceCount – a létrehozandó átjáró-gépek száma.  
    - alwaysOn – azt jelzi, hogy a létrehozott Azure Functions alkalmazást meleg állapotban szeretné-e tartani. A Azure Functions alkalmazás megtartása megakadályozza a késést, amikor a felhasználók először próbálnak csatlakozni a labor virtuális géphez, de a költségeket is figyelembe kell venni.  
    - tokenLifetime – az az időtartam, ameddig a létrehozott jogkivonat érvényes lesz. A formátum óó: PP: mm.
    - sslCertificate – az átjáróhoz tartozó TLS/SSL-tanúsítvány Base64-kódolása.
    - sslCertificatePassword – az átjáró géphez tartozó TLS/SSL-tanúsítvány jelszava.
    - sslCertificateThumbprint – a Tanúsítvány ujjlenyomata az azonosításhoz a TLS/SSL-tanúsítvány helyi tanúsítványtárolójában.
    - signCertificate – az átjáróhoz tartozó számítógép aláíró tanúsítványának Base64-kódolása.
    - signCertificatePassword – az átjáróhoz tartozó számítógép aláíró tanúsítványának jelszava.
    - signCertificateThumbprint – a Tanúsítvány ujjlenyomata az aláíró tanúsítvány helyi tanúsítványtárolójában való azonosításhoz.
    - _artifactsLocation – az URI-hely, ahol az összes támogató erőforrás megtalálható. Az értéknek teljesen minősített UIR kell lennie, nem relatív elérési útnak.
    - _artifactsLocationSasToken – a támogatási erőforrások eléréséhez használt közös hozzáférés-aláírási (SAS) token, ha a hely egy Azure Storage-fiók.

    A sablon a következő paranccsal helyezhető üzembe az Azure CLI használatával:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    A paraméterek leírása a következő:

    - A () {Storage-Account-Endpoint} lekérhető a futtatásával `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  A (z) {Storage-acct-Name} annak a Storage-fióknak a neve, amely a feltöltött fájlokat tárolja.  
    - A (z) {Container-name} a (z) {Storage-acct-Name} tároló neve, amely a feltöltött fájlokat tárolja.  
    - A ({SAS-token}) a futtatásával szerezhető be `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - A (z) {Storage-acct-Name} annak a Storage-fióknak a neve, amely a feltöltött fájlokat tárolja.  
        - A (z) {Container-name} a (z) {Storage-acct-Name} tároló neve, amely a feltöltött fájlokat tárolja.  
        - A (z) {UTC-lejárati dátum} az a dátum, amely UTC szerint lejár, és az SAS-token már nem használható a Storage-fiók eléréséhez.

    Jegyezze fel a gatewayFQDN és a gatewayIP értékét a sablon központi telepítési kimenetéről. Emellett mentenie kell az újonnan létrehozott függvényhez tartozó funkcióbillentyű értékét is, amely a [Function app Settings (funkció alkalmazás beállításai](../azure-functions/functions-how-to-use-azure-function-app-settings.md) ) lapon található.
5. Konfigurálja a DNS-t úgy, hogy a TLS/SSL-tanúsítvány teljes tartományneve az előző lépésben a gatewayIP IP-címére irányítsa.

    A Távoli asztali átjáró farm létrehozása és a megfelelő DNS-frissítések elvégzése után készen áll arra, hogy a labor a DevTest Labs szolgáltatásban is használható legyen. Az **átjáró-állomásnév** és az **átjáró-jogkivonat titkos** beállításait úgy kell konfigurálni, hogy az Ön által telepített átjáró-számítógép (eke) t használják. 

    > [!NOTE]
    > Ha a labor gép magánhálózati IP-címeket használ, akkor az átjárót tartalmazó gépről hálózati elérési útnak kell lennie a tesztkörnyezet számítógépének, vagy ugyanazzal a virtuális hálózattal, vagy egy egyenrangú virtuális hálózat használatával.

    Ha az átjárót és a labort is konfigurálta **, akkor a kapcsolódáskor** létrehozott kapcsolatfájl automatikusan tartalmazza a jogkivonat-hitelesítés használatával történő kapcsolódáshoz szükséges információkat.     

## <a name="next-steps"></a>Következő lépések
A Távoli asztali szolgáltatások: [Távoli asztali szolgáltatások dokumentációjának](/windows-server/remote/remote-desktop-services/Welcome-to-rds) megismeréséhez tekintse meg a következő cikket.