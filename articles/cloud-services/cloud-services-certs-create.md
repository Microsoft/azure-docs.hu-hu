---
title: Cloud Services (klasszikus) és felügyeleti tanúsítványok | Microsoft Docs
description: Tudnivalók a Cloud Services-tanúsítványok létrehozásáról és üzembe helyezéséről, valamint az Azure-beli felügyeleti API-k hitelesítéséről.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c73f9812f344eecf4e51f43405b48693ddfa191b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739734"
---
# <a name="certificates-overview-for-azure-cloud-services-classic"></a>Tanúsítványok áttekintése az Azure Cloud Services (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A tanúsítványok az Azure for Cloud Services ([szolgáltatás-tanúsítványok](#what-are-service-certificates)) és a felügyeleti API ([felügyeleti tanúsítványok](#what-are-management-certificates)) használatával történő hitelesítéshez használatosak. Ez a témakör általános áttekintést nyújt mindkét tanúsítvány típusáról, valamint arról, hogyan [hozhatja létre](#create) és helyezheti üzembe őket az Azure-ban.

Az Azure-ban használt tanúsítványok x. 509 v3 tanúsítványok, és egy másik megbízható tanúsítvány alá is vonhatók, vagy önaláírtak. Az önaláírt tanúsítványt a saját létrehozója írja alá, ezért alapértelmezés szerint nem megbízható. A legtöbb böngésző figyelmen kívül tudja hagyni ezt a problémát. A felhőalapú szolgáltatások fejlesztése és tesztelése során csak önaláírt tanúsítványokat használhat. 

Az Azure által használt tanúsítványok nyilvános kulcsot is tartalmaznak. A tanúsítványok ujjlenyomattal rendelkeznek, amely azt jelenti, hogy egyértelmű módon azonosítja őket. Ezt az ujjlenyomatot használja az Azure [konfigurációs fájlban](cloud-services-configure-ssl-certificate-portal.md) annak azonosítására, hogy melyik tanúsítványnak kell használnia a Cloud Service-t. 

>[!Note]
>Az Azure Cloud Services nem fogadja el a AES256-SHA256 titkosított tanúsítványt.

## <a name="what-are-service-certificates"></a>Mik azok a szolgáltatási tanúsítványok?
A szolgáltatási tanúsítványok a felhőszolgáltatásokhoz kapcsolódnak, és a szolgáltatásból kifelé és befelé irányuló biztonságos kommunikációt teszik lehetővé. Ha például központilag telepített egy webes szerepkört, olyan tanúsítványt kell megadnia, amely képes a kitett HTTPS-végpont hitelesítésére. A szolgáltatás definíciójában definiált szolgáltatási tanúsítványokat a rendszer automatikusan telepíti a szerepkör példányát futtató virtuális gépre. 

A szolgáltatási tanúsítványokat feltöltheti az Azure-ba az Azure Portal használatával, vagy a klasszikus üzembehelyezési modell alkalmazásával is. A szolgáltatási tanúsítványok az adott felhőszolgáltatáshoz vannak társítva. Ezek a szolgáltatás definíciós fájljában vannak hozzárendelve a központi telepítéshez.

A szolgáltatási tanúsítványok a szolgáltatásaitól függetlenül kezelhetők, és a különböző személyek is kezelhetik. Előfordulhat például, hogy egy fejlesztő feltölt egy olyan tanúsítványt, amely egy IT-kezelő által korábban az Azure-ba feltöltött tanúsítványra hivatkozik. Az IT-menedzser felügyelheti és megújíthatja a tanúsítványt (vagyis módosíthatja a szolgáltatás konfigurációját) anélkül is, hogy új szervizcsomagot kellene feltölteni. Az új szervizcsomag nélküli frissítés lehetséges, mert a tanúsítvány logikai neve, tárolójának neve és helye a szolgáltatás definíciós fájljában található, és a Tanúsítvány ujjlenyomata meg van adva a szolgáltatás konfigurációs fájljában. A tanúsítvány frissítéséhez ezért elég feltölteni egy új tanúsítványt és megváltoztatni az ujjlenyomat értéket a szolgáltatás konfigurációs fájljában.

