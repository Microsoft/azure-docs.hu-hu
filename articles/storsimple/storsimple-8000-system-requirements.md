---
title: StorSimple 8000 sorozat rendszerkövetelményei | Microsoft Docs
description: Ismerteti a szoftverekkel, a hálózattal és a magas rendelkezésre állással kapcsolatos követelményeket, valamint a Microsoft Azure StorSimple megoldás ajánlott eljárásait.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: fa7616a740e8246fa08e950494428095f41ee404
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382854"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000-es sorozatú szoftverek, magas rendelkezésre állás és hálózati követelmények

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Üdvözli a Microsoft Azure StorSimple. Ez a cikk a StorSimple-eszközre vonatkozó fontos rendszerkövetelményeket és ajánlott eljárásokat, valamint az eszközt elérő Storage-ügyfeleket ismerteti. Javasoljuk, hogy a StorSimple rendszer üzembe helyezése előtt gondosan tekintse át az információkat, majd az üzembe helyezés és az azt követő művelet során szükség szerint küldje vissza az adatokat.

A rendszerkövetelmények a következők:

* A **Storage-ügyfelek szoftveres követelményei** – a támogatott operációs rendszereket és az operációs rendszerekre vonatkozó további követelményeket ismerteti.
* **A StorSimple eszköz hálózati követelményei** – információt nyújt azokról a portokról, amelyeket meg kell nyitni a tűzfalon az iSCSI-, felhő-vagy felügyeleti forgalom engedélyezéséhez.
* **Magas rendelkezésre állási követelmények a StorSimple** – a StorSimple-eszköz és a gazdaszámítógép magas rendelkezésre állási követelményeit és ajánlott eljárásait ismerteti.

## <a name="software-requirements-for-storage-clients"></a>A Storage-ügyfelek szoftveres követelményei

A StorSimple-eszközt elérő Storage-ügyfelekre a következő szoftver-követelmények vonatkoznak.

