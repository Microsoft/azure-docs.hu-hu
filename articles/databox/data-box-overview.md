---
title: A Microsoft Azure Data Box áttekintése | Microsoft Docs
description: A cikk ismerteti az Azure Data Box nevű felhőalapú megoldást, amellyel hatalmas mennyiségű adat vihető át az Azure-ba
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 13c07da7393c70bb1fc61930dd1179994c68ed75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389807"
---
# <a name="what-is-azure-data-box"></a>Mi az az Azure Data Box?

A Microsoft Azure Data Box Cloud megoldás lehetővé teszi, hogy gyors, költséges és megbízható módon küldjön terabájtos adatmennyiséget az Azure-ba. A biztonságos adatátvitelt egy saját fejlesztésű Data Box tárolóeszköz küldésével gyorsítjuk fel. Minden tárolóeszköz legfeljebb 80 TB használható tárolókapacitással rendelkezik, és egy regionális futár szállítja az Ön adatközpontjába. Az eszköz ütésálló tokkal rendelkezik, hogy az adatok az átvitel során védve legyenek.

Az Azure-ból az adatok importálásához vagy exportálásához a Azure Portal segítségével rendelhet Data Box eszközt. Miután az eszköz megérkezett, gyorsan beállíthatja a helyi webes felhasználói felülettel. Attól függően, hogy importálja vagy exportálja az adatait, másolja át az adatait a kiszolgálókról az eszközre vagy az eszközről a kiszolgálókra, és az eszközt visszaküldi az Azure-ba. Ha az Azure-adatközpontban az Azure-ba importálja az adatait, a rendszer automatikusan feltölti az adatait az eszközről az Azure-ba. A teljes folyamatot végigkövetheti a Data Box szolgáltatásban, az Azure Portalon.

## <a name="use-cases"></a>Használati esetek

A Data Box ideális megoldás 40 TB-nál nagyobb mennyiségű adat átviteléhez olyan forgatókönyvek esetében, amelyekben korlátozott a hálózati kapcsolat. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is. 

Az alábbi forgatókönyvek az adatok Azure-ba történő importálásához használható Data Boxeket ismertetik.

 - **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba. 
     - Médiatár áthelyezése offline szalagokról az Azure-ba és egy online médiatár létrehozása.
     - A VM-farm, az SQL Server és az alkalmazások migrálása az Azure-ba
     - Előzményadatok áthelyezése az Azure-ba a HDInsight használatával történő átfogó elemzéshez és jelentéskészítéshez

 - **Kezdeti tömeges átvitel** – Amikor a kezdeti tömeges átvitel a Data Box használatával történik (kiindulás), majd növekményes átvitelek történnek a hálózaton. 
     - Például a biztonsági mentési partnerek, mint a Commvault vagy a Data Box segítségével történik a nagy méretű biztonsági mentési előzményadatok kezdeti átvitele az Azure-ba. Miután ez befejeződött, a növekményes adatok a hálózaton keresztül lesznek továbbítva az Azure Storage-ba.

- **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül. 

Az alábbi forgatókönyvek az Azure-ból származó adatok exportálására szolgáló Data Box használhatók.

- Vész- **helyreállítás** – ha az Azure-ból származó adatok egy példánya egy helyszíni hálózatra lett visszaállítva. Egy tipikus vész-helyreállítási forgatókönyv esetén nagy mennyiségű Azure-adatmennyiséget exportál egy Data Boxba. A Microsoft ezután leszállítja ezt a Data Box, és rövid idő alatt visszaállítja az adatait a helyszínen.

- **Biztonsági követelmények** – ha a kormányzati vagy biztonsági követelmények miatt el kell tudnia érni az Azure-ból történő adatexportálást. Az Azure Storage például az Egyesült Államok titkos és titkos felhők területén érhető el, és a Data Box segítségével exportálhatja az Azure-ból az adatmennyiséget. 

- **Áttelepítés a helyszíni rendszerbe vagy egy másik felhőalapú** szolgáltatóba – ha az összes adat visszahelyezését a helyszíni rendszerbe vagy egy másik felhőalapú szolgáltatóba kívánja áthelyezni, exportálja az adatok Data Box használatával a munkaterhelések áttelepítéséhez.


## <a name="benefits"></a>Előnyök

A Data Box nagy mennyiségű adat az Azure-ba való átvitelére szolgál, és minimális hatással van a hálózat működésére. A megoldás a következő előnyökkel jár:

- A **Speed** -Data Box 1 vagy 10 GB/s hálózati adaptereket használ az Azure-ba és az Azure-ba irányuló akár 80 TB-os adatátvitelre.

