---
title: A Hyper-V Deployment Planner futtatása Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan futtathatja a Hyper-V vész-helyreállítási Azure Site Recovery Deployment Plannerét az Azure-ba.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mayg
ms.openlocfilehash: ba1979c940d4a92b3d1a7a52a4f356b2896ece55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74082609"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Az Azure Site Recovery Deployment Planner futtatása a Hyper-V Azure-ba történő vészhelyreállításához

A következő négy mód bármelyikében futtathatja a Site Recovery Deployment Planner parancssori eszközt (ASRDeploymentPlanner.exe): 
-   A virtuálisgép-lista lekérdezése
-   [Profil](#profile-hyper-v-vms)
-   Jelentés létrehozása
-   [Átviteli sebesség lekérdezése](#get-throughput)

Először futtassa az eszközt, hogy megkapja a virtuális gépek listáját egy vagy több Hyper-V-gazdagépről. Ezt követően futtassa profilkészítési módban az eszközt a virtuális gép adatváltozásának és IOPS-értékének összegyűjtéséhez. Ezután futtassa az eszközt a jelentés létrehozásához, hogy megtudhassa a hálózati sávszélességet és a tárolási követelményeket.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>A virtuálisgép-lista lekérése Hyper-V virtuális gépek profiljának készítéséhez
Először létre kell hoznia a profillal ellátni kívánt virtuális gépek listáját. Használja a Deployment Planner GetVMList módját, hogy egyetlen paranccsal előállíthassa a több Hyper-V-gazdagépen található virtuális gépek listáját. A teljes lista létrehozását követően eltávolíthatja a kimeneti fájlból azokat a virtuális gépeket, amelyekhez nem kíván profilt készíteni. Ezt követően a kimeneti fájlt a többi művelet végrehajtásához használhatja (profilkészítés, jelentéskészítés és az átviteli sebesség lekérdezése).

A virtuális gépek listáját úgy hozhatja létre, ha az eszközt egy Hyper-V-fürtre, egy önálló Hyper-V-gazdagépre, vagy a kettő kombinációjára irányítja.

### <a name="command-line-parameters"></a>Parancssori paraméterek
A következő táblázat a GetVMList módban futtatandó kötelező és nem kötelező eszközparaméterek listáját tartalmazza. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Paraméter neve | Leírás |
|---|---|
| -Művelet | GetVMList |
| -Felhasználó | A Hyper-V-kiszolgálóhoz vagy Hyper-V-fürthöz való csatlakozáshoz használt felhasználónév. A felhasználónak rendszergazdai hozzáféréssel kell rendelkeznie.|
| -ServerListFile | Azon kiszolgálók listáját tartalmazó fájl, amelyek a profillal ellátni kívánt virtuális gépeket tartalmazzák. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorának tartalmazza a következők egyikét:<ul><li>Hyper-V-gazdagép neve vagy IP-címe</li><li>Hyper-V-fürt neve vagy IP-címe</li></ul><br>**Például:** A ServerList.txt az alábbi kiszolgálókat tartalmazza:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Nem kötelező) Az univerzális elnevezési konvenciónak (UNC) megfelelő elérési út vagy azon helyi könyvtár elérési útja, ahol a művelet során létrehozott adatokat tárolni kívánja. Ha nincs név megadva, a rendszer az aktuális elérési úton található ProfiledData könyvtárat használja alapértelmezett könyvtárként.|
|-OutputFile| Választható A rendszer menti a Hyper-V-kiszolgálókról beolvasott virtuális gépek listáját tartalmazó fájlt. Ha nincs megemlítve név, a részleteket a VMList.txt fájl tartalmazza.  A fájl használatával kezdje meg a profilkészítést, miután eltávolította azokat a virtuális gépeket, amelyekhez nem kíván profilt készíteni.|
|-Password|(Nem kötelező) A Hyper-V-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg paraméterként, a rendszer a parancs végrehajtásakor el fogja kérni.|

### <a name="getvmlist-discovery"></a>GetVMList-felderítés

- **Hyper-V-fürt**: A Hyper-V-fürt nevének a kiszolgálólista fájljában történő megadásakor az eszköz megkeresi a fürt összes Hyper-V-csomópontját, és megszerzi az egyes Hyper-V-gazdagépeken található virtuális gépeket.
**Hyper-V-gazdagép**: A Hyper-V-gazdagép nevének megadásakor az eszköz ellenőrzi, hogy az fürthöz tartozik-e. Ha igen, az eszköz lekérdezi a fürthöz tartozó csomópontokat. Ezután lekéri a virtuális gépeket minden Hyper-V-gazdagéptől. 

Egy fájlba is listázhatja azon virtuális gépek rövid nevét vagy IP-címét, amelyeket manuálisan szeretne profillal ellátni.

Nyissa meg a kimeneti fájlt a Jegyzettömbben, és másolja egy másik fájlba (például a ProfileVMList.txt-be) minden olyan virtuális gép nevét, amelyről profilt kíván készíteni. Minden virtuális gép nevét írja külön sorba. A rendszer ezt a fájlt használja majd bemenetként az eszköz -VMListFile paraméteréhez az összes műveletnél (profilkészítés, jelentéskészítés és az átviteli sebesség lekérdezése).

### <a name="examples"></a>Példák

#### <a name="store-the-list-of-vms-in-a-file"></a>A virtuális gépek listájának tárolása egy fájlban
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>A virtuális gépek listájának tárolása az alapértelmezett helyen (-Directory elérési útja)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Profilkészítés Hyper-V rendszerű virtuális gépekhez
Profilkészítési módban a Deployment Planner eszköz mindegyik Hyper-V-gazdagéphez csatlakozik, hogy összegyűjtse a virtuális gépek teljesítményadatait. 

A profilkészítés nincs hatással az éles virtuális gépek teljesítményére, mivel nem jön létre közvetlen kapcsolat az éles virtuális gépekkel. A rendszer minden teljesítményadatot összegyűjt a Hyper-V-kiszolgálóról.

Az eszköz 15 másodpercenként lekérdezi a Hyper-V-kiszolgálót a pontos profilkészítés érdekében, és percenként elmenti a teljesítményszámláló adatainak átlagát.

Az eszköz zökkenőmentesen kezeli a virtuális gépek csomópontok közötti migrációját a fürtben és a tárolómigrációt egy gazdagépen belül.

### <a name="getting-the-vm-list-to-profile"></a>Profillal ellátni kívánt virtuális gépek listájának lekérése
Tekintse át a GetVMList műveletet a profillal ellátni kívánt virtuális gépek listájának létrehozásához.

Ha megvan azon virtuális gépek listája, amelyekről profilt szeretne készíteni, futtathatja az eszközt profilkészítési módban. 

### <a name="command-line-parameters"></a>Parancssori paraméterek
A következő táblázat felsorolja a profilkészítési módban futtatandó kötelező és nem kötelező eszközparamétereket. Az eszköz ugyanaz, akár VMware-ről, akár Hyper-V-ről szeretne Azure-ra áthelyezést végezni. Ezek a paraméterek Hyper-V használata esetén érvényesek. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Paraméter neve | Leírás |
|---|---|
| -Művelet | StartProfiling |
| -Felhasználó | A Hyper-V-kiszolgálóhoz vagy Hyper-V-fürthöz való csatlakozáshoz használt felhasználónév. A felhasználónak rendszergazdai hozzáféréssel kell rendelkeznie.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekhez profilt szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A Hyper-V esetében ez a fájl a GetVMList művelet kimeneti fájlja. Ha az előkészületeket manuálisan végzi el, a fájl minden sorában egy kiszolgáló nevének vagy IP-címének kell szerepelnie, amelyet egy virtuális gép neve követ (soronként \ perjellel elválasztva). A fájlban megadott virtuálisgép-névnek meg kell egyeznie a Hyper-V-gazdagépen szereplő névvel.<br><br>**Például:** A VMList.txt az alábbi virtuális gépeket tartalmazza:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|A percek száma, ameddig a profilkészítés futni fog. A minimális érték 30 perc.|
|-NoOfHoursToProfile|Az órák száma, ameddig a profilkészítés futni fog.|
|-NoOfDaysToProfile |A napok száma, ameddig a profilkészítés futni fog. Javasoljuk, hogy legalább 7 napig futtassa a profilkészítést. Ez az időtartam biztosítja a számítási feladatok mintájának pontos megfigyelését a környezetben, amely alapján pontos javaslat adható.|
|-Virtualization|A virtualizálás típusa (VMware vagy Hyper-V).|
|-Directory|(Nem kötelező) Az UNC elérési út vagy azon helyi könyvtár elérési útja, ahol a profilkészítés során létrehozott adatokat tárolni kívánja. Ha nincs név megadva, a rendszer az aktuális elérési úton található ProfiledData könyvtárat használja alapértelmezett könyvtárként.|
|-Password|(Nem kötelező) A Hyper-V-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg paraméterként, a rendszer a parancs végrehajtásakor el fogja kérni.|
|-StorageAccountName|(Nem kötelező) A helyszínről az Azure-ba történő adatreplikáció során elérhető átviteli sebesség azonosításához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat az átviteli sebesség kiszámításához. A tárfióknak Általános célú v1 (GPv1) típusúnak kell lennie.|
|-StorageAccountKey|(Nem kötelező) A tárfiók eléréséhez használt kulcs. Nyissa meg a Azure Portal > **Storage-fiókok** tárolási fiókjának  >  *neve*  >  **Beállítások**  >  **hozzáférési kulcsok**  >  **Key1** (vagy egy klasszikus Storage-fiók elsődleges hozzáférési kulcsát).|
|-Környezet|(Nem kötelező) Az Azure-tárfiók célkörnyezete. A következő három érték egyike lehet: AzureCloud, AzureUSGovernment vagy AzureChinaCloud. Az alapértelmezett érték az AzureCloud. A paramétert akkor használja, ha a célként megadott régió az Azure US government vagy az Azure China 21Vianet.|

Javasoljuk, hogy legalább 7 napig végezze a virtuális gépek profiljának készítését. Ha az adatváltozási minta egy hónapon belül változik, javasoljuk, hogy a héten akkor készítsen profilt, amikor a maximális változás tapasztalható. A legjobb megoldás, ha 31 nap alatt készíti el a profilt, így jobb javaslatokat kaphat. 

A profilkészítés időtartama alatt az ASRDeploymentPlanner.exe alkalmazás folyamatosan fut. A profilkészítés időtartama napokban adható meg az eszközben. Az eszköz gyors teszteléséhez vagy a megvalósíthatósági vizsgálathoz néhány óra vagy perc alatt is készíthet profilt. A minimális profilkészítési idő 30 perc. 

A profilkészítés során lehetősége van átadni egy tárfióknevet és -kulcsot, amivel megtudhatja, hogy az Azure Site Recovery mekkora átviteli sebességet érhet el a Hyper-V-kiszolgálóról az Azure-ba történő replikáció során. Ha nem ad át tárfióknevet és -kulcsot a profilkészítés során, az eszköz nem számítja ki az elérhető átviteli sebességet.

Az eszköz több példánya is futtatható egyszerre különböző virtuálisgép-csoportokon. Győződjön meg arról, hogy a virtuális gépek neve nem ismétlődik egyik profilkészítési csoportban sem. Tegyük fel például, hogy 10 virtuális gépről készített profilt (VM1–VM10). Néhány nap elteltével szeretne további öt virtuális gépről profilt készíteni (VM11–VM15). A második csoporton (VM11–VM15) egy másik parancssori konzolról futtathatja az eszközt. 

Győződjön meg arról, hogy a második virtuálisgép-csoport nem tartalmaz olyan virtuálisgép-nevet, amely szerepel az első profilkészítési példányban, vagy a második futtatáshoz adjon meg egy másik kimeneti könyvtárat. Ha az eszköz két példánya ugyanazokról a virtuális gépekről készít profilt, és ugyanazt a kimeneti könyvtárat is használják, a létrehozott jelentés helytelen lesz. 

Alapértelmezés szerint az eszköz a profilhoz van beállítva, és legfeljebb 1000 virtuális géphez hoz létre jelentéseket. A korlátozás módosításához meg kell változtatni a MaxVMsSupported kulcs értékét az ASRDeploymentPlanner.exe.config fájlban.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Ahhoz, hogy a profil például 1500 virtuális géppel dolgozzon, alapértelmezett beállítások esetén két VMList.txt fájlt kell létrehozni. Az egyikben 1000, a másikban 500 virtuális gép lesz. Futtassa az Azure Site Recovery Deployment Planner két példányát: az egyiket a VMList1.txt, a másikat pedig a VMList2.txt fájllal. Mindkét VMList virtuális gépeinek esetében ugyanazt a könyvtárútvonalat is használhatja a profilkészítési adatok tárolásához. 

A hardverkonfigurációtól, és különösen a jelentést létrehozó eszközt futtató kiszolgáló RAM-méretétől függően a művelet elegendő memória hiányában megszakadhat. Megfelelő hardveres feltételek mellett a MaxVMsSupported beállításhoz bármilyen nagyobb értéket meg lehet adni.  

A rendszer egyszer, a profilkészítési művelet elején rögzíti virtuálisgép-konfigurációkat, és egy VMDetailList.xml nevű fájlban tárolja őket. Ezeket az adatokat azután a jelentés létrehozásakor használja. Ha a profilkészítés során a virtuális gépek konfigurációjának bármely része módosul (például nő a magok, a lemezek vagy a hálózati adapterek száma), azt a rendszer nem rögzíti. Ha a profilkészítés során módosul az egyik virtuális gép konfigurációja, amelyről profilt készít, akkor a következő megkerülő megoldással kérheti le a virtuális gép legfrissebb adatait a jelentés készítésekor:

* Készítsen biztonsági másolatot a VMdetailList.xml fájlról, és törölje a fájlt a jelenlegi helyéről.
* Adja meg a -User és -Password argumentumokat a jelentés létrehozásakor.

A profilkészítési parancs számos fájlt létrehoz a profilkészítési könyvtárban. Ezeket ne törölje, mert az hatással lenne a jelentésre.

### <a name="examples"></a>Példák

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Profilkészítés virtuális gépekről 30 napon keresztül, valamint a helyszín és az Azure közötti átviteli sebesség meghatározása
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>Profilkészítés virtuális gépekről 15 napon keresztül
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Profilkészítés virtuális gépekről 60 perc alatt az eszköz gyors teszteléséhez
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Profilkészítés virtuális gépekről 2 óra alatt megvalósíthatósági vizsgálathoz
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Profilkészítéssel kapcsolatos megfontolások

Ha a kiszolgáló, amelyen az eszköz fut, újraindul vagy összeomlik, vagy ha bezárja az eszközt a Ctrl+C billentyűkombinációval, a rendszer megőrzi a profilkészítés során létrehozott adatokat. Előfordulhat azonban, hogy az utolsó 15 percnyi profilkészítés adatai elvesznek. Ezen esetekben a kiszolgáló újraindítása után ismét futtassa az eszközt profilkészítési módban.

Ha megadja a tárfióknevet és -kulcsot, az eszköz a profilkészítés utolsó lépéseként megméri az átviteli sebességet. Ha bezárja az eszközt, mielőtt a profilkészítés befejeződött volna, a rendszer nem számítja ki az átviteli sebességet. Az átviteli sebesség a jelentés létrehozása előtti lekérdezéséhez futtassa a GetThroughput műveletet a parancssori konzolból. Ellenkező esetben a létrehozott jelentés nem tartalmazza majd az átviteli sebességgel kapcsolatos információkat.

A Azure Site Recovery nem támogatja az iSCSI-és csatlakoztatott lemezekkel rendelkező virtuális gépeket. Az eszköz nem ismeri fel a virtuális gépekhez csatolt iSCSI-és csatlakoztatott lemezeket.

## <a name="generate-a-report"></a>Jelentés létrehozása
Az eszköz egy makróbarát Microsoft Excel-fájlt (XLSM-fájlt) hoz létre a jelentés kimeneteként. Ez az üzembehelyezési javaslatokat foglalja össze. A jelentés neve DeploymentPlannerReport_ *egyéni numerikus azonosító*.xlsm, és a megadott könyvtárban lesz elérhető.

A profilkészítés befejezése után futtathatja az eszközt jelentéskészítési módban. 

### <a name="command-line-parameters"></a>Parancssori paraméterek
A következő táblázat a jelentéskészítési módban futtatandó kötelező és nem kötelező eszközparaméterek listáját tartalmazza. Az eszköz ugyanaz, akár VMware-ről, akár Hyper-V-ről szeretne Azure-ra áthelyezést végezni. A Hyper-V esetében az alábbi paraméterek alkalmazhatók.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Paraméter neve | Leírás |
|---|---|
| -Művelet | Jelentés készítése |
|-VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt készített, és amelyekről jelentést szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A Hyper-V esetében ez a fájl a GetVMList művelet kimeneti fájlja. Ha az előkészületeket manuálisan végzi el, a fájl minden sorában egy kiszolgáló nevének vagy IP-címének kell szerepelnie, amelyet egy virtuális gép neve követ (soronként \ perjellel elválasztva). A fájlban megadott virtuálisgép-névnek meg kell egyeznie a Hyper-V-gazdagépen szereplő névvel.<br><br>**Például:** A VMList.txt az alábbi virtuális gépeket tartalmazza:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|A virtualizálás típusa (VMware vagy Hyper-V).|
|-Directory|(Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha nincs név megadva, a rendszer az aktuális elérési úton található ProfiledData könyvtárat használja alapértelmezett könyvtárként.|
| -Felhasználó | (Nem kötelező) A Hyper-V-kiszolgálóhoz vagy Hyper-V-fürthöz való csatlakozáshoz használt felhasználónév. A felhasználónak rendszergazdai hozzáféréssel kell rendelkeznie. A rendszer a felhasználó és a jelszó használatával kéri le a virtuális gépek legfrissebb konfigurációs adatait a jelentéshez (pl. lemezek, magok és hálózati adapterek száma). Ha ez nincs megadva, a rendszer a profilkészítéskor összegyűjtött konfigurációs adatokat használja fel.|
|-Password|(Nem kötelező) A Hyper-V-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg paraméterként, a rendszer a parancs végrehajtásakor el fogja kérni.|
| -DesiredRPO | (Nem kötelező) A kívánt helyreállítási időkorlát (recovery point objective, RPO) percekben megadva. Az alapértelmezett beállítás 15 perc.|
| -Bandwidth | (Nem kötelező) A sávszélesség (Mb/s). A rendszer ezen paraméter alapján számítja ki az elérhető helyreállítási időkorlátot a megadott sávszélességhez. |
| -StartDate | (Nem kötelező) Kezdő dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. A StartDate és az EndDate paraméter megadása kötelező. Ha a StartDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -EndDate | (Nem kötelező) Záró dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. Az EndDate és a StartDate paraméter megadása kötelező. Ha az EndDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -GrowthFactor | (Nem kötelező) A növekedési tényező százalékértékként megadva. Az alapértelmezett érték 30%. |
| -UseManagedDisks | (Nem kötelező) UseManagedDisks: Igen/Nem. Az alapértelmezett érték az Igen. Az egy tárfiókban elhelyezhető virtuális gépek számát a rendszer az alapján határozza meg, hogy a virtuális gépek feladatátvételére, illetve feladatátvételi tesztjére nem felügyelt lemez helyett felügyelt lemezen kerül-e sor. |
|-SubscriptionId |(Nem kötelező) Az előfizetés GUID azonosítója. A paraméter használatával létrehozhat egy költségbecslési jelentést a legújabb árakkal az előfizetése, az előfizetéséhez tartozó ajánlat és a célul szolgáló Azure-régió alapján, a megadott pénznemben.|
|-TargetRegion|(Nem kötelező) A replikáció által megcélzott Azure-régió. Mivel az Azure költségei régiónként eltérőek, egy célul szolgáló adott Azure-régióra vonatkozó jelentés létrehozásához használja ezt a paramétert. Az alapértelmezett régió az USA 2. nyugati régiója vagy a legutoljára használt célrégió. Tekintse át a [támogatott célrégiók](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) listáját.|
|-OfferId|(Nem kötelező) Az előfizetéshez társított ajánlat. Az alapértelmezett az MS-AZR-0003P (használatalapú fizetés).|
|-Currency|(Nem kötelező) A pénznem, amelyben a költségek megjelennek a létrehozott jelentésben. Az alapértelmezett az amerikai dollár ($) vagy a legutoljára használt pénznem. Tekintse át a [támogatott pénznemek](hyper-v-deployment-planner-cost-estimation.md#supported-currencies) listáját.|

Alapértelmezés szerint az eszköz a profilhoz van beállítva, és legfeljebb 1000 virtuális géphez hoz létre jelentéseket. A korlátozás módosításához meg kell változtatni a MaxVMsSupported kulcs értékét az ASRDeploymentPlanner.exe.config fájlban.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Példák
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Jelentés készítése az alapértelmezett értékekkel, ha a profilkészítés során létrehozott adatok a helyi meghajtón találhatóak
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Jelentés készítése, ha a profilkészítés során létrehozott adatok távoli kiszolgálón találhatóak
A felhasználónak olvasási/írási hozzáféréssel kell rendelkeznie a távoli könyvtárhoz.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Jelentés készítése a replikációhoz létrehozott adott sávszélességgel
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Jelentés készítése 5%-os növekedési tényezővel az alapértelmezett 30% helyett 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Jelentés létrehozása a profilkészítés során használt adatok egy részéből
Tegyük fel, hogy van 30 napnyi adata, amelyet a profilkészítés során hozott létre, de csak 20 napról szeretne jelentést készíteni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Jelentés készítése 5 perces helyreállítási időkorláttal
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Jelentés létrehozása a Dél-India Azure-régióhoz indiai rúpia pénznemmel és adott ajánlatazonosítóval
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>A számításhoz használt százalékérték
Az eszköz által a jelentéskészítéshez használt alapértelmezett érték az írási/olvasási IOPS, az írási IOPS és az adatváltozás esetén a 95%-os érték. Ezeket az értékeket a rendszer az összes virtuális gép profiljának elkészítése során gyűjti. Ez a mérőszám gondoskodik róla, hogy a virtuális gépek által az ideiglenes események miatt esetlegesen észlelt 100%-os kiugrást a rendszer ne használja fel a céloldali tárfiók és a forrássávszélesség követelményeinek meghatározásakor. Az ideiglenes esemény lehet például egy naponta egyszer futtatott biztonsági mentési feladat, rendszeres időközönként végzett adatbázis-indexelés, elemzési jelentés készítése vagy bármely hasonló, rövid ideig tartó, időpontalapú esemény.

A 95%-os érték használata valós képet ad a számítási feladatok valódi jellemzőiről, és a lehető legjobb teljesítményt biztosítja, amikor a számítási feladatok futnak az Azure-on. Ezt az értéket valószínűleg nem kell módosítania. Ha mégis úgy dönt, hogy módosítja (például 90%-os értékre), akkor az alapértelmezett mappában található ASRDeploymentPlanner.exe.config konfigurációs fájl frissítésével és mentésével létrehozhat egy új jelentést a meglévő, profilkészítés során létrehozott adatokból.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>A növekedési tényezőre vonatkozó megfontolások
Rendkívül fontos figyelembe venni a növekedést a számítási feladatok jellemzőinek meghatározása során, feltételezve a használat esetleges növekedését. Ha beállítja a védelmet, és módosulnak a számítási feladatok jellemzői, nincs lehetőség arra, hogy egy másik védett tárfiókra váltson a védelem letiltása és ismételt engedélyezése nélkül.

Tegyük fel például, hogy a virtuális gép jelenleg megfelel egy standard szintű tárreplikációs fiókhoz. A következő néhány hónapban az alábbi változások várhatók:

1. Nő azoknak a felhasználóknak a száma, akik az alkalmazást a virtuális gépen futtatják.
2. A megnövekedett mértékű adatváltozás miatt a virtuális gépnek prémium szintű tárolóra van szüksége, hogy az Azure Site Recovery replikációja tartani tudja a lépést.
3. Le kell tiltania a védelmet, majd újra engedélyeznie kell egy prémium szintű tárfiókon.

Határozottan javasoljuk, hogy az üzembe helyezés tervezése során a növekedéssel is számoljon. Noha az alapértelmezett érték 30 százalék, az alkalmazása használati mintáját és növekedési előrejelzéseit Ön ismeri a legjobban. A jelentés elkészítése közben ezt a számot igény szerint módosíthatja. Több jelentést is készíthet különböző növekedési tényezőkkel, de ugyanazon adatok alapján. Így eldöntheti, hogy melyik, a céloldali tárolóra, valamint a forrásoldali sávszélességre vonatkozó javaslatok felelnek meg Önnek a leginkább. 

Az elkészített Microsoft Excel-jelentés a következő információkat tartalmazza:

* [Helyszíni összefoglalás](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Javaslatok](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VIRTUÁLIS gépek – tárolás elhelyezése](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Compatible VMs](hyper-v-deployment-planner-analyze-report.md#compatible-vms) (Kompatibilis virtuális gépek)
* [Nem kompatibilis virtuális gépek](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [A helyszíni tárolásra vonatkozó követelmények](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR-kötegelés](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Költségbecslés](hyper-v-deployment-planner-cost-estimation.md)

![Deployment Planner-jelentés](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Átviteli sebesség lekérdezése
Ha meg szeretné becsülni az átviteli sebességet, amelyet az Azure Site Recovery el tud érni helyszínről az Azure-ba történő replikáció során, futtassa az eszközt GetThroughput módban. Az eszköz arról a kiszolgálóról számítja ki az átviteli sebességet, amelyen fut. Ideális esetben ez a Hyper-V-kiszolgáló, amelynek virtuális gépei számára védelmet biztosít. 

### <a name="command-line-parameters"></a>Parancssori paraméterek 
Nyisson meg egy parancssori konzolt, és lépjen az Azure Site Recovery Deployment Planner eszköz mappájába. Futtassa az ASRDeploymentPlanner.exe fájlt az alábbi paraméterekkel.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Paraméter neve | Leírás |
|---|---|
| -Művelet | Átviteli sebesség lekérdezése |
|-Virtualization|A virtualizálás típusa (VMware vagy Hyper-V).|
|-Directory|(Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha nincs név megadva, a rendszer az aktuális elérési úton található ProfiledData könyvtárat használja alapértelmezett könyvtárként.|
| -StorageAccountName | A helyszínről az Azure-ba történő adatreplikáció során felhasznált sávszélesség meghatározásához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat a felhasznált sávszélesség megállapításához. A tárfióknak Általános célú v1 (GPv1) típusúnak kell lennie.|
| -StorageAccountKey | A tárfiók eléréséhez használt tárfiókkulcs. Lépjen a Azure Portal > **Storage**  >  *-fiókok tárolója-fióknév*  >  **Beállítások**  >  **hozzáférési kulcsok**  >  **Key1**.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni a felhasznált sávszélesség kiszámításához. A fájl elérési útja lehet abszolút vagy relatív. A Hyper-V esetében ez a fájl a GetVMList művelet kimeneti fájlja. Ha az előkészületeket manuálisan végzi el, a fájl minden sorában egy kiszolgáló nevének vagy IP-címének kell szerepelnie, amelyet egy virtuális gép neve követ (soronként \ perjellel elválasztva). A fájlban megadott virtuálisgép-névnek meg kell egyeznie a Hyper-V-gazdagépen szereplő névvel.<br><br>**Például:** A VMList.txt az alábbi virtuális gépeket tartalmazza:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Környezet|(Nem kötelező) Az Azure-tárfiók célkörnyezete. A következő három érték egyike lehet: AzureCloud, AzureUSGovernment vagy AzureChinaCloud. Az alapértelmezett érték az AzureCloud. A paramétert akkor használja, ha a cél Azure-régiója az Azure US government vagy az Azure China 21Vianet.|

### <a name="example"></a>Példa
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Átviteli sebességgel kapcsolatos szempontok

Az eszköz több 64 MB-os asrvhdfile *szám*.vhd nevű fájlt (a *szám* a fájlok számát jelöli) is létrehoz a megadott könyvtárban. Az eszköz feltölti a fájlokat a tárfiókba az átviteli sebesség megállapításához. Az átviteli sebesség mérése után az eszköz törli az összes fájlt a tárfiókból és a helyi kiszolgálóról. Ha az eszköz bármilyen oknál fogva leáll az átviteli sebesség kiszámítása közben, akkor nem törli a fájlokat a tárfiókból és a helyi kiszolgálóról. Ezeket manuálisan kell törölnie.

Az eszköz egy meghatározott időpontban méri az átviteli sebességet. A kapott érték az Azure Site Recovery által a replikáció során elérhető maximális átviteli sebesség, ha a többi tényező nem változik. Ha például valamelyik alkalmazás több sávszélességet kezd el felhasználni ugyanazon a hálózaton belül, a tényleges átviteli sebesség változhat a replikáció során. Az átviteli sebesség mérésének eredménye eltérő lesz, ha a védett virtuális gépek adatváltozása magas a GetThroughput művelet végrehajtásakor. 

Ajánlott az eszközt különböző időpontokban futtatni a profilkészítés során, hogy átláthassa, milyen átviteli sebességi szintek érhetők el a különféle időpontokban. Az eszköz a legutóbb mért átviteli sebességet jeleníti meg a jelentésben.

> [!NOTE]
> Olyan kiszolgálón futtassa az eszközt, amelynek a tárolási és processzorjellemzői megegyeznek a Hyper-V-kiszolgálóéval.

A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet állítsa be. Ha a megfelelő sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:

1. Ellenőrizze, hogy nem egy hálózati szolgáltatásminőséggel (QoS) kapcsolatos probléma korlátozza-e az Azure Site Recovery átviteli sebességét.
2. Ellenőrizze a hálózati késés minimalizálása érdekében, hogy az Azure Site Recovery-tároló a legközelebbi fizikailag támogatott Microsoft Azure-régióban található-e.
3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).

    
## <a name="next-steps"></a>Következő lépések
* [A létrehozott jelentés elemzése](hyper-v-deployment-planner-analyze-report.md)
