---
title: A Microsoft Azure Data Box Heavy áttekintése | Microsoft Docs in data
description: Ismerteti az Azure Data Box nevű hibrid megoldást, amellyel nagy mennyiségű adat továbbítható az Azure-ba.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 2f1f01a8cfa25c222848e7cc5c86dc3532eba348
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437773"
---
# <a name="what-is-azure-data-box-heavy"></a>Mi az az Azure Data Box Heavy?

Azure Data Box Heavy lehetővé teszi, hogy egy gyors, olcsó és megbízható módon küldjön több száz terabájtos adatmennyiséget az Azure-ba. Az adatátvitelt az Azure-ba küldi el egy olyan Data Box Heavy-eszköz átadásával, amely 1 PB tárolókapacitással rendelkezik, amelyet az adataihoz tölt be, és vissza kell küldenie a Microsoftnak. Az eszköz robusztus burkolattal rendelkezik, amely védelmet nyújt az adatai számára az átvitel során.

Miután az eszköz megkapja az adatközpontot, állítsa azt a helyi webes felhasználói felület használatával. Másolja át a kiszolgálóin található adatokat az eszközre, és küldje vissza az eszközt az Azure-nak. Az Azure-adatközpontban az adatai feltöltve lettek az Azure Storage-fiók (ok) ba. A Azure Portal teljes végpontok közötti folyamatát nyomon követheti.


