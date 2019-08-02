---
title: Operations Manager összekötése Azure Monitorhoz | Microsoft Docs
description: Meglévő System Center Operations Manager-befektetései kamatoztatása, és a Log Analytics képességeinek kiterjesztése érdekében az Operations Managert integrálhatja munkaterületével.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: magoedte
ms.openlocfilehash: 2ea17a4363218351eb6e5ba0678435f3707e4ab9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663737"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Operations Manager összekötése a Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) meglévő beruházásainak fenntartásához és a kibővített képességek Azure monitor-nal való használatához integrálhatja a Operations Managert a log Analytics munkaterületére. Ez lehetővé teszi, hogy kihasználja Azure Monitor naplók lehetőségeit, miközben továbbra is használja a Operations Manager:

* Az informatikai szolgáltatások állapotának monitorozása az Operations Managerrel
* Az incidens- és problémakezelést támogató ITSM-megoldásokkal való integráció megőrzése
* Az Operations Managerrel monitorozott helyszíni és a nyilvános felhőn üzemelő IaaS-alapú virtuális gépeken telepített ügynökök életciklusának felügyelete

A System Center Operations Manager integrálása a szolgáltatás működési stratégiájához a Azure Monitor sebességének és hatékonyságának növelésével biztosítja a Operations Manager adatainak gyűjtését, tárolását és elemzését. A Azure Monitor log-lekérdezések segítenek összekapcsolni a problémákat, és megkeresik a meglévő probléma-kezelési folyamat támogatása során felmerülő problémák hibáit és a feldolgozói ismétlődéseket. A lekérdezési motor rugalmassága a teljesítmény-, esemény-és riasztási adatelemzések, valamint a részletes irányítópultok és jelentéskészítési képességek vizsgálatával lehetővé teszi, hogy az adott adat érthető módon legyen elérhető, és bemutatja, hogy milyen erősséggel Azure Monitor a dicsérő Operations Manager.

A Operations Manager felügyeleti csoportnak jelentést küldő ügynökök a munkaterületen engedélyezett [log Analytics](agent-data-sources.md) adatforrások és megoldások alapján gyűjtenek adatokat a kiszolgálókról. Az engedélyezett megoldástól függően az adatok közvetlenül egy Operations Manager felügyeleti kiszolgálóról a szolgáltatásba kerülnek, vagy az ügynök által felügyelt rendszeren gyűjtött adatok mennyisége miatt közvetlenül az ügynökről egy Log Analytics munkaterületre kerülnek. A felügyeleti kiszolgáló az adatokat közvetlenül továbbítja a szolgáltatásnak, és azok soha nem lesznek az éles vagy az adattárház-adatbázisba írva. Ha egy felügyeleti kiszolgáló elveszti a Azure Monitor kapcsolatát, a rendszer helyileg gyorsítótárazza az adatforgalmat, amíg a kommunikáció újra nem jön. Ha a felügyeleti kiszolgáló tervezett karbantartás vagy nem tervezett leállás miatt offline állapotban van, a felügyeleti csoport egy másik felügyeleti kiszolgálója folytatja a kapcsolódást Azure Monitor.  

