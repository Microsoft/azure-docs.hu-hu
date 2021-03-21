---
title: Tanúsítványokra vonatkozó követelmények és hibaelhárítás az Azure Stack Edge Pro-val | Microsoft Docs
description: Ismerteti a tanúsítványokra vonatkozó követelményeket és a tanúsítvány hibáinak elhárítását Azure Stack Edge Pro-eszközzel.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 9184a3f429804ac383f137de49c5391e2e1db80f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439238"
---
# <a name="certificate-requirements"></a>Tanúsítványkövetelmények

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azokat a tanúsítványokra vonatkozó követelményeket ismerteti, amelyeknek teljesülniük kell ahhoz, hogy a tanúsítványok telepíthetők legyenek a Azure Stack Edge Pro-eszközre. A követelmények a PFX-tanúsítványokkal, a kiállító hatóságokkal, a tanúsítvány tulajdonosának nevével és a tulajdonos alternatív nevével, valamint a támogatott tanúsítvány-algoritmusokkal kapcsolatosak.

## <a name="certificate-issuing-authority"></a>Tanúsítvány kiállító hatósága

A tanúsítvány kiállítására vonatkozó követelmények a következők:

* A tanúsítványokat a belső hitelesítésszolgáltatótól vagy egy nyilvános hitelesítésszolgáltatótól kell kibocsátani.

* Az önaláírt tanúsítványok használata nem támogatott.

* A tanúsítvány *kiállítója:* mező nem lehet ugyanaz, mint a *kiállító:* mező, kivéve a legfelső szintű hitelesítésszolgáltatói tanúsítványokat.


## <a name="certificate-algorithms"></a>Tanúsítvány-algoritmusok

A tanúsítvány-algoritmusoknak az alábbi követelményeknek kell megfelelniük:

* A tanúsítványoknak az RSA-kulcs algoritmusát kell használniuk.

* Csak a Microsoft RSA/Schannel titkosítási szolgáltatóval rendelkező RSA-tanúsítványok támogatottak.

* A tanúsítvány-aláírási algoritmus nem lehet SHA1.

* A minimális kulcs mérete 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve

A tanúsítványoknak a következő tulajdonos neve és a tulajdonos alternatív neve követelményekkel kell rendelkezniük:

* A tanúsítvány tulajdonos alternatív neve (SAN) mezőiben egyetlen tanúsítványt is használhat, amely magában foglalja az összes név térközét. Azt is megteheti, hogy az egyes névterekhez egyéni tanúsítványokat is használhat. Mindkét módszerhez szükség esetén helyettesítő karakterek használata szükséges, például a bináris nagy objektum (blob).

* Győződjön meg arról, hogy a tulajdonos neve (köznapi név) a tulajdonos alternatív neveinek a tulajdonos alternatív neve bővítményben való része.

* A tanúsítvány SAN mezőiben egyetlen helyettesítő karakteres tanúsítványt is használhat, amely minden nevet tartalmaz.

* Végponti tanúsítvány létrehozásakor használja az alábbi táblázatot:

    |Típus |Tulajdonos neve (SN)  |Tulajdonos alternatív neve (SAN)  |Tulajdonos neve – példa |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Helyi felhasználói felület| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Több-SAN önálló tanúsítvány mindkét végponthoz|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Csomópont|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * A AzureStackEdgeVPNCertificate hardcoded.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX-tanúsítvány

Az Azure Stack Edge Pro-eszközre telepített PFX-tanúsítványoknak az alábbi követelményeknek kell megfelelniük:

* Ha a tanúsítványokat az SSL-szolgáltatótól kapja, akkor ellenőrizze, hogy a tanúsítványokhoz tartozó teljes aláírási láncot kapja-e.

* PFX-tanúsítvány exportálásakor győződjön meg arról, hogy az **összes tanúsítvány belefoglalása a láncba** lehetőség van kiválasztva, ha lehetséges.

* Használjon PFX-tanúsítványt a végponthoz, a helyi KEZELŐFELÜLEThez, a csomóponthoz, a VPN-hez és a Wi-Fihoz, mivel a nyilvános és a titkos kulcs is szükséges a Azure Stack Edge Pro-hoz. A titkos kulcsnak rendelkeznie kell a helyi számítógép kulcs attribútumával.

* A tanúsítvány PFX-titkosításának 3DES-nek kell lennie. Ez a Windows 10-es ügyfélről vagy a Windows Server 2016 tanúsítványtárolóból való exportáláskor használt alapértelmezett titkosítás. A 3DES-vel kapcsolatos további információkért lásd: [Triple des](https://en.wikipedia.org/wiki/Triple_DES).

* A tanúsítvány PFX-fájljainak érvényes *digitális aláírással* és *KeyEncipherment* értékekkel kell rendelkezniük a *kulcshasználat* mezőben.

* A tanúsítvány PFX-fájljainak a *Kibővített kulcshasználat* mezőben tartalmazniuk kell az Values *Server Authentication (1.3.6.1.5.5.7.3.1)* és az *ügyfél-hitelesítés (1.3.6.1.5.5.7.3.2)* értéket.

* Az összes tanúsítvány PFX-fájl jelszavának meg kell egyeznie a telepítés időpontjában, ha a Azure Stack Readiness-ellenőrző eszközt használja. További információ: [tanúsítványok létrehozása a Azure stack Edge Pro-hoz Azure stack hub Readiness-ellenőrző eszköz használatával](azure-stack-edge-gpu-create-certificates-tool.md).

* A tanúsítvány PFX-ként megadott jelszavának összetett jelszónak kell lennie. Jegyezze fel ezt a jelszót, mert az központi telepítési paraméterként van használatban.

* Csak RSA-tanúsítványokat használjon a Microsoft RSA/Schannel titkosítási szolgáltatóval.

További információ: PFX- [tanúsítványok exportálása titkos kulccsal](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Következő lépések

[Tanúsítványok használata Azure Stack Edge Pro-val](azure-stack-edge-gpu-manage-certificates.md)

[Tanúsítványok létrehozása a Azure Stack Edge Pro-hoz Azure Stack hub Readiness-ellenőrző eszköz használatával](azure-stack-edge-gpu-create-certificates-tool.md)

[PFX-tanúsítványok exportálása titkos kulccsal](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Tanúsítványokkal kapcsolatos hibák elhárítása](azure-stack-edge-gpu-certificate-troubleshooting.md)