>[!Note]
>A [Cloud Services GYIK – konfigurációs és felügyeleti](cloud-services-configuration-and-management-faq.md) cikk hasznos információkat tartalmaz a tanúsítványokról.

## <a name="what-are-management-certificates"></a>Mik azok a felügyeleti tanúsítványok?
A felügyeleti tanúsítványok lehetővé teszik a klasszikus üzembehelyezési modell alkalmazásával történő hitelesítést. Számos program és eszköz (például a Visual Studio vagy az Azure SDK) ezeket a tanúsítványokat használja a különböző Azure-szolgáltatások konfigurációjának és üzembe helyezésének automatizálására. Ezek nem igazán kapcsolódnak a Cloud Serviceshez. 

> [!WARNING]
> légy óvatos! Az ilyen típusú tanúsítványok lehetővé teszik, hogy bárki hitelesítse őket a velük társított előfizetés kezeléséhez. 
> 
> 

### <a name="limitations"></a>Korlátozások
Az előfizetéshez legfeljebb 100 felügyeleti tanúsítvány adható meg. A szolgáltatás rendszergazdája által megadott felhasználói AZONOSÍTÓhoz tartozó összes előfizetéshez legfeljebb 100 felügyeleti tanúsítvány tartozik. Ha a fiók rendszergazdájának felhasználói azonosítója már használatban van az 100-es felügyeleti tanúsítvány hozzáadásához, és további tanúsítványokra van szükség, hozzáadhat egy társ-rendszergazdát a további tanúsítványok hozzáadásához. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Új önaláírt tanúsítvány létrehozása
A rendelkezésre álló bármely eszköz használatával létrehozhat egy önaláírt tanúsítványt, ha ezek a beállítások megfelelnek a következőknek:

* X. 509 tanúsítvány.
* Nyilvános kulcsot tartalmaz.
* Létrehozva a Key Exchange (. pfx fájl) számára.
* A tulajdonos nevének meg kell egyeznie a felhőalapú szolgáltatás eléréséhez használt tartománnyal.

    > Nem vásárolhat TLS/SSL-tanúsítványt a cloudapp.net (vagy bármely Azure-hoz kapcsolódó tartományhoz). a tanúsítvány tulajdonosának nevének meg kell egyeznie az alkalmazás eléréséhez használt egyéni tartománynévvel. Például **contoso.net**, nem **contoso.cloudapp.net**.

* Legalább 2048 bites titkosítás.
* **Csak a szolgáltatás tanúsítványa**: az ügyféloldali tanúsítványnak a *személyes* tanúsítványtárolóban kell lennie.

A Windowson, a segédprogramon vagy az IIS-ben kétféleképpen hozhat létre tanúsítványokat `makecert.exe` .

### <a name="makecertexe"></a>Makecert.exe
Ez a segédprogram elavult, és már nem dokumentálva van. További információt [ebben az MSDN-cikkben](/windows/desktop/SecCrypto/makecert)talál.

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Ha tartomány helyett IP-címmel szeretné használni a tanúsítványt, használja az IP-címet a-DnsName paraméterben.


Ha ezt [a tanúsítványt a felügyeleti portálon](/previous-versions/azure/azure-api-management-certs)szeretné használni, exportálja egy **. cer** fájlba:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Az interneten sok oldal található, amely az IIS használatával foglalkozik. [Itt](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) van egy nagyszerű, hogy azt hiszem, jól megmagyarázom. 

### <a name="linux"></a>Linux
[Ez a](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikk bemutatja, hogyan hozhat létre a tanúsítványokat SSH-val.

## <a name="next-steps"></a>Következő lépések
[Töltse fel a szolgáltatási tanúsítványt a Azure Portalba](cloud-services-configure-ssl-certificate-portal.md).

Töltsön fel egy [felügyeleti API-tanúsítványt](/previous-versions/azure/azure-api-management-certs) a Azure Portalba.