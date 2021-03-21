---
title: Azure Key Vault monitorozás és riasztás | Microsoft Docs
description: Hozzon létre egy irányítópultot a kulcstartó állapotának figyeléséhez és a riasztások konfigurálásához.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 523f24a7d8131dda67244e4d48510d9e1cb7cdb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582629"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Figyelés és riasztás a Azure Key Vault

## <a name="overview"></a>Áttekintés

Miután elindította a Key vaultot a termelési titkok tárolására, fontos a kulcstartó állapotának figyelése annak biztosításához, hogy a szolgáltatás a kívánt módon működjön. A szolgáltatás skálázásának megkezdése után a Key vaultba küldött kérések száma növekedni fog. Ez potenciálisan növelheti a kérések késését, és szélsőséges esetekben a kérések szabályozása is lehetséges, ami hatással lehet a szolgáltatás teljesítményére. Akkor is riasztást kell kapnia, ha a kulcstartó szokatlan számú hibakódot küld, így gyorsan értesítheti a hozzáférési házirendet vagy a tűzfal konfigurációs hibáit. Ez a dokumentum a következő témákat tárgyalja:

+ A figyelni kívánt alapszintű Key Vault mérőszámok
+ Metrikák konfigurálása és irányítópult létrehozása
+ Riasztások létrehozása a megadott küszöbértékeken

A Key Vault Azure Monitor a naplók és a metrikák összeadásával globális figyelési megoldást biztosít. [További információ a Azure Monitor Key Vualt](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>A figyelni kívánt alapszintű Key Vault mérőszámok

+ Tár rendelkezésre állása  
+ Tár telítettsége 
+ Szolgáltatás API-késése 
+ Összes szolgáltatási API-találat (Szűrés tevékenység típusa szerint) 
+ Hibakódok (szűrés állapotkód alapján) 

Tár **rendelkezésre állása** – ez a metrika mindig a 100%-os lehet. Ez egy fontos mérőszám a figyeléshez, mert gyorsan megmutathatja, hogy a kulcstartó áramkimaradást észlelt-e. 

Tár **telítettsége** – a Key Vault által kiszolgálható kérelmek másodpercenkénti száma az elvégezhető művelet típusától függ. Egyes tár-műveleteknél kisebb kérelmek másodpercenkénti száma. Ez a mérőszám összesíti a kulcstartó teljes használatát az összes működési típusban, hogy a rendszer az aktuális kulcstartó-használatot jelző százalékos értéket jelenítse meg. A Key Vault szolgáltatási korlátainak teljes listáját a következő dokumentum tartalmazza. [Az Azure Key Vault szolgáltatáskorlátozásai](service-limits.md)

**Szolgáltatás API-késése** – ez a metrika a Key Vault felé irányuló hívások átlagos késését mutatja a szolgáltatásban mérve. Nem tartalmazza az ügyfél vagy a hálózat által az ügyfél és a szolgáltatás között felhasznált időt.

**API-találatok összesen** – ez a metrika a kulcstartón végrehajtott összes hívást mutatja. Ez segít azonosítani, hogy mely alkalmazások hívja meg a kulcstartót. 

**Hibakódok** – ez a metrika megmutatja, hogy a kulcstartó szokatlan mennyiségű hibát észlelt. A hibakódok és a hibaelhárítási útmutató teljes listáját az alábbi dokumentumban tekintheti meg. [Azure Key Vault REST API hibakódok](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Metrikák konfigurálása és irányítópult létrehozása

1. Jelentkezzen be az Azure Portalba.
2. Navigáljon a Key Vault
3. **Metrikák** kiválasztása a **figyelés** alatt 

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: a figyelés szakasz mérőszámok lehetőségének kiemelése.](../media/alert-1.png)

4. Frissítse a diagram címét, hogy mit szeretne látni az irányítópulton. 
5. Válassza ki a hatókört. Ebben a példában egyetlen kulcstartót fogunk kiválasztani. 
6. Válassza ki a metrikák **teljes rendelkezésre állását** és az összesítési **átlagot** 
7. Frissítse az időtartományt az elmúlt 24 órában, és frissítse az idő részletességét 1 percre. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely a tár teljes Availablility metrikáját mutatja.](../media/alert-2.png)

