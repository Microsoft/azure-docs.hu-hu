---
title: Háttérrendszer-és háttér-készletek az Azure bejárati ajtóban | Microsoft Docs
description: Ez a cikk ismerteti, hogy milyen háttér-és háttér-készletek találhatók az előtérben lévő konfigurációban.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449299"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Háttérrendszer és háttérbeli készletek az Azure-ban – bejárati ajtó
Ez a cikk bemutatja, hogyan képezhető le a webalkalmazások üzembe helyezése az Azure bejárati ajtóval. Emellett az alkalmazási háttérrendszer körüli bejárati konfigurációban használt különböző fogalmakat is ismerteti.

## <a name="backends"></a>Háttérrendszerek
A háttérrendszer egy adott régióban található webalkalmazás-telepítésre hivatkozik. A bejárati ajtó támogatja mind az Azure-, mind a nem Azure-erőforrásokat a háttér-készletben. Az alkalmazás lehet a helyszíni adatközpontban, vagy egy másik felhőalapú szolgáltatóban található.

Az előtérben futó háttérrendszer az alkalmazás állomásneve vagy nyilvános IP-címére hivatkozik, amely az ügyfelek kérelmeit szolgálja ki. A háttérrendszer nem tévesztendő össze az adatbázis szintjével, a tárolási szintjével és így tovább. A háttereket az alkalmazás-háttér nyilvános végpontjának kell tekinteni. Amikor felvesz egy háttérrendszer-háttérrendszer-készletet, a következőket is hozzá kell adnia:

- **Háttérbeli gazdagép típusa** A hozzáadni kívánt erőforrás típusa. A bejárati ajtó támogatja az App Service, a Cloud Service vagy a Storage szolgáltatásból származó alkalmazás-hátterek automatikus észlelését. Ha egy másik erőforrást szeretne használni az Azure-ban, vagy akár egy nem Azure-beli hátteret is, válassza az **Egyéni gazdagép** lehetőséget.

    >[!IMPORTANT]
    >A konfiguráció során az API-k nem ellenőrzik, hogy a háttérrendszer nem érhető-e el az ajtós környezetekben. Győződjön meg arról, hogy a bejárati ajtó elérheti a hátteret.

- **Az előfizetés és a háttérbeli gazdagép neve**. Ha nem választotta ki az **Egyéni gazdagép** a háttérbeli gazdagéphez lehetőséget, válassza ki a megfelelő előfizetést, és a megfelelő háttér-gazdagép nevét a felhasználói felületen.

- **Háttérbeli állomásfejléc**. A gazdagép fejlécének értéke minden kérelem esetében a háttérbe kerül. További információ: [backend Host header](#hostheader).

- **Prioritás**. Rendeljen prioritásokat a különböző háttérekhez, ha az elsődleges szolgáltatási hátteret szeretné használni az összes forgalomhoz. Adja meg a biztonsági mentést is, ha az elsődleges vagy a biztonsági mentési háttérrendszer nem érhető el. További információ: Priority ( [prioritás](front-door-routing-methods.md#priority)).

- **Súlyozás**. Súlyok hozzárendelésével a különböző háttérrendszer-adatokkal terjesztheti a forgalmat a különféle háttérrendszer-csoportokon belül, akár egyenletesen, akár súlyozási együtthatók alapján. További információ: [súlyok](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Háttérbeli gazdagép fejléce

A háttérben a háttérbe továbbított kérések közé tartozik egy állomásfejléc mező, amelyet a háttér a célként megadott erőforrás lekérésére használ. A mező értéke általában a gazdagép fejlécével és portjával rendelkező háttér-URI-ből származik.

A (z) rendszerre vonatkozó kérelem például `www.contoso.com` a gazdagép fejlécének www.contoso.com lesz. Ha Azure Portalt használ a háttér konfigurálásához, a mező alapértelmezett értéke a háttér állomásneve. Ha a háttérrendszer contoso-westus.azurewebsites.net, a Azure Portal a háttérbeli állomásfejléc automatikusan kitöltött értéke contoso-westus.azurewebsites.net lesz. Ha azonban Azure Resource Manager sablonokat vagy egy másik módszert használ a mező explicit beállítása nélkül, akkor a bejárati ajtó a gazdagép fejlécének értékeként elküldi a bejövő állomásnév nevét. Ha a kérést a www \. contoso.com, a háttér pedig olyan contoso-westus.azurewebsites.net, amely üres fejlécet tartalmaz, akkor a bejárati ajtó a következőt adja meg: www \. contoso.com.

A legtöbb alkalmazás-háttér (az Azure Web Apps, a blob Storage és a Cloud Services) megköveteli, hogy a gazdagép fejléce megfeleljen a háttér tartományának. Ugyanakkor a háttér-gazdagép, amely a backend útvonalra mutat, egy másik állomásnevet fog használni, például www.contoso.net.

Ha a háttérrendszer megköveteli, hogy a gazdagép fejléce megfeleljen a háttérbeli állomásnévnek, ellenőrizze, hogy a háttérbeli állomásfejléc tartalmazza-e a háttér állomásnevét.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>A háttérrendszer-állomásfejléc konfigurálása a háttérrendszer számára

A háttérbeli **gazdagép fejlécének** beállítása a háttérrendszer-készletben:

1. Nyissa meg az előtérben található erőforrást, és válassza ki a háttérrendszer-készletet a konfiguráláshoz.

2. Ha még nem tette meg, vegyen fel egy hátteret, vagy szerkesszen egy meglévőt.

3. Állítsa a háttérbeli állomásfejléc mezőt egyéni értékre, vagy hagyja üresen. A rendszer a bejövő kérelem állomásneveként fogja használni a gazdagép fejlécének értékét.

## <a name="backend-pools"></a>Háttér-készletek
A bejárati ajtón lévő háttérrendszer az alkalmazáshoz hasonló adatforgalmat fogadó háttérrendszer-készletre hivatkozik. Ez azt jelenti, hogy az alkalmazás példányainak logikai csoportosítása a világon, amely ugyanazt a forgalmat kapja, és a várt működésre reagál. Ezek a hátterek különböző régiókban vagy ugyanazon a régión belül vannak telepítve. Az összes háttér aktív/aktív üzembe helyezési módban lehet, vagy az aktív/passzív konfigurációként van meghatározva.

A háttér-készletek határozzák meg, hogy a különböző háttérrendszer milyen módon legyen kiértékelve az állapot-ellenőrzéseken keresztül. Azt is meghatározza, hogyan történik a terheléselosztás a közöttük.

### <a name="health-probes"></a>Állapotminták
A bejárati ajtó rendszeres HTTP/HTTPS mintavételi kérelmeket küld az egyes konfigurált háttérrendszer-példányoknak. A mintavételi kérelmek meghatározzák az egyes háttérrendszer közelségét és állapotát a végfelhasználói kérelmek elosztása érdekében. A háttérbeli készlet állapot-mintavételi beállításai határozzák meg, hogy az alkalmazás-háttérrendszer állapotának lekérdezése hogyan történjen. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

- **Elérési út**: a háttér-készletben található összes háttérrendszer mintavételi kérelméhez használt URL-cím. Ha például az egyik háttérrendszer contoso-westus.azurewebsites.net, és az elérési út a/Probe/test.aspx értékre van állítva, akkor a bejárati ajtó környezetei, feltéve, hogy a protokoll HTTP-értékre van állítva, a rendszer az állapot-mintavételi kérelmeket http//contoso-westus.azurewebsites.net/probe/test.aspx-re küldi \: .

- **Protokoll**: meghatározza, hogy a rendszer elküldje-e az állapot-mintavételi kérelmeket a háttérbe a http-vagy HTTPS-protokollal.

- **Metódus**: az állapotadatok küldéséhez használandó http-metódus. A lehetőségek közé tartozik a GET vagy a HEAD (alapértelmezett).
    > [!NOTE]
    > A háttérbeli terhelések és a költségek csökkentése érdekében a bejárati ajtó javasolja az állapot-mintavételek HEAD kérelmének használatát.

- **Időköz (másodperc)**: meghatározza az állapot-mintavételek gyakoriságát a háttérbe, illetve azokat az intervallumokat, amelyekben az egyes bejárati környezetek mintavételt küldenek.

    >[!NOTE]
    >A gyorsabb feladatátvétel érdekében állítsa az intervallumot alacsonyabb értékre. Minél alacsonyabb az érték, annál nagyobb az állapot mintavételi mennyisége, amelyet a hátterek kapnak. Ha például az intervallum 30 másodpercre van állítva, azaz a 100-es bejárati ajtó globálisan jelenik meg, az egyes háttérrendszer percenként körülbelül 200 mintavételi kérést fog kapni.

További információ: [Health](front-door-health-probes.md)mintavétel.

### <a name="load-balancing-settings"></a>Terheléselosztási beállítások
A háttér-készlet terheléselosztási beállításai határozzák meg, hogyan értékeljük ki az állapotadatok kiértékelését. Ezek a beállítások határozzák meg, hogy a háttér állapota Kifogástalan-e. Azt is bemutatják, hogyan kell terheléselosztást alkalmazni a különböző háttérrendszer-adatforgalom között a háttér-készletben. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

- **Minta mérete** Meghatározza, hogy a háttér-állapot kiértékeléséhez hány mintát kell figyelembe venni.

- A **minta mérete sikeres**. Meghatározza a korábban említett minta méretét, a háttér kifogástalan meghívásához szükséges sikeres minták számát. Tegyük fel például, hogy a bejárati ajtó állapotának mintavételi intervalluma 30 másodperc, a minta mérete 5, a sikeres minta mérete pedig 3. Minden alkalommal, amikor kiértékeljük a háttérrendszer állapotát, megvizsgáljuk az utolsó öt mintát 150 másodpercnél (5 x 30). Legalább három sikeres mintavételre van szükség ahhoz, hogy a hátteret Kifogástalan állapotba lehessen nyilvánítani.

- **Késési érzékenység (további késés)**. Azt határozza meg, hogy a bejárati ajtón szeretné-e elküldeni a kérést a háttérrendszer-mérési érzékenységi tartományon belül, vagy továbbítsa a kérést a legközelebbi háttérbe.

További információ: a [minimális késésen alapuló útválasztási módszer](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Következő lépések

- [Bejárati ajtó profiljának létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)
