---
title: A Defender for IoT parancssori felületi parancsainak használata
description: Ez a cikk az érzékelőkre és a helyszíni felügyeleti konzolokra vonatkozó IoT CLI-parancsait ismerteti.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 93efc89722d3152d92b6f8c8038deaa566741f7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636560"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>A Defender for IoT parancssori felületi parancsainak használata

Ez a cikk az érzékelők és a helyszíni felügyeleti konzolok CLI-parancsait ismerteti. A parancsok a következő felhasználók számára érhetők el:

- Rendszergazda
- CyberX 
- Támogatás

A CLI-vel való munkavégzés megkezdéséhez kapcsolódjon egy terminál használatával. Például: terminál neve `Putty` és `Support` felhasználó. 

## <a name="create-local-alert-exclusion-rules"></a>Helyi riasztás kizárási szabályainak létrehozása

A következő parancsnak a CLI-be történő beírásával létrehozhat egy helyi riasztás kizárási szabályát:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

A következő attribútumok használhatók a riasztás kizárási szabályainak használatával:

| Attribútum | Leírás |
|--|--|
| [-h] | A parancshoz tartozó súgó adatainak kinyomtatása. |
| -n név | A létrehozandó szabály neve. |
| [-TS TIMES] | Az az időtartomány, amelyre a szabály aktív. Ezt a következőképpen kell megadni:<br />`xx:yy-xx:yy`<br />Több időszakot is megadhat a közöttük lévő vesszők használatával. Példa: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir irány] | A szabály alkalmazásának iránya. Ezt a következőképpen kell megadni:<br />`both | src | dst` |
| [-dev-eszközök] | A szabály által kizárandó eszközök IP-címe és címe, a következőként megadva:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a RIASZTÁSok] | A szabály által kizárni kívánt riasztás neve:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Helyi riasztás kizárási szabályainak hozzáfűzése

A helyi riasztás kizárási szabályait a parancssori felület következő parancsának beírásával lehet hozzáfűzni:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Az itt használt attribútumok megegyeznek a helyi riasztás kizárási szabályok létrehozása szakaszban ismertetett attribútumokkal. A használat különbsége, hogy itt az attribútumok a meglévő szabályokra érvényesek.

## <a name="show-local-alert-exclusion-rules"></a>Helyi riasztás kizárási szabályainak megjelenítése

Adja meg a következő parancsot a kizárási szabályok meglévő listájának bemutatásához:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Helyi riasztás kizárási szabályainak törlése

A következő parancs megadásával törölheti a meglévő riasztás kizárási szabályát:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

A következő attribútum használható a riasztás kizárási szabályainak használatával:

| Attribútum | Leírás|
| --------- | ---------------------------------- |
| -n név | A törlendő szabály neve. |

## <a name="sync-time-from-the-ntp-server"></a>Szinkronizálási idő az NTP-kiszolgálóról

Engedélyezheti vagy letilthatja a megadott NTP-kiszolgálóról történő időszinkronizálást.

### <a name="enable-ntp-sync"></a>NTP-szinkronizálás engedélyezése

Adja meg a következő parancsot, hogy rendszeres időközönként lekérje az időpontot a megadott NTP-kiszolgálóról:

```azurecli-interactive
ntp enable IP
```

A parancson belül megadható attribútum az NTP-kiszolgáló IP-címe.

### <a name="disable-ntp-sync"></a>NTP-szinkronizálás letiltása

Adja meg a következő parancsot az idő szinkronizálásának letiltásához a megadott NTP-kiszolgálóval:

```azurecli-interactive
ntp disable IP
```

A parancson belül megadható attribútum az NTP-kiszolgáló IP-címe.

## <a name="network-configuration"></a>Hálózati konfiguráció

A következő táblázat a IoT Azure Defender hálózati beállításainak konfigurálásához elérhető parancsokat ismerteti:

|Name|Parancs|Leírás|
|-----------|-------|-----------|
|Ping|`ping IP`| Egy IoT platformon kívüli címek pingelése.|
|Blink|`network blink`| Keresse meg a kapcsolatot úgy, hogy az interfész jelzõfényei villognak. |
|A hálózat újrakonfigurálása |`network edit-settings`| A hálózati konfigurációs paraméterek módosításának engedélyezése. |
|Hálózati beállítások megjelenítése |`network list`|Megjeleníti a hálózati adapter paramétereit. |
|A hálózati konfiguráció ellenőrzése |`network validate` |Megadja a kimeneti hálózati beállításokat. <br /> <br />Például: <br /> <br />Aktuális hálózati beállítások: <br /> illesztő: ETH0 <br /> IP: 10.100.100.1 <br />alhálózat: 255.255.255.0 <br />alapértelmezett átjáró: 10.100.100.254 <br />DNS: 10.100.100.254 <br />figyelő felületek: eth1|
|Tanúsítvány importálása |`certificate import FILE` |Importálja a HTTPS-tanúsítványt. Meg kell adnia a teljes elérési utat, amely egy \* . CRT-fájlhoz vezet. |
|Dátum megjelenítése |`date` |Az aktuális dátumot adja vissza GMT formátumban a gazdagépen. |