- **Biztonság** – A Data Box beépített védelmet biztosít az eszköz, az adatok és a szolgáltatás számára.
  - Az eszköz biztonsági tokkal rendelkezik illetéktelen hozzáférést gátló csavarokkal és az illetéktelen hozzáférést jelző matricákkal. 
  - Az eszközön lévő adatokat minden pillanatban 256 bites AES-titkosítás védi.
  - Az eszköz zárolása csak az Azure Portalon megadott jelszóval oldható fel.
  - A szolgáltatást az Azure biztonsági funkciói védik.
  - Miután az importálási rendelés adatait feltölti az Azure-ba, az eszközön lévő lemezeket a rendszer a NIST 800-88r1 szabványoknak megfelelően törli. Exportálási rendelés esetén a lemezek törlődnek, ha az eszköz eléri az Azure-adatközpontot.
    
    További információt [az Azure Data Box biztonsági és adatvédelmi szolgáltatásait](data-box-security.md) ismertető cikkben talál.

## <a name="features-and-specifications"></a>Funkciók és specifikációk

A Data Box eszköz jelen kiadása az alábbi funkciókkal rendelkezik.

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Tömeg                                                  | < 22,7 kg                |
| Dimenziók                                              | Eszköz – szélesség: 309 mm; magasság: 430,4 mm; mélység: 502 mm |            
| Kiszolgálószekrény-terület                                              | 7 U a kiszolgálószekrénybe helyezve, az oldalára állítva (nem szerelhető rá az állványra)|
| Szükséges kábelek                                         | 1 db tápkábel (a csomag tartalmazza) <br> 2 db RJ45-kábel <br> 2 db SFP+ Twinax-rézkábel|
| Tárkapacitás                                        | 100 – a TB-os eszközön 80 TB vagy felhasználható kapacitás van a RAID 5 védelme után|
| Energiaellátási minősítés                                            | Az áramellátási egység 700 W-ra van értékelve. <br> Az egység jellemzően a 375 W-ot hívja meg.|
| Hálózati adapterek                                      | 2 X 1 – GbE felület – MGMT, adat3. <br> MGMT – a kezeléshez, felhasználó által nem konfigurálható, a kezdeti beállításhoz szükséges <br> DATA3 – az adatokhoz, felhasználó által konfigurálható, alapértelmezetten dinamikus <br> Az MGMT és a DATA 3 is működhet 10 GbE-ként <br> 2 X 10 – GbE felület – 1. adattábla, 2. adattábla <br> Mindkettő az adatokhoz, konfigurálhatók dinamikusra (alapértelmezés) vagy statikusra |
| Adatátvitel                                      | Az Importálás és az Exportálás is támogatott.  |
| Adatátviteli felület                                     | RJ45, SFP + 10 GbE réz Ethernet  |
| Biztonság                                                | Ütésálló eszköztok, az illetéktelen hozzáférést gátló, egyedi csavarokkal <br> Az illetéktelen hozzáférést jelző matricák az eszköz alján|
| Adatátviteli sebesség                                      | Akár 80 TB-os nap egy 10 GbE hálózati adapteren keresztül        |
| Kezelés                                              | Helyi webes felhasználói felület – egyszeri kezdeti beállítás és konfigurálás <br> Azure Portal – mindennapos eszközkezelés        |

## <a name="data-box-components"></a>Data Box-összetevők

A Data Box a következő összetevőket tartalmazza:

* **Data Box eszköz** – olyan fizikai eszköz, amely elsődleges tárolót biztosít, kezeli a Felhőbeli tárolással folytatott kommunikációt, és segít az eszközön tárolt összes adat biztonságának és titkosságának biztosításában. A Data Box eszköz 80 TB hasznos tárolókapacitással rendelkezik. 

    ![A Data Box elöl- és hátulnézete](media/data-box-overview/data-box-combined.png)

    
* **Data Box szolgáltatás** – Az Azure Portal bővítménye, amely lehetővé teszi a Data Box eszközök kezelését egy webes felületről, amelyet eltérő földrajzi helyekről is elérhet. A Data Box szolgáltatással elvégezheti a Data Box eszköz napi adminisztrálását. A szolgáltatásban végrehajtható feladatok a rendelések létrehozását és kezelését, a riasztások megtekintését és kezelését, valamint a megosztások kezelését foglalják magukban.  

    ![A Data Box szolgáltatás az Azure Portalon](media/data-box-overview/data-box-service.png)

    További információt [a Data Box szolgáltatás a Data Box eszköz felügyeletére történő használatát](data-box-portal-ui-admin.md) ismertető cikkben talál.

