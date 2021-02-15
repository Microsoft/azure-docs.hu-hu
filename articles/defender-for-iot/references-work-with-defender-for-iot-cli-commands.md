---
title: A Defender for IoT parancssori felületi parancsainak használata
description: Ez a cikk az érzékelőkre és a helyszíni felügyeleti konzolokra vonatkozó IoT CLI-parancsait ismerteti.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 2ec682bf76e35b54f58acc1956972c57128edd75
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523141"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>A Defender for IoT parancssori felületi parancsainak használata

Ez a cikk az érzékelők és a helyszíni felügyeleti konzolok CLI-parancsait ismerteti. A parancsok a rendszergazdák, a cyberx-felhasználók és a támogatási felhasználók számára érhetők el.

Kizárási szabályok definiálása a karbantartási tevékenységek vagy a riasztást nem igénylő tevékenységek tervezésekor.

## <a name="create-local-alert-exclusion-rules"></a>Helyi riasztás kizárási szabályainak létrehozása

A következő parancs beírásával hozhat létre kizárási szabályt a parancssori felületre:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

A riasztás kizárási szabályaiban megadható attribútumok a következők:

| Attribútum | Leírás |
|--|--|
| [-h] | A parancshoz tartozó súgó adatainak kinyomtatása. |
| -n név | A létrehozandó szabály neve. |
| [-TS TIMES] | Az az időtartomány, amelyre a szabály aktív. Ezt a következőképpen kell megadni:<br />`xx:yy-xx:yy`<br />Több időszakot is megadhat a közöttük lévő vesszők használatával. Példa: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir irány] | A szabály alkalmazásának iránya. Ezt a következőképpen kell megadni:<br />`both | src | dst` |
| [-dev-eszközök] | A szabály által kizárandó eszközök IP-címe és címe, a következőként megadva:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a RIASZTÁSok] | A szabály által kizárni kívánt riasztás neve:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Helyi riasztás kizárási szabályainak hozzáfűzése

Új szabályokat adhat hozzá a riasztások jelenlegi kizárási szabályaihoz a parancssori felület következő parancsának beírásával:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Az itt használt attribútumok hasonlók a helyi riasztás kizárási szabályainak létrehozásakor leírt attribútumokhoz. A használat itt az attribútumokat alkalmazza a meglévő szabályokra.

## <a name="show-local-alert-exclusion-rules"></a>Helyi riasztás kizárási szabályainak megjelenítése

A következő parancs megadásával tekintheti meg az összes meglévő kizárási szabályt:

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

A riasztás kizárási szabályai a következő attribútummal használhatók:

| Attribútum | Leírás|
| --------- | ---------------------------------- |
| -n név | A törlendő szabály neve. |

## <a name="sync-time-from-the-ntp-server"></a>Szinkronizálási idő az NTP-kiszolgálóról

Engedélyezheti és letilthatja az időszinkronizálást egy NTP-kiszolgálóról.

### <a name="enable-ntp-sync"></a>NTP-szinkronizálás engedélyezése

A következő parancs beírásával lehetővé válik a megadott NTP-kiszolgáló aktuális idejének rendszeres lekérése:

```azurecli-interactive
ntp enable IP
```

A parancson belül megadható attribútum az NTP-kiszolgáló IP-címe.

### <a name="disable-ntp-sync"></a>NTP-szinkronizálás letiltása

A következő parancs beírásával letilthatja az időszinkronizálást a megadott NTP-kiszolgálóval:

```azurecli-interactive
ntp disable IP
```

A parancson belül megadható attribútum az NTP-kiszolgáló IP-címe.

## <a name="configure-the-network"></a>A hálózat konfigurálása

A következő táblázat a IoT Azure Defender hálózati beállításainak konfigurálásához elérhető parancsokat ismerteti:

|Name|Parancs|Leírás|
|-----------|-------|-----------|
|Ping|`ping IP `| A IoT platform Defenderen kívüli címeinek pingelése.|
|Blink|`network blink`|Engedélyezi a hálózati konfigurációs paraméterek módosítását.|
|A hálózat újrakonfigurálása |`network edit-settings`| Engedélyezi a hálózati konfigurációs paraméterek módosítását. |
|Hálózati beállítások megjelenítése |`network list`|Megjeleníti a hálózati adapter paramétereit. |
|A hálózati konfiguráció ellenőrzése |`network validate` |Megadja a kimeneti hálózati beállításokat. <br /> <br />Például: <br /> <br />Aktuális hálózati beállítások: <br /> illesztő: ETH0 <br /> IP: 10.100.100.1 <br />alhálózat: 255.255.255.0 <br />alapértelmezett átjáró: 10.100.100.254 <br />DNS: 10.100.100.254 <br />figyelő felületek: eth1|
|Tanúsítvány importálása |`certificate import FILE` |Importálja a HTTPS-tanúsítványt. Meg kell adnia a teljes elérési utat, amely egy \* . CRT-fájlhoz vezet. |
|Dátum megjelenítése |`date` |Az aktuális dátumot adja vissza GMT formátumban a gazdagépen. |

## <a name="filter-network-configurations"></a>Hálózati konfigurációk szűrése

A `network capture-filter` parancs lehetővé teszi a rendszergazdák számára a nem elemzett hálózati forgalom kiiktatását. A forgalom szűrése egy belefoglalási lista vagy egy kizárási lista használatával.

```azurecli-interactive
network capture-filter
```

A parancs beírása után a következő kérdés jelenik meg:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Válassza ki a `Y` Nano-fájl megnyitásához, ahol eszközöket, csatornákat, portokat és részhalmazokat adhat hozzá az alábbi szintaxis szerint:

| Attribútum | Leírás |
|--|--|
| 1.1.1.1 | Az eszköz összes adatforgalmát tartalmazza. |
| 1.1.1.1, 2.2.2.2 | A csatorna összes adatforgalmát tartalmazza. |
| 1.1.1, 2.2.2 | Az alhálózat összes adatforgalmát tartalmazza. |

Egy sor eldobásával válassza el az argumentumokat.

Ha tartalmaz egy eszközt, csatornát vagy alhálózatot, az érzékelő feldolgozza az adott argumentum érvényes forgalmát, beleértve a általában nem feldolgozott portokat és forgalmat.

Ezután a következőt kell megadnia:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Válassza ki a `Y` Nano-fájl megnyitásához, ahol az alábbi szintaxis szerint adhat hozzá eszközöket, csatornákat, portokat és alkészleteket:

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

A rendszer a következőket kéri:

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

Ha a IoT Defender nem ismeri fel automatikusan az ügyfél-és kiszolgáló-gazdagépeket, a következő parancs megadásával állíthatja be az ügyfél és a kiszolgáló gazdagépeit:

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
    
## <a name="next-steps"></a>Következő lépések

[Defender for IoT API-érzékelő és felügyeleti konzol API-k](references-work-with-defender-for-iot-apis.md)