## <a name="filter-network-configurations"></a>Hálózati konfigurációk szűrése

A `network capture-filter` parancs lehetővé teszi a rendszergazdáknak, hogy kiküszöböljék a nem elemzett hálózati forgalmat. A forgalmat belefoglalási lista vagy kizárási lista használatával is szűrheti.

```azurecli-interactive
network capture-filter
```

A parancs beírása után a következő kérdés jelenik meg:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Válassza ki a `Y` Nano-fájl megnyitásához, ahol az alábbi szintaxis szerint adhat hozzá egy eszközt, csatornát, portot és részhalmazt:

| Attribútum | Leírás |
|--|--|
| 1.1.1.1 | Az eszköz összes adatforgalmát tartalmazza. |
| 1.1.1.1, 2.2.2.2 | A csatorna összes adatforgalmát tartalmazza. |
| 1.1.1, 2.2.2 | Az alhálózat összes adatforgalmát tartalmazza. |

Egy sor eldobásával válassza el az argumentumokat.

Ha tartalmaz egy eszközt, csatornát vagy alhálózatot, az érzékelő feldolgozza az adott argumentum érvényes forgalmát, beleértve a általában nem feldolgozott portokat és forgalmat.

Ezután a következő kérdéssel kell megválaszolni:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Válassza ki a `Y` Nano-fájl megnyitásához, ahol az alábbi szintaxis szerint adhat hozzá egy eszközt, csatornát, portot és alkészletet:

| Attribútum | Leírás |
|--|--|
| 1.1.1.1 | Az eszközön lévő összes forgalom kizárása. |
| 1.1.1.1, 2.2.2.2 | Kizárja a csatorna összes forgalmát, ami azt jelenti, hogy az összes forgalom két eszköz között van. |
| 1.1.1.1, 2.2.2.2, 443 | Kizárja a csatorna összes forgalmát a port alapján. |
| 1.1.1 | Az alhálózat összes adatforgalmának kizárása. |
| 1.1.1, 2.2.2 | Az alhálózatok közötti összes forgalom kizárása. |

Egy sor eldobásával válassza el az argumentumokat.

Ha kizár egy eszközt, csatornát vagy alhálózatot, az érzékelő kizárja az adott argumentum érvényes forgalmát.

### <a name="ports"></a>Portok

UDP-és TCP-portok belefoglalása vagy kizárása az összes forgalomhoz.

>`502`: egyetlen port

>`502,443`: mindkét port

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Összetevők

A rendszer a következő kérdést kéri:

>`In which component do you wish to apply this capture filter?`

A lehetőségek a következők:,,,,  `all` `dissector` `collector` `statistics-collector` `rpc-parser` vagy `smb-parser` .

A legtöbb felhasználási esetben válassza a elemet `all` .

### <a name="custom-base-capture-filter"></a>Egyéni alap rögzítési szűrő

Az alap rögzítési szűrő az összetevők alapértéke. A szűrő például meghatározza, hogy mely portok érhetők el az összetevő számára.

Válassza ki az `Y` alábbi lehetőségek mindegyikét. A módosítások beállítása után az összes szűrő hozzá lesz adva az alaptervhez. Ha módosítást végez, a rendszer felülírja a meglévő alaptervet.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Ha úgy dönt, hogy kizár egy olyan alhálózatot, mint például az 1.1.1:

- `internal` csak ezt az alhálózatot fogja kizárni.

- `all-connected` a kizárja ezt az alhálózatot, és az adott alhálózatra irányuló és onnan érkező összes forgalmat.

Javasoljuk, hogy válassza a lehetőséget `internal` .

> [!NOTE]
> A rendszer az eszköz összes szűrőjét használja, és nem függ a munkamenettől. Más szóval a `internal`   szűrők és  `all-connected`   mások számára soha nem választhat.

### <a name="comments"></a>Megjegyzések

A szűrőket a ben tekintheti meg  ```/var/cyberx/properties/cybershark.properties``` :

- **statisztika – gyűjtő**:  `bpf_filter property` ```/var/cyberx/properties/net.stats.collector.properties```