8. Ismételje meg a fenti lépéseket a tár telítettségéhez és a szolgáltatás API-késési metrikáinak használatához. Válassza a **rögzítés az irányítópulton** lehetőséget a metrikák irányítópultra való mentéséhez. 

> [!IMPORTANT]
> Válassza a rögzítés az irányítópulton lehetőséget, és mentse az összes konfigurált mérőszámot. Ha elhagyja a lapot, és mentés nélkül visszatér hozzá, akkor a konfiguráció módosításai elvesznek. 

9. A Key Vault összes típusú műveletének figyeléséhez használja a **teljes szolgáltatás API találati** metrikáját, és válassza a **felosztás a tevékenység típusa szerint** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Képernyőkép: a felosztás alkalmazása gomb megjelenítése.](../media/alert-3.png)

10. A Key Vault-hibakódok figyeléséhez használja a **teljes szolgáltatás API eredmények** metrikáját, és válassza a **felosztás a tevékenység típusa szerint** lehetőséget.

> [!div class="mx-imgBorder"]
> ![A kiválasztott teljes szolgáltatás API-eredmények mérőszámát megjelenítő képernyőkép.](../media/alert-4.png)

Most egy irányítópult jelenik meg, amely így néz ki. Az egyes csempék jobb felső sarkában található 3 pontra kattintva átrendezheti és átméretezheti a csempéket a szükséges módon. 

Miután mentette és közzétette az irányítópultot, egy új erőforrást fog létrehozni az Azure-előfizetésében. A "megosztott irányítópult" kifejezésre keresve bármikor megtekintheti. 

> [!div class="mx-imgBorder"]
> ![A közzétett irányítópultot bemutató képernyőkép.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Riasztások konfigurálása a Key Vault 

Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a riasztásokat a kulcstartóban, hogy figyelmeztesse a csapatát, hogy azonnal tegyen lépéseket, ha a kulcstartó sérült állapotban van. Beállíthat olyan riasztásokat, amelyek e-mailt küldenek, lehetőleg egy csapat DL, egy Event Grid-értesítés vagy egy telefonszám hívása vagy SMS-címe. A statikus riasztásokat rögzített érték alapján is kiválaszthatja, vagy egy dinamikus riasztást, amely riasztást küld, ha egy figyelt metrika meghaladja a kulcstartó átlagos korlátját egy meghatározott időtartományon belül. 

> [!IMPORTANT]
> Vegye figyelembe, hogy az újonnan konfigurált riasztások akár 10 percet is igénybe vehetnek az értesítések küldésének megkezdéséhez. 

### <a name="configure-an-action-group"></a>Műveleti csoport konfigurálása 

A műveleti csoport az értesítések és a tulajdonságok konfigurálható listáját tartalmazza.

1. Jelentkezzen be az Azure Portalba.
2. **Riasztások** keresése a keresőmezőbe
3. **Kezelési műveletek** kiválasztása

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely kiemeli a műveletek kezelése gombot.](../media/alert-6.png)

4. Válassza a **+ műveleti csoport hozzáadása** lehetőséget

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: a + művelet csoport hozzáadása gomb.](../media/alert-7.png)

5. Válassza ki a műveleti csoport **Művelettípus típusát** . Ebben a példában egy e-mail-riasztást fogunk létrehozni.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: a műveleti csoport hozzáadásához szükséges mezők kiemelése.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: az e-mail vagy SMS-üzenet riasztásának hozzáadásához szükséges tudnivalók.](../media/alert-9.png)

6. Kattintson az **OK** gombra a lap alján. Sikeresen létrehozott egy műveleti csoportot. 

Most, hogy beállított egy műveleti csoportot, konfigurálni fogjuk a Key Vault riasztási küszöbértékeit. 

### <a name="configure-alert-thresholds"></a>Riasztási küszöbértékek konfigurálása 

1. Válassza ki a Key Vault-erőforrást a Azure Portalban, és válassza a **figyelés** alatt a **riasztások** elemet.

> [!div class="mx-imgBorder"]
> ![A figyelés szakasz riasztások menüpontját megjelenítő képernyőkép.](../media/alert-10.png)

2. **Új riasztási szabály** kiválasztása

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: az + új riasztási szabály gomb megjelenítése.](../media/alert-11.png)

3. Válassza ki a riasztási szabály hatókörét. Kiválaszthat egy vagy több tárat is. 

