---
title: Egyéni mintavétel létrehozása a portál használatával
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egyéni mintavételt a Application Gatewayhoz a portál használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95975955"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Egyéni mintavétel létrehozása Application Gatewayhoz a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy egyéni állapot-mintavételt ad hozzá egy meglévő Application Gateway-hez a Azure Portalon keresztül. Az állapotadatok használatával az Azure Application Gateway figyeli a háttérbeli készlet erőforrásainak állapotát.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik Application Gateway-rel, látogasson el a [Application Gateway létrehozására](./quick-create-portal.md) , és hozzon létre egy Application Gateway-t, amely együttműködik a szolgáltatással.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Mintavétel létrehozása Application Gateway v2 SKU-hoz

A mintavétel két lépésből álló folyamaton keresztül történik a portálon. Első lépésként adja meg a mintavételi konfigurációhoz szükséges értékeket. A második lépésben tesztelheti a háttér állapotát ezzel a mintavételi konfigurációval, és mentheti a mintavételt. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Mintavételi tulajdonságok megadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem rendelkezik fiókkal, regisztrálhat egy [hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free)

2. Az Azure Portal Kedvencek panelén kattintson az Összes erőforrás elemre. Kattintson az Application Gateway elemre a minden erőforrás panelen. Ha a kiválasztott előfizetésben már több erőforrás szerepel, a DNS-zóna egyszerű eléréséhez beírhatja a partners.contoso.net nevet a Szűrés név alapján... mezőbe.

3. Válassza az **állapot** -mintavételek lehetőséget, majd válassza a **Hozzáadás** lehetőséget egy új állapot-mintavétel hozzáadásához.

   ![Új mintavétel hozzáadása][4]

