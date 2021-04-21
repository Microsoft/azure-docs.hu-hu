---
title: Azure Key Vault monitorozási és riasztási | Microsoft Docs
description: Hozzon létre egy irányítópultot a kulcstartó állapotának figyelése és a riasztások konfigurálása érdekében.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: f8f9dd6d51b974ebd31804daf0402ca5535ffc92
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751579"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorozás és riasztás a Azure Key Vault

## <a name="overview"></a>Áttekintés

Miután elkezdte használni a Key Vaultot az éles környezetben használt titkos kulcsok tárolására, fontos monitorozni a kulcstartó állapotát, hogy a szolgáltatás a kívánt működést biztosítsa. Ahogy elkezdi skálázni a szolgáltatást, a kulcstartóba küldött kérések száma nőni fog. Ez megnövelheti a kérések késését, szélsőséges esetben pedig a kérelmek szabályozását okozhatja, ami hatással lesz a szolgáltatás teljesítményére. Akkor is riasztást kell kapnia, ha a kulcstartó szokatlan számú hibakódot küld, így gyorsan értesítést kaphat a hozzáférési szabályzatokkal vagy a tűzfalkonfigurációval kapcsolatos problémákról. Ez a dokumentum a következő témaköröket tartalmazza:

+ Monitor Key Vault alapszintű metrikák
+ Metrikák konfigurálása és irányítópult létrehozása
+ Riasztások létrehozása megadott küszöbértékekkel

Azure Monitor a Key Vault naplókat és metrikákat kombinálva biztosít egy globális monitorozási megoldást. [További információ a Azure Monitor való Key Vault itt](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>Monitor Key Vault alapszintű metrikák

+ Tároló rendelkezésre állása  
+ Tároló telítettség 
+ Szolgáltatási API késése 
+ Szolgáltatási API-találatok összesen (szűrés tevékenységtípus szerint) 
+ Hibakódok (szűrés állapotkód alapján) 

**Tároló rendelkezésre állása** – Ennek a metrikának mindig 100%-osnak kell lennie, ez egy fontos monitorozható metrika, mivel gyorsan megmutathatja, ha a kulcstartóban szolgáltatáskimaradás történt. 

**Tárolótelítettség** – A kulcstartó által kiszolgált kérelmek másodpercenkénti száma a végrehajtott művelet típusától függ. Egyes tárolóműveletek másodpercenkénti kérelmekre vonatkozó küszöbértéke alacsonyabb. Ez a metrika összesíti a kulcstartó összes művelettípusra vonatkozó teljes használatát, hogy az aktuális kulcstartó-használatot jelző százalékos értéket adja meg. A Key Vault szolgáltatás korlátainak teljes listáját az alábbi dokumentumban láthatja. [Az Azure Key Vault szolgáltatáskorlátozásai](service-limits.md)

**Szolgáltatási API késése** – Ez a metrika a Key Vaulthoz való hívások átlagos késését mutatja a szolgáltatásban mérve. Nem tartalmazza az ügyfél vagy a hálózat által az ügyfél és a szolgáltatás között felhasznált időt.

**Összes API-találat** – Ez a metrika a kulcstartóhoz történt összes hívást megjeleníti. Ez segít azonosítani, hogy mely alkalmazások hívják a kulcstartót. 

**Hibakódok** – Ez a metrika megmutatja, ha a kulcstartóban szokatlan mennyiségű hiba tapasztalható. A hibakódok teljes listájáért és a hibaelhárítási útmutatóért tekintse meg a következő dokumentumot. [Azure Key Vault REST API hibakódok](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Metrikák konfigurálása és irányítópult létrehozása

1. Jelentkezzen be az Azure Portalba.
2. Lépjen a Key Vault
3. A **Monitorozás alatt válassza** a Metrikák **lehetőséget** 

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Monitorozás szakasz Metrikák lehetőségének kiemelésével.](../media/alert-1.png)

4. Frissítse a diagram címét az irányítópulton látni kívánt címre. 
5. Válassza ki a hatókört. Ebben a példában egyetlen kulcstartót választunk ki. 
6. Válassza ki a Metric Overall Vault Availability and Aggregation Avg (Metrika **általános rendelkezésre állása és** összesítése **átlaga) lehetőséget** 
7. Frissítse az időtartományt az Elmúlt 24 órára, és frissítse az idő részletességét 1 percre. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Overall Vault Availablility metrikáról.](../media/alert-2.png)

8. Ismételje meg a fenti lépéseket a Tároló telítettség és a Szolgáltatási API késése metrikáknál. Válassza **a Rögzítés az irányítópulton** lehetőséget a metrikák irányítópultba mentéshez. 

