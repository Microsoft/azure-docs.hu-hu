---
title: Az összetevőkkel kapcsolatos hibák elhárítása Azure DevTest Labsban | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani az összetevők egy Azure DevTest Labs virtuális gépen való alkalmazása során felmerülő problémákat.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483193"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Az összetevők Azure DevTest Labs virtuális gépen való alkalmazásával kapcsolatos hibák elhárítása
Az összetevők virtuális gépen való alkalmazása különböző okok miatt sikertelen lehet. Ez a cikk néhány, a lehetséges okok azonosítását megkönnyítő módszert ismertet.

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot a Azure DevTest Labs (DTL) szakértőivel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget.   

> [!NOTE]
> Ez a cikk a Windows és a nem Windows rendszerű virtuális gépekre is vonatkozik. Habár vannak különbségek, a jelen cikkben kifejezetten a továbbiakban is meghívja őket.

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépések
Győződjön meg arról, hogy a virtuális gép fut. A DevTest Labs működéséhez a virtuális gépnek futnia kell, és a [Microsoft Azure virtuálisgép-ügynök (VM-ügynök)](../virtual-machines/extensions/agent-windows.md) telepítve van és készen áll.

> [!TIP]
> A **Azure Portal** keresse meg a virtuális gép összetevők **kezelése** lapját, és ellenőrizze, hogy a virtuális gép készen áll-e az összetevők alkalmazására. Ekkor megjelenik egy üzenet az oldal tetején. 
> 
> **Azure PowerShell** használatával vizsgálja meg a jelző **canApplyArtifacts**, amely csak akkor lesz visszaadva, ha kibontja a Get műveletet. Tekintse meg a következő példában szereplő parancsot:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>A hibakeresés módjai 
A DevTest Labs és a Resource Manager-alapú üzemi modell használatával létrehozott virtuális gépeket a következő módszerek egyikével lehet elhárítani:

- **Azure Portal** – nagyszerű, ha gyorsan be kell olvasnia a problémát okozó vizuális mutatót.
- **Azure PowerShell** – ha egy PowerShell-parancssort használ, gyorsan lekérdezheti a DevTest Labs-erőforrásokat a Azure PowerShell-parancsmagok használatával.