> [!IMPORTANT]
> Vegye figyelembe, hogy ha több tárolót választ ki a riasztások hatóköréhez, az összes kiválasztott tárolónak ugyanabban a régióban kell lennie. Külön riasztási szabályokat kell konfigurálnia a különböző régiókban lévő tárakhoz. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely bemutatja, hogyan választhat ki egy tárolót.](../media/alert-12.png)

4. Válassza ki a riasztások feltételeit. Az alábbi jelek bármelyikét kiválaszthatja, és meghatározhatja a riasztások logikáját. A Key Vault csapat az alábbi riasztási küszöbértékek konfigurálását javasolja. 

    + Key Vault rendelkezésre állás 100% alá csökken (statikus küszöb)
    + Key Vault késés nagyobb, mint 500ms (statikus küszöbérték) 
    + A tár teljes telítettsége nagyobb, mint 75% (statikus küszöbérték) 
    + A tár teljes telítettsége meghaladja az átlagot (dinamikus küszöbérték)
    + Az átlagnál nagyobb hibakódok összesen (dinamikus küszöbérték) 

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely megjeleníti a riasztási feltételek kiválasztásának helyét.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>1. példa: statikus riasztási küszöbérték konfigurálása késéshez

A **szolgáltatás teljes API-késésének** kiválasztása a jel neveként
> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely a szolgáltatás API-késésének teljes nevét mutatja.](../media/alert-14.png)

Tekintse meg a következő konfigurációs paramétereket.

+ A küszöbérték beállítása **statikusra** 
+ Az operátor beállítása **nagyobb, mint**
+ Az Összesítés típusának beállítása az **átlag** értékre
+ A küszöbérték **500** értékre állítása
+ Összesítési időszak beállítása **5 percre**
+ Az értékelés gyakoriságának **1 percre** állítása
+ Válassza a **kész** lehetőséget  

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely kiemeli a beállított riasztási logikát.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>2. példa: dinamikus riasztási küszöbérték konfigurálása a tár telítettségéhez 

Dinamikus riasztás használatakor megtekintheti a kiválasztott kulcstartó korábbi információit. A kék terület a kulcstartó átlagos használatát jelöli. A vörös felület olyan tüskéket mutat be, amelyek riasztást váltottak ki, ha a riasztás konfigurációjában más feltételek teljesülnek. A piros pontok olyan megsértési példányokat mutatnak be, amelyekben a riasztás feltételei teljesültek az összesített időszak során. Beállíthatja, hogy a riasztások bizonyos számú megsértést követően beállíthatók legyenek a megadott időn belül. Ha nem szeretné belefoglalni a múltbeli adatszolgáltatásokat, a speciális beállításokban az alábbi régi adatelemek is kizárhatók. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely a tár teljes telítettségének gráfját mutatja.](../media/alert-16.png)

Tekintse meg a következő konfigurációs paramétereket.

+ A küszöbérték beállítása **dinamikusra** 
+ Az operátor beállítása **nagyobb, mint**
+ Az Összesítés típusának beállítása az **átlag** értékre
+ A küszöbérték érzékenységének beállítása **közepesre**
+ Összesítési időszak beállítása **5 percre**
+ Az értékelés gyakoriságának **1 percre** állítása
+ Nem **kötelező** Speciális beállítások konfigurálása 
+ Válassza a **kész** lehetőséget

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Azure Portalról](../media/alert-17.png)

5. Adja hozzá a konfigurált műveleti csoportot

> [!div class="mx-imgBorder"]
> ![A műveleti csoport hozzáadását bemutató képernyőkép.](../media/alert-18.png)

6. A riasztás engedélyezése és a súlyosság kiosztása

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: a riasztás engedélyezésének és súlyosságának megadása.](../media/alert-19.png)

7. A riasztás létrehozása 

### <a name="example-email-alert"></a>Példa e-mail riasztásra 

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: az e-mail-riasztások konfigurálásához szükséges információk kiemelése.](../media/alert-20.png)

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen létrehozott egy figyelési irányítópultot és konfigurált riasztásokat a kulcstartóhoz. Ha követte a fenti lépéseket, e-mail-riasztásokat kell kapnia, amikor a kulcstartó megfelel a konfigurált riasztási feltételeknek. Erre mutat példát az alábbi ábra. Az ebben a cikkben beállított eszközök használatával aktívan figyelheti a kulcstartó állapotát. 


