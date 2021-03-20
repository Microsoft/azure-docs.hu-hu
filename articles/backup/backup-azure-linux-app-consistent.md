---
title: A Linux rendszerű virtuális gépek alkalmazás-konzisztens biztonsági mentései
description: A Linux rendszerű virtuális gépek alkalmazás-konzisztens biztonsági másolatait az Azure-ba hozhatja létre. Ez a cikk a parancsfájl-keretrendszer konfigurálását ismerteti az Azure-ban üzembe helyezett Linux virtuális gépek biztonsági mentéséhez. Ez a cikk hibaelhárítási információkat is tartalmaz.
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 22053004026a2dd8976027359f11d50a5663b334
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88999240"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Azure-beli linuxos virtuális gépek alkalmazáskonzisztens biztonsági mentése

A virtuális gépek biztonsági mentési pillanatképének készítésekor az alkalmazás konzisztenciája azt jelenti, hogy az alkalmazások akkor kezdődnek, amikor a virtuális gépek a visszaállítás után indulnak. Ahogy az is elképzelhető, hogy az alkalmazások konzisztenciája rendkívül fontos. Annak érdekében, hogy a Linux rendszerű virtuális gépek konzisztensek legyenek, az alkalmazással konzisztens biztonsági mentéseket használhat a Linux előtti és utáni parancsfájl-keretrendszer használatával. A pre-script és a parancsfájl utáni keretrendszer támogatja a Azure Resource Manager telepített linuxos virtuális gépeket. Az alkalmazások konzisztenciájához tartozó parancsfájlok nem támogatják Service Manager telepített virtuális gépeket vagy Windows rendszerű virtuális gépeket.

## <a name="how-the-framework-works"></a>A keretrendszer működése

A keretrendszer lehetővé teszi, hogy a VM-pillanatképek készítése közben egyéni parancsfájlok futtatását és a parancsfájlok utáni parancsfájlokat is futtasson. A virtuális gép pillanatképének megkezdése előtt a parancsfájlok futtatása előtt futtassa a parancsfájlokat, és a szkriptek azonnal futnak a virtuális gép pillanatképének elkészítése után. A parancsfájlok előtti és utáni parancsfájlok lehetővé teszik az alkalmazás és a környezet szabályozását, miközben a virtuális gépek pillanatképeit futtatja.

A parancsfájl-előkészítők natív Application API-kat indítanak, amelyek fokozatos leválasztása az IOs-et, és a memóriában lévő tartalmakat a lemezre ürítik. Ezek a műveletek biztosítják, hogy a pillanatkép alkalmazás konzisztens legyen. A szkriptek a natív alkalmazás API-kat használják az IOs kiolvasztására, ami lehetővé teszi, hogy az alkalmazás a virtuális gép pillanatképe után folytassa a normál műveleteket.

## <a name="steps-to-configure-pre-script-and-post-script"></a>A parancsfájl előtti és utáni parancsfájl konfigurálásának lépései

1. Jelentkezzen be a legfelső szintű felhasználóként arra a linuxos virtuális gépre, amelyről biztonsági másolatot szeretne készíteni.

2. A [githubról](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)töltse le **VMSnapshotScriptPluginConfig.js** , és másolja a **/etc/Azure** mappába minden olyan virtuális gép számára, amelyről biztonsági másolatot szeretne készíteni. Ha a **/etc/Azure** mappa nem létezik, hozza létre.

3. Másolja az alkalmazáshoz tartozó előzetes parancsfájlt és parancsfájlt a biztonsági mentésre tervezett összes virtuális gépre. A szkripteket a virtuális gép bármely helyére másolhatja. Ügyeljen arra, hogy a parancsfájl teljes elérési útját frissítse a **VMSnapshotScriptPluginConfig.js** fájljában.

4. Ellenőrizze a következő engedélyeket a fájlokhoz:

   - **VMSnapshotScriptPluginConfig.json**: engedély "600." Például csak a "root" felhasználó "READ" és "Write" engedélyekkel kell rendelkeznie ehhez a fájlhoz, és egyetlen felhasználónak sem kell "EXECUTE" engedélyekkel rendelkeznie.

   - **Parancsfájl előtti fájl**: engedély "700".  Például csak a "root" felhasználó "READ", "Write" és "EXECUTE" engedélyekkel kell rendelkeznie ehhez a fájlhoz.

   - **Parancsfájl utáni** Engedély "700." Például csak a "root" felhasználó "READ", "Write" és "EXECUTE" engedélyekkel kell rendelkeznie ehhez a fájlhoz.

   > [!IMPORTANT]
   > A keretrendszer nagy teljesítményt nyújt a felhasználóknak. Gondoskodjon a keretrendszer védelméről, és győződjön meg arról, hogy csak a "root" felhasználó fér hozzá a kritikus JSON-és parancsfájl-fájlokhoz.
   > Ha a követelmények nem teljesülnek, a parancsfájl nem fog futni, ami egy fájlrendszer összeomlását és inkonzisztens biztonsági mentését eredményezi.
   >

