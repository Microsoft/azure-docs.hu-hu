---
title: Feladatátvétel több végpont között Traffic Manager
titleSuffix: Azure Content Delivery Network
description: Megtudhatja, hogyan konfigurálhat feladatátvételt több Azure Content Delivery Network-végponton az Azure Traffic Manager használatával.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a003becba0bc1e42d8fe0c0c5b199402a430a8e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034758"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Feladatátvétel több végpont között az Azure Traffic Manager

Az Azure Content Delivery Network (CDN) konfigurálásakor kiválaszthatja az igényeinek megfelelő optimális szolgáltatói és díjszabási szintet. 

A globálisan elosztott infrastruktúrával Azure CDN alapértelmezés szerint helyi és földrajzi redundanciát és globális terheléselosztást hoz létre a szolgáltatás rendelkezésre állásának és teljesítményének javítása érdekében. 

Ha egy hely nem érhető el a tartalom kiszolgálásához, a rendszer automatikusan átirányítja a kéréseket egy másik helyre. Az optimális jelenléti pont (POP) az egyes ügyfelekre vonatkozó kérések kiszolgálására szolgál. Az automatikus útválasztás a kérelmek helyének és a kiszolgáló terhelésének a tényezői alapján történik.
 
Ha több CDN-profillal rendelkezik, tovább növelheti a rendelkezésre állást és a teljesítményt az Azure Traffic Manager használatával. 

Az Azure Traffic Manager és az Azure CDN használatával terheléselosztást használhat több CDN-végpont között a következőhöz:
 
* Feladatátvétel
* Földrajzi terheléselosztás 

Egy tipikus feladatátvételi forgatókönyvben az összes ügyfél-kérelem az elsődleges CDN-profilra lesz irányítva. 

Ha a profil nem érhető el, a rendszer a kérelmeket a másodlagos profilra irányítja.  A kérések visszatérnek az elsődleges profiljához, amikor online állapotba kerül.

Az Azure Traffic Manager így biztosíthatja, hogy a webalkalmazás mindig elérhető legyen. 

Ez a cikk útmutatást nyújt, és bemutatja, hogyan konfigurálhatja a feladatátvételt profilokkal a következővel: 

* **Azure CDN standard a Verizontól**
* **Azure CDN standard a Akamai**

**A Microsoft Azure CDN** is támogatott.

## <a name="create-azure-cdn-profiles"></a>Azure CDN profilok létrehozása
Hozzon létre két vagy több Azure CDN-profilt és-végpontot különböző szolgáltatókkal.

