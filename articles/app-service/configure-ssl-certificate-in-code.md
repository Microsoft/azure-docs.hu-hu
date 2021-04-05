---
title: TLS/SSL-tanúsítvány használata a kódban
description: Ismerje meg, hogyan használhatja az Ügyféltanúsítványok használatát a kódban. Hitelesítse magát a távoli erőforrásokkal egy ügyféltanúsítvány használatával, vagy futtasson titkosítási feladatokat.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126039"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben

Az alkalmazás kódjában elérheti a [app Servicehoz hozzáadott nyilvános vagy privát tanúsítványokat](configure-ssl-certificate.md). Az alkalmazás kódja ügyfélként működhet, és olyan külső szolgáltatáshoz férhet hozzá, amelyhez tanúsítványalapú hitelesítés szükséges, vagy szükség lehet titkosítási feladatok végrehajtására. Ez a útmutató bemutatja, hogyan használhatók a nyilvános vagy privát tanúsítványok az alkalmazás kódjában.

A kódban szereplő tanúsítványok használatának módszere a App Service TLS funkciójának használatát igényli, amelyhez az alkalmazásnak **alapszintű vagy magasabb szintűnek** kell lennie. Ha az alkalmazás **ingyenes** vagy **megosztott** szinten van, akkor az alkalmazás adattárában is megadhatja [a tanúsítványfájl-fájlt](#load-certificate-from-file).

Ha App Service felügyeli a TLS/SSL-tanúsítványokat, megtarthatja a tanúsítványokat és az alkalmazás kódját, és gondoskodhat a bizalmas adatok védelméről is.

## <a name="prerequisites"></a>Előfeltételek

A következő útmutató követése:

- [Létre kell hoznia egy App Service-alkalmazást.](./index.yml)
- [Tanúsítvány hozzáadása az alkalmazáshoz](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Az ujjlenyomat megkeresése

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a app Services lehetőséget   >  **\<app-name>** .

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások**, majd a **titkos kulcsú tanúsítványok (. pfx)** vagy a **nyilvános kulcsú tanúsítványok (. cer)** lehetőséget.

Keresse meg a használni kívánt tanúsítványt, és másolja az ujjlenyomatot.

![A tanúsítvány ujjlenyomatának másolása](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>A tanúsítvány elérhetővé tétele

Az alkalmazás kódjában található tanúsítvány eléréséhez adja hozzá az ujjlenyomatát az `WEBSITE_LOAD_CERTIFICATES` alkalmazás beállításaihoz a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Az összes tanúsítvány elérhetővé tételéhez állítsa a értéket a következőre: `*` .

## <a name="load-certificate-in-windows-apps"></a>Tanúsítvány betöltése Windows-alkalmazásokban

Az Alkalmazásbeállítások `WEBSITE_LOAD_CERTIFICATES` lehetővé teszi, hogy a megadott tanúsítványok elérhetők legyenek a Windows által üzemeltetett alkalmazás számára a Windows-tanúsítványtárolóban, az [aktuális Settingsnew](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

A C#-kódban a Tanúsítvány ujjlenyomata alapján éri el a tanúsítványt. A következő kód betölti az ujjlenyomattal rendelkező tanúsítványt `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

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

A Java-kódban a tulajdonos köznapi név mezővel férhet hozzá a tanúsítványhoz a "Windows-MY" áruházból (lásd: [nyilvános kulcsú tanúsítvány](https://en.wikipedia.org/wiki/Public_key_certificate)). A következő kód bemutatja, hogyan tölthető be egy titkos kulcsú tanúsítvány:

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

Azokon a nyelveken, amelyeken nem támogatott vagy nem nyújt elegendő támogatást a Windows tanúsítványtárolóhoz, lásd: [tanúsítvány betöltése fájlból](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Tanúsítvány betöltése fájlból

Ha manuálisan feltöltött tanúsítványfájl betöltését szeretné elvégezni, akkor jobb, ha például a [git](deploy-local-git.md)helyett a [FTPS](deploy-ftp.md) használatával tölti fel a tanúsítványt. A bizalmas adatokat, például egy privát tanúsítványt a forrás-ellenőrzésen kívül kell tartania.

> [!NOTE]
> A Windows ASP.NET és ASP.NET Core akkor is el kell érnie a tanúsítványtárolót, ha fájlból tölt be tanúsítványt. Egy Windows .NET-alkalmazásban lévő tanúsítványfájl betöltéséhez töltse be az aktuális felhasználói profilt a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>következő parancsával:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> A kódban szereplő tanúsítványok használatának módszere a App Service TLS funkciójának használatát igényli, amelyhez az alkalmazásnak **alapszintű vagy magasabb szintűnek** kell lennie.

A következő C#-példa egy nyilvános tanúsítványt tölt be az alkalmazás relatív elérési útjából:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Ha szeretné megtekinteni, hogyan tölthető be a TLS/SSL-tanúsítvány egy Node.js, PHP, Python, Java vagy Ruby fájlból, tekintse meg a megfelelő nyelvi vagy webes platform dokumentációját.

## <a name="load-certificate-in-linuxwindows-containers"></a>Tanúsítvány betöltése Linux/Windows-tárolókban

Az `WEBSITE_LOAD_CERTIFICATES` Alkalmazásbeállítások lehetővé teszi, hogy a megadott tanúsítványok elérhetők legyenek a Windows-vagy Linux-tárolós alkalmazások számára (beleértve a beépített Linux-tárolókat) fájlokként. A fájlok a következő könyvtárakban találhatók:

| Tároló platform | Nyilvános tanúsítványok | Privát tanúsítványok |
| - | - | - |
| Windows-tárolók | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux-tárolók | `/var/ssl/certs` | `/var/ssl/private` |

A tanúsítványfájl neve a tanúsítvány ujjlenyomatai megfelelnek. 

> [!NOTE]
> App Service a tanúsítvány elérési útját a Windows-tárolókban adja meg a következő környezeti változók `WEBSITE_PRIVATE_CERTS_PATH` ,, `WEBSITE_INTERMEDIATE_CERTS_PATH` `WEBSITE_PUBLIC_CERTS_PATH` és `WEBSITE_ROOT_CERTS_PATH` . Érdemes a tanúsítvány elérési útját a rögzítjük helyett a környezeti változókkal hivatkozni, ha a tanúsítvány elérési útjai a jövőben változnak.
>

Emellett a [Windows Server Core tárolók](configure-custom-container.md#supported-parent-images) automatikusan betöltik a tanúsítványokat a tanúsítványtárolóba a **LocalMachine\My**-ben. A tanúsítványok betöltéséhez kövesse ugyanazt a mintát, mint a [Windows-alkalmazások betöltési tanúsítványa](#load-certificate-in-windows-apps). Windows Nano-alapú tárolók esetén a fent megadott fájlelérési utak használatával [töltse be a tanúsítványt közvetlenül a fájlból](#load-certificate-from-file).

A következő C#-kód bemutatja, hogyan tölthető be egy nyilvános tanúsítvány egy Linux-alkalmazásban.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Ha szeretné megtekinteni, hogyan tölthető be a TLS/SSL-tanúsítvány egy Node.js, PHP, Python, Java vagy Ruby fájlból, tekintse meg a megfelelő nyelvi vagy webes platform dokumentációját.

## <a name="more-resources"></a>További erőforrások

* [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure App Service](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](configure-ssl-bindings.md#enforce-tls-versions)
* [Gyakori kérdések: App Service tanúsítványok](./faq-configuration-and-management.md)