5. Konfigurálja **VMSnapshotScriptPluginConfig.jsaz** itt leírtak szerint:
    - **pluginName**: hagyja ezt a mezőt a következőnek megfelelően, vagy előfordulhat, hogy a parancsfájlok nem a várt módon működnek.

    - **preScriptLocation**: adja meg az előzetes parancsfájl teljes elérési útját azon a virtuális gépen, amelyen biztonsági mentés készül.

    - **postScriptLocation**: adja meg a virtuális gépen a parancsfájl teljes elérési útját, amelyről biztonsági mentés készül.

    - **preScriptParams**: adja meg azokat az opcionális paramétereket, amelyeket át kell adni a parancsfájl előtti előírásnak. Az összes paraméternek idézőjelek közé kell esnie. Ha több paramétert használ, a paramétereket vesszővel válassza el egymástól.

    - **postScriptParams**: adja meg azokat a választható paramétereket, amelyeket át kell adni a parancsfájl utáni művelethez. Az összes paraméternek idézőjelek közé kell esnie. Ha több paramétert használ, a paramétereket vesszővel válassza el egymástól.

    - **preScriptNoOfRetries**: állítsa be, hogy a rendszer hányszor próbálja meg újból végrehajtani az előzetes parancsfájlt, ha a megszakítás előtt hiba történt. Ha hiba történt, akkor a nulla érték csak egyetlen próbálkozást jelent.

    - **postScriptNoOfRetries**: állítsa be, hogy a rendszer hányszor próbálja meg újból végrehajtani a parancsfájlt, ha hiba történt a megszakítás előtt. Ha hiba történt, akkor a nulla érték csak egyetlen próbálkozást jelent.

    - **timeoutInSeconds**: adjon meg egyedi időtúllépést a parancsfájl előtti és a parancsfájl utáni (maximális érték 1800).

    - **continueBackupOnFailure**: állítsa **igaz** értékre, ha azt szeretné, hogy a Azure Backup a fájlrendszer konzisztens/összeomlásos konzisztens biztonsági mentésére térjen vissza, ha az előzetes parancsfájl vagy a parancsfájl utáni hiba meghiúsul. Ha a beállítás **értéke hamis** , akkor a biztonsági mentés sikertelen lesz, ha parancsfájl-meghibásodás történik (kivéve, ha egy egylemezes virtuális gép a beállítástól függetlenül visszaesik az összeomlás-konzisztens biztonsági mentésre). Ha a **continueBackupOnFailure** értéke false (hamis) értékre van állítva, ha a biztonsági mentés sikertelen, a rendszer megkísérli a biztonsági mentési műveletet a szolgáltatásban lévő újrapróbálkozási logika alapján (a megadott számú próbálkozás esetében).

    - **fsFreezeEnabled**: azt határozza meg, hogy a rendszer meghívhatja-e a Linux-fsfreeze, amikor a virtuális gép pillanatképét a fájlrendszer konzisztenciájának biztosítására kéri. Azt javasoljuk, hogy ezt a beállítást állítsa **igaz** értékre, kivéve, ha az alkalmazás nem függ a fsfreeze letiltásával.

    - **ScriptsExecutionPollTimeSeconds**: állítsa be azt az időpontot, ameddig a bővítménynek alvó állapotba kell esnie az egyes lekérdezések között a szkript végrehajtásához. Ha például az érték 2, a bővítmény ellenőrzi, hogy az előzetes/post parancsfájl végrehajtása 2 másodpercenként fejeződött-e be. A minimális és maximális érték 1 és 5 közötti lehet. Az értéknek szigorúan egész számnak kell lennie.

6. A parancsfájl-keretrendszer konfigurálva van. Ha a virtuális gép biztonsági mentése már be van állítva, a következő biztonsági mentés elindítja a parancsfájlokat, és elindítja az alkalmazás-konzisztens biztonsági mentést. Ha a virtuális gép biztonsági mentése nincs konfigurálva, állítsa be úgy, hogy az [Azure-beli virtuális gépek biztonsági](./backup-azure-vms-first-look-arm.md) mentését Recovery Services tárolók használatára konfigurálja.

