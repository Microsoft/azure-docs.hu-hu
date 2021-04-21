---
title: ILB ASE v1 létrehozása
description: Hozzon létre App Service egy belső terheléselosztási (ILB ASE) környezettel. Ez a dokumentum csak az örökölt V1 ASE-t felhasználók számára biztosítja.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a6cc1cae640b97ecb3d95ee1e4f8ec34750e32d2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833003"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>ILB ASE létrehozása Azure Resource Manager-sablonokkal

> [!NOTE] 
> Ez a cikk a App Service Environment v1-ről szól. A verziónak van egy újabb verziója App Service Environment amely könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a Bevezetés a [App Service Environment.](intro.md)
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
App Service környezetek nyilvános VIRTUÁLIS IP-cím helyett belső virtuális hálózattal is létre lehet hozni.  Ezt a belső címet egy belső terheléselosztási (ILB) nevű Azure-összetevő biztosítja.  Az ILB ASE a fürt használatával Azure Portal.  Automatizálással és sablonokkal is Azure Resource Manager létre.  Ez a cikk végigvezeti az ILB ASE sablonokkal való létrehozásához szükséges lépéseken és Azure Resource Manager lépéseken.

Az ILB ASE létrehozásának automatizálása három lépésből áll:

1. Először az alap ASE jön létre egy virtuális hálózatban egy belső terheléselosztási cím használatával nyilvános VIRTUÁLIS IP-cím helyett.  Ennek a lépésnek a részeként egy gyökértartománynév lesz hozzárendelve az ILB ASE-hez.
2. Az ILB ASE létrehozása után egy TLS-/SSL-tanúsítvány lesz feltöltve.  
3. A feltöltött TLS-/SSL-tanúsítvány explicit módon hozzá van rendelve az ILB ASE-hez alapértelmezett TLS-/SSL-tanúsítványként.  Ez a TLS-/SSL-tanúsítvány lesz használva az ILB ASE-alkalmazások TLS-forgalmához, ha az alkalmazásokat az ASE-hez rendelt közös gyökértartomány használatával címzik (pl. `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>Az alap ILB ASE létrehozása
Egy példa Azure Resource Manager sablon és a hozzá tartozó paraméterfájl itt érhető el a [GitHubon.][quickstartilbasecreate]

A fájlban található paraméterek *azuredeploy.parameters.js* gyakoriak mind az ILB AS-k, mind a nyilvános VIP-hez kötött AVE-k létrehozásakor.  Az alábbi lista az ILB ASE létrehozásakor kiemelt vagy egyedi paramétereket sorol fel:

* *internalLoadBalancingMode:* A legtöbb esetben állítsa ezt 3-ra, ami azt jelenti, hogy a 80/443-as porton a HTTP/HTTPS-forgalom, valamint az ASE FTP-szolgáltatása által a vezérlő/adatcsatorna portjai egy ILB által lefoglalt belső virtuális hálózathoz lesznek kötve.  Ha ez a tulajdonság 2-re van állítva, akkor csak az FTP-szolgáltatáshoz kapcsolódó portok (vezérlők és adatcsatornák) lesznek ILB-címhez kötve, a HTTP/HTTPS-forgalom pedig a nyilvános VIP-címen marad.
* *dnsSuffix:* Ez a paraméter határozza meg az alapértelmezett gyökértartományt, amely hozzá lesz rendelve az ASE-hez.  A webalkalmazások nyilvános változatában Azure App Service webalkalmazások alapértelmezett gyökértartománya a *azurewebsites.net.*  Mivel azonban az ILB ASE az ügyfél virtuális hálózatán belül található, nincs értelme a nyilvános szolgáltatás alapértelmezett gyökértartományát használni.  Ehelyett az ILB ASE-nek olyan alapértelmezett gyökértartományt kell használnia, amely a vállalat belső virtuális hálózatán belül használható.  Egy feltételezett Contoso Corporation például a internal-contoso.com  alapértelmezett gyökértartományát használhatja olyan alkalmazásokhoz, amelyek csak a Contoso virtuális hálózatán belül feloldhatók és hozzáférhetők. 
* *ipSslAddressCount:* Ez a paraméter automatikusan 0 értékre van állítva a fájlazuredeploy.jsparaméterében, mivel az ILB *AE-k* csak egyetlen ILB-címmel bírnak.  Az ILB ASE-hez nem tartozik explicit IP-SSL-cím, ezért az ILB ASE IP-SSL-címkészletét nullára kell állítani, különben kiépítési hiba történik. 

Miután a *azuredeploy.parameters.js* meg lett töltve az ILB ASE-hez, az ILB ASE a következő PowerShell-kódrészlettel is létre lehet hozva.  Módosítsa úgy a fájl elérési útját, hogy Azure Resource Manager sablonfájlokat a számítógépen.  Ne felejtse el a saját értékeit is meg Azure Resource Manager üzemelő példány és az erőforráscsoport neve számára.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A Azure Resource Manager sablon beküldtét követően az ILB ASE létrehozása néhány órát is igénybe fog venni.  A létrehozás befejezése után az ILB ASE megjelenik a portál felhasználói felületében a App Service környezetek listájában az üzembe helyezést kiváltó előfizetéshez.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Az "alapértelmezett" TLS-/SSL-tanúsítvány feltöltése és konfigurálása
Az ILB ASE létrehozása után az ASE-hez TLS-/SSL-tanúsítványt kell hozzárendelni alapértelmezett TLS/SSL-tanúsítványként az alkalmazásokhoz való TLS/SSL-kapcsolatok létrehozásához.  Folytatva a feltételezett Contoso Corporation-példát, ha az ASE alapértelmezett *DNS-utótagja internal-contoso.com*, akkor a kapcsolatához olyan TLS/SSL-tanúsítványra van szükség, amely *`https://some-random-app.internal-contoso.com`* a **.internal-contoso.com.* 

Érvényes TLS-/SSL-tanúsítvány beszerzésének számos módja van, beleértve a belső hitelesítésszolgáltatót, a tanúsítvány külső kiállítótól való megvásárlását és az önaírt tanúsítvány használatát.  A TLS/SSL-tanúsítvány forrásától függetlenül a következő tanúsítványattribútumokat kell megfelelően konfigurálni:

* *Tárgy:* Ezt az attribútumot **.your-root-domain-here.com*
* *Tulajdonos alternatív neve:* Ennek az attribútumnak tartalmaznia kell a **.your-root-domain-here.com* és a **.scm.your-root-domain-here.com.*  A második bejegyzés oka az, hogy az egyes alkalmazásokhoz társított SCM/Kudu-webhelyhez tartozó TLS-kapcsolatok a következő űrlapcím *használatával your-app-name.scm.your-root-domain-here.com.*

Érvényes TLS-/SSL-tanúsítvány esetén két további előkészítő lépésre van szükség.  A TLS-/SSL-tanúsítványt .pfx fájlként kell konvertálni/menteni.  Ne feledje, hogy a .pfx fájlnak tartalmaznia kell az összes köztes és főtanúsítványt, valamint egy jelszóval kell biztonságosnak lennie.

Ezután az eredményül kapott .pfx fájlt base64-sztringgé kell konvertálni, mert a TLS-/SSL-tanúsítvány egy Azure Resource Manager sablonnal lesz feltöltve.  Mivel Azure Resource Manager sablonok szöveges fájlok, a .pfx fájlt base64-sztringgé kell konvertálni, hogy a sablon paramétereként is használható legyen.

Az alábbi PowerShell-kódrészlet egy példát mutat be egy önaírt tanúsítvány előállítására, a tanúsítvány .pfx-fájlként való exportálására, a .pfx fájl base64 kódolású sztringgé konvertálására, majd a base64 kódolású sztring külön fájlba mentésre.  A base64 kódoláshoz a PowerShell-kódot a [PowerShell-parancsfájlok blogja adaptálta.][examplebase64encoding]

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

A TLS/SSL-tanúsítvány sikeres létrehozása és base64 kódolású sztringgé konvertálása után használható a GitHubon az alapértelmezett [TLS/SSL-tanúsítvány][configuringDefaultSSLCertificate] konfigurálásához használható példa Azure Resource Manager-sablon.

A fájlban *azuredeploy.parameters.jsparaméterek* alább vannak felsorolva:

* *appServiceEnvironmentName:* A konfigurált ILB ASE neve.
* *existingAseLocation:* Az ILB ASE üzembe helyezésének Azure-régióját tartalmazó szöveges sztring.  Például: "USA déli középső középső országa".
* *pfxBlobString:* A .pfx fájl based64 kódolású sztringes ábrázolása.  A korábban bemutatott kódrészlet használatával másolja ki az "exportedcert.pfx.b64" fájlban található sztringet, és illessze be a *pfxBlobString attribútum értékeként.*
* *password*: A .pfx fájl biztonságossá tára használt jelszó.
* *certificateThumbprint:* A tanúsítvány ujjlenyomata.  Ha ezt az értéket a PowerShellből olvassa be (például: *$certificate. Ujjlenyomat a* korábbi kódrészletből), az értéket használhatja.  Ha azonban a Windows-tanúsítvány párbeszédpanelről másolja ki az értéket, ne felejtse el a felesleges szóközöket is kiveszni.  A *certificateThumbprint* fájlnak így kell kinéznie: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName:* A tanúsítvány identitásához választott felhasználóbarát sztringazonosító.  A név a TLS-/SSL-tanúsítványt Azure Resource Manager *Microsoft.Web/certificates* entitás egyedi azonosítójának részeként használatos.  A **névnek a** következő utótaggal kell végződnie:  \_ yourASENameHere_InternalLoadBalancingASE.  A portál ezt az utótagot használja annak jelzéseként, hogy a rendszer a tanúsítványt használja az ILB-kompatibilis ASE biztonságossá tétele érdekében.

Alább látható egy rövidített *azuredeploy.parameters.js, amely be* van ítve:

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

Miután *azuredeploy.parameters.js* fájlban található adatok ki vannak töltve, az alapértelmezett TLS-/SSL-tanúsítvány a következő PowerShell-kódrészlettel konfigurálható.  Módosítsa úgy a fájl elérési útját, hogy Azure Resource Manager sablonfájlokat a számítógépen.  Ne felejtsen el saját értékeket is meg Azure Resource Manager üzemelő példány nevéhez és az erőforráscsoport nevéhez.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A sablon Azure Resource Manager után körülbelül 40 percet fog igénybe venni ASE-előlaponként a módosítás alkalmazása.  Ha például egy alapértelmezett méretű ASE két előoldalt használ, a sablon befejezése körülbelül egy órát és 20 percet fog igénybe venni.  Amíg a sablon fut, az ASE nem skálázható.  

Ha a sablon elkészült, az ILB ASE-ben található alkalmazások HTTPS-kapcsolaton keresztül érhetők el, és a kapcsolatok az alapértelmezett TLS-/SSL-tanúsítvány használatával lesznek biztonságosak.  Az alapértelmezett TLS-/SSL-tanúsítványt akkor használja a rendszer, ha az ILB ASE-ben az alkalmazások az alkalmazásnév és az alapértelmezett gazdanév kombinációjával vannak címzve.  Például az *`https://mycustomapp.internal-contoso.com`* alapértelmezett TLS/SSL-tanúsítványt használná a **.internal-contoso.com.*

A fejlesztők azonban a nyilvános több-bérlős szolgáltatásban futó alkalmazásokhoz hasonló módon egyéni állomásneveket is konfigurálnak az egyes alkalmazásokhoz, majd egyedi SNI TLS/SSL-tanúsítványkötéseket konfigurálnak az egyes alkalmazásokhoz.  

## <a name="getting-started"></a>Első lépések
A App Service használatának első App Service Environment [](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

