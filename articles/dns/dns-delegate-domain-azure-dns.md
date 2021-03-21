---
title: 'Oktatóanyag: a tartomány és az altartomány üzemeltetése – Azure DNS'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a Azure DNS a DNS-zónák üzemeltetéséhez.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: a8f64ab3141459142def12a1758b0fe0a94ca432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92282162"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben

Az Azure DNS használatával saját DNS-tartományt üzemeltethet, és kezelheti a tartomány DNS-rekordjait. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Tegyük fel, hogy megvette a „contoso.net” tartományt egy tartományregisztrálótól, majd létrehozott egy „contoso.net” nevű zónát az Azure DNS-ben. Mivel Ön a tartomány tulajdonosa, a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálói rekordjait. A regisztrátor a .NET szülő zónában tárolja az NS-rekordokat. Az internetes felhasználók ezután a Azure DNS zónában lesznek átirányítva a tartományba, amikor megpróbálják feloldani a DNS-rekordokat a contoso.net-ben.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy DNS-zónát.
> * A névkiszolgálók listájának beolvasása.
> * A tartomány delegálása.
> * Ellenőrizze, hogy a delegálás működik-e.


Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Meg kell adnia egy tartománynevet, hogy tesztelni tudja, hogy Azure DNS üzemeltethető-e. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

Ebben a példában a **contoso.net** fogja hivatkozni a szülőtartomány tartományára.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. DNS-zóna létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza ki a **DNS-zónákat**.

   ![DNS-zóna](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Válassza a **DNS-zóna létrehozása** lehetőséget.
1. A **DNS-zóna létrehozása** lapon adja meg a következő értékeket, majd válassza a **Létrehozás** lehetőséget: például **contoso.net**
      > [!NOTE] 
      > Ha a létrehozandó új zóna egy alárendelt zóna (például: Parent Zone = contoso.net Child Zone = child.contoso.net), tekintse meg az [új gyermek DNS-zóna létrehozásával foglalkozó oktatóanyagot](./tutorial-public-dns-zones-child.md) .

    | **Beállítás** | **Érték** | **Részletek** |
    |--|--|--|
    | **Projekt részletei:**  |  |  |
    | **Erőforráscsoport**    | ContosoRG | Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoport helye nincs hatással a DNS-zónára. A DNS-zóna helye mindig "globális", és nem jelenik meg. |
    | **Példány részletei:** |  |  |
    | **Zóna gyermeke**        | ne legyen bejelölve | Mivel ez a zóna **nem** [alárendelt zóna](./tutorial-public-dns-zones-child.md) , hagyja meg ezt a jelölést |
    | **Név**              | contoso.net | A szülő zóna nevének mezője      |
    | **Hely**          | USA keleti régiója | Ez a mező az erőforráscsoport létrehozásának részeként kiválasztott helyen alapul.  |
    

## <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak nevét. Minden zóna létrehozásakor az Azure DNS egy névkiszolgálói készletből választ ki egyet.

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** ablaktábláján válassza az **Összes erőforrás** lehetőséget. Az **Összes erőforrás** lapon válassza ki a saját DNS-zónáját. Ha a kiválasztott előfizetés már rendelkezik több erőforrással is, megadhatja a tartománynevet a **szűrés név alapján** mezőben az Application Gateway egyszerű eléréséhez. 

1. Kérdezze le a névkiszolgálókat a DNS-zóna lapon. Ebben a példában a contoso.net a *ns1-01.Azure-DNS.com*, a *ns2-01.azure-dns.net*, a *ns3-01.Azure-DNS.org* és a *ns4-01.Azure-DNS.info* névvel rendelkező kiszolgálók vannak hozzárendelve:

   ![Névkiszolgálók listája](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.

## <a name="delegate-the-domain"></a>A tartomány delegálása

Most, hogy létrehozta a DNS-zónát, és megvannak a névkiszolgálók is, frissítenie kell a szülőtartományt az Azure DNS-névkiszolgálókkal. Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. 

1. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS névkiszolgálóira.

1. Amikor Azure DNSre delegál egy tartományt, a Azure DNS által biztosított névkiszolgálók használatát kell használnia. Használja mind a négy névszervert, a tartomány nevétől függetlenül. A tartomány delegálásához nem szükséges, hogy a névkiszolgáló ugyanazt a legfelső szintű tartományt használja, mint a tartomány.

> [!NOTE]
> Amikor átmásolja a névkiszolgálók címeit, mindenképpen másolja át a cím végén szereplő pontot is. A záró karakterként szolgáló pont jelzi egy teljes tartománynév végét. Egyes regisztrátorok hozzáfűzik az időszakot, ha az NS neve nem a végén van. Ahhoz, hogy meg lehessen felelni a DNS RFC-nek, tartalmaznia kell a záró pontot.

Azok a delegálások, amelyek a saját zónában lévő névkiszolgálók használatát használják, esetenként a *hiúság névkiszolgálók* néven is ismertek, a Azure DNS jelenleg nem támogatottak.

## <a name="verify-the-delegation"></a>A delegálás ellenőrzése

A delegálás befejezése után ellenőrizheti, hogy működik-e egy eszköz, például az *nslookup* használatával a zóna SOA-rekordjának lekérdezéséhez. A SOA típusú rekord automatikusan létrejön a zóna létrehozásakor. Előfordulhat, hogy a delegálás befejezése után legalább 10 percet várnia kell, mielőtt sikeresen ellenőrizni tudja, hogy működik-e. Időbe telik a módosítások propagálása a DNS-rendszeren keresztül.

Nem kell megadnia a Azure DNS névkiszolgálók nevét. Ha a delegálást helyesen végezte el, a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat.

1. A parancssorban adjon meg egy, az alábbi példához hasonló nslookup parancsot:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Ellenőrizze, hogy a válasz a következő nslookup-kimenethez hasonlóan néz ki:

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

- Válassza ki a **contosoRG** erőforráscsoportot, majd válassza az **erőforráscsoport törlése** lehetőséget. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy DNS-zónát a tartományhoz, és delegálta azt Azure DNSra. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
