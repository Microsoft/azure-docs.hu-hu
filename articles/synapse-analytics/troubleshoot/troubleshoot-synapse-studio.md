---
title: A Synapse Studio hibaelhárítása
description: Az Azure szinapszis Studio hibáinak megoldása
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98116177"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Az Azure szinapszis Studio hibaelhárítása

Ez a hibaelhárítási útmutató útmutatást nyújt arról, hogy milyen információkat kell megadnia a hálózati kapcsolattal kapcsolatos támogatási jegyek megnyitásakor. A megfelelő információkkal a probléma gyorsabban oldható fel.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Kiszolgáló nélküli SQL Pool szolgáltatás kapcsolódási problémája

### <a name="symptom-1"></a>1. tünet

A "kiszolgáló nélküli SQL-készlet" beállítás szürkén jelenik meg a "kapcsolódás a legördülő listához".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>2. tünet

Ha a lekérdezést a "kiszolgáló nélküli SQL-készlettel" futtatja, a "nem sikerült kapcsolatot létesíteni a kiszolgálóval" hibaüzenet jelenik meg.

![2. tünet](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

> [!NOTE] 
>    A következő hibaelhárítási lépések a Chromium Edge és a Chrome. Használhat más böngészőket is (például a FireFoxot) ugyanazzal a hibaelhárítási lépéssel, de a "fejlesztői eszköz" ablak eltérő elrendezéssel rendelkezhet az ebben a HKT-ben található képernyőképből. Ha lehetséges, ne használja a klasszikus Edge-t a hibaelhárításhoz, mivel bizonyos helyzetekben pontatlan információkat jeleníthet meg.

Nyissa meg a "diagnosztikai adatok" panelt, és válassza a "diagnosztika letöltése" gombot. Tartsa meg a letöltött adatokat a hibajelentéshez. Ehelyett másolja a "munkamenet-azonosítót", és csatolja a támogatási jegy megnyitásakor.

![diagnosztikai adatok](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

A hibaelhárítás megkezdéséhez próbálja megismételni az Azure szinapszis Studióban végrehajtott műveletet.

- Az 1. tünet esetében válassza az "adatbázis használata" legördülő menüből jobbra található "frissítés" gombot, és ellenőrizze, hogy látható-e a "kiszolgáló nélküli SQL-készlet".
- A 2. tünet esetében próbálkozzon újra a lekérdezés futtatásával, és ellenőrizze, hogy sikeresen végrehajtja-e a műveletet.

Ha a probléma továbbra is fennáll, nyomja meg az F12 billentyűt a böngészőben a "Fejlesztői eszközök" (DevTools) megnyitásához.

A "Fejlesztői eszközök" ablakban váltson a "hálózat" panelre. Ha szükséges, válassza a "hálózat" panel eszköztárának "Törlés" gombját.
Győződjön meg arról, hogy a "hálózati" panelen a "gyorsítótár letiltása" lehetőség be van jelölve.

Próbálja megismételni az Azure szinapszis Studióban végrehajtott műveletet. Előfordulhat, hogy a "Fejlesztői eszközök" hálózat listájában látható új elemek jelennek meg. Jegyezze fel a jelenlegi rendszeridőt, hogy a támogatási jegyet megadja.

![hálózat – 1. panel](media/troubleshooting-synapse-studio/network-panel.png)

Keresse meg azt az elemet, amelynek URL-oszlopa megfelel a következő mintának:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Ahol [*A*] a munkaterület neve, és a "-OnDemand" lehet "-sqlod", és A [*B*] nevet kell megadni, például: "Master". Legfeljebb két elemnek kell lennie ugyanazzal az URL-címmel, de eltérő metódusi értékekkel; BEÁLLÍTÁSOK és POST. Győződjön meg arról, hogy a két elem "200" vagy "20x" értékkel rendelkezik az Állapot oszlopban, ahol az "x" egyetlen számjegy lehet.

Ha egyikük sem a "20x", sem a következőhöz tartozik:

- Az állapot "(sikertelen)" értékkel kezdődik, vagy szélesítse az "állapot" oszlopot, vagy vigye a mutatót az állapot szövege fölé a teljes szöveg megjelenítéséhez. A támogatási jegy megnyitásakor adja meg a szöveget és/vagy a képernyőképet.

    ![állapot szövege](media/troubleshooting-synapse-studio/status-text.png)

    - Ha megjelenik a ERR_NAME_NOT_RESOLVED, és 10 percen belül létrehozta a munkaterületet, várjon 10 percet, és próbálkozzon újra annak megtekintésével, hogy a probléma továbbra is fennáll-e.
    - Ha ERR_INTERNET_DISCONNECTED vagy ERR_NETWORK_CHANGED jelenik meg, akkor előfordulhat, hogy a számítógép hálózati kapcsolataival kapcsolatban problémák léptek fel. Győződjön meg a hálózati kapcsolatban, majd próbálja megismételni a műveletet.
    - Ha ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR vagy más, SSL-t tartalmazó hibakód jelenik meg, akkor előfordulhat, hogy a helyi SSL-konfiguráció problémákba ütközik, vagy ha a hálózati rendszergazdája letiltotta a hozzáférést a kiszolgáló nélküli SQL-készlet kiszolgálóhoz. Nyisson meg egy támogatási jegyet, és csatolja a hibakódot a leírásban.
    - Ha ERR_NETWORK_ACCESS_DENIED jelenik meg, előfordulhat, hogy a rendszergazdával kell ellenőriznie, hogy a helyi tűzfal házirendje letiltotta-e a (z) *. database.windows.net tartomány elérését vagy a 1443-es távoli portot.
    - Ha szeretné, próbálja megismételni ugyanezt a műveletet egy másik gépen és/vagy hálózati környezetben, hogy kizárja a hálózati konfigurációs problémát a SZÁMÍTÓGÉPén.

- Az állapot "40x", "50x" vagy más szám, és az elem (ek) ra kattintva megtekintheti a részleteket. Az elem részleteinek a jobb oldalon kell megjelennie. A "válasz fejléc" szakasz megkeresése Ezután győződjön meg arról, hogy létezik-e "hozzáférés-vezérlés – engedélyezés – forrás" nevű elem. Ha igen, ellenőrizze, hogy az alábbi értékek valamelyike szerepel-e:

    - `*` (egy csillag)
    - https://web.azuresynapse.net/ (vagy más olyan érték, amelyet a böngésző címsorának szövege a következővel kezdődik:)

Ha a válasz fejléce a fenti értékek egyikét tartalmazza, akkor azt jelenti, hogy már összegyűjtöttük a hiba adatait. Ha szükséges, megnyithatja a támogatási jegyet, és opcionálisan csatolhatja az elem részleteinek képernyőképét is.

Ha nem látja a fejlécet, vagy a fejléc nem rendelkezik a fent felsorolt értékek egyikével, csatolja az elem részleteit a jegy megnyitásakor.

 
![elemek részletei](media/troubleshooting-synapse-studio/item-details.png)
 
Ha a fenti lépések nem oldják meg a problémát, lehetséges, hogy meg kell nyitnia egy támogatási jegyet. A támogatási jegy elküldésekor az útmutató elején letöltött "munkamenet-azonosító" vagy "diagnosztikai információ" szerepel.

A probléma jelentésekor lehetősége van arra, hogy a "Fejlesztői eszközök" konzoljának lapfülét, és csatolja a támogatási jegyhez. Görgesse a tartalmat, és ha szükséges, a teljes üzenet rögzítéséhez vegyen fel több képernyőképet.

![fejlesztői eszköz konzolja](media/troubleshooting-synapse-studio/developer-tool-console.png)

Ha képernyőképeket csatol, adja meg az időt (vagy a becsült időtartományt), amikor elvégezte a képernyőképeket. A probléma megoldásához segítséget nyújt nekünk.

Bizonyos böngészők támogatják az időbélyegek megjelenítését a "konzol" lapon. A Chromium Edge/Chrome esetében nyissa meg a "beállítások" párbeszédpanelt a "Fejlesztői eszközök" részben, és a "beállítások" lapon tekintse meg a "időbélyegek megjelenítése" lapot.

![fejlesztői eszköz konzoljának beállításai](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![időbélyegző megjelenítése](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Következő lépések
Ha az előző lépések nem segítenek a probléma megoldásában, [hozzon létre egy támogatási jegyet](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)