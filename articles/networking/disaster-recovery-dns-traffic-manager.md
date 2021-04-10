---
title: Vész-helyreállítás Azure DNS és Traffic Manager használatával | Microsoft Docs
description: A vész-helyreállítási megoldások áttekintése Azure DNS és Traffic Manager használatával.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 8cb1a490ac8edf2630253b45d99c3394bbe721b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234154"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Vészhelyreállítás az Azure DNS-sel és a Traffic Managerrel

A vész-helyreállítási funkció az alkalmazások jelentős elvesztése miatti helyreállításra összpontosít. A vész-helyreállítási megoldás kiválasztásához az üzleti és technológiai tulajdonosoknak először meg kell határozniuk a katasztrófa során szükséges funkciók szintjét, például a-nem érhető el, részben elérhető a csökkentett funkcionalitás vagy a késleltetett rendelkezésre állás, vagy teljes mértékben elérhető.
A legtöbb nagyvállalati ügyfél olyan többrégiós architektúrát választ ki, amely egy alkalmazás-vagy infrastruktúra-szintű feladatátvételsel szembeni rugalmasságot biztosít. Az ügyfelek több megközelítést is választhatnak a feladatátvétel és a magas rendelkezésre állás érdekében a redundáns architektúrán keresztül. Íme néhány népszerű megközelítés:

