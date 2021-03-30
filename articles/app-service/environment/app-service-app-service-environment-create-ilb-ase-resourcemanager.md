---
title: ILB bemutató v1 létrehozása
description: Hozzon létre egy App Service környezetet belső terheléselosztó (ILB-t). Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2a03b791f37868010e107214ddcb7cf42174e4e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85833553"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>ILB ASE létrehozása Azure Resource Manager-sablonokkal

> [!NOTE] 
> Ez a cikk a App Service Environment v1-es verzióról szól. A App Service Environment újabb verziója könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a [app Service Environment bevezetésével](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
App Service környezetek nyilvános VIP helyett virtuális hálózattal hozhatók létre belső IP-cím használatával.  Ezt a belső internetcímet egy belső terheléselosztó (ILB) nevű Azure-összetevő biztosít.  Az ILB-előállítók a Azure Portal használatával hozhatók létre.  Az Automation használatával Azure Resource Manager sablonok segítségével is létrehozhatók.  Ez a cikk végigvezeti a ILB beAzure Resource Manager-sablonokkal való létrehozásához szükséges lépéseken és szintaxison.

Az ILB-előkészítők létrehozásának automatizálása három lépésből áll:

1. Először az alapszintű beadást egy virtuális hálózatban hozza létre a rendszer a nyilvános VIP helyett belső terheléselosztó-cím használatával.  Ennek a lépésnek a részeként a rendszer egy gyökértartomány nevét rendeli hozzá a ILB-központhoz.
2. Miután létrejött a ILB-alapú bekészítés, TLS/SSL-tanúsítvány kerül feltöltésre.  
3. A feltöltött TLS/SSL-tanúsítvány explicit módon hozzá van rendelve a ILB a "default" TLS/SSL-tanúsítványhoz.  Ezt a TLS/SSL-tanúsítványt fogja használni a rendszer a TLS-forgalomhoz a ILB-központon futó alkalmazásokhoz, ha az alkalmazások a központhoz rendelt általános gyökértartomány használatával vannak kezelve (például `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>Az alapszintű ILB létrehozása
Egy példa Azure Resource Manager sablonra és a hozzá tartozó Parameters fájlra a GitHubon [itt][quickstartilbasecreate]érhetők el.

A *azuredeploy.parameters.js* fájljában található paraméterek többsége közös a ILB-ASE, valamint a nyilvános VIP-hez kötött ASE létrehozásához.  Az alábbi lista a speciális megjegyzések paramétereit hívja meg, vagy amelyek egyediek a ILB bevezetésének létrehozásakor:

* *internalLoadBalancingMode*: a legtöbb esetben ez a 3 értékre van állítva, ami azt jelenti, hogy mind a http-, mind a https-forgalom a 80/443-as porton, valamint a szolgáltató által az FTP szolgáltatás által figyelt vezérlési/adatcsatorna-portok a ILB lefoglalt virtuális hálózat belső címeihez lesznek kötve.  Ha ez a tulajdonság 2 értékre van állítva, akkor csak az FTP szolgáltatáshoz kapcsolódó portok (vezérlő-és adatcsatornák) lesznek kötve egy ILB-címhez, míg a HTTP/HTTPS-forgalom a nyilvános VIP-en marad.
* *dnsSuffix*: Ez a paraméter határozza meg az alapértelmezett legfelső szintű tartományt, amelyet a rendszer a közirányhoz rendel.  A Azure App Service nyilvános változatában az összes webalkalmazás alapértelmezett legfelső szintű tartománya a *azurewebsites.net*.  Mivel azonban egy ILB-beszállító egy ügyfél virtuális hálózatán belül van, nem érdemes a nyilvános szolgáltatás alapértelmezett gyökértartomány-tartományát használni.  Ehelyett egy ILB-szolgáltatónak rendelkeznie kell egy alapértelmezett gyökértartomány-tartománnyal, amely logikus a vállalat belső virtuális hálózatán belüli használatra.  Előfordulhat például, hogy egy feltételezett contoso Corporation a *Internal-contoso.com* alapértelmezett gyökértartomány-tartományát használja azon alkalmazások esetében, amelyek célja csak a contoso virtuális hálózatán belül lehet feloldható és elérhető. 
* *ipSslAddressCount*: Ez a paraméter automatikusan alapértelmezett értéke a 0 érték a fájlban lévő *azuredeploy.jsban* , mert a ILB ASE csak egyetlen ILB-címnek van.  Nincsenek explicit IP-SSL-címek egy ILB-előállítók számára, ezért a ILB-adáshoz tartozó IP-SSL-címkészlet értéke nulla, ellenkező esetben kiépítési hiba történik. 

Miután betöltötte a *azuredeploy.parameters.jsa* fájlból egy ILB-előállítók számára, a következő PowerShell-kódrészlettel hozható létre a ILB-betöltő.  Módosítsa a fájlelérési utakat úgy, hogy megegyezzenek a Azure Resource Manager sablonfájlokat a gépen.  Ne feledje, hogy adja meg a saját értékeit a Azure Resource Manager központi telepítési név és az erőforráscsoport neve számára.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A Azure Resource Manager-sablon elküldése után eltarthat néhány órát, amíg létre nem jön a ILB bevezetés.  A létrehozás befejezése után a ILB bevezetője megjelenik a portálon, amely a központi telepítést kiváltó előfizetéshez tartozó App Service környezetek listájában jelenik meg.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Az "alapértelmezett" TLS/SSL-tanúsítvány feltöltése és konfigurálása
Miután létrejött a ILB-alapú bekapcsolás, TLS/SSL-tanúsítványt kell társítani a közszolgáltatóhoz, mint az "alapértelmezett" TLS/SSL-tanúsítvány, amellyel TLS/SSL-kapcsolatok hozhatók létre az alkalmazásokhoz.  Folytassa a feltételezett contoso Corporation példával, ha a *Internal-contoso.com* alapértelmezett DNS-utótagja a *`https://some-random-app.internal-contoso.com`* következő:. a kapcsolathoz olyan TLS/SSL-tanúsítvány szükséges, amely a **. internal-contoso.com* esetében érvényes. 

Számos különböző módon szerezhet be érvényes TLS/SSL-tanúsítványt, beleértve a belső hitelesítésszolgáltatókat, a tanúsítvány megvásárlását egy külső kiállítótól és egy önaláírt tanúsítvány használatával.  A TLS/SSL-tanúsítvány forrástól függetlenül a következő tanúsítvány-attribútumokat megfelelően kell konfigurálni:

* *Subject*: ezt az attribútumot a **. your-root-domain-here.com* értékre kell állítani.
* *Tulajdonos alternatív neve*: ebben az attribútumban a **. your-root-domain-here.com* és a **. SCM.your-root-domain-here.com* is szerepelnie kell.  A második bejegyzés oka az, hogy az egyes alkalmazásokhoz társított SCM/kudu-helyhez kapcsolódó TLS-kapcsolatok az űrlap *Your-app-Name.SCM.your-root-domain-here.com*-címe alapján lesznek létrehozva.

Ha egy érvényes TLS/SSL-tanúsítvánnyal rendelkezik, két további előkészítési lépésre van szükség.  A TLS/SSL-tanúsítványt. pfx-fájlként kell átalakítani/menteni.  Ne feledje, hogy a. pfx-fájlnak tartalmaznia kell az összes köztes és főtanúsítványt, és a jelszót is védeni kell.

Ezután az eredményül kapott. pfx fájlt át kell alakítani egy Base64-karakterláncba, mivel a TLS/SSL-tanúsítvány egy Azure Resource Manager sablon használatával lesz feltöltve.  Mivel Azure Resource Manager sablonok szövegfájlok, a. pfx-fájlt Base64 karakterlánccá kell alakítani, hogy a sablon paraméterként is szerepeljen.

Az alábbi PowerShell-kódrészlet bemutatja, hogyan hozhat létre önaláírt tanúsítványt, exportálja a tanúsítványt. pfx-fájlként, konvertálja a. pfx-fájlt egy Base64 kódolású karakterláncba, majd mentse a Base64 kódolású karakterláncot egy külön fájlba.  A Base64-kódoláshoz tartozó PowerShell-kód a [PowerShell-parancsfájlok blogjában][examplebase64encoding]lett kialakítva.

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

Ha a TLS/SSL-tanúsítvány sikeresen létrejött, és Base64 kódolású karakterlánccá lett konvertálva, akkor a GitHubon lévő példa Azure Resource Manager sablon használható [az alapértelmezett TLS/SSL-tanúsítvány konfigurálásához][configuringDefaultSSLCertificate] .

A következő listában szereplő paraméterek szerepelnek az *azuredeploy.parameters.js* fájlban:

* *appServiceEnvironmentName*: a konfigurált ILB-előállítók neve.
* *existingAseLocation*: az a szöveges karakterlánc, amely azt az Azure-régiót tartalmazza, ahol a ILB-központot telepítették.  Például: "az USA déli középső régiója".
* *pfxBlobString*: a. pfx fájl bementi kódolt karakterlánc-ábrázolása.  A korábban bemutatott kódrészlet használatával másolja a "exportedcert. pfx. b64" fájlban található karakterláncot, és illessze be a *pfxBlobString* attribútum értékeként.
* *Password (jelszó*): a. pfx fájl védelméhez használt jelszó.
* *certificateThumbprint*: a Tanúsítvány ujjlenyomata.  Ha ezt az értéket a PowerShellből kéri le (például *$Certificate.* A korábbi kódrészletből származó ujjlenyomatot), az értéket is használhatja.  Ha azonban az értéket a Windows-tanúsítvány párbeszédpanelen másolja, ne felejtse el kihúzni a felesleges helyeket.  A *certificateThumbprint* valahogy így kell kinéznie: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: a tanúsítvány azonosítására szolgáló, saját maga által választott, felhasználóbarát karakterlánc-azonosító.  A nevet a rendszer a TLS/SSL-tanúsítványt jelképező *Microsoft. Web/Certificates* entitás egyedi Azure Resource Manager azonosítójának részeként használja.  A névnek a következő utótaggal **kell** végződnie:  \_ yourASENameHere_InternalLoadBalancingASE.  Ezt az utótagot a portál használja jelzőként, hogy a tanúsítvány a ILB-kompatibilis kiegészítő szolgáltatás biztonságossá tételére szolgál.

Alább látható a *azuredeploy.parameters.js* következő rövidített példája:

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

Miután megtörtént a *azuredeploy.parameters.js* fájl kitöltése, az alapértelmezett TLS/SSL-tanúsítvány konfigurálható a következő PowerShell-kódrészlet használatával.  Módosítsa a fájlelérési utakat úgy, hogy megegyezzenek a Azure Resource Manager sablonfájlokat a gépen.  Ne feledje, hogy adja meg a saját értékeit a Azure Resource Manager központi telepítési név és az erőforráscsoport neve számára.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A Azure Resource Manager-sablon elküldése után a rendszer körülbelül 40 percet vesz igénybe a módosítás alkalmazásához.  Ha például az alapértelmezett méretű betekintő két előtérrel rendelkezik, a sablon körülbelül egy órát vesz igénybe, és húsz percet is igénybe vehet.  Amíg a sablon fut, a rendszer nem fogja tudni méretezni a szolgáltatást.  

Miután a sablon befejeződik, a ILB-beadási csomagon keresztül elérhető alkalmazások HTTPS-kapcsolaton keresztül érhetők el, és a kapcsolatok az alapértelmezett TLS/SSL-tanúsítvány használatával lesznek biztosítva.  Az alapértelmezett TLS/SSL-tanúsítvány akkor lesz használatban, ha az ILB-alapú adatfeldolgozási szolgáltatásban lévő alkalmazások az alkalmazás neve és az alapértelmezett állomásnév kombinációja alapján vannak kezelve.  Például *`https://mycustomapp.internal-contoso.com`* a **. internal-contoso.com* alapértelmezett TLS/SSL-tanúsítványát fogja használni.

A nyilvános, több-bérlős szolgáltatáson futó alkalmazások esetében azonban a fejlesztők egyéni állomásnevek is konfigurálhatók az egyes alkalmazásokhoz, majd egyedi SNI TLS/SSL-tanúsítvány-kötéseket állíthatnak be az egyes alkalmazásokhoz.  

## <a name="getting-started"></a>Első lépések
App Service környezetek használatának megkezdéséhez tekintse [meg a app Service Environment bemutatása](app-service-app-service-environment-intro.md) című témakört.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