> [!IMPORTANT]
> Válassza a Rögzítés az irányítópulton lehetőséget, és mentse az összes konfigurált metrikát. Ha elhagyja az oldalt, és mentés nélkül tér vissza hozzá, a konfigurációs módosítások elvesznek. 

9. A kulcstartón található összes művelettípus monitorozásához használja a **Total Service API Hits** metrikát, és válassza a Felosztás alkalmazása **tevékenységtípus szerint lehetőséget.**

> [!div class="mx-imgBorder"]
> ![A Felosztás alkalmazása gombot bemutató képernyőkép.](../media/alert-3.png)

10. A kulcstartó hibakódjának monitorozásához használja a **Total Service API Results** (Szolgáltatási API-eredmények összesen) metrikát, és válassza a Felosztás alkalmazása **tevékenységtípus szerint lehetőséget.**

> [!div class="mx-imgBorder"]
> ![A Kiválasztott Total Service API Results (Szolgáltatási API-eredmények összesen) metrikát bemutató képernyőkép.](../media/alert-4.png)

Most egy ehhez hasonló irányítópultot fog tartalmazni. Az egyes csempék jobb felső részen található 3 pontra kattintva szükség szerint átrendezheti és átméretezheti a csempéket. 

Miután menti és közzéteszi az irányítópultot, az létrehoz egy új erőforrást az Azure-előfizetésében. A "megosztott irányítópult" kereséssel bármikor láthatja. 

> [!div class="mx-imgBorder"]
> ![A közzétett irányítópultot bemutató képernyőkép.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Riasztások konfigurálása a Key Vault 

Ez a szakasz bemutatja, hogyan konfigurálhatja a riasztásokat a kulcstartón, hogy a csapat azonnal el tudja kezdeni a műveletet, ha a kulcstartó nem megfelelő állapotban van. Konfigurálhat olyan riasztásokat, amelyek lehetőleg egy csapat DL-nek küldenek e-mailt, eseményrács-értesítést küldenek, vagy telefonhívást vagy SMS-t küldenek egy telefonszámra. Statikus riasztásokat is választhat egy rögzített érték alapján, vagy egy dinamikus riasztást, amely riasztást küld, ha egy figyelt metrika bizonyos számú alkalommal túllépi a kulcstartó átlagos korlátját egy meghatározott időtartományon belül. 

> [!IMPORTANT]
> Vegye figyelembe, hogy akár 10 percet is igénybe vehet, hogy az újonnan konfigurált riasztások elkezdenek értesítéseket küldeni. 

### <a name="configure-an-action-group"></a>Műveletcsoport konfigurálása 

A műveletcsoport az értesítések és tulajdonságok konfigurálható listája.

1. Jelentkezzen be az Azure Portalba.
2. Riasztások **keresése** a keresőmezőben
3. Válassza a **Műveletek kezelése lehetőséget**

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Műveletek kezelése gomb kiemelésről.](../media/alert-6.png)

4. Válassza **a + Műveletcsoport hozzáadása lehetőséget**

> [!div class="mx-imgBorder"]
> ![Képernyőkép a + Műveletcsoport hozzáadása gomb kiemelésről.](../media/alert-7.png)

5. Válassza ki **a műveletcsoport** művelettípusát. Ebben a példában egy e-mailes riasztást hozunk létre.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a műveletcsoport hozzáadásához szükséges mezők kiemeléséhez.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Az e-mail- vagy SMS-riasztás hozzáadásához szükséges kódot bemutató képernyőkép.](../media/alert-9.png)

6. Kattintson az **OK** gombra a lap alján. Sikeresen létrehozott egy műveletcsoportot. 

Most, hogy konfigurált egy műveletcsoportot, konfiguráljuk a Key Vault riasztási küszöbértékét. 

### <a name="configure-alert-thresholds"></a>Riasztási küszöbértékek konfigurálása 

1. Válassza ki a key vault-erőforrást a Azure Portal a **Figyelés** alatt válassza a **Riasztások lehetőséget**

> [!div class="mx-imgBorder"]
> ![A Figyelés szakasz Riasztások menüpontját bemutató képernyőkép.](../media/alert-10.png)

2. Válassza az **Új riasztási szabály lehetőséget**

> [!div class="mx-imgBorder"]
> ![Az + Új riasztási szabály gombot bemutató képernyőkép.](../media/alert-11.png)

3. Válassza ki a riasztási szabály hatókörét. Kiválaszthat egy vagy több tárolót is. 

> [!IMPORTANT]
> Vegye figyelembe, hogy ha több tárolót választ ki a riasztások hatóköreként, az összes kiválasztott tárolónak ugyanabban a régióban kell lennie. A különböző régiókban található tárolókhoz külön riasztási szabályokat kell konfigurálnia. 