- **Aktív – passzív készenléti állapot**: ebben a feladatátvételi megoldásban a virtuális gépek és a készenléti régióban futó más készülékek nem aktívak, amíg nincs szükség feladatátvételre. Az éles környezet azonban biztonsági mentések, virtuálisgép-lemezképek vagy Resource Manager-sablonok formájában replikálódik egy másik régióba. Ez a feladatátvételi mechanizmus költséghatékony, de a teljes feladatátvétel elvégzése hosszabb időt vesz igénybe.
 
    ![Aktív/passzív, hideg készenléti állapottal](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Aktív/passzív – készenléti vész-helyreállítási konfiguráció*

- **Aktív/passzív tesztelési fénnyel**: ebben a feladatátvételi megoldásban a készenléti környezet beállítása minimális konfigurációval történik. A telepítő csak a minimális és kritikus fontosságú alkalmazások támogatásához futtatja a szükséges szolgáltatásokat. A natív formában ez a forgatókönyv csak minimális funkcionalitást tud végrehajtani, azonban a feladatátvételt követően további szolgáltatásokat is felhasználhat, amelyekkel a termelési terhelés nagy részét elvégezheti.
    
    ![Aktív/passzív tesztelési fénnyel](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Ábra: aktív/passzív – kísérleti fény – vész-helyreállítási konfiguráció*

- **Aktív/passzív készenléti állapot**: ebben a feladatátvételi megoldásban a készenléti régió előre bemelegítve van, és készen áll az alapterhelés betöltésére, az automatikus skálázás be van kapcsolva, és az összes példány működőképes lesz. Ez a megoldás nem méretezhető úgy, hogy a teljes éles terhelést kihasználja, de működőképes, és minden szolgáltatás működik. Ez a megoldás a kísérleti fény megközelítésének kibővített változata.
    
    ![Aktív/passzív, meleg készenléti állapottal](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Ábra: aktív/passzív, meleg készenléti vész-helyreállítási konfigurációval*
    
További információ a feladatátvételről és a magas rendelkezésre állásról: vész- [helyreállítás Azure-alkalmazásokhoz](/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>A vész-helyreállítási architektúra megtervezése

A vész-helyreállítási architektúra beállítása két technikai szempontot jelent:
-  Üzembe helyezési mechanizmus használatával replikálhatja a példányokat, az adatforrásokat és a konfigurációkat az elsődleges és a készenléti környezetek között. Ezt a típusú vész-helyreállítási típust natív módon teheti meg az Azure Site-Recovery Microsoft Azure partneri készülékeken/szolgáltatásokon, például a Veritas vagy a NetApp-n keresztül. 
- Megoldás fejlesztése az elsődleges helyről a készenléti helyre irányuló hálózati/webes forgalom átirányításához. A vész-helyreállítási típus Azure DNS, Azure Traffic Manager (DNS) vagy harmadik féltől származó globális terheléselosztó használatával érhető el.

Ez a cikk a hálózati és webes forgalom átirányításán keresztüli megközelítésekre korlátozódik. A Azure Site Recovery beállítására vonatkozó utasításokért lásd: [Azure site Recovery dokumentáció](../site-recovery/index.yml).
A DNS az egyik leghatékonyabb mechanizmus a hálózati forgalom átirányításához, mivel a DNS gyakran globális és külső az adatközpont, és minden regionális vagy rendelkezésre állási zóna (AZ) szintű meghibásodástól elszigetelve van. Az egyik DNS-alapú feladatátvételi mechanizmust és az Azure-t használva két DNS-szolgáltatás ugyanazt a módszert Azure DNS (mérvadó DNS) és az Azure Traffic Manager (DNS-alapú intelligens forgalom-útválasztás) is elvégezheti. 

Fontos megérteni a DNS-ben a cikkben ismertetett megoldások részletes leírására szolgáló néhány fogalmat:
- **DNS – rekord** – a rekordok olyan mutatók, amelyek egy tartományt IPv4-címekre mutatnak. 
- **CNAME vagy kanonikus név** – ez a bejegyzéstípus egy másik DNS-rekordra mutat. A CNAME nem válaszol IP-címmel, hanem a mutatót az IP-címet tartalmazó rekordra. 
- **Súlyozott útválasztás** – az egyik dönthet úgy, hogy súlyozást társít a szolgáltatási végpontokhoz, majd a hozzárendelt súlyok alapján elosztja a forgalmat. Ez az útválasztási módszer a Traffic Manageron belül elérhető négy forgalmi útválasztási mechanizmus egyike. További információ: [súlyozott útválasztási módszer](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioritási útválasztás** – a prioritási útválasztás a végpontok állapotának ellenőrzésén alapul. Alapértelmezés szerint az Azure Traffic Manager az összes forgalmat a legmagasabb prioritású végpontra küldi, és meghibásodás vagy katasztrófa esetén Traffic Manager irányítja a forgalmat a másodlagos végpont felé. További információ: prioritás- [útválasztási módszer](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Manuális feladatátvétel a Azure DNS használatával
A vész-helyreállítási Azure DNS manuális feladatátvételi megoldás a szabványos DNS-mechanizmust használja a biztonsági mentési helyre történő feladatátvételhez. Az Azure DNSon keresztüli manuális beállítás akkor működik a legjobban, ha a hideg készenléti vagy a kísérleti fény megközelítésével együtt használja. 

![Manuális feladatátvétel a Azure DNS használatával](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Ábra – manuális feladatátvétel a Azure DNS használatával*

A megoldásra tett feltételezések a következők:
- Mind az elsődleges, mind a másodlagos végpontok statikus IP-címekkel rendelkeznek, amelyek nem változnak gyakran. Tegyük fel, hogy az elsődleges hely IP-címe 100.168.124.44, a másodlagos hely IP-címe pedig 100.168.124.43.
- Az elsődleges és a másodlagos helyhez Azure DNS zóna is létezik. Tegyük fel, hogy az elsődleges hely a végpont prod.contoso.com, és a biztonsági mentési hely dr.contoso.com. Létezik egy DNS-rekord a fő alkalmazáshoz, amely a www \. contoso.com néven is ismert.   
- Az élettartam a szervezet RTO SLA-készletén vagy alatt van. Ha például egy vállalat a 60 percre állítja be az alkalmazás RTO, akkor az ÉLETTARTAMnak kevesebbnek kell lennie a 60 percnél, lehetőleg minél alacsonyabb legyen. 
  A manuális feladatátvételhez a következőképpen állíthatja be Azure DNS:
- DNS-zóna létrehozása
- DNS-zónák rekordjainak létrehozása
- CNAME rekord frissítése

### <a name="step-1-create-a-dns"></a>1. lépés: DNS létrehozása
Hozzon létre egy DNS-zónát (például a www- \. contoso.com) az alábbi ábrán látható módon:

![DNS-zóna létrehozása az Azure-ban](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Ábra – DNS-zóna létrehozása az Azure-ban*

### <a name="step-2-create-dns-zone-records"></a>2. lépés: DNS-zónák rekordjainak létrehozása

A zónán belül hozzon létre három rekordot (például: www \. contoso.com, prod.contoso.com és Dr.consoto.com) az alábbi ábrán látható módon.

![DNS-zónák rekordjainak létrehozása](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Ábra – DNS-zónák rekordjainak létrehozása az Azure-ban*

Ebben a forgatókönyvben a www- \. contoso.com 30 perc élettartammal rendelkezik, amely jóval a megadott RTO alatt van, és az üzemi hely prod.contoso.com mutat. Ez a konfiguráció a normál üzleti műveletekben van. A prod.contoso.com és a dr.contoso.com ÉLETTARTAMa 300 másodpercre vagy 5 percre van beállítva. Használhat olyan Azure-figyelési szolgáltatást, mint például a Azure Monitor vagy az Azure app-elemzések, vagy bármely partner-figyelési megoldás, mint például a Dynatrace, olyan otthoni fejlesztésű megoldásokat is használhat, amelyek figyelik vagy észlelik az alkalmazás-vagy virtuális infrastruktúra-szintű hibákat.

### <a name="step-3-update-the-cname-record"></a>3. lépés: a CNAME rekord frissítése

Ha a rendszer hibát észlelt, módosítsa a rekord értékét úgy, hogy az az alábbi ábrán látható módon dr.contoso.com mutasson:
       
![CNAME rekord frissítése](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Ábra – a CNAME rekord frissítése az Azure-ban*

30 percen belül, amikor a legtöbb feloldó frissíti a gyorsítótárazott zónafájl fájlját, a rendszer a www-contoso.com irányuló összes lekérdezést \. átirányítja a Dr.contoso.com.
A CNAME érték módosításához a következő Azure CLI-parancsot is futtathatja:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ezt a lépést manuálisan vagy Automation használatával is végrehajthatja. A konzolon vagy az Azure CLI-n keresztül manuálisan is elvégezhető. Az Azure SDK és az API használatával automatizálható a CNAME-frissítés, így nincs szükség manuális beavatkozásra. Az Automation az Azure functions használatával vagy egy harmadik féltől származó figyelési alkalmazásban, vagy akár a helyszínen is felépíthető.

### <a name="how-manual-failover-works-using-azure-dns"></a>A manuális feladatátvétel működése Azure DNS
Mivel a DNS-kiszolgáló a feladatátvételi vagy a katasztrófa-zónán kívül esik, a rendszer minden állásidőtől elszigeteli. Ez lehetővé teszi a felhasználó számára, hogy egy egyszerű feladatátvételi forgatókönyvet, amely költséghatékony, és minden alkalommal működni fog, feltételezve, hogy az operátornak hálózati kapcsolata van a katasztrófa során, és a tükrözést is elvégezheti. Ha a megoldás parancsfájlt használ, az egyiknek biztosítania kell, hogy a parancsfájlt futtató kiszolgáló vagy szolgáltatás szigetelve legyen az éles környezetet érintő problémával. Ne feledje továbbá, hogy a zónában beállított alacsony élettartam, így a világ egyetlen feloldója sem tartja a hosszú ideig gyorsítótárazott végpontot, és az ügyfelek hozzáférhetnek a webhelyhez a RTO belül. A készenléti állapot és a kísérleti fény esetében, mivel néhány előmelegítő és egyéb felügyeleti tevékenység szükséges lehet – az egyiknek elegendő időt kell adnia a tükrözés megkezdése előtt.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatikus feladatátvétel az Azure Traffic Manager használatával
Ha összetett architektúrákkal és több erőforrás-készlettel rendelkezik, amelyek ugyanazt a függvényt képesek elvégezni, akkor az Azure Traffic Manager (a DNS alapján) beállíthatja az erőforrások állapotát, és átirányíthatja a nem kifogástalan erőforrásról az egészséges erőforrásra irányuló forgalmat. A következő példában mind az elsődleges, mind a másodlagos régió teljes üzembe helyezéssel rendelkezik. Ez az üzembe helyezés magában foglalja a Cloud Servicest és egy szinkronizált adatbázist. 

![Automatikus feladatátvétel az Azure Traffic Manager használatával](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Ábra – automatikus feladatátvétel az Azure Traffic Manager használatával*

Azonban csak az elsődleges régió aktívan kezeli a felhasználók hálózati kérelmeit. A másodlagos régió csak akkor válik aktívvá, ha az elsődleges régió a szolgáltatás megszakadását tapasztalja. Ebben az esetben az összes új hálózati kérelem a másodlagos régió felé irányítja át. Mivel az adatbázis biztonsági mentése közel van a közeljövőben, mind a terheléselosztó olyan IP-címekkel rendelkezik, amelyek állapota ellenőrizhető, és a példányok mindig működőképesek, ez a topológia lehetőséget biztosít az alacsony RTO és feladatátvételre, manuális beavatkozás nélkül. A másodlagos feladatátvételi régiónak azonnal elérhetőnek kell lennie az elsődleges régió meghibásodása után.
Ez a forgatókönyv ideális az olyan Azure-Traffic Manager használatára, amely a különböző típusú állapot-ellenőrzésekhez (például HTTP/HTTPS és TCP) készült, beépített mintavételi módszerekkel rendelkezik. Az Azure Traffic Managerhez egy olyan szabályrendszer is tartozik, amely az alább leírtak szerint feladatátvételre is konfigurálható. Tekintsük át a következő megoldást a Traffic Manager használatával:
- Az ügyfél rendelkezik #1 a prod.contoso.com néven ismert 100.168.124.44-végponttal, amely statikus IP-címmel rendelkezik, és egy olyan régiót #2 végpontot, amely a dr.contoso.com statikus IP-címmel, 100.168.124.43-ként ismert. 
-   Ezen környezetek mindegyike egy nyilvános, például egy terheléselosztó-tulajdonsággal van elfoglalva. A terheléselosztó beállítható úgy, hogy a fent látható módon DNS-alapú végpontot vagy teljes tartománynevet (FQDN) rendelkezzen.
-   A 2. régió összes példánya közel valós idejű replikálást folytat az 1. régióval. Továbbá a számítógép lemezképei naprakészek, és az összes szoftver/konfigurációs adat javítás alatt áll, és az 1. régióval összhangban vannak.  
-   Az automatikus skálázás előre konfigurálva van. 

A feladatátvétel Azure Traffic Manager használatával történő konfigurálásának lépései a következők:
1. Új Azure Traffic Manager-profil létrehozása
2. Végpontok létrehozása a Traffic Manager profilon belül
3. Állapot-ellenőrzési és feladatátvételi konfiguráció beállítása

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>1. lépés: új Azure Traffic Manager-profil létrehozása
Hozzon létre egy új Azure Traffic Manager-profilt a contoso123 néven, és válassza ki az útválasztási módszert prioritásként. Ha van olyan már meglévő erőforráscsoport, amelyet hozzá szeretne rendelni, válasszon ki egy meglévő erőforráscsoportot, máskülönben hozzon létre egy új erőforráscsoportot.

![Traffic Manager profil létrehozása](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Ábra – Traffic Manager profil létrehozása*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>2. lépés: végpontok létrehozása a Traffic Manager profilon belül

Ebben a lépésben olyan végpontokat hoz létre, amelyek az éles és a vész-helyreállítási helyekre mutatnak. Itt válassza ki a **típust** külső végpontként, de ha az erőforrás az Azure-ban fut, akkor az **Azure-végpontot** is választhatja. Ha az **Azure-végpontot** választja, akkor válasszon ki egy olyan **célként megadott erőforrást** , amely az Azure által lefoglalt **app Service** vagy **nyilvános IP-cím** . A prioritás értéke **1** , mivel ez az 1. régió elsődleges szolgáltatása.
Hasonlóképpen hozza létre a vész-helyreállítási végpontot Traffic Manageron belül is.

![Vész-helyreállítási végpontok létrehozása](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Ábra – vész-helyreállítási végpontok létrehozása*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>3. lépés: az állapot-ellenőrzési és a feladatátvételi konfiguráció beállítása

Ebben a lépésben a DNS ÉLETTARTAMát 10 másodpercre állítja be, amelyet a legtöbb internetre irányuló rekurzív feloldó megtart. Ez a konfiguráció azt jelenti, hogy a DNS-feloldó nem gyorsítótárazza az információkat 10 másodpercnél tovább. A Endpoint monitor beállításainál az elérési út aktuálisan a/vagy a root értékre van állítva, de a végpontok beállításait testreszabhatja az elérési út kiértékeléséhez, például prod.contoso.com/index. Az alábbi példa a **https** protokollt mutatja be a szondázás protokollként. Azonban a **http** vagy a **TCP** lehetőséget is választhatja. A protokoll kiválasztása a befejezési alkalmazástól függ. A szondázás időköze 10 másodpercre van beállítva, amely lehetővé teszi a gyors szondázás használatát, és az Újrapróbálkozás értéke 3. Ennek eredményeképpen a Traffic Manager feladatátvételt hajt végre a második végponton, ha három egymást követő intervallum hibát regisztrál. A következő képlet az automatikus feladatátvétel teljes idejét határozza meg: idő a feladatátvételhez = TTL + újrapróbálkozás * szondázás intervalluma, és ebben az esetben az érték 10 + 3 * 10 = 40 másodperc (max).
Ha az Újrapróbálkozás értéke 1, a TTL értéke pedig 10 másodperc, akkor a feladatátvételi idő 10 + 1 * 10 = 20 másodperc. Állítsa az újrapróbálkozást 1-nél nagyobb értékre **, hogy elkerülje** a feladatátvételek esélyét a hamis pozitív vagy a kisebb hálózati visszavertség miatt. 


![Állapot-ellenőrzési beállítás](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Ábra – állapot-ellenőrzési és feladatátvételi konfiguráció beállítása*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Hogyan működik az automatikus feladatátvétel a Traffic Manager használatával

Vészhelyzet esetén az elsődleges végpont próbára kerül, és az állapot **romlik** , és a vész-helyreállítási hely **online állapotban** marad. A Traffic Manager alapértelmezés szerint minden forgalmat az elsődleges (legmagasabb prioritású) végpontra irányít. Ha az elsődleges végpont teljesítményének romlása látható, Traffic Manager átirányítja a forgalmat a második végpontra mindaddig, amíg kifogástalan marad. Az egyik lehetőség a Traffic Manageron belüli további végpontok konfigurálására, amelyek további feladatátvételi végpontként szolgálnak, vagy a végpontok közötti terhelést megosztó terheléselosztó.

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Traffic Managerról](../traffic-manager/traffic-manager-overview.md).
- További információ a [Azure DNSról](../dns/dns-overview.md).