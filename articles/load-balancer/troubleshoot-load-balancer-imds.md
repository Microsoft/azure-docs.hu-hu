---
title: Gyakori hibakódok az Azure Instance Metadata Service (IMDS) szolgáltatáshoz
titleSuffix: Azure Load Balancer
description: Az Azure Instance Metadata Service (IMDS) gyakori hibakódok és a hozzájuk tartozó kockázatcsökkentő módszerek áttekintése
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519184"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Hibakódok: gyakori hibakódok a terheléselosztó adatainak lekéréséhez a IMDS használatával

Ez a cikk a gyakori telepítési hibákat ismerteti, valamint azt, hogy Hogyan oldhatók meg ezek a hibák az Azure Instance Metadata Service (IMDS) használata során.

## <a name="error-codes"></a>Hibakódok

| Hibakód | Hibaüzenet | Részletek és enyhítés |
| --- | ---------- | ----------------- |
| 400 | Hiányzó kötelező paraméter: " \<ParameterName> ". Javítsa ki a kérést, és próbálkozzon újra. | A hibakód egy hiányzó paramétert jelez. </br> A hiányzó paraméter hozzáadásával kapcsolatos további információkért lásd: a [Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | A paraméter értéke nem engedélyezett, vagy a paraméter értéke \<ParameterValue> ("") nem engedélyezett a (z) "ParameterName" paraméter esetében. Javítsa ki a kérést, és próbálkozzon újra. | A hibakód azt jelzi, hogy a kérelem formátuma nincs megfelelően konfigurálva. </br> További információkért lásd: a [Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response) a kérelem törzsének kijavításához és az újrapróbálkozáshoz. |
| 400 | Váratlan kérelem. Ellenőrizze a lekérdezési paramétereket, majd próbálkozzon újra. | A hibakód azt jelzi, hogy a kérelem formátuma nincs megfelelően konfigurálva. </br> További információkért lásd: a [Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response) a kérelem törzsének kijavításához és az újrapróbálkozáshoz. |
| 404 | Nem található terheléselosztó-metaadatok. Ellenőrizze, hogy a virtuális gép nem alapszintű SKU Load balancert használ-e, majd próbálkozzon újra később. | A hibakód azt jelzi, hogy a virtuális gép nincs hozzárendelve egy terheléselosztó, vagy a terheléselosztó alapszintű SKU a standard helyett. </br> További információ: rövid útmutató [: nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához a Azure Portal használatával](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) a standard Load Balancer üzembe helyezéséhez.|
| 404 | Az API nem található: Path = " \<UrlPath> ", metódus = " \<Method> " | A hibakód az elérési út hibás konfigurációját jelzi. </br> További információkért lásd: a [Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response) a kérelem törzsének kijavításához és az újrapróbálkozáshoz.|
| 405 | A http-metódus nem engedélyezett: Path = " \<UrlPath> ", metódus = " \<Method> " | A hibakód egy nem támogatott HTTP-műveletet jelez. </br> További információ: [Azure instance metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) a támogatott műveletekhez. |
| 429 | Túl sok kérelem | A hibakód a díjszabási korlátot jelzi. </br> A díjszabással kapcsolatos további információkért lásd: [Azure instance metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting).|
| 400 | A kérelem törzse nagyobb, mint MaxBodyLength:... | A hibakód a MaxBodyLength nagyobb kérést jelez. </br> A törzs hosszával kapcsolatos további információkért lásd: [a Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | A paraméter kulcsának hossza nagyobb, mint MaxParameterKeyLength:... | A hibakód a MaxParameterKeyLength nagyobb hosszúságú paramétert jelez. </br> A törzs hosszával kapcsolatos további információkért lásd: [a Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | A paraméter értékének hossza nagyobb, mint MaxParameterValueLength:... | A hibakód a MaxParameterValueLength nagyobb hosszúságú paramétert jelez. </br> Az érték hosszával kapcsolatos további információkért lásd: [a Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | A paraméter fejlécének értéke nagyobb, mint MaxHeaderValueLength:... | A hibakód azt jelzi, hogy a paraméter fejlécének értéke nagyobb, mint a MaxHeaderValueLength. </br> Az érték hosszával kapcsolatos további információkért lásd: [a Load Balancer metaadatainak beolvasása az Azure instance metadata Service (IMDS) használatával](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | A Load Balancer metaadat-API jelenleg nem érhető el. Próbálkozzon újra később | A hibakód azt jelzi, hogy az API kiépíthető. Próbálkozzon később a kéréssel. |
| 404 | a/metadata/loadbalancer jelenleg nem érhető el | A hibakód azt jelzi, hogy az API az engedélyezés folyamatban van. Próbálkozzon később a kéréssel. |
| 503 | A belső szolgáltatás nem érhető el. Próbálkozzon újra később  | A hibakód azt jelzi, hogy az API átmenetileg nem érhető el. Próbálkozzon később a kéréssel. |
|  |  |

## <a name="next-steps"></a>Következő lépések

További információ az [Azure instance metadata Service](../virtual-machines/windows/instance-metadata-service.md)

