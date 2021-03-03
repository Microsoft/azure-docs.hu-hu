---
title: A Linux-alkalmazások teljesítményének összegyűjtése a Azure Monitorban | Microsoft Docs
description: Ez a cikk részletesen ismerteti a Linux rendszerhez készült Log Analytics-ügynök konfigurálását a MySQL-hez és az Apache HTTP-kiszolgálóhoz tartozó teljesítményszámlálók összegyűjtéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: f924bcdce0937c74c3deeab96f3d4b2a85e3944d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704446"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Teljesítményszámlálók gyűjtése a Azure Monitor linuxos alkalmazásaihoz 

Ez a cikk részletesen ismerteti a [Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) rendszerhez készült log Analytics-ügynök konfigurálásának részleteit, hogy az egyes alkalmazásokhoz tartozó teljesítményszámlálók Azure monitorba legyenek gyűjtve.  A cikkben szereplő alkalmazások a következők:  

- [MySQL](#mysql)
- [Apache HTTP-kiszolgáló](#apache-http-server)

## <a name="mysql"></a>MySQL
Ha a rendszer a Log Analytics ügynök telepítésekor MySQL-kiszolgálót vagy MariaDB-kiszolgálót észlel a számítógépen, a rendszer automatikusan telepíti a MySQL-kiszolgáló Teljesítményfigyelő szolgáltatóját. Ez a szolgáltató csatlakozik a helyi MySQL/MariaDB-kiszolgálóhoz, és elérhetővé teszi a teljesítménnyel kapcsolatos statisztikákat. A MySQL-felhasználó hitelesítő adatait úgy kell konfigurálni, hogy a szolgáltató hozzáférhessen a MySQL-kiszolgálóhoz.

### <a name="configure-mysql-credentials"></a>MySQL hitelesítő adatok konfigurálása
A MySQL-t szolgáltatónak előre konfigurált MySQL-felhasználót és telepített MySQL-ügyféloldali kódtárakat kell megadnia ahhoz, hogy lekérdezze a MySQL-példány teljesítményére és állapotára vonatkozó információkat.  Ezeket a hitelesítő adatokat egy Linux-ügynökön tárolt hitelesítési fájlban tárolja a rendszer.  A hitelesítési fájl határozza meg, hogy a MySQL-példány milyen kötési-címeket és portokat figyel, és milyen hitelesítő adatokat használjon a metrikák összegyűjtéséhez.  

A Linux rendszerhez készült Log Analytics-ügynök telepítése során a MySQL adatszolgáltató megvizsgálja a MySQL saját. cnf konfigurációs fájljait (az alapértelmezett helyet) a kötési és a porthoz, és részben beállítja a MySQL-vel kapcsolatos hitelesítési fájlt.

A MySQL-hitelesítési fájlt a következő helyen tárolja: `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth` .


### <a name="authentication-file-format"></a>Hitelesítési fájl formátuma
A MySQL-vel kapcsolatos hitelesítési fájl formátuma a következő:

> [Port] = [kötési-címe], [Felhasználónév], [Base64 kódolású jelszó]  
> (Port) = (kötési-címe), (Felhasználónév), (Base64 kódolású jelszó)  
> (Port) = (kötési-címe), (Felhasználónév), (Base64 kódolású jelszó)  
> AutoUpdate = [TRUE | FALSE]  

A hitelesítési fájlban szereplő bejegyzéseket a következő táblázat ismerteti.

| Tulajdonság | Leírás |
|:--|:--|
| Port | A MySQL-példányt figyelő aktuális portot jelöli. A 0. port azt adja meg, hogy a következő tulajdonságok használatosak az alapértelmezett példányhoz. |
| Bind-Address| Aktuális MySQL-kötés – címe. |
| username| MySQL-felhasználó, amely a MySQL-kiszolgálópéldány figyelésére használatos. |
| Base64 kódolású jelszó| A MySQL-figyelési felhasználó Base64-kódolású jelszava. |
| AutoUpdate| Megadja, hogy a rendszer újravizsgálja-e a My. cnf fájl módosításait, és felülírja-e a MySQL-vel kapcsolatos hitelesítési fájlt a MySQL-adatszolgáltató frissítésekor. |

### <a name="default-instance"></a>Alapértelmezett példány
A MySQL-vel kapcsolatos hitelesítési fájl definiálhat egy alapértelmezett példányt és portszámot, amellyel több MySQL-példányt kezelhet egy Linux-gazdagépen.  Az alapértelmezett példányt a 0. porttal rendelkező példány jelöli. A további példányok öröklik az alapértelmezett példány tulajdonságait, kivéve, ha eltérő értékeket határoznak meg. Ha például a MySQL-példány figyeli a 3308-es portot, a rendszer az alapértelmezett példány kötési-címe, felhasználóneve és Base64 kódolású jelszavát fogja használni a 3308-es példány figyelésére és figyelésére. Ha a 3308-es példány egy másik címről van kötve, és ugyanazt a MySQL-felhasználónevet és jelszót használja, csak a kötési címekre van szükség, a többi tulajdonság pedig örökölni fog.

A következő táblázat példaként tartalmazza a példányok beállításait 

| Leírás | File |
|:--|:--|
| Az alapértelmezett példány és példány az 3308-as porttal. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Az alapértelmezett példány és példány a 3308-es porttal és a másik felhasználónévvel és jelszóval. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL-vel való hitelesítés fájljának programja
A MySQL-vel rendelkező szolgáltató a MySQL-t használó MySQL-es hitelesítési fájl, amely a MySQL-vel kapcsolatos hitelesítési fájl szerkesztésére használható. A hitelesítési fájl program a következő helyen található.

`/opt/microsoft/mysql-cimprov/bin/mycimprovauth`

> [!NOTE]
> A hitelesítő adatok fájljának olvashatónak kell lennie a omsagent-fiókban. A mycimprovauth parancs futtatása a omsgent használata javasolt.

A következő táblázat részletesen ismerteti a mycimprovauth használatának szintaxisát.

| Művelet | Példa | Leírás
|:--|:--|:--|
| *hamis vagy igaz* értékű AutoUpdate | mycimprovauth AutoUpdate false | Megadja, hogy a hitelesítési fájl frissítése automatikusan megtörténjen-e az újraindítás vagy a frissítés során. |
| alapértelmezett *kötési Felhasználónév jelszava* | mycimprovauth alapértelmezett 127.0.0.1 root pwd | Beállítja az alapértelmezett példányt a MySQL-t használó hitelesítési fájlban.<br>A jelszó mezőt egyszerű szövegként kell megadni – a MySQL-t tartalmazó hitelesítési fájl jelszava a 64-es alapszintű. |
| *alapértelmezett vagy port_num* törlése | mycimprovauth 3308 | A megadott példány törlése alapértelmezett vagy portszám alapján. |
| segítség | mycimprov Súgó | Kinyomtatja a használni kívánt parancsok listáját. |
| nyomtatási | mycimprov nyomtatása | Kinyomtat egy könnyen olvasható MySQL-alapú hitelesítési fájlt. |
| a *kötési Felhasználónév jelszavának* frissítése port_num | mycimprov frissítés 3307 127.0.0.1 root pwd | Frissíti a megadott példányt, vagy hozzáadja a példányt, ha az nem létezik. |

A következő példában szereplő parancsok egy alapértelmezett felhasználói fiókot határoznak meg a MySQL-kiszolgálóhoz a localhost-on.  A jelszó mezőt egyszerű szövegként kell megadni – a MySQL-t tartalmazó hitelesítési fájl jelszava a 64-es alapszintű.

```console
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
sudo /opt/omi/bin/service_control restart
```

### <a name="database-permissions-required-for-mysql-performance-counters"></a>A MySQL-teljesítményszámlálók számára szükséges adatbázis-engedélyek
A MySQL-felhasználónak hozzá kell férnie a következő lekérdezésekhez a MySQL-kiszolgáló teljesítményadatok gyűjtéséhez. 

```sql
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

A MySQL-felhasználónak emellett a következő alapértelmezett táblákhoz is meg kell adnia a hozzáférést.

- information_schema
- MySQL. 

Ezek a jogosultságok a következő engedélyezési parancsok futtatásával adhatók meg.

```sql
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

> [!NOTE]
> Ha engedélyeket szeretne adni egy MySQL-figyelési felhasználónak, az adott felhasználónak rendelkeznie kell a "GRANT Option" jogosultsággal, valamint a megadott jogosultsággal.

### <a name="define-performance-counters"></a>Teljesítményszámlálók definiálása

Miután konfigurálta a Linux Log Analytics-ügynökét, hogy adatokat küldjön a Azure Monitornak, konfigurálnia kell a teljesítményszámlálók gyűjtését.  A következő táblázatban található számlálókkal Azure Monitor használhatja a [Windows és a Linux teljesítmény-adatforrásaiban található](data-sources-performance-counters.md) eljárást.

| Objektum neve | Számláló neve |
|:--|:--|
| MySQL-adatbázis | Lemezterület bájtban |
| MySQL-adatbázis | Táblák |
| MySQL-kiszolgáló | Megszakított kapcsolatok PCT |
| MySQL-kiszolgáló | A kapcsolatok használata PCT |
| MySQL-kiszolgáló | Lemezterület-használat bájtban |
| MySQL-kiszolgáló | Teljes táblázatos vizsgálat PCT |
| MySQL-kiszolgáló | InnoDB-puffer találata (PCT) |
| MySQL-kiszolgáló | A InnoDB Buffer-készlete PCT-t használ |
| MySQL-kiszolgáló | A InnoDB Buffer-készlete PCT-t használ |
| MySQL-kiszolgáló | Kulcs gyorsítótárának találati aránya (%) |
| MySQL-kiszolgáló | A kulcs gyorsítótára a PCT protokollt használja |
| MySQL-kiszolgáló | Kulcs-gyorsítótárazási írási PCT |
| MySQL-kiszolgáló | Lekérdezés gyorsítótárának találati aránya (%) |
| MySQL-kiszolgáló | Lekérdezési gyorsítótár aszalt szilva (PCT) |
| MySQL-kiszolgáló | A lekérdezési gyorsítótár használata PCT |
| MySQL-kiszolgáló | A tábla gyorsítótárának találati aránya (%) |
| MySQL-kiszolgáló | A tábla gyorsítótára a PCT protokollt használja |
| MySQL-kiszolgáló | Tábla zárolásának feloldása PCT |

## <a name="apache-http-server"></a>Apache HTTP-kiszolgáló 
Ha az Apache HTTP-kiszolgáló észlelhető a számítógépen a omsagent csomag telepítésekor, a rendszer automatikusan telepíti az Apache HTTP-kiszolgáló Teljesítményfigyelő szolgáltatóját. Ez a szolgáltató egy Apache-modulra támaszkodik, amelyet be kell tölteni az Apache HTTP-kiszolgálóra a teljesítményadatok elérése érdekében. A modul a következő paranccsal tölthető be:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Az Apache monitoring modul eltávolításához futtassa a következő parancsot:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Teljesítményszámlálók definiálása

Miután konfigurálta a Linux Log Analytics-ügynökét, hogy adatokat küldjön a Azure Monitornak, konfigurálnia kell a teljesítményszámlálók gyűjtését.  A következő táblázatban található számlálókkal Azure Monitor használhatja a [Windows és a Linux teljesítmény-adatforrásaiban található](data-sources-performance-counters.md) eljárást.

| Objektum neve | Számláló neve |
|:--|:--|
| Apache HTTP-kiszolgáló | Foglalt feldolgozók |
| Apache HTTP-kiszolgáló | Tétlen feldolgozók |
| Apache HTTP-kiszolgáló | Százalékos elfoglalt feldolgozók |
| Apache HTTP-kiszolgáló | Teljes PCT CPU |
| Apache virtuális gazdagép | Hibák percenként – ügyfél |
| Apache virtuális gazdagép | Hibák percenként – kiszolgáló |
| Apache virtuális gazdagép | KB/kérelem |
| Apache virtuális gazdagép | KB/s kérések másodpercenként |
| Apache virtuális gazdagép | Kérelmek/másodperc |



## <a name="next-steps"></a>Következő lépések
* [Teljesítményszámlálók összegyűjtése](data-sources-performance-counters.md) Linux-ügynököktől.
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../logs/log-query-overview.md) .