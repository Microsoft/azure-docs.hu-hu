---
title: ASE létrehozása ARM-sel
description: Megtudhatja, hogyan hozhat létre külső vagy ILB-App Service egy új Azure Resource Manager használatával.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 10482538c819f9713e9940cffbeb5a1f966ddcf5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832049"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>ASE létrehozása egy Azure Resource Manager sablonnal

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service környezetek (AS-k) egy internetről elérhető végponttal vagy egy Azure-beli virtuális hálózat (VNet) belső címére vonatkozó végponttal is létre lehet hozva. Belső végponttal létrehozva ezt a végpontot egy Belső terheléselosztás (ILB) nevű Azure-összetevő biztosítja. A belső IP-címen az ASE-t ILB ASE-nek nevezzük. A nyilvános végponttal az ASE-t külső ASE-nek nevezzük. 

Az ASE-t a Azure Portal vagy egy Azure Resource Manager lehet létrehozni. Ez a cikk végigvezeti a külső ASE vagy ILB ASE létrehozásához szükséges lépéseken és szintaxison, Resource Manager sablonokkal. Az ASE környezetben történő létrehozásáról a Külső [][MakeExternalASE] ASE létrehozása vagy az [ILB ASE][MakeILBASE]létrehozása Azure Portal olvashat.

Amikor létrehoz egy ASE-t a Azure Portal, létrehozhatja a virtuális hálózatát egyidejűleg, vagy kiválaszthat egy már létrehozott VNetet, amelybe az üzembe helyezés történik. Amikor sablonból hoz létre ASE-t, a következővel kell kezdenie: 

* Egy Resource Manager virtuális hálózat.
* A virtuális hálózat egyik alhálózata. Javasoljuk, hogy a jövőbeli növekedés és méretezési igények kielégítése érdekében 256 címből áll az `/24` ASE-alhálózat mérete. Az ASE létrehozása után nem módosíthatja a méretet.
* A virtuális hálózat erőforrás-azonosítója. Ezt az információt a virtuális hálózat Azure Portal alatt található virtuális gépekről kaphatja meg.
* Az előfizetés, amelybe az üzembe helyezést szeretné helyezni.
* Az a hely, ahol az üzembe helyezést el szeretné helyezni.

Az ASE létrehozásának automatizálása:

1. Hozza létre az ASE-t egy sablonból. Ha külső ASE-t hoz létre, akkor ezt a lépést követően már nem kell tovább lépnie. Ha ILB ASE-t hoz létre, van még néhány további dolog.

2. Az ILB ASE létrehozása után egy, az ILB ASE-tartománynak megfelelő TLS-/SSL-tanúsítvány lesz feltöltve.