Az alábbi ábrán a felügyeleti kiszolgálók és az ügynökök közötti kapcsolat látható egy System Center Operations Manager felügyeleti csoportban és Azure Monitor, beleértve az irányt és a portokat is.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Ha az IT-biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást, a felügyeleti kiszolgálók úgy konfigurálhatók, hogy a konfigurációs adatok fogadásához és a megoldástól függően az összegyűjtött adatok elküldéséhez csatlakozzanak a Log Analytics átjáróhoz. engedélyezve. További információkat és lépéseket arról, hogyan konfigurálhatja a Operations Manager felügyeleti csoportot egy Log Analytics-átjárón keresztül történő kommunikációra Azure Monitorre: [Azure monitor számítógépek összekapcsolása az log Analytics átjáró használatával](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt tekintse át az alábbi követelményeket.

* A Azure Monitor csak a 2016-es vagy újabb System Center Operations Manager, Operations Manager 2012 SP1 UR6 vagy újabb, illetve Operations Manager 2012 R2 UR2 vagy újabb verziókat támogatja. A proxytámogatás az Operations Manager 2012 SP1 UR 7-es és az Operations Manager 2012 R2 UR 3-as verziójában jelent meg.
* A System Center Operations Manager 2016 az USA kormányzati felhővel való integrálásához a 2. kumulatív frissítéssel vagy újabb verzióval rendelkező frissített Advisor felügyeleti csomag szükséges. System Center Operations Manager 2012 R2 esetében a 3. vagy újabb kumulatív frissítéshez mellékelt frissített Advisor felügyeleti csomag szükséges.
* Minden Operations Manager-ügynöknek meg kell felelnie a minimális támogatási feltételeknek. Győződjön meg arról, hogy az ügynökök a minimális frissítéssel rendelkeznek, ellenkező esetben előfordulhat, hogy a Windows-ügynök kommunikációja meghiúsul, és hibákat eredményez a Operations Manager eseménynaplóban.
* Egy Log Analytics-munkaterület. További információkért tekintse át [log Analytics munkaterület áttekintését](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json).   
* Az Azure-ban olyan fiókkal végez hitelesítést, amely tagja a [log Analytics közreműködő szerepkörnek](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users).

* Támogatott régiók – a System Center Operations Manager csak a következő Azure-régiókat támogatja Log Analytics munkaterülethez való kapcsolódáshoz:
    - USA nyugati középső régiója
    - Délkelet-Ausztrália
    - Nyugat-Európa
    - East US
    - Délkelet-Ázsia
    - Kelet-Japán
    - Az Egyesült Királyság déli régiója
    - Közép-India
    - Közép-Kanada
    - USA nyugati régiója, 2.

>[!NOTE]
>Az Azure API-k legutóbbi módosításai megakadályozzák, hogy az ügyfelek sikeresen konfigurálhatják a felügyeleti csoportjuk és a Azure Monitor közötti integrációt. Azon ügyfelek esetében, akik már integrálták a felügyeleti csoportot a szolgáltatással, nem érinti a rendszer, kivéve, ha újra kell konfigurálnia a meglévő kapcsolatokat.  
>A Operations Manager következő verzióihoz új felügyeleti csomag lett közzétéve:
>  
>* A System Center Operations Manager 1801-es verziójának letöltéséhez töltse le [innen](https://www.microsoft.com/download/details.aspx?id=57173) a felügyeleti csomagot  
>* A System Center 2016 – Operations Manager esetében töltse le [innen](https://www.microsoft.com/download/details.aspx?id=57172) a felügyeleti csomagot  
>* System Center Operations Manager 2012 R2 esetén töltse le [innen](https://www.microsoft.com/download/details.aspx?id=57171) a felügyeleti csomagot  
>
>Ez a felügyeleti csomag frissítése nem alkalmazható az 1807-es verzióra, amely a 1801-es verziójú frissítés, és nem a termék teljes összeállítására System Center Operations Manager vonatkozik.   

### <a name="network"></a>Network (Hálózat)

Az alábbi információk a Operations Manager ügynökhöz, a felügyeleti kiszolgálókhoz és az operatív konzolhoz szükséges proxy-és tűzfal-konfigurációs adatokat felsorolják Azure Monitorekkel való kommunikációhoz. Az egyes összetevőktől érkező forgalom a hálózatról Azure Monitorra van kifelé.   

|Resource | Portszám| HTTP-ellenőrzés kihagyása|  
|---------|------|-----------------------|  
|**Ügynök**|||  
|\*.ods.opinsights.azure.com| 443 |Igen|  
|\*.oms.opinsights.azure.com| 443|Igen|  
|\*.blob.core.windows.net| 443|Igen|  
|\*.azure-automation.net| 443|Igen|  
|**Felügyeleti kiszolgáló**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Igen|  
|\*.ods.opinsights.azure.com| 443| Igen|  
|*.azure-automation.net | 443| Igen|  
|**Konzol Operations Manager Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 és 443||  
|\*.microsoft.com| 80 és 443||  
|\*.microsoftonline.com| 80 és 443||  
|\*.mms.microsoft.com| 80 és 443||  
|login.windows.net| 80 és 443||  
|portal.loganalytics.io| 80 és 443||
|api.loganalytics.io| 80 és 443||
|docs.loganalytics.io| 80 és 443||  

### <a name="tls-12-protocol"></a>A TLS 1.2 protokoll

A Azure Monitor felé irányuló adatforgalom biztosításához határozottan javasoljuk, hogy az ügynököt és a felügyeleti csoportot legalább Transport Layer Security (TLS) 1,2 használatára konfigurálja. Biztonsági rés található régebbi verziói a TLS/Secure Sockets Layer (SSL), és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**. További információkért tekintse át a [biztonságosan a TLS 1.2 használatával az adatok küldésének](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Operations Manager csatlakoztatása Azure Monitor

Az alábbi lépések végrehajtásával konfigurálhatja Operations Manager felügyeleti csoportját, hogy a Log Analytics munkaterületei egyikéhez csatlakozzon.

A Operations Manager felügyeleti csoport Log Analytics munkaterületre való kezdeti regisztrációja során a felügyeleti csoport proxy-konfigurációjának megadására szolgáló beállítás nem érhető el az operatív konzolon.  A felügyeleti csoportot sikeresen regisztrálni kell a szolgáltatásban ahhoz, hogy ez a lehetőség rendelkezésre álljon.  Ennek megkerüléséhez frissítenie kell a rendszerproxy konfigurációját a netsh használatával azon a rendszeren, amelyen az operatív konzolt futtatja az integráció konfigurálásához, valamint a felügyeleti csoport összes felügyeleti kiszolgálóját.  

1. Nyisson meg egy emelt szintű parancssort.
   a. Lépjen a **Start** , és írja be **cmd**.
   b. Kattintson a jobb gombbal **parancssor** , és válassza ki futtató rendszergazda **.
1. Írja be a következő parancsot, majd nyomja le az **Enter** billentyűt:

    `netsh winhttp set proxy <proxy>:<port>`

Miután elvégezte a következő lépéseket a Azure monitor-nal való integráláshoz, a konfigurációt eltávolíthatja a futtatásával `netsh winhttp reset proxy` , majd az operatív konzol **proxykiszolgáló konfigurálása** lehetőségével megadhatja a proxyt vagy log Analytics átjárókiszolgáló .

1. Nyissa meg az Operatív konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
1. Kattintson a **Register to Operations Management Suite** (Regisztráció az Operations Management Suite-be) hivatkozásra.
1. Az **Operations Management Suite előkészítési varázslója: Hitelesítés** lapon adja meg a OMS-előfizetéshez társított rendszergazdai fiók e-mail-címét, telefonszámát és jelszavát, majd kattintson a **Bejelentkezés**elemre.

   >[!NOTE]
   >Az Operations Management Suite neve ki lett vonva.

1. A sikeres hitelesítés után az **Operations Management Suite előkészítési varázslója: Válassza ki** a munkaterület lapot, és válassza ki az Azure-bérlőt, az előfizetést és a log Analytics munkaterületet. Ha több munkaterülettel is rendelkezik, válassza ki a legördülő listából azt, amelyiket az Operations Manager felügyeleti csoportba regisztrálni szeretne, majd kattintson a **Tovább** gombra.

   > [!NOTE]
   > Az Operations Manager egyszerre csak egy Log Analytics-munkaterület használatát támogatja. Az előző munkaterülettel Azure Monitor regisztrált kapcsolatok és számítógépek el lesznek távolítva a Azure Monitorból.
   >
   >
1. Az **Operations Management Suite előkészítési varázslója: Összefoglalás** lapon erősítse meg a beállításokat, és ha helyesek, kattintson a **Létrehozás**gombra.
1. Az **Operations Management Suite előkészítési varázslója: Befejezés** lapon kattintson a **Bezárás**gombra.

### <a name="add-agent-managed-computers"></a>Ügynök által felügyelt számítógépek hozzáadása

Miután konfigurálta az integrációt a Log Analytics munkaterülettel, csak kapcsolatot létesít a szolgáltatással, a felügyeleti csoportnak jelentett ügynökök nem gyűjtenek adatokat. Ez addig nem történik meg, amíg be nem állította, hogy az ügynök által felügyelt számítógépek hogyan gyűjtik be a Azure Monitor naplózási adatait. A számítógép-objektumokat kiválaszthatja egyenként, vagy kiválaszthat egy Windows számítógép-objektumokat tartalmazó csoportot. Nem választhat olyan csoportot, amely egy másik osztályba tartozó példányokat, például logikai lemezeket vagy SQL-adatbázisokat tartalmaz.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
1. Kattintson a **Számítógép/csoport hozzáadása** hivatkozásra a panel jobb oldalán a Műveletek cím alatt.
1. A **Számítógép keresése** párbeszédpanelen az Operations Manager által megfigyelt számítógépekre vagy csoportokra kereshet. Jelölje ki a számítógépeket vagy csoportokat, beleértve a Operations Manager felügyeleti kiszolgálót, amelybe be szeretné Azure Monitor a bevezetést, kattintson a **Hozzáadás**, majd **az OK**gombra.

A Felügyelt számítógépek csomópont adatgyűjtésre konfigurált számítógépei és csoportjai az Operations Management Suite-ben az Operatív konzol **Adminisztráció** munkaterületén láthatók. Itt szükség szerint hozzá is adhat és el is távolíthat számítógépeket és csoportokat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Proxybeállítások konfigurálása az Operatív konzolon

Ha a felügyeleti csoport és a Azure Monitor között belső proxykiszolgáló található, hajtsa végre a következő lépéseket. Ezek a beállítások központilag kezelhetők a felügyeleti csoportból, és az ügynök által felügyelt rendszerekhez vannak elosztva, amelyek a hatókörben találhatók a Azure Monitor naplózási adatainak összegyűjtéséhez.  Ez hasznosnak bizonyul, ha egyes megoldások megkerülik a felügyeleti kiszolgálót, és közvetlenül a szolgáltatásba küldik az adatokat.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki a Microsoft Operations Management Suite elemet, majd kattintson a **Kapcsolatok** gombra.
1. Az OMS Connection (OMS-kapcsolat) nézetben kattintson a **Configure Proxy Server** (Proxykiszolgáló konfigurálása) lehetőségre.
1. Az **Operations Management Suite varázslóban: Proxykiszolgáló lapon jelölje be a **proxykiszolgáló használata az Operations Management Suite eléréséhez**lehetőséget, majd írja be az URL-címet a portszámmal, például, http://corpproxy:80 majd kattintson a **Befejezés**gombra.**

Ha a proxykiszolgáló hitelesítést igényel, a következő lépésekkel konfigurálhatja azokat a hitelesítő adatokat és beállításokat, amelyeket a felügyeleti csoportba Azure Monitor jelentésekkel rendelkező felügyelt számítógépekre kell terjeszteni.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. A **RunAs Configuration** (RunAs-konfiguráció) területen válassza a **Profiles** (Profilok) lehetőséget.
1. Nyissa meg a **System Center Advisor Run As Profile Proxy** (System Center Advisor RunAs-profiljának proxyja) profilt.
1. A futtatásiprofil-varázslóban kattintson az Add (Hozzáadás) lehetőségre egy futtató fiók használatához. Létrehozhat egy [futtató fiókot](https://technet.microsoft.com/library/hh321655.aspx), vagy használhat egy meglévő fiókot is. Ennek a fióknak rendelkeznie kell a megfelelő engedélyekkel a proxykiszolgálón való áthaladáshoz.
1. A felügyelni kívánt fiók beállításához válassza az **Egy kijelölt osztály, csoport vagy objektum** lehetőséget, kattintson a **Kiválasztás…** gombra, majd kattintson a **Csoport...** gombra a **Csoport keresése** keresőmező megnyitásához.
1. Keresse meg és válassza ki a **Microsoft System Center Advisor Monitoring Server Group** (Microsoft System Center Advisor monitorozásikiszolgáló-csoport) nevű csoportot. Miután kiválasztotta a csoportot, kattintson az **OK** gombra a **Csoport keresése** keresőmező bezárásához.
1. Az **OK** gombra kattintva zárja be az **Add a Run As account** (Futtató fiók hozzáadása) mezőt.
1. Kattintson a **Mentés** gombra a varázsló befejezéséhez és a módosítások mentéséhez.

Miután létrejött a kapcsolódás, és beállíthatja, hogy mely ügynökök gyűjtik és naplózzák az adatokat Azure Monitor, a felügyeleti csoportban a következő konfigurációt alkalmazza a rendszer, nem feltétlenül sorrendben:

* A **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** futtató fiók létrejön. Ez a fiók a **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor futtatóprofil-blob) futtató blobhoz van társítva, és két osztályt céloz: **Gyűjtési kiszolgáló** és **Operations Manager felügyeleti csoport**.
* Két összekötő jön létre.  Az első neve **Microsoft. SystemCenter. Advisor. DataConnector** , és automatikusan olyan előfizetéssel van konfigurálva, amely a felügyeleti csoportban lévő összes osztály példányaiból generált összes riasztást továbbítja Azure monitor. A második összekötő az **Advisor Connector**, amely a Azure monitor és az adatmegosztással való kommunikációért felelős.
* A felügyeleti csoportban az adatok gyűjtésére konfigurált ügynökök és csoportok fel lesznek véve a **Microsoft System Center Advisor monitorozásikiszolgáló-csoportba**.

## <a name="management-pack-updates"></a>Felügyeleti csomagok frissítései

A konfiguráció befejezése után a Operations Manager felügyeleti csoport kapcsolatot létesít Azure Monitorval. A felügyeleti kiszolgáló szinkronizál a webszolgáltatással, és felügyeleti csomagok formájában megkapja a frissített konfigurációadatokat az Operations Managerrel való integrációra engedélyezett megoldásokra vonatkozóan. Operations Manager ellenőrzi a felügyeleti csomagok frissítéseit, és automatikusan letölti és importálja őket, amikor elérhetők. Ezt a működést kifejezetten két szabály vezérli:

* **Microsoft. SystemCenter. Advisor. MPUpdate** – frissíti az alapszintű Azure monitor felügyeleti csomagokat. Alapértelmezés szerint 12 óránként fut.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – A munkaterületen engedélyezett megoldások felügyeleti csomagjait frissíti. Alapértelmezés szerint öt (5) percenként fut.

A két szabály felülbírálásával megakadályozhatja az automatikus letöltést, ha letiltja őket, vagy módosítani szeretné, hogy a felügyeleti kiszolgáló milyen gyakran szinkronizálja Azure Monitor annak megállapítására, hogy elérhető-e új felügyeleti csomag, és le kell-e tölteni. A [Szabály vagy figyelő felülbírálása](https://technet.microsoft.com/library/hh212869.aspx) cikkben ismertetett lépések mentén módosíthatja a **Gyakoriság** paraméter másodpercben kifejezett értékét, amely a szinkronizálás ütemezését adja meg, vagy az **Engedélyezve** paraméter módosításával letilthatja a szabályokat. Ezeket a szabálymódosításokat az Operations Manager felügyeleti csoport osztályban lévő minden objektumra alkalmazza.

Ha továbbra is szeretné folytatni a felügyeleti csomagok kiadásának szabályozását az éles felügyeleti csoportban, letilthatja a szabályokat, és engedélyezheti azokat adott időpontokban a frissítések engedélyezésekor. Ha a környezetében van olyan fejlesztési vagy QA-felügyeleti csoporttal, amely tud kapcsolódni az internetre, konfigurálhatja a felügyeleti csoportot egy Log Analytics-munkaterülettel, amely támogatja ezt a forgatókönyvet. Ez lehetővé teszi, hogy áttekintse és kiértékelje a Azure Monitor felügyeleti csomagok ismétlődő kiadásait, mielőtt felszabadítja őket az éles felügyeleti csoportba.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Váltás Operations Manager csoportról egy új Log Analytics-munkaterületre

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet, és hozzon létre egy munkaterületet.  
1. Nyissa meg az Operations Manager konzolt az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal, és válassza a **Felügyelet** munkaterületet.
1. Bontsa ki a Log Analytics elemet, majd válassza a **kapcsolatok**lehetőséget.
1. Válassza az **Operation Management Suite újrakonfigurálása** hivatkozást, az ablaktábla közepén.
1. Kövesse a **log Analytics** bevezetési varázslót, és adja meg az új log Analytics-munkaterülethez társított rendszergazdai fiók e-mail-címét, telefonszámát és jelszavát.

   > [!NOTE]
   > Az **Operations Management Suite előkészítési varázslója: A munkaterület** kiválasztása oldalon megadhatja a meglévő munkaterületet, amely használatban van.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Operations Manager integráció ellenőrzése Azure Monitor

Néhány különböző módon ellenőrizheti, hogy a Azure Monitor Operations Manager integráció sikeres volt-e.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Integráció megerősítése az Azure Portalról

1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket.
1. A Log Analytics-munkaterületek listájában válassza ki a vonatkozó munkaterületet.  
1. Válassza a **Speciális beállítások**, a **Csatlakoztatott Források**, majd a **System Center** elemet.
1. A System Center Operations Manager szakaszában található táblázatban megjelenik a felügyeleti csoport neve, az ügynökök számával együtt, valamint az utolsó adatok beérkezésének állapota.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Integráció megerősítése az Operatív konzolról

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Kattintson a **Felügyeleti csomagok** elemre, és a **Keresés:** szövegmezőbe írja be az **Advisor** vagy az **Intelligence** kifejezést.
1. Ekkor a keresési eredményekben megjelenik a megfelelő felügyeleti csomag, attól függően, hogy mely megoldások lettek engedélyezve.  Ha például az Alert Management megoldást engedélyezte, akkor a listában a Microsoft System Center Advisor Riasztáskezelés csomag szerepel.
1. A **Monitorozás** nézetből nyissa meg az **Operations Management Suite/Állapot** nézetet.  Jelöljön ki egy felügyeleti kiszolgálót a **Felügyeleti kiszolgáló állapota** ablaktáblán, majd a **Részletes nézet** ablaktáblán erősítse meg, hogy a **Hitelesítési kiszolgálói URI** tulajdonság értéke megfelel-e a Log Analytics-munkaterület azonosítójának.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Azure Monitor integrációjának eltávolítása

Ha már nincs szüksége az Operations Manager felügyeleti csoport és a Log Analytics-munkaterület közötti integrációra, több lépést is el kell végeznie a kapcsolat és a konfiguráció a felügyeleti csoportban való megfelelő eltávolításának érdekében. Az alábbi eljárással frissítheti Log Analytics munkaterületét a felügyeleti csoport hivatkozásának törlésével, törölheti a Azure Monitor összekötőket, majd törölheti a szolgáltatással való integrációt támogató felügyeleti csomagokat.  

A Operations Manager és az Azure Monitor integrációjának támogatásához szükséges felügyeleti csomagokat nem lehet egyszerűen törölni a felügyeleti csoportból a felügyeleti csomagokba. Ennek az az oka, hogy a Azure Monitor felügyeleti csomagjainak némelyike más kapcsolódó felügyeleti csomagokkal is függőségekkel rendelkezik. Ha törölni szeretné a más felügyeleti csomagoktól függő felügyeleti csomagokat, töltse le a [függőséggel rendelkező felügyeleti csomagok eltávolítására](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) szolgáló szkriptet a TechNet Script Centerből.  

1. Nyissa meg az Operations Manager parancsrendszerhéjat az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.

    > [!WARNING]
    > A folytatás előtt győződjön meg arról, hogy nem rendelkezik olyan egyéni felügyeleti csomagokkal, amelyek nevében szerepel az Advisor vagy az IntelligencePack szó, mert ha vannak ilyenek, azok is törlődni fognak a következő lépés során a felügyeleti csoportból.
    >

1. A parancsrendszerhéjban írja be a következőt: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Ezután írja be a következőt: `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Olyan felügyeleti csomagok eltávolításához, amelyek más System Center Advisor felügyeleti csomagoktól függnek, használja a *RecursiveRemove.ps1* szkriptet, amelyet még a TechNet Script Centerből töltött le.  

    > [!NOTE]
    > Az Advisor felügyeleti csomagjainak PowerShell-lel való eltávolításának lépése nem törli automatikusan a Microsoft System Center Advisor vagy a Microsoft System Center Advisor belső felügyeleti csomagjait.  Ne próbálja meg törölni őket.  
    >  

1. Nyissa meg az Operations Manager Operatív konzolját az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.
1. A **Felügyelet** területen válassza a **Felügyeleti csomagok** csomópontot, majd a **Keresés:** mezőbe gépelje be az **Advisor** szót, és győződjön meg arról, hogy az alábbi felügyeleti csomagok továbbra is importálódnak felügyeleti csoportjába:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. A Azure Portal kattintson a **Beállítások** csempére.
1. Válassza a **csatlakoztatott források**lehetőséget.
1. A System Center Operations Manager szakaszban található táblázatban látnia kell annak a felügyeleti csoportnak a nevét, amelyet el szeretne távolítani a munkaterületről. A **Legutóbbi adatok** oszlop alatt kattintson az **Eltávolítás** elemre.  

    > [!NOTE]
    > Az **Eltávolítás** hivatkozás csak 14 nap múlva válik elérhetővé, ha nem észlelhető aktivitás a csatlakoztatott felügyeleti csoportban.  
    >

1. Ezután megjelenik egy ablak, amely arra kéri, hogy erősítse meg eltávolítási szándékát.  A továbblépéshez kattintson az **Igen** gombra.

Ha törölni szeretné a két összekötőt (a Microsoft.SystemCenter.Advisor.DataConnectort és az Advisor-összekötőt), mentse számítógépére az alábbi PowerShell-szkriptet, és hajtsa végre az alábbi példák segítségével:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> A számítógépen, amelyről a szkriptet futtatja, ha nem felügyeleti kiszolgáló, telepítve kell lennie az Operations Manager-parancsrendszerhéjnak, a felügyeleti csoport verziójától függően.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Ha a felügyeleti csoport log Analytics munkaterületre való újrakapcsolódását tervezi, akkor a `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` felügyeleti csomag fájlját újra kell importálnia. A környezetében telepített System Center Operations Manager verziójától függően a fájlt a következő helyen találja:

* A forrás-adathordozón a `\ManagementPacks` System Center 2016 – Operations Manager mappában vagy felette.
* A felügyeleti csoporton alkalmazott legújabb kumulatív frissítésben. A Operations Manager 2012, a forrás mappa `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` és a 2012 R2 esetében a következő helyen található:. `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`

## <a name="next-steps"></a>További lépések

A funkciók hozzáadásával és az adatok összegyűjtésével kapcsolatban lásd: [Azure monitor megoldások hozzáadása a Solutions Gallery](../../azure-monitor/insights/solutions.md).
