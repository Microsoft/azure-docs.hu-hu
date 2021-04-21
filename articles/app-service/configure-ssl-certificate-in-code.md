---
title: TLS-/SSL-tanúsítvány használata a kódban
description: Megtudhatja, hogyan használhatja az ügyféltanúsítványokat a kódban. Hitelesítés távoli erőforrásokkal ügyfél-tanúsítvánnyal, vagy titkosítási feladatok futtatása velük.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 294587fde846a3774f7d74f64029e0bca00e9c08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829403"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben

Az alkalmazáskódban hozzáférhet a alkalmazáshoz hozzáadt nyilvános vagy privát [tanúsítványokhoz, App Service.](configure-ssl-certificate.md) Előfordulhat, hogy az alkalmazáskód ügyfélként működik, és tanúsítványhitelesítést igénylő külső szolgáltatáshoz fér hozzá, vagy kriptográfiai feladatokat kell végrehajtania. Ez az útmutató bemutatja, hogyan használhatja a nyilvános vagy privát tanúsítványokat az alkalmazáskódban.

A tanúsítványok kódban való használatának ezen megközelítése a App Service TLS-funkcióit használja, amelyhez az alkalmazásnak alapszintű vagy magasabb szintűnek kell lennie.  Ha az alkalmazás ingyenes **vagy** megosztott szinten **van,** a tanúsítványfájlt az alkalmazás [adattárában is használhatja.](#load-certificate-from-file)

Ha lehetővé App Service TLS-/SSL-tanúsítványok kezelését, a tanúsítványokat és az alkalmazás kódját külön-külön tarthatja karban, és megvédheti a bizalmas adatokat.

## <a name="prerequisites"></a>Előfeltételek

Az útmutatót a következőképpen követjük:

- [Létre kell hoznia egy App Service-alkalmazást.](./index.yml)
- [Tanúsítvány hozzáadása az alkalmazáshoz](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Az ujjlenyomat megkeresve

A bal <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menüben válassza a App Services  >  **\<app-name>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** lehetőséget, majd a Titkos kulcsú tanúsítványok **(.pfx)** vagy a Nyilvános kulcsú tanúsítványok **(.cer) lehetőséget.**

Keresse meg a használni kívánt tanúsítványt, és másolja ki az ujjlenyomatot.

![A tanúsítvány ujjlenyomatának másolása](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>A tanúsítvány elérhetővé tétele

Ha hozzá szeretne férni egy tanúsítványhoz az alkalmazáskódban, adja hozzá az ujjlenyomatát az alkalmazásbeállításhoz a következő parancs futtatásával a `WEBSITE_LOAD_CERTIFICATES` <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Az összes tanúsítvány elérhetővé tétele érdekében állítsa az értékét `*` értékre.

## <a name="load-certificate-in-windows-apps"></a>Tanúsítvány betöltése Windows-alkalmazásokban

Az alkalmazásbeállítás elérhetővé teszi a megadott tanúsítványokat a Windows által üzemeltetett alkalmazás számára a Windows tanúsítványtárolójában, `WEBSITE_LOAD_CERTIFICATES` az Aktuális [felhasználó\Saját mappában.](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)

A C#-kódban a tanúsítványhoz a tanúsítvány ujjlenyomatával férhet hozzá. A következő kód betölt egy tanúsítványt a következő ujjlenyomattal: `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

A Java-kódban a tanúsítványt a "Windows-MY" tárolóból, a Tulajdonos közneve mezővel fogja elérni (lásd: [Nyilvános kulcsú tanúsítvány).](https://en.wikipedia.org/wiki/Public_key_certificate) A következő kód bemutatja, hogyan kell betölteni egy titkos kulcsú tanúsítványt:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

A Windows tanúsítványtárolót nem támogató vagy nem megfelelő támogatást nem támogató nyelvekért lásd: [Tanúsítvány betöltése fájlból.](#load-certificate-from-file)

## <a name="load-certificate-from-file"></a>Tanúsítvány betöltése fájlból

Ha manuálisan feltöltött tanúsítványfájlt kell betöltenie, jobb, ha a [Tanúsítványt](deploy-local-git.md)például a Git helyett [FTPS](deploy-ftp.md) használatával tölti fel. A bizalmas adatokat, például a privát tanúsítványokat érdemes a forrásból kivédni.

> [!NOTE]
> ASP.NET windowsos ASP.NET a Core-nak akkor is hozzá kell férnie a tanúsítványtárolóhoz, ha egy fájlból tölt be tanúsítványt. Ha tanúsítványfájlt tölt be egy Windows .NET-alkalmazásban, töltse be az aktuális felhasználói profilt a következő paranccsal a <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> A tanúsítványok kódban való használatának ezen megközelítése a App Service TLS-funkcióit használja, amelyhez az alkalmazásnak alapszintű vagy magasabb szintűnek kell lennie. 

Az alábbi C#-példa egy nyilvános tanúsítványt tölt be egy relatív elérési útról az alkalmazásban:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Ha meg szeretné tudni, hogyan lehet TLS/SSL-tanúsítványt betölteni egy Node.js-, PHP-, Python-, Java- vagy Ruby-fájlból, tekintse meg a megfelelő nyelv vagy webes platform dokumentációját.

## <a name="load-certificate-in-linuxwindows-containers"></a>Tanúsítvány betöltése Linux-/Windows-tárolókban

Az alkalmazásbeállítások fájlként elérhetővé teszi a megadott tanúsítványokat a Windows- vagy Linux-tárolóalkalmazások számára (beleértve a beépített `WEBSITE_LOAD_CERTIFICATES` Linux-tárolókat is). A fájlok a következő könyvtárakban találhatók:

| Tárolóplatform | Nyilvános tanúsítványok | Privát tanúsítványok |
| - | - | - |
| Windows-tárolók | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux-tárolók | `/var/ssl/certs` | `/var/ssl/private` |

A tanúsítványfájl nevei a tanúsítvány ujjlenyomatai. 

> [!NOTE]
> App Service a tanúsítvány elérési útjait a Windows-tárolókba a következő környezeti változókként( `WEBSITE_PRIVATE_CERTS_PATH` `WEBSITE_INTERMEDIATE_CERTS_PATH` , , és `WEBSITE_PUBLIC_CERTS_PATH` `WEBSITE_ROOT_CERTS_PATH` ). Jobb a tanúsítvány elérési útjára hivatkozni a környezeti változóknál ahelyett, hogy a tanúsítvány elérési útját szoftveres kódolással használjuk arra az esetre, ha a tanúsítvány elérési útjai megváltoznak a jövőben.
>

Ezenkívül a [Windows Server Core-tárolók](configure-custom-container.md#supported-parent-images) automatikusan betöltik a tanúsítványokat a tanúsítványtárolóba a **LocalMachine\My mappában.** A tanúsítványok betöltéséhez kövesse a Tanúsítvány betöltése [Windows-alkalmazásokban mintát.](#load-certificate-in-windows-apps) Windows Nano-alapú tárolók esetén a fent megadott fájlútvonalak használatával töltse be a tanúsítványt [közvetlenül a fájlból.](#load-certificate-from-file)

Az alábbi C#-kód bemutatja, hogyan lehet betölteni egy nyilvános tanúsítványt egy Linux-alkalmazásban.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Az alábbi C#-kód bemutatja, hogyan lehet betölteni egy privát tanúsítványt egy Linux-alkalmazásban.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;
...
var bytes = File.ReadAllBytes("/var/ssl/private/<thumbprint>.p12");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Ha meg szeretné tudni, hogyan lehet TLS/SSL-tanúsítványt betölteni egy Node.js- vagy PHP-, Python-, Java- vagy Ruby-fájlból, tekintse meg a megfelelő nyelv vagy webes platform dokumentációját.

## <a name="more-resources"></a>További erőforrások

* [Egyéni DNS-név biztonságossá teése TLS/SSL-kötéssel a Azure App Service](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](configure-ssl-bindings.md#enforce-tls-versions)
* [Gyakori kérdések: App Service tanúsítványok](./faq-configuration-and-management.md)