- **szektor**: `override.capture_filter`   tulajdonság a (z) ```/var/cyberx/properties/cybershark.properties```

- **RPC-elemző**: `override.capture_filter` tulajdonság a (z) ```/var/cyberx/properties/rpc-parser.properties```

- **SMB-elemző**:  `override.capture_filter`   tulajdonság a (z) ```/var/cyberx/properties/smb-parser.properties```

- **gyűjtő**: `general.bpf_filter`   tulajdonság a (z) ```/var/cyberx/properties/collector.properties```

Az alapértelmezett konfiguráció visszaállításához írja be a következő kódot a cyberx-felhasználóhoz:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Ügyfél-és kiszolgáló-gazdagépek definiálása

Ha a IoT Defender nem ismeri fel automatikusan az ügyfelet és a kiszolgáló gazdagépeit, a következő parancs megadásával állíthatja be az ügyfél és a kiszolgáló gazdagépeit:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

A paranccsal a következő attribútumok használhatók `directions` :

| Attribútum | Leírás |
|--|--|
| [-h] | A parancshoz tartozó súgó adatainak kinyomtatása. |
| [--azonosító azonosító] | A kiszolgáló azonosítója. |
| [--port PORT] | A kiszolgáló portszáma. |
| [--Remove] | Egy ügyfél vagy egy kiszolgáló gazdagépének eltávolítása a listáról. |
| [--Add] | Egy ügyfél vagy egy kiszolgáló gazdagépét adja hozzá a listához. |
| [--TCP] | TCP használata a gazdagéptel folytatott kommunikáció során. |
| [--UDP] | UDP használata a gazdagéptel folytatott kommunikáció során. |

## <a name="system-actions"></a>Rendszerműveletek
A következő táblázat ismerteti a különböző rendszerműveletek végrehajtásához elérhető parancsokat a Defender for IoT:

|Name|Code|Description|
|----|----|-----------|
|Dátum megjelenítése|`date`|Az aktuális dátumot adja vissza GMT formátumban a gazdagépen.|
|A gazdagép újraindítása|`system reboot`|Újraindítja a gazdagép eszközét.|
|A gazdagép leállítása|`system shutdown`|Leállítja a gazdagépet.|
|A System biztonsági mentése|`system backup`|Azonnali biztonsági mentést kezdeményez (nem ütemezett biztonsági mentés).|
|A rendszer visszaállítása biztonsági másolatból|`system restore`|Visszaállítja a legutóbbi biztonsági mentést.|
|A biztonságimásolat-fájlok listázása|`system backup-list`|Felsorolja a rendelkezésre álló biztonságimásolat-fájlokat.|
|Az összes Defender állapotának megjelenítése a IoT platform szolgáltatásaihoz|`system sanity`|A IoT platform szolgáltatásainak aktuális állapotának listázásával ellenőrzi a rendszer teljesítményét.|
|A szoftver verziójának megjelenítése|`system version`|Megjeleníti a rendszeren jelenleg futó szoftver verzióját.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>SSL-és TLS-tanúsítványok üzembe helyezése készülékekre

Adja meg a következő parancsot az SSL-és TLS vállalati tanúsítványok a CLI-be történő importálásához:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Az eszköz használatához fel kell töltenie a tanúsítvány-fájlokat az eszközre. Ezt megteheti olyan eszközökkel, mint a megnyerő vagy a wget. 

A parancs a következő bemeneti jelzőket támogatja:

| Jelölő | Leírás |
|--|--|
| -h | A parancssori Súgó szintaxisát jeleníti meg. |
| – CRT | A tanúsítványfájl (. CRT kiterjesztés) elérési útja. |
| – kulcs | A \* . key fájl. A kulcs hosszának legalább 2 048 bitenek kell lennie. |
| – lánc | A tanúsítványlánc fájljának elérési útja (nem kötelező). |
| --pass | A tanúsítvány titkosításához használt jelszó (nem kötelező). |
| --jelszó-készlet | Az alapértelmezett érték **hamis**, nem **használt**. <br />Állítsa **igaz** értékre az előző tanúsítványhoz megadott előző jelszó (nem kötelező) használatára. |  |

Az eszköz használatakor:

- Ellenőrizze, hogy a tanúsítvány fájljai olvashatók-e a készüléken. 

- Erősítse meg a berendezés tartományát (ahogy az a tanúsítványban jelenik meg) a DNS-kiszolgálóval és a megfelelő IP-címmel. 
    
## <a name="see-also"></a>Lásd még

[Defender for IoT API-érzékelő és felügyeleti konzol API-k](references-work-with-defender-for-iot-apis.md)
