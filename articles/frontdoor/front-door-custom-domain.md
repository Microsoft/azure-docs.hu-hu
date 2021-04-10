---
title: Oktatóanyag – egyéni tartomány hozzáadása az Azure-beli bejárati ajtó konfigurációjához
description: Ebből az oktatóanyagból megtudhatja, hogyan regisztrálható egyéni tartomány az Azure Front Door szolgáltatásban.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: duau
ms.openlocfilehash: 6abed66a5fbd9987e5a8a677dde7b4a77589e907
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065044"
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

* Ha nem rendelkezik egyéni tartománnyal, először vásároljon egyet egy tartományszolgáltatótól. Példákért lásd az [egyéni tartománynév vásárlásáról](../app-service/manage-custom-dns-buy-domain.md) szóló részt.

* Ha az Azure-t használja a [DNS-tartományai](../dns/dns-overview.md) üzemeltetésére, delegálnia kell a tartományszolgáltató tartománynévrendszerét (DNS-ét) egy Azure DNS-re. További információ: [tartomány delegálása Azure DNSra](../dns/dns-delegate-domain-azure-dns.md). Egyéb esetben, ha tartományszolgáltatót használ a DNS-tartománya kezeléséhez, lépjen tovább a [CNAME DNS-rekord létrehozása](#create-a-cname-dns-record) szakaszra.


## <a name="create-a-cname-dns-record"></a>CNAME DNS-rekord létrehozása

Ahhoz, hogy egyéni tartományt lehessen használni a bejárati ajtóhoz, először létre kell hoznia egy kanonikus név (CNAME) rekordot a tartományi szolgáltatóval, hogy az a bejárati ajtó alapértelmezett előtér-gazdagépére mutasson (például contoso.azurefd.net). A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre. Az Azure bejárati ajtónál a forrás tartomány neve az Egyéni tartománynév, a cél tartomány neve pedig az alapértelmezett állomásnév. Miután a bejárati ajtó ellenőrizte a létrehozott CNAME-rekordot, a forrás egyéni tartományhoz (például a www contoso.com) irányuló forgalmat a \. rendszer átirányítja a megadott célhelyre az alapértelmezett előtér-gazdagéphez (például contoso-frontend.azurefd.net). 

Egy egyéni tartományt és altartományát egyszerre csak egyetlen bejárati ajtóhoz lehet társítani. Ugyanakkor több CNAME rekord használatával különböző altartományokat is használhat ugyanazon egyéni tartományból különböző bejárati ajtókhoz. A különböző altartományokkal rendelkező egyéni tartományokat is leképezheti ugyanahhoz a bejárati ajtóhoz.


## <a name="map-the-temporary-afdverify-subdomain"></a>Az ideiglenes afdverify altartomány leképezése

Amikor meglévő, éles környezetben futó tartományt képez le, speciális szempontokat kell figyelembe vennie. Amikor regisztrálja az egyéni tartományát az Azure Portalon, rövid kimaradás fordulhat elő a tartományon. A webes forgalom megszakításának elkerülése érdekében először az Azure afdverify altartománnyal társítsa az egyéni tartományt az előtérben lévő alapértelmezett előtér-gazdagéphez, és hozzon létre egy ideiglenes CNAME-leképezést. Ezzel a módszerrel a felhasználók megszakítás nélkül férhetnek hozzá a tartományhoz a DNS-hozzárendelés közben.

Egyéb esetben, ha először használja az egyéni tartományát, és nem fut rajta éles forgalom, a tartományt leképezheti közvetlenül a Front Doorra. Lépjen tovább [az állandó egyéni tartomány leképezését](#map-the-permanent-custom-domain) bemutató cikkre.

CNAME rekord létrehozása az afdverify altartománnyal:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldal helyét a szolgáltató dokumentációjának segítségével, vagy a webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** területeinek áttekintésével. 

3. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás                    | Típus  | Cél                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Forrás: adja meg az egyéni tartománynevet, beleértve a afdverify altartományt is, a következő formátumban: afdverify. _&lt; Egyéni tartománynév &gt;_. Például: afdverify.www.contoso.com.

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
    
2. A **Front Door-tervező** lapon kattintson a „+” jelre egyéni tartomány hozzáadásához.
    
3. Adjon meg az **Egyéni tartomány** értékét. 

4. Az **Előtérbeli gazdagép** mezőben előre ki van töltve a CNAME rekordhoz céltartományként használt előtérbeli gazdagép neve, amely a Front Doorból van származtatva: *&lt;alapértelmezett gazdagépnév&gt;*.azurefd.net. A név nem módosítható.

5. Az **Egyéni gazdagépnév** mezőben adja meg az egyéni tartomány nevét az altartomány nevével együtt, amelyet a CNAME rekord forrástartományaként fog használni. Például: www \. contoso.com vagy CDN.contoso.com. Ne használja az afdverify altartománynevet.

6. Válassza a **Hozzáadás** lehetőséget.

   Az Azure ellenőrzi, hogy a megadott egyéni tartománynév esetében létezik-e a CNAME rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz.

>[!WARNING]
> **Kötelező** gondoskodnia róla, hogy a Front Doorban az összes előtérbeli gazdagép (beleértve az egyéni tartományokat) rendelkezzen útválasztási szabállyal, hozzá társított alapértelmezett elérési úttal („/\*”). Vagyis minden egyes előtérbeli gazdagépnek rendelkeznie kell legalább egy olyan útválasztási szabállyal, amely az alapértelmezett útvonalat (\*) használja. Ha ez a feltétel nem teljesül, akkor a végfelhasználói forgalom útvonalválasztása nem lesz megfelelő.

## <a name="verify-the-custom-domain"></a>Az egyéni tartomány ellenőrzése

Az egyéni tartomány regisztrálása után ellenőrizze, hogy az egyéni tartomány az alapértelmezett előtérbeli Front Door-gazdagépre hivatkozik-e.
 
A böngészőjében navigáljon a fájl címére az egyéni tartomány használatával. Ha például az egyéni tartománya a robotics.contoso.com, a gyorsítótárazott fájl URL-címe a következő URL-címre fog hasonlítani: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Győződjön meg arról, hogy az eredmény ugyanaz, mint amikor közvetlenül *&lt; &gt; a bejárati* ajtóhoz fér hozzá. azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Az állandó egyéni tartomány leképezése

Ha ellenőrizte, hogy az adfverify altartomány sikeresen le lett képezve a Front Doorra (vagy ha új egyéni tartományt használ, amely nem fut éles környezetben), akkor leképezheti az egyéni tartományt közvetlenül az alapértelmezett előtérbeli Front Door-gazdagépre.

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

6. Ha éles környezetben most először használja az egyéni tartományt, kövesse [Az egyéni tartomány hozzárendelése a Front Doorhoz](#associate-the-custom-domain-with-your-front-door) és [Az egyéni tartomány ellenőrzése](#verify-the-custom-domain) szakaszok lépéseit.

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

Az előző lépésekben hozzáadott egy egyéni tartományt egy Front Doorhoz. Ha már nem szeretné egyéni tartománnyal társítani a Front Doort, az alábbi lépésekkel eltávolíthatja az egyéni tartományt:
 
1. A Front Door-tervezőben válassza ki azt az egyéni tartományt, amelyet el szeretne távolítani.

2. Kattintson a Törlés elemre a helyi menüben az egyéni tartománynál.  

   Az egyéni tartomány társítása a végponttal ekkor megszűnik.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* CNAME DNS-rekord létrehozása.
* Az egyéni tartomány társítása a Front Doorral.
* Az egyéni tartomány ellenőrzése.

Ha szeretné megtudni, hogyan engedélyezheti a HTTPS-t az egyéni tartományhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [A HTTPS engedélyezése egyéni tartományhoz](front-door-custom-domain-https.md)