3. A feltöltött TLS-/SSL-tanúsítvány "alapértelmezett" TLS-/SSL-tanúsítványként van hozzárendelve az ILB ASE-hez.  Ez a tanúsítvány az ILB ASE-ben található alkalmazások TLS/SSL-forgalmára használatos, ha az ASE-hez rendelt közös gyökértartományt használják (például `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Az ASE létrehozása
Egy Resource Manager sablon, amely létrehoz egy ASE-t és a hozzá tartozó paraméterfájlt, elérhető [egy][quickstartasev2create] GitHub-példában.

Ha ILB ASE-t szeretne létrehozni, használja az alábbi Resource Manager [példákat.][quickstartilbasecreate] Az esethez igazak. A fájlban található *azuredeploy.parameters.js* az ILB AE-k és a külső ATA-k létrehozása során gyakran használt paraméterek. Az alábbi lista az ILB ASE létrehozásakor kiemelt vagy egyedi paramétereket sorolja fel:

* *internalLoadBalancingMode:* A legtöbb esetben állítsa ezt 3-ra, ami azt jelenti, hogy a 80/443-as porton a HTTP/HTTPS-forgalom, valamint az ASE FTP-szolgáltatása által a vezérlő/adatcsatorna portjai egy ILB által lefoglalt belső virtuális hálózati címhez lesznek kötve. Ha ez a tulajdonság 2-re van állítva, csak az FTP-szolgáltatáshoz kapcsolódó portok (vezérlők és adatcsatornák) vannak ILB-címhez kötve. A HTTP/HTTPS-forgalom a nyilvános VIP-címen marad.
* *dnsSuffix:* Ez a paraméter határozza meg az ASE-hez rendelt alapértelmezett gyökértartományt. A webalkalmazások nyilvános változatában Azure App Service webalkalmazások alapértelmezett gyökértartománya a *azurewebsites.net.* Mivel az ILB ASE egy ügyfél virtuális hálózatán belül található, nincs értelme a nyilvános szolgáltatás alapértelmezett gyökértartományát használni. Ehelyett az ILB ASE-nek olyan alapértelmezett gyökértartományt kell használnia, amely a vállalat belső virtuális hálózatán belül használható. A Contoso Corporation például a internal-contoso.com  alapértelmezett gyökértartományát használhatja olyan alkalmazásokhoz, amelyek feloldhatók és csak a Contoso virtuális hálózatán belül érhetők el. 
* *ipSslAddressCount:* Ez a paraméter automatikusan 0 értéket ad meg a *azuredeploy.jsfájlban, mivel* az ILB AE-k csak egyetlen ILB-címmel bírnak. Az ILB ASE nem tartalmaz explicit IP-SSL-címeket. Ezért az ILB ASE IP-SSL-címkészletét nullára kell állítani. Ellenkező esetben kiépítési hiba történik. 

Miután a *azuredeploy.parameters.jsmeg* van töltve, hozza létre az ASE-t a PowerShell-kódrészlet használatával. Módosítsa úgy a fájl elérési útját, hogy Resource Manager sablonfájl helyei a gépen. Ne felejtsen el saját értékeket Resource Manager üzemelő példány és az erőforráscsoport neve számára:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Az ASE létrehozása körülbelül egy órát vesz igénybe. Ezután az ASE megjelenik a portálon az üzembe helyezést kiváltó előfizetés ASE-k listájában.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Az "alapértelmezett" TLS-/SSL-tanúsítvány feltöltése és konfigurálása
Az ASE-hez TLS/SSL-tanúsítványt kell "alapértelmezett" TLS/SSL-tanúsítványként társítva, amely az alkalmazásokhoz való TLS-kapcsolatok létrehozására használható. Ha az ASE alapértelmezett DNS-utótagja *internal-contoso.com*, a kapcsolathoz olyan TLS/SSL-tanúsítványra van szükség, amely `https://some-random-app.internal-contoso.com` a **.internal-contoso.com.* 

Szerezzen be érvényes TLS-/SSL-tanúsítványt belső hitelesítésszolgáltató használatával, tanúsítvány vásárlása külső kiállítótól vagy önaírt tanúsítvány használatával. A TLS-/SSL-tanúsítvány forrásától függetlenül a következő tanúsítványattribútumokat kell megfelelően konfigurálni:

* **Tárgy:** Ezt az attribútumot **.your-root-domain-here.com.*
* **Tulajdonos alternatív neve:** Ennek az attribútumnak tartalmaznia kell a **.your-root-domain-here.com* és a **.scm.your-root-domain-here.com.* Az egyes alkalmazásokhoz társított SCM/Kudu-hely TLS-kapcsolatai a következő *űrlapcímet your-app-name.scm.your-root-domain-here.com.*

Érvényes TLS-/SSL-tanúsítvány esetén két további előkészítő lépésre van szükség. Konvertálja/mentse a TLS-/SSL-tanúsítványt .pfx fájlként. Ne feledje, hogy a .pfx fájlnak tartalmaznia kell az összes köztes és főtanúsítványt. Jelszóval gondoskodjon a védelméről.

A .pfx fájlt base64-sztringgé kell konvertálni, mert a TLS-/SSL-tanúsítvány feltöltése egy Resource Manager sablon használatával történik. Mivel Resource Manager sablonok szöveges fájlok, a .pfx fájlt base64-sztringgé kell konvertálni. Így a sablon paramétereként is használható.

A következő PowerShell-kódrészlet használatával:

* Hozzon létre egy önaírt tanúsítványt.
* Exportálja a tanúsítványt .pfx fájlként.
* Konvertálja a .pfx fájlt base64 kódolású sztringgé.
* Mentse a base64 kódolású sztringet egy külön fájlba. 

A base64 kódoláshoz ezt a PowerShell-kódot a [PowerShell-parancsfájlok blogja adaptálta:][examplebase64encoding]

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

A TLS-/SSL-tanúsítvány sikeres létrehozása és base64 kódolású sztringgé konvertálása után használja az Resource Manager-sablont Az alapértelmezett [SSL-tanúsítvány][quickstartconfiguressl] konfigurálása a GitHubon. 

A fájlban *azuredeploy.parameters.jsparaméterek* itt vannak felsorolva:

* *appServiceEnvironmentName:* A konfigurált ILB ASE neve.
* *existingAseLocation:* Az ILB ASE üzembe helyezésének Azure-régióját tartalmazó szöveges sztring.  Például: "USA déli középső középső országa".
* *pfxBlobString:* A .pfx fájl based64 kódolású sztringre ábrázolása. Használja a korábban bemutatott kódrészletet, és másolja ki az "exportedcert.pfx.b64" fájlban található sztringet. Illessze be a fájlba a *pfxBlobString attribútum értékeként.*
* *password*: A .pfx fájl biztonságossá tára használt jelszó.
* *certificateThumbprint:* A tanúsítvány ujjlenyomata. Ha ezt az értéket a PowerShellből olvassa be (például: *$certificate. Ujjlenyomat a* korábbi kódrészletből), az értéket használhatja. Ha a Windows-tanúsítvány párbeszédpanelről másolja ki az értéket, ne felejtse el a felesleges szóközöket is kivasalni. A *certificateThumbprint* fájlnak a következő hasonlónak kell lennie: AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName:* A tanúsítvány identitásához választott felhasználóbarát sztringazonosító. A név a TLS-/SSL-tanúsítványt képviselő *Microsoft.Web/certificates* Resource Manager egyedi tanúsítványazonosító részeként használatos. A *névnek a* következő utótaggal kell végződnie: \_ yourASENameHere_InternalLoadBalancingASE. A Azure Portal ezt az utótagot használja annak jelzéseként, hogy a rendszer a tanúsítványt használja az ILB-kompatibilis ASE biztonságossá tenni.

Itt látható aazuredeploy.parameters.js *rövidített* példája:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

A *fájlazuredeploy.parameters.js* után konfigurálja az alapértelmezett TLS-/SSL-tanúsítványt a PowerShell-kódrészlet használatával. Módosítsa úgy a fájl elérési útját, hogy Resource Manager sablonfájlokat a számítógépen. Ne felejtsen el saját értékeket Resource Manager üzemelő példány és az erőforráscsoport neve számára:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A módosítás ASE-előoldalonként körülbelül 40 percet vesz igénybe. Például egy alapértelmezett méretű, két előoldalt használó ASE esetén a sablon befejezése körülbelül egy órát és 20 percet vesz igénybe. Amíg a sablon fut, az ASE nem méretezhető.  

A sablon befejezése után az ILB ASE-ben található alkalmazások HTTPS-kapcsolaton keresztül érhetők el. A kapcsolatokat az alapértelmezett TLS-/SSL-tanúsítvány védi. Az alapértelmezett TLS-/SSL-tanúsítvány akkor használatos, ha az ILB ASE-ben az alkalmazások az alkalmazásnév és az alapértelmezett gazdagépnév kombinációjával vannak címzve. A például `https://mycustomapp.internal-contoso.com` az alapértelmezett TLS/SSL-tanúsítványt használja a **.internal-contoso.com.*

A fejlesztők azonban a nyilvános több-bérlős szolgáltatásban futó alkalmazásokhoz hasonló módon egyéni állomásneveket is konfigurálnak az egyes alkalmazásokhoz. Emellett egyedi SNI TLS-/SSL-tanúsítványkötéseket is konfigurálhat az egyes alkalmazásokhoz.

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##
Kétféle verzió érhető el az App Service Environment szolgáltatáshoz: ASEv1 és ASEv2. A fenti információ az ASEv2 verzión alapul. Ebben a szakaszban az ASEv1 és ASEv2 különbségeiről olvashat.

Az ASEv1-ben az összes erőforrást manuálisan kezelheti. Ebbe beletartoznak az előtérrendszerek, a feldolgozók, valamint IP-alapú SSL esetén az IP-címek is. Mielőtt horizontálisan felskálazhatja a App Service, horizontálisan fel kell skáláznunk a feldolgozókészletet, amely számára a gazdagépet szeretné.

Az ASEv1 díjszabása eltér az ASEv2-étől. Az ASEv1 esetében minden lefoglalt vCPU után fizet. Ide tartoznak az előterek vagy a számítási feladatokat nem üzemeltető dolgozók számára használt vCPU-k. Az ASEv1 esetében egy ASE alapértelmezett maximális skálázhatósága összesen 55 gazdagép. Ez tartalmazza a feldolgozókat és az előtérrendszereket is. Az ASEv1 egyik előnye az, hogy klasszikus, illetve Resource Manager virtuális hálózatokban is üzembe helyezhető. Az ASEv1 verzióról további információt az [App Service Environment v1 bemutatása][ASEv1Intro] témakörben találhat.

Ha asev1-et Resource Manager sablonnal, lásd: [ILB ASE v1][ILBASEv1Template]létrehozása Resource Manager sablonnal.


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
