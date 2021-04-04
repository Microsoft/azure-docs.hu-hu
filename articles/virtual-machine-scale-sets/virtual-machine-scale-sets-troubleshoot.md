---
title: Az Virtual Machine Scale Sets
description: Az Virtual Machine Scale Setstel való autoskálázás hibáinak megoldása. Az észlelt jellemző problémák megismerése és a megoldásuk módja.
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 11302c301bee466f678d544d0c4838c39cec9c8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91818531"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Az automatikus méretezés hibaelhárítása a Virtual Machine Scale Sets használatával
**Probléma** – a Azure Resource Manager a virtuálisgép-méretezési csoportok használatával létrehozott egy automatikus skálázási infrastruktúrát, például egy sablon üzembe helyezésével: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale  – a méretezési szabályok definiálva vannak, és nagyszerűen működik, kivéve, ha a virtuális gépeken elhelyezett terhelések mennyisége nem automatikus méretezés.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Néhány megfontolandó szempont:

* Hány vCPU rendelkezik az egyes virtuális gépek, és betölti az egyes vCPU?
  Az előző minta Azure rövid útmutató sablon egy do_work. php szkripttel rendelkezik, amely egyetlen vCPU tölt be. Ha olyan virtuális gépet használ, amely egy vCPU virtuálisgép-méretnél nagyobb, mint Standard_A1 vagy D1, akkor többször is futtatnia kell ezt a terhelést. Az [Azure-beli Windows rendszerű virtuális gépek méretének](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) áttekintésével győződjön meg arról, hogy hány vCPU rendelkezik a virtuális gépekhez
* Hány virtuális gépet használnak a virtuálisgép-méretezési csoportokban?
  
    A kibővített esemény csak akkor zajlik le, ha egy méretezési csoportba tartozó **összes** virtuális gép átlagos CPU-értéke meghaladja a küszöbértéket az autoskálázási szabályokban definiált belső idő során.
* Kihagyott egy méretezési eseményt?
  
    A skálázási eseményekhez tartozó Azure Portal ellenőrizze a naplókat. Lehet, hogy kimaradt egy vertikális felskálázás. A szűrést a "Scale" kifejezéssel végezheti el.
  
    ![Naplók][audit]
* Eléggé különbözőek a méretezési és a kibővíthető küszöbértékek?
  
    Tegyük fel, hogy olyan szabályt állít be, amely felskálázást végez, ha az átlagos CPU több mint 50%-kal több mint öt perc, és ha az átlagos CPU kevesebb, mint 50%. Ez a beállítás egy "lefokozás" problémát okoz, ha a CPU-használat a küszöbértékhez közeledik, és a méretezési műveletek folyamatosan növelik és csökkentik a készlet méretét. Ennek a beállításnak a következtében az automatikus skálázási szolgáltatás megpróbálja megakadályozni, hogy a méretezés nem méretezhető legyen. Ezért ügyeljen arra, hogy a horizontális Felskálázási és a skálázási küszöbértékek elég különbözőek legyenek, hogy a méretezés között legyen némi térköz.
* Írt egy saját JSON-sablont?
  
    Egyszerűen elvégezheti a hibákat, ezért olyan sablonnal kezdheti meg a munkát, amely a fentieknek megfelelően működik, és kis növekményes változtatásokat tesz elérhetővé. 
* Be-vagy kibővítheti a manuális méretezést?
  
    Próbálja meg újratelepíteni a virtuálisgép-méretezési csoport erőforrását egy másik "kapacitás" beállítással a virtuális gépek számának manuális módosításához. Példa erre a sablonra: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – Előfordulhat, hogy a sablon szerkesztésével meg kell győződnie arról, hogy a méretezési csoport ugyanazt a virtuálisgép-méretet használja. Ha sikeresen megváltoztathatja a virtuális gépek számát manuálisan, akkor tudja, hogy a probléma el van különítve az autoscale-re.
