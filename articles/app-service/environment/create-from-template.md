---
title: Beadási ág létrehozása ARM-mel
description: Megtudhatja, hogyan hozhat létre külső vagy ILB App Service környezetet Azure Resource Manager sablon használatával.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15cd0979fdc2468ab50451042cd99a8442470139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148169"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>BeAzure Resource Manageri sablon létrehozása

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service környezetek (ASE-EK) internetről elérhető végponttal vagy végponttal hozhatók létre egy Azure-beli virtuális hálózatban (VNet) található belső címen. Belső végponttal való létrehozáskor a végpontot egy belső terheléselosztó (ILB) nevű Azure-összetevő kapja meg. A belső IP-címekhez tartozó beadást ILB-beadásnak nevezzük. A nyilvános végponttal rendelkező közforrást külső szakszolgáltatásnak nevezik. 

A beAzure Portal vagy egy Azure Resource Manager sablonnal hozhatók létre a kiegészítő szolgáltatás. Ez a cikk végigvezeti a következő lépéseken és szintaxison, amelyekben létre kell hoznia egy külső bevezetőt vagy ILB a Resource Manager-sablonokkal. Ha meg szeretné tudni, hogyan hozhat létre egy központot a Azure Portalban, tekintse meg a [külső][MakeExternalASE] betanító vagy a [ILB][MakeILBASE]beszerzése című részt.

Amikor létrehoz egy bevezetést a Azure Portalban, egyszerre létrehozhatja a VNet, vagy kiválaszthat egy már meglévő VNet a üzembe helyezéséhez. Ha sablonból hoz létre egy beadást, a következővel kell kezdődnie: 

* Egy Resource Manager-VNet.
* Egy alhálózat az adott VNet. A `/24` jövőbeli növekedési és méretezési igények kielégítése érdekében javasoljuk, hogy a rendszer 256-címmel rendelkező, a közszolgálati hálózatra vonatkozó előtagja legyen. A kisegítő lehetőség létrehozása után a méret nem módosítható.
* A VNet erőforrás-azonosítója. Ezt az információt a virtuális hálózat tulajdonságai között található Azure Portalból szerezheti be.
* Az előfizetés, amelyet központilag telepíteni szeretne.
* Az a hely, amelyet üzembe szeretne helyezni.

A kiegészítő környezet létrehozásának automatizálása:

1. Hozzon létre egy sablont egy sablonból. Ha külső beléptetést hoz létre, akkor a lépés után már elkészült. Ha létrehoz egy ILB-kiegészítőt, még néhány dolgot kell tennie.

2. A ILB-előkészítés létrehozása után a rendszer feltölt egy TLS-/SSL-tanúsítványt, amely megfelel az ILB-előtagjai tartománynak.

3. A feltöltött TLS/SSL-tanúsítvány az "alapértelmezett" TLS/SSL-tanúsítványként van hozzárendelve a ILB-alapú adatforráshoz.  Ez a tanúsítvány a ILB-központhoz tartozó, a központhoz rendelt általános gyökértartomány (például:) használata esetén a TLS/SSL-alapú adatforgalomhoz használatos `https://someapp.mycustomrootdomain.com` .


## <a name="create-the-ase"></a>A bekészítés létrehozása
Egy olyan Resource Manager-sablon, amely létrehoz egy bevezetőt és a hozzá tartozó paramétereket tartalmazó fájlt, a GitHubon [egy példán][quickstartasev2create] keresztül érhető el.

Ha ILB-bevezetőt szeretne készíteni, használja a következő Resource Manager-sablonok [példáit][quickstartilbasecreate]. Ez a használati esetet is kielégíti. A *azuredeploy.parameters.js* fájljának legtöbb paramétere közös a ILB-ASE és a külső ASE létrehozásához. Az alábbi lista a speciális megjegyzések paramétereit hívja meg, vagy amelyek egyediek, amikor létrehoz egy ILB-bevezetést:

* *internalLoadBalancingMode*: a legtöbb esetben ez a beállítás 3 értékre van állítva, ami azt jelenti, hogy a 80/443-as porton a HTTP/HTTPS-forgalom, valamint a szolgáltatón futó FTP-szolgáltatás által figyelt vezérlési/adatcsatorna-portok a ILB lefoglalt virtuális hálózat belső címeihez lesznek kötve. Ha ez a tulajdonság 2 értékre van állítva, akkor csak az FTP szolgáltatáshoz kapcsolódó portok (vezérlő-és adatcsatornák) vannak kötve egy ILB-címnek. A HTTP/HTTPS-forgalom a nyilvános VIP-en marad.
* *dnsSuffix*: Ez a paraméter határozza meg a közirányhoz rendelt alapértelmezett gyökértartomány-tartományt. A Azure App Service nyilvános változatában az összes webalkalmazás alapértelmezett legfelső szintű tartománya a *azurewebsites.net*. Mivel a ILB-alapú adatközpont egy ügyfél virtuális hálózatán belül van, nem érdemes a nyilvános szolgáltatás alapértelmezett gyökértartomány-tartományát használni. Ehelyett egy ILB-szolgáltatónak rendelkeznie kell egy alapértelmezett gyökértartomány-tartománnyal, amely logikus a vállalat belső virtuális hálózatán belüli használatra. Előfordulhat például, hogy a contoso Corporation a *Internal-contoso.com* alapértelmezett gyökértartomány-tartományát használja olyan alkalmazások esetében, amelyek csak a contoso virtuális hálózatán belül lesznek feloldhatók és elérhetők. 
* *ipSslAddressCount*: Ez a paraméter automatikusan alapértelmezett értéke a 0 érték a fájlban lévő *azuredeploy.jsban* , mert a ILB ASE csak egyetlen ILB-címnek van. Nincsenek explicit IP-SSL-címek egy ILB-alapú beszabályozáshoz. Ezért a ILB-beadáshoz tartozó IP-SSL-címkészlet értéke csak nulla lehet. Ellenkező esetben kiépítési hiba történik. 

A fájl *azuredeploy.parameters.jsának* kitöltése után hozza létre a kiegészítő szolgáltatást a PowerShell-kódrészlet használatával. Módosítsa a fájl elérési útját úgy, hogy az megfeleljen a számítógép Resource Manager-sablonjának helyeinek. Ne feledje, hogy a Resource Manager-alapú központi telepítési név és az erőforráscsoport neve számára adja meg a saját értékeit:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Egy órát vesz igénybe a bevezetés létrehozásakor. Ezután a bevezető megjelenik a portálon a ASE az üzembe helyezést kiváltó előfizetéshez tartozó listájában.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Az "alapértelmezett" TLS/SSL-tanúsítvány feltöltése és konfigurálása
TLS/SSL-tanúsítványt kell társítani a kiegészítő csomaghoz, amely az alkalmazások TLS-kapcsolatainak létrehozásához használt alapértelmezett TLS/SSL-tanúsítvány. Ha a *Internal-contoso.com* alapértelmezett DNS-utótagja a, a kapcsolathoz a `https://some-random-app.internal-contoso.com` **. internal-contoso.com* érvényes TLS/SSL-tanúsítványra van szükség. 

Szerezzen be egy érvényes TLS/SSL-tanúsítványt belső hitelesítésszolgáltatók használatával, tanúsítvány vásárlása külső kiállítótól vagy önaláírt tanúsítvány használatával. A TLS/SSL-tanúsítvány forrástól függetlenül a következő tanúsítvány-attribútumokat megfelelően kell konfigurálni:

* **Subject**: ezt az attribútumot a **. your-root-domain-here.com* értékre kell állítani.
* **Tulajdonos alternatív neve**: ebben az attribútumban a **. your-root-domain-here.com* és a **. SCM.your-root-domain-here.com* is szerepelnie kell. Az egyes alkalmazásokhoz társított SCM/kudu-helyhez tartozó TLS-kapcsolatok az űrlap *Your-app-Name.SCM.your-root-domain-here.com*-címeit használják.

Ha egy érvényes TLS/SSL-tanúsítvánnyal rendelkezik, két további előkészítési lépésre van szükség. A TLS/SSL-tanúsítvány konvertálása/mentése. pfx-fájlként. Ne feledje, hogy a. pfx-fájlnak tartalmaznia kell az összes köztes és főtanúsítványt. Jelszóval gondoskodjon a védelméről.

A. pfx-fájlt Base64-karakterlánccá kell konvertálni, mert a TLS/SSL-tanúsítvány egy Resource Manager-sablon használatával lett feltöltve. Mivel a Resource Manager-sablonok szövegfájlok, a. pfx-fájlt Base64 karakterlánccá kell konvertálni. Így a sablon paraméterként is szerepelhet.

Használja az alábbi PowerShell-kódrészletet a következőhöz:

* Önaláírt tanúsítvány létrehozása.
* Exportálja a tanúsítványt. pfx-fájlként.
* Alakítsa át a. pfx-fájlt Base64 kódolású karakterlánccá.
* Mentse a Base64 kódolású karakterláncot egy külön fájlba. 

Ez a Base64 kódolású PowerShell-kód a [PowerShell-parancsfájlok blogjában][examplebase64encoding]lett kialakítva:

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

Miután a TLS/SSL-tanúsítvány sikeresen létrejött és át lett alakítva Base64 kódolású karakterlánccá, a példa Resource Manager-sablonnal [konfigurálja az alapértelmezett SSL-tanúsítványt][quickstartconfiguressl] a githubon. 

A *azuredeploy.parameters.js* fájljának paramétereit itt találja:

