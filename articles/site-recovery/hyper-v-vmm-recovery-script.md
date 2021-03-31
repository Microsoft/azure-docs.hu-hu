---
title: Parancsfájl hozzáadása helyreállítási tervhez Azure Site Recovery
description: Megtudhatja, hogyan adhat hozzá VMM-szkriptet helyreállítási tervhez a VMM-felhőkben futó Hyper-V virtuális gépek vész-helyreállításához.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: 3217c30737a133c1c1092fc4a8a8caaa0338e980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425874"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>VMM-parancsfájl hozzáadása helyreállítási tervhez

Ez a cikk bemutatja, hogyan hozhat létre System Center Virtual Machine Manager (VMM) parancsfájlt, és hogyan adhatja hozzá egy helyreállítási tervhez a [Azure site Recoveryban](site-recovery-overview.md).

A cikk alján található megjegyzéseket vagy kérdéseket felteheti, vagy a [Microsoft Q&az Azure Recovery Services kérdéseit](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Előfeltételek

A helyreállítási tervekben PowerShell-parancsfájlokat is használhat. Ahhoz, hogy elérhető legyen a helyreállítási tervből, el kell készítenie a szkriptet, és el kell helyeznie a szkriptet a VMM könyvtárba. A szkript írása közben tartsa szem előtt a következő szempontokat:

* Győződjön meg arról, hogy a parancsfájlok a try-catch blokkokat használják, hogy a kivételek szabályosan kezelhetők legyenek.
    - Ha kivétel történik a parancsfájlban, a parancsfájl leáll, és a feladat sikertelenként jelenik meg.
    - Ha hiba történik, a parancsfájl hátralévő része nem fut.
    - Ha hiba lép fel a nem tervezett feladatátvétel futtatásakor, a helyreállítási terv folytatódik.
    - Ha egy tervezett feladatátvétel futtatásakor hiba történik, a helyreállítási terv leáll. Javítsa ki a parancsfájlt, ellenőrizze, hogy az a várt módon fut-e, majd futtassa újra a helyreállítási tervet.
        - A `Write-Host` parancs helyreállítási terv parancsfájljában nem működik. Ha `Write-Host` parancsfájlban használja a parancsot, a parancsfájl végrehajtása sikertelen lesz. A kimenet létrehozásához hozzon létre egy proxy-parancsfájlt, amely a fő parancsfájlt futtatja. Annak érdekében, hogy az összes kimenet kifelé legyen, használja az **\>\>** parancsot.
        - A parancsfájl túllépi az időkorlátot, ha az 600 másodpercen belül nem tér vissza.
        - Ha bármilyen STDERR van írva, a parancsfájl nem sikerült besorolású. Ezek az információk a parancsfájl-végrehajtás részleteiben jelennek meg.

* A helyreállítási tervben szereplő parancsfájlok a VMM-szolgáltatásfiók környezetében futnak. Győződjön meg arról, hogy ez a fiók rendelkezik olvasási engedéllyel ahhoz a távoli megosztáshoz, amelyen a parancsfájl található. Tesztelje a parancsfájlt úgy, hogy a VMM-szolgáltatásfiók azonos szintű felhasználói jogosultságokkal fusson.
* A VMM-parancsmagok egy Windows PowerShell-modulban érkeznek. A modul telepítése a VMM-konzol telepítésekor történik. A modul parancsfájlba való betöltéséhez használja a következő parancsot a parancsfájlban: 

    `Import-Module -Name virtualmachinemanager`

    További információ: Ismerkedés [a Windows PowerShell-lel és a VMM](/previous-versions/system-center/system-center-2012-R2/hh875013(v=sc.12)).
* Győződjön meg arról, hogy legalább egy függvénytár-kiszolgálója van a VMM üzemelő példányában. Alapértelmezés szerint a VMM-kiszolgáló megosztott elérési útja helyileg található a VMM-kiszolgálón. A mappa neve MSCVMMLibrary.

  Ha a megosztott kódtár elérési útja távoli (vagy helyi, de nem a MSCVMMLibrary van megosztva), a következőképpen konfigurálja a megosztást a \\ libserver2. contoso. com\share\ használatával példaként:
  
  1. Nyissa meg a Beállításszerkesztőt, majd lépjen a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration** elemre.

  1. Módosítsa a **ScriptLibraryPath** értékét a **\\ \libserver2.contoso.com\share \\** értékre. A teljes FQDN meghatározása. Adja meg a megosztási hely engedélyeit. Ez a megosztás legfelső szintű csomópontja. A legfelső szintű csomópont kereséséhez a VMM-ben nyissa meg a könyvtárban található legfelső szintű csomópontot. A megnyíló elérési út az elérési út gyökerét képezi. Ezt az elérési utat kell használnia a változóban.

  1. Tesztelje a parancsfájlt egy olyan felhasználói fiók használatával, amely azonos szintű felhasználói jogosultságokkal rendelkezik, mint a VMM-szolgáltatásfiók. Ezen felhasználói jogosultságok használata ellenőrzi, hogy az önálló, tesztelt parancsfájlok ugyanúgy futnak-e, mint a helyreállítási tervekben. A VMM-kiszolgálón a következőképpen állítsa be a végrehajtási házirendet a mellőzésre:

     a. Nyissa meg a **64 bites Windows PowerShell-** konzolt rendszergazdaként.
     
     b. Adja meg a **Set-ExecutionPolicy mellőzése** értéket. További információ: [a Set-ExecutionPolicy parancsmag használata](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)).

     > [!IMPORTANT]
     > Set **-ExecutionPolicy kitérő** beállítása csak a 64 bites PowerShell-konzolon. Ha a 32 bites PowerShell-konzolra állítja be, a parancsfájlok nem futnak.