* Keresse meg a Microsoft. számítás/virtualMachineScaleSet és a Microsoft. bepillantások erőforrásait a [Azure erőforrás-kezelő](https://resources.azure.com/)
  
    A Azure Erőforrás-kezelő egy nem megfelelő hibaelhárítási eszköz, amely a Azure Resource Manager erőforrásainak állapotát jeleníti meg. Kattintson az előfizetésre, és tekintse meg a hibaelhárítás alatt lévő erőforráscsoportot. A számítási erőforrás-szolgáltató területen tekintse meg a létrehozott virtuálisgép-méretezési készletet, és ellenőrizze a példány nézetet, amely megjeleníti a központi telepítés állapotát. Továbbá tekintse meg a virtuálisgép-méretezési csoport virtuális gépek példány nézetét is. Ezután nyissa meg a Microsoft. betekintési erőforrás-szolgáltatót, és ellenőrizze, hogy az autoskálázási szabályok megfelelőek-e.
* A diagnosztikai bővítmény működik és kibocsátja a teljesítményadatokat?
  
    **Frissítés:** Az Azure-alapú autoskálázás a gazdagépen alapuló metrikai folyamat használatára lett kibővítve, amely már nem igényel diagnosztikai bővítményt a telepítéshez. A következő néhány bekezdés már nem érvényes, ha egy automatikus skálázási alkalmazást hoz létre az új folyamat használatával. A gazdagép-folyamat használatára konvertált Azure-sablonok például itt érhetők el: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale . 
  
    A gazdagép-alapú metrikák használata az autoskálázáshoz a következő okok miatt jobb:
  
  * Kevesebb mozgó rész szükséges a diagnosztikai bővítmények telepítéséhez.
  * Egyszerűbb sablonok. Csak egy meglévő méretezési csoport sablonját adja hozzá az autoskálázási szabályokhoz.
  * Megbízhatóbb jelentéskészítés és az új virtuális gépek gyorsabb elindítása.
    
    Az egyetlen ok, amiért érdemes megtartani a diagnosztikai bővítményt, ha a memória-diagnosztika jelentéskészítés/skálázás szükséges. A gazdagép-alapú mérőszámok nem jelentik a memóriát.
    
    Ennek szem előtt tartásával csak akkor kövesse a cikk további részét, ha diagnosztikai bővítményeket használ az automatikus skálázáshoz.
    
    A Azure Resource Managerban az autoskálázás a diagnosztikai bővítménynek nevezett virtuálisgép-bővítmény használatával működhet (de már nem). Teljesítményadatokat küld a sablonban definiált Storage-fiókba. Ezt követően a Azure Monitor szolgáltatás összesíti ezeket az adatokat.
    
    Ha az elemzések szolgáltatás nem tudja beolvasni az adatait a virtuális gépekről, a rendszer elküld egy e-mailt. Ha például egy e-mailt kap, ha a virtuális gépek nem állnak le. Ügyeljen arra, hogy ellenőrizze az e-mail-címet az Azure-fiók létrehozásakor megadott e-mail-címen.
    
    Saját maga is megtekintheti az adatot. Tekintse meg az Azure Storage-fiókot a Cloud Explorer használatával. Például a [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)használatával jelentkezzen be, és válassza ki a használni kívánt Azure-előfizetést. Ezután tekintse meg a diagnosztikai tár fiók nevét a telepítési sablon diagnosztika bővítményének definíciójában.
    
    ![Cloud Explorer][explorer]
    
    Megjelenik egy csomó olyan tábla, ahol az egyes virtuális gépekről származó adatok tárolódnak. A Linux és a CPU-metrika példaként való elkészítésekor tekintse meg a legfrissebb sorokat. A Visual Studio Cloud Explorer támogatja a lekérdezési nyelvet, így futtathat egy lekérdezést. Futtathat például egy lekérdezést a "Timestamp gt datetime" 2016-02-02T21:20:00Z "" lekérdezéssel, hogy biztosan megkapja a legfrissebb eseményeket. Az időzóna megfelel az UTC-nek. Az ott látható információk megfelelnek a beállított méretezési szabályoknak? A következő példában a 20. gép PROCESSZORa 100%-ra nőtt az elmúlt öt percben.
    
    ![Tárolási táblák][tables]
    
    Ha az adat nem létezik, az azt jelenti, hogy a probléma a virtuális gépeken futó diagnosztikai bővítmény. Ha az adat ott van, az azt jelenti, hogy probléma van a méretezési szabályokkal vagy az elemzések szolgáltatással. Az [Azure-állapot](https://azure.microsoft.com/status/)ellenõrzése.
    
    Ha a fenti lépésekkel még mindig rendelkezik az autoskálázással kapcsolatos problémákkal, próbálja ki a következő erőforrásokat: 
    * Tekintse meg a [Microsoft Q&egy kérdés oldalát](/answers/topics/azure-virtual-machines.html)vagy a [stack túlcsordulását](https://stackoverflow.com/questions/tagged/azure) 
    * Egy támogatási hívás naplózása. Készüljön fel a sablon megosztására és a teljesítményadatok megtekintésére.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
