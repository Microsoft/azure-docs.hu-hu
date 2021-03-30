---
title: Azure CDN-végpontok hibaelhárítása – 404 állapotkód
description: Ismerje meg, hogy miként lehet elhárítani az Azure Content Delivery Network-végpontokkal kapcsolatos problémákat, amelyek 404 HTTP-válasz állapotkódot adnak vissza.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192480"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>404 állapotkódot visszaadó Azure CDN-végpontok hibaelhárítása
Ez a cikk lehetővé teszi az Azure Content Delivery Network-(CDN-) végpontokkal kapcsolatos hibák elhárítását, amelyek a 404 HTTP-válasz állapotkódot adják vissza.

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és a stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Azt is megteheti, hogy Azure-támogatási incidenst is beküld. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.

## <a name="symptom"></a>Hibajelenség
Létrehozott egy CDN-profilt és egy végpontot, de úgy tűnik, hogy a tartalom nem érhető el a CDN-ben. Azok a felhasználók, akik a CDN URL-címen keresztül próbálnak hozzáférni a tartalomhoz, HTTP 404 állapotkódot kapnak. 

## <a name="cause"></a>Ok
Több lehetséges oka van, többek között a következők:

* A fájl forrása nem látható a CDN számára.
* A végpont helytelenül van konfigurálva, így a CDN nem megfelelő helyen fog megjelenni.
* A gazdagép elutasítja a gazdagép fejlécét a CDN-ből.
* A végpontnak nem volt ideje propagálni a CDN-ben.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!IMPORTANT]
> A CDN-végpont létrehozása után nem lesz azonnal elérhető, mivel a regisztráció a CDN-en keresztül propagálható:
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 90 percen belül fejeződik be. 
> 
> Ha elvégezte a jelen dokumentumban ismertetett lépéseket, és még mindig 404 választ kap, érdemes néhány órát várnia, hogy a támogatási jegy megnyitása előtt ismét ellenőrizze.
> 
> 

### <a name="check-the-origin-file"></a>Az eredeti fájl keresése
Először ellenőrizze, hogy a gyorsítótár elérhető-e a forráskiszolgálón, és nyilvánosan elérhető-e az interneten. Ennek leggyorsabb módja, ha egy privát vagy inkognitóban-munkamenetben nyit meg egy böngészőt, és közvetlenül a fájlra keres. Írja be vagy illessze be az URL-címet a cím mezőbe, és ellenőrizze, hogy az eredmény a várt fájlra esik-e. Tegyük fel például, hogy egy Azure Storage-fiókban található egy fájl, amely elérhető a https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt címen. Ha sikeresen betölti a fájl tartalmát, a rendszer átadja a tesztet.

