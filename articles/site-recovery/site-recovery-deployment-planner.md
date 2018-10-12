---
title: Azure Site Recovery Deployment Planner VMware Azure-hoz| Microsoft Docs
description: Ez az Azure Site Recovery Deployment Planner felhasználói útmutatója
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: garavd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: nisoneji
ms.openlocfilehash: a04a372e52c682af428938514eac5d5e0f4274f0
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093714"
---
# <a name="azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Azure Site Recovery Deployment Planner VMware – Azure-hoz
Ez a cikk az Azure Site Recovery Deployment Planner felhasználói útmutatója a VMware–Azure éles környezetben való üzembe helyezéséhez.

## <a name="overview"></a>Áttekintés

A VMware virtuális gépek Azure Site Recoveryvel történő védelmének megkezdése előtt elegendő sávszélességet kell kiosztania a napi szintű adatváltozások alapján, hogy elérje a kívánt helyreállítási időkorlátot (RPO). Ügyeljen arra, hogy üzembe helyezze a megfelelő számú helyszíni konfigurációs és folyamatkiszolgálót.

Létre kell hoznia a megfelelő típusú és számú cél Azure-tárfiókot is. Hozzon létre standard vagy prémium szintű tárfiókokat a fokozatosan növekvő használat során megnövekedett éles kiszolgálók miatt. A tárolási típust virtuális gépenként válassza ki, a számítási feladatok jellemzői (például olvasási és írási, bemeneti és kimeneti műveletek másodpercenként [IOPS], vagy adatváltozás) és Site Recovery-korlátok alapján.

 A Site Recovery üzembehelyezés-tervező egy, a Hyper-V – Azure és VMware – Azure vészhelyreállítási forgatókönyvekben elérhető parancssori eszköz. Az eszközzel (az éles környezet megzavarása nélkül) távolról készítheti el VMware virtuális gépek profilját, és megismerheti a sávszélességet és a tárolási követelményeket a sikeres replikálás és feladatátvételi teszt érdekében. Az eszközt helyszíni Site Recovery összetevők telepítése nélkül is futtathatja. Az elért átviteli sebesség pontos meghatározása érdekében futtassa a tervezőt egy olyan Windows Serveren, amely teljesíti annak a Site Recovery konfigurációs kiszolgálónak a minimális követelményeit, amelyet végül telepítenie kell az éles környezetben való üzembe helyezés egyik első lépéseként.

Az eszköz a következő részleteket biztosítja:

**Kompatibilitási felmérés**

* A virtuális gép jogosultságfelmérése a lemezszám, a lemezméretek, az IOPS, a forgalom, a rendszerindítási típus (EFI/BIOS) és az operációs rendszer verziója alapján

**A hálózatisávszélesség-igény és RPO-elemzés**

* A változásreplikáláshoz szükséges becsült hálózati sávszélesség
* Átviteli sebesség a Site Recovery számára a helyszíni rendszerek és az Azure között
* A kötegelendő virtuális gépek száma a becsült sávszélesség alapján a kezdeti replikálás adott időn belüli végrehajtásához
* Az adott sávszélesség esetén elérhető RPO
* A kívánt RPO-ra gyakorolt hatás kisebb sávszélesség kiosztása esetén

**Azure infrastruktúra-követelmények**

* A tárolótípus (standard vagy prémium szintű tárfiók) követelménye az egyes virtuális gépekhez
* A replikáláshoz beállítandó standard és prémium szintű tárfiókok teljes száma
* Tárfiókok elnevezési javaslatai a Storage útmutatója alapján
* Az összes virtuális gép tárfiókjának elhelyezése
* A feladatátvételi teszt vagy feladatátvétel előtt beállítandó Azure-magok száma az előfizetésen
* Az Azure virtuális gépek javasolt mérete az egyes helyszíni virtuális gépeknél

**Helyszíni infrastruktúra-követelmények**
* A megfelelő számú helyszíni konfigurációs és folyamatkiszolgáló üzembe helyezése

**Becsült vészhelyreállítási költségek az Azure-hoz**
* A becsült teljes vészhelyreállítási költség Azure-hoz: számítás, tárolás, hálózat és a Site Recovery licencköltsége
* Részletes költségelemzés virtuális gépenként


>[!IMPORTANT]
>
>Mivel a használat idővel megnövekszik, az eszközben minden előzetes számítás a munkaterhelési jellemzők 30 százalékos növekedési tényezőjével történik. Emellett a rendszer a profilkészítési mérőszámok 95%-át veszi alapul, mint például írási/olvasási IOPS és adatváltozás. A növekedési tényező és a százalékérték is konfigurálható. További információkat a növekedési tényezőről „A növekedési tényezővel kapcsolatos szempontok” szakaszban találhat. További információkat a százalékértékről „A számításhoz használt százalékérték” szakaszban találhat.
>