## <a name="add-the-script-to-the-vmm-library"></a>A parancsfájl hozzáadása a VMM-könyvtárhoz

Ha rendelkezik VMM, létrehozhat egy parancsfájlt a VMM-kiszolgálón. Ezután vegye fel a szkriptet a helyreállítási tervbe.

1. A könyvtár megosztásában hozzon létre egy új mappát. Például: \<VMM server name> \MSSCVMMLibrary\RPScripts. Helyezze a mappát a forrás és a cél VMM-kiszolgálókra.
1. Hozza létre a parancsfájlt. Adja meg például a RPScript parancsfájl nevét. Ellenőrizze, hogy a parancsfájl a várt módon működik-e.
1. Helyezze a parancsfájlt a \<VMM server name> forrás és a cél VMM-kiszolgálók \MSSCVMMLibrary mappájába.

## <a name="add-the-script-to-a-recovery-plan"></a>A parancsfájl hozzáadása helyreállítási tervhez

Miután hozzáadta a virtuális gépeket vagy replikációs csoportokat egy helyreállítási tervhez, és létrehozta a csomagot, hozzáadhatja a parancsfájlt a csoporthoz.

1. Nyissa meg a helyreállítási tervet.
1. A **lépés** listában válasszon ki egy elemet. Ezután válassza a **parancsfájl** vagy a **manuális művelet** lehetőséget.
1. Adja meg, hogy a parancsfájlt vagy műveletet a kijelölt elem előtt vagy után kívánja-e hozzáadni. Ha feljebb **vagy lejjebb szeretné** helyezni a szkript pozícióját, válassza a **feljebb és lejjebb** gombokat.
1. Ha VMM-parancsfájlt ad hozzá, válassza a **FELADATÁTVÉTEL VMM szkriptet**. A **parancsfájl elérési útja** mezőben adja meg a megosztás relatív elérési útját. Adja meg például a következőt: **\RPScripts\RPScript.PS1**.
1. Ha hozzáad egy Azure Automation runbook, adja meg azt az Automation-fiókot, amelyben a runbook található. Ezután válassza ki a használni kívánt Azure runbook-szkriptet.
1. Annak érdekében, hogy a parancsfájl a várt módon működjön, végezzen feladatátvételi tesztet a helyreállítási tervből.


## <a name="next-steps"></a>Következő lépések
* További információ a [feladatátvételek futtatásáról](site-recovery-failover.md).

