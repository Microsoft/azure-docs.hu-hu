---
title: Oktatóanyag – egyéni tartomány hozzáadása az Azure-beli bejárati ajtó konfigurációjához
description: Ebből az oktatóanyagból megtudhatja, hogyan regisztrálható egyéni tartomány az Azure Front Door szolgáltatásban.
services: frontdoor
documentationcenter: ''
author: jessie-jyy
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: yuajia
ms.openlocfilehash: 7e2f05a7d911ce2b311a423994d2b459de0fa269
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308863"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Oktatóanyag: Egyéni tartomány hozzáadása a Front Doorhoz

Ebből az oktatóanyagból elsajátíthatja, hogyan adhat hozzá egyéni tartományt a Front Doorhoz. Ha az Azure bejárati ajtót használja az alkalmazások kézbesítéséhez, egyéni tartományra van szükség, ha szeretné, hogy a saját tartományneve megjelenjen a végfelhasználói kérelemben. A látható tartománynév hasznos lehet az ügyfelei számára, és a vállalati arculat szempontjából is.

Miután létrehozta a bejárati ajtót, az alapértelmezett előtér-gazdagép, amely a (z) altartománya `azurefd.net` , az URL-cím tartalmazza a háttérbeli tartalomnak a háttérből alapértelmezés szerint történő kézbesítéséhez (például https: \/ /contoso-frontend.azurefd.net/activeusers.htm). A kényelmes használat érdekében az Azure Front Door lehetőséget ad arra, hogy egyéni tartományt társítson az alapértelmezett gazdagéphez. Ezzel a lehetőséggel a tartalom továbbításakor az egyéni tartomány neve lesz az URL-címben a Front Door tulajdonában lévő tartománynév helyett (például https:\//www.contoso.com/photo.png). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - CNAME DNS-rekord létrehozása.
> - Az egyéni tartomány társítása a Front Doorral.
> - Az egyéni tartomány ellenőrzése.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> A bejárati ajtó **nem** támogatja a [punycode](https://en.wikipedia.org/wiki/Punycode) -karakterekkel rendelkező egyéni tartományokat. 

## <a name="prerequisites"></a>Előfeltételek

* Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy Front Doort. További információkért lásd a [Rövid útmutató: Front Door létrehozása](quickstart-create-front-door.md) szakaszt.

* Ha még nem rendelkezik egyéni tartománnyal, először az egyiket kell megvásárolnia egy tartományi szolgáltatóval. Példákért lásd az [egyéni tartománynév vásárlásáról](../app-service/manage-custom-dns-buy-domain.md) szóló részt.

* Ha az Azure-t használja a [DNS-tartományok](../dns/dns-overview.md)üzemeltetéséhez, akkor a tartományi szolgáltató tartománynevét (DNS) delegálnia kell egy Azure DNS. További információ: [tartomány delegálása Azure DNSra](../dns/dns-delegate-domain-azure-dns.md). Ellenkező esetben, ha tartományi szolgáltatót használ a DNS-tartomány kezeléséhez, folytassa a [CNAME DNS-rekord létrehozásával](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>CNAME DNS-rekord létrehozása

Ahhoz, hogy egyéni tartományt lehessen használni a bejárati ajtóhoz, először létre kell hoznia egy kanonikus név (CNAME) rekordot a tartományi szolgáltatóval, hogy az a bejárati ajtó alapértelmezett előtér-gazdagépére mutasson (például contoso.azurefd.net). A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre. Az Azure bejárati ajtónál a forrás tartomány neve az Egyéni tartománynév, a cél tartomány neve pedig az alapértelmezett állomásnév. Miután a bejárati ajtó ellenőrizte a létrehozott CNAME-rekordot, a forrás egyéni tartományhoz (például a www contoso.com) irányuló forgalmat a \. rendszer átirányítja a megadott célhelyre az alapértelmezett előtér-gazdagéphez (például contoso-frontend.azurefd.net). 

Egy egyéni tartományt és altartományát egyszerre csak egyetlen bejárati ajtóhoz lehet társítani. Ugyanakkor több CNAME rekord használatával különböző altartományokat is használhat ugyanazon egyéni tartományból különböző bejárati ajtókhoz. A különböző altartományokkal rendelkező egyéni tartományokat is leképezheti ugyanahhoz a bejárati ajtóhoz.


## <a name="map-the-temporary-afdverify-subdomain"></a>Az ideiglenes afdverify altartomány leképezése

Amikor meglévő, éles környezetben futó tartományt képez le, speciális szempontokat kell figyelembe vennie. Az egyéni tartomány a Azure Portalban való regisztrálása közben előfordulhat, hogy a tartomány rövid állásidőt eredményez. A webes forgalom megszakításának elkerülése érdekében először az Azure afdverify altartománnyal társítsa az egyéni tartományt az előtérben lévő alapértelmezett előtér-gazdagéphez, és hozzon létre egy ideiglenes CNAME-leképezést. Ezzel a módszerrel a felhasználók megszakítás nélkül férhetnek hozzá a tartományhoz a DNS-hozzárendelés közben.

Ellenkező esetben, ha első alkalommal használja az egyéni tartományt, és az üzemi forgalom nem fut rajta, közvetlenül leképezheti az egyéni tartományt az előtérben. folytassa [az állandó egyéni tartomány leképezését](#map-the-permanent-custom-domain).

CNAME rekord létrehozása az afdverify altartománnyal:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldal helyét a szolgáltató dokumentációjának segítségével, vagy a webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** területeinek áttekintésével. 

3. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás                    | Típus  | Cél                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Forrás: adja meg az egyéni tartománynevet, beleértve a afdverify altartományt is, a következő formátumban: afdverify. _&lt; Egyéni tartománynév &gt;_. Például: afdverify.www.contoso.com. Ha helyettesítő karaktert (például \* . contoso.com) rendel, a forrás értéke ugyanaz, mint a helyettesítő karakter: afdverify.contoso.com.

    - Típus: Írja be a *CNAME* szöveget.

    - Cél: adja meg az alapértelmezett előtér-gazdagépet, beleértve a afdverify altartományt is, a következő formátumban: afdverify. _&lt; végpont neve &gt;_. azurefd.net. Például: afdverify.contoso-frontend.azurefd.net.

4. Mentse a módosításokat.

Példaképp a GoDaddy tartományregisztráló eljárása a következő:

1. Jelentkezzen be, és válassza ki a használni kívánt egyéni tartományt.

2. A Tartományok szakaszban válassza az **Összes kezelése**, majd a **DNS** | **Zónák kezelése** lehetőséget.

3. A **Tartománynév** mezőben adja meg az egyéni tartomány nevét, majd válassza a **Keresés** lehetőséget.

4. A **DNS-kezelés** lapon válassza a **Hozzáadás**, lehetőséget, majd válassza a **CNAME** elemet a **Típus** listából.

5. Töltse ki a következő mezőket a CNAME bejegyzéshez:

    - Típus: Hagyja kijelölve a *CNAME* elemet.

    - Gazdagép: Adja meg az egyéni tartománya altartományát az afdverify altartomány nevével együtt. Például: afdverify.www.

    - Erre mutat: Adja meg az alapértelmezett előtérbeli Front Door-gazdagépet az afdverify altartománnyal együtt. Például: afdverify.contoso-frontend.azurefd.net. 

    - TTL: hagyjon *egy órát* .

6. Kattintson a **Mentés** gombra.
 
    A CNAME bejegyzést a rendszer a DNS-rekordok táblázatához adja.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Az egyéni tartomány társítása a Front Doorral

Miután regisztrálta az egyéni tartományát, hozzáadhatja azt a Front Doorhoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és navigáljon arra a Front Doorra, amely tartalmazza azt az előtérbeli gazdagépet, amelyet az egyéni tartományhoz kíván leképezni.
    
2. A **bejárati ajtó tervezője** lapon válassza a "+" lehetőséget az egyéni tartomány hozzáadásához.
    
3. Adjon meg az **Egyéni tartomány** értékét. 

4. Az **Előtérbeli gazdagép** mezőben előre ki van töltve a CNAME rekordhoz céltartományként használt előtérbeli gazdagép neve, amely a Front Doorból van származtatva: *&lt;alapértelmezett gazdagépnév&gt;*.azurefd.net. A név nem módosítható.

5. Az **Egyéni gazdagépnév** mezőben adja meg az egyéni tartomány nevét az altartomány nevével együtt, amelyet a CNAME rekord forrástartományaként fog használni. Például: www \. contoso.com vagy CDN.contoso.com. Ne használja a afdverify aldomain nevét.

6. Válassza a **Hozzáadás** lehetőséget.

   Az Azure ellenőrzi, hogy a megadott egyéni tartománynév esetében létezik-e a CNAME rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz.

>[!WARNING]
> **Kötelező** gondoskodnia róla, hogy a Front Doorban az összes előtérbeli gazdagép (beleértve az egyéni tartományokat) rendelkezzen útválasztási szabállyal, hozzá társított alapértelmezett elérési úttal („/\*”). Vagyis minden egyes előtérbeli gazdagépnek rendelkeznie kell legalább egy olyan útválasztási szabállyal, amely az alapértelmezett útvonalat (\*) használja. Ha ez a feltétel nem teljesül, akkor a végfelhasználói forgalom útvonalválasztása nem lesz megfelelő.

## <a name="verify-the-custom-domain"></a>Az egyéni tartomány ellenőrzése

Miután elvégezte az egyéni tartomány regisztrációját, ellenőrizze, hogy az egyéni tartomány hivatkozik-e az alapértelmezett előtér-gazdagépre.
 
A böngészőjében navigáljon a fájl címére az egyéni tartomány használatával. Ha például az egyéni tartománya a robotics.contoso.com, a gyorsítótárazott fájl URL-címe a következő URL-címre fog hasonlítani: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Győződjön meg arról, hogy az eredmény ugyanaz, mint amikor közvetlenül *&lt; &gt; a bejárati* ajtóhoz fér hozzá. azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Az állandó egyéni tartomány leképezése

Ha ellenőrizte, hogy a afdverify altartomány sikeresen le lett képezve a bejárati ajtóra (vagy ha olyan új egyéni tartományt használ, amely nem éles környezetben van), akkor az egyéni tartományt közvetlenül az alapértelmezett előtér-gazdagéphez rendelheti.

CNAME rekord létrehozása az egyéni tartományhoz:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldalt a szolgáltató dokumentációjának segítségével, vagy a webhely **tartománynevet**, DNS-t vagy névkiszolgáló **-** **felügyeletet** tartalmazó területének megkeresésével. 

3. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás          | Típus  | Cél           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - Forrás: adja meg az egyéni tartománynevet (például www \. contoso.com).

   - Típus: Írja be a *CNAME* szöveget.

   - Cél: Adja meg az alapértelmezett előtérbeli Front Door-gazdagépnevet. A következő formátumúnak kell lennie:_&lt; hostname &gt;_. azurefd.net. Például: contoso-frontend.azurefd.net.

4. Mentse a módosításokat.

5. Ha korábban létrehozott egy ideiglenes afdverify altartományhoz tartozó CNAME rekordot, törölje azt. 

6. Ha első alkalommal használja ezt az egyéni tartományt, kövesse az [egyéni tartomány a bejárati ajtóhoz való hozzárendeléséhez](#associate-the-custom-domain-with-your-front-door) és [az egyéni tartomány ellenőrzéséhez](#verify-the-custom-domain)szükséges lépéseket.

Példaképp a GoDaddy tartományregisztráló eljárása a következő:

1. Jelentkezzen be, és válassza ki a használni kívánt egyéni tartományt.

2. A Tartományok szakaszban válassza az **Összes kezelése**, majd a **DNS** | **Zónák kezelése** lehetőséget.

3. A **Tartománynév** mezőben adja meg az egyéni tartomány nevét, majd válassza a **Keresés** lehetőséget.

4. A **DNS-kezelés** lapon válassza a **Hozzáadás**, lehetőséget, majd válassza a **CNAME** elemet a **Típus** listából.

5. Töltse ki a CNAME bejegyzés mezőit:

    - Típus: Hagyja kijelölve a *CNAME* elemet.

    - Gazdagép: Adja meg a használandó egyéni tartománya altartományát. Például: www vagy profile.

    - A következő helyre mutat: Adja meg a Front Door alapértelmezett gazdagépnevét. Például: contoso.azurefd.net. 

    - TTL: hagyjon *egy órát* .

6. Kattintson a **Mentés** gombra.
 
    A CNAME bejegyzést a rendszer a DNS-rekordok táblázatához adja.

7. Ha rendelkezik adfverify CNAME rekorddal, kattintson a mellette található ceruza ikonra, majd válassza a kuka ikont.

8. A **Törlés** gombra kattintva törölje a CNAME rekordot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben hozzáadott egy egyéni tartományt egy Front Doorhoz. Ha már nem szeretné egyéni tartományhoz rendelni a bejárati ajtót, az alábbi lépésekkel távolíthatja el az egyéni tartományt:
 
1. Nyissa meg a DNS-szolgáltatót, törölje az egyéni tartomány CNAME rekordját, vagy frissítse az egyéni tartomány CNAME rekordját egy nem előtérben lévő végpontra.

    > [!Important]
    > Ha meg szeretné akadályozni, hogy a DNS-bejegyzések és az általuk létrehozott biztonsági kockázatok ne legyenek létrehozva, az Azure-beli bejárati ajtón az erőforrások törlése előtt el kell 2021 indítani a CNAME-rekordokat az első ajtós végpontokra. Az erőforrások közé tartoznak a bejárati ajtóhoz tartozó egyéni tartományok, a bejárati végpontok vagy az Azure-erőforráscsoportok, amelyeken engedélyezve van az egyéni tartomány (ok).

2. A Front Door-tervezőben válassza ki azt az egyéni tartományt, amelyet el szeretne távolítani.

3. Válassza a **Törlés** lehetőséget az egyéni tartomány helyi menüjében. Az egyéni tartomány most már nem lesz hozzárendelve a végponthoz.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* CNAME DNS-rekord létrehozása.
* Az egyéni tartomány társítása a Front Doorral.
* Az egyéni tartomány ellenőrzése.

Ha szeretné megtudni, hogyan engedélyezheti a HTTPS-t az egyéni tartományhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [A HTTPS engedélyezése egyéni tartományhoz](front-door-custom-domain-https.md)