4. Az **állapotadatok hozzáadása** lapon adja meg a mintavételhez szükséges adatokat, és ha elkészült, kattintson az **OK gombra**.

   |**Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Név**|customProbe|Ez az érték a portálon elérhető mintavételhez megadott rövid név.|
   |**Protokoll**|HTTP vagy HTTPS | Az állapotfelmérés által használt protokoll. |
   |**Állomás**|azaz contoso.com|Ez az érték annak a virtuális gazdagépnek a neve (amely eltér a virtuálisgép-állomásnévtől), amely az alkalmazáskiszolgáló kiszolgálón fut. A mintavétel a \<protocol\> :// \<host name\> :\<port\>/\<urlPath\>|
   |**Állomásnév kiválasztása a háttérbeli HTTP-beállításokból**|Igen vagy nem|Beállítja a  mintavételben szereplő állomásfejléc nevét annak a http-beállításoknak az állomásnevét, amelyhez ez a mintavétel hozzá van rendelve. Kifejezetten a több-bérlős háttérrendszer, például az Azure app Service esetében szükséges. [További információ](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Válasszon portot a háttérbeli HTTP-beállítások közül**| Igen vagy nem|Beállítja az állapot-mintavétel *portját* arra a http-beállítások portra, amelyhez ez a mintavétel hozzá van rendelve. Ha a nem lehetőséget választja, megadhatja a használni kívánt egyéni célport-portot |
   |**Port**| 1-65535 | Az állapot-mintavételhez használandó egyéni port | 
   |**Elérési út**|/vagy bármely érvényes elérési út|Az egyéni mintavétel teljes URL-címének fennmaradó része. Egy érvényes elérési út a következővel kezdődik: "/". A http:/contoso.com alapértelmezett elérési útja \/ csak a "/" értéket használja |
   |**Időköz (mp)**|30|Milyen gyakran fut a mintavétel az állapot kereséséhez. A 30 másodpercnél kisebb értéket nem ajánlott beállítani.|
   |**Időkorlát (mp)**|30|Az az időtartam, ameddig a mintavétel időtúllépés előtt várakozik. Ha az időkorláton belül nem érkezik érvényes válasz, a mintavétel sikertelenként van megjelölve. Az időtúllépési intervallumnak elég magasnak kell lennie ahhoz, hogy http-hívást lehessen biztosítani, hogy a háttér állapota lap elérhető legyen. Vegye figyelembe, hogy az időtúllépési érték nem lehet nagyobb, mint a mintavételi beállításban használt "Interval" érték, vagy a "kérelem időtúllépése" érték abban a HTTP-beállításban, amely ehhez a mintavételhez lesz társítva.|
   |**Nem kifogástalan állapot küszöbértéke**|3|Az egymást követő sikertelen kísérletek nem megfelelőnek tekintendők. A küszöbérték értéke 1 vagy több lehet.|
   |**Mintavételi feltételek használata**|Igen vagy nem|Alapértelmezés szerint a 200 és 399 közötti állapotkódot biztosító HTTP (S) válaszok kifogástalannak számítanak. Módosíthatja a háttérbeli válasz kódja vagy a háttérbeli válasz törzsének elfogadható tartományát. [További információ](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP-beállítások**|kijelölés a legördülő listából|A mintavétel hozzá lesz rendelve az itt kiválasztott HTTP-beállítás (ok) hoz, ezért a a kiválasztott HTTP-beállításhoz társított háttér-készlet állapotát figyeli. Ugyanazt a portot fogja használni a mintavételi kérelemhez, amelyet a kiválasztott HTTP-beállításban használ. Csak azokat a HTTP-beállításokat lehet választani, amelyek nincsenek társítva más egyéni mintavételhez. <br>Vegye figyelembe, hogy csak azok a HTTP-beállítások érhetők el olyan társításokhoz, amelyek a mintavételi konfigurációban választott protokollal megegyező protokollal rendelkeznek, és azonos állapotban vannak a *kiválasztó állomásnévnek a háttérbeli http-beállítás* kapcsolóval.|
   
   > [!IMPORTANT]
   > A mintavétel csak akkor fogja figyelni a háttér állapotát, ha egy vagy több HTTP-beállítással van társítva. Ezzel a beállítással megfigyelheti azokat a háttérbeli készleteket, amelyek azokhoz a HTTP-beállításokhoz vannak társítva, amelyekhez a mintavétel társítva van. A mintavételi kérelmet \<protocol\> ://:-ként küldi el a rendszer \<hostName\> \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Háttérrendszer állapotának tesztelése a mintavételsel

A mintavételi tulajdonságok megadása után a háttérbeli erőforrások állapotának tesztelésével ellenőrizheti, hogy a mintavételi konfiguráció helyes-e, és hogy a háttérbeli erőforrások a várt módon működnek-e.

1. Válassza a **teszt** lehetőséget, és jegyezze fel a mintavétel eredményét. Az Application Gateway megvizsgálja a mintavételhez használt HTTP-beállítás (ok) hoz tartozó backend-készletek összes háttérbeli erőforrásának állapotát. 

   ![Háttérrendszer állapotának tesztelése][5]

2. Ha sérült háttérbeli erőforrások vannak, akkor a **részletek** oszlopban tekintse át az erőforrás sérült állapotának okát. Ha az erőforrás nem megfelelő állapotra van megjelölve egy helytelen mintavételi konfiguráció miatt, válassza a **visszalépés** a mintavételhez hivatkozást, és szerkessze a mintavétel konfigurációját. Ellenkező esetben, ha az erőforrás a háttérrel kapcsolatos probléma miatt nem megfelelőként van megjelölve, akkor oldja meg a háttér-erőforrással kapcsolatos problémákat, majd próbálja megismételni a hátteret a **visszalépés a mintavételhez hivatkozásra kattintva** , és válassza a **teszt** lehetőséget.

   > [!NOTE]
   > Dönthet úgy is, hogy a mintavételt a nem megfelelő állapotú háttér-erőforrásokkal is menti, de nem ajánlott. Ennek az az oka, hogy a Application Gateway nem továbbítja a kéréseket a backend-kiszolgálóknak a háttér-készletből, mert a mintavétel nem megfelelő állapotba kerül. Ha nincs kifogástalan erőforrás a háttér-készletben, nem fog tudni hozzáférni az alkalmazáshoz, és HTTP 502-es hibát fog kapni.

   ![Mintavétel eredményének megtekintése][6]

3. A mintavétel mentéséhez válassza a **Hozzáadás** lehetőséget. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Mintavétel létrehozása Application Gateway v1 SKU-hoz

A mintavétel két lépésből álló folyamaton keresztül történik a portálon. Első lépésként hozza létre a mintavételt. A második lépésben hozzáadja a mintavételt az Application Gateway háttérbeli http-beállításaihoz.

### <a name="create-the-probe"></a><a name="createprobe"></a>A mintavétel létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem rendelkezik fiókkal, regisztrálhat egy [hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free)

2. Az Azure Portal Kedvencek ablaktábláján válassza az **Összes erőforrás** lehetőséget. Válassza ki az Application Gatewayt a **minden erőforrás** lapon. Ha a kiválasztott előfizetésben már több erőforrás szerepel, a DNS-zóna egyszerű eléréséhez beírhatja a partners.contoso.net nevet a Szűrés név alapján... mezőbe.

3. Válassza a mintavételek **lehetőséget, majd a** **Hozzáadás** lehetőséget a mintavétel hozzáadásához.

   ![Mintavételi panel hozzáadása a kitöltött információkkal][1]

4. Az **állapotfelmérés hozzáadása** panelen töltse ki a mintavételhez szükséges adatokat, és ha elkészült, kattintson az **OK gombra**.

   |**Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Név**|customProbe|Ez az érték a portálon elérhető mintavételhez megadott rövid név.|
   |**Protokoll**|HTTP vagy HTTPS | Az állapotfelmérés által használt protokoll. |
   |**Állomás**|azaz contoso.com|Ez az érték annak a virtuális gazdagépnek a neve (amely eltér a virtuálisgép-állomásnévtől), amely az alkalmazáskiszolgáló kiszolgálón fut. A rendszer elküldi a mintavételt a (z)://(állomásnév):(port httpsetting)/urlPath.  Ez akkor alkalmazható, ha a többhelyes konfiguráció a Application Gatewayon van konfigurálva. Ha a Application Gateway egyetlen helyhez van konfigurálva, írja be a "127.0.0.1" értéket.|
   |**Állomásnév kiválasztása a háttérbeli HTTP-beállításokból**|Igen vagy nem|A mintavételben *szereplő állomásfejléc* értékének beállítása annak a http-beállításnak a háttér-erőforrásának állomásneve, amelyhez ez a mintavétel hozzá van rendelve. Kifejezetten a több-bérlős háttérrendszer, például az Azure app Service esetében szükséges. [További információ](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Elérési út**|/vagy bármely érvényes elérési út|Az egyéni mintavétel teljes URL-címének fennmaradó része. Egy érvényes elérési út a következővel kezdődik: "/". A http:/contoso.com alapértelmezett elérési útja \/ csak a "/" értéket használja |
   |**Időköz (mp)**|30|Milyen gyakran fut a mintavétel az állapot kereséséhez. A 30 másodpercnél kisebb értéket nem ajánlott beállítani.|
   |**Időkorlát (mp)**|30|Az az időtartam, ameddig a mintavétel időtúllépés előtt várakozik. Ha az időkorláton belül nem érkezik érvényes válasz, a mintavétel sikertelenként van megjelölve. Az időtúllépési intervallumnak elég magasnak kell lennie ahhoz, hogy http-hívást lehessen biztosítani, hogy a háttér állapota lap elérhető legyen. Vegye figyelembe, hogy az időtúllépési érték nem lehet nagyobb, mint a mintavételi beállításban használt "Interval" érték, vagy a "kérelem időtúllépése" érték abban a HTTP-beállításban, amely ehhez a mintavételhez lesz társítva.|
   |**Nem kifogástalan állapot küszöbértéke**|3|Az egymást követő sikertelen kísérletek nem megfelelőnek tekintendők. A küszöbérték értéke 1 vagy több lehet.|
   |**Mintavételi feltételek használata**|Igen vagy nem|Alapértelmezés szerint a 200 és 399 közötti állapotkódot biztosító HTTP (S) válaszok kifogástalannak számítanak. Módosíthatja a háttérbeli válasz kódja vagy a háttérbeli válasz törzsének elfogadható tartományát. [További információ](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > Az állomásnév nem egyezik meg a kiszolgáló nevével. Ez az érték az alkalmazáskiszolgáló által futtatott virtuális gazdagép neve. A mintavétel a \<protocol\> :// \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Mintavétel hozzáadása az átjáróhoz

Most, hogy létrejött a mintavétel, itt az ideje, hogy hozzáadja az átjáróhoz. A mintavételi beállítások az Application Gateway háttérbeli http-beállításainál állíthatók be.

1. Kattintson a **http-beállítások** elemre az Application gatewayben a konfiguráció panel megjelenítéséhez, majd kattintson az ablakban az aktuális háttérbeli http-beállítások elemre.

   ![https-beállítások ablak][2]

2. A **appGatewayBackEndHttpSettings** -beállítások lapon jelölje be az **Egyéni mintavétel használata** jelölőnégyzetet, és válassza ki a mintavétel [létrehozása](#createprobe) szakaszban létrehozott mintavételt az **Egyéni** mintavétel legördülő menüben.
   Ha elkészült, kattintson a **Mentés** gombra, és alkalmazza a beállításokat.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a háttérbeli erőforrások állapotát a [háttér állapot nézet](./application-gateway-diagnostics.md#back-end-health)alapján, a mintavétel alapján.

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