1. Hozzon létre két CDN-profilt:
    * **Azure CDN standard a Verizontól**
    * **Azure CDN standard a Akamai** 

    A profilok létrehozásához kövesse az [új CDN-profil létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-profile)című témakör lépéseit.
 
   ![CDN több profil](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Mindegyik új profilban hozzon létre legalább egy végpontot az [új CDN-végpont létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)című rész lépéseit követve.

## <a name="create-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Hozzon létre egy Azure Traffic Manager-profilt, és konfigurálja a terheléselosztást a CDN-végpontok között. 

1. Hozzon létre egy Azure Traffic Manager-profilt a [Traffic Manager profil létrehozása](../traffic-manager/quickstart-create-traffic-manager-profile.md)című témakör lépéseit követve. 

    * **Útválasztási módszer**, válassza a **Priority (prioritás**) lehetőséget.

2. Adja hozzá a CDN-végpontokat a Traffic Manager profilhoz a Traffic Manager- [végpontok hozzáadása](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints) című témakör lépéseit követve.

    * **Írja be** a **külső végpontokat**.
    * **Prioritás**, adjon meg egy számot.

    Hozzon létre például egy **1** és **cdndemo101verizon.azureedge.net** prioritással rendelkező **cdndemo101akamai.azureedge.net** a **2-es** prioritással.

   ![CDN Traffic Manager-végpontok](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Egyéni tartomány konfigurálása a Azure CDN és az Azure Traffic Manager
A CDN-és Traffic Manager-profilok konfigurálása után kövesse az alábbi lépéseket a DNS-hozzárendelés hozzáadásához és az egyéni tartomány regisztrálásához a CDN-végpontokon. Ebben a példában az Egyéni tartománynév a **cdndemo101.dustydogpetcare.online**.

1. Nyissa meg az egyéni tartomány (például GoDaddy) tartományi szolgáltatójának webhelyét, és hozzon létre két DNS CNAME-bejegyzést. 

    a. Az első CNAME-bejegyzéshez rendelje hozzá az egyéni tartományt a cdnverify altartományhoz a CDN-végponthoz. Ez a bejegyzés egy szükséges lépés ahhoz, hogy regisztrálja az egyéni tartományt ahhoz a CDN-végponthoz, amelyet a 2. lépésben Traffic Managerhoz adott hozzá.

      Például: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. A második CNAME-bejegyzéshez rendelje hozzá az egyéni tartományt a cdnverify altartomány nélkül a CDN-végponthoz. Ez a bejegyzés az egyéni tartományt az Traffic Managerra képezi le. 

      Például: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Ha a tartomány jelenleg élő, és nem lehet megszakítani, akkor hajtsa végre ezt a lépést. Győződjön meg arról, hogy a CDN-végpontok és a Traffic Manager-tartományok élőben vannak, mielőtt frissíti az egyéni tartomány DNS-ét Traffic Managerra.
    >
   
    > [!NOTE]
    > Ahhoz, hogy ez a feladatátvétel implemeting, mindkét végpontnak különböző profilokban kell lennie, és a különböző profiloknak különböző CDN-szolgáltatóknak kell lenniük a tartománynevek ütközésének elkerülése érdekében.
    > 

2.  Az Azure CDN-profilban válassza ki az első CDN-végpontot (Akamai). Válassza az **egyéni tartomány hozzáadása** és a bemeneti **cdndemo101.dustydogpetcare.online** lehetőséget. Győződjön meg arról, hogy az egyéni tartomány érvényesítése zöld színnel történik. 

    Azure CDN a **cdnverify** altartomány használatával ellenőrzi a DNS-hozzárendelést a regisztrációs folyamat befejezéséhez. További információ: [CNAME DNS-rekord létrehozása](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ez a lépés lehetővé teszi, hogy a Azure CDN felismerje az egyéni tartományt, hogy válaszoljon a kéréseire.
    
    > [!NOTE]
    > Ha engedélyezni szeretné a TLS-t a Akamai-profilokból **származó Azure CDN** , akkor az egyéni tartományt közvetlenül a végponthoz kell CNAME-re feltüntetni. a TLS engedélyezésének cdnverify még nem támogatott. 
    >

3.  Térjen vissza az egyéni tartomány tartományi szolgáltatójának webhelyére. Frissítse az első létrehozott DNS-leképezést. Rendelje hozzá az egyéni tartományt a második CDN-végponthoz.
                             
    Például: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. A Azure CDN profilban válassza a második CDN-végpontot (Verizon), és ismételje meg a 2. lépést. Válassza az **egyéni tartomány hozzáadása** lehetőséget, és adja meg a **cdndemo101.dustydogpetcare.online**.
 
A lépések elvégzése után a többszörös CDN szolgáltatás a feladatátvételi képességekkel együtt az Azure Traffic Manager-vel van konfigurálva. 

Elérheti az egyéni tartományának tesztelési URL-címeit. 

A funkció teszteléséhez tiltsa le az elsődleges CDN-végpontot, és ellenőrizze, hogy a kérés megfelelően át lett-e helyezve a másodlagos CDN-végpontra. 

## <a name="next-steps"></a>Következő lépések
Más útválasztási módszereket (például földrajzi) is beállíthat a különböző CDN-végpontok közötti terhelés kiegyensúlyozására. 

További információ: [a földrajzi forgalom útválasztási módszerének konfigurálása Traffic Manager használatával](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).