* **Helyi webes felhasználói felület** – Egy webes felhasználói felület, amelynek használatával az eszköz a helyi hálózathoz való csatlakozásra konfigurálható, majd regisztrálható a Data Box szolgáltatásban. A helyi webes felhasználói felülettel emellett leállíthatja és újraindíthatja a Data Box eszközt, megtekintheti a másolási naplókat, valamint szolgáltatáskérések elküldése érdekében kapcsolatba léphet a Microsoft ügyfélszolgálatával.

    ![A Data Box helyi webes felhasználói felülete](media/data-box-overview/data-box-local-web-ui.png)

    A webalapú felhasználói felület használatáról a [Data Box a webalapú felhasználói felülettel végzett felügyeletéről](data-box-portal-ui-admin.md) szóló cikkből tájékozódhat.

## <a name="the-workflow"></a>A munkafolyamat

Egy tipikus importálási folyamat a következő lépéseket tartalmazza:

1. **Megrendelés** – Hozzon létre egy megrendelést az Azure Portalon, adja meg a szállítási adatokat és az adatokhoz célként használandó Azure-tárfiókot. Ha az eszköz elérhető, az Azure előkészíti és postázza az eszközt egy nyomkövetési azonosítóval együtt.

2. **Átvétel** – Miután az eszköz megérkezett, csatlakoztassa a hálózatra és a tápellátáshoz a mellékelt kábelekkel. Kapcsolja be az eszközt, és csatlakozzon hozzá. Konfigurálja a hálózatot az eszközön, majd csatlakoztasson megosztásokat a gazdagépre, amelyről az adatokat másolni szeretné.

3. **Adatok másolása** – Másolja az adatokat a Data Box-megosztásokra.

4. **Visszaküldés** – Készítse elő, kapcsolja ki, majd küldje vissza az eszközt az Azure-adatközpontba.

5. **Feltöltés** – Az eszközről az adatok automatikusan át lesznek másolva az Azure-ba. Az eszköz lemezeit a National Institute of Standards and Technology (NIST) útmutatásait követve biztonságosan töröljük.

A folyamat alatt minden lépéséről e-mailben kap értesítést. További információt a részletes folyamatról a [Data Box az Azure Portalon történő üzembe helyezését](data-box-deploy-ordered.md) ismertető cikkben találhat.


Egy tipikus exportálási folyamat a következő lépéseket tartalmazza:

1. **Rendelés** – exportálási sorrend létrehozása a Azure Portalban, adja meg a szállítási adatokat, valamint az adatok forrás Azure Storage-fiókját. Ha az eszköz elérhető, az Azure előkészíti az eszközt. Az adatok az Azure Storage-fiókból a Data Boxba másolódnak. Az Adatmásolás befejezése után a Microsoft az eszközt szállítólevél-követési AZONOSÍTÓval szállítja.

2. **Átvétel** – Miután az eszköz megérkezett, csatlakoztassa a hálózatra és a tápellátáshoz a mellékelt kábelekkel. Kapcsolja be az eszközt, és csatlakozzon hozzá. Konfigurálja az eszköz hálózatát és a csatlakoztatási megosztásokat azon a gazdagépen, amelyre másolni kívánja az adatfájlokat.

3. **Adatok másolása** – adatok másolása Data Box-megosztásokból a helyszíni adatkiszolgálókra.

4. **Visszaküldés** – Készítse elő, kapcsolja ki, majd küldje vissza az eszközt az Azure-adatközpontba.

5. **Adattörlés** – az eszközök lemezeit a National Institute of Standards and Technology (NIST) irányelvek alapján biztonságosan törli a rendszer.

Az exportálási folyamat során e-mailben értesítjük az összes állapotadatok változásáról. További információt a részletes folyamatról a [Data Box az Azure Portalon történő üzembe helyezését](data-box-deploy-export-ordered.md) ismertető cikkben találhat.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Data Box a szolgáltatás üzembe helyezése, az eszközt szállító ország vagy régió, valamint az adatok átviteléhez használt cél Azure Storage-fiók alapján továbbítja az adatok átvitelét.

### <a name="for-import"></a>Importáláshoz

- **Szolgáltatás rendelkezésre állása** – ha az importálási vagy exportálási megrendelésekhez Data Box használ, a régió rendelkezésre állásával kapcsolatos információk eléréséhez nyissa meg az [Azure-termékeket régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Importálási rendelések esetén Data Box a Azure Government felhőben is üzembe helyezhető. További információ: [Mi az Azure Government?](../azure-government/documentation-government-welcome.md) 

- **Cél Storage-fiókok** – az adattárolást tároló fiókok az összes Azure-régióban elérhetők, ahol a szolgáltatás elérhető.


## <a name="next-steps"></a>Következő lépések

- A [Data Box rendszerkövetelményeinek](data-box-system-requirements.md) áttekintése.
- A [Data Box korlátjainak](data-box-limits.md) értelmezése.
- Az [Azure Data Box](data-box-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
