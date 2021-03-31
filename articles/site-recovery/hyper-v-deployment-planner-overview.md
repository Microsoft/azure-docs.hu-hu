---
title: Deployment Planner a Hyper-V vész-helyreállításhoz Azure Site Recovery
description: Ismerje meg a Azure Site Recovery Deployment Planner Hyper-V vész-helyreállítást az Azure-ba.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: mayg
ms.openlocfilehash: e4f1931aab056306ac5e9f9e9ef402ca26ec2d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86528944"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Az Azure-ba irányuló Hyper-V vész-helyreállítás Azure Site Recovery Deployment Planner

Ez a cikk az Azure Site Recovery Deployment Planner felhasználói útmutatója a Hyper-V–Azure éles környezetben való üzembe helyezéséhez.

A Hyper-V virtuális gépek Site Recoveryvel történő védelmének megkezdése előtt elegendő sávszélességet kell kiosztania a napi szintű adatváltozások alapján, hogy elérje a kívánt helyreállítási időkorlátot (RPO), és elegendő szabad tárterületet kell lefoglalni a helyszíni Hyper-V-tároló minden kötetén.

Létre kell hoznia a megfelelő típusú és számú cél Azure-tárfiókot is. Hozzon létre standard vagy prémium szintű tárfiókokat a fokozatosan növekvő használat során megnövekedett éles kiszolgálók miatt. A tárolási típust virtuális gépenként válassza ki, a számítási feladatok jellemzői (például olvasási és írási I/O-műveletek másodpercenként (IOPS), vagy adatváltozás) és az Azure Site Recovery-korlátok alapján. 

Az Azure Site Recovery üzembehelyezés-tervező egy, a Hyper-V – Azure és VMware – Azure vészhelyreállítási forgatókönyvekben elérhető parancssori eszköz. Az eszközzel (az éles környezet megzavarása nélkül) távolról készítheti el a több Hyper-V-gazdagépen lévő Hyper-V virtuális gépek profilját, és megismerheti a sávszélességet és az Azure Storage követelményeit a sikeres replikálás és feladatátvétel/feladatátvételi teszt érdekében. Az eszközt helyszíni Azure Site Recovery-összetevők telepítése nélkül is futtathatja. Az elért átviteli sebesség pontos meghatározása érdekében azonban ajánlott a tervezőeszköz futtatása egy olyan Windows Serveren, amelynek hardverkonfigurációja megegyezik az Azure-beli vészhelyreállítási védelem biztosításához használt Hyper-V-kiszolgálókéval. 

Az eszköz a következő részleteket biztosítja:

**Kompatibilitási felmérés**

* A virtuális gép jogosultságfelmérése a lemezszám, a lemezméretek, az IOPS, a változás és néhány virtuálisgép-jellemző alapján.

**A hálózatisávszélesség-igény és RPO-elemzés**

* A változásreplikáláshoz szükséges becsült hálózati sávszélesség
* Átviteli sebesség az Azure Site Recovery számára a helyszíni rendszerek és az Azure között
* Az adott sávszélesség esetén elérhető RPO
* A kívánt RPO-ra gyakorolt hatás kisebb sávszélesség kiosztása esetén.

    
**Azure infrastruktúra-követelmények**

* A tárolótípus (standard vagy prémium szintű tárfiók) követelménye az egyes virtuális gépekhez
* A replikáláshoz beállítandó standard és prémium szintű tárfiókok teljes száma
* Tárfiókok elnevezési javaslatai az Azure Storage útmutatója alapján
* Az összes virtuális gép tárfiókjának elhelyezése
* A feladatátvételi teszt vagy feladatátvétel előtt beállítandó Azure magok száma az előfizetésen
* Az Azure virtuális gépek javasolt mérete az egyes helyszíni virtuális gépeknél