## <a name="support-matrix"></a>Támogatási mátrix

| | **VMware – Azure** |**Hyper-V – Azure**|**Azure – Azure**|**Hyper-V – másodlagos hely**|**VMware – másodlagos hely**
--|--|--|--|--|--
Támogatott esetek |Igen|Igen|Nem|Igen*|Nem
Támogatott verzió | vCenter 6.5, 6.0 vagy 5.5| Windows Server 2016, Windows Server 2012 R2 | NA |Windows Server 2016, Windows Server 2012 R2|NA
Támogatott konfiguráció|vCenter, ESXi| Hyper-V fürt, Hyper-V gazdagép|NA|Hyper-V fürt, Hyper-V gazdagép|NA|
Azon kiszolgálók száma, amelyek profilozhatók a Site Recovery Deployment Planner futó példányaihoz |Egyszeres (az egy vCenter Serverhez vagy egy ESXi-kiszolgálóhoz tartozó virtuális gépek profilozhatók egyszerre)|Többszörös (több gazdagéphez vagy gazdagépfürthöz tartozó virtuális gépek profilozhatók egyszerre)| NA |Többszörös (több gazdagéphez vagy gazdagépfürthöz tartozó virtuális gépek profilozhatók egyszerre)| NA

*Az eszköz elsősorban a Hyper-V – Azure vészhelyreállítási forgatókönyvhöz való. Hyper-V – másodlagos hely vészhelyreállítás esetében csak a forrásoldali javaslatok (pl. szükséges hálózati sávszélesség, az egyes forrás Hyper-V-kiszolgálókhoz szükséges szabad tárterület és a kezdeti replikáláskötegelési számok és kötegelési definíciók) megismeréséhez használható. A jelentésből származó Azure javaslatokat és költségeket hagyja figyelmen kívül. Emellett a Get Throughput művelet nem használható a Hyper-V – másodlagos hely vészhelyreállítási forgatókönyvben.

## <a name="prerequisites"></a>Előfeltételek
Az eszköz két fő fázisból áll: a profil- és jelentéskészítésből. Van egy harmadik lehetőség, amely csak az átviteli sebességet számítja ki. Az alábbi táblázatban láthatók annak a kiszolgálónak a követelményei, ahonnan a profilkészítés/átviteli sebesség mérését kezdeményezi.