> [!TIP]
> Az összetevők virtuális gépen belüli végrehajtásának ellenőrzésével kapcsolatos további információkért lásd: [az összetevők hibáinak diagnosztizálása a laborban](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Tünetek, okok és lehetséges megoldások 

### <a name="artifact-appears-to-stop-responding"></a>Az összetevő úgy tűnik, hogy nem válaszol

Egy összetevő úgy tűnik, hogy nem válaszol, amíg egy előre definiált időtúllépési időszak lejár, és az összetevő **sikertelenként** van megjelölve.

Ha egy összetevő úgy tűnik, hogy lefagy, először határozza meg, hogy hol ragadt meg. A végrehajtás során a következő lépések bármelyikével blokkolható egy összetevő:

- **A kezdeti kérelem során**. A DevTest Labs létrehoz egy Azure Resource Manager sablont, amely az egyéni parancsfájl-kiterjesztés (CSE) használatát kéri. Ezért a színfalak mögött egy erőforráscsoport üzemelő példánya aktiválódik. Ha ezen a szinten hiba történik, az adott virtuális géphez tartozó erőforráscsoport **tevékenység naplójában** részletes információkat talál.  
    - A tevékenység naplóját a labor virtuális gép oldalának navigációs sávjából érheti el. Ha kiválasztja, megjelenik egy bejegyzés, amely az összetevők **virtuális gépre való alkalmazására** vonatkozik (ha az összetevők alkalmazása műveletet közvetlenül aktiválták), vagy **virtuális gépeket ad hozzá vagy módosít** (ha az összetevők alkalmazása művelet a virtuális gép létrehozási folyamatának része volt).
    - Keresse meg a hibákat a bejegyzések alatt. Időnként a hiba nem lesz címkézve, és minden egyes bejegyzést meg kell vizsgálnia.
    - Az egyes bejegyzések adatainak kivizsgálása során mindenképpen tekintse át a JSON-adattartalom tartalmát. Előfordulhat, hogy a dokumentum alján egy hibaüzenet jelenik meg.
- **Az összetevő futtatására tett kísérlet során**. Hálózati vagy tárolási problémákhoz vezethet. A részletekért tekintse meg a jelen cikk későbbi részében található megfelelő szakaszt. A szkript létrehozási módja miatt is előfordulhat. Például:
    - Egy PowerShell-parancsfájl **kötelező paraméterekkel** rendelkezik, de az egyik nem tud értéket adni neki, mert lehetővé teszi, hogy a felhasználó üresen hagyja, vagy mert nem rendelkezik alapértelmezett értékkel a tulajdonsághoz a artifactfile.jsa definíciós fájlban. A parancsfájl nem válaszol, mert a felhasználói bevitelre vár.
    - A PowerShell-parancsfájlok végrehajtásának részeként **felhasználói bevitelre van szükség** . A parancsfájlokat úgy kell írni, hogy a beavatkozás nélkül is csendesen működjenek.
- **A virtuálisgép-ügynök hosszú ideig tart, hogy készen** álljon. A virtuális gép első indításakor, vagy ha az egyéni parancsfájl-bővítményt először telepíti az összetevők alkalmazására irányuló kérelem kiszolgálására, akkor a virtuális gépnek frissítenie kell a virtuálisgép-ügynököt, vagy várnia kell a virtuálisgép-ügynök inicializálására. Lehetnek olyan szolgáltatások, amelyeken a virtuálisgép-ügynök attól függ, hogy a rendszer mennyi időt vesz igénybe az inicializáláskor. Ilyen esetekben az [Azure Virtual Machine Agent áttekintése](../virtual-machines/extensions/agent-windows.md) című témakörben talál további hibaelhárítást.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>Annak ellenőrzése, hogy az összetevő úgy tűnik-e, hogy a parancsfájl miatt nem válaszol

1. Jelentkezzen be a szóban forgó virtuális gépre.
2. Másolja a szkriptet helyileg a virtuális gépre, vagy keresse meg a virtuális gépen a alatt `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` . Ez az a hely, ahol az összetevők parancsfájljai le vannak töltve.
3. Rendszergazda jogú parancssor használatával helyileg hajthatja végre a parancsfájlt, és megadhatja a probléma okának megfelelő paramétereket.
4. Annak megállapítása, hogy a parancsfájl a nemkívánatos viselkedéstől szenved-e. Ha igen, kérjen frissítést az összetevőhöz (ha a nyilvános tárházból származik); vagy végezze el a megfelelő módosításokat (ha a privát tárházból származik).

> [!TIP]
> Kiválaszthatja a [nyilvános](https://github.com/Azure/azure-devtestlab) tárházban üzemeltetett összetevőkkel kapcsolatos problémákat, és elküldheti a módosításokat a felülvizsgálati és jóváhagyási feladatokban. Tekintse meg a [readme.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) -dokumentum **hozzájárulások** szakaszát.
> 
> További információ a saját összetevők írásáról: [AUTHORING.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) Document.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>Annak ellenőrzése, hogy az összetevő úgy tűnik-e, hogy a virtuálisgép-ügynök miatt nem válaszol:
1. Jelentkezzen be a szóban forgó virtuális gépre.
2. A Fájlkezelőben navigáljon a **C:\WindowsAzure\logs**.
3. Keresse meg és nyissa meg a **WaAppAgent. log** fájlt.
4. Keresse meg azokat a bejegyzéseket, amelyek megmutatják, hogy mikor induljon el a virtuálisgép-ügynök, és hogy mikor fejezi be az inicializálást (azaz az első szívverést küldik). Előnyben részesített újabb bejegyzések vagy konkrétan az az időszak, amelyben a problémát tapasztalja.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    Ebben a példában láthatja, hogy a virtuálisgép-ügynök indítási ideje 10 perc és 20 másodperc volt, mivel a szívverés elküldése megtörtént. Ebben az esetben az OOBE szolgáltatás hosszú időt vesz igénybe.

> [!TIP]
> Az Azure-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuálisgép-bővítmények és-funkciók](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Tárolási hibák
A DevTest Labs-nek hozzáférést kell adni a labor Storage-fiókjához, amely az összetevők gyorsítótárazására lett létrehozva. Ha a DevTest Labs egy összetevőt alkalmaz, a rendszer beolvassa az összetevő-konfigurációt és a hozzá tartozó fájlokat a konfigurált adattárakból. Alapértelmezés szerint a DevTest Labs konfigurálja a **nyilvános** összetevő-tárházhoz való hozzáférést.

A virtuális gép konfigurálásának módjától függően előfordulhat, hogy nincs közvetlen hozzáférése ehhez a tárházhoz. Ezért a DevTest Labs a labor első inicializálása során létrehozott Storage-fiókban gyorsítótárazza az összetevőket.

Ha a Storage-fiókhoz való hozzáférés bármilyen módon le van tiltva, mivel a virtuális gépről az Azure Storage szolgáltatásba való adatforgalom le van tiltva, az alábbihoz hasonló hibaüzenet jelenhet meg:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

A fenti hiba az összetevők **kezelése** területen az összetevők **eredményei** lap **központi telepítési üzenet** szakaszában jelenik meg. Emellett a kérdéses virtuális gép erőforráscsoport alatt is megjelenik a **tevékenység naplófájljaiban** .

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Az Azure Storage szolgáltatással folytatott kommunikáció megakadályozása érdekében:

- **A hozzáadott hálózati biztonsági csoportok (NSG-EK) keresése**. Előfordulhat, hogy egy előfizetési szabályzatot adott hozzá, ahol a NSG automatikusan konfigurálva vannak az összes virtuális hálózaton. Ez hatással lenne a labor alapértelmezett virtuális hálózatára, ha van ilyen, vagy a laborban konfigurált más virtuális hálózat, amelyet a virtuális gépek létrehozására használnak.
- Győződjön meg arról, hogy **az alapértelmezett Lab Storage-fiókja** (azaz a labor létrehozásakor létrehozott első Storage-fiók, amelynek a neve általában az "a" betűvel kezdődik, és egy több számjegyből álló számmal végződik \<labname\> ).
    1. Navigáljon a laborhoz tartozó erőforráscsoporthoz.
    2. Keresse meg a Storage- **fiók** típusú erőforrást, amelynek a neve megegyezik az egyezménnyel.
    3. Navigáljon a Storage-fiók lapra **tűzfalak és virtuális hálózatok** néven.
    4. Győződjön meg arról, hogy az **minden hálózatra** van beállítva. Ha a **kiválasztott hálózatok** lehetőség be van jelölve, akkor győződjön meg arról, hogy a labor virtuális gépek létrehozásához használt virtuális hálózatai hozzáadódnak a listához.

További részletes hibaelhárítást az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../storage/common/storage-network-security.md)című témakörben talál.

> [!TIP]
> **Ellenőrizze a hálózati biztonsági csoport szabályait**. Az [IP-folyamat ellenőrzésével](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) ellenőrizze, hogy egy hálózati biztonsági csoportban lévő szabály blokkolja-e a virtuális gép felé irányuló vagy onnan érkező forgalmat. A hatályos biztonsági csoportok szabályait is ellenőrizheti, hogy a bejövő **engedélyezési** NSG szabály létezik-e. További információ: [hatékony biztonsági szabályok használata a virtuális gépek forgalmának hibakereséséhez](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Egyéb hibás források
Más, ritkábban előforduló hiba lehetséges. Győződjön meg arról, hogy az egyes esetekben értékeli, hogy az adott esetre vonatkozik-e. A következők egyike: 

- **A privát tárház lejárt személyes hozzáférési jogkivonata**. Ha lejárt, az összetevő nem jelenik meg, és a lejárt magánhálózati hozzáférési jogkivonattal rendelkező tárházban található összetevőkre hivatkozó parancsfájlok ennek megfelelően sikertelenek lesznek.

## <a name="next-steps"></a>Következő lépések
Ha ezen hibák egyike sem történt meg, és továbbra sem tudja alkalmazni az összetevőket, egy Azure-támogatási incidenst is betölthet. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.
