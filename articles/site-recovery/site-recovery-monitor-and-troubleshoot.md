---
title: Figyelő Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery replikációs problémák és műveletek figyelése és hibaelhárítása a portál használatával
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: a80f68c230bfed5caad4fd5ce474d8d06fd3d093
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579324"
---
# <a name="monitor-site-recovery"></a>A Site Recovery monitorozása

Ebből a cikkből megtudhatja, hogyan figyelheti az Azure [site Recoveryt](site-recovery-overview.md)site Recovery beépített figyelés használatával.  A következőket figyelheti:

- A Site Recovery által replikált gépek állapota és állapota
- A gépek feladatátvételi állapotának tesztelése.
- A konfigurációt és a replikálást érintő problémák és hibák.
- Infrastruktúra-összetevők, például helyszíni kiszolgálók.


## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, érdemes áttekinteni a [gyakori figyelési kérdéseket](monitoring-common-questions.md) .

## <a name="monitor-in-the-dashboard"></a>Figyelő az irányítópulton

1. A tárolóban kattintson az **Áttekintés** elemre. A Recovery Services irányítópult egyetlen helyen összesíti a tár összes figyelési információját. Mindkét Site Recovery és a Azure Backup szolgáltatáshoz lapok is vannak, amelyek között válthat.

    ![Site Recovery irányítópult](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Az irányítópulton különböző területekre szűkítheti a részletezést. 

    ![Képernyőkép, amely megjeleníti az irányítópult azon területeit, amelyeken részletezheti a részletezést.](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. A **replikált elemek** területen kattintson az **összes megtekintése** elemre a tárolóban lévő összes kiszolgáló megtekintéséhez.
4. A részletezéshez kattintson az egyes szakaszok állapot részleteire.
5. Az **infrastruktúra nézetben** a replikálni kívánt gépek típusától függően rendezheti a figyelési adatokat.

## <a name="monitor-replicated-items"></a>Replikált elemek figyelése

A **replikált elemekben** figyelje a tárolóban lévő összes olyan gép állapotát, amelyen engedélyezve van a replikáció.

**Állapot** | **Részletek**
--- | ---
Kifogástalan | A replikáció általában folyamatban van. A rendszer nem észlel hibát vagy figyelmeztetési tüneteket.
Figyelmeztetés | A rendszer egy vagy több olyan figyelmeztetési tünetet észlel, amely hatással lehet a replikációra.
Kritikus | Egy vagy több kritikus replikációs hiba tünetei észlelhetők.<br/><br/> Ezek a hibák általában a replikálás során beragadtak, vagy az adatváltozási sebességnek megfelelően nem haladnak előre.
Nem alkalmazható | Azok a kiszolgálók, amelyeknek jelenleg nem várható a replikálásuk. Ilyenek lehetnek a feladatátvétel alatt álló gépek.

## <a name="monitor-test-failovers"></a>Feladatátvételi teszt figyelése

A **feladatátvételi teszt sikeressége** során figyelje a tárolóban lévő gépek feladatátvételi állapotát.

- Javasoljuk, hogy hat havonta legalább egyszer futtasson feladatátvételi tesztet a replikált gépeken. Ez a módszer azt vizsgálja, hogy a feladatátvétel a várt módon működik-e, az éles környezet megszakítása nélkül. 
- A feladatátvételi teszt csak akkor tekinthető sikeresnek, ha a feladatátvétel és feladatátvétel utáni karbantartás sikeresen befejeződött.

**Állapot** | **Részletek**
--- | ---
Tesztelés javasolt | Azok a gépek, amelyeknek nincs feladatátvételi tesztje, mivel a védelem engedélyezve volt.
Sikeresen elvégezve | Gépek sikeres feladatátvételi teszttel.
Nem alkalmazható | Azok a gépek, amelyek jelenleg nem jogosultak feladatátvételi tesztre. A feladatátvételt nem igénylő gépek például a kezdeti replikálási/tesztelési feladatátvétel/feladatátvétel folyamatban van.

## <a name="monitor-configuration-issues"></a>Konfigurációs problémák figyelése

**Konfigurációs problémák** esetén figyelje azokat a problémákat, amelyek hatással lehetnek a sikeres feladatátvételre.

- A konfigurációs problémákat (kivéve a szoftverfrissítés rendelkezésre állását) egy periodikus érvényesítő művelet észleli, amely alapértelmezés szerint 12 óránként fut. Az érvényesítési művelet azonnal futtatható, ha a frissítés ikonra kattint a **konfigurációs problémák** szakasz fejléce mellett.
- A hivatkozásokra kattintva további részleteket tudhat meg. Az egyes gépeket érintő problémák esetén kattintson a **szükséges figyelmet** a **cél konfigurációk** oszlopban. A részletek közé tartoznak a szervizeléssel kapcsolatos javaslatok.

**Állapot** | **Részletek**
--- | ---
Hiányzó konfigurációk | Hiányzik egy szükséges beállítás, például egy helyreállítási hálózat vagy egy erőforráscsoport.
Hiányzó erőforrások | Egy megadott erőforrás nem található, vagy nem érhető el az előfizetésben. Az erőforrás például törölve lett vagy át lett telepítve. A figyelt erőforrások közé tartozik a célként megadott erőforráscsoport, a cél VNet/alhálózat, a napló/cél Storage-fiók, a cél rendelkezésre állási csoport, a célként megadott IP-cím.
Előfizetési kvóta |  A rendszer összehasonlítja a rendelkezésre álló előfizetési erőforrás-kvóta egyenlegét a tárolóban lévő összes gép feladatátvételéhez szükséges egyenleggel.<br/><br/> Ha nincs elegendő erőforrás, a rendszer nem elegendő kvóta-egyenleget jelez.<br/><br/> A kvóták a virtuális gépek alapszámának, a virtuálisgép-család alapszámának, a hálózati kártya (NIC) darabszámának figyelésére szolgálnak.
Szoftverfrissítések | Az új szoftverfrissítések rendelkezésre állása és a lejáró szoftverekkel kapcsolatos információk.


## <a name="monitor-errors"></a>Figyelési hibák

A **hiba összefoglalása** során figyelje a jelenleg aktív hibák tüneteit, amelyek hatással lehetnek a tárolóban lévő kiszolgálók replikálására, és figyelheti az érintett gépek számát.

- A helyszíni infrastruktúra-összetevőket érintő hibák megjelennek a szakasz elején. Például nem érkezik szívverés a Azure Site Recovery szolgáltatótól a helyszíni konfigurációs kiszolgálón vagy a Hyper-V-gazdagépen.
- Ezt követően a replikált kiszolgálókat érintő replikációs hibák tünetei láthatók.
- A tábla bejegyzései a hiba súlyosságának csökkenő sorrendjében vannak rendezve, majd az érintett gépek számának csökkenő sorrendbe állításával.
- Az érintett kiszolgálók száma hasznos módszer annak megértéséhez, hogy egyetlen mögöttes probléma milyen hatással lehet több gépre. Például egy hálózati hiba hatással lehet az Azure-ba replikált összes gépre. 
- Egyetlen kiszolgálón több replikációs hiba is megjelenhet. Ebben az esetben minden egyes hiba tünete a kiszolgálót az érintett kiszolgálók listáján számolja. A probléma kijavítása után a replikációs paraméterek javulnak, és a rendszer törli a hibát a gépről.

## <a name="monitor-the-infrastructure"></a>Az infrastruktúra figyelése.

Az **infrastruktúra nézetben** figyelje a replikációban részt vevő infrastruktúra-összetevőket, valamint a kiszolgálók és az Azure-szolgáltatások közötti kapcsolati állapotot.

- A zöld vonal azt jelzi, hogy a kapcsolatok kifogástalanok.
- Az átfedésben lévő hibát jelző piros vonal azt jelzi, hogy a kapcsolódást befolyásoló hibák közül egy vagy több is fennáll.
-  Vigye az egérmutatót a hiba ikon fölé a hiba és az érintett entitások számának megjelenítéséhez. Kattintson az érintett entitások szűrt listájának ikonjára.

    ![Site Recovery infrastruktúra nézet (tároló)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tippek az infrastruktúra monitorozásához

- Győződjön meg arról, hogy a helyszíni infrastruktúra-összetevők (konfigurációs kiszolgáló, folyamat-kiszolgálók, VMM-kiszolgálók, Hyper-V-gazdagépek, VMware-gépek) a Site Recovery szolgáltató és/vagy ügynökök legújabb verzióit futtatják.
- Az infrastruktúra nézet összes funkciójának használatához az ezen összetevőkhöz tartozó [22-es kumulatív frissítést](https://support.microsoft.com/help/4072852) kell futtatnia.
- Az infrastruktúra nézet használatához válassza ki a megfelelő replikációs forgatókönyvet a környezetében. További részletekért tekintse meg a nézet részletezését. A következő táblázat bemutatja, hogy mely forgatókönyvek jelennek meg.

    **Forgatókönyv** | **Állapot**  | **Elérhető a nézet?**
    --- |--- | ---
    **Replikáció a helyszíni helyek között** | Minden állam | No 
    **Azure-beli virtuális gépek Azure-régiók közötti replikációja**  | Replikáció engedélyezve/kezdeti replikálás folyamatban | Yes
    **Azure-beli virtuális gépek Azure-régiók közötti replikációja** | Feladatátvétel/feladat-visszavétel | No   
    **VMware–Azure replikálás** | Replikáció engedélyezve/kezdeti replikálás folyamatban | Yes     
    **VMware–Azure replikálás** | Feladatátvétel/feladatátvétel meghiúsult | No      
    **Hyper-V–Azure replikálás** | Feladatátvétel/feladatátvétel meghiúsult | No

- Egy replikáló gép infrastruktúra-nézetének megtekintéséhez a tároló menüjében kattintson a **replikált elemek** elemre, és válasszon egy kiszolgálót.  




## <a name="monitor-recovery-plans"></a>Helyreállítási tervek figyelése

A **helyreállítási tervekben** figyelje a csomagok számát, új csomagokat hozzon létre, és módosítsa a meglévőket.  

## <a name="monitor-jobs"></a>Feladatok figyelése

A **feladatok** területen figyelje site Recovery műveletek állapotát.

- A Azure Site Recovery legtöbb művelete aszinkron módon történik, és nyomon követési feladatot hoz létre, és a művelet előrehaladásának követésére szolgál. 
- A feladatütemezés minden olyan információt tartalmaz, amire szüksége van az állapot nyomon követéséhez és a művelet végrehajtásához. 

A feladatokat a következőképpen figyelheti:

1. Az irányítópult > **feladatok** szakaszban megtekintheti az elmúlt 24 órában megjelenő feladatok összegzését, amelyek folyamatban vannak, vagy a bevitelre várnak. Bármelyik állapotra kattintva további információkat kaphat a kapcsolódó feladatokról.
2. Az összes feladat megjelenítéséhez kattintson az **összes megtekintése** elemre az elmúlt 24 órában.

    > [!NOTE]
    > A feladat adatait a tár menüjéből > **site Recovery feladatokhoz** is elérheti. 

2. A **site Recovery feladatok** listában megjelenik a feladatok listája. A felső menüben lekérdezheti egy adott feladat hibáit, szűrheti a feladatok listáját meghatározott feltételek alapján, és exportálhatja a kijelölt feladat részleteit az Excel programba.
3. A feladatok részletezéséhez kattintson rá. 

## <a name="monitor-virtual-machines"></a>Virtuális gépek figyelése

A **replikált elemek** listájában szerezze be a replikált gépek listáját. 
    ![A replikált elemek listájának Site Recovery nézete](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Az információk megtekinthetők és szűrhetők. A felső művelet menüben műveleteket hajthat végre egy adott gépre vonatkozóan, beleértve a feladatátvételi teszt futtatását vagy adott hibák megtekintését is.
3. Az **oszlopok** elemre kattintva további oszlopokat jeleníthet meg, például megjelenítheti a RPO, a célként megadott konfigurációs problémákat és a replikációs hibákat.
4. Kattintson a **Filter (szűrő** ) elemre az információk meghatározott paraméterek, például a replikálási állapot vagy egy adott replikációs házirend alapján történő megtekintéséhez.
5. Kattintson a jobb gombbal egy gépre olyan műveletek elindításához, mint például a feladatátvételi teszt, vagy a hozzá tartozó konkrét hiba részleteinek megtekintéséhez.
6. Kattintson egy gépre a további részletek részletezéséhez. Az adatok a következők:
   - **Replikációs információ**: a gép aktuális állapota és állapota.
   - **RPO** (helyreállítási pont célkitűzése): a virtuális gép aktuális RPO és a RPO utolsó kiszámításának időpontja.
   - **Helyreállítási pontok**: a gép legújabb elérhető helyreállítási pontjai.
   - **Feladatátvételi készültség**: azt jelzi, hogy a rendszer futtatott-e feladatátvételi tesztet a gépen, a gépen futó ügynök verziószáma (a mobilitási szolgáltatást futtató gépek esetében) és a konfigurációs problémák.
   - **Hibák**: a gépen jelenleg megfigyelt replikációs hiba tüneteinek listája, valamint a lehetséges okok/műveletek.
   - **Események**: a gépet érintő közelmúltbeli események kronológiai listája. A hiba részletei a jelenleg megfigyelhető hibák tüneteit mutatják, míg az események a gépet érintő problémák múltbeli rekordjai.
   - **Infrastruktúra nézet**: az infrastruktúra állapotát mutatja a forgatókönyvhöz, amikor a gépek az Azure-ba replikálódnak.

     ![Site Recovery replikált elemek részletei/áttekintés](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Előfizetés e-mailes értesítésekre

Feliratkozhat e-mail-értesítések fogadására ezekre a kritikus eseményekre:
 
- A replikált gép kritikus állapota.
- Nincs kapcsolat a helyszíni infrastruktúra-összetevők és a Site Recovery szolgáltatás között. A tárolóban regisztrált Site Recovery és helyszíni kiszolgálók közötti kapcsolat Szívveréses mechanizmus használatával észlelhető.
- Feladatátvételi hibák.

Az előfizetés a következőképpen történik:

A tár > **figyelés** szakaszban kattintson az **site Recovery események** elemre.
1. Kattintson az **E-mail-értesítések** elemre.
1. Az **e-mail-értesítések** területen kapcsolja be az értesítéseket, és válassza ki, hogy kinek szeretné elküldeni a szolgáltatást. Az összes előfizetés-rendszergazdának elküldheti az értesítéseket, és opcionálisan meghatározott e-mail-címeket is küldhet.

    ![E-mail-értesítések](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Következő lépések

[Ismerkedjen meg](monitor-log-analytics.md) a Azure monitor site Recovery monitorozásával.
