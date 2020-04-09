---
title: Útmutató – Egyéni tartomány hozzáadása az Azure CDN-végponthoz | Microsoft Docs
description: Ebben az oktatóanyagban egy Azure CDN-végpont tartalmát fogja leképezni egy egyéni tartományra.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 04c6ddf03f1c0433ad4e2d0808f118f42670ee08
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891271"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Útmutató: Egyéni tartomány hozzáadása az Azure CDN-végponthoz
Ez az útmutató megmutatja, hogyan adható hozzá egyéni tartomány egy Azure Content Delivery Network- (CDN-) végponthoz. Amikor CDN-végpontot használ a tartalom továbbítására, és azt szeretné, hogy a saját tartományneve jelenjen meg a CDN URL-címében, egyéni tartományt kell létrehoznia. A látható tartománynév hasznos lehet az ügyfelei számára, és a vállalati arculat szempontjából is. 

Miután létrehoz egy CDN-végpontot a profiljában, a végpont neve – amely az azureedge.net altartománya – része lesz annak az URL-címnek, amelyre alapértelmezetten a rendszer a CDN-tartalmat irányítja (például https:\//contoso.azureedge.net/photo.png). A kényelmes használat érdekében az Azure CDN lehetőséget ad arra, hogy CDN-végpontot rendeljen egy egyéni tartományhoz. Ezzel a lehetőséggel a tartalom továbbításakor az egyéni tartomány neve lesz az URL-ben a végpont neve helyett (például https:\//www.contoso.com/photo.png). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - CNAME DNS-rekord létrehozása.
> - Az egyéni tartomány hozzárendelése a CDN-végponthoz.
> - Az egyéni tartomány ellenőrzése.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy CDN-profilt, és legalább egy CDN-végpontot. További információk: [Gyors útmutató: Azure CDN-profil és -végpont létrehozása](cdn-create-new-endpoint.md)

Ha nem rendelkezik egyéni tartománnyal, először vásároljon egyet egy tartományszolgáltatótól. Példákért lásd az [egyéni tartománynév vásárlásáról](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain) szóló részt.

Ha az Azure-t használja a [DNS-tartományai](https://docs.microsoft.com/azure/dns/dns-overview) üzemeltetésére, delegálnia kell a tartományszolgáltató tartománynévrendszerét (DNS-ét) egy Azure DNS-re. További információt a [Tartomány delegálása az Azure DNS-re](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)című témakörben talál. Egyéb esetben, ha tartományszolgáltatót használ a DNS-tartománya kezeléséhez, lépjen tovább a [CNAME DNS-rekord létrehozása](#create-a-cname-dns-record) szakaszra.


## <a name="create-a-cname-dns-record"></a>CNAME DNS-rekord létrehozása

Mielőtt használhatna egy egyéni tartományt egy Azure CDN-végponttal, a tartományszolgáltatójával létre kell hoznia egy kanonikusnév- (CNAME) rekordot, amely a CDN-végpontra mutat. A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre. Az Azure CDN esetében a forrástartománynév az Ön egyéni tartományneve, a céltartománynév pedig a CDN-végpontja gazdaneve. Miután az Azure CDN ellenőrizte a létrehozott CNAME rekordot, a forrás\.egyéni tartománynak (például www contoso.com) címzett forgalom a megadott cél CDN-végpont állomásnevéhez (például contoso.azureedge.net) kerül. 

Egy egyéni tartománynév és annak altartománya egyszerre csak egy végponttal társítható. Azonban lehetséges egy egyéni tartomány több altartományának használata különböző Azure-szolgáltatásvégpontokhoz. Ezt több CNAME rekord használatával teheti meg. Emellett leképezhet egy több altartománnyal rendelkező egyéni tartományt is ugyanarra a CDN-végpontra.

> [!NOTE]
> Bármilyen aliasrekord-típus használható egyéni tartományokhoz, ha az Azure DNS-t használja tartományszolgáltatóként. Ez a forgatókönyv a CNAME bejegyzéstípust használja. Ha A vagy AAAA bejegyzéstípusokat használ, kövesse az alábbi lépéseket, és cserélje le a CNAME nevet a kívánt bejegyzéstípusra. Ha aliasrekordot használ egy gyökértartomány egyéni tartományként való hozzáadásához, és engedélyezni szeretné a TLS-t, akkor a [jelen cikkben](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint)leírtak szerint manuális érvényesítést kell használnia. További információ: [Point zone apex to Azure CDN endpoints](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints).

## <a name="map-the-temporary-cdnverify-subdomain"></a>Az ideiglenes cdnverify altartomány leképezése

Amikor meglévő, éles környezetben futó tartományt képez le, speciális szempontokat kell figyelembe vennie. Amikor regisztrálja az egyéni tartományát az Azure Portalon, rövid kimaradás fordulhat elő a tartományon. A webes forgalom megszakításának elkerüléséhez először képezze le az egyéni tartományt a CDN-végpont gazdanevére az Azure cdnverify altartománnyal. Ezzel létrehoz egy ideiglenes CNAME-leképezést. Ezzel a módszerrel a felhasználók megszakítás nélkül férhetnek hozzá a tartományhoz a DNS-hozzárendelés közben. 

Egyéb esetben, ha először használja az egyéni tartományát, és nem fut rajta éles forgalom, a tartományt leképezheti közvetlenül a CDN-végpontra is. Lépjen tovább [az állandó egyéni tartomány leképezését](#map-the-permanent-custom-domain) bemutató cikkre.

CNAME rekord létrehozása a cdnverify altartománnyal:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldal helyét a szolgáltató dokumentációjának segítségével, vagy a webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** területeinek áttekintésével. 

3. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás                    | Típus  | Cél                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Forrás: Adja meg egyéni tartománynevét, beleértve a cdnverify aldomaint a következő formátumban: cdnverify. &lt;egyéni tartománynév&gt;. Például: cdnverify.www.contoso.com.

    - Típus: Írja be a *CNAME* szöveget.

    - Rendeltetési hely: Adja meg a CDN végpont állomásnevét, beleértve a cdnverify altartományt a következő formátumban: cdnverify. _végpont neve&gt;.azureedge.net. &lt;_ Például: cdnverify.contoso.azureedge.net.

4. Mentse a módosításokat.

Példaképp a GoDaddy tartományregisztráló eljárása a következő:

1. Jelentkezzen be, és válassza ki a használni kívánt egyéni tartományt.

2. A Tartományok szakaszban válassza az **Összes kezelése**, majd a **DNS** | **Zónák kezelése** lehetőséget.

3. A **Tartománynév** mezőben adja meg az egyéni tartomány nevét, majd válassza a **Keresés** lehetőséget.

4. A **DNS-kezelés** lapon válassza a **Hozzáadás**, lehetőséget, majd válassza a **CNAME** elemet a **Típus** listából.

5. Töltse ki a következő mezőket a CNAME bejegyzéshez:

    ![CNAME bejegyzés](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Típus: Hagyja kijelölve a *CNAME* elemet.

    - Gazdagép: Adja meg az egyéni tartománya altartományát a cdnverify altartomány nevével együtt. Például: cdnverify.www.

    - A következő helyre mutat: Adja meg a CDN-végpont gazdanevét a cdnverify altartomány nevével együtt. Például: cdnverify.contoso.azureedge.net. 

    - Élettartam: Hagyja kiválasztva az *1 óra* lehetőséget.

6. Kattintson a **Mentés** gombra.
 
    A CNAME bejegyzést a rendszer a DNS-rekordok táblázatához adja.

    ![DNS-rekordok táblázata](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Az egyéni tartomány hozzárendelése a CDN-végponthoz

Miután regisztrálta az egyéni tartományát, hozzáadhatja azt a CDN-végpontjához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és navigáljon arra a CDN-profilra, amely tartalmazza azt a végpontot, amelyet az egyéni tartományhoz kíván leképezni.
    
2. A **CDN-profil** oldalon válassza ki a CDN-végpontot, amelyet társítani kíván az egyéni tartománnyal.

   Megnyílik a **Végpont** lap.
    
3. Válassza az **Egyéni tartomány** lehetőséget. 

   ![CDN – egyéni tartomány gomb](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   Megnyílik az **Egyéni tartomány hozzáadása** oldal.

4. A **Végpont gazdaneve** mezőben a CNAME rekord céltartományaként használandó végpont gazdaneve előre ki van töltve, és a CDN-végponti URL-címből van származtatva: *&lt;végpont gazdagépneve&gt;*.azureedge.net. A név nem módosítható.

5. Az **Egyéni gazdagépnév** mezőben adja meg az egyéni tartomány nevét az altartomány nevével együtt, amelyet a CNAME rekord forrástartományaként fog használni. Például a\.www contoso.com vagy a cdn.contoso.com. A cdnverify altartománynevet ne használja.

   ![CDN – egyéni tartomány párbeszédpanel](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Válassza a **Hozzáadás** lehetőséget.

   Az Azure ellenőrzi, hogy a megadott egyéni tartománynév esetében létezik-e a CNAME rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz. 

   Időbe telhet, amíg az új egyéni tartománybeállítások propagálása az összes CDN-határcsomópontra megtörténik: 
    - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
    - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
    - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be.   


## <a name="verify-the-custom-domain"></a>Az egyéni tartomány ellenőrzése

Az egyéni tartomány regisztrálása után ellenőrizze, hogy az egyéni tartomány a CDN-végpontjára hivatkozik-e.
 
1. Győződjön meg arról, hogy rendelkezik olyan nyilvános tartalommal, amely a végponton van gyorsítótárazva. Ha például a CDN-végpont egy tárfiókkal van társítva, az Azure CDN egy nyilvános tárolóban fogja gyorsítótárazni a tartalmat. Az egyéni tartomány teszteléséhez győződjön meg arról, hogy a tároló nyilvános hozzáférésre van beállítva, és tartalmaz legalább egy fájlt.

2. A böngészőjében navigáljon a fájl címére az egyéni tartomány használatával. Ha például az egyéni `www.contoso.com`tartomány a tartomány, a gyorsítótárazott fájl `http://www.contoso.com/my-public-container/my-file.jpg`URL-címének a következő URL-címhez kell hasonlítania: . Ellenőrizze, hogy az eredmény megegyezik-e a CDN-végpont közvetlen elérésékor a .azureedge.net * &lt;&gt;végpont-állomásnévnél.*


## <a name="map-the-permanent-custom-domain"></a>Az állandó egyéni tartomány leképezése

Ha ellenőrizte, hogy a cdnverify altartomány sikeresen le lett képezve a végpontra (vagy ha új egyéni tartományt használ, amely nem fut éles környezetben), leképezheti az egyéni tartományt közvetlenül a CDN-végpont gazdagépnevéhez.

CNAME rekord létrehozása az egyéni tartományhoz:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. A DNS-rekordok kezelésére szolgáló lap megkeresése a szolgáltató dokumentációjában, vagy a webhely **Domain Name**, **DNS**vagy Name Server Management címkével ellátott területeinek **keresése.** 

3. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás          | Típus  | Cél           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - Forrás: Adja meg az egyéni tartománynevet (például www\.contoso.com).

   - Típus: Írja be a *CNAME* szöveget.

   - Cél: Adja meg a CDN-végpont gazdanevét. A végpont_&lt;neve&gt;_.azureedge.net. Például: contoso.azureedge.net.

4. Mentse a módosításokat.

5. Ha korábban létrehozott egy ideiglenes cdnverify altartományhoz tartozó CNAME rekordot, törölje azt. 

6. Ha éles környezetben most először használja az egyéni tartományt, kövesse [Az egyéni tartomány hozzárendelése a CDN-végponthoz](#associate-the-custom-domain-with-your-cdn-endpoint) és [Az egyéni tartomány ellenőrzése](#verify-the-custom-domain) szakaszok lépéseit.

Példaképp a GoDaddy tartományregisztráló eljárása a következő:

1. Jelentkezzen be, és válassza ki a használni kívánt egyéni tartományt.

2. A Tartományok szakaszban válassza az **Összes kezelése**, majd a **DNS** | **Zónák kezelése** lehetőséget.

3. A **Tartománynév** mezőben adja meg az egyéni tartomány nevét, majd válassza a **Keresés** lehetőséget.

4. A **DNS-kezelés** lapon válassza a **Hozzáadás**, lehetőséget, majd válassza a **CNAME** elemet a **Típus** listából.

5. Töltse ki a CNAME bejegyzés mezőit:

    ![CNAME bejegyzés](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Típus: Hagyja kijelölve a *CNAME* elemet.

    - Gazdagép: Adja meg a használandó egyéni tartománya altartományát. Például: www vagy cdn.

    - A következő helyre mutat: Adja meg a CDN-végpont gazdanevét. Például: contoso.azureedge.net. 

    - Élettartam: Hagyja kiválasztva az *1 óra* lehetőséget.

6. Kattintson a **Mentés** gombra.
 
    A CNAME bejegyzést a rendszer a DNS-rekordok táblázatához adja.

    ![DNS-rekordok táblázata](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Ha rendelkezik cdnverify CNAME rekorddal, kattintson a mellette található ceruza ikonra, majd válassza a kuka ikont.

8. A **Törlés** gombra kattintva törölje a CNAME rekordot.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben hozzáadott egy egyéni tartományt egy CDN-végponthoz. Ha már nem szeretné egyéni tartománnyal társítani a végpontot, az alábbi lépésekkel eltávolíthatja az egyéni tartományt:
 
1. A CDN-profilban válassza ki azt a végpontot, amely tartalmazza az eltávolítani kívánt egyéni tartományt.

2. A **Végpont** oldalon, az Egyéni tartományok területen kattintson a jobb gombbal az eltávolítani kívánt egyéni tartományra, és válassza a **Törlés** lehetőséget a helyi menüből.  

   Az egyéni tartomány társítása a végponttal ekkor megszűnik.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - CNAME DNS-rekord létrehozása.
> - Az egyéni tartomány hozzárendelése a CDN-végponthoz.
> - Az egyéni tartomány ellenőrzése.

Lépjen tovább a következő oktatóanyagra, amely azt mutatja be, hogyan konfigurálhatja a HTTPS-t egy Azure CDN egyéni tartományon.

> [!div class="nextstepaction"]
> [Oktatóanyag: HTTPS konfigurálása Azure CDN egyéni tartományon](cdn-custom-ssl.md)


