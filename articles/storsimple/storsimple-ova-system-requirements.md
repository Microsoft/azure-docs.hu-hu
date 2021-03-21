---
title: Microsoft Azure StorSimple virtuális tömb rendszerkövetelményei
description: A StorSimple virtuális tömb szoftver-és hálózati követelményeinek megismerése
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966173"
---
# <a name="storsimple-virtual-array-system-requirements"></a>A StorSimple virtuális tömb rendszerkövetelményei

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a Microsoft Azure StorSimple virtuális tömb és a tömbhöz hozzáférő Storage-ügyfelek fontos rendszerkövetelményeit. Javasoljuk, hogy a StorSimple rendszer üzembe helyezése előtt gondosan tekintse át az információkat, majd az üzembe helyezés és az azt követő művelet során szükség szerint küldje vissza az adatokat.

A rendszerkövetelmények a következők:

* A **Storage-ügyfelekkel kapcsolatos szoftverek** – ismerteti a támogatott virtualizációs platformokat, a webböngészőket, az iSCSI-kezdeményezőket, az SMB-ügyfeleket, a virtuális eszközök minimális követelményeit, valamint az adott operációs rendszerek további követelményeit.
* **A StorSimple eszköz hálózati követelményei** – információt nyújt azokról a portokról, amelyeket meg kell nyitni a tűzfalon az iSCSI-, felhő-vagy felügyeleti forgalom engedélyezéséhez.

Az ebben a cikkben közzétett StorSimple rendszerkövetelményekkel kapcsolatos információk csak a StorSimple virtuális tömbökre vonatkoznak.

* Az 8000 sorozatú eszközökhöz lépjen a [StorSimple 8000 Series eszköz rendszerkövetelményei](./storsimple-8000-system-requirements.md)között.
* Az 7000 sorozatú eszközökhöz lépjen a [StorSimple 5000-7000 Series eszköz rendszerkövetelményei](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)között.

## <a name="software-requirements"></a>Szoftverkövetelmények
A szoftverre vonatkozó követelmények tartalmazzák a támogatott webböngészőkkel, az SMB-verziókkal, a virtualizációs platformokkal és a virtuális eszközök minimális követelményeivel kapcsolatos információkat.

### <a name="supported-virtualization-platforms"></a>Támogatott virtualizációs platformok
| **Hipervizor** | **Verzió** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 és újabb verziók |
| VMware ESXi |5,0, 5,5, 6,0 és 6,5. |

> [!IMPORTANT]
> Ne telepítse a VMware-eszközöket a StorSimple virtuális tömbbe; Ez nem támogatott konfigurációt eredményez.

### <a name="virtual-device-requirements"></a>Virtuális eszközre vonatkozó követelmények
| **Összetevő** | **Követelmény** |
| --- | --- |
| Virtuális processzorok minimális száma (magok) |4 |
| Minimális memória (RAM) |8 GB <br> Fájlkiszolgálón, 8 GB-nál kevesebb, mint 2 000 000 fájl és 16 GB 2-4 millió fájl esetén|
| <sup>1</sup> . lemezterület |OPERÁCIÓSRENDSZER-lemez – 80 GB <br></br>Adatlemez – 500 GB – 8 TB |
| Hálózati adapterek minimális száma |1 |
| Internetes sávszélesség<sup>2</sup> |Minimális sávszélesség szükséges: 5 Mbps <br> Ajánlott sávszélesség: 100 Mbps <br> Az adatátviteli sebesség az Internet sávszélességének megfelelően méretezhető. Például 100 GB adat 2 nap alatt átvihető 5 Mbps-ra, ami biztonsági mentési hibákhoz vezethet, mert a napi biztonsági mentések nem fejeződnek be naponta. 100 Mbps sávszélességgel 100 GB adatforgalom 2,5 órán belül átvihető.   |

<sup>1</sup> – dinamikus kiépítve

<sup>2</sup> – a hálózati követelmények a napi adatváltozási aránytól függően változhatnak. Ha például egy eszköznek egy nap alatt 10 GB-nyi vagy több módosítást kell biztonsági mentést végeznie, akkor a napi biztonsági mentés 5 Mbps-kapcsolaton keresztül akár 4,25 órát is igénybe vehet (ha az adat nem tömöríthető vagy deduplikált).

### <a name="supported-web-browsers"></a>Támogatott webböngészők
| **Összetevő** | **Verzió** | **További követelmények/megjegyzések** |
| --- | --- | --- |
| Microsoft Edge |Legújabb verzió | |
| Internet Explorer |Legújabb verzió |Az Internet Explorer 11 tesztelése |
| Google Chrome |Legújabb verzió |Tesztelték a Chrome 46-mel |

### <a name="supported-storage-clients"></a>Támogatott Storage-ügyfelek
A következő szoftver-követelmények azon iSCSI-kezdeményezők esetében szükségesek, amelyek hozzáférnek a StorSimple virtuális tömbhöz (iSCSI-kiszolgálóként konfigurálva).

| **Támogatott operációs rendszerek** | **Szükséges verzió** | **További követelmények/megjegyzések** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |A StorSimple dinamikusan kiosztott és teljesen kiosztott köteteket hozhat létre. Nem lehet részben kiosztott köteteket létrehozni. A StorSimple iSCSI-kötetek csak a következők esetén támogatottak: <ul><li>Egyszerű kötetek a Windows alaplemezeken.</li><li>A kötetek formázásának Windows NTFS.</li> |