**Helyszíni infrastruktúra-követelmények**
* A Hyper-V-tároló egyes kötetein a sikeres kezdeti replikációhoz és a változásreplikációhoz szükséges szabad tárterület, amelynek megléte esetén a virtuális gépek replikációja semmiképpen nem okozza a termelési alkalmazások nem kívánt leállását
* A Hyper-V-replikáció számára beállítható maximális másolási gyakoriság

**Útmutató a kezdeti replikáció kötegeléséhez** 
* A védelemhez használni kívánt virtuálisgép-kötegek száma
* Az egyes kötegekben lévő virtuális gépek listája
* A kötegek védelmének sorrendje
* Az egyes kötegek kezdeti replikációja végrehajtásának várható időtartama

**Becsült vészhelyreállítási költségek az Azure-hoz**
* A becsült teljes vészhelyreállítási költség Azure-hoz: számítás, tárolás, hálózat és az Azure Site Recovery licencköltsége
* Részletes költségelemzés virtuális gépenként



>[!IMPORTANT]
>
>Mivel a használat idővel megnövekszik, az eszközben minden előzetes számítás a munkaterhelési jellemzők 30%-os növekedési tényezőjével történik, illetve a profilkészítési mérőszámok 95%-át veszi alapul (írási/olvasási IOPS, adatváltozás stb.). Mindkét elem (a növekedési tényező és a százalékérték is) konfigurálható. További információkat a növekedési tényezőről „A növekedési tényezővel kapcsolatos szempontok” szakaszban találhat. További információkat a százalékértékről „A számításhoz használt százalékérték” szakaszban találhat.
>

## <a name="support-matrix"></a>Támogatási mátrix

|**Kategóriák** | **VMware – Azure** |**Hyper-V – Azure**|**Azure – Azure**|**Hyper-V – másodlagos hely**|**VMware – másodlagos hely**
--|--|--|--|--|--
Támogatott esetek |Igen|Igen|Nem|Igen*|No
Támogatott verzió | vCenter 6,7, 6,5, 6,0 vagy 5,5| Windows Server 2016, Windows Server 2012 R2 | NA |Windows Server 2016, Windows Server 2012 R2|NA
Támogatott konfiguráció|vCenter, ESXi| Hyper-V fürt, Hyper-V gazdagép|NA|Hyper-V fürt, Hyper-V gazdagép|NA|
Azon kiszolgálók száma, amelyek profilozhatók az Azure Site Recovery Deployment Planner futó példányaihoz |Egyszeres (az egy vCenter Serverhez vagy egy ESXi-kiszolgálóhoz tartozó virtuális gépek profilozhatók egyszerre)|Többszörös (több gazdagéphez vagy gazdagépfürthöz tartozó virtuális gépek profilozhatók egyszerre)| NA |Többszörös (több gazdagéphez vagy gazdagépfürthöz tartozó virtuális gépek profilozhatók egyszerre)| NA

*Az eszköz elsősorban a Hyper-V – Azure vészhelyreállítási forgatókönyvhöz való. Hyper-V – másodlagos hely vészhelyreállítás esetében csak a forrásoldali javaslatok (pl. szükséges hálózati sávszélesség, az egyes forrás Hyper-V-kiszolgálókhoz szükséges szabad tárterület és a kezdeti replikáláskötegelési számok és kötegelési definíciók) megismeréséhez használható.  A jelentésből származó Azure javaslatokat és költségeket hagyja figyelmen kívül. Emellett a Get Throughput művelet nem használható a Hyper-V – másodlagos hely vészhelyreállítási forgatókönyvben.

## <a name="prerequisites"></a>Előfeltételek
Az eszköz Hyper-V esetén három fő fázisból áll: virtuálisgép-lista lekérése, valamint profilkészítés és jelentéskészítés. Van egy negyedik lehetőség, amely csak az átviteli sebességet számítja ki. Az alábbi táblázatban találhatók annak a kiszolgálónak a követelményei, amelyen az egyes fázisokat futtatni kell:

