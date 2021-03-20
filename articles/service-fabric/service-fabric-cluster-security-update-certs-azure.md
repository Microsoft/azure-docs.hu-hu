---
title: Tanúsítványok kezelése Azure Service Fabric-fürtben
description: Útmutatás új tanúsítványok hozzáadásához, az átváltási tanúsítványhoz és a tanúsítvány eltávolításához egy Service Fabric-fürtön.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 6dd4440d76bed9d110c13baab9f4e67b3a5c64c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660899"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Tanúsítványok hozzáadása és törlése Service Fabric-fürtön az Azure-ban
Javasoljuk, hogy ismerkedjen meg a Service Fabric X. 509 tanúsítványokkal, és Ismerje meg a [fürt biztonsági forgatókönyveit](service-fabric-cluster-security.md). A folytatás előtt meg kell ismernie, hogy mi a fürt tanúsítványa, és mire használható.

Az Azure Service Fabric SDK alapértelmezett tanúsítvány-betöltési viselkedése a következő lejárati dátummal rendelkező meghatározott tanúsítvány üzembe helyezése és használata. az elsődleges vagy másodlagos konfigurációs definíciótól függetlenül. A klasszikus viselkedésre való visszatérés nem ajánlott speciális művelet, és a konfigurációban a "UseSecondaryIfNewer" paraméter értékének FALSE értéket kell beállítania `Fabric.Code` .

A Service Fabric lehetővé teszi két fürtcsomópont, egy elsődleges és egy másodlagos beállítás megadását, ha a fürt létrehozása során konfigurálja a tanúsítvány biztonságát, az Ügyféltanúsítványok mellett. Tekintse át az [Azure-fürtök portálon keresztüli létrehozását](service-fabric-cluster-creation-via-portal.md) , vagy hozzon létre [egy Azure-fürtöt Azure Resource Manager segítségével](service-fabric-cluster-creation-via-arm.md) a létrehozási idő beállításával kapcsolatos részletekért. Ha csak egy fürtöt ad meg a létrehozás ideje alatt, akkor az elsődleges tanúsítványként lesz használva. A fürt létrehozása után hozzáadhat egy új tanúsítványt másodlagosként.

> [!NOTE]
> Biztonságos fürt esetén mindig szüksége lesz legalább egy érvényes (nem visszavont és nem lejárt) fürt tanúsítványára (elsődleges vagy másodlagos) (ha nem, a fürt működése leáll). 90 nappal azelőtt, hogy az összes érvényes tanúsítvány eléri a lejáratot, a rendszer egy figyelmeztetési nyomkövetést és egy figyelmeztetési állapotra vonatkozó eseményt hoz létre a csomóponton. Ezek jelenleg csak az értesítések Service Fabric küldése a tanúsítvány lejáratával kapcsolatban.
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Másodlagos fürthöz tartozó tanúsítvány hozzáadása a portál használatával
A másodlagos fürt tanúsítványa nem adható hozzá a Azure Portalon keresztül; Azure PowerShell használata. A folyamatot a dokumentum későbbi részében ismertetjük.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Fürt tanúsítványának eltávolítása a portál használatával
Biztonságos fürt esetén mindig szüksége lesz legalább egy érvényes (nem visszavont és nem lejárt) tanúsítványra. A legtávolabbi lejárati dátummal üzembe helyezett tanúsítvány használatban lesz, és a rendszer eltávolítja a fürtöt a fürt működésének leállítása érdekében. Győződjön meg arról, hogy csak a lejárt tanúsítvány, vagy egy nem használt tanúsítvány van, amely lejár a leghamarabb.

A nem használt fürt biztonsági tanúsítványának eltávolításához navigáljon a biztonság szakaszhoz, és válassza a Törlés lehetőséget a nem használt tanúsítvány helyi menüjében.

Ha a cél az elsődlegesként megjelölt tanúsítvány eltávolítása, akkor egy másodlagos tanúsítványt kell telepítenie egy lejárati dátummal, amely az elsődleges tanúsítványhoz képest tovább tart, és lehetővé teszi az automatikus átváltási viselkedést; az automatikus rollover befejeződése után törölje az elsődleges tanúsítványt.

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Másodlagos tanúsítvány hozzáadása a Azure Resource Manager használatával

Ezek a lépések feltételezik, hogy tisztában van azzal, hogy a Resource Manager hogyan működik, és legalább egy Service Fabric-fürtöt üzembe helyezett egy Resource Manager-sablon használatával, és a sablont, amelyet a fürt kézi beállításához használt. Azt is feltételezi, hogy a JSON-t használja.

