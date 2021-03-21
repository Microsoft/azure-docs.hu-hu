---
title: Futtassa a VMware vész-helyreállítás Deployment Planner a Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan futtatható Azure Site Recovery Deployment Planner a VMware vész-helyreállítás az Azure-ba.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: ramamill
ms.openlocfilehash: 05d260de726c62c130a58938c2a2c9fa2440a96d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004721"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>A VMware vész-helyreállítás Deployment Planner futtatása
Ez a cikk az Azure Site Recovery Deployment Planner felhasználói útmutatója a VMware–Azure éles környezetben való üzembe helyezéséhez.


## <a name="modes-of-running-deployment-planner"></a>A Deployment Planner futtatásának módjai
A parancssori eszköz (ASRDeploymentPlanner.exe) a következő három mód bármelyikében futtatható:

1.  [Profilkészítés](#profile-vmware-vms)
2.  [Jelentés létrehozása](#generate-report)
3.  [Átviteli sebesség lekérdezése](#get-throughput)

Először futtassa profilkészítési módban az eszközt a virtuális gép adatváltozásának és IOPS-értékének összegyűjtéséhez. Ezután futtassa az eszközt a jelentés létrehozásához, hogy megtudhassa a hálózati sávszélességet, a tárolási követelményeket és a vészhelyreállítási költséget.

## <a name="profile-vmware-vms"></a>Profilkészítés VMware virtuális gépekről
Profilkészítési módban a Deployment Planner eszköz a vCenter-kiszolgálóhoz vagy a vSphere ESXi-gazdagéphez csatlakozik, hogy összegyűjtse a virtuális gép teljesítményadatait.

* A profilkészítés nincs hatással az éles virtuális gépek teljesítményére, mivel nem jön létre közvetlen kapcsolat az éles virtuális gépekkel. A rendszer minden teljesítményadatot összegyűjt a vCenter-kiszolgálóról / vSphere ESXi-gazdagépről.
* Az eszköz 15 percenként lekérdezi a vCenter-kiszolgálót vagy a vSphere ESXi-gazdagépet, hogy a profilkészítés biztosan csak elhanyagolható hatással legyen a kiszolgálóra. A lekérdezési időköz azonban nem veszélyezteti a profilkészítés pontosságát, mivel az eszköz minden percben tárolja a teljesítményszámláló adatait.

### <a name="create-a-list-of-vms-to-profile"></a>A profillal ellátni kívánt virtuális gépek listájának létrehozása
Először létre kell hoznia a profillal ellátni kívánt virtuális gépek listáját. A következő eljárás VMware vSphere PowerCLI-parancsai használatával a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen található összes virtuális gép nevét elérheti. Másik lehetőségként egy fájlba listázhatja azon virtuális gépek rövid nevét vagy IP-címét, amelyeket manuálisan szeretne profillal ellátni.

1. Jelentkezzen be arra a virtuális gépre, amelyen a VMware vSphere PowerCLI telepítve van.
2. Nyissa meg a VMware vSphere PowerCLI-konzolt.
3. Ellenőrizze, hogy a végrehajtási szabályzat engedélyezve van-e a szkript esetében. Ha a szabályzat le van tiltva, indítsa el a VMware vSphere PowerCLI-konzolt rendszergazdai módban, és futtassa a következő parancsot az engedélyezéséhez:

    ```powershell
    Set-ExecutionPolicy –ExecutionPolicy AllSigned
    ```

4. Előfordulhat, hogy a következő parancsot kell futtatnia, ha Connect-VIServer nem ismeri fel a parancsmag nevét.

    ```powershell
    Add-PSSnapin VMware.VimAutomation.Core
    ```

5. Futtassa az alábbi két parancsot a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen található összes virtuális gép nevének lekéréséhez és a nevek egy szöveges dokumentumba (.txt) mentéséhez.
Cserélje le a &lsaquo;server name&rsaquo; (kiszolgáló neve), a &lsaquo;user name&rsaquo; (felhasználónév), a &lsaquo;password&rsaquo; (jelszó), az &lsaquo;outputfile.txt&rsaquo; (kimenetifájl.txt) paramétereket saját értékeire.

    ```powershell
    Connect-VIServer -Server <server name> -User <user name> -Password <password>

    Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>
    ```

6. Nyissa meg a kimeneti fájlt a Jegyzettömbben, és másolja egy másik fájlba (pl. ProfileVMList.txt) minden olyan virtuális gép nevét, amelyről profilt kíván készíteni. Minden virtuális gép nevét külön sorba írja. A rendszer ezt a fájlt használja majd bemenetként a parancssori eszköz *-VMListFile* paraméteréhez.

    ![Virtuálisgép-nevek listája a Deployment Planner eszközben
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Profilkészítés indítása
Ha megvan azon virtuális gépek listája, amelyekről profilt szeretne készíteni, futtathatja az eszközt profilkészítési módban. Az alábbi lista az eszköz profilkészítési módjában használható kötelező és választható paramétereket sorolja fel.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Paraméter neve | Leírás |
|---|---|
| -Művelet | StartProfiling |
| -Kiszolgáló | Azon vCenter-kiszolgáló vagy vSphere ESXi-gazdagép teljes tartományneve vagy IP-címe, amelynek virtuális gépeiről profilt szeretne készíteni.|
| -Felhasználó | A vCenter-kiszolgálóhoz vagy vSphere ESXi-gazdagéphez való csatlakozáshoz használt felhasználónév. A felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy az ESXi-gazdagépen szereplő nevekkel.<br>A „VMList.txt” fájl például az alábbi virtuális gépeket tartalmazza:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|A percek száma, ameddig a profilkészítést futtatni szeretné. A minimális érték 30 perc.|
|-NoOfHoursToProfile|Az órák száma, ameddig a profilkészítést futtatni szeretné.|
| -NoOfDaysToProfile | A napok száma, ameddig a profilkészítést futtatni szeretné. Javasoljuk, hogy legalább 7 napig futtassa a profilkészítést. Ez biztosítja a számítási feladatok mintájának megfigyelését a környezetben a meghatározott időtartamon belül, amely alapján pontos javaslat adható. |
|-Virtualization|Itt adhatja meg a virtualizálás típusát (VMware vagy Hyper-V).|
| -Directory | (Nem kötelező) Az univerzális elnevezési konvenciónak (UNC) megfelelő elérési út vagy azon helyi könyvtár elérési útja, ahol a profilkészítés során létrehozott adatokat tárolni kívánja. Ha nem adja meg könyvtár nevét, a rendszer az aktuális elérési úton található „ProfiledData” könyvtárat használja alapértelmezett könyvtárként. |
| -Password | (Nem kötelező) A vCenter-kiszolgálóhoz vagy vSphere ESXi-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg most, a rendszer a parancs végrehajtásakor rákérdez.|
|-Port|(Nem kötelező) A vCenter/ESXi-gazdagéphez csatlakozó port száma. Az alapértelmezett port a 443-as számú.|
|-Protocol| (Nem kötelező) „http” vagy „https” protokollt ad meg a vCenterhez való csatlakozáshoz. Az alapértelmezett protokoll a https.|
| -StorageAccountName | (Nem kötelező) A helyszínről az Azure-ba történő adatreplikáció során elérhető átviteli sebesség azonosításához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat az átviteli sebesség kiszámításához. A tárfióknak Általános célú v1 (GPv1) típusúnak kell lennie. |
| -StorageAccountKey | (Nem kötelező) A tárfiók eléréséhez használt tárfiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > <*Tárfiók neve*> > Beállítások > Hozzáférési kulcsok > 1. kulcs elemre. |
| -Környezet | (nem kötelező) Ez az Ön Azure Storage-fiókjának célkörnyezete. Ez a következő három érték egyike lehet: AzureCloud, AzureUSGovernment, AzureChinaCloud. Az alapértelmezett érték az AzureCloud. A paramétert akkor használja, ha a cél Azure-régiója az Azure US government vagy az Azure China 21Vianet. |


Javasoljuk, hogy legalább 7 napig végezze a virtuális gépek profiljának készítését. Ha az adatváltozási minta egy hónapon belül változik, javasoljuk, hogy a héten akkor készítsen profilt, amikor a maximális változás tapasztalható. A legjobb megoldás, ha 31 nap alatt készít profilt, így jobb javaslatokat kaphat. A profilkészítés időtartama alatt az ASRDeploymentPlanner.exe alkalmazás folyamatosan fut. A profilkészítés időtartama napokban adható meg az eszközben. Az eszköz gyors teszteléséhez vagy megvalósíthatósági próbához néhány óra vagy perc alatt is készíthet profilt. A minimális profilkészítési idő 30 perc.

A profilkészítés során lehetősége van átadni egy tárfióknevet és -kulcsot, amivel megtudhatja, hogy a Site Recovery mekkora átviteli sebességet érhet el a konfigurációs kiszolgálóról vagy a folyamatkiszolgálóról az Azure-ba történő replikáció során. Ha nem ad át tárfióknevet és -kulcsot a profilkészítés során, az eszköz nem számítja ki az elérhető átviteli sebességet.

Az eszköz több példánya is futtatható egyszerre különböző virtuálisgép-csoportokon. Győződjön meg arról, hogy a virtuális gépek neve nem ismétlődik egyik profilkészítési csoportban sem. Ha például profilt készített tíz virtuális gépről (VM1–VM10), és néhány nap elteltével további öt virtuális gépről szeretne profilt készíteni (VM11–VM15), akkor a második csoporton (VM11–VM15) egy másik parancssori konzolról futtathatja az eszközt. Győződjön meg arról, hogy a második virtuálisgép-csoport nem tartalmaz olyan virtuálisgép-nevet, amely szerepel az első profilkészítési példányban, vagy a második futtatás esetében használjon eltérő kimeneti könyvtárat. Ha az eszköz két példánya ugyanazokról a virtuális gépekről készít profilt, és ugyanazt a kimeneti könyvtárat is használják, a létrehozott jelentés helytelen lesz.

Alapértelmezés szerint az eszköz a profilhoz van konfigurálva, és a jelentést akár 1000 virtuális gépre is létrehozhatja. A korlát módosításához meg kell változtatni a MaxVMsSupported kulcs értékét az *ASRDeploymentPlanner.exe.config* fájlban.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Az alapértelmezett beállítások mellett ahhoz, hogy a profil 1500 virtuális géppel dolgozzon, két VMList.txt fájlt kell létrehozni. Az egyiken 1000, a másikon pedig 500 virtuális gépnek kell szerepelnie. Futtassa a Azure Site Recovery Deployment Planner két példányát, amelyek közül az egyiket VMList1.txt és más VMList2.txt. Mindkét VMList virtuális gépeinek esetében ugyanazt a könyvtárútvonalat is használhatja a profilkészítési adatok tárolásához.

Tapasztalatunk szerint a hardverkonfigurációtól, és különösen a jelentést generáló eszközt futtató kiszolgáló RAM-méretétől függően a művelet elegendő memória hiányában megszakadhat. Megfelelő hardveres feltételek mellett a MaxVMsSupported esetében bármilyen nagyobb értéket meg lehet adni.  

Több vCenter-kiszolgáló esetén minden egyes darabnál a ASRDeploymentPlanner egy külön példányát kell futtatnia a profilkészítéshez.

A rendszer egyszer, a profilkészítési művelet elején rögzíti virtuálisgép-konfigurációkat, és egy VMDetailList.xml nevű fájlban tárolja őket. Ezeket az adatokat azután a jelentés létrehozásakor használja. Ha a profilkészítés során a virtuális gépek konfigurációjának bármely része módosul (például nő a magok, a lemezek vagy a hálózati adapterek száma), azt a rendszer nem rögzíti. Ha a profilkészítés során módosul az egyik virtuális gép konfigurációja, amelyről profilt készített, akkor a nyilvános előzetes verzióban a következő megkerülő megoldással kérheti le a virtuális gép legfrissebb adatait a jelentés készítésekor:

* Készítsen biztonsági másolatot a VMdetailList.xml fájlról, és törölje a fájlt a jelenlegi helyéről.
* Adja meg a -User és -Password argumentumokat a jelentés létrehozásakor.

A profilkészítési parancs számos fájlt létrehoz a profilkészítési könyvtárban. Ezeket ne törölje, mert az hatással lenne a jelentésre.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>1. példa: Profilkészítés virtuális gépről 30 napon keresztül, valamint a helyszín és az Azure közötti átviteli sebesség meghatározása
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>2. példa: Profilkészítés virtuális gépről 15 napon keresztül

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>3. példa: Profilkészítés virtuális gépekről 60 perc alatt az eszköz gyors teszteléséhez
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>4. példa: Profilkészítés virtuális gépekről 2 óra alatt megvalósíthatósági próbához
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Ha a kiszolgáló, amelyen az eszköz fut, újraindul vagy összeomlik, vagy ha bezárja az eszközt a Ctrl + C billentyűkombinációval, a rendszer megőrzi a profilkészítés során létrehozott adatokat. Előfordulhat azonban, hogy az utolsó 15 percnyi profilkészítés adatai elvesznek. Ebben az esetben a kiszolgáló újraindítása után ismét futtassa az eszközt profilkészítési módban.
>* Ha megadja a tárfióknevet és -kulcsot, az eszköz a profilkészítés utolsó lépéseként megméri az átviteli sebességet. Ha bezárja az eszközt, mielőtt a profilkészítés befejeződött volna, a rendszer nem számítja ki az átviteli sebességet. Az átviteli sebesség a jelentés létrehozása előtti lekérdezéséhez futtassa a GetThroughput műveletet a parancssori konzolból. Ellenkező esetben a létrehozott jelentés nem tartalmazza majd az átviteli sebességgel kapcsolatos információkat.


## <a name="generate-report"></a>Jelentés létrehozása
Az eszköz egy makróbarát Microsoft Excel-fájlt (XLSM-fájlt) hoz létre a jelentés kimeneteként, amely összefoglalja az üzembehelyezési javaslatokat. A jelentés neve `DeploymentPlannerReport_<unique numeric identifier>.xlsm` és elhelyezése a megadott könyvtárban történik.

>[!NOTE]
>A jelentéskészítéshez szükség van egy Windows rendszerű számítógépre vagy Windows Serverre, amely Excel 2013 vagy újabb verziójú. A számítógép decimális szimbólumát "." értékre kell állítani a becsült költségbecslés létrehozásához. Ha a (z) "," decimális szimbólumként van beállítva, lépjen a "dátum, idő vagy számformátum módosítása" elemre a Vezérlőpulton, és lépjen a "További beállítások" lehetőségre a decimális szimbólum "." értékre való módosításához.

A profilkészítés befejezése után futtathatja az eszközt jelentéskészítési módban. A következő táblázat a jelentéskészítési módban futtatandó kötelező és nem kötelező eszközparaméterek listáját tartalmazza.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Paraméter neve | Leírás |
|-|-|
| -Művelet | Jelentés készítése |
| -Kiszolgáló |  Annak a vCenter- vagy vSphere-kiszolgálónak a teljes tartományneve vagy IP-címe, ahol azon virtuális gépek találhatók, amelyekről profilt készített, és amelyekről most jelentést szeretne készíteni. (Használja a profilkészítéskor használt nevet vagy IP-címet.) Ügyeljen arra, hogy ha vCenter-kiszolgálót használt a profilkészítés során, akkor nem használhat vSphere-kiszolgálót a jelentéskészítéshez, és fordítva.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt készített, és amelyekről jelentést szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen szereplő nevekkel, és egyezniük kell a profilkészítés során használt nevekkel.|
|-Virtualization|Itt adhatja meg a virtualizálás típusát (VMware vagy Hyper-V).|
| -Directory | (Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha a név nincs megadva, a rendszer a „ProfiledData” könyvtárat használja. |
| -GoalToCompleteIR | (Nem kötelező) Ennyi óra alatt kell befejeződnie azon virtuális gépek kezdeti replikációjának, amelyekről profilt kíván készíteni. Az elkészített jelentés tartalmazza azon virtuális gépek számát, amelyeknek a kezdeti replikációja befejezhető a megadott időtartam alatt. Az alapértelmezett érték 72 óra. |
| -Felhasználó | (Nem kötelező) A vCenter- vagy vSphere-kiszolgálóhoz való csatlakozáshoz használni kívánt felhasználónév. A rendszer a név használatával kéri le a virtuális gépek legutóbbi konfigurációs adatait a jelentéshez (pl. a lemezek, magok, hálózati adapterek száma stb.). Ha a név nincs megadva, a rendszer a profilkészítés indításakor összegyűjtött konfigurációs adatokat használja fel. |
| -Password | (Nem kötelező) A vCenter-kiszolgálóhoz vagy vSphere ESXi-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg a jelszót paraméterként, a rendszer később, a parancs végrehajtásakor rákérdez. |
|-Port|(Nem kötelező) A vCenter/ESXi-gazdagéphez csatlakozó port száma. Az alapértelmezett port a 443-as számú.|
|-Protocol|(Nem kötelező) „http” vagy „https” protokollt ad meg a vCenterhez való csatlakozáshoz. Az alapértelmezett protokoll a https.|
| -DesiredRPO | (Nem kötelező) A kívánt helyreállítási időkorlát, percben megadva. Az alapértelmezett beállítás 15 perc.|
| -Bandwidth | Sávszélesség (Mbps). A rendszer ezen paraméter alapján számítja ki az elérhető helyreállítási időkorlátot a megadott sávszélességhez. |
| -StartDate | (Nem kötelező) Kezdő dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. A *StartDate* és az *EndDate* paraméter megadása kötelező. Ha a StartDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -EndDate | (Nem kötelező) Záró dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. Az *EndDate* és a *StartDate* paraméter megadása kötelező. Ha az EndDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -GrowthFactor | (Nem kötelező) A növekedési tényező százalékértékként megadva. Az alapértelmezett érték 30%. |
| -UseManagedDisks | (Nem kötelező) UseManagedDisks – Igen/Nem. Az alapértelmezett érték az Igen. Az egy tárfiókban elhelyezhető virtuális gépek számának meghatározáskor a rendszer figyelembe veszi, hogy a virtuális gépek feladatátvétele, illetve feladatátvételi tesztje nem felügyelt lemez helyett felügyelt lemezen történik. |
|-SubscriptionId |(Nem kötelező) Az előfizetés GUID azonosítója. Vegye figyelembe, hogy ezt a paramétert akkor kell megadni, ha az előfizetése és az előfizetése és a **megadott pénznemben** meghatározott Azure-régiója esetében a díjszabási jelentést a legújabb árral kell létrehoznia.|
|-TargetRegion|(Nem kötelező) A replikáció által megcélzott Azure-régió. Mivel az Azure költségei régiónként eltérőek, adott cél Azure-régióra vonatkozó jelentés létrehozásához használja ezt a paramétert.<br>Az alapértelmezett régió az USA 2. nyugati régiója vagy a legutoljára használt célrégió.<br>Tekintse át a [támogatott célrégiók](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) listáját.|
|-OfferId|(Nem kötelező) Az adott előfizetéshez társított ajánlat. Az alapértelmezett az MS-AZR-0003P (használatalapú fizetés).|
|-Currency|(Nem kötelező) A pénznem, amelyben a költségek megjelennek a létrehozott jelentésben. Az alapértelmezett az amerikai dollár ($), vagy a legutoljára használt pénznem.<br>Tekintse át a [támogatott pénznemek](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies) listáját.|

Alapértelmezés szerint az eszköz a profilhoz van konfigurálva, és a jelentést akár 1000 virtuális gépre is létrehozhatja. A korlát módosításához meg kell változtatni a MaxVMsSupported kulcs értékét az *ASRDeploymentPlanner.exe.config* fájlban.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>1. példa: Jelentés készítése az alapértelmezett értékekkel, ha a profilkészítés során létrehozott adatok a helyi meghajtón találhatóak
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>2. példa: Jelentés készítése, ha a profilkészítés során létrehozott adatok távoli kiszolgálón találhatóak
A felhasználónak olvasási/írási hozzáféréssel kell rendelkeznie a távoli könyvtárhoz.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>3. példa: Jelentés készítése megadott sávszélességgel és a kezdeti replikáció adott határidejű befejezésére vonatkozó céllal
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>4. példa: Jelentés készítése 5%-os növekedési tényezővel az alapértelmezett 30% helyett
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>5. példa: Jelentés létrehozása a profilkészítés során használt adatok egy részéből
Tegyük fel, hogy van 30 napnyi adata, amelyet a profilkészítés során hozott létre, de csak 20 napról szeretne jelentést készíteni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>6. példa: Jelentés készítése 5 perces helyreállítási időkorláttal
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>7. példa: Jelentés létrehozása a Dél-India Azure-régióhoz indiai rúpia pénznemmel és adott ajánlatazonosítóval

Vegye figyelembe, hogy az előfizetés-azonosító szükséges a Cost-jelentés adott pénznemben történő létrehozásához.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>A számításhoz használt százalékérték
**Alapértelmezés szerint a profilkészítés során összegyűjtött teljesítménymetrikák milyen százalékos arányát használja az eszköz jelentések készítésekor?**

Az eszköz alapértelmezett értéke az írási/olvasási IOPS, az írási IOPS és az adatváltozás esetén a 95. százalékérték az összes virtuális gép profiljának elkészítése során. Ez a metrika biztosítja, hogy a virtuális gépek által az ideiglenes események miatt esetlegesen észlelt 100. százalékértékes kiugrást a rendszer nem használja fel a cél tárfiók és a forrássávszélesség követelményeinek meghatározásakor. Az ideiglenes esemény lehet például egy naponta egyszer futtatott biztonsági mentési feladat, rendszeres időközönként végzett adatbázis-indexelés, elemzésijelentés-készítési tevékenység vagy bármely hasonló, rövid ideig tartó, időpontalapú esemény.

A 95. százalékérték használata valós képet ad a számítási feladatok valódi jellemzőiről, és a lehető legjobb teljesítményt biztosítja, miközben a számítási feladatok futnak az Azure-on. Ezt az értéket valószínűleg nem kell módosítania. Ha úgy dönt, hogy módosítja (például a 90. százalékértékre), akkor az alapértelmezett mappában található *ASRDeploymentPlanner.exe.config* konfigurációs fájl frissítésével és mentésével létrehozhat egy új jelentést a meglévő, profilkészítés során létrehozott adatokról.
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>A növekedési tényezővel kapcsolatos szempontok
**Miért kell figyelembe vennem a növekedési tényezővel kapcsolatos szempontokat a környezetek megtervezésekor?**

Rendkívül fontos figyelembe venni a növekedést a számítási feladatok jellemzőiben, feltételezve a használat lehetséges növekedését. Ha beállítja a védelmet, és módosulnak a számítási feladatok jellemzői, nincs lehetőség arra, hogy egy másik védett tárfiókra váltson a védelem letiltása és ismételt engedélyezése nélkül.

Tegyük fel például, hogy a virtuális gép jelenleg megfelel egy standard szintű tárreplikációs fiókhoz. A következő néhány hónapban számos változás következhet be:

* Nő azoknak a felhasználóknak a száma, akik az alkalmazást a virtuális gépen futtatják.
* Az így létrejövő megnövekedett adatforgalom miatt a virtuális gépnek prémium szintű tárolóra van szüksége, hogy a Site Recovery replikációja tartani tudja a lépést.
* Ennek következtében le kell tiltania, majd újra engedélyeznie kell a védelmet egy prémium szintű tárfiókon.

Erősen ajánlott, hogy az üzembe helyezés tervezése során számoljon a növekedéssel, amelynek az alapértelmezett értéke 30 százalék. Az alkalmazása használati mintáját és növekedési előrejelzéseit Ön ismeri a legjobban, és a jelentés elkészítése közben igény szerint módosíthatja ezt a számot. Továbbá több jelentést is készíthet különböző növekedési tényezőkkel, de ugyanazokról az adatokról. Így eldöntheti, hogy melyik, a céloldali tárolóra, valamint a forrásoldali sávszélességre vonatkozó javaslatok felelnek meg leginkább Önnek.

Az elkészített Microsoft Excel-jelentés a következő információkat tartalmazza:

* [Helyszíni összefoglalás](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Javaslatok](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM< – >tárolási hely](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [Compatible VMs](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms) (Kompatibilis virtuális gépek)
* [Nem kompatibilis virtuális gépek](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Költségbecslés](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Átviteli sebesség lekérdezése

Ha meg szeretné becsülni az átviteli sebességet, amelyet a Site Recovery el tud érni helyszínről az Azure-ba történő replikáció során, futtassa az eszközt GetThroughput módban. Az eszköz arról a kiszolgálóról számítja ki az átviteli sebességet, amelyen fut. Ez a kiszolgáló optimális esetben a konfigurációs kiszolgálók méretezési útmutatóján alapul. Ha már üzembe helyezte a Site Recovery infrastruktúra-összetevőit a helyszínen, futtassa az eszközt a konfigurációs kiszolgálón.

Nyisson meg egy parancssori konzolt, és keresse meg a Site Recovery üzembehelyezés-tervező eszköz mappáját. Futtassa az ASRDeploymentPlanner.exe fájlt az alábbi paraméterekkel.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Paraméter neve | Leírás |
|-|-|
| -Művelet | Átviteli sebesség lekérdezése |
|-Virtualization|Itt adhatja meg a virtualizálás típusát (VMware vagy Hyper-V).|
| -Directory | (Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha a könyvtár neve nincs megadva, a rendszer a „ProfiledData” könyvtárat használja. |
| -StorageAccountName | A helyszínről az Azure-ba történő adatreplikáció során felhasznált sávszélesség meghatározásához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat a felhasznált sávszélesség megállapításához. A tárfióknak Általános célú v1 (GPv1) típusúnak kell lennie.|
| -StorageAccountKey | A tárfiók eléréséhez használt tárfiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > <*Tárfiók neve*> > Beállítások > Hozzáférési kulcsok > 1. kulcs (vagy klasszikus tárfiók esetén az Elsődleges elérési kulcs) elemre. |
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni a felhasznált sávszélesség kiszámításához. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen szereplő névvel.<br>A „VMList.txt” fájl például az alábbi virtuális gépeket tartalmazza:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Környezet | (nem kötelező) Ez az Ön Azure Storage-fiókjának célkörnyezete. Ez a következő három érték egyike lehet: AzureCloud, AzureUSGovernment, AzureChinaCloud. Az alapértelmezett érték az AzureCloud. A paramétert akkor használja, ha a cél Azure-régiója az Azure US government vagy az Azure China 21Vianet. |

Az eszköz több 64 MB-os „asrchdfile<#>.vhd” nevű fájlt (a „#” a fájlok számát jelöli) is létrehoz a megadott könyvtárban. Az eszköz feltölti a fájlokat a tárfiókba az átviteli sebesség megállapításához. Az átviteli sebesség mérése után az eszköz törli az összes fájlt a tárfiókból és a helyi kiszolgálóról. Ha az eszköz bármilyen oknál fogva leáll az átviteli sebesség kiszámítása közben, akkor nem törli a fájlokat a tárolóból és a helyi kiszolgálóról. Ezeket manuálisan kell törölnie.

Az eszköz egy meghatározott időpontban méri az átviteli sebességet, és a kapott érték a Site Recovery által a replikáció során elérhető maximális átviteli sebesség, feltéve, hogy a többi tényező sem változik. Ha például valamelyik alkalmazás több sávszélességet kezd el felhasználni ugyanazon a hálózaton belül, a tényleges átviteli sebesség változhat a replikáció során. Ha a GetThroughput parancsot egy konfigurációs kiszolgálóról futtatja, az eszköz nem veszi figyelembe a védett virtuális gépeket és a folyamatban lévő replikációkat. Az átviteli sebesség mérésének eredménye eltérő lesz, ha a védett virtuális gépek adatváltozása magas a GetThroughput művelet végrehajtásakor. Ajánlott az eszközt különböző időpontokban futtatni a profilkészítés során, hogy átláthassa, milyen átviteli sebességi szintek érhetők el a különféle időpontokban. Az eszköz a legutóbb mért átviteli sebességet jeleníti meg a jelentésben.

### <a name="example"></a>Példa
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Olyan kiszolgálón futtassa az eszközt, amelynek a tároló- és processzorjellemzői megegyeznek a konfigurációs kiszolgálóéval
>
> A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet állítsa be. Ha a megfelelő sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:
>
>  1. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná a Site Recovery átviteli sebességét.
>
>  2. Ellenőrizze, hogy a hálózati késés minimalizálása érdekében a Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e.
>
>  3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).
>
>  4. Módosítsa a Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Következő lépések
* [A generált jelentés elemzése](site-recovery-vmware-deployment-planner-analyze-report.md).
