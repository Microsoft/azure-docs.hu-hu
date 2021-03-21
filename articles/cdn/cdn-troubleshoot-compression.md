---
title: Fájltömörítés hibaelhárítása Azure CDNban | Microsoft Docs
description: Ismerje meg, hogy miként lehet elhárítani a fájltömörítés problémáit az Azure Content Delivery Networkban. Ez a cikk számos lehetséges okot tárgyal.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996151"
---
# <a name="troubleshooting-cdn-file-compression"></a>A CDN-fájlok tömörítési hibáinak elhárítása
Ez a cikk segítséget nyújt a [CDN-fájlok tömörítésével](cdn-improve-performance.md)kapcsolatos problémák elhárításában.

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és a stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Azt is megteheti, hogy Azure-támogatási incidenst is beküld. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és kattintson a **támogatás kérése** lehetőségre.

## <a name="symptom"></a>Hibajelenség
A végpont tömörítése engedélyezve van, de a fájlok kibontása nem történik meg.

> [!TIP]
> Annak megállapításához, hogy a fájlok tömörítése folyamatban van-e, olyan eszközt kell használnia, mint a [Hegedűs](https://www.telerik.com/fiddler) vagy a böngésző [fejlesztői eszközei](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Győződjön meg arról, hogy a gyorsítótárazott CDN-tartalommal visszaadott HTTP-válasz fejlécei szerepelnek.  Ha a fejlécben a `Content-Encoding` **gzip**, a **bzip2** vagy a **deflate** érték szerepel, a rendszer tömöríti a tartalmat.
> 
> ![Content-Encoding fejléc](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Ok
Több lehetséges oka van, többek között a következők:

* A kért tartalom nem alkalmas a tömörítésre.
* A tömörítés nincs engedélyezve a kért fájltípushoz.
* A HTTP-kérelem nem tartalmaz egy érvényes tömörítési típust kérő fejlécet.
* A forrás darabolásos tartalmat küld.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!TIP]
> Az új végpontok üzembe helyezéséhez hasonlóan a CDN konfigurációs módosításai eltarthat egy ideig a hálózaton való propagáláshoz.  A módosítások általában 90 percen belül érvénybe lépnek.  Ha első alkalommal állít be tömörítést a CDN-végponthoz, érdemes 1-2 órát várnia, hogy a tömörítési beállítások propagálva legyenek a pop-ra. 
> 
> 

### <a name="verify-the-request"></a>A kérelem ellenőrzése
Először is végre kell hajtania egy gyors józan ész-ellenőrzését a kérelemben.  A böngésző [fejlesztői eszközeivel](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) megtekintheti az elvégzett kérelmeket.

* Ellenőrizze, hogy a rendszer elküldte-e a kérést a végpont URL-címére, `<endpointname>.azureedge.net` és ne a forrását.
* Ellenőrizze, hogy a kérelem tartalmazza-e az **Accept-Encoding** fejlécet, és a fejléc értéke **gzip**, **deflate** vagy **bzip2**.

> [!NOTE]
> **A Akamai-** profilokból Azure CDN csak a **gzip** -kódolást támogatja.
> 
> 

![CDN-kérelmek fejlécei](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Tömörítési beállítások ellenőrzése (szabványos CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN-profil **Azure CDN szabvány a Microsofttól**, **Azure CDN a standard from Verizon**, vagy **Azure CDN standard from Akamai** Profile. 
> 
> 

Navigáljon a végponthoz a [Azure Portalon](https://portal.azure.com) , majd kattintson a **Konfigurálás** gombra.

* Ellenőrizze, hogy engedélyezve van-e a tömörítés.
* Ellenőrizze, hogy a tömörített tartalomhoz tartozó MIME-típus szerepel-e a tömörített formátumok listáján.

![CDN-tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Tömörítési beállítások ellenőrzése (prémium CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN **-profil Azure CDN Premium a Verizon-** profilból.
> 
> 

Navigáljon a végponthoz a [Azure Portalon](https://portal.azure.com) , és kattintson a **kezelés** gombra.  Ekkor megnyílik a kiegészítő portál.  Vigye a kurzort a **nagyméretű http** -lapra, majd vigye a kurzort a **gyorsítótár beállításai** menü fölé.  Kattintson a **tömörítés** gombra. 

* Ellenőrizze, hogy engedélyezve van-e a tömörítés.
* Ellenőrizze, hogy a **fájltípusok** lista tartalmazza-e a MIME-típusok vesszővel tagolt listáját (szóközök nélkül).
* Ellenőrizze, hogy a tömörített tartalomhoz tartozó MIME-típus szerepel-e a tömörített formátumok listáján.

![CDN prémium tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>A tartalom gyorsítótárazásának ellenőrzése (Verizon CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN-profil **Azure CDN standard a** Verizon vagy a **Azure CDN Premium a Verizon** -profilból.
> 
> 

A böngésző fejlesztői eszközeinek használatával ellenőrizze a válasz fejléceit, és győződjön meg arról, hogy a fájl gyorsítótárazva van abban a régióban, ahol a rendszer kéri.

* Keresse meg a **kiszolgáló** válaszának fejlécét.  A fejlécnek a következő példában látható módon kell **MEGjelennie a Format platformmal (pop/Server ID)**.
* Keresse meg az **X-cache** válasz fejlécét.  A fejlécnek olvasnia kell a **találatot**.  

![CDN-válasz fejlécei](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Ellenőrizze, hogy a fájl megfelel-e a méret követelményeinek (Verizon CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN-profil **Azure CDN standard a** Verizon vagy a **Azure CDN Premium a Verizon** -profilból.
> 
> 

A tömörítéshez a fájlnak meg kell felelnie a következő méretre vonatkozó követelményeknek:

* 128 bájtnál nagyobb (Content-Length: 128)
* 3 MB-nál kisebb

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>A kérelem megtekintése a forrás-kiszolgálón a következőn **keresztül** : fejléc
A **on** HTTP-fejléc azt jelzi, hogy a webkiszolgálón a kérést egy proxykiszolgáló adja át.  A Microsoft IIS-webkiszolgálók alapértelmezés szerint nem tömörítik a válaszokat, ha a **kérelem fejlécet** tartalmaz.  A viselkedés felülbírálásához hajtsa végre a következőket:

* **IIS 6**: [set HCNOCOMPRESSIONFORPROXIES = "false" az IIS-metabázis tulajdonságaiban](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 és up**: [állítsa a **NoCompressionForHttp10** és a **noCompressionForProxies** értéket hamis értékre a kiszolgáló konfigurációja](https://www.iis.net/configreference/system.webserver/httpcompression)

