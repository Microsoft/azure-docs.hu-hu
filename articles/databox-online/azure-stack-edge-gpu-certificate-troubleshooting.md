---
title: Tanúsítványok hibaelhárítása Azure Stack Edge Pro-val GPU-val | Microsoft Docs
description: A Azure Stack Edge Pro GPU-eszközzel kapcsolatos hibák elhárítását ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 67dd2b35229c15ae4df5ec8acb357aa35621d67c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436634"
---
# <a name="troubleshooting-certificate-errors"></a>Tanúsítványokkal kapcsolatos hibák elhárítása

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A cikk a tanúsítványoknak a Azure Stack Edge Pro-eszközre történő telepítésekor gyakori hibák elhárítását ismerteti.

## <a name="common-certificate-errors"></a>Gyakori tanúsítvány-hibák

Az alábbi táblázat a hibákkal és a lehetséges megoldásokkal kapcsolatos gyakori tanúsítvány-hibákat és részletes információkat mutatja be:

> [!NOTE]
> &#8220;{0} , {1} ,..., {n} &#8221; a pozíciós paramétereket jelölik. A pozicionális paraméterek a használt tanúsítványtól függően értékeket vesznek fel.

| Hibakód | Description |
|---|---|
| CertificateManagement_UntrustedCertificate | A tulajdonos nevű tanúsítványhoz tartozó tanúsítványlánc {0} megszakadt. Töltse fel az aláíró lánc tanúsítványát a tanúsítvány feltöltése előtt.|
| CertificateManagement_DeviceNotRegistered| Az eszköz nincs aktiválva. A támogatási tanúsítványokat csak az aktiválás után töltheti fel.|
| CertificateManagement_ExpiredCertificate | A típusú tanúsítvány érvényessége {0} lejárt vagy hamarosan lejár. Ellenőrizze a tanúsítvány lejáratát, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_FormatMismatch | A tanúsítvány formátuma nem támogatott. Ellenőrizze a tanúsítvány formátumát, és ha szükséges, hozza egy új tanúsítványt.  {0}A rendszer a következőt várta: {1} . |
| CertificateManagement_GenericError | Nem hajtható végre a Tanúsítványkezelő művelet. Próbálja megismételni a műveletet néhány perc múlva. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| CertificateManagement_HttpsBindingFailure | A tulajdonos nevű tanúsítvány {0} nem tudott biztonságos HTTPS-csatornát létrehozni. Ellenőrizze a feltöltött tanúsítványt, és ha szükséges, új tanúsítványt kell behoznia. Ez a hiba az eszköz csomópont-tanúsítványával fordul elő.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezhet a kulcshasználat tanúsítványának aláírásával. Ellenőrizze a tanúsítvány kulcshasználat használatát, és ha szükséges, egy új tanúsítványt. Ez a hiba az aláíró lánc tanúsítványával fordul elő. |
| CertificateManagement_IncorrectKeyNumber | A tulajdonos nevével rendelkező tanúsítvány {0} nem megfelelő számú kulccsal rendelkezik {1} . Ellenőrizze a tanúsítvány kulcsának számát, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_InvalidP7b | A feltöltött tanúsítványfájl érvénytelen.  Ellenőrizze a tanúsítvány formátumát, és ha szükséges, hozza egy új tanúsítványt.|
| CertificateManagement_KeyAlgorithmNotRSA | Ez a tanúsítvány nem használja az RSA-kulcs algoritmusát. Csak az RSA-tanúsítványok támogatottak. |
| CertificateManagement_KeySizeNotSufficient | A tulajdonos nevével rendelkező tanúsítvány {0} mérete nem elegendő {1} . A minimális kulcs mérete 4096.|
| CertificateManagement_MissingClientOid | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik ügyfél-hitelesítési OID azonosítóval. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik a kulcshasználat digitális aláírásával. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt. |
| CertificateManagement_MissingKeyCertSignKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik a kulcshasználat tanúsítvány-aláírással. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik kulcs-titkosítási. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt. |
| CertificateManagement_MissingServerOid | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik kiszolgálói hitelesítés OID-vel. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_NameMismatch | A tanúsítvány típusa nem egyezik. Várt hatókör: {0} , található {1} . Töltse fel a megfelelő tanúsítványt.|
| CertificateManagement_NoPrivateKeyPresent | A tulajdonos névvel rendelkező tanúsítvány {0} nem tartalmaz titkos kulcsot. Töltsön fel egy titkos kulccsal rendelkező. pfx-tanúsítványt.|
| CertificateManagement_NoRSACryptoPrivateKey | A tulajdonos nevű tanúsítvány titkos kulcsa {0} nem érhető el. Győződjön meg arról, hogy támogatott tanúsítványt használ. Csak a Microsoft RSA/Schannel titkosítási szolgáltató támogatott. |
| CertificateManagement_NotSelfSignedCertificate | A tulajdonos nevű tanúsítvány {0} nem saját aláírású. A főtanúsítványoknak önaláírtnak kell lenniük |
| CertificateManagement_NotSupportedOnVirtualAppliance | Ez a művelet nem támogatott a virtuális eszközön. Ez a hiba azt jelzi, hogy az aláírás csak a taktikai felhőalapú berendezésben futó Data Box Gateway esetén jelentkezik. Ez a hiba akkor fordul elő, amikor az eszközt a Windows PowerShellen keresztül kezeli.|
| CertificateManagement_SelfSignedCertificate | A tulajdonos nevű tanúsítvány {0} önaláírt. A megfelelő aláírással rendelkező tanúsítvány feltöltése.|
| CertificateManagement_SignatureAlgorithmSha1 | A tulajdonos nevű tanúsítvány {0} nem támogatott aláírási algoritmust tartalmaz. Az SHA1-RSA nem támogatott. |
| CertificateManagement_SubjectNamesInvalid | A tulajdonos nevével rendelkező tanúsítványhoz {0} nem tartozik a tanúsítvány helyes tulajdonosának neve vagy a tulajdonos alternatív neve {1} . Ellenőrizze a feltöltött tanúsítványt, és ha szükséges, új tanúsítványt kell behoznia. Azt is ellenőriznie kell, hogy a DNS-név megegyezik-e a SANS-nevekkel.|
| CertificateManagement_UnreadableCertificate | A típusú tanúsítvány {0} nem olvasható. Ez a hiba akkor fordul elő, ha a tanúsítvány nem olvasható vagy sérült. Új tanúsítvány létrehozása.|
| CertificateSubjectNotFound | Nem található a tulajdonos nevű tanúsítvány {0} . Új tanúsítvány létrehozása.|
| CertificateRotationGenericFailure | Egy vagy több tanúsítvány elforgatása meghiúsult. Próbálkozzon újra néhány perc múlva. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz.|
| CertificateImportFailure | Az ujjlenyomattal rendelkező tanúsítvány {0} nem lett importálva a csomóponton {1} . Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| CertificateApplyFailure | Az ujjlenyomattal rendelkező tanúsítvány {0} nem lett alkalmazva a csomóponton {1} . Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz.|
| NodeNotReachable | Nem lehetett érvényesíteni a tanúsítványt {0} . Keresse meg a rendszerhardver és a szoftver állapotát.|


## <a name="next-steps"></a>Következő lépések

[Tanúsítványokra vonatkozó követelmények](azure-stack-edge-gpu-certificate-requirements.md)