A következő szoftver-követelmények azokra az SMB-ügyfelekre vonatkoznak, amelyek hozzáférnek a StorSimple virtuális tömbhöz (fájlkiszolgálóként konfigurálva).

| **SMB-verzió** |
| --- |
| SMB 2. x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Ne másolja és ne tárolja a Windows titkosított fájlrendszer (EFS) által védett fájlokat a StorSimple Virtual Array fájlkiszolgáló számára; Ez nem támogatott konfigurációt eredményez.


### <a name="supported-storage-format"></a>Támogatott tárolási formátum
Csak az Azure Block blob Storage támogatott. Az oldal Blobok nem támogatottak. További információ [a blobok és a Blobok blokkolásáról](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Hálózati követelmények
A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon az iSCSI-, SMB-, felhő-vagy felügyeleti forgalom engedélyezéséhez. A (z)  vagy a *bejövő* tábla a bejövő ügyfelek által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimenő* állapot arra utal, hogy a StorSimple-eszköz hogyan küldi *el az* adatokat külsőleg, az üzembe helyezésen kívül: például az internet felé.

| **<sup>1</sup> . port** | **Be vagy ki** | **Port hatóköre** | **Kötelező** | **Megjegyzések** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Ki |WAN |No |A rendszer a kimenő portot használja az internet-hozzáféréshez a frissítések lekéréséhez. <br></br>A kimenő webes proxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS) |Ki |WAN |Yes |A kimenő port a felhőben tárolt adatok elérésére szolgál. <br></br>A kimenő webes proxy a felhasználó által konfigurálható. |
| UDP 53 (DNS) |Ki |WAN |Bizonyos esetekben; Lásd: megjegyzések. |Erre a portra csak akkor van szükség, ha Internet alapú DNS-kiszolgálót használ. <br></br> Vegye figyelembe, hogy ha egy fájlkiszolgáló üzembe helyezését végzi, javasoljuk a helyi DNS-kiszolgáló használatát. |
| UDP 123 (NTP) |Ki |WAN |Bizonyos esetekben; Lásd: megjegyzések. |Erre a portra csak akkor van szükség, ha Internet-alapú NTP-kiszolgálót használ.<br></br> Vegye figyelembe, hogy ha egy fájlkiszolgáló üzembe helyezését javasolja, javasoljuk, hogy az időt szinkronizálja Active Directory tartományvezérlőkkel. |
| TCP 80 (HTTP) |In |LAN |Yes |Ez a bejövő port helyi felhasználói felület esetén a helyi felügyelethez a StorSimple eszközön. <br></br> Vegye figyelembe, hogy a helyi felhasználói felület HTTP protokollon keresztüli elérése automatikusan a HTTPS-re lesz átirányítva. |
| TCP 443 (HTTPS) |In |LAN |Yes |Ez a bejövő port helyi felhasználói felület esetén a helyi felügyelethez a StorSimple eszközön. |
| TCP 3260 (iSCSI) |In |LAN |No |Ez a port az iSCSI-kapcsolaton keresztüli adateléréshez használatos. |

<sup>1</sup> nem szükséges bejövő portot megnyitni a nyilvános interneten.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosítja vagy visszafejti a StorSimple-eszköz és az Azure közötti TLS-forgalmat.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Tűzfalszabályok URL-mintái
A hálózati rendszergazdák gyakran konfigurálhatják a speciális tűzfalszabályok alapján a bejövő és a kimenő forgalom szűrésére szolgáló URL-mintákat. A virtuális tömb és a StorSimple Eszközkezelő szolgáltatás más Microsoft-alkalmazásokkal (például Azure Service Bus, Azure Active Directory Access Control, Storage-fiókokkal és Microsoft Update-kiszolgálókkal függ. Az ezekhez az alkalmazásokhoz társított URL-mintákat a tűzfalszabályok konfigurálására lehet használni. Fontos megérteni, hogy az alkalmazásokhoz társított URL-minták megváltoztathatók. Ehhez a hálózati rendszergazdának meg kell figyelnie és frissítenie kell a StorSimple vonatkozó tűzfalszabályok beállításait a és ha szükséges. 

Javasoljuk, hogy a legtöbb esetben a StorSimple rögzített IP-címek alapján állítsa be a tűzfal szabályait a kimenő forgalomra vonatkozóan. Az alábbi információk segítségével azonban megadhatja a biztonságos környezetek létrehozásához szükséges speciális tűzfalszabályok beállításait is.

> [!NOTE]
> 
> * Az eszköz (forrás) IP-címeit mindig az összes felhőalapú hálózati adapterre kell beállítani. 
> * A cél IP-címeket az [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653)értékre kell beállítani.
> 
> 

| URL-minta | Összetevő/funkció |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple-eszközkezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás|
| `http://*.backup.windowsazure.com` |Eszközregisztráció |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány visszavonása |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-fiókok és-figyelés |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Kiszolgálók Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Támogatási csomag |
| `https://*.data.microsoft.com` |Telemetria szolgáltatás a Windowsban: [frissítés a felhasználói élmény és a diagnosztikai telemetria](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Következő lépések
* [A portál előkészítése a StorSimple virtuális tömb üzembe helyezéséhez](storsimple-virtual-array-deploy1-portal-prep.md)