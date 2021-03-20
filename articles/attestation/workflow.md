---
title: Azure-igazolási munkafolyamat
description: Az Azure-igazolás munkafolyamata.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 27a97ceb2ca9a7b58df7200930e4e47d89c9ae89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762166"
---
# <a name="workflow"></a>Munkafolyamat

Microsoft Azure igazolás igazolja a enklávékat, és kiértékeli az Azure biztonsági alapkonfiguráció és a konfigurálható szabályzatok elleni bizonyítékokat. Sikeres ellenőrzés után az Azure-igazolás igazoló jogkivonatot hoz létre az enklávé megbízhatóságának megerősítéséhez.

Az Azure igazolási munkafolyamatában a következő szereplők vesznek részt:

- **Függő entitás**: az az összetevő, amely az Azure-igazolásra támaszkodik az enklávé érvényességének ellenőrzéséhez. 
- **Ügyfél**: az a komponens, amely adatokat gyűjt egy enklávéból, és kéréseket küld az Azure-igazolásnak. 
- **Azure-tanúsítvány**: az a komponens, amely az ügyféltől származó enklávé-bizonyítékokat fogad el, ellenőrzi, és visszaadja a tanúsító jogkivonatot az ügyfélnek.


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>Intel® Software Guard Extensions (SGX ENKLÁVÉHOZ) enklávé ellenőrzési munkafolyamata

Az alábbi általános lépések egy tipikus SGX ENKLÁVÉHOZ enklávé-igazolási munkafolyamatban (az Azure igazolás használatával):

1. Az ügyfél egy enklávéból származó bizonyítékot gyűjt. A bizonyítékok az enklávé környezetére és az enklávéban futó ügyféloldali könyvtárra vonatkozó információk.
1. Az ügyfél rendelkezik egy URI-val, amely az Azure-igazolás egy példányára hivatkozik. Az ügyfél bizonyítékokat küld az Azure-igazolásra. A szolgáltatónak küldött pontos információk az enklávé típusától függenek.
1. Az Azure-igazolás ellenőrzi a beküldött adatokat, és kiértékeli azt egy konfigurált házirend alapján. Ha az ellenőrzés sikeres, az Azure igazolása hibát jelez, és visszaadja az ügyfélnek. Ha ez a lépés meghiúsul, az Azure-igazolás hibát jelez az ügyfélnek. 
1. Az ügyfél elküldi az igazolási jogkivonatot a függő entitásnak. A függő entitás meghívja az Azure-tanúsítvány nyilvános kulcsú metaadat-végpontját az aláíró tanúsítványok lekéréséhez. A függő entitás ezután ellenőrzi az igazolási jogkivonat aláírását, és biztosítja az enklávé megbízhatóságát. 

![SGX ENKLÁVÉHOZ enklávé ellenőrzési folyamata](./media/sgx-validation-flow.png)

> [!Note]
> Amikor igazolási kérelmeket küld az [2018-09-01 – előzetes](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview) verziójú API-verzióban, az ügyfélnek bizonyítania kell az Azure-igazolást az Azure ad hozzáférési jogkivonatával együtt.

## <a name="trusted-platform-module-tpm-enclave-validation-work-flow"></a>A platformmegbízhatósági modul (TPM) enklávé ellenőrzési munkafolyamata

Az alábbi általános lépések egy tipikus TPM enklávé-igazolási munkafolyamatban (az Azure igazolás használatával):

1.  Az eszköz/platform rendszerindításkor a különböző rendszerindító és rendszerindítási szolgáltatások mérik a TPM által támogatott és biztonságosan tárolt eseményeket (TCG-napló).
2.  Az ügyfél a TCG-naplókat az eszközről és a TPM-idézetből gyűjti, amely igazolja az igazolást.
3.  Az ügyfél rendelkezik egy URI-val, amely az Azure-igazolás egy példányára hivatkozik. Az ügyfél bizonyítékokat küld az Azure-igazolásra. A szolgáltatónak küldött pontos információk a platformtól függenek.
4.  Az Azure-igazolás ellenőrzi a beküldött adatokat, és kiértékeli azt egy konfigurált házirend alapján. Ha az ellenőrzés sikeres, az Azure igazolása hibát jelez, és visszaadja az ügyfélnek. Ha ez a lépés meghiúsul, az Azure-igazolás hibát jelez az ügyfélnek. Az ügyfél és az igazolási szolgáltatás közötti kommunikációt az Azure igazolási TPM protokollja határozza meg.
5.  Az ügyfél ezután elküldi az igazolási jogkivonatot a függő entitásnak. A függő entitás meghívja az Azure-tanúsítvány nyilvános kulcsú metaadat-végpontját az aláíró tanúsítványok lekéréséhez. A függő entitás ezután ellenőrzi az igazolási jogkivonat aláírását, és biztosítja a platformok megbízhatóságát.

![TPM-ellenőrzési folyamat](./media/tpm-validation-flow.png)

## <a name="next-steps"></a>Következő lépések
- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)