> [!IMPORTANT]
> - Az eszköz igényléséhez regisztráljon a [Azure Portal](https://portal.azure.com).


## <a name="use-cases"></a>Használati esetek

Data Box Heavy a legmegfelelőbb a több száz terabájtos adatméretekhez, ahol a hálózati kapcsolat nem elegendő az adatok Azure-ba való feltöltéséhez. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is. Íme a különböző forgatókönyvek, amelyekben a Data Box Heavy adatátvitelhez használható.

 - **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba.
     - Adathordozó-függvénytár áthelyezése offline szalagokról az Azure-ba egy online médiatár létrehozásához.
     - Telepítse át a VM-farmot, az SQL Servert és az alkalmazásokat az Azure-ba.
     - Az előzményeket az Azure-ba helyezheti részletes elemzésre és jelentésre a HDInsight használatával.

 - **Kezdeti tömeges átvitel** – Amikor a kezdeti tömeges átvitel a Data Box Heavy használatával történik (kiindulás), majd növekményes átvitelek történnek a hálózaton.
     - A Data Box Heavy és a Backup Solutions-partner például a kezdeti nagyméretű korábbi biztonsági másolatok Azure-ba való áthelyezésére szolgál. Miután ez befejeződött, a növekményes adatok a hálózaton keresztül lesznek továbbítva az Azure Storage-ba.

 - **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül.

## <a name="benefits"></a>Előnyök

Data Box Heavy úgy lett kialakítva, hogy nagy mennyiségű adatmennyiséget helyezzen át az Azure-ba, kevés a hálózatra gyakorolt hatás nélkül. A megoldás a következő előnyökkel jár:

- A **Speed** -Data Box Heavy nagy teljesítményű 40 – Gbps hálózati adaptereket használ.

- A **Security** -Data Box Heavy beépített biztonsági védelemmel rendelkezik az eszközhöz, az adatkezeléshez és a szolgáltatáshoz.
    - Az eszköz biztonsági tokkal rendelkezik illetéktelen hozzáférést gátló csavarokkal és az illetéktelen hozzáférést jelző matricákkal.
    - Az eszközön lévő adatokat minden pillanatban 256 bites AES-titkosítás védi.
    - Az eszköz zárolása csak az Azure Portalon megadott jelszóval oldható fel.
    - A szolgáltatást az Azure biztonsági funkciói védik.
    - Az adatok az Azure-ba való feltöltése után az eszközön lévő lemezeket a National Institute of Standards and Technology (NIST) 800 88r1 szabványoknak megfelelően törli a rendszer.


## <a name="features-and-specifications"></a>Funkciók és specifikációk

A Data Box Heavy eszköz ebben a kiadásban a következő funkciókkal rendelkezik.

| Specifikációk                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Tömeg                                                  | ~ 500 lbs. <br>Az eszköz, amely a kerekek zárolását a szállításhoz|
| Dimenziók                                              | Szélesség: 26 hüvelyk magasság: 28 cm hosszúság: 48 hüvelyk |
| Kiszolgálószekrény-terület                                              | Nem lehet állványra csatlakoztatni|
| Szükséges kábelek                                         | 4 megalapozott 120 V/10 A beépített tápkábelek (néma 5-15) <br> Az eszköz legfeljebb 240 V-os teljesítményt támogat, és C-13 energiaellátási tárolóval rendelkezik <br> A [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) kompatibilis hálózati kábelek használata  |
| Energiaellátás                                                    | 4 az eszközök csomópontjain megosztott beépített tápegységek (PSUs-EK) <br> 1 200 wattos átlagos Power Draw|
| Tárkapacitás                                        | ~ 1 – PB nyers, 70, egyenként 14 TB-os lemez <br> 770 – TB felhasználható kapacitás|
| Csomópontok száma                                          | 2 független csomópont/eszköz (500 TB) |
| Hálózati adapterek/csomópont                             | 4 hálózati adapter/csomópont <br><br> MGMT, DATA3 <ul><li> 2 X 1 – GbE felületek </li><li> A MGMT a felügyelet és a kezdeti beállítás, nem pedig a felhasználó által konfigurálható </li><li> A DATA3 felhasználó által konfigurálható és Dynamic Host Configuration Protocol (DHCP) alapértelmezés szerint</li></ul>DATA1, DATA2-adatillesztők <ul><li>2 X 40 – GbE felületek </li><li> Felhasználó által konfigurálható DHCP (alapértelmezett) vagy statikus</li></ul>|


## <a name="components"></a>Összetevők

A Data Box Heavy a következő összetevőket tartalmazza:

* **Data Box Heavy eszköz** – Egy biztonsági tokban lévő fizikai eszköz, amely biztonságosan tárolja az adatokat. Az eszköz 770 TB-os felhasználható tárolási kapacitással rendelkezik.
    
* **Data Box szolgáltatás** – Az Azure Portal bővítménye, amely segítségével a Data Box Heavy eszköz különböző földrajzi helyekről elérhető webes felületről kezelhető. A Data Box Heavy eszköz felügyeletéhez használja a Data Box szolgáltatást. A szolgáltatásban végrehajtható feladatok a rendelések létrehozását és kezelését, a riasztások megtekintését és kezelését, valamint a megosztások kezelését foglalják magukban.  

* **Helyi webes felhasználói felület** – Egy webes felhasználói felület, amelynek használatával az eszköz a helyi hálózathoz való csatlakozásra konfigurálható, majd regisztrálható a Data Box szolgáltatásban. A helyi webes felhasználói felülettel emellett leállíthatja és újraindíthatja az eszközt, megtekintheti a másolási naplókat, valamint szolgáltatáskérések elküldése érdekében kapcsolatba léphet a Microsoft ügyfélszolgálatával.


## <a name="the-workflow"></a>A munkafolyamat

A munkafolyamat általában az alábbi lépésekből áll:

1. **Megrendelés** – Hozzon létre egy megrendelést az Azure Portalon, adja meg a szállítási adatokat és az adatokhoz célként használandó Azure-tárfiókot. Ha az eszköz elérhető, az Azure előkészíti és postázza az eszközt egy nyomkövetési azonosítóval együtt.

2. **Átvétel** – Miután az eszköz megérkezett, csatlakoztassa a hálózatra és a tápellátáshoz a mellékelt kábelekkel. Kapcsolja be az eszközt, és csatlakozzon hozzá. Konfigurálja a hálózatot az eszközön, majd csatlakoztasson megosztásokat a gazdagépre, amelyről az adatokat másolni szeretné.

3. **Adatok másolása** – Másolja az adatokat a Data Box Heavy-megosztásokra.

4. **Visszaküldés** – Készítse elő, kapcsolja ki, majd küldje vissza az eszközt az Azure-adatközpontba.

5. **Feltöltés** – Az eszközről az adatok automatikusan át lesznek másolva az Azure-ba. Az eszköz lemezeit a National Institute of Standards and Technology (NIST) útmutatásait követve biztonságosan töröljük.

A folyamat során e-mailben értesítjük az összes állapotadatok változásáról.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Data Box Heavy a szolgáltatás üzembe helyezése, az adott ország/régió, valamint az adatok átviteléhez használt cél Azure Storage-fiók alapján tudja átvinni az adatok átvitelét.

- **A szolgáltatás rendelkezésre állása** – Ebben a kiadásban a Data Box Heavy a következő régiókban érhető el:
    - Az Egyesült Államok minden nyilvánosfelhő-régiója: az USA nyugati középső régiója, az USA 2. nyugati régiója, az USA nyugati régiója, az USA déli középső régiója, az USA középső régiója, az USA északi középső régiója, az USA keleti régiója és az USA 2. keleti régiója.
    - Európai Unió: Nyugat-Európa és Észak-Európa.
    - Egyesült Királyság: az Egyesült Királyság déli régiója és az Egyesült Királyság nyugati régiója.
    - Franciaország: Közép-Franciaország és Dél-Franciaország.

- **Cél tárfiókok** – Az adatokat tároló tárfiókok az összes olyan Azure-régióban elérhetők, amelyben a szolgáltatás is.

Az Data Box Heavy régió rendelkezésre állásának legfrissebb információit az [Azure-termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Regisztráció

A Data Box Heavy regisztrálásához hajtsa végre a következő lépéseket:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com).
2. Kattintson az **+ erőforrás létrehozása** lehetőségre egy új erőforrás létrehozásához. Keressen az **Azure Data Box** kifejezésre. Válassza az **Azure Data Box** szolgáltatást.
3. Kattintson a **Létrehozás** lehetőségre.
4. Válassza ki a Data Box Heavyhoz használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Heavy-erőforrást. A **Data Box Heavy** lehetőségnél kattintson a **Regisztráció** elemre.
5. Válaszoljon az adatforgalomra, a hálózati sávszélességre és az adatátviteli gyakoriságra vonatkozó, az adatok tartózkodási országával/régiójával, az időkerettel és az Azure-szolgáltatással kapcsolatos kérdésekre. Tekintse át az adatvédelmi és felhasználási feltételeket, és a négyzet bejelölésével járuljon hozzá, hogy a Microsoft használhassa az e-mail-címét az Önnel való kapcsolatfelvételhez.

Ha már regisztrált, megrendelhet egy Data Box Heavy.

    