> [!div class="mx-imgBorder"]
> ![A tároló kiválasztását bemutató képernyőkép.](../media/alert-12.png)

4. Válassza ki a riasztások feltételeit. Az alábbi jelek bármelyikét kiválaszthatja, és meghatározhatja a riasztási logikát. A Key Vault a következő riasztási küszöbértékek konfigurálását javasolja. 

    + Key Vault rendelkezésre állás 100% alá csökken (statikus küszöbérték)
    + Key Vault késés nagyobb, mint 500 ezredm (statikus küszöbérték) 
    + A teljes tárolótelítettség meghaladja a 75%-ot (statikus küszöbérték) 
    + A teljes tárolótelítettség meghaladja az átlagot (dinamikus küszöbérték)
    + Az átlagnál magasabb hibakódok összesen (dinamikus küszöbérték) 

> [!div class="mx-imgBorder"]
> ![A riasztások feltételeinek kiválasztását bemutató képernyőkép.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>1. példa: Statikus riasztási küszöbérték konfigurálása a késéshez

A **jel neveként válassza** a Teljes szolgáltatási API késése lehetőséget
> [!div class="mx-imgBorder"]
> ![Képernyőkép a Teljes szolgáltatási API késési jelének nevéről.](../media/alert-14.png)

Tekintse meg az alábbi konfigurációs paramétereket.

+ A Küszöbérték beállítása **statikusra** 
+ Állítsa az Operátort **Nagyobb, mint**
+ Az Összesítés típusa beállításnál adja meg az **Átlagot**
+ A Küszöbérték beállítása **500-ra**
+ Az Összesítési időszak beállítása **5 percre**
+ A kiértékelési gyakoriságot **állítsa 1 percre**
+ Válassza a **Kész lehetőséget**  

> [!div class="mx-imgBorder"]
> ![Képernyőkép a konfigurált riasztási logikáról.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>2. példa: Dinamikus riasztási küszöbérték konfigurálása tárolótelítettséghez 

Dinamikus riasztás használata esetén láthatja a kiválasztott kulcstartó előzményadatát. A kék terület a kulcstartó átlagos használatát jelöli. A piros terület olyan kiugró csúcsokat mutat, amelyek riasztást váltottak volna ki, feltéve, hogy a riasztás konfigurációjában más feltételek is teljesültek. A piros pont olyan szabálysértések példányait mutatja, amelyekben a riasztásra vonatkozó feltételek teljesültek az összesített időablakban. Beállíthatja, hogy egy adott számú szabálysértés után egy adott idő alatt riasztást állítson be. Ha nem szeretné a korábbi adatokat is szerepeletni, az alábbi speciális beállításokban kizárhatja a régi adatokat. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép a teljes tárolótelítettség grafikonján.](../media/alert-16.png)

Tekintse meg az alábbi konfigurációs paramétereket.

+ A Küszöbérték beállítása **dinamikusra** 
+ Az Operátor beállítása **Nagyobb, mint**
+ Az Összesítés típusa beállításnál adja meg az **Átlagot**
+ Állítsa a Küszöbérték érzékenysége beállítását **Közepesre**
+ Az Összesítési időszak beállítása **5 percre**
+ A kiértékelési gyakoriságot **állítsa 1 percre**
+ **Nem kötelező** Speciális beállítások konfigurálása 
+ Válassza a **Kész lehetőséget**

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Azure Portal](../media/alert-17.png)

5. A konfigurált műveletcsoport hozzáadása

> [!div class="mx-imgBorder"]
> ![A műveletcsoport hozzáadását bemutató képernyőkép.](../media/alert-18.png)

6. A riasztás engedélyezése és súlyosság hozzárendelése

> [!div class="mx-imgBorder"]
> ![A riasztás engedélyezésének és a súlyosság hozzárendelésének helyét bemutató képernyőkép.](../media/alert-19.png)

7. A riasztás létrehozása 

### <a name="example-email-alert"></a>Példa e-mail-riasztásra 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az e-mail-riasztás konfiguráláshoz szükséges információkról.](../media/alert-20.png)

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen létrehozott egy monitorozási irányítópultot és konfigurálta a riasztásokat a kulcstartóhoz! Ha a fenti lépések mindegyikét követte, e-mailes riasztásokat kell kapnia, ha a kulcstartó megfelel a konfigurált riasztási feltételeknek. Erre mutat példát az alábbi ábra. Használja a cikkben beállított eszközöket a kulcstartó állapotának aktív figyelése érdekében. 