| Kiszolgálókövetelmények | Description |
|---|---|
|Virtuálisgép-lista lekérésre, profilkészítés és az átviteli sebesség mérése |<ul><li>Operációs rendszer: Microsoft Windows Server 2016 vagy Microsoft Windows Server 2012 R2 </li><li>Gépkonfiguráció: 8 vCPU, 16 GB RAM, 300 GB HDD</li><li>[Microsoft .NET-keretrendszer 4.5](https://aka.ms/dotnet-framework-45)</li><li>[A Visual Studio 2012 szoftverhez készült Microsoft Visual C++ terjeszthető változata](https://aka.ms/vcplusplus-redistributable)</li><li>Internet-hozzáférés az Azure-hoz (*. blob.core.windows.net) erről a kiszolgálóról, 443-es portról<br>[Ez nem kötelező. Megadhatja, hogy a jelentés létrehozásakor a rendelkezésre álló sávszélességet manuálisan adja meg.]</li><li>Azure Storage-fiók</li><li>Rendszergazdai hozzáférés a kiszolgálón</li><li>Minimális szabad lemezterület 100 GB (feltéve, hogy 1000 virtuális gépen átlagosan gépenként három lemezről 30 napig készít profilokat)</li><li>A virtuális gépet, ahol az Azure Site Recovery Deployment Planner fut, hozzá kell adni az összes Hyper-V-kiszolgáló TrustedHosts listájához.</li><li>A felvenni kívánt Hyper-V-kiszolgálókat hozzá kell adni az ügyfél virtuális TrustedHosts listájához, amelyből az eszköz fut. [További információ kiszolgálók a TrustedHosts listához való hozzáadásáról](#steps-to-add-servers-into-trustedhosts-list). </li><li> Az eszközt rendszergazdai jogosultságokkal kell futtatni a PowerShellből vagy az ügyfél parancssori konzoljáról.</ul></ul>|
| Jelentéskészítés | 2013-as vagy újabb Microsoft Excellel rendelkező Windows PC vagy Windows Server |
| Felhasználói engedélyek | Rendszergazdai fiók, amely hozzáférhet a Hyper-V-fürthöz/Hyper-V-gazdagéphez a virtuálisgép-lista lekérése és a profilkészítési műveletek során.<br>Az összes profillal ellátni kívánt gazdagépnek rendelkeznie kell egy tartományi rendszergazdai fiókkal, amelynek hitelesítő adatai, vagyis felhasználóneve és jelszava megegyezik
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Kiszolgálók a TrustedHosts listához való hozzáadásának lépései
1. Az összes profillal ellátni kívánt gazdagépnek szerepelnie kell azon virtuális gép TrustedHosts listájában, ahonnan az eszközt telepíteni kívánja. Az ügyfél TrustedHosts listához való hozzáadásához futtassa a következő parancsot a virtuális gépen egy emelt szintű PowerShell-munkamenetből. A virtuális gép Windows Server 2012 R2 vagy Windows Server 2016 rendszerű lehet. 

   ```powershell
   set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
   ```
1. A profillal ellátni kívánt összes Hyper-V-gazdagépen a következők megléte szükséges:

    a. A virtuális gép, amelyen az eszközt futtatni kívánja hozzá van adva a TrustedHosts listájához. Futtassa a következő parancsot a Hyper-V-gazdagépen egy emelt szintű PowerShell-munkamenetből.

      ```powershell
      set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
      ```

    b. Engedélyezve van a PowerShell távoli eljáráshívás.

      ```powershell
      Enable-PSRemoting -Force
      ```

## <a name="download-and-extract-the-deployment-planner-tool"></a>Az üzembehelyezés-tervező eszköz letöltése és kibontása

1.  Töltse le az [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner) legújabb verzióját.
Az eszköz .zip-mappába van csomagolva. Ugyanaz az eszköz támogatja a VMware – Azure és a Hyper-V – Azure vészhelyreállítási forgatókönyveket. Az eszközt Hyper-V – másodlagos hely vészhelyreállítási forgatókönyvekhez is használhatja, de hagyja figyelmen kívül a jelentés Azure-infrastruktúrára vonatkozó javaslatait.

1.  Másolja a zip-fájlt azon Windows Serverre, ahol futtatni kívánja az eszközt. Az eszközt Windows Server 2012 R2 vagy Windows Server 2016 rendszeren futtathatja. A kiszolgáló számára hálózati hozzáférés szükséges a profillal ellátni kívánt virtuális gépeket tároló Hyper-V-fürthöz vagy Hyper-V-gazdagéphez való csatlakozáshoz. Javasoljuk, hogy a virtuális gép, ahol az eszközt futtatni kívánja és a Hyper-V-kiszolgáló, amely számára védelmet kíván biztosítani azonos hardverkonfigurációval rendelkezzen. Ez biztosítja, hogy az eszköz által jelentett elért átviteli sebesség megegyezik az Azure Site Recovery által replikáció közben elért tényleges átviteli sebességgel. Az átviteli sebesség kiszámítása a kiszolgálón elérhető hálózati sávszélességtől és a kiszolgáló hardverkonfigurációjától (processzor, tárterület stb.) függ. Az átviteli sebesség arról a kiszolgálóról lesz kiszámítva, ahol az eszköz fut az Azure-ra. Ha a kiszolgáló és a Hyper-V-kiszolgáló hardverkonfigurációja nem egyezik, az eszköz által jelentett elért átviteli sebesség nem lesz pontos.
A virtuális gép ajánlott konfigurációja: 8 vCPU, 16 GB RAM, 300 GB HDD.

1.  Csomagolja ki a .zip mappát.
A mappa több fájlt és almappát tartalmaz. Az ASRDeploymentPlanner.exe futtatható fájl a szülőmappában található.

Példa: másolja a zip-fájlt az E:\ meghajtóra, és csomagolja ki. E:\ASR üzembe helyezési Planner_v2.3.zip

E:\ASR üzembe helyezési Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Frissítés az üzembehelyezés-tervező a legújabb verzióra

A legújabb frissítéseket a Deployment Planner [korábbi verzióiban](site-recovery-deployment-planner-history.md)tekintjük össze.

Ha az üzembehelyezés-tervező korábbi verziójával rendelkezik, tegye az alábbiak egyikét:
 * Ha a legújabb verzió nem tartalmaz profilkészítést érintő javítást, és a profilkészítés már folyamatban van a Planner aktuális verzióján, folytassa a profilkészítést.
 * Ha a legújabb verzió tartalmaz profilkészítést érintő javítást, javasoljuk, hogy állítsa le az aktuális verzión a profilkészítést, és indítsa újra a profilkészítést az új verzióval.


  >[!NOTE]
  >
  >Ha az új verzióval indítja el a profilkészítést, akkor ugyanazt a kimeneti elérési utat kell megadnia ahhoz, hogy az eszköz a már létező fájlokhoz fűzze hozzá a profiladatokat. Ekkor a profiladatok teljes körét felhasználja majd a jelentés létrehozásához. Ha eltérő kimeneti könyvtárat ad meg, akkor új fájlok jönnek létre, és a régi profiladatokat nem lehet majd felhasználni a jelentés létrehozásához.
  >
  >Minden új Deployment Planner a .zip fájl összegző frissítését jelenti. Ezért nem kell a legújabb fájlokat a korábbi mappába másolnia. Létrehozhat és használhat egy új mappát is.

## <a name="version-history"></a>Korábbi verziók
A legújabb Azure Site Recovery Deployment Planner eszköz verziója 2,5.
Tekintse meg az egyes frissítésekben hozzáadott javítások [Azure site Recovery Deployment Planner korábbi verziók](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) lapját.


## <a name="next-steps"></a>Következő lépések
* [Az üzembehelyezés-tervező futtatása](./hyper-v-deployment-planner-run.md).