* *appServiceEnvironmentName*: a konfigurált ILB-előállítók neve.
* *existingAseLocation*: az a szöveges karakterlánc, amely azt az Azure-régiót tartalmazza, ahol a ILB-központot telepítették.  Például: "az USA déli középső régiója".
* *pfxBlobString*: a. pfx fájl bementi-kódolású karakterláncának ábrázolása. Használja a korábban bemutatott kódrészletet, és másolja a "exportedcert. pfx. b64" fájlban található karakterláncot. Illessze be a értéket a *pfxBlobString* attribútum értékeként.
* *Password (jelszó*): a. pfx fájl védelméhez használt jelszó.
* *certificateThumbprint*: a Tanúsítvány ujjlenyomata. Ha ezt az értéket a PowerShellből kéri le (például *$Certificate.* A korábbi kódrészletből származó ujjlenyomatot), az értéket használhatja. Ha az értéket a Windows-tanúsítvány párbeszédpanelen másolja, ne felejtse el kihúzni a felesleges szóközöket. A *certificateThumbprint* hasonlóan kell kinéznie, mint a AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: a tanúsítvány azonosítására szolgáló, saját maga által választott, felhasználóbarát karakterlánc-azonosító. A nevet a rendszer a TLS/SSL-tanúsítványt képviselő *Microsoft. Web/Certificates* entitás egyedi Resource Manager-azonosítójának részeként használja. A névnek a következő utótaggal *kell* végződnie: \_ yourASENameHere_InternalLoadBalancingASE. Az Azure Portal ezt az utótagot használja jelzőként, hogy a tanúsítvány egy ILB-kompatibilis bemutatók biztonságossá tételére szolgál.

Itt látható egy rövidített példa *azuredeploy.parameters.json* :

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

A fájl *azuredeploy.parameters.jsának* kitöltése után konfigurálja az alapértelmezett TLS/SSL-tanúsítványt a PowerShell-kódrészlet használatával. Módosítsa a fájlelérési utakat úgy, hogy az megfeleljen a számítógépen található Resource Manager-Sablonfájlok helyének. Ne feledje, hogy a Resource Manager-alapú központi telepítési név és az erőforráscsoport neve számára adja meg a saját értékeit:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A módosítás alkalmazása körülbelül 40 percet vesz igénybe. Ha például egy alapértelmezett méretű, két előtéri végpontot használó, a sablon körülbelül egy óra, és 20 percet vesz igénybe. Amíg a sablon fut, a kiszolgáló nem méretezhető.  

A sablon befejeződése után a ILB-beadási csomag alkalmazásai HTTPS-kapcsolaton keresztül érhetők el. A kapcsolatok az alapértelmezett TLS/SSL-tanúsítvány használatával biztonságosak. Az alapértelmezett TLS/SSL-tanúsítvány akkor használatos, ha az ILB-szolgáltatón lévő alkalmazások az alkalmazás neve és az alapértelmezett állomásnév együttes használatával vannak kezelve. Például `https://mycustomapp.internal-contoso.com` a **. internal-contoso.com* alapértelmezett TLS/SSL-tanúsítványát használja.

Akárcsak a nyilvános több-bérlős szolgáltatáson futó alkalmazások esetében, a fejlesztők egyéni állomásnévket állíthatnak be az egyes alkalmazásokhoz. Emellett egyedi SNI TLS/SSL-tanúsítvány-kötéseket is beállíthat az egyes alkalmazásokhoz.

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##
Kétféle verzió érhető el az App Service Environment szolgáltatáshoz: ASEv1 és ASEv2. A fenti információ az ASEv2 verzión alapul. Ebben a szakaszban az ASEv1 és ASEv2 különbségeiről olvashat.

A ASEv1-ben manuálisan kezelheti az összes erőforrást. Ebbe beletartoznak az előtérrendszerek, a feldolgozók, valamint IP-alapú SSL esetén az IP-címek is. A App Service tervének méretezése előtt ki kell bővíteni a használni kívánt munkavégző készletet.

Az ASEv1 díjszabása eltér az ASEv2-étől. Az ASEv1 esetében minden lefoglalt vCPU után fizet. Ebbe beletartozik a vCPU, amelyek az előfizetéseket és a munkaterheléseket nem működtető feladatokhoz használatosak. Az ASEv1 esetében egy ASE alapértelmezett maximális skálázhatósága összesen 55 gazdagép. Ez tartalmazza a feldolgozókat és az előtérrendszereket is. Az ASEv1 egyik előnye az, hogy klasszikus, illetve Resource Manager virtuális hálózatokban is üzembe helyezhető. Az ASEv1 verzióról további információt az [App Service Environment v1 bemutatása][ASEv1Intro] témakörben találhat.

Ha Resource Manager-sablonnal szeretne ASEv1 létrehozni, tekintse meg a [ILB bevezető v1 Resource Manager-sablonnal történő létrehozását][ILBASEv1Template]ismertető témakört.


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