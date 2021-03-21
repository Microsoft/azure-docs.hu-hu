---
title: Oktatóanyag – Tárolóblobok elérése egyéni Azure CDN-tartomány használatával HTTPS-en keresztül
description: Megtudhatja, hogyan adhat hozzá Azure CDN egyéni tartományt, és hogyan engedélyezheti az adott tartományhoz tartozó HTTPS-t az egyéni blob Storage-végpont számára.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 296a51edf024551a03330f4843690e97c6abd47d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564883"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Oktatóanyag – Tárolóblobok elérése egyéni Azure CDN-tartomány használatával HTTPS-en keresztül

Miután integrálta az Azure Storage-fiókot az Azure Content Delivery Network (CDN) hálózattal, hozzáadhat egy egyéni tartományt, és engedélyezheti a HTTPS-t a tartományon az egyéni blobtároló végpontjához. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először integrálnia kell az Azure Storage-fiókot az Azure CDN-nel. További információért lásd az [Oktatóanyag: Azure Storage-fiók integrálása az Azure CDN-nel](cdn-create-a-storage-account-with-cdn.md) című cikket.

## <a name="add-a-custom-domain"></a>Egyéni tartomány hozzáadása
Miután létrehoz egy CDN-végpontot a profiljában, a végpont neve – amely az azureedge.net altartománya – része lesz annak az URL-címnek, amelyre alapértelmezés szerint a rendszer a CDN-tartalmat irányítja. Emellett lehetősége van arra, hogy CDN-végpontot rendeljen egy egyéni tartományhoz. Így a tartalom továbbításakor az egyéni tartomány neve szerepel majd az URL-ben a végpont neve helyett. Ha egyéni tartományt szeretne hozzáadni a végponthoz kövesse az [Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md) című oktatóanyagban szereplő utasításokat.

## <a name="configure-https"></a>HTTPS konfigurálása
A HTTPS-protokoll egyéni tartományon belüli használatával biztosítható, hogy a bizalmas adatokat a rendszer biztonságosan, TLS/SSL-titkosításon keresztül továbbítsa az interneten. Amikor a böngésző HTTPS-protokollal kapcsolódik egy webhelyhez, akkor ellenőrzi a webhely biztonsági tanúsítványát, és megállapítja, hogy azt arra jogosult hitelesítésszolgáltató adta-e ki. Ha az egyéni tartományon szeretné konfigurálni a HTTPS-t, kövesse a [HTTPS konfigurálása Azure CDN egyéni tartományon](cdn-custom-ssl.md) című oktatóanyag lépéseit.

## <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok
Ha a Blob Storage-végpont a névtelen olvasási hozzáférések elutasítására van konfigurálva, akkor minden, az egyéni tartományhoz küldött kérésben meg kell adnia egy [közös hozzáférésű jogosultságkód (SAS-)](cdn-sas-storage-support.md) tokent. Alapértelmezés szerint a Blob Storage-végpontok nem engedélyezik a névtelen olvasási hozzáférést. Az SAS-szel kapcsolatos további információkért lásd a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../storage/blobs/anonymous-read-access-configure.md) foglalkozó témakört.

Az Azure CDN figyelmen kívül hagyja a SAS-jogkivonathoz hozzáadott korlátozásokat. Például minden SAS-token rendelkezik lejárati idővel, ami azt jelenti, hogy a tartalmak lejárt SAS-tokennel is hozzáférhetők, amíg az adott tartalom véglegesen törölve nem lesz a CDN jelenléti pont (POP-) kiszolgálókról. A gyorsítótár válaszfejlécének beállításával szabályozható, hogy a rendszer meddig tárolja az adatokat az Azure CDN-ben. További információkért lásd: [Azure Storage-blobok lejárati idejének kezelése az Azure CDN-ben](cdn-manage-expiration-of-blob-content.md).

Ha ugyanazon végponthoz több SAS URL-címet hoz létre, fontolja meg a lekérdezési sztringek gyorsítótárazásának engedélyezését. Ezzel biztosíthatja, hogy a rendszer minden egyes URL-címet egyedi entitásként kezeljen. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP–HTTPS átirányítás
A HTTP-forgalom HTTPS-re való átirányítását úgy is megadhatja, ha URL-átirányítási szabályt hoz létre a [Standard Rules Engine](cdn-standard-rules-engine.md) vagy a [Verizon Premium Rules Engine](cdn-verizon-premium-rules-engine.md)használatával. A standard szintű szabályok motorja csak a Microsoft-profilokból Azure CDN érhető el, míg a Verizon Premium Rules Engine csak a Verizon-profilokból származó Azure CDN Premium verzióban érhető el.

![Microsoft átirányítási szabály](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

A fenti szabályban az állomásnév, az elérési út, a lekérdezési karakterlánc és a töredék hagyva az átirányítás során használt bejövő értékeket fogja eredményezni. 

![Verizon-átirányítási szabály](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

A fenti szabályban a *CDN-Endpoint-Name* kifejezés a CDN-végponthoz konfigurált nevet jelenti, amelyet a legördülő listából választhat ki. A *forrásútvonal* értéke a forrásként szolgáló tárfiókban szereplő útvonalra utal, ahol a statikus tartalmak található. Ha minden statikus tartalmat egyetlen tárolóban tárolja, cserélje le a *forrásútvonal* értékét az adott tároló nevére.

## <a name="pricing-and-billing"></a>Árak és számlázás
Ha a blobokat az Azure CDN-en keresztül éri el, akkor a [Blob Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/) alapján fizet a POP-kiszolgálók és a forrás (Blob Storage) közötti forgalomért, a POP-kiszolgálókról elért adatokért pedig az [Azure CDN díjszabása](https://azure.microsoft.com/pricing/details/cdn/) alapján.

Ha például rendelkezik egy Storage-fiókkal az Egyesült Államokban, amelyhez az Azure CDN-en keresztül fér hozzá, és valaki Európából az Azure CDN-en keresztül megkísérli az ott található blobok egyikéhez való hozzáférést, az Azure CDN először megkeresi az Európához legközelebb eső POP-kiszolgálót a blob számára. Ha megtalálta, az Azure CDN hozzáfér a blob másolatához, és a CDN díjszabását használja, mert a hozzáférés az Azure CDN-en történik. Ha nem talál ilyet, az Azure CDN átmásolja a blobot a POP-kiszolgálóra, amely a Blob Storage díjszabásában megadott kimenő forgalmi és tranzakciós díjakat vonja maga után, majd a POP-kiszolgálón hozzáfér a fájlhoz, amelyre már az Azure CDN díjszabása vonatkozik.

## <a name="next-steps"></a>Következő lépések
[Oktatóanyag: Azure CDN gyorsítótárazási szabályainak beállítása](cdn-caching-rules-tutorial.md)