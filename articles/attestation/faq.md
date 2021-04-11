---
title: Gyakori kérdések
description: Válaszok az Microsoft Azure igazolással kapcsolatos gyakori kérdésekre
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 9b15a336e97cad1fb03a63ec34f563a4453755ff
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504192"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Microsoft Azure igazolással kapcsolatos gyakori kérdések

Ez a cikk az [Azure-igazolással](overview.md)kapcsolatos leggyakoribb kérdésekre ad választ.

Ha az Azure-beli probléma nem szerepel ebben a cikkben, Azure-támogatási kérelmet is beküldhet az [Azure támogatási oldalára](https://azure.microsoft.com/support/options/).

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Mi az az Azure PCK cache Service és annak szerepe az enklávé igazolásában

Az Azure PCK cache Service meghatározza az Azure-beli [bizalmas számítástechnikai (ACC)](../confidential-computing/overview.md) csomópontok Azure-beli biztonsági alaptervét az Intel-ból, és gyorsítótárazza az adatokból. A gyorsítótárazott adatokat az Azure igazolása továbbra is felhasználja a megbízható végrehajtási környezetek (pólók) ellenőrzéséhez.  

Azure PCK gyorsítótárazási szolgáltatás:
   - Magas rendelkezésre állást biztosít 
   - Csökkenti a külsőleg üzemeltetett szolgáltatások és az internetkapcsolat függőségeit.
   - Az Intel-tanúsítványok legújabb verzióit, a CRL-ek, a megbízható számítástechnikai bázis (TCB) információit és az ACC-csomópontok az Intelből való előjegyzési identitásának beolvasása. A szolgáltatás ezért megerősíti, hogy az Azure-igazolás a pólók ellenőrzése közben az Azure-beli biztonsági alaptervet jelzi, nagy mértékben csökkenti az igazolási hibákat az Intel-tanúsítványok érvénytelenítése vagy visszavonása miatt.  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Az Azure-igazolások által támogatott SGX ENKLÁVÉHOZ-igazolások nem Azure-környezetekben

Nem. Az Azure-igazolás az Azure PCK cache Service által a pólók ellenőrzéséhez megadott biztonsági alaptervtől függ. Az Azure PCK cache Service jelenleg csak az Azure bizalmas számítástechnikai csomópontok támogatására lett tervezve. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Milyen érvényesítést végez az Azure-igazolás a SGX ENKLÁVÉHOZ enklávék igazolására

Az Azure igazolása egy egységes keretrendszer a különböző típusú pólók távoli hitelesítéséhez. Azure-igazolás:

   - Ellenőrzi, hogy az aláírt enklávé-idézet megbízható gyökere az Intel-hoz tartozik-e.
   - Ellenőrzi, hogy az enklávé-idézet megfelel-e az Azure PCK cache Service által meghatározott Azure biztonsági alaptervnek.
   - Ellenőrzi, hogy az SHA256 kivonata (EHD) az igazolási kérelem objektumában megegyezik-e az enklávé-ajánlat első 32 bájt reportData mezőjével.
   - Lehetővé teszi, hogy az ügyfelek tanúsítvány-szolgáltatót hozzanak létre, és egyéni szabályzatot konfiguráljanak. A fenti érvényesítéseken kívül az Azure-igazolás a szabályzatra is kiértékeli az enklávé-árajánlatot. A szabályzatok határozzák meg az enklávé engedélyezési szabályait, valamint az igazolási jogkivonat generálásához szükséges kiállítási szabályokat is. Annak ellenőrzéséhez, hogy a kívánt szoftver egy enklávéban fut-e, az ügyfelek hozzáadhatnak-e engedélyezési szabályokat annak ellenőrzéséhez, hogy a **mrsigner** és a **mrenclave** mezők az enklávé-idézőjelben megegyezik-e az ügyfél bináris értékeivel.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Hogyan szerezhet be egy ellenőrző biztosítékot az Azure-igazolás által támogatott SGX ENKLÁVÉHOZ igazoláshoz

Általánosságban elmondható, hogy az igazolási modellek esetében az Intel a megbízhatóság gyökerét, az igazolási ügyfél pedig az enklávé API-kat hívja meg az enklávé-tanúsítványok beolvasásához. Az enklávé API-k belsőleg meghívja az Intel PCK cache Service-t, hogy beolvassa az igazolni kívánt csomópont Intel tanúsítványait. A tanúsítványok a enklávé-bizonyítékok aláírására szolgálnak, így távolról igazolható biztosítékot generálnak.  

Ugyanez a folyamat valósítható meg az Azure-igazolások esetében is. Az Azure PCK cache Service által nyújtott előnyök kihasználása azonban az ACC virtuális gép telepítése után ajánlott az [Azure DCAP Library](https://www.nuget.org/packages/Microsoft.Azure.DCAP)telepítése. Az Intel megállapodása alapján az Azure DCAP Library telepítésekor az enklávé-alapú tanúsítványok létrehozásával kapcsolatos kérelmeket az Intel PCK gyorsítótárazási szolgáltatásból az Azure PCK cache Service-be irányítja át. Az Azure DCAP Library Windows-és Linux-alapú környezetekben támogatott.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Áttérés az Azure-igazolásra más igazolási modellekből

- Az Azure bizalmas számítástechnikai virtuális gép telepítése után telepítse az Azure DCAP Library ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) szolgáltatást, hogy kihasználja az Azure PCK cache Service nyújtotta előnyöket.
- A távoli igazolási ügyfelet úgy kell létrehozni, hogy az beolvassa az enklávé bizonyítékait, és kéréseket küldjön az Azure-igazolásba. Lásd: [kód minták](/samples/browse/?expanded=azure&terms=attestation) referenciához 
- Az igazolási kérelmeket az alapértelmezett szolgáltatók REST API végpontjának vagy az egyéni igazolási szolgáltatók számára lehet elküldeni 
- Az Azure-igazolási API-kat az Azure AD-hitelesítés védi. Ezért a tanúsító API-kat meghívó ügyfélnek képesnek kell lennie arra, hogy érvényes Azure AD hozzáférési jogkivonatot szerezzen be és adjon át az igazolási kérelemben 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Hogyan ellenőrzi a függő entitás a tanúsító jogkivonat integritását.

Az Azure-igazolás által generált igazolási jogkivonat aláírása önaláírt tanúsítvány használatával történik. A tanúsítványok egy [OpenID metaadat-végponton](/rest/api/attestation/metadataconfiguration/get)keresztül érhetők el. A függő entitás lekérheti az aláíró tanúsítványokat ebből a végpontból, és elvégezheti az igazolási jogkivonat aláírásának ellenőrzését. Az igazolási jogkivonat érvényességi ideje 8 óra. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Az aláírás-ellenőrzéshez használandó tanúsítvány azonosítása az OpenID metaadat-végpontból

Az OpenID metaadatok végpontjában elérhető több tanúsítvány az Azure-igazolás által támogatott különböző használati esetekre (például SGX ENKLÁVÉHOZ igazolásra) vonatkozik. Az [RFC 7515](https://tools.ietf.org/html/rfc7515)által meghatározott szabványok alapján az aláírás-ellenőrzéshez az igazolási jogkivonat fejlécében a *Kid* paraméterrel egyező kulcs-azonosítóval rendelkező tanúsítványt kell használni. Ha nem található megfelelő **kölyök** , a rendszer az OpenID metaadat-végpont által közzétett összes tanúsítvány kipróbálását is elvárta.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>Lehetséges, hogy a függő entitás megoszthatja a titkos kulcsokat az ellenőrzött megbízható végrehajtási környezetekkel (TEEs)

Az enklávéban generált nyilvános kulcsot az ügyfél által az Azure-igazoláshoz eljuttatott igazolási kérelem objektumának enklávé birtokában lévő adategység (EHD) tulajdonságában lehet kifejezni. Ha a rendszer megerősíti, hogy a EHD SHA256 kivonata az idézőjel reportData mezőjében van-e kifejezve, az Azure-igazolás magában foglalja a EHD az igazolási jogkivonatban. A függő entitás a hitelesített igazolási válasz EHD használatával titkosíthatja a titkokat, és megoszthatja őket az enklávéban. További információért lásd az [Azure igazolásának alapvető fogalmait](basic-concepts.md) .