## <a name="troubleshooting"></a>Hibaelhárítás

Győződjön meg arról, hogy a megfelelő naplózást adja hozzá a parancsfájl előtti és utáni parancsfájl írásakor, majd tekintse át a parancsfájl-naplókat a parancsfájlokkal kapcsolatos problémák megoldásához. Ha továbbra is problémákat tapasztal a parancsfájlok futtatásakor, további információért tekintse meg a következő táblázatot.

| Hiba | Hibaüzenet | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Előzetes ScriptExecutionFailed |Az előzetes parancsfájl hibát adott vissza, ezért előfordulhat, hogy a biztonsági mentés nem alkalmazás-konzisztens.| A probléma megoldásához tekintse meg a parancsfájlhoz tartozó hibák naplóit.|  
|ScriptExecutionFailed utáni |A post-script olyan hibát adott vissza, amely hatással lehet az alkalmazás állapotára. |A probléma megoldásához és az alkalmazás állapotának megtekintéséhez tekintse meg a parancsfájlhoz tartozó hibák naplóit. |
| Előzetes ScriptNotFound |Az előzetes parancsfájl nem található a konfigurációs fájl **VMSnapshotScriptPluginConfig.jsjában** megadott helyen. |Győződjön meg arról, hogy a konfigurációs fájlban megadott elérési úton lévő előzetes parancsfájl szerepel az alkalmazás-konzisztens biztonsági mentés biztosításához.|
| ScriptNotFound utáni |A parancsfájl nem található a konfigurációs fájlban **VMSnapshotScriptPluginConfig.js** megadott helyen. |Győződjön meg arról, hogy a parancsfájl a konfigurációs fájlban megadott elérési úton van, hogy biztosítsa az alkalmazás-konzisztens biztonsági mentést.|
| IncorrectPluginhostFile |A VmSnapshotLinux-bővítményhez tartozó **pluginhost fájlja** -fájl sérült, ezért a parancsfájl előtti és utáni parancsfájl nem futtatható, és a biztonsági mentés nem lesz alkalmazás-konzisztens.| Távolítsa el a **VmSnapshotLinux** bővítményt, és a rendszer automatikusan újratelepíti a következő biztonsági mentéssel a probléma megoldásához. |
| IncorrectJSONConfigFile | A fájl **VMSnapshotScriptPluginConfig.jsa** helytelen, ezért a parancsfájl előtti és utáni parancsfájl nem futtatható, és a biztonsági mentés nem lesz az alkalmazás-konzisztens. | Töltse le a másolatot a [githubról](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) , és konfigurálja újra. |
| InsufficientPermissionforPre-Script | A parancsfájlok futtatásához a "root" felhasználónak a fájl tulajdonosának kell lennie, és a fájlnak "700" engedélyekkel kell rendelkeznie (azaz csak a "tulajdonos" legyen "READ", "Write" és "EXECUTE" engedélyekkel). | Győződjön meg arról, hogy a "root" felhasználó a parancsfájl tulajdonosa, és hogy csak a "tulajdonos" rendelkezik "READ", "Write" és "EXECUTE" engedélyekkel. |
| InsufficientPermissionforPost-Script | A parancsfájlok futtatásához a legfelső szintű felhasználónak a fájl tulajdonosának kell lennie, és a fájlnak "700" engedélyekkel kell rendelkeznie (azaz csak a "tulajdonos" legyen "READ", "Write" és "EXECUTE" engedélyekkel). | Győződjön meg arról, hogy a "root" felhasználó a parancsfájl tulajdonosa, és hogy csak a "tulajdonos" rendelkezik "READ", "Write" és "EXECUTE" engedélyekkel. |
| Előzetes ScriptTimeout | Az alkalmazás-konzisztens biztonsági mentés előzetes parancsfájljának végrehajtása időtúllépés miatt megszakítva. | Keresse meg a parancsfájlt, és növelje a **VMSnapshotScriptPluginConfig.js** a **/etc/Azure** címen található fájlban lévő időkorlátot. |
| ScriptTimeout utáni | Az alkalmazás-konzisztens biztonsági mentési parancsfájl végrehajtása időtúllépés miatt megszakítva. | Keresse meg a parancsfájlt, és növelje a **VMSnapshotScriptPluginConfig.js** a **/etc/Azure** címen található fájlban lévő időkorlátot. |

## <a name="next-steps"></a>Következő lépések

[Virtuális gép biztonsági mentésének konfigurálása Recovery Services-tárolóra](./backup-azure-vms-first-look-arm.md)