| Támogatott operációs rendszerek | Szükséges verzió | További követelmények/megjegyzések |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |A StorSimple iSCSI-kötetek használata csak a következő Windows-lemezeken támogatott:<ul><li>Egyszerű kötet az alapszintű lemezen</li><li>Egyszerű és tükrözött kötet dinamikus lemezen</li></ul>Csak az operációs rendszer natív módon elérhető szoftveres iSCSI-kezdeményezői támogatottak. A hardveres iSCSI-kezdeményezők nem támogatottak.<br></br>A Windows Server 2012 és a 2016 dinamikus kiosztású és ODX funkciók támogatottak, ha StorSimple iSCSI-kötetet használ.<br><br>A StorSimple dinamikusan kiosztott és teljesen kiosztott köteteket hozhat létre. Nem lehet részben kiosztott köteteket létrehozni.<br><br>A dinamikusan kiosztott kötetek újraformázása hosszú időt vehet igénybe. Javasoljuk, hogy az újraformázás helyett törölje a kötetet, majd hozzon létre egy újat. Ha azonban továbbra is szeretne újraformázni egy kötetet:<ul><li>Az újraformázás előtt futtassa az alábbi parancsot, amellyel elkerülheti a terület-visszaigénylés késleltetését:  <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>A formázás végeztével használja az alábbi parancsot a terület-visszaigénylés engedélyezéséhez: <br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Alkalmazza a Windows Server 2012 gyorsjavítást a [KB 2878635](https://support.microsoft.com/kb/2870270) -ben leírt módon a Windows Server rendszerű számítógépére.</li></ul></li></ul></ul> Ha StorSimple Snapshot Manager vagy StorSimple-adaptert szeretne konfigurálni a SharePointhoz, lépjen a [választható összetevőkhöz tartozó szoftverekre vonatkozó követelmények](#software-requirements-for-optional-components)közül. <br> Ha a Windows Server-ügyfél az SMB protokollt használja a StorSimple-eszköz eléréséhez, ugorjon az [SMB-fájlkiszolgálók teljesítmény-finomhangolása](/windows-server/administration/performance-tuning/role/file-server/smb-file-server) elemre a párhuzamos feldolgozás növelésével kapcsolatos útmutatásért.|
| VMware ESX |5,5 és 6,0 |A VMware vSphere iSCSI-ügyfélként támogatott. A VAAI funkció VMware vSphere StorSimple-eszközökön támogatott. |
| Linux RHEL/CentOS |5, 6 és 7 |A Linux iSCSI-ügyfelek támogatása az Open-iSCSI-kezdeményezővel 5, 6 és 7 verziót biztosít. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> Az IBM AIX jelenleg nem támogatott a StorSimple.


## <a name="software-requirements-for-optional-components"></a>A választható összetevők szoftveres követelményei

A következő szoftverek követelményei a választható StorSimple-összetevők (StorSimple Snapshot Manager és StorSimple-adapter a SharePointhoz).

| Összetevő | Gazda platform | További követelmények/megjegyzések |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |A StorSimple Snapshot Manager Windows Serveren való használata a tükrözött dinamikus lemezek biztonsági mentéséhez és visszaállításához, valamint az alkalmazások által konzisztens biztonsági mentésekhez szükséges.<br> A StorSimple Snapshot Manager csak Windows Server 2008 R2 SP1 (64 bites), Windows Server 2012 R2 és Windows Server 2012 rendszeren támogatott.<ul><li>Ha a Windows Server 2012-et használja, telepítenie kell a .NET 3.5 – 4.5-ös verzióját, mielőtt telepítené a StorSimple Snapshot Manager.</li><li>Ha a Windows Server 2008 R2 SP1 rendszert használja, a StorSimple Snapshot Manager telepítése előtt telepítenie kell a Windows Management Framework 3,0-es verzióját.</li></ul> |
| SharePointhoz készült StorSimple-adapter |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>A SharePointhoz készült StorSimple-adapter csak a SharePoint 2010 és a SharePoint 2013 esetén támogatott.</li><li>Az RBS működéséhez SQL Server Enterprise Edition, 2008 R2 vagy 2012 verzió szükséges.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>A StorSimple-eszköz hálózati követelményei

A StorSimple-eszköz egy zárolt eszköz. A tűzfalon azonban meg kell nyitni a portokat az iSCSI-, a felhő-és a felügyeleti forgalom engedélyezéséhez. A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon. A (z)  vagy a *bejövő* tábla a bejövő ügyfelek által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimenő* állapot arra utal, hogy a StorSimple-eszköz hogyan küldi *el az* adatokat külsőleg, az üzembe helyezésen kívül: például az internet felé.

| Portszám:<sup>1, 2</sup> | Be vagy ki | Port hatóköre | Kötelező | Jegyzetek |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Ki |WAN |No |<ul><li>A rendszer a kimenő portot használja az internet-hozzáféréshez a frissítések lekéréséhez.</li><li>A kimenő webes proxy a felhasználó által konfigurálható.</li><li>A rendszerfrissítések engedélyezéséhez ezt a portot is meg kell nyitni a vezérlő rögzített IP-címei számára.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Ki |WAN |Yes |<ul><li>A kimenő port a felhőben tárolt adatok elérésére szolgál.</li><li>A kimenő webes proxy a felhasználó által konfigurálható.</li><li>A rendszerfrissítések engedélyezéséhez ezt a portot is meg kell nyitni a vezérlő rögzített IP-címei számára.</li><li>Ezt a portot is használja a rendszer az adattárolók begyűjtésére szolgáló vezérlőn is.</li></ul> |
| UDP 53 (DNS) |Ki |WAN |Bizonyos esetekben; Lásd: megjegyzések. |Erre a portra csak akkor van szükség, ha Internet alapú DNS-kiszolgálót használ. |
| UDP 123 (NTP) |Ki |WAN |Bizonyos esetekben; Lásd: megjegyzések. |Erre a portra csak akkor van szükség, ha Internet-alapú NTP-kiszolgálót használ. |
| TCP 9354 |Ki |WAN |Yes |A StorSimple eszköz a kimenő portot használja a StorSimple Eszközkezelő szolgáltatással való kommunikációhoz. |
| 3260 (iSCSI) |In |LAN |No |Ez a port az iSCSI-kapcsolaton keresztüli adateléréshez használatos. |
| 5985 |In |LAN |No |A StorSimple Snapshot Manager a bejövő portot használja a StorSimple eszközzel való kommunikációhoz.<br>Ezt a portot akkor is használja a rendszer, amikor távolról csatlakozik a Windows PowerShell StorSimple-bővítménye HTTP-n keresztül. |
| 5986 |In |LAN |No |Ezt a portot akkor használja a rendszer, amikor távolról csatlakozik Windows PowerShell StorSimple-bővítménye HTTPS-kapcsolaton keresztül. |

<sup>1</sup> nem szükséges bejövő portot megnyitni a nyilvános interneten.

<sup>2</sup> ha több port hajtja végre az átjáró konfigurációját, a rendszer a kimenő útvonalon megadott forgalmi sorrendet az alábbi [port Routing (portok útválasztása](#routing-metric)) című részében ismertetett port útválasztási sorrend alapján határozza meg.

<sup>3</sup> a vezérlő rögzített IP-címeinek irányíthatónak kell lennie a StorSimple-eszközön, és képesnek kell lennie arra, hogy közvetlenül vagy a konfigurált webproxyn keresztül csatlakozhasson az internethez. A rögzített IP-címek az eszköz frissítéseinek karbantartásához és a Garbage-gyűjtéshez használatosak. Ha az eszközök nem tudnak csatlakozni az internethez a rögzített IP-címeken keresztül, akkor nem fogja tudni frissíteni a StorSimple-eszközt, és a szemetet tartalmazó gyűjtemény nem fog megfelelően működni.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosítja vagy visszafejti a StorSimple-eszköz és az Azure közötti TLS-forgalmat.


### <a name="url-patterns-for-firewall-rules"></a>Tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatják a speciális tűzfalszabályok alapján a bejövő és a kimenő forgalom szűrésére szolgáló URL-mintákat. A StorSimple-eszköz és a StorSimple Eszközkezelő szolgáltatás más Microsoft-alkalmazásokkal (például Azure Service Bus, Azure Active Directory Access Control, Storage-fiókokkal és Microsoft Update-kiszolgálókkal függ. Az ezekhez az alkalmazásokhoz társított URL-mintákat a tűzfalszabályok konfigurálására lehet használni. Fontos megérteni, hogy az alkalmazásokhoz társított URL-minták megváltoztathatók. Ehhez a hálózati rendszergazdának meg kell figyelnie és frissítenie kell a StorSimple vonatkozó tűzfalszabályok beállításait a és ha szükséges.

Javasoljuk, hogy a legtöbb esetben a StorSimple rögzített IP-címek alapján állítsa be a tűzfal szabályait a kimenő forgalomra vonatkozóan. Az alábbi információk segítségével azonban megadhatja a biztonságos környezetek létrehozásához szükséges speciális tűzfalszabályok beállításait is.

> [!NOTE]
> Az eszköz (forrás) IP-címeit mindig az összes engedélyezett hálózati adapterre kell beállítani. A cél IP-címeket az [Azure Datacenter IP-címtartományok](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)értékre kell beállítani.


#### <a name="url-patterns-for-azure-portal"></a>A Azure Portal URL-mintái

| URL-minta | Összetevő/funkció | Eszköz IP-címei |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple-eszközkezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás |Felhőalapú hálózati adapterek |
| `https://*.backup.windowsazure.com` |Eszközregisztráció |Csak az adat0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány visszavonása |Felhőalapú hálózati adapterek |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-fiókok és-figyelés |Felhőalapú hálózati adapterek |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Kiszolgálók Microsoft Update<br> |Csak a vezérlő rögzített IP-címei |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Csak a vezérlő rögzített IP-címei |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |Felhőalapú hálózati adapterek |

#### <a name="url-patterns-for-azure-government-portal"></a>Azure Government portál URL-mintái

| URL-minta | Összetevő/funkció | Eszköz IP-címei |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple-eszközkezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás |Felhőalapú hálózati adapterek |
| `https://*.backup.windowsazure.us` |Eszközregisztráció |Csak az adat0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány visszavonása |Felhőalapú hálózati adapterek |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-fiókok és-figyelés |Felhőalapú hálózati adapterek |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Kiszolgálók Microsoft Update<br> |Csak a vezérlő rögzített IP-címei |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Csak a vezérlő rögzített IP-címei |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |Felhőalapú hálózati adapterek |

### <a name="routing-metric"></a>Útválasztási metrika

Az útválasztási metrika társítva van az interfészekhez és az átjáróhoz, amely az adatokat a megadott hálózatokra irányítja. Az útválasztási metrikát az útválasztási protokoll használja az adott célhelyhez tartozó legjobb elérési út kiszámításához, ha több elérési út is létezik ugyanarra a célhelyre. Minél alacsonyabb az útválasztási metrika, annál magasabb a preferencia.

A StorSimple kontextusában, ha több hálózati adapter és átjáró van konfigurálva a forgalom továbbítására, az útválasztási mérőszámok lejátszásra kerülnek, hogy meghatározzák azt a relatív sorrendet, amelyben a rendszer használni fogja a csatolókat. A felhasználó nem módosíthatja az útválasztási metrikákat. A parancsmag segítségével azonban `Get-HcsRoutingTable` kinyomtathatja az útválasztási táblázatot (és metrikákat) a StorSimple-eszközön. További információ a Get-HcsRoutingTable parancsmagról a [StorSimple telepítésének hibaelhárítása című](./storsimple-8000-troubleshoot-deployment.md)témakörben.

A 2. frissítéshez és újabb verziókhoz használt útválasztási metrikai algoritmust a következő módon lehet megmagyarázni.

* Előre meghatározott értékek halmaza lett hozzárendelve a hálózati adapterekhez.
* Vegye fontolóra az alábbi példában szereplő táblázatot a különböző hálózati adapterekhez rendelt értékekkel, ha azok felhőalapú vagy felhőalapú, de konfigurált átjáróval rendelkeznek. Figyelje meg, hogy az itt hozzárendelt értékek csak példa értékeket tartalmaznak.

    | Hálózati adapter | Felhőben engedélyezve | Felhő – letiltva az átjáróval |
    |-----|---------------|---------------------------|
    | 0. adatérték  | 1            | -                        |
    | 1. adatérték  | 2            | 20                       |
    | 2. adatérték  | 3            | 30                       |
    | 3. adattábla  | 4            | 40                       |
    | 4. adatkezelés  | 5            | 50                       |
    | 5. adatkezelés  | 6            | 60                       |


* A Felhőbeli forgalom a hálózati adaptereken keresztüli továbbításának sorrendje a következőket eredményezi:
  
    *Adat 0 > adat 1 > dátum 2 > adat 3 > adat 4 > adat 5*
  
    Ezt az alábbi példa is ismerteti.
  
    Vegyünk fel egy StorSimple-eszközt két felhőalapú hálózati csatolóval, a 0. adatértékkel és az 5. adattal. Az 1 – 4. adatértékek a felhőben le vannak tiltva, de konfigurált átjáróval rendelkeznek. Az eszközre irányuló forgalom átirányításának sorrendje a következő lesz:
  
    *Adatértékek 0 (1) > adat> (6) az 1. (20.) adat>i adat>i (30) adat>i adat (40) 4 (50)*
  
    *A zárójelben lévő számok a megfelelő útválasztási mérőszámokat jelölik.*
  
    Ha a 0. adat sikertelen, a Felhőbeli forgalom az 5. adaton keresztül lesz átirányítva. Mivel az átjáró minden más hálózaton konfigurálva van, ha mind a 0, mind az 5. adat sikertelen volt, a Felhőbeli forgalom az 1. adaton halad át.
* Ha egy felhőalapú hálózati adapter meghibásodik, akkor a rendszer 3 újrapróbálkozik a csatolóhoz való csatlakozás 30 másodperces késleltetésével. Ha az összes újrapróbálkozás sikertelen, a rendszer átirányítja a forgalmat a következő elérhető felhőalapú felületre az útválasztási táblázat alapján. Ha az összes felhőalapú hálózati adapter meghibásodik, akkor az eszköz feladatátvételt hajt végre a másik vezérlőn (ebben az esetben nincs szükség újraindításra).
* Ha egy iSCSI-kompatibilis hálózati adapter esetében VIP-hiba történik, a rendszer 3 újrapróbálkozást végez, 2 másodperces késleltetéssel. Ez a viselkedés az előző kiadásokban is megmaradt. Ha az összes iSCSI hálózati adapter meghibásodik, akkor a rendszer egy vezérlő feladatátvételt hajt végre (újraindítással együtt).
* A rendszer riasztást küld a StorSimple-eszközön is, ha VIP-hiba történik. További információkért tekintse meg a [riasztás gyors leírása](storsimple-8000-manage-alerts.md)című témakört.
* Az újrapróbálkozások tekintetében az iSCSI elsőbbséget élvez a felhővel szemben.
  
    Vegye figyelembe a következő példát: egy StorSimple-eszközön két hálózati adapter van engedélyezve, az adat0 és az 1. adategység. A 0. adat felhőben engedélyezett, míg az 1. adat Felhőbeli és iSCSI-kompatibilis. Az eszközön nincs más hálózati adapter engedélyezve a felhőben vagy az iSCSI-ben.
  
    Ha az 1. adat meghiúsul, mivel ez az utolsó iSCSI hálózati adapter, akkor a vezérlő feladatátvételt hajt végre a másik vezérlőn lévő 1. adatban.

### <a name="networking-best-practices"></a>Hálózatkezelés – ajánlott eljárások

A fenti hálózati követelmények mellett a StorSimple-megoldás optimális teljesítményéhez az alábbi ajánlott eljárásoknak kell megfelelnie:

* Győződjön meg arról, hogy a StorSimple-eszközön dedikált 40 Mbps sávszélesség (vagy több) érhető el. Ez a sávszélesség nem osztható meg (vagy a foglalást a QoS-házirendek használatával kell garantálni) más alkalmazásokkal.
* Győződjön meg arról, hogy a hálózati kapcsolat mindig elérhető az internethez. Az eszközök szórványos vagy megbízhatatlan internetkapcsolata, beleértve az internetkapcsolat nélküli kapcsolatot, nem támogatott konfigurációt eredményez.
* Az iSCSI és a felhőalapú forgalom elkülönítéséhez dedikált hálózati adapterek szükségesek az eszközön az iSCSI-és a felhőalapú hozzáféréshez. További információ: a [hálózati adapterek módosítása](storsimple-8000-modify-device-config.md#modify-network-interfaces) a StorSimple-eszközön.
* Ne használjon LACP-konfigurációt a hálózati adapterekhez. Ez egy nem támogatott konfiguráció.

## <a name="high-availability-requirements-for-storsimple"></a>Magas rendelkezésre állási követelmények a StorSimple

A StorSimple-megoldás részét képező hardver platform olyan rendelkezésre állási és megbízhatósági funkciókkal rendelkezik, amelyek alapot biztosítanak az adatközpontban található, nagy rendelkezésre állású, hibatűrő tárolási infrastruktúrához. Vannak azonban olyan követelmények és ajánlott eljárások, amelyeket be kell tartania a StorSimple-megoldás rendelkezésre állásának biztosítása érdekében. A StorSimple telepítése előtt alaposan tekintse át az alábbi követelményeket és ajánlott eljárásokat a StorSimple-eszközhöz és a csatlakoztatott gazdagépekhez.

A StorSimple-eszköz hardveres összetevőinek figyelésével és karbantartásával kapcsolatos további információkért látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a hardver összetevőinek és állapotának](storsimple-8000-monitor-hardware-status.md) , valamint a [StorSimple hardveres összetevők cseréjének](storsimple-8000-hardware-component-replacement.md)figyeléséhez.

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Magas rendelkezésre állási követelmények és eljárások a StorSimple-eszközhöz

A StorSimple-eszköz magas rendelkezésre állásának biztosítása érdekében alaposan tekintse át a következő információkat.

#### <a name="pcms"></a>PCMs

A StorSimple-eszközök közé tartoznak a redundáns, lecserélhető energiagazdálkodási és hűtési modulok (PCMs). Minden PCM rendelkezik elegendő kapacitással a teljes alváz szolgáltatás biztosításához. A magas rendelkezésre állás biztosítása érdekében mindkét PCMs telepítve kell lennie.

* A PCMs a különböző áramforrásokhoz kapcsolhatja a rendelkezésre állás biztosítása érdekében, ha az áramforrás meghibásodik.
* Ha a PCM meghibásodik, azonnal kérjen cserét.
* Csak akkor távolítson el egy sikertelen PCM-t, ha a pótlása megtörtént, és készen áll a telepítésre.
* Ne távolítsa el egyidejűleg mindkét PCMs. A PCM-modul tartalmazza a Backup akkumulátor-modult. Ha mindkét PCMs eltávolítja, akkor az akkumulátor védelme nélkül leáll, és az eszköz állapota nem lesz mentve. További információ az akkumulátorról: [a tartalék akkumulátor-modul karbantartása](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Vezérlő modulok

A StorSimple-eszközök közé tartozik a redundáns, lecserélhető vezérlő modulok. A vezérlő modulok aktív/passzív módon működnek. Az egyik vezérlő modul egy adott időpontban aktív, és biztosítja a szolgáltatást, míg a másik vezérlő modul passzív. A passzív vezérlő modul be van kapcsolva, és működőképes lesz, ha az aktív vezérlő modul meghibásodik vagy el lett távolítva. Minden vezérlő modul rendelkezik elegendő kapacitással a teljes alváz szolgáltatás biztosításához. A magas rendelkezésre állás biztosítása érdekében mindkét vezérlő modult telepíteni kell.

* Győződjön meg arról, hogy mindkét vezérlő modul telepítve van.
* Ha egy vezérlő modul meghibásodik, azonnal kérjen cserét.
* Csak akkor távolíthat el egy sikertelen vezérlő modult, ha a pótlása megtörtént, és készen áll a telepítésre. Ha eltávolít egy modult a hosszabb időszakokra, az hatással lesz a légáramlásra, és így a rendszer hűtésére.
* Győződjön meg arról, hogy a vezérlő modulok hálózati kapcsolatai azonosak, és a csatlakoztatott hálózati adapterek azonos hálózati konfigurációval rendelkeznek.
* Ha egy vezérlő modul meghibásodik vagy cserére szorul, győződjön meg arról, hogy a másik vezérlő modul aktív állapotban van a sikertelen vezérlő moduljának cseréje előtt. Ha ellenőrizni szeretné, hogy a vezérlő aktív-e, nyissa [meg az eszközön az aktív vezérlőt](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Ne távolítsa el egyszerre mindkét vezérlő modult. Ha a vezérlő feladatátvétele folyamatban van, ne állítsa le a készenléti vezérlő modulját, vagy távolítsa el az alvázról.
* A vezérlő feladatátvétele után várjon legalább öt percet, mielőtt eltávolítja bármelyik vezérlő modult.

#### <a name="network-interfaces"></a>Hálózati adapterek

A StorSimple-vezérlő moduljai 4 1 Gigabit és 2 10 Gigabit Ethernet hálózati adapterrel rendelkeznek.

* Győződjön meg arról, hogy a vezérlő modulok hálózati kapcsolatai azonosak, és hogy a vezérlő modul felületéhez kapcsolódó hálózati adapterek azonos hálózati konfigurációval rendelkeznek.
* Ha lehetséges, telepítsen hálózati kapcsolatokat különböző kapcsolókon, hogy a szolgáltatás rendelkezésre állása hálózati eszköz meghibásodása esetén is megtörténjen.
* Az egyetlen vagy az utolsó fennmaradó iSCSI-kompatibilis felület leválasztása (IP-címek hozzárendelése esetén) esetében először tiltsa le a felületet, majd húzza ki a kábeleket. Ha a csatoló először le van húzva, akkor az aktív vezérlő feladatátvételt hajt végre a passzív vezérlőn. Ha a passzív vezérlő a hozzá tartozó csatolókat is leválasztja, akkor a vezérlők egyszerre többször is újraindulnak az egyik vezérlőre való rendezés előtt.
* Csatlakoztasson legalább két adatkapcsolatot a hálózathoz az egyes vezérlő moduloktól.
* Ha engedélyezte a 2 10 GbE felületét, telepítse a különböző kapcsolókon keresztül.
* Ha lehetséges, az MPIO-t használja a kiszolgálókon annak biztosítása érdekében, hogy a kiszolgálók el tudják viselni a kapcsolat, a hálózat vagy az interfész meghibásodását.

További információ az eszköz magas rendelkezésre álláshoz és teljesítményhez való hálózatkezeléséről: [telepítse a StorSimple 8100 eszközt](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) , vagy [telepítse a StorSimple 8600 eszközét](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD-k és HDD-k

A StorSimple-eszközök közé tartoznak a tükrözött szóközökkel védett SSD-k és merevlemez-meghajtók (HDD-k). A tükrözött szóközök használata biztosítja, hogy az eszköz képes legyen elviselni egy vagy több SSD vagy HDD meghibásodását.

* Győződjön meg arról, hogy az összes SSD-és HDD-modul telepítve van.
* Ha egy SSD vagy HDD meghibásodik, azonnal kérjen cserét.
* Ha egy SSD vagy HDD meghibásodik vagy csere szükséges, ügyeljen arra, hogy csak a cserét igénylő SSD-t vagy HDD-t távolítsa el.
* Ne távolítson el egynél több SSD-t vagy HDD-t a rendszerből egy adott időpontban.
  Ha a rövid időn belül 2 vagy több lemez (HDD, SSD) vagy egymást követő hiba miatt sikertelen, a rendszer meghibásodik, és lehetséges az adatvesztés. Ha ez történik, [kérje a Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) segítségét.
* A csere során figyelje az SSD-k és HDD-k meghajtóinak **hardver állapota** paneljén található **megosztott összetevőket** . A zöld ellenőrzés állapota azt jelzi, hogy a lemezek kifogástalanok vagy rendben vannak, míg a vörös felkiáltójel a meghibásodott SSD-t vagy HDD-t jelzi.
* Azt javasoljuk, hogy a rendszer meghibásodása esetén minden olyan kötet esetében konfigurálja a Felhőbeli pillanatképeket, amelyet a védelemmel kell ellátnia.

#### <a name="ebod-enclosure"></a>EBOD ház

A StorSimple-eszköz 8600-es verziójában az elsődleges ház mellett egy kiterjesztett lemez (EBOD) található. A EBOD a tükrözött szóközökkel védett EBOD-vezérlőket és merevlemez-meghajtókat (HDD-ket) tartalmaz. A tükrözött szóközök használata biztosítja, hogy az eszköz képes legyen elviselni egy vagy több HDD meghibásodását. A EBOD ház a redundáns SAS-kábelek használatával csatlakozik az elsődleges bekerítéshez.

* Győződjön meg arról, hogy mind a EBOD, mind az SAS-kábelek, mind a merevlemez-meghajtók mindig telepítve vannak.
* Ha egy EBOD-bekerítés-vezérlő modul meghibásodik, azonnal kérjen cserét.
* Ha egy EBOD ház-vezérlő modul meghibásodik, győződjön meg arról, hogy a másik vezérlő modul aktív, mielőtt lecseréli a hibás modult. Ha ellenőrizni szeretné, hogy a vezérlő aktív-e, nyissa [meg az eszközön az aktív vezérlőt](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Egy EBOD-vezérlő modul cseréje során a StorSimple Eszközkezelő szolgáltatásban folyamatosan figyeli az összetevő állapotát a **figyelési**  >  **hardver állapotával**.
* Ha egy SAS-kábel meghibásodása vagy pótlása szükséges (Microsoft ügyfélszolgálata kell vennie az ilyen meghatározáshoz), ügyeljen arra, hogy csak a cserét igénylő SAS-kábelt távolítsa el.
* Ne egyidejűleg távolítsa el mindkét SAS-kábelt a rendszerből.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Magas rendelkezésre állással kapcsolatos javaslatok a gazdagép-számítógépekhez

Körültekintően tekintse át az ajánlott eljárásokat a StorSimple-eszközhöz csatlakoztatott gazdagépek magas rendelkezésre állásának biztosításához.

* Konfigurálja az StorSimple [-t két csomópontos fájlkiszolgálói fürt-konfigurációval][1]. Ha eltávolítja az egyes meghibásodási pontokat, és a gazdagép oldalon redundancia-felépítést készít, a teljes megoldás rendkívül elérhetővé válik.
* A Windows Server 2012 (SMB 3,0) szolgáltatásban elérhető folyamatos rendelkezésre állású (CA) megosztások használata a tárolási vezérlők feladatátvétele során. A fájlkiszolgáló-fürtök és a folyamatosan rendelkezésre álló megosztások Windows Server 2012-ben történő konfigurálásával kapcsolatos további információkért tekintse meg ezt a [videót bemutató](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)részt.

## <a name="next-steps"></a>Következő lépések

* [A StorSimple rendszerkorlátainak megismerése](storsimple-8000-limits.md).
* [Ismerje meg, hogyan helyezheti üzembe a StorSimple-megoldást](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)