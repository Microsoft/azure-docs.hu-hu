---
title: Terheléselosztási szolgáltatások használata az Azure-ban | Microsoft Docs
description: 'Ez az oktatóanyag bemutatja, hogyan hozhat létre forgatókönyvet az Azure terheléselosztási portfóliójának használatával: Traffic Manager, Application Gateway és Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: eaf50f3bdacaf5680bc5ecb1379faff20133b5ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184373"
---
# <a name="using-load-balancing-services-in-azure"></a>Terheléselosztási szolgáltatások használata az Azure-ban

## <a name="introduction"></a>Bevezetés

Microsoft Azure több szolgáltatást biztosít a hálózati forgalom elosztásának és terheléselosztásának kezeléséhez. Ezeket a szolgáltatásokat egyenként is használhatja, vagy igény szerint kombinálhatja a módszereit az optimális megoldás kiépítéséhez.

Ebben az oktatóanyagban először definiálunk egy ügyfél-használati esetet, és megtudhatjuk, hogyan lehet az alábbi Azure-beli terheléselosztási portfólióval robusztus és hatékonyabban elvégezni a teljesítményt: Traffic Manager, Application Gateway és Load Balancer. Ezután részletes útmutatót biztosítunk a földrajzilag redundáns központi telepítés létrehozásához, elosztja a forgalmat a virtuális gépekre, és segít a különböző típusú kérések kezelésében.

Koncepcionális szinten a szolgáltatások mindegyike külön szerepet játszik a terheléselosztási hierarchiában.

* A **Traffic Manager** globális DNS-terheléselosztást biztosít. A bejövő DNS-kérelmeket tekinti át, és egy kifogástalan állapottal válaszol, az ügyfél által kiválasztott útválasztási házirendnek megfelelően. Az útválasztási módszerek beállításai a következők:
  * A teljesítmény-útválasztás, hogy a kérelmezőt a legközelebbi végpontra küldje a késés szempontjából.
  * Prioritás-útválasztás a végpontra irányuló összes forgalom közvetlen biztonsági mentéssel való irányításához.
  * Súlyozott ciklikus időszeleteléses útválasztás, amely az egyes végpontokhoz rendelt súlyozás alapján osztja el a forgalmat.
  * Földrajzi alapú útválasztás, amely a felhasználó földrajzi helye alapján osztja el az alkalmazás-végpontokra irányuló forgalmat.
  * Alhálózat-alapú útválasztás az alkalmazás-végpontokra irányuló forgalom elosztásához a felhasználó alhálózata (IP-címtartomány) alapján.
  * Többértékű útválasztás, amely lehetővé teszi több alkalmazás-végpont IP-címeinek küldését egyetlen DNS-válaszban.

  Az ügyfél közvetlenül csatlakozik a Traffic Manager által visszaadott végponthoz. Az Azure Traffic Manager észleli, ha egy végpont állapota sérült, majd átirányítja az ügyfeleket egy másik kifogástalan állapotú példányra. A szolgáltatással kapcsolatos további információkért tekintse meg az [Azure Traffic Manager dokumentációját](traffic-manager-overview.md) .
* A **Application Gateway** az Application Delivery Controller (ADC) szolgáltatást nyújtja, amely különböző, 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazáshoz. Lehetővé teszi az ügyfelek számára a webfarmok hatékonyságának optimalizálását azáltal, hogy kiszervezik a CPU-igényes TLS-lezárást az Application Gateway felé. Az egyéb 7. rétegbeli útválasztási képességek közé tartozik a bejövő forgalom ciklikus lekéréses eloszlása, a cookie-alapú munkamenet-affinitás, az URL-alapú útválasztás, valamint az egyetlen Application Gateway mögött több webhely üzemeltetése. Application Gateway konfigurálható internetre irányuló átjáróként, csak belső átjáróként, vagy mindkettő kombinációja. A Application Gateway teljes mértékben az Azure által felügyelt, méretezhető és magasan elérhető. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.
* **Load Balancer** az Azure Sdn stack szerves része, amely nagy teljesítményű, kis késleltetésű 4. rétegbeli terheléselosztási szolgáltatásokat biztosít minden UDP-és TCP-protokollhoz. Felügyeli a bejövő és kimenő kapcsolatokat. Segítségével nyilvános és belső elosztott terhelésű végpontok konfigurálhatók, valamint szabályok definiálhatók, amelyek a bejövő kapcsolatokat a háttérbeli készletben található célokra irányítják TCP- és HTTP-állapotellenőrzési lehetőségek használatával, a szolgáltatás rendelkezésre állásának felügyeletéhez.