| Kiszolgálókövetelmények | Leírás|
|---|---|
|Profilkészítés és az átviteli sebesség mérése| <ul><li>Operációs rendszer: Windows Server 2016 vagy Windows Server 2012 R2<br>(ideális esetben legalább a [konfigurációs kiszolgáló javasolt méretével egyezik](https://aka.ms/asr-v2a-on-prem-components))</li><li>Gépkonfiguráció: 8 vCPU, 16 GB RAM, 300 GB HDD</li><li>[.NET-keretrendszer 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[A Visual Studio 2012 szoftverhez készült Visual C++ terjeszthető változata](https://aka.ms/vcplusplus-redistributable)</li><li>Internet-hozzáférés az Azure-szolgáltatáshoz erről a kiszolgálóról</li><li>Azure Storage-fiók</li><li>Rendszergazdai hozzáférés a kiszolgálón</li><li>Minimális szabad lemezterület 100 GB (feltéve, hogy 1000 virtuális gépen átlagosan gépenként három lemezről 30 napig készít profilokat)</li><li>VMware vCenter statisztikaszint-beállításait lehet 1 vagy magasabb szintű</li><li>Lehetővé teszi a vCenter-portot (443-as alapértelmezett érték): a Site Recovery Deployment Planner ezt a portot használja a vCenter kiszolgálóhoz/ESXi-gazdagéphez való kapcsolódáshoz</ul></ul>|
| Jelentéskészítés | Windows PC vagy Windows Server, az Excel 2013 vagy újabb.<li>[.NET-keretrendszer 4.5](https://aka.ms/dotnet-framework-45)</li><li>[A Visual Studio 2012 szoftverhez készült Visual C++ terjeszthető változata](https://aka.ms/vcplusplus-redistributable)</li><li>[A VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli) csak akkor adja át – a jelentés projektlétrehozási parancsot a virtuális gépek legutóbbi konfigurációs adatait a virtuális gépek beolvasása a felhasználó beállítás kötelező. A Depolyment Planner csatlakozik a vCenter-kiszolgáló. Engedélyezi a vCenter portot (443-as alapértelmezett érték) vCenter-kiszolgálóhoz való csatlakozáshoz.</li>|
| Felhasználói engedélyek | Csak olvasási jogosultság a felhasználói fióknak a VMware vCenter/VMware vSphere ESXi kiszolgáló profilkészítés közben való hozzáféréséhez |

> [!NOTE]
>
>Az eszköz csak a VMDK- és RDM-lemezzel rendelkező virtuális gépeknek tud profilt készíteni. Nem képes az iSCSI- vagy NFS-lemezzel rendelkező virtuális gépek profilkészítésére. A Site Recovery támogatja az iSCSI- és NFS-lemezeket a VMware-kiszolgálókon. Mivel az üzembehelyezési tervező nem a vendéggépen belül fut, és csak a vCenter teljesítményszámlálók használatával készít profilokat, az eszköz számára nem láthatók ezek a lemeztípusok.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Az üzembehelyezés-tervező eszköz letöltése és kibontása
1. Töltse le a [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner) legújabb verzióját.
Az eszköz .zip-mappába van csomagolva. Az eszköz jelenlegi verziója csak a VMware–Azure forgatókönyvet támogatja.

2. Másolja a zip-fájlt a Windows Serveren belül oda, ahonnan futtatni kívánja az eszközt.
Az eszközt futtathatja Windows Server 2012 R2-ről, ha a kiszolgáló hálózati hozzáférésével képes csatlakozni a ahhoz a vCenter kiszolgálóhoz/vSphere ESXi-gazdagéphez, amely a profilkészítéshez használt virtuális gépeket üzemelteti. Ajánlott azonban egy olyan kiszolgálón futtatni az eszközt, amelynek a hardverkonfigurációja megfelel a [konfigurációs kiszolgáló méretezési útmutatójának](https://aka.ms/asr-v2a-on-prem-components). Ha már üzembe helyezte a Site Recovery helyszíni összetevőit, futtassa az eszközt a konfigurációs kiszolgálóról.

    A konfigurációs kiszolgálóval megegyező (beépített folyamatkiszolgálóval rendelkező) hardverkonfiguráció ajánlott azon a kiszolgálón, ahol futtatja az eszközt. Az ilyen konfiguráció biztosítja, hogy az eszköz által jelentett elért átviteli sebesség megegyezik a Site Recovery által replikáció közben elért tényleges átviteli sebességgel. Az átviteli sebesség kiszámítása a kiszolgálón elérhető hálózati sávszélességtől és a kiszolgáló hardverkonfigurációjától (mint például processzor és tárterület) függ. Ha az eszközt bármely másik kiszolgálóról futtatja, az arról a kiszolgálóról az Azure irányába elérhető átviteli sebesség lesz kiszámítva. Mivel a kiszolgáló hardverkonfigurációja eltérhet a konfigurációs kiszolgálóétól, lehetséges, hogy az eszköz által jelentett elért átviteli sebesség nem lesz pontos.

3. Csomagolja ki a .zip mappát.
A mappa több fájlt és almappát tartalmaz. Az ASRDeploymentPlanner.exe futtatható fájl a szülőmappában található.

    Példa: másolja a zip-fájlt az E:\ meghajtóra, és csomagolja ki.
    E:\ASR Deployment Planner_v2.2.zip

    E:\ASR Deployment Planner_v2.2\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Frissítés a Deployment Planner legújabb verziójára
Ha a Deployment Planner korábbi verziójával rendelkezik, tegye az alábbiak egyikét:
 * Ha a legújabb verzió nem tartalmaz profilkészítést érintő javítást, és a profilkészítés már folyamatban van a Planner aktuális verzióján, folytassa a profilkészítést.
 * Ha a legújabb verzió tartalmaz profilkészítést érintő javítást, javasoljuk, hogy állítsa le az aktuális verzión a profilkészítést, és indítsa újra a profilkészítést az új verzióval.


 >[!NOTE]
 >
 >Ha az új verzióval indítja el a profilkészítést, akkor ugyanazt a kimeneti elérési utat kell megadnia ahhoz, hogy az eszköz a már létező fájlokhoz fűzze hozzá a profiladatokat. Ekkor a profiladatok teljes körét felhasználja majd a jelentés létrehozásához. Ha eltérő kimeneti könyvtárat ad meg, akkor új fájlok jönnek létre, és a régi profiladatokat nem lehet majd felhasználni a jelentés létrehozásához.
 >
 >A Deployment Planner minden új verziója a .zip fájl összegző frissítését jelenti. A legújabb fájlokat nem kell a korábbi mappába másolnia. Létrehozhat és használhat egy új mappát is.


## <a name="version-history"></a>Verzióelőzmények
A legutóbbi Site Recovery Deployment Planner eszköz verziószáma 2.2.
Az egyes frissítésekben hozzáadott javításokról a [Site Recovery Deployment Planner verzióelőzményeinek oldalán](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) lehet tájékozódni.

## <a name="next-steps"></a>További lépések
[A Site Recovery Deployment Planner futtatása](site-recovery-vmware-deployment-planner-run.md)