![Sikerült!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Bár ez a leggyorsabb és legegyszerűbb módja annak, hogy a fájl nyilvánosan elérhető legyen, a szervezet egyes hálózati konfigurációi azt láthatják, hogy egy fájl nyilvánosan elérhető, ha valójában csak a hálózat felhasználói számára látható (még akkor is, ha az az Azure-ban található). Annak érdekében, hogy ez ne igaz legyen, tesztelje a fájlt egy külső böngészővel, például egy olyan mobileszközön, amely nem csatlakozik a szervezet hálózatához, vagy egy virtuális gépet az Azure-ban.
> 
> 

### <a name="check-the-origin-settings"></a>A forrás beállításainak megtekintése
Miután meggyőződött arról, hogy a fájl nyilvánosan elérhető az interneten, ellenőrizze a forrás beállításait. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilját, és válassza ki a hibaelhárítási végpontot. Az eredményül kapott **végpont** lapon válassza ki a forrást.  

![Végponti oldal, a forrás kiemelve](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Megjelenik a **forrás** lap. 

![Forrás lap](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Forrás típusa és állomásnév
Ellenőrizze, hogy helyesek-e a **forrás típusú** és a **forrás-állomásnév** értékei. Ebben a példában a https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt az URL-cím állomásnév része *cdndocdemo.blob.Core.Windows.net*, ami helyes. Mivel az Azure Storage, a Web App és a Cloud Service Origins a **forrás állomásnév** mező legördülő lista értékét használja, a helytelen helyesírások nem jelentenek problémát. Ha azonban egyéni forrást használ, győződjön meg arról, hogy az állomásnév helyesen van-e írva.

#### <a name="http-and-https-ports"></a>HTTP-és HTTPS-portok
Keresse meg a **http** -és **https-portokat**. A legtöbb esetben a 80-es és a 443-es verzió helyes, és nem kell módosítania a módosításokat.  Ha azonban a forráskiszolgáló egy másik portot figyel, akkor itt kell megjelenítenie. Ha nem biztos a dolgában, tekintse meg a forrás fájljának URL-címét. A HTTP-és HTTPS-specifikációk az 80-es és a 443-es portot használják alapértelmezettként. A példában a https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt nem ad meg portot, ezért a rendszer az alapértelmezett 443-as értéket feltételezi, és a beállítások helyesek.  

Tegyük fel azonban, hogy a korábban tesztelt forrásfájl URL-címe http: \/ /www.contoso.com:8080/file.txt. Jegyezze fel a *: 8080* részt az állomásnév szegmens végén. Ez a szám arra utasítja a böngészőt, hogy az 8080-es portot használja a webkiszolgálóhoz való csatlakozáshoz a www \. contoso.com, ezért a **http-port** mezőben meg kell adnia a *8080* értéket. Fontos megjegyezni, hogy ezek a portbeállítások csak azt a portot érintik, amelyet a végpont az információk forrásból való lekéréséhez használ.

> [!NOTE]
> A **Akamai-végpontokról Azure CDN standard** nem engedélyezi a teljes TCP-port tartományát az eredetek számára.  A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Az Akamai Azure CDN engedélyezett forrásportjai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>A végpont beállításainak bejelölése
A **végpont** lapon válassza a **Konfigurálás** gombot.

![Végpont lap kiemelve beállítás gombja](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Megjelenik a CDN-végpont **konfigurálása** oldal.

![Lap konfigurálása](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokollok
A **protokollok** esetében ellenőrizze, hogy az ügyfelek által használt protokoll van-e kiválasztva. Mivel az ügyfél által használt protokoll ugyanaz, mint a forrás eléréséhez, fontos, hogy az előző szakaszban helyesen konfigurálja a forrás portokat. A CDN-végpont csak az alapértelmezett HTTP-és HTTPS-portokon (80-es és 443-as) figyeli a forrás portoktól függetlenül.

Térjen vissza a feltételezett példához a http: \/ /www.contoso.com:8080/file.txt segítségével.  Ahogy emlékszik, a contoso a *8080* -as http-portot adta meg, de tegyük fel, hogy az *44300* -et a https-portként adták meg.  Ha a *contoso* nevű végpontot hoztak létre, a CDN-végpont állomásneve *contoso.azureedge.net* lesz.  Http: \/ /contoso.azureedge.net/file.txt-kérelem http-kérelem, ezért a végpont az 8080-as porton keresztül használja a http-t, hogy lekérje a forrásról.  HTTPS-en, https:/contoso.azureedge.net/file.txton keresztüli biztonságos kérelem esetén \/ a végpont a 44300-es porton HTTPS-t használ a fájl eredetből való beolvasásakor.

#### <a name="origin-host-header"></a>Forrás állomásfejléce
A **forrásként** szolgáló állomásfejléc az a állomásfejléc-érték, amelyet a rendszer az egyes kérelmekkel együtt küld a forrásnak.  A legtöbb esetben ez megegyezik a korábban ellenőrzött **forrás-állomásnévvel** .  Az ebben a mezőben szereplő helytelen érték általában 404 állapotot okoz, de valószínűleg más 4xx-állapotokat okoz, attól függően, hogy mit vár a forrás.

#### <a name="origin-path"></a>Forrás elérési útvonala
Végül ellenőrizzük a **forrás elérési útját**.  Alapértelmezés szerint ez az érték üres.  Ezt a mezőt csak akkor használja, ha le szeretné szűkíteni a CDN-ben elérhetővé tenni kívánt forrásként üzemeltetett erőforrások hatókörét.  

A példában azt akartuk, hogy a Storage-fiók összes erőforrása elérhető legyen, ezért a **forrás elérési útja** üres marad.  Ez azt jelenti, hogy a https: \/ /cdndocdemo.azureedge.net/publicblob/-re irányuló kérelem lorem.txt a végpontról a/publicblob/- *lorem.txt* kérelmeket kérő cdndocdemo.Core.Windows.net eredményez.  Hasonlóképpen, a https: \/ /cdndocdemo.azureedge.net/donotcache/status.png a forrástól érkező */donotcache/-status.png* kérő végpontot eredményez.

Mi a teendő, ha nem szeretné a CDN-t használni a forrás minden elérési útjához?  Tegyük fel, hogy csak a *publicblob* elérési útját akarta kitenni.  Ha a **forrás elérési útja** mezőben a */publicblob* értéket adja meg, akkor a végpontot a rendszer beilleszti a */publicblob* , mielőtt minden kérelem bekerül a forrásba.  Ez azt jelenti, hogy a https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt iránti kérelem mostantól az URL-cím, a */publicblob/lorem.txt* és a */publicblob* hozzáfűzését is végrehajtja az elejéhez. Ez a forrástól *lorem.txt/publicblob/publicblob/* kérelmet eredményez.  Ha az elérési út nem a tényleges fájlra van feloldva, a forrás 404 állapotot ad vissza.  Ebben a példában a lorem.txt beolvasásához szükséges helyes URL-cím valójában https: \/ /cdndocdemo.azureedge.net/lorem.txt.  Vegye figyelembe, hogy a */publicblob* elérési útja egyáltalán nem szerepel, mert az URL-cím kérésének része */lorem.txt* , és a végpont hozzáadja a */publicblob*-t, ami azt eredményezi, hogy a */publicblob/lorem.txt* a kérést a forrásnak.