> [!NOTE]
> Ha egy minta sablont és paramétereket keres, amelyekkel követheti nyomon vagy kiindulási pontként, letöltheti azt ebből a [git-](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)adattárból. 
> 
> 

### <a name="edit-your-resource-manager-template"></a>A Resource Manager-sablon szerkesztése

A könnyebben követhető és a minta 5-VM-1-NodeTypes-Secure_Step2.JSaz összes szerkesztési műveletet tartalmazza. a minta a [git-](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)tárházban érhető el.

**Ügyeljen arra, hogy kövesse az összes lépést**

1. Nyissa meg a fürt üzembe helyezéséhez használt Resource Manager-sablont. (Ha letöltötte a mintát az előző tárházból, akkor a 5-VM-1-NodeTypes-Secure_Step1.JShasználatával helyezzen üzembe egy biztonságos fürtöt, majd nyissa meg a sablont).

2. Adjon hozzá két "string" típusú "secCertificateThumbprint" és "secCertificateUrlValue" **paramétert** a sablon paraméter szakaszához. Másolja a következő kódrészletet, és adja hozzá a sablonhoz. A sablon forrása alapján előfordulhat, hogy már rendelkezik a megadott beállításokkal, ha a következő lépésre lép. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Módosítsa a **Microsoft. ServiceFabric/Clusters** erőforrást – keresse meg a "Microsoft. ServiceFabric/Clusters" erőforrás-definíciót a sablonban. A definíció tulajdonságai alatt a "tanúsítvány" JSON-címkét fogja találni, amelynek a következő JSON-kódrészlethez hasonlóan kell kinéznie:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Adjon hozzá egy "thumbprintSecondary" nevű új címkét, és adjon neki egy "[parameters (' secCertificateThumbprint ')] ' értéket.  

    Így most az erőforrás-definíciónak a következőhöz hasonlóan kell kinéznie (a sablon forrásától függően előfordulhat, hogy nem pontosan az alábbi kódrészlethez hasonló). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Ha át szeretné adni **a tanúsítványt**, adja meg az új tanúsítványt elsődlegesként, és helyezze át az aktuális elsődlegest másodlagosként. Ennek eredményeképpen az aktuális elsődleges tanúsítvány átváltását az új tanúsítványra egy központi telepítési lépésben.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Módosítsa az **összes** **Microsoft. számítási/virtualMachineScaleSets** erőforrás-definíciót – keresse meg a Microsoft. számítás/virtualMachineScaleSets erőforrás-definíciót. Görgessen a "kiadó": "Microsoft. Azure. ServiceFabric" kifejezésre a "virtualMachineProfile" alatt.

    A Service Fabric közzétevő beállításainál ehhez hasonlóan kell megjelennie.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Adja hozzá az új tanúsítvány-bejegyzéseket
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    A tulajdonságoknak most így kell kinéznie
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Ha át szeretné adni **a tanúsítványt**, adja meg az új tanúsítványt elsődlegesként, és helyezze át az aktuális elsődlegest másodlagosként. Ennek eredményeképpen az aktuális tanúsítvány átváltását az új tanúsítványra az egyik központi telepítési lépésben.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    A tulajdonságoknak most így kell kinéznie    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Módosítsa az **összes** **Microsoft. számítási/virtualMachineScaleSets** erőforrás-definíciót – keresse meg a Microsoft. számítás/virtualMachineScaleSets erőforrás-definíciót. Görgessen a "vaultCertificates":, a "OSProfile" alatt. Ehhez hasonlóan kell kinéznie.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adja hozzá a secCertificateUrlValue. használja a következő kódrészletet:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Most az eredményül kapott JSON-nek ehhez hasonlóan kell kinéznie.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Győződjön meg arról, hogy a (z) 4. és 5. lépés megismétlése a sablon összes Nodetypes/Microsoft. számítási/virtualMachineScaleSets erőforrás-definíciójában. Ha kihagyja az egyiket, a tanúsítvány nem lesz telepítve az adott virtuálisgép-méretezési csoporton, és a fürtben előre nem látható eredmények lesznek, beleértve a fürt leállását is (ha nem rendelkezik érvényes tanúsítványokkal, amelyeket a fürt a biztonság érdekében használhat. Ezért a folytatás előtt ellenőrizze a következőt:
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Szerkessze a sablon fájlját, hogy tükrözze a fent hozzáadott új paramétereket
Ha a [git-](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) tárházban található mintát használja a követéshez, megkezdheti a módosítást a minta 5-VM-1-NodeTypes-Secure.parameters_Step2.JS 

Szerkessze a Resource Manager-sablon paraméterét tartalmazó fájlt, adja hozzá a két új paramétert a secCertificateThumbprint és a secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>A sablon üzembe helyezése az Azure-ban

- Most már készen áll a sablon üzembe helyezésére az Azure-ban. Nyisson meg egy Azure PS Version 1 + parancssort.
- Jelentkezzen be az Azure-fiókjába, és válassza ki az adott Azure-előfizetést. Ez egy fontos lépés azon emberek számára, akik több Azure-előfizetéshez férnek hozzá.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Tesztelje a sablont a telepítése előtt. Használja ugyanazt az erőforráscsoportot, amelyben a fürt jelenleg telepítve van.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Telepítse a sablont az erőforráscsoporthoz. Használja ugyanazt az erőforráscsoportot, amelyben a fürt jelenleg telepítve van. Futtassa az New-AzResourceGroupDeployment parancsot. Nem kell megadnia a módot, mert az alapértelmezett érték **növekményes**.

> [!NOTE]
> Ha a mód be van állítva, akkor véletlenül törölheti a sablonban nem szereplő erőforrásokat. Ezt ne használja ebben a forgatókönyvben.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Íme egy kitöltött példa ugyanarra a PowerShellre.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Az üzembe helyezés befejezése után kapcsolódjon a fürthöz az új tanúsítvánnyal, és hajtson végre néhány lekérdezést. Ha tudja, Ezután törölheti a régi tanúsítványt. 

Ha önaláírt tanúsítványt használ, ne felejtse el importálni azokat a helyi tanúsítványtárolójából tanúsítvány-tárolóba.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
A gyors hivatkozás itt a biztonságos fürthöz való kapcsolódásra szolgáló parancs 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
A rövid útmutatóhoz itt találja a fürt állapotának beolvasására szolgáló parancsot.

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Ügyféltanúsítványok üzembe helyezése a fürtön.

Az előző 5. lépésben ismertetett lépéseket követve a kulcstartóból a csomópontokra telepített tanúsítványokat is használhatja. Csak meg kell határoznia és használni a különböző paramétereket.


## <a name="adding-or-removing-client-certificates"></a>Ügyféltanúsítványok hozzáadása vagy eltávolítása

A fürt tanúsítványainak mellett az Ügyféltanúsítványok hozzáadásával Service Fabric fürtön is elvégezheti a felügyeleti műveleteket.

Kétféle ügyféltanúsítványt vehet fel – rendszergazda vagy csak olvasható. Ezek ezután a fürt rendszergazdai műveleteihez és lekérdezési műveleteihez való hozzáférés szabályozására használhatók. Alapértelmezés szerint a rendszer hozzáadja a fürtözött tanúsítványokat az engedélyezett rendszergazdai tanúsítványok listához.

Tetszőleges számú ügyféltanúsítványt is megadhat. Minden hozzáadás/törlés a Service Fabric-fürt konfigurációs frissítését eredményezi.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Ügyféltanúsítványok hozzáadása – rendszergazda vagy Read-Only a portálon keresztül

1. Navigáljon a biztonság szakaszra, és válassza a "+ hitelesítés" gombot a biztonság szakasz tetején.
2. A "hitelesítés hozzáadása" szakaszban válassza a "hitelesítés típusa"-"írásvédett ügyfél" vagy a "rendszergazda ügyfél" lehetőséget.
3. Most válassza ki az engedélyezési módszert. Ez azt jelzi, hogy Service Fabric, hogy a tanúsítványt a tulajdonos neve vagy az ujjlenyomat használatával kell-e keresni. Általánosságban elmondható, hogy nem jó biztonsági gyakorlat a tulajdonos neve engedélyezési metódusának használatára. 

![Ügyféltanúsítvány hozzáadása][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Ügyféltanúsítványok törlése – rendszergazda vagy Read-Only a portál használatával

Ha el szeretne távolítani egy másodlagos tanúsítványt a fürt biztonságához, navigáljon a biztonság szakaszhoz, és válassza a Törlés lehetőséget az adott tanúsítvány helyi menüjében.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Alkalmazás-tanúsítványok hozzáadása virtuálisgép-méretezési csoportokhoz

Az alkalmazásokhoz a fürthöz használt tanúsítvány üzembe helyezéséhez tekintse meg [ezt a PowerShell-parancsfájlt](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Következő lépések
A fürtözéssel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Service Fabric fürt frissítési folyamata és elvárásai](service-fabric-cluster-upgrade.md)
* [Szerepköralapú hozzáférés beállítása az ügyfelek számára](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


