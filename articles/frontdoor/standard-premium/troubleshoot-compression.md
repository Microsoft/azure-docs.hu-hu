---
title: Fájltömörítés hibaelhárítása Az Azure bejárati ajtó standard/Premium
description: Ismerje meg, hogy miként lehet elhárítani a fájltömörítés problémáit az Azure-beli bejárati ajtón. Ez a cikk számos lehetséges okot tárgyal.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100229"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Azure bejárati ajtó standard/prémium fájl tömörítésének hibaelhárítása

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ez a cikk segítséget nyújt az Azure bejárati ajtó standard/prémium fájltömörítés problémáinak elhárításához.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Hibajelenség

Az útvonal tömörítése engedélyezve van, de a fájlok kibontása nem történik meg.

> [!TIP]
> Annak megállapításához, hogy a fájlok tömörítése folyamatban van-e, olyan eszközt kell használnia, mint a [Hegedűs](https://www.telerik.com/fiddler) vagy a böngésző [fejlesztői eszközei](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Győződjön meg arról, hogy a gyorsítótárazott CDN-tartalommal visszaadott HTTP-válasz fejlécei szerepelnek.  Ha a fejlécben a `Content-Encoding` **gzip**, a **bzip2** vagy a **deflate** érték szerepel, a rendszer tömöríti a tartalmat.
> 
> ![Content-Encoding fejléc](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Ok

Több lehetséges oka van, többek között a következők:

* A kért tartalom nem alkalmas a tömörítésre.
* A tömörítés nincs engedélyezve a kért fájltípushoz.
* A HTTP-kérelem nem tartalmaz egy érvényes tömörítési típust kérő fejlécet.
* A forrás darabolásos tartalmat küld.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

> [!TIP]
> Az új végpontok üzembe helyezéséhez hasonlóan a AFD-konfiguráció módosításai eltarthat egy ideig a hálózaton való propagáláshoz.  A módosítások általában 90 percen belül érvénybe lépnek.  Ha első alkalommal állít be tömörítést a CDN-végponthoz, érdemes 1-2 órát várnia, hogy a tömörítési beállítások propagálva legyenek a pop-ra. 
> 

### <a name="verify-the-request"></a>A kérelem ellenőrzése

Először is ellenőrizze a kérést. A böngésző **[fejlesztői eszközeivel](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** megtekintheti az elvégzett kérelmeket.

* Ellenőrizze, hogy a rendszer elküldte-e a kérést a végpont URL-címére, `<endpointname>.z01.azurefd.net` és ne a forrását.
* Ellenőrizze, hogy a kérelem tartalmazza-e az **Accept-Encoding** fejlécet, és a fejléc értéke **gzip**, **deflate** vagy **bzip2**.

![CDN-kérelmek fejlécei](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Tömörítési beállítások ellenőrzése

A [Azure Portal](https://portal.azure.com) navigáljon a végponthoz, és válassza a **Konfigurálás** gombot az útvonalak panelen. Ellenőrizze, hogy **engedélyezve** van-e a tömörítés.

![CDN-tömörítési beállítások](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>A kérelem megtekintése a forrás-kiszolgálón a következőn **keresztül** : fejléc

A **on** HTTP-fejléc azt jelzi, hogy a webkiszolgálón a kérést egy proxykiszolgáló adja át.  A Microsoft IIS-webkiszolgálók alapértelmezés szerint nem tömörítik a válaszokat, ha a **kérelem fejlécet** tartalmaz.  A viselkedés felülbírálásához tegye a következőket:

* **IIS 6**: állítsa be a HcNoCompressionForProxies = "false" értéket az IIS-metabázis tulajdonságai között. További információ: az [IIS 6 tömörítése](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 és up**: állítsa a **NoCompressionForHttp10** és  a noCompressionForProxies *értéket hamis* értékre a kiszolgálói konfigurációban. További információ: [HTTP-tömörítés](https://www.iis.net/configreference/system.webserver/httpcompression).
