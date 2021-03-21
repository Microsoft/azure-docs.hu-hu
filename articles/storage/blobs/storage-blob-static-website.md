---
title: Statikus webhely üzemeltetés az Azure Storage-ban
description: Azure Storage – statikus webhely üzemeltetése, költséghatékony, méretezhető megoldás a modern webalkalmazások üzemeltetéséhez.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: bbb996362df1f99a8702de310bc11f6828cc303a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008450"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statikus webhely üzemeltetés az Azure Storage-ban

A statikus tartalom (HTML-, CSS-, JavaScript-és képfájlok) közvetlenül egy *$web* nevű tárolóból is kiszolgálható. A tartalom Azure Storage-ban való üzemeltetése lehetővé teszi, hogy olyan kiszolgáló nélküli architektúrákat használjon, amelyek a [Azure functions](../../azure-functions/functions-overview.md) és más platformként nyújtott szolgáltatásokat is tartalmazzák. Az Azure Storage statikus webhelyének üzemeltetése kiváló megoldás olyan esetekben, amikor nincs szükség webkiszolgálóra a tartalom megjelenítéséhez.

[App Service statikus Web Apps](https://azure.microsoft.com/services/app-service/static/) az Azure Storage statikus webhelyeinek üzemeltetése, és olyan esetekben is megfelelő, amikor nincs szükség webkiszolgálóra a tartalom megjelenítéséhez. App Service statikus Web Apps teljes körűen felügyelt folyamatos integrációs és folyamatos teljesítési (CI/CD) munkafolyamatot biztosít a GitHub-forrásból a globális üzembe helyezésig.

Ha a tartalom megjelenítéséhez webkiszolgálóra van szüksége, használhatja a [Azure app Service](https://azure.microsoft.com/services/app-service/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up-a-static-website"></a>Statikus webhely beállítása

A statikus webhely üzemeltetése olyan szolgáltatás, amelyet engedélyeznie kell a Storage-fiókban.

A statikus webhely üzemeltetésének engedélyezéséhez válassza ki az alapértelmezett fájl nevét, majd opcionálisan adja meg az Egyéni 404-oldal elérési útját. Ha egy **$web** nevű blob Storage-tároló még nem létezik a fiókban, a rendszer létrehoz egyet. Adja hozzá a hely fájljait a tárolóhoz.

Részletes útmutatásért lásd: [statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website-how-to.md).

![Azure Storage – statikus webhelyek mérőszámai metrika](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

A **$web** tárolóban lévő fájlok megkülönböztetik a kis-és nagybetűket, és névtelen hozzáférési kérelmeken keresztül érhetők el, és csak olvasási műveleteken keresztül érhetőek el.

## <a name="uploading-content"></a>Tartalom feltöltése

A következő eszközök bármelyikével feltöltheti a tartalmakat a **$web** tárolóba:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell-modul](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-bővítmény](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Tartalom megtekintése

A felhasználók a webhely nyilvános URL-címének használatával tekinthetik meg a tartalmak böngészőből való megtekintését. Az URL-címet a Azure Portal, az Azure CLI vagy a PowerShell használatával keresheti meg. Tekintse [meg a webhely URL-címét](storage-blob-static-website-how-to.md#portal-find-url).

Ha a kiszolgáló 404 hibát ad vissza, és nem adott meg hibaüzenetet a webhely engedélyezésekor, akkor a rendszer az alapértelmezett 404 lapot adja vissza a felhasználónak.

> [!NOTE]
> A statikus webhely nem támogatja az [CORS-támogatást az Azure Storage-](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) hoz.

### <a name="regional-codes"></a>Regionális kódok

A hely URL-címe tartalmaz egy regionális kódot. Például az URL-cím `https://contosoblobaccount.z22.web.core.windows.net/` regionális kódot tartalmaz `z22` .

Habár a kódnak az URL-címben kell maradnia, csak belső használatra szolgál, és semmilyen más módon nem kell ezt a kódot használnia.

A statikus webhelyek üzemeltetésének engedélyezésekor megadott index-dokumentum akkor jelenik meg, amikor a felhasználók megnyitják a helyet, és nem határoznak meg konkrét fájlt (például: `https://contosoblobaccount.z22.web.core.windows.net` ).

### <a name="secondary-endpoints"></a>Másodlagos végpontok

Ha [a redundanciát egy másodlagos régióban](../common/storage-redundancy.md#redundancy-in-a-secondary-region)állítja be, akkor a webhely tartalmát másodlagos végpont használatával is elérheti. Mivel az adatreplikációt a másodlagos régiókba aszinkron módon replikálja a rendszer, a másodlagos végponton elérhető fájlok nem mindig szinkronban vannak az elsődleges végponton elérhető fájlokkal.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>A webes tároló nyilvános hozzáférési szintjének beállításának hatása

Módosíthatja a **$web** tároló nyilvános hozzáférési szintjét, de ez nem befolyásolja az elsődleges statikus webhely végpontját, mert ezek a fájlok névtelen hozzáférési kérelmeken keresztül vannak kézbesítve. Ez azt jelenti, hogy a nyilvános (csak olvasható) hozzáférés minden fájlhoz.

Az alábbi képernyőfelvételen a Azure Portal nyilvános hozzáférési szintjének beállítása látható:

![A nyilvános hozzáférési szint megadását bemutató képernyőkép a portálon](./media/anonymous-read-access-configure/configure-public-access-container.png)

Noha a rendszer nem érinti az elsődleges statikus webhely végpontját, a nyilvános hozzáférési szint módosítása hatással van az elsődleges blob Service-végpontra.

Ha például módosítja a **$web** tároló nyilvános hozzáférési szintjét **(nincs névtelen hozzáférés)** a **blobhoz (névtelen olvasási hozzáférés csak Blobok esetén)**, akkor az elsődleges statikus webhely végpontjának való nyilvános hozzáférés szintje `https://contosoblobaccount.z22.web.core.windows.net/index.html` nem változik.

Az elsődleges blob Service-végponthoz való nyilvános hozzáférés azonban `https://contosoblobaccount.blob.core.windows.net/$web/index.html` magánjellegűről nyilvánosra változik. Most a felhasználók megnyithatja a fájlt a két végpont egyikével.

Ha letiltja a nyilvános hozzáférést a Storage-fiókon, az nem érinti az adott Storage-fiókban üzemeltetett statikus webhelyeket. További információ: [Névtelen nyilvános olvasási hozzáférés beállítása tárolók és Blobok](anonymous-read-access-configure.md)számára.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Egyéni tartomány leképezése statikus webhely URL-címére

A statikus webhelyet egyéni tartományon keresztül is elérhetővé teheti.

Könnyebben engedélyezhető a HTTP-hozzáférés az egyéni tartományhoz, mivel az Azure Storage natív módon támogatja azt. A HTTPS engedélyezéséhez Azure CDN kell használnia, mivel az Azure Storage még nem támogatja natív módon a HTTPS-t az egyéni tartományokkal. részletes útmutatásért lásd: [egyéni tartomány leképezése Azure Blob Storage végpontra](storage-custom-domain-name.md) .

Ha a Storage-fiók úgy van konfigurálva, hogy [biztonságos átvitelt igényel](../common/storage-require-secure-transfer.md) a HTTPS protokollon keresztül, akkor a felhasználóknak a https-végpontot kell használniuk.

> [!TIP]
> Érdemes lehet tartományt üzemeltetni az Azure-ban. További információ: [tartomány üzemeltetése Azure DNSban](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>HTTP-fejlécek hozzáadása

A statikus webhely funkciójának részeként nincs lehetőség fejlécek konfigurálására. Az Azure CDN segítségével azonban fejléceket adhat hozzá, és hozzáfűzheti (vagy felülírhatja) a fejlécek értékeit. Lásd: [a Standard Rules Engine referenciája Azure CDN](../../cdn/cdn-standard-rules-engine-reference.md).

Ha fejléceket kíván használni a gyorsítótárazás vezérléséhez, tekintse meg [a vezérlés Azure CDN gyorsítótárazási viselkedés a gyorsítótárazási szabályokkal](../../cdn/cdn-caching-rules.md)című témakört.

## <a name="multi-region-website-hosting"></a>Több régiós webhely üzemeltetése

Ha több földrajzi helyen szeretné üzemeltetni a webhelyeket, javasoljuk, hogy használjon egy [Content Delivery Network](../../cdn/index.yml) a regionális gyorsítótárazáshoz. Ha az egyes régiókban különböző tartalmakat szeretne kiszolgálni, az [Azure bejárati ajtót](../../frontdoor/index.yml) kell használnia. Emellett feladatátvételi képességeket is biztosít. Az [Azure Traffic Manager](../../traffic-manager/index.yml) használata nem ajánlott, ha egyéni tartományt tervez használni. Problémák merülhetnek fel amiatt, hogy az Azure Storage hogyan ellenőrzi az egyéni tartományneveket.


## <a name="pricing"></a>Díjszabás

A statikus webhelyek üzemeltetése díjmentesen engedélyezhető. Csak a hely által használt BLOB Storage-hoz és az üzemeltetési költségekhez kell fizetnie. Az Azure Blob Storage áraival kapcsolatos további információkért tekintse meg az [azure blob Storage díjszabási oldalát](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mérőszámok

A metrikák a statikus webhelyek oldalain is engedélyezhetők. A metrikák engedélyezése után a **$web** tárolóban található fájlokra vonatkozó forgalmi statisztikát a metrikák irányítópultján kell jelenteni.

A mérőszámok statikus webhely oldalain való engedélyezéséhez tekintse [meg a metrikák engedélyezése statikus webhelyeken](storage-blob-static-website-how-to.md#metrics)című témakört.

## <a name="next-steps"></a>Következő lépések

* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website-how-to.md)
* [Egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md)
* [Azure Functions](../../azure-functions/functions-overview.md)
* [Azure App Service](../../app-service/overview.md)
* [Az első kiszolgáló nélküli Webalkalmazás létrehozása](/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben](../../dns/dns-delegate-domain-azure-dns.md)