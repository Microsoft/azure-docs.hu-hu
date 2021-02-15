---
title: Magas rendelkezésre állás a Azure Data Factory adatkezelési átjáróval
description: Ez a cikk azt ismerteti, hogyan méretezhető ki egy adatkezelési átjáró további csomópontok hozzáadásával és vertikális felskálázással a csomóponton futtatható egyidejű feladatok számának növelésével.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: ad34ed14682d729157f45e67eb3e0d3bb3eb39b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391728"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Adatkezelés átjáró – magas rendelkezésre állás és méretezhetőség (előzetes verzió)
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a saját üzemeltetésű [integrációs modul](../create-self-hosted-integration-runtime.md)című részét. 


Ez a cikk segítséget nyújt a magas rendelkezésre állást és méretezhetőséget biztosító megoldás konfigurálásához adatkezelés átjáróval/integrációval.    

> [!NOTE]
> Ez a cikk azt feltételezi, hogy már ismeri a Integration Runtime (korábbi adatkezelés átjáró) alapjait. Ha nem, tekintse meg [adatkezelés átjárót](data-factory-data-management-gateway.md).
> 
> **Ez az előzetes verziójú funkció hivatalosan támogatott a adatkezelés-átjáró 2.12. xxxx. x vagy újabb verziójában**. Győződjön meg arról, hogy a 2.12. xxxx. x vagy újabb verziót használja. Töltse le az adatkezelés-átjáró legújabb verzióját [itt](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Áttekintés
Olyan adatkezelési átjárókat társíthat, amelyek több helyszíni gépre vannak telepítve, egyetlen logikai átjáróval a portálon. Ezeket a gépeket **csomópontoknak** nevezzük. Egy logikai átjáróhoz legfeljebb **négy csomópont** társítható. A logikai átjárók számára a következő előnyökkel jár, ha több csomópontot (helyszíni gépeket telepítettek az átjáróval):  

- A helyszíni és a Felhőbeli adattárak közötti adatáthelyezési teljesítmény javítása.  
- Ha a csomópontok valamelyike valamilyen okból leáll, más csomópontok továbbra is elérhetők az adatáthelyezéshez. 
- Ha az egyik csomópontot karbantartás céljából offline állapotba kell helyezni, más csomópontok továbbra is elérhetők az adatáthelyezéshez.

A csomóponton futtatható **egyidejű** adatáthelyezési feladatok számát úgy is beállíthatja, hogy az adatok a helyszíni és a Felhőbeli adattárak között is felméretezhetőek legyenek. 

A Azure Portal használatával nyomon követheti ezen csomópontok állapotát, így eldöntheti, hogy a logikai átjáróból szeretne-e csomópontot hozzáadni vagy eltávolítani. 

## <a name="architecture"></a>Architektúra 
Az alábbi ábra az adatkezelés átjáró skálázhatósági és rendelkezésre állási funkciójának architektúráját mutatja be: 

![Adatkezelés átjáró – magas rendelkezésre állás és méretezhetőség](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logikai átjáró** a Azure Portal egy adatgyárhoz hozzáadott átjáró. Korábban egy logikai átjáróval telepített adatkezelés átjáróval rendelkező helyszíni Windows-gépet is hozzárendelhet. Ezt a helyszíni átjárót tartalmazó számítógépet csomópontnak nevezzük. Most akár **négy fizikai csomópontot** is hozzárendelhet logikai átjáróval. A több csomóponttal rendelkező logikai átjárókat **több csomópontos átjárónak** nevezzük.  

Ezek a csomópontok **aktívak**. Az adatáthelyezési feladatok feldolgozásával az adatok a helyszíni és a Felhőbeli adattárak között helyezhetők át. Az egyik csomópont a diszpécserként és a feldolgozóként is működik. A csoportok további csomópontjai munkavégző csomópontok. A **kiosztó** csomópont lekéri az adatáthelyezési feladatokat/feladatokat a Cloud Service-ből, és elküldi őket a feldolgozó csomópontoknak (beleértve a saját magát). A **feldolgozó csomópont** adatáthelyezési feladatokat hajt végre az adatok helyszíni és felhőalapú adattárak közötti mozgatásához. Minden csomópont feldolgozó. Csak egy csomópont lehet a küldő és a feldolgozó is.    

Általában egy csomóponttal kezdődhet, és **kibővítheti** a további csomópontok hozzáadását, mivel a meglévő csomópont (ok) túlterhelt az adatáthelyezési terheléssel. Az átjáró-csomópontok adatáthelyezési képességeinek növelésével a csomóponton futtatható egyidejű feladatok **számát is növelheti** . Ez a funkció egy egycsomópontos átjáróval is elérhető (még akkor is, ha a méretezhetőség és a rendelkezésre állási funkció nincs engedélyezve). 

A több csomóponttal rendelkező átjárók az összes csomóponton szinkronizálják az adattár hitelesítő adatait. Csomópontok közötti csatlakozási probléma esetén előfordulhat, hogy a hitelesítő adatok nem szinkronizálhatók. Amikor átjárót használó helyszíni adattár hitelesítő adatait állítja be, a hitelesítő adatokat ment a diszpécser/feldolgozó csomóponton. A diszpécser csomópont más munkavégző csomópontokkal szinkronizál. Ezt a folyamatot **hitelesítő adatok szinkronizálásának** nevezzük. A csomópontok közötti kommunikációs csatornát egy nyilvános SSL/TLS-tanúsítvány **titkosíthatja** . 

## <a name="set-up-a-multi-node-gateway"></a>Több csomópontos átjáró beállítása
Ez a szakasz azt feltételezi, hogy elvégezte a következő két cikket, vagy ismeri az alábbi cikkekben ismertetett fogalmakat: 

- [Adatkezelés átjáró](data-factory-data-management-gateway.md) – részletes áttekintést nyújt az átjáróról.
- Az [adatáthelyezés a helyszíni és a Felhőbeli adattárak között](data-factory-move-data-between-onprem-and-cloud.md) – egy olyan bemutatót tartalmaz, amely részletes útmutatást nyújt egy átjáró egyetlen csomóponttal való használatához.  

> [!NOTE]
> Mielőtt telepít egy adatkezelési átjárót egy helyszíni Windows-gépen, tekintse meg [a fő cikkben](data-factory-data-management-gateway.md#prerequisites)felsorolt előfeltételek című témakört.

1. A [forgatókönyvben](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)a logikai átjáró létrehozásakor engedélyezze a **magas rendelkezésre állású & skálázhatósági** funkciót. 

    ![Adatkezelés átjáró – magas rendelkezésre állás és méretezhetőség engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. A **configure (Konfigurálás** ) lapon az **Express Setup** vagy a **manuális telepítési** hivatkozás használatával telepítheti az átjárót az első csomóponton (helyszíni Windows-gépen).

    ![Adatkezelés Gateway-Express vagy manuális beállítás](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Az expressz telepítés beállítás használata esetén a csomópontok közötti kommunikáció titkosítás nélkül történik. A csomópont neve megegyezik a számítógépnévvel. Ha a csomópont-csomópont típusú kommunikációt titkosítani kell, vagy a kívánt csomópont nevét szeretné megadni, használja a manuális telepítést. A csomópontok nevei később nem szerkeszthetők.
3. Ha az **expressz telepítőt** választja
    1. Az átjáró sikeres telepítése után a következő üzenet jelenik meg:

        ![Adatkezelés Gateway – expressz telepítés sikeres](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. A következő [utasításokat](data-factory-data-management-gateway.md#configuration-manager)követve indítsa el adatkezelés Configuration Manager az átjáróhoz. Megjelenik az átjáró neve, a csomópont neve, az állapot stb.

        ![Képernyőkép, amely megmutatja, hol láthatja az átjáró nevét, a csomópont nevét és az állapotot.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Ha a **manuális telepítést** választja:
    1. Töltse le a telepítőcsomagot a Microsoft letöltőközpontból, és futtassa az átjáró telepítéséhez a gépen.
    2. A **configure (Konfigurálás** ) lapon használja a **hitelesítési kulcsot** az átjáró regisztrálásához.
    
        ![A hitelesítési kulcs használatának helyét bemutató képernyőkép.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Az **új átjáró csomópont** lapon megadhatja az átjáró csomópontjának egyéni **nevét** . Alapértelmezés szerint a csomópont neve megegyezik a számítógépnévvel.    

        ![Átjáró adatkezelés-adja meg a nevet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. A következő lapon megadhatja, hogy **engedélyezi-e a titkosítást a csomópontok közötti kommunikációhoz**. Kattintson a **kihagyás** gombra a titkosítás letiltásához (alapértelmezett).

        ![Adatkezelés átjáró – titkosítás engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > A titkosítási mód módosítása csak akkor támogatott, ha egyetlen átjáró csomópontja van a logikai átjáróban. Ha úgy szeretné módosítani a titkosítási módot, hogy egy átjáró több csomóponttal rendelkezik, hajtsa végre a következő lépéseket: törölje az összes csomópontot az egyik csomópont kivételével, módosítsa a titkosítási módot, majd adja hozzá újra a csomópontokat.
        > 
        > A TLS/SSL-tanúsítvány használatára vonatkozó követelmények listáját a [TLS/SSL-tanúsítványokra vonatkozó követelmények](#tlsssl-certificate-requirements) című szakaszban találja. 
    5. Az átjáró sikeres telepítése után kattintson az Indítás gombra Configuration Manager:
    
        ![Manuális telepítés – a Configuration Manager elindítása](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)     
    6. megjelenik adatkezelés átjáró Configuration Manager a csomóponton (helyszíni Windows-gépen), amely a kapcsolat állapotát, az **átjáró nevét** és a **csomópont nevét** mutatja.  

        ![Adatkezelés átjáró – sikeres telepítés](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Ha az átjárót egy Azure-beli virtuális gépen építi ki, akkor [ezt a Azure Resource Manager sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway)használhatja. Ez a szkript létrehoz egy logikai átjárót, beállítja a virtuális gépeket adatkezelés telepített átjáró szoftverrel, és regisztrálja őket a logikai átjáróval. 
6. Az Azure Portalban indítsa el az **átjáró** lapot: 
    1. A portálon, a portálon kattintson a **társított szolgáltatások** elemre.
    
        ![Képernyőkép, amely kiemeli a társított szolgáltatások csempéjét.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Válassza ki **az átjárót** az **átjáró** lap megjelenítéséhez:
    
        ![Data factory kezdőlap](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Megjelenik az **átjáró** lapja:   

        ![Átjáró egyetlen csomópontos nézettel](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kattintson a **Csomópont hozzáadása** elemre az eszköztáron, és adjon hozzá egy csomópontot a logikai átjáróhoz. Ha az expressz telepítés használatát tervezi, ezt a lépést a helyszíni gépről kell végrehajtania, amely csomópontként lesz hozzáadva az átjáróhoz. 

    ![Adatkezelés átjáró – csomópont hozzáadása menü](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. A lépések hasonlóak az első csomópont beállításához. A Configuration Manager felhasználói felület lehetővé teszi a csomópont nevének megadását, ha a manuális telepítési lehetőséget választja: 

    ![Configuration Manager – a második átjáró telepítése](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Miután az átjáró telepítése sikeresen megtörtént a csomóponton, a Configuration Manager eszköz a következő képernyőt jeleníti meg:  

    ![Configuration Manager – a második átjáró telepítése sikerült](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Ha megnyitja az **átjáró** lapot a portálon, akkor két átjáró csomópont jelenik meg most: 

    ![Átjáró két csomóponttal a portálon](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Az átjárók csomópontjának törléséhez kattintson a **csomópont törlése** elemre az eszköztáron, válassza ki a törölni kívánt csomópontot, majd kattintson a **Törlés** elemre az eszköztárból. Ez a művelet törli a kijelölt csomópontot a csoportból. Vegye figyelembe, hogy ez a művelet nem távolítja el az adatkezelési átjáró szoftverét a csomópontról (helyszíni Windows rendszerű számítógépről). Az átjáró eltávolításához használja a Vezérlőpult **Programok telepítése és törlése** paneljét. Ha az átjárót a csomópontról távolítja el, az automatikusan törlődik a portálon.   

## <a name="upgrade-an-existing-gateway"></a>Meglévő átjáró frissítése
Egy meglévő átjárót frissítheti a magas rendelkezésre állás és méretezhetőség szolgáltatás használatára. Ez a szolgáltatás csak olyan csomópontokkal működik, amelyeken >= 2.12. xxxx verziójú adatkezelési átjáró van. A számítógépen telepített adatkezelési átjáró verziója a adatkezelés Configuration Manager átjárójának **Súgó** lapján tekinthető meg. 

1. Frissítse az átjárót a helyszíni gépen a legújabb verzióra egy MSI-telepítőcsomag letöltésével és futtatásával a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). A részletekért lásd a [telepítés](data-factory-data-management-gateway.md#installation) című szakaszt.  
2. Lépjen az Azure Portalra. Indítsa el az adatelőállító **Data Factory lapját** . Kattintson a társított szolgáltatások csempére a **társított szolgáltatások lap** elindításához. Válassza ki az átjárót az **átjáró oldalának** elindításához. Kattintson és engedélyezze az **előnézet funkciót** az alábbi képen látható módon: 

    ![Adatkezelés átjáró – Előnézet funkció engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Ha az előzetes verzió funkció engedélyezve van a portálon, akkor zárjunk be minden oldalt. Nyissa meg újra az **átjáró lapot** az új előzetes verziójú felhasználói felület (UI) megtekintéséhez.
 
    ![Adatkezelés átjáró – az Előnézet funkció engedélyezése sikeres](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Adatkezelés átjáró – előzetes verzió felhasználói felülete](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > A frissítés során az első csomópont neve a gép neve. 
3. Most adjon hozzá egy csomópontot. Az **átjáró** lapon kattintson a **Csomópont hozzáadása** elemre.  

    ![Adatkezelés átjáró – csomópont hozzáadása menü](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    A csomópont beállításához kövesse az előző szakasz utasításait. 

### <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez

- Konfigurálja az energiagazdálkodási tervet a gazdagépen az átjáróhoz, hogy a gép ne legyen hibernálva. Ha a gazdaszámítógép hibernált állapotba kerül, az átjáró nem válaszol az adatkérelmekre.
- Az átjáróhoz társított tanúsítvány biztonsági mentése.
- Győződjön meg arról, hogy az összes csomópont hasonló konfigurációval rendelkezik (ajánlott) az ideális teljesítmény érdekében. 
- Adjon hozzá legalább két csomópontot a magas rendelkezésre állás biztosításához.  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-tanúsítványokra vonatkozó követelmények
Az Integration Runtime-csomópontok közötti kommunikáció biztonságossá tételéhez használt TLS/SSL-tanúsítványra vonatkozó követelmények a következők:

- A tanúsítványnak nyilvánosan megbízható X509 v3 tanúsítványnak kell lennie. Javasoljuk, hogy használjon nyilvános (külső) hitelesítésszolgáltató (CA) által kiadott tanúsítványokat.
- Minden egyes Integration Runtime-csomópontnak meg kell bíznia a tanúsítványban, valamint a Hitelesítőadat-kezelő alkalmazást futtató ügyfélszámítógépen. 
  > [!NOTE]
  > A Hitelesítőadat-kezelő alkalmazást a rendszer a hitelesítő adatok a másolási varázslóból/Azure Portalról való biztonságos beállításakor használja. Ez pedig bármely, a helyszíni/privát adattárral azonos hálózaton belüli gépről elvégezhető.
- A Wild Card-tanúsítványok támogatottak. Ha a teljes tartománynév neve **node1.domain.contoso.com**, a **_. domain.contoso.com_* nevet használhatja a tanúsítvány tulajdonos neveként.
- A SAN-tanúsítványok használata nem ajánlott, mert a rendszer csak a tulajdonos alternatív neveinek utolsó tételét fogja használni, és az összes többi figyelmen kívül lesz hagyva a jelenlegi korlátozás miatt. Például olyan SAN-tanúsítvánnyal rendelkezik, amelynek SAN a **node1.domain.contoso.com** és a **node2.domain.contoso.com**, ezt a tanúsítványt csak olyan gépen használhatja, amelynek teljes tartományneve **node2.domain.contoso.com**.
- A a Windows Server 2012 R2 által támogatott bármely, a TLS/SSL-tanúsítványokra vonatkozó kulcs méretét támogatja.
- A CNG-kulcsokat használó tanúsítvány nem támogatott.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Gyakori kérdések: Mikor nem engedélyezem ezt a titkosítást?
Ha engedélyezi a titkosítást, a következő esetekben kihagyhatja a titkosítás engedélyezését az infrastruktúrában (birtokló nyilvános tanúsítvány).
- Ha az Integration Runtime egy megbízható hálózaton fut, vagy egy olyan hálózat, amely transzparens titkosítással (például IP/s) rendelkezik. Mivel a csatorna kommunikációja csak a megbízható hálózaton belül van korlátozva, előfordulhat, hogy nincs szükség további titkosításra.
- Ha az Integration Runtime nem éles környezetben fut. Ez segít csökkenteni a TLS/SSL-tanúsítvány költségeit.


## <a name="monitor-a-multi-node-gateway"></a>Több csomópontos átjáró figyelése
### <a name="multi-node-gateway-monitoring"></a>Több csomópontos átjáró figyelése
A Azure Portal az erőforrások kihasználtságának közel valós idejű pillanatképét (CPU, memória, hálózat (in/out) stb.) megtekintheti az egyes csomópontokon, az átjáró-csomópontok állapotával együtt. 

![Adatkezelés átjáró – több csomópont figyelése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

A **Speciális beállítások** megadásával az **átjáró** lapon megtekintheti a speciális mérőszámokat, például a **hálózati**(in/out), a **szerepkör & a hitelesítő adatokat**, amelyek hasznosak lehetnek az átjárókkal kapcsolatos problémák megoldásához, valamint az **egyidejű feladatok** (Futtatás/korlát), amely a teljesítmény finomhangolása során módosítható/módosítható. Az alábbi táblázat az **átjáró-csomópontok** listájában szereplő oszlopok leírását tartalmazza:  

Figyelési tulajdonság | Leírás
:------------------ | :---------- 
Név | Az átjáróhoz társított logikai átjáró és csomópontok neve.  
Állapot | A logikai átjáró és az átjáró csomópontjainak állapota. Példa: online/offline/korlátozott/stb. További információ ezekről az állapotokról: [átjáró állapota](#gateway-status) szakasz. 
Verzió | Megjeleníti a logikai átjáró és az egyes átjáró-csomópontok verzióját. A logikai átjáró verziószáma a csoport csomópontjainak többsége alapján van meghatározva. Ha a logikai átjáró beállításában különböző verziójú csomópontok vannak, akkor csak a logikai átjáróval megegyező verziószámmal rendelkező csomópontok működnek. Mások korlátozott módban vannak, és manuálisan kell frissíteni (csak abban az esetben, ha az automatikus frissítés meghiúsul). 
Igénybe vehető memória | Rendelkezésre álló memória egy átjáró-csomóponton. Ez az érték a közel valós idejű pillanatkép. 
Processzorhasználat | Egy átjáró-csomópont CPU-kihasználtsága. Ez az érték a közel valós idejű pillanatkép. 
Hálózatkezelés (be/ki) | Átjáró-csomópontok hálózati kihasználtsága. Ez az érték a közel valós idejű pillanatkép. 
Egyidejű feladatok (futó/korlát) | Az egyes csomópontokon futó feladatok vagy feladatok száma. Ez az érték a közel valós idejű pillanatkép. A korlát az egyes csomópontok maximális egyidejű feladatait jelzi. Ez az érték a gép méretétől függően van meghatározva. Az egyidejű feladatok végrehajtásának korlátját növelheti speciális forgatókönyvekben, ahol a CPU/memória/hálózat nincs használatban, a tevékenységek azonban időtúllépést okozhatnak. Ez a funkció egy egycsomópontos átjáróval is elérhető (még akkor is, ha a méretezhetőség és a rendelkezésre állási funkció nincs engedélyezve). További információ: [méretezési megfontolások](#scale-considerations) szakasz. 
Szerepkör | A szerepköröknek két típusa van: Diszpécser és feldolgozó. Minden csomópont munkavégző, ami azt jelenti, hogy mind a feladatok végrehajtásához használhatók. Csak egy kiosztó csomópont létezik, amely a feladatok/feladatok a Cloud servicesből való lekérésére, valamint a különböző munkavégző csomópontokra (beleértve a saját magára) történő küldésére szolgál. 

![Adatkezelés-átjáró – több csomópontos figyelés](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Átjáró állapota

Az alábbi táblázat egy **átjáró-csomópont** lehetséges állapotát tartalmazza: 

Állapot    | Megjegyzések/forgatókönyvek
:------- | :------------------
Online | Data Factory szolgáltatáshoz csatlakoztatott csomópont.
Offline | A csomópont offline állapotban van.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | Kapcsolódási probléma miatt. A hiba oka lehet a 8050-es HTTP-port, a Service Bus-csatlakozási probléma vagy a hitelesítő adatok szinkronizálása. 
Inaktív | A csomópont más többségi csomópontok konfigurációjától eltérő konfigurációban található.<br/><br/> Egy csomópont inaktív lehet, ha nem tud csatlakozni más csomópontokhoz. 


A következő táblázat a **logikai átjáró** lehetséges állapotait tartalmazza. Az átjáró állapota az átjáró csomópontjainak állapotától függ. 

Állapot | Megjegyzések
:----- | :-------
Regisztráció szükséges | Ehhez a logikai átjáróhoz még nincs regisztrálva csomópont
Online | Az átjáró-csomópontok online állapotban vannak
Offline | Nincs online állapotú csomópont.
Korlátozott | Az átjáró nem minden csomópontja kifogástalan állapotban van. Ez az állapot arra figyelmeztet, hogy egyes csomópontok leállnak. <br/><br/>A hitelesítőadat-szinkronizálási probléma oka lehet a diszpécser/feldolgozó csomóponton. 

### <a name="pipeline-activities-monitoring"></a>Folyamat/tevékenységek figyelése
A Azure Portal egy folyamat-figyelési élményt biztosít részletes csomópont-szintű részletekkel. Például azt jeleníti meg, hogy mely tevékenységek futottak a csomóponton. Ezek az információk hasznosak lehetnek egy adott csomópont teljesítménybeli problémáinak megismeréséhez, a hálózati sávszélesség szabályozása miatt. 

![Adatkezelés átjáró – folyamatok több csomópontos figyelése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Adatkezelés átjáró – folyamat részletei](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Méretezési szempontok

### <a name="scale-out"></a>Horizontális felskálázás
Ha a **rendelkezésre álló memória alacsony** , és a **CPU-használat magas**, új csomópont hozzáadásával kibővítheti a terhelést a gépek között. Ha a tevékenységek sikertelenek, mert az időtúllépés vagy az átjáró csomópontja offline állapotban van, akkor segít, ha csomópontot ad hozzá az átjáróhoz.
 
### <a name="scale-up"></a>Vertikális felskálázás
Ha a rendelkezésre álló memória és a CPU nem jól működik, de az üresjárati kapacitás 0, akkor a csomóponton futtatható egyidejű feladatok számának növelésével növelje a skálázást. Érdemes lehet vertikális felskálázást végezni, ha a tevékenységek időtúllépés miatt megtörténik, mert az átjáró túlterhelt. Ahogy az az alábbi ábrán is látható, növelheti a csomópontok maximális kapacitását. Javasoljuk, hogy a kezdéshez a következővel kezdjen.  

![Adatkezelés Gateway – méretezési szempontok](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Ismert problémák/feltörési változások

- Jelenleg legfeljebb négy fizikai átjáró csomópontja lehet egyetlen logikai átjáróhoz. Ha a teljesítmény szempontjából négynél több csomópontra van szüksége, küldjön e-mailt a címre [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com) .
- Nem regisztrálhat újra egy átjáró-csomópontot egy másik logikai átjáró hitelesítési kulcsával az aktuális logikai átjáróról való váltáshoz. Az újraregisztráláshoz távolítsa el az átjárót a csomópontból, telepítse újra az átjárót, és regisztrálja a másik logikai átjáró hitelesítési kulcsával. 
- Ha a HTTP-proxy szükséges az összes átjáró-csomóponthoz, állítsa a proxyt diahost.exe.config és diawp.exe.config, és a Kiszolgálókezelő használatával győződjön meg arról, hogy az összes csomópont azonos diahost.exe.config és diawip.exe.config. Részletekért lásd: [Proxybeállítások konfigurálása](data-factory-data-management-gateway.md#configure-proxy-server-settings) szakasz. 
- Ha módosítani szeretné a csomópontok közötti kommunikáció titkosítási módját az átjáró Configuration Manager, törölje a portál összes csomópontját, kivéve egyet. Ezután adja hozzá a csomópontokat a titkosítási mód módosítása után.
- Ha úgy dönt, hogy titkosítja a csomópontok közötti kommunikációs csatornát, használjon hivatalos TLS-tanúsítványt. Az önaláírt tanúsítvány kapcsolódási problémákat okozhat, mivel előfordulhat, hogy az azonos tanúsítvány nem megbízható a többi gépen lévő hitelesítésszolgáltatók listájában. 
- Átjáró-csomópontot nem regisztrálhat logikai átjáróra, ha a csomópont verziója alacsonyabb, mint a logikai átjáró verziója. Törölje a logikai átjáró összes csomópontját a portálról, így regisztrálhat egy alacsonyabb verziójú csomópontot (visszalépés). Ha egy logikai átjáró összes csomópontját törli, manuálisan telepítse és regisztrálja az új csomópontokat a logikai átjárón. Ebben az esetben az expressz telepítés nem támogatott.
- Az expressz telepítővel nem lehet csomópontokat telepíteni egy meglévő logikai átjáróra, amely továbbra is Felhőbeli hitelesítő adatokat használ. Megtekintheti a hitelesítő adatok tárolási helyét az átjáró Configuration Manager a beállítások lapon.
- Az expressz telepítővel nem lehet csomópontokat telepíteni egy meglévő logikai átjáróra, amelyen engedélyezve van a csomópontok közötti titkosítás. A titkosítási mód beállítása magában foglalja a tanúsítványok manuális hozzáadását, az expressz telepítés nem több lehetőség. 
- A helyszíni környezetből származó fájlok esetében ne használja a localhost vagy a C:\files-t, mert előfordulhat, hogy a \\ localhost vagy a helyi meghajtó nem érhető el az összes csomóponton keresztül. Ehelyett használja \\ a ServerName\files a fájlok helyének megadásához.


## <a name="rolling-back-from-the-preview"></a>Visszaállítás az előzetes verzióról 
Az előzetes verzióról történő visszaállításhoz törölje az összes csomópontot, de egy csomópontot. Nem számít, hogy mely csomópontokat törli, de győződjön meg arról, hogy van legalább egy csomópontja a logikai átjárón. A csomópontot törölheti az átjáró eltávolításával a gépen vagy a Azure Portal használatával. A Azure Portal a **Data Factory** lapon a társított szolgáltatások elemre kattintva indítsa el a **társított szolgáltatások** lapot. Válassza ki az átjárót az **átjáró** oldalának elindításához. Az átjáró lapon láthatja az átjáróhoz társított csomópontokat. A lapon törölheti a csomópontokat az átjáróról.
 
A törlés után kattintson az **előnézet szolgáltatások** elemre ugyanabban a Azure Portal oldalon, és tiltsa le az előzetes verzió funkciót. Alaphelyzetbe állítja az átjárót egy Node GA-(általános rendelkezésre állási) átjáróra.


## <a name="next-steps"></a>Következő lépések
Tekintse át a következő cikkeket:
- [Adatkezelés átjáró](data-factory-data-management-gateway.md) – részletes áttekintést nyújt az átjáróról.
- Az [adatáthelyezés a helyszíni és a Felhőbeli adattárak között](data-factory-move-data-between-onprem-and-cloud.md) – egy olyan bemutatót tartalmaz, amely részletes útmutatást nyújt egy átjáró egyetlen csomóponttal való használatához. 
