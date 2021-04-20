---
title: 'Oktatóanyag: Saját tartomány és altartomány Azure DNS'
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a Azure DNS DNS-zónákat.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: c9c0568eb4d8a7403fc29f34a4c4e9f6e0fadecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738863"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben

Az Azure DNS használatával saját DNS-tartományt üzemeltethet, és kezelheti a tartomány DNS-rekordjait. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Tegyük fel, hogy a tartományt egy tartományregisztrálótól vásárolja meg, majd létrehoz egy zónát a névvel a `contoso.net` `contoso.net` Azure DNS. Mivel Ön a tartomány tulajdonosa, a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálói (NS) rekordjait. A regisztráló a .NET szülőzónában tárolja az NS-rekordokat. A világ internetfelhasználói ezután a saját tartományába Azure DNS, amikor megpróbálják feloldani a DNS-rekordokat a contoso.net.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * DNS-zóna létrehozása.
> * Névkiszolgálók listájának lekérése.
> * Delegálhatja a tartományt.
> * Ellenőrizze, hogy a delegálás működik-e.


Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.

## <a name="prerequisites"></a>Előfeltételek

A teszteléshez rendelkezésre álló tartománynévvel kell Azure DNS. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

Ebben a példában a szülőtartományra a `contoso.net` hivatkozunk.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. A [DNS-zóna Azure Portal](https://portal.azure.com/) gombra. Keresse meg és válassza ki a **DNS-zónákat.**

   ![DNS-zóna](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Válassza **a DNS-zóna létrehozása lehetőséget.**

1. A **DNS-zóna létrehozása lapon** adja meg a következő értékeket, majd válassza a **Létrehozás lehetőséget.** Például: `contoso.net`.

   > [!NOTE] 
   > Ha az új létrehoz egy gyermekzóna (például szülőzóna = Gyermekzóna = ), tekintse meg az új gyermek DNS-zóna létrehozásával `contoso.net` `child.contoso.net` kapcsolatos [oktatóanyagot](./tutorial-public-dns-zones-child.md)

    | **Beállítás** | **Érték** | **Részletek** |
    |--|--|--|
    | **Erőforráscsoport**    | ContosoRG | Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoport helye nincs hatással a DNS-zónára. A DNS-zóna helye mindig "globális", és nem jelenik meg. |
    | **Zóna gyermek**        | hagyja bejelölve | Mivel ez a zóna **nem** [gyermekzóna,](./tutorial-public-dns-zones-child.md) ne jelölje be |
    | **Név**              | `contoso.net` | A szülőzóna nevének mezője      |
    | **Hely**          | USA keleti régiója | Ez a mező az erőforráscsoport létrehozásakor kiválasztott helyen alapul  |
    

## <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak nevét. Azure DNS ad a névkiszolgálókat a készletből minden alkalommal, amikor létrehoznak egy zónát.

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** ablaktábláján válassza az **Összes erőforrás** lehetőséget. Az **Összes erőforrás** lapon válassza ki a saját DNS-zónáját. Ha a kiválasztott előfizetésben már több erőforrás található, az Alkalmazásátjáró  egyszerű eléréséhez megadhatja a tartománynevét a Szűrés név alapján mezőbe. 

1. Kérdezze le a névkiszolgálókat a DNS-zóna lapon. Ebben a példában a zónához a következő névkiszolgálók vannak `contoso.net` hozzárendelve: , * , és `ns1-01.azure-dns.com` `ns2-01.azure-dns.net` `ns3-01.azure-dns.org` `ns4-01.azure-dns.info` :

   ![Névkiszolgálók listája](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.

## <a name="delegate-the-domain"></a>A tartomány delegálása

A DNS-zóna létrehozása és a névkiszolgálók létrehozása után frissítenie kell a szülőtartományt a Azure DNS kiszolgálókkal. Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. 

1. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS névkiszolgálóira.

1. Amikor tartományt delegál egy Azure DNS számára, a rendszer által Azure DNS használnia. Használja mind a négy névkiszolgálót, a tartomány nevétől függetlenül. A tartománydelegáláshoz nem szükséges, hogy a névkiszolgáló ugyanazt a legfelső szintű tartományt használja, mint a tartomány.

> [!NOTE]
> Amikor átmásolja a névkiszolgálók címeit, mindenképpen másolja át a cím végén szereplő pontot is. A záró karakterként szolgáló pont jelzi egy teljes tartománynév végét. Egyes regisztrálók hozzáfűzik az időszakot, ha az NS-név nem végződik. A DNS RFC-nek való megfeleléshez foglalja bele a záró időszakot.

A saját zónájában névkiszolgálókat (más néven személyes névkiszolgálókat) használ delegálások jelenleg nem támogatottak a Azure DNS.

## <a name="verify-the-delegation"></a>A delegálás ellenőrzése

A delegálás befejezése után ellenőrizheti, hogy működik-e egy olyan eszközzel, mint például az *nslookup,* amely lekérdezi a zóna SoA-rekordját. A SOA típusú rekord automatikusan létrejön a zóna létrehozásakor. Előfordulhat, hogy a delegálás befejezése után legalább 10 percet várnia kell, mielőtt sikeresen ellenőrizheti a delegálást. Időbe telik a módosítások propagálása a DNS-rendszeren keresztül.

Nem kell megadnia a Azure DNS kiszolgálókat. Ha a delegálást helyesen végezte el, a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat.

1. Egy parancssorból adjon meg egy nslookup parancsot, amely az alábbi példához hasonló:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Ellenőrizze, hogy a válasz az alábbi nslookup-kimenethez hasonlóan néz-e ki:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja a **contosoRG** erőforráscsoportot. Ellenkező esetben törölje a **contosoRG** erőforráscsoportot és az oktatóanyag során létrehozott erőforrásokat.

Válassza ki a **contosoRG** erőforráscsoportot, majd válassza az **Erőforráscsoport törlése lehetőséget.** 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy DNS-zónát a tartományhoz, és delegálta azt a Azure DNS. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