## <a name="scenario"></a>Eset

Ebben a példában egy egyszerű webhelyet használunk, amely két típusú tartalmat kínál: a képeket és a dinamikusan megjelenített weblapokat. A webhelynek földrajzilag redundánsnak kell lennie, és a felhasználókat a legközelebbi (legalacsonyabb késésű) helyről kell kiszolgálni. Az alkalmazás fejlesztői úgy döntöttek, hogy a/images/* mintának megfelelő URL-címek a webfarm többi részétől eltérő, dedikált virtuális gépekből származnak.

Emellett a dinamikus tartalmat kiszolgáló alapértelmezett virtuálisgép-készletnek a magas rendelkezésre állású fürtön üzemeltetett háttér-adatbázissal kell kommunikálnia. A teljes telepítés Azure Resource Manageron keresztül van beállítva.

A Traffic Manager, Application Gateway és Load Balancer használata lehetővé teszi a webhely számára a következő tervezési célok elérését:

* **Többszörös földrajzi redundancia**: Ha az egyik régió leáll, Traffic Manager a forgalmat zökkenőmentesen továbbítja a legközelebbi régióhoz az alkalmazás tulajdonosának beavatkozása nélkül.
* **Csökkentett késés**: mivel Traffic Manager automatikusan irányítja az ügyfelet a legközelebbi régióba, az ügyfél kevesebb késést tapasztal a weblap tartalmának kérésekor.
* **Független méretezhetőség**: mivel a webalkalmazás számítási feladatait a tartalom típusa választja el, az alkalmazás tulajdonosai egymástól független módon méretezhetik a kérelmek munkaterhelését. Application Gateway biztosítja, hogy a forgalom a megfelelő készletekre legyen irányítva a megadott szabályok és az alkalmazás állapota alapján.
* **Belső terheléselosztás**: mivel Load Balancer a magas rendelkezésre állású fürt előtt van, csak az adatbázis aktív és kifogástalan végpontja lesz elérhető az alkalmazás számára. Emellett az adatbázis-rendszergazdák a munkaterhelést úgy is optimalizálhatja, hogy az aktív és passzív replikákat az előtér-alkalmazástól függetlenül, a fürtön keresztül terjeszti. Load Balancer biztosítja a kapcsolatot a magas rendelkezésre állású fürttel, és gondoskodik arról, hogy csak az egészséges adatbázisok fogadják a kapcsolódási kérelmeket.

Az alábbi ábrán a forgatókönyv architektúrája látható:

![Terheléselosztási architektúra ábrája](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Ez a példa az Azure által kínált terheléselosztási szolgáltatások számos lehetséges konfigurációjának egyike. A Traffic Manager, a Application Gateway és a Load Balancer összekeverhető, és a legjobban megfelel a terheléselosztási igényeknek. Ha például a TLS-kiszervezés vagy a 7. rétegbeli feldolgozás nem szükséges, Load Balancer használható Application Gateway helyett.

## <a name="setting-up-the-load-balancing-stack"></a>A terheléselosztási verem beállítása

### <a name="step-1-create-a-traffic-manager-profile"></a>1. lépés: Traffic Manager profil létrehozása

1. A Azure Portal kattintson az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Traffic Manager profil**  >  **létrehozása** elemre.
2. Adja meg a következő alapvető információkat:

   * **Név**: adja meg a Traffic Manager-profilt a DNS-előtag neveként.
   * **Útválasztási módszer**: válassza ki a forgalom-útválasztási módszer házirendjét. További információ a módszerekről: tudnivalók a [Traffic Manager forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md).
   * **Előfizetés**: válassza ki a profilt tartalmazó előfizetést.
   * **Erőforráscsoport**: válassza ki a profilt tartalmazó erőforráscsoportot. Ez lehet egy új vagy egy meglévő erőforráscsoport.
   * **Erőforráscsoport helye**: Traffic Manager a szolgáltatás globális, és nincs helyhez kötve. Azonban meg kell adnia egy régiót ahhoz a csoporthoz, ahol az Traffic Manager-profilhoz tartozó metaadatok találhatók. Ez a hely nem befolyásolja a profil futásidejű rendelkezésre állását.

3. A Traffic Manager profil létrehozásához kattintson a **Létrehozás** gombra.

   !["Traffic Manager létrehozása" panel](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>2. lépés: az Application Gateway létrehozása

1. A Azure Portal a bal oldali ablaktáblán kattintson az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Application Gateway** elemre.
2. Adja meg az Application Gateway következő alapvető adatait:

   * **Name (név**): az Application Gateway neve.
   * **SKU-méret**: az Application Gateway mérete, amely kis, közepes vagy nagy méretűként érhető el.
   * **Példányszám: a** példányok száma, a 2 és 10 közötti érték.
   * **Erőforráscsoport**: az Application Gatewayt birtokló erőforráscsoport. Ez lehet egy meglévő erőforráscsoport vagy egy új csoport.
   * **Hely**: az Application Gateway régiója, amely az erőforráscsoporthoz megegyező helyen található. A hely azért fontos, mert a virtuális hálózatnak és a nyilvános IP-nek ugyanazon a helyen kell lennie, mint az átjárónak.
3. Kattintson az **OK** gombra.
4. Adja meg a virtuális hálózatot, az alhálózatot, az előtér-IP-címet és a figyelő konfigurációját az Application Gateway számára. Ebben az esetben az előtér-IP-cím **nyilvános**, amely lehetővé teszi, hogy később a Traffic Manager-profilhoz végpontként adja hozzá.
5. Konfigurálja a figyelőt a következő lehetőségek egyikével:
    * Ha HTTP-t használ, nem kell konfigurálnia a konfigurációt. Kattintson az **OK** gombra.
    * Ha HTTPS-t használ, további konfigurálásra van szükség. Lásd: [Application Gateway létrehozása](../application-gateway/quick-create-portal.md)a 9. lépéstől kezdve. A konfiguráció befejezése után kattintson **az OK** gombra.

#### <a name="configure-url-routing-for-application-gateways"></a>URL-útválasztás konfigurálása Application Gateway-hez

Ha a háttér-készletet választja, a Path-alapú szabályhoz konfigurált Application Gateway a kérés URL-címéhez tartozó elérésiút-mintát veszi fel a ciklikus multiplexelés eloszlása mellett. Ebben a forgatókönyvben egy elérésiút-alapú szabályt adunk hozzá, amely a "/images/" URL-címet \* a rendszerkép-kiszolgáló készlethez irányítja. Az Application Gateway URL-alapú útválasztásának konfigurálásával kapcsolatos további információkért lásd: [elérésiút-alapú szabály létrehozása Application gatewayhez](../application-gateway/create-url-route-portal.md).

![Application Gateway webes réteg diagramja](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Az erőforráscsoporthoz lépjen az előző szakaszban létrehozott Application Gateway-példányra.
2. A **Beállítások** területen válassza a **háttér-készletek** lehetőséget, majd a **Hozzáadás** gombra kattintva adja hozzá azokat a virtuális gépeket, amelyeket a webes rétegbeli háttér-készletekkel szeretne hozzárendelni.
3. Adja meg a háttér-készlet nevét és a készletben lévő gépek összes IP-címét. Ebben a forgatókönyvben a virtuális gépek két háttér-kiszolgáló készletét csatlakoztatjuk.

   ![Application Gateway "háttér-készlet hozzáadása"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Az Application Gateway **beállításai** területen válassza a **szabályok** elemet, majd a szabály hozzáadásához kattintson az **elérésiút-alapú** gombra.

   ![Application Gateway szabályok "elérésiút-alapú" gomb](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Konfigurálja a szabályt az alábbi információk megadásával.

   Alapszintű beállítások:

   + **Name (név**): a portálon elérhető szabály rövid neve.
   + **Figyelő**: a szabályhoz használt figyelő.
   + **Alapértelmezett háttér-készlet**: az alapértelmezett szabállyal használandó háttér-készlet.
   + **Alapértelmezett http-beállítások**: az alapértelmezett SZABÁLLYAL használandó http-beállítások.

   Elérésiút-alapú szabályok:

   + **Name (név**): az elérésiút-alapú szabály rövid neve.
   + **Paths**: a forgalom továbbítására szolgáló elérésiút-szabály.
   + **Háttér-készlet**: a szabályhoz használandó háttér-készlet.
   + **Http-beállítás**: a szabályhoz használandó http-beállítások.

   > [!IMPORTANT]
   > Elérési utak: az érvényes elérési utaknak "/" értékkel kell kezdődnie. A "" helyettesítő karakter \* csak a végén engedélyezett. Érvényes példák:/XYZ,/XYZ \* vagy/XYZ/ \* .

   ![Application Gateway "elérésiút-alapú szabály hozzáadása" panel](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>3. lépés: Application Gateway hozzáadása az Traffic Manager-végpontokhoz

Ebben a forgatókönyvben Traffic Manager a különböző régiókban található Application Gateway-hez (az előző lépésekben konfiguráltak szerint) csatlakozik. Most, hogy konfigurálta az Application Gateway-t, a következő lépés a Traffic Manager profiljához való kapcsolódás.

1. Nyissa meg Traffic Manager-profilját. Ehhez keresse meg az erőforráscsoportot, vagy keresse meg az Traffic Manager profil nevét az **összes erőforrásból**.
2. A bal oldali ablaktáblán válassza a **végpontok** lehetőséget, majd kattintson a **Hozzáadás** elemre egy végpont hozzáadásához.

   ![Traffic Manager végpontok "Hozzáadás" gomb](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Hozzon létre egy végpontot az alábbi információk beírásával:

   * **Típus**: válassza ki a betölteni kívánt végpont típusát. Ebben az esetben válassza az **Azure-végpont** lehetőséget, mert a korábban konfigurált Application Gateway-példányokhoz csatlakoztatjuk.
   * **Név**: adja meg a végpont nevét.
   * **Cél erőforrástípus**: válassza a **nyilvános IP-cím** lehetőséget, majd a **cél erőforrás** területen válassza ki a korábban konfigurált Application Gateway nyilvános IP-címét.

   ![Traffic Manager "végpont hozzáadása"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Most tesztelheti a telepítést úgy, hogy a Traffic Manager profiljának DNS-fiókjával fér hozzá (ebben a példában: TrafficManagerScenario.trafficmanager.net). A kérések újraküldését, a különböző régiókban létrehozott virtuális gépeket és webkiszolgálókat hozhatja létre vagy helyezheti le, és módosíthatja a Traffic Manager profil beállításait a beállítás teszteléséhez.

### <a name="step-4-create-a-load-balancer"></a>4. lépés: terheléselosztó létrehozása

Ebben a forgatókönyvben a Load Balancer a webes rétegből származó kapcsolatokat a magas rendelkezésre állású fürtben lévő adatbázisokra terjeszti.

Ha a magas rendelkezésre állású adatbázis-fürt SQL Server AlwaysOn használ, a Részletes utasításokért tekintse [meg egy vagy több always on rendelkezésre állási csoport figyelőjét](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) .

A belső terheléselosztó konfigurálásával kapcsolatos további információkért lásd: [belső terheléselosztó létrehozása a Azure Portalban](../load-balancer/quickstart-load-balancer-standard-internal-portal.md).

1. A Azure Portal a bal oldali ablaktáblán kattintson az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer** elemre.
2. Válassza ki a terheléselosztó nevét.
3. Állítsa a **típust** **belső** értékre, majd válassza ki a megfelelő virtuális hálózatot és alhálózatot ahhoz, hogy a terheléselosztó elérhető legyen.
4. Az **IP-cím hozzárendelése** területen válassza a **dinamikus** vagy a **statikus** lehetőséget.
5. Az **erőforráscsoport** területen válassza ki a terheléselosztó erőforráscsoportot.
6. A **hely** területen válassza ki a megfelelő régiót a terheléselosztó számára.
7. A terheléselosztó létrehozásához kattintson a **Létrehozás** gombra.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Háttérbeli adatbázis-réteg összekapcsolása a terheléselosztó használatával

1. Az erőforráscsoport alatt keresse meg az előző lépésekben létrehozott terheléselosztó.
2. A **Beállítások** területen kattintson a **háttér-készletek** elemre, majd a **Hozzáadás** elemre a háttérbeli készlet hozzáadásához.

   ![Load Balancer "háttér-készlet hozzáadása"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Adja meg a háttér-készlet nevét.
4. Adjon hozzá különálló gépeket vagy rendelkezésre állási készletet a háttér-készlethez.

#### <a name="configure-a-probe"></a>Mintavétel konfigurálása

1. A terheléselosztó terület **Beállítások** **területén válassza a** mintavételek lehetőséget, majd a mintavétel hozzáadásához kattintson a **Hozzáadás** gombra.

   ![Load Balancer "mintavétel hozzáadása"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Adja meg a mintavétel nevét.
3. Válassza ki a mintavételhez használandó **protokollt** . Az adatbázisok esetében előfordulhat, hogy HTTP-mintavétel helyett TCP-mintavételt szeretne használni. Ha többet szeretne megtudni a terheléselosztó-mintavételekről, tekintse meg a [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md)-tesztek ismertetése című témakört.
4. Adja meg az adatbázisnak a mintavételhez való hozzáféréshez használandó **portját** .
5. Az **intervallum** alatt határozza meg, hogy milyen gyakran történjen az alkalmazás mintavétele.
6. A nem kifogástalan **állapotú küszöbértéknél** határozza meg, hogy a HÁTTÉRBELI virtuális gép nem megfelelő állapotba kerüljön-e a folyamatos mintavételi hibák száma.
7. A mintavétel létrehozásához kattintson **az OK** gombra.

#### <a name="configure-the-load-balancing-rules"></a>Terheléselosztási szabályok konfigurálása

1. A terheléselosztó **beállításai** területen válassza a terheléselosztási **szabályok** lehetőséget, majd kattintson a **Hozzáadás** elemre egy szabály létrehozásához.
2. Adja meg a terheléselosztási szabály **nevét** .
3. Válassza ki a terheléselosztó, a **protokoll** és a **port** előtérbeli **IP-címét** .
4. A **háttér-port** területen válassza ki a háttér-készletben használni kívánt portot.
5. Válassza ki a **háttér-készletet** **és az előző** lépésekben létrehozott mintavételt a szabály alkalmazásához.
6. A **munkamenetek megőrzése** területen válassza ki, hogyan szeretné megőrizni a munkameneteket.
7. Az **Üresjárat időkorlátja** területen állítsa be a percek számát az Üresjárati időkorlát előtt.
8. A **lebegő IP** területen válassza a **Letiltva** vagy az **engedélyezve** lehetőséget.
9. A szabály létrehozásához kattintson az **OK** gombra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>5. lépés: webrétegbeli virtuális gépek összekötése a terheléselosztó szolgáltatással

Most konfiguráljuk az IP-címet és a terheléselosztó előtér-portot a webrétegbeli virtuális gépeken futó alkalmazásokban bármely adatbázis-kapcsolathoz. Ez a konfiguráció az ezeken a virtuális gépeken futó alkalmazásokra vonatkozik. A cél IP-cím és port konfigurálásához tekintse meg az alkalmazás dokumentációját. Az előtér IP-címének megkereséséhez a Azure Portal nyissa meg az előtér-IP-készletet a **terheléselosztó beállításainál**.

![Load Balancer "előtéri IP-címkészlet" navigációs ablaktábla](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Következő lépések

* [A Traffic Manager áttekintése](traffic-manager-overview.md)
* [Az Application Gateway áttekintése](../application-gateway/overview.md)
* [Az Azure Load Balancer áttekintése](../load-balancer/load-balancer-overview.md)