---
title: Saját üzemeltetésű Integration Runtime létrehozása
description: Ismerje meg, hogyan hozhat létre saját üzemeltetésű integrációs modult Azure Data Factoryban, amely lehetővé teszi, hogy az adatfeldolgozók hozzáférhessenek az adattárakhoz a magánhálózaton.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.date: 02/10/2021
ms.openlocfilehash: 3e61b6a0f17d2d21aaaebc5ff42b0221cf851a4b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389500"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Helyi integrációs modul létrehozása és konfigurálása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Integration Runtime (IR) az a számítási infrastruktúra, amelyet a Azure Data Factory használ az adatintegrációs képességek biztosítására különböző hálózati környezetekben. Az IR-vel kapcsolatos részletekért lásd: [Integration Runtime – áttekintés](concepts-integration-runtime.md).

A saját üzemeltetésű integrációs modul képes a másolási tevékenységek futtatására egy felhőalapú adattár és egy magánhálózat adattára között. A helyszíni hálózaton vagy egy Azure-beli virtuális hálózaton is elküldheti az átalakítási tevékenységeket a számítási erőforrásokkal szemben. A saját üzemeltetésű integrációs modul telepítésének helyszíni gépnek vagy virtuális gépnek kell lennie egy magánhálózat belsejében.  

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy saját üzemeltetésű integrációs modult.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="considerations-for-using-a-self-hosted-ir"></a>A saját üzemeltetésű integrációs modul használatának szempontjai

- Egyetlen saját üzemeltetésű integrációs modult is használhat több helyszíni adatforráshoz. Ugyanezen Azure Active Directory (Azure AD) bérlőn belül is megoszthatja azt egy másik adatgyárral. További információ: [a saját üzemeltetésű integrációs modul megosztása](./create-shared-self-hosted-integration-runtime-powershell.md).
- A saját üzemeltetésű integrációs modulnak csak egy példányát telepítheti egyetlen gépen is. Ha két, a helyszíni adatforrásokhoz hozzáférő adatfeldolgozóval rendelkezik, használja a saját üzemeltetésű [IR megosztási szolgáltatást](./create-shared-self-hosted-integration-runtime-powershell.md) a saját üzemeltetésű integrációs modul megosztásához, vagy telepítse a saját üzemeltetésű IR-t két helyszíni számítógépre, egyet az egyes adatelőállítók számára.  
- A saját üzemeltetésű integrációs modulnak nem kell ugyanazon a gépen lennie, mint az adatforrásnak. Azonban a saját üzemeltetésű integrációs modul az adatforráshoz való közelsége csökkenti a saját üzemeltetésű integrációs modul adatforráshoz való csatlakozásának idejét. Javasoljuk, hogy a saját üzemeltetésű integrációs modult olyan gépre telepítse, amely eltér a helyszíni adatforrást üzemeltető gépről. Ha a saját üzemeltetésű Integration Runtime és az adatforrás különböző gépeken található, a saját üzemeltetésű integrációs modul nem versenyez az erőforrások adatforrásával.
- Több saját üzemeltetésű integrációs modult is használhat különböző gépeken, amelyek ugyanahhoz a helyszíni adatforráshoz csatlakoznak. Ha például két olyan saját üzemeltetésű integrációs modulja van, amely két adat-előállítót szolgál ki, akkor ugyanaz a helyszíni adatforrás regisztrálható mindkét adat-előállítók esetében.
- Saját üzemeltetésű integrációs modul használata az Azure-beli virtuális hálózaton belüli Adatintegráció támogatásához.
- Az adatforrást olyan helyszíni adatforrásként kezelheti, amely tűzfal mögött található, még akkor is, ha az Azure ExpressRoute-t használja. A saját üzemeltetésű integrációs modul használatával kapcsolódhat a szolgáltatáshoz az adatforráshoz.
- Használja a saját üzemeltetésű integrációs modult, még akkor is, ha az adattár a felhőben van egy Azure-beli infrastruktúra-szolgáltatás (IaaS) virtuális gép.
- Előfordulhat, hogy a feladatok sikertelenek lehetnek a saját üzemeltetésű integrációs modulban, amelyet egy olyan Windows-kiszolgálóra telepített, amelyen engedélyezve van az FIPS-kompatibilis titkosítás. A probléma megkerüléséhez két lehetősége van: tárolja a hitelesítő adatokat/titkos értékeket egy Azure Key Vault, vagy tiltsa le a FIPS-kompatibilis titkosítást a kiszolgálón. A FIPS-kompatibilis titkosítás letiltásához módosítsa a beállításjegyzék következő alkulcsának értékét 1 (engedélyezve) értékről 0 (letiltva) értékre: `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` . Ha a saját üzemeltetésű [integrációs modult proxyként használja a SSIS Integration Runtime számára](./self-hosted-integration-runtime-proxy-ssis.md), akkor a FIPS-kompatibilis titkosítás engedélyezhető, és a rendszer az adatok a helyszínről az Azure-ba blob Storage átmeneti területként való áthelyezésekor használható.

## <a name="command-flow-and-data-flow"></a>A parancs folyamata és adatfolyama

Ha a helyszíni és a felhő közötti adatáthelyezést végez, a tevékenység egy saját üzemeltetésű integrációs modult használ az adatok átvitelére egy helyszíni adatforrás és a felhő között.

Az alábbiakban a saját üzemeltetésű integrációs modulról történő másoláshoz szükséges adatáramlási lépések magas szintű összefoglalását olvashatja:

![Az adatfolyamok magas szintű áttekintése](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Egy adatfejlesztő létrehoz egy saját üzemeltetésű integrációs modult egy Azure-beli adatgyárban az Azure Portal vagy a PowerShell-parancsmag használatával.

2. Az adatfejlesztő létrehoz egy társított szolgáltatást a helyszíni adattárakhoz. A fejlesztő ehhez a saját üzemeltetésű Integration Runtime-példányt kell megadnia, amelyet a szolgáltatásnak az adattárakhoz való csatlakozáshoz használnia kell.

3. A saját üzemeltetésű Integration Runtime csomópontja titkosítja a hitelesítő adatokat a Windows adatvédelmi alkalmazásprogramozási felületének (DPAPI) használatával, és a hitelesítő adatokat helyileg menti. Ha több csomópont van beállítva a magas rendelkezésre álláshoz, a hitelesítő adatok tovább szinkronizálhatók más csomópontok között. Az egyes csomópontok a DPAPI használatával titkosítják a hitelesítő adatokat, és helyileg tárolják azokat. A hitelesítő adatok szinkronizálása átlátható az adatfejlesztő számára, és a saját üzemeltetésű integrációs modul kezeli.

4. A Azure Data Factory a saját üzemeltetésű integrációs modulval kommunikál a feladatok ütemezhetik és kezelhetik. A kommunikáció egy olyan vezérlési csatornán keresztül történik, amely megosztott [Azure Relay](../azure-relay/relay-what-is-it.md#wcf-relay) kapcsolatot használ. Ha egy tevékenység-feladatnak futnia kell, Data Factory várólistára helyezi a kérést, valamint a hitelesítő adatokat. Így abban az esetben, ha a hitelesítő adatok még nem tárolódnak a saját üzemeltetésű integrációs modulban. A saját üzemeltetésű integrációs modul elindítja a feladatot, miután lekérdezi a várólistát.

5. A saját üzemeltetésű integrációs modul a helyszíni tároló és a Felhőbeli tároló közötti Adatmásolást végzi. A másolás iránya attól függ, hogy a másolási tevékenység hogyan van konfigurálva az adatfolyamatban. Ebben a lépésben a saját üzemeltetésű integrációs modul közvetlenül kommunikál a felhőalapú tárolási szolgáltatásokkal, például az Azure Blob Storage-nal egy biztonságos HTTPS-csatornán keresztül.

## <a name="prerequisites"></a>Előfeltételek

- A Windows támogatott verziói a következők:
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

A saját üzemeltetésű integrációs modul tartományvezérlőre történő telepítése nem támogatott.

- A saját üzemeltetésű integrációs modulhoz 64 bites operációs rendszer szükséges a .NET-keretrendszer 4.7.2 vagy újabb verziójának használatával. A részletekért lásd a [.NET-keretrendszer rendszerkövetelményeit](/dotnet/framework/get-started/system-requirements) ismertető témakört.
- A saját üzemeltetésű integrációs modul ajánlott minimális konfigurációja egy 2 GHz-es processzor, 4 maggal, 8 GB RAM-mal és 80 GB szabad merevlemez-területtel. A rendszerkövetelmények részleteiért lásd: [Letöltés](https://www.microsoft.com/download/details.aspx?id=39717).
- Ha a gazdaszámítógép hibernált állapotba kerül, a saját üzemeltetésű integrációs modul nem válaszol az adatkérésekre. Konfigurálja a megfelelő energiasémát a számítógépen a saját üzemeltetésű integrációs modul telepítése előtt. Ha a gép hibernált állapotra van állítva, a saját üzemeltetésű integrációs modul telepítője egy üzenettel kéri.
- A saját üzemeltetésű integrációs modul sikeres telepítéséhez és konfigurálásához rendszergazdának kell lennie a gépen.
- A másolási tevékenység futtatása egy adott gyakorisággal történik. A processzor és a RAM-használat a gépen ugyanazt a mintát követi, mint a csúcs és az üresjárati idő. Az erőforrás-használat az áthelyezett adatok mennyiségétől is nagy mértékben függ. Ha több másolási feladat van folyamatban, az erőforrás-használat a csúcs idején jelenik meg.
- Előfordulhat, hogy a feladatok a parketta, az ork vagy a Avro formátumú adatok kinyerése során meghiúsulnak. A parkettával kapcsolatos további információkért lásd: [parketta formátum Azure Data Factoryban](./format-parquet.md#using-self-hosted-integration-runtime). A fájl létrehozása a saját üzemeltetésű integrációs gépen fut. Ahhoz, hogy a várt módon működjön, a fájl létrehozásához a következő előfeltételek szükségesek:
  - [Visual C++ 2010 újraterjeszthető](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) csomag Csomag (x64)
  - Java Runtime (JRE) 8-as verzió egy JRE-szolgáltatótól, például a [OpenJDK elfogadása](https://adoptopenjdk.net/). Győződjön meg arról, hogy a `JAVA_HOME` környezeti változó a JRE mappára van beállítva (és ne csak a JDK-mappát).

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul beállítása

Saját üzemeltetésű integrációs modul létrehozásához és beállításához kövesse az alábbi eljárásokat.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Önkiszolgáló IR létrehozása Azure PowerShell használatával

1. Ehhez a feladathoz Azure PowerShell is használhatja. Alább bemutatunk egy példát:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) és telepítse a saját üzemeltetésű integrációs modult egy helyi gépre.

3. Kérje le a hitelesítési kulcsot, és regisztrálja a saját üzemeltetésű integrációs modult a kulccsal. Itt látható egy PowerShell-példa:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Saját üzemeltetésű IR létrehozása Azure Data Factory felhasználói felületen keresztül

A következő lépésekkel hozhat létre saját üzemeltetésű integrációs modult Azure Data Factory felhasználói felület használatával.

1. Azure Data Factory felhasználói felületének első **lépések** oldalán válassza a [kezelés fület](./author-management-hub.md) a bal szélső ablaktáblán.

   ![A Kezdőlap kezelés gombja](media/doc-common-process/get-started-page-manage-button.png)

1. Válassza az **integrációs** modulok lehetőséget a bal oldali ablaktáblán, majd válassza az **+ új** lehetőséget.

   ![Integrációs modul létrehozása](media/doc-common-process/manage-new-integration-runtime.png)

1. Az **Integration Runtime telepítése** lapon válassza az **Azure, a saját** üzemeltetésű lehetőséget, majd kattintson a **Folytatás** gombra.

1. A következő lapon válassza a **saját** üzemeltetésű Self-Hosted IR létrehozása lehetőséget, majd kattintson a **Folytatás** gombra.
   ![Selfhosted IR létrehozása](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. Adja meg az IR nevét, majd válassza a **Létrehozás** lehetőséget.

1. Az **integrációs modul telepítése** lapon válassza az **1. lehetőség** alatt található hivatkozást az expressz telepítő megnyitásához a számítógépen. Vagy kövesse a 2. **beállítás** lépéseit a manuális beállításhoz. Az alábbi utasítások a manuális telepítésen alapulnak:

   ![Integrációs modul telepítése](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Másolja és illessze be a hitelesítési kulcsot. Válassza **az Integration Runtime letöltése és telepítése** lehetőséget.

    1. Töltse le a saját üzemeltetésű integrációs modult egy helyi windowsos gépre. Indítsa el a telepítőt.

    1. A **Integration Runtime (helyi) regisztrálása** lapon illessze be a korábban mentett kulcsot, és válassza a **regisztráció** lehetőséget.

       ![Az integrációs modul regisztrálása](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Az **új Integration Runtime (helyi) csomópont** lapon válassza a **Befejezés** lehetőséget.

1. A saját üzemeltetésű integrációs modul sikeres regisztrálását követően a következő ablak jelenik meg:

    ![Sikeres regisztráció](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Saját üzemeltetésű IR beállítása Azure-beli virtuális gépen Azure Resource Manager sablon használatával

A saját üzemeltetésű IR-telepítőt automatizálhatja egy Azure-beli virtuális gépen az önkiszolgáló [IR-sablon létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)lehetőség használatával. A sablon egyszerű módszert kínál a teljesen működőképes, saját üzemeltetésű IR-k Azure-beli virtuális hálózaton belüli üzemeltetésére. Az IR magas rendelkezésre állási és méretezhetőségi funkciókkal rendelkezik, feltéve, hogy a csomópontok száma 2 vagy magasabb.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Meglévő saját üzemeltetésű IR beállítása helyi PowerShell használatával

Egy meglévő, saját üzemeltetésű integrációs modul beállításához vagy kezeléséhez használhatja a parancssort. Ez a használat különösen segít automatizálni a saját üzemeltetésű IR-csomópontok telepítését és regisztrálását.

A Dmgcmd.exe a saját üzemeltetésű telepítő része. Ez általában a C:\Program Files\Microsoft Integration Runtime\4.0\Shared\ mappában található. Ez az alkalmazás támogatja a különböző paramétereket, és az automatizáláshoz használható batch-parancsfájlok használatával parancssorból hívható meg.

Az alkalmazást a következőképpen használhatja:

```powershell
dmgcmd ACTION args...
```

Az itt található információk az alkalmazás műveleteiről és argumentumokról: 

|MŰVELET|args|Description|
|------|----|-----------|
|`-rn`,<br/>`-RegisterNewNode`|"`<AuthenticationKey>`" ["`<NodeName>`"]|Regisztrálja a saját üzemeltetésű Integration Runtime csomópontot a megadott hitelesítési kulccsal és csomópont nevével.|
|`-era`,<br/>`-EnableRemoteAccess`|"`<port>`" ["`<thumbprint>`"]|A magas rendelkezésre állású fürt beállításához engedélyezze a távelérést az aktuális csomóponton. Vagy engedélyezze a hitelesítő adatok közvetlen beállítását a saját üzemeltetésű IR-n keresztül anélkül, hogy Azure Data Factory. Ezt az utóbbit a **New-AzDataFactoryV2LinkedServiceEncryptedCredential** parancsmag használatával végezheti el ugyanazon a hálózaton lévő távoli gépről.|
|`-erac`,<br/>`-EnableRemoteAccessInContainer`|"`<port>`" ["`<thumbprint>`"]|Engedélyezze a távoli hozzáférést az aktuális csomóponthoz, amikor a csomópont egy tárolóban fut.|
|`-dra`,<br/>`-DisableRemoteAccess`||Távoli hozzáférés letiltása az aktuális csomóponthoz. A többcsomópontos telepítéshez távoli hozzáférés szükséges. A **New-AzDataFactoryV2LinkedServiceEncryptedCredential PowerShell-** parancsmag még akkor is működik, ha a távoli hozzáférés le van tiltva. Ez a viselkedés akkor igaz, ha a parancsmagot a saját üzemeltetésű IR-csomóponttal megegyező gépen hajtja végre.|
|`-k`,<br/>`-Key`|"`<AuthenticationKey>`"|Felülírja vagy frissítse az előző hitelesítési kulcsot. Ügyeljen erre a műveletre. A korábbi saját üzemeltetésű IR-csomópont offline állapotba léphet, ha a kulcs egy új integrációs modul.|
|`-gbf`,<br/>`-GenerateBackupFile`|"`<filePath>`" "`<password>`"|Biztonságimásolat-fájl létrehozása az aktuális csomóponthoz. A biztonságimásolat-fájl tartalmazza a csomópont kulcsát és az adattároló hitelesítő adatait.|
|`-ibf`,<br/>`-ImportBackupFile`|"`<filePath>`" "`<password>`"|Állítsa vissza a csomópontot egy biztonságimásolat-fájlból.|
|`-r`,<br/>`-Restart`||Indítsa újra a saját üzemeltetésű Integration Runtime Host szolgáltatást.|
|`-s`,<br/>`-Start`||Indítsa el a saját üzemeltetésű Integration Runtime Host szolgáltatást.|
|`-t`,<br/>`-Stop`||Állítsa le a saját üzemeltetésű Integration Runtime Host szolgáltatást.|
|`-sus`,<br/>`-StartUpgradeService`||Indítsa el a saját üzemeltetésű Integration Runtime verziófrissítési szolgáltatását.|
|`-tus`,<br/>`-StopUpgradeService`||Állítsa le a saját üzemeltetésű Integration Runtime verziófrissítési szolgáltatását.|
|`-tonau`,<br/>`-TurnOnAutoUpdate`||Kapcsolja be a saját üzemeltetésű Integration Runtime automatikus frissítését.|
|`-toffau`,<br/>`-TurnOffAutoUpdate`||Kapcsolja ki a saját üzemeltetésű Integration Runtime automatikus frissítését.|
|`-ssa`,<br/>`-SwitchServiceAccount`|"`<domain\user>`" ["`<password>`"]|Állítsa be úgy a DIAHostService, hogy az új fiókként fusson. A rendszerfiókok és a virtuális fiókok esetében használja az üres jelszót.|

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Saját üzemeltetésű integrációs modul telepítése és regisztrálása a Microsoft letöltőközpontból

1. Nyissa meg a [Microsoft Integration Runtime letöltési lapját](https://www.microsoft.com/download/details.aspx?id=39717).
2. Válassza a **Letöltés** lehetőséget, válassza ki a 64 bites verziót, majd kattintson a **tovább** gombra. Az 32 bites verzió nem támogatott.
3. Futtassa közvetlenül a felügyelt Identity fájlt, vagy mentse a merevlemezre, és futtassa.
4. Az **üdvözlő** ablakban válasszon egy nyelvet, és kattintson a **tovább** gombra.
5. Fogadja el a Microsoft szoftverlicenc-szerződését, és válassza a **tovább** lehetőséget.
6. Válassza a **mappa** lehetőséget a saját üzemeltetésű integrációs modul telepítéséhez, majd kattintson a **Tovább gombra**.
7. A **telepítésre kész** lapon válassza a **telepítés** lehetőséget.
8. A telepítés befejezéséhez kattintson a **Befejezés** gombra.
9. Szerezze be a hitelesítési kulcsot a PowerShell használatával. Íme egy PowerShell-példa a hitelesítési kulcs lekéréséhez:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

10. A számítógépen futó Microsoft Integration Runtime Configuration Manager **regisztrálása Integration Runtime (helyi)** ablakban végezze el a következő lépéseket:

    1. Illessze be a hitelesítési kulcsot a szövegmezőbe.

    2. Ha szeretné, válassza a **hitelesítési kulcs megjelenítése** lehetőséget a kulcs szövegének megtekintéséhez.

    3. Válassza a **Regisztráció** lehetőséget.

## <a name="service-account-for-self-hosted-integration-runtime"></a>Szolgáltatásfiók a saját üzemeltetésű integrációs modulhoz

A saját üzemeltetésű Integration Runtime alapértelmezett bejelentkezési fiókja az **NT SERVICE\DIAHostService**. Megtekintheti a **szolgáltatások – > Integration Runtime szolgáltatás-> tulajdonságok – > bejelentkezés** lehetőségre.

![Szolgáltatásfiók a saját üzemeltetésű integrációs modulhoz](media/create-self-hosted-integration-runtime/shir-service-account.png)

Győződjön meg arról, hogy a fiók rendelkezik a szolgáltatásként való bejelentkezés engedélyével. Ellenkező esetben a saját üzemeltetésű integrációs modul nem tud sikeresen elindulni. Megtekintheti az engedélyt a **helyi biztonsági házirendben – > biztonsági beállítások – > helyi házirendek – > felhasználói jogok kiosztása – > Bejelentkezés szolgáltatásként**

![Képernyőfelvétel a helyi biztonsági házirendről – felhasználói jogok kiosztása](media/create-self-hosted-integration-runtime/shir-service-account-permission.png)

![Képernyőkép a szolgáltatás felhasználói jogosultságok kiosztásának bejelentkezni](media/create-self-hosted-integration-runtime/shir-service-account-permission-2.png)

## <a name="notification-area-icons-and-notifications"></a>Értesítési területek ikonjai és értesítései

Ha az egérmutatót az értesítési területen lévő ikonra vagy üzenet fölé helyezi, a saját üzemeltetésű integrációs modul állapotáról is tájékozódhat.

![Értesítések az értesítési területeken](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="high-availability-and-scalability"></a>Magas rendelkezésre állás és méretezhetőség

A saját üzemeltetésű integrációs modult több helyszíni géppel vagy virtuális géppel is társíthatja az Azure-ban. Ezeket a gépeket csomópontoknak nevezzük. Legfeljebb négy, saját üzemeltetésű integrációs modulhoz társított csomópontot lehet hozzárendelni. A következő előnyökkel jár, ha több csomóponttal rendelkezik a logikai átjáróhoz telepített átjáróval rendelkező helyszíni gépeken:

- A saját üzemeltetésű integrációs modul magasabb rendelkezésre állása annak érdekében, hogy a big data-megoldás vagy a Felhőbeli Adatintegráció a Data Factoryával többé ne legyen az egyetlen meghibásodási pont. Ez a rendelkezésre állás segít biztosítani a folytonosságot, ha legfeljebb négy csomópontot használ.
- Jobb teljesítmény és teljesítmény a helyszíni és a Felhőbeli adattárak közötti adatáthelyezés során. További információ a [teljesítmény-összehasonlításokról](copy-activity-performance.md).

Több csomópontot úgy is hozzárendelhet, ha telepíti a saját üzemeltetésű Integration Runtime szoftvert a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). Ezután regisztráljon a **New-AzDataFactoryV2IntegrationRuntimeKey** parancsmagból beszerzett hitelesítési kulcsok valamelyikével, az [oktatóanyagban](tutorial-hybrid-copy-powershell.md)leírtak szerint.

> [!NOTE]
> Nem kell létrehoznia új, saját üzemeltetésű integrációs modult az egyes csomópontok hozzárendeléséhez. Telepítheti a saját üzemeltetésű integrációs modult egy másik gépre, és ugyanazzal a hitelesítési kulccsal regisztrálhatja azt.

> [!NOTE]
> Mielőtt újabb csomópontot ad hozzá a magas rendelkezésre álláshoz és a méretezhetőséghez, győződjön meg arról, hogy az első csomóponton engedélyezve van az **intranetes távelérés** beállítás. Ehhez válassza az **Microsoft Integration Runtime Configuration Manager**  >    >  **a beállítások távoli elérés az intranethez** lehetőséget.

### <a name="scale-considerations"></a>Méretezési szempontok

#### <a name="scale-out"></a>Horizontális felskálázás

Ha a processzor használata magas, és a rendelkezésre álló memória kevés a saját üzemeltetésű integrációs modulban, adjon hozzá egy új csomópontot a gépek terhelésének kiskálázásához. Ha a tevékenységek sikertelenek, mert időtúllépés vagy a saját üzemeltetésű IR-csomópont offline állapotban van, akkor segít, ha csomópontot ad hozzá az átjáróhoz.

#### <a name="scale-up"></a>Vertikális felskálázás

Ha a processzor és a rendelkezésre álló RAM nem megfelelően van kihasználva, de az egyidejű feladatok végrehajtása eléri a csomópontok korlátait, a skálázást a csomópont által futtatható egyidejű feladatok számának növelésével növelheti. Előfordulhat, hogy vertikális felskálázásra is szükség van, amikor a tevékenységek időtúllépést okoznak, mert a saját üzemeltetésű integrációs modul túlterhelt. Ahogy az az alábbi ábrán is látható, növelheti a csomópontok maximális kapacitását:  

![A csomóponton futtatható egyidejű feladatok számának növelésére](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-tanúsítványokra vonatkozó követelmények

Az Integration Runtime-csomópontok közötti kommunikáció biztonságossá tételéhez használt TLS/SSL-tanúsítványra vonatkozó követelmények a következők:

- A tanúsítványnak nyilvánosan megbízható X509 v3 tanúsítványnak kell lennie. Javasoljuk, hogy használjon egy nyilvános partner hitelesítésszolgáltató (CA) által kiadott tanúsítványokat.
- Minden Integration Runtime csomópontnak meg kell bíznia a tanúsítványban.
- Nem javasoljuk a tulajdonos alternatív neve (SAN) tanúsítványait, mert csak az utolsó SAN-objektum van használatban. Az összes többi SAN-elemet figyelmen kívül hagyja a rendszer. Ha például egy SAN-tanúsítvánnyal rendelkezik, amelynek a **node1.domain.contoso.com** és a **node2.domain.contoso.com**, akkor ezt a tanúsítványt csak olyan gépen használhatja, amelynek teljes tartományneve (FQDN) **node2.domain.contoso.com**.
- A tanúsítvány bármely, a Windows Server 2012 R2 által támogatott kulcstárolót használhat a TLS/SSL-tanúsítványokhoz.
- A CNG-kulcsokat használó tanúsítványok nem támogatottak.  

> [!NOTE]
> Ezt a tanúsítványt használja a rendszer:
>
> - Portok titkosítása egy saját üzemeltetésű IR-csomóponton.
> - Az állapot-szinkronizálás csomópontok közötti kommunikációja esetében, amely magában foglalja a csomópontok közötti társított szolgáltatások hitelesítő adatainak szinkronizálását.
> - Ha egy PowerShell-parancsmagot használ a társított szolgáltatás hitelesítő adataihoz a helyi hálózaton belül.
>
> Javasoljuk, hogy ezt a tanúsítványt használja, ha a magánhálózati környezet nem biztonságos, vagy ha szeretné védeni a magánhálózaton belüli csomópontok közötti kommunikációt.
>
> A saját üzemeltetésű integrációs modulból más adattárakba irányuló adatáthelyezés mindig egy titkosított csatornán belül történik, függetlenül attól, hogy a tanúsítvány be van-e állítva.

## <a name="proxy-server-considerations"></a>A proxykiszolgáló szempontjai

Ha a vállalati hálózati környezet proxykiszolgálót használ az Internet eléréséhez, konfigurálja a saját üzemeltetésű integrációs modult a megfelelő proxybeállítások használatára. A proxyt a kezdeti regisztrációs fázisban állíthatja be.

![A proxy meghatározása](media/create-self-hosted-integration-runtime/specify-proxy.png)

Ha be van állítva, a saját üzemeltetésű integrációs modul a proxykiszolgálót használva csatlakozik a Cloud Service forrásához és céljához (amely a HTTP vagy a HTTPS protokollt használja). Ezért válassza a **módosítás hivatkozás** lehetőséget a kezdeti beállítás során.

![A proxy beállítása](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Három konfigurációs lehetőség közül választhat:

- Ne **használja a proxyt**: a saját üzemeltetésű integrációs modul nem használ explicit módon semmilyen proxyt a Cloud Serviceshez való csatlakozáshoz.
- A **System proxy használata**: a saját üzemeltetésű integrációs modul a diahost.exe.config és diawp.exe.config konfigurált proxybeállításokat használja. Ha ezek a fájlok nem határoznak meg proxy-konfigurációt, a saját üzemeltetésű integrációs modul közvetlenül a proxyn keresztül csatlakozik a Cloud Service-hez.
- **Egyéni proxy használata**: KONFIGURÁLJA a http-proxy beállítását a saját üzemeltetésű integrációs modulhoz a diahost.exe.config és diawp.exe.config konfigurációk használata helyett. A **címek** és a **portok** értékének megadása kötelező. A proxy hitelesítési beállításától függően a **Felhasználónév** és a **jelszó** értéke nem kötelező. Az összes beállítás titkosítása a Windows DPAPI a saját üzemeltetésű integrációs modulban és helyileg, a gépen történik.

Az Integration Runtime Host szolgáltatás automatikusan újraindul a frissített proxybeállítások mentése után.

Miután regisztrálta a saját üzemeltetésű integrációs modult, ha szeretné megtekinteni vagy frissíteni a proxybeállításokat, használja a Microsoft Integration Runtime Configuration Manager.

1. Nyissa meg **Microsoft Integration Runtime Configuration Manager**.
1. Válassza a **Settings** (Beállítások) fület.
1. A **http-proxy** alatt kattintson a **módosítás** hivatkozásra a **http-proxy beállítása** párbeszédpanel megnyitásához.
1. Kattintson a **Tovább** gombra. Ekkor megjelenik egy figyelmeztetés, amely arra kéri, hogy mentse a proxybeállításokat, és indítsa újra az Integration Runtime Host szolgáltatást.

A Configuration Manager eszköz használatával megtekintheti és frissítheti a HTTP-proxyt.

![A proxy megtekintése és frissítése](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Ha NTLM-hitelesítést használó proxykiszolgálót állít be, az Integration Runtime Host szolgáltatás a tartományi fiók alatt fut. Ha később megváltoztatja a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, majd indítsa újra a szolgáltatást. Ennek a követelménynek a miatt javasoljuk, hogy a proxykiszolgálót egy dedikált tartományi fiókkal nyissa meg, amely nem igényli, hogy gyakran frissítse a jelszót.

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló beállításainak konfigurálása

Ha a **rendszerproxy használata** lehetőséget választja a http-proxyhoz, a saját üzemeltetésű integrációs modul a proxybeállításokat használja diahost.exe.config és diawp.exe.config. Ha ezek a fájlok nem határoznak meg proxyt, a saját üzemeltetésű integrációs modul közvetlenül a proxyn keresztül csatlakozik a Cloud Service-hez. Az alábbi eljárás útmutatást nyújt a diahost.exe.config fájl frissítéséhez:

1. A Fájlkezelőben készítse el a C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config biztonságos másolatát az eredeti fájl biztonsági másolatából.
1. Nyissa meg rendszergazdaként a Jegyzettömb alkalmazást.
1. A Jegyzettömbben nyissa meg a C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config szövegfájlt.
1. Keresse meg az alapértelmezett **System.net** címkét az alábbi kódban látható módon:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Ezután a következő példában látható módon adhatja hozzá a proxykiszolgáló adatait:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    A proxy címkéje további tulajdonságokat is lehetővé tesz a szükséges beállítások megadásához, például: `scriptLocation` . A szintaxishoz lásd: [ \<proxy\> elem (hálózati beállítások)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) .

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. Mentse a konfigurációs fájlt az eredeti helyére. Ezután indítsa újra a saját üzemeltetésű Integration Runtime Host szolgáltatást, amely felveszi a módosításokat.

   A szolgáltatás újraindításához használja a Vezérlőpulton a szolgáltatások kisalkalmazást. Vagy Integration Runtime Configuration Manager kattintson a **szolgáltatás leállítása** gombra, majd válassza a **szolgáltatás indítása** lehetőséget.

   Ha a szolgáltatás nem indul el, valószínűleg helytelen XML-címkét ad hozzá a szerkesztett alkalmazás konfigurációs fájljában.

> [!IMPORTANT]
> Ne felejtse el frissíteni a diahost.exe.config és diawp.exe.config is.

Emellett meg kell győződnie arról, hogy Microsoft Azure a vállalat engedélyezési listájában van. Az érvényes Azure IP-címek listáját letöltheti a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>A tűzfalhoz és a proxykiszolgálóhoz kapcsolódó problémák lehetséges tünetei

Ha a következőhöz hasonló hibaüzenetek jelennek meg, a valószínű ok a tűzfal vagy a proxykiszolgáló helytelen konfigurációja. Ez a konfiguráció megakadályozza, hogy a saját üzemeltetésű integrációs modul csatlakozzon a Data Factoryhoz a hitelesítéshez. A tűzfal és a proxykiszolgáló megfelelő konfigurálásának biztosításához tekintse meg az előző szakaszt.

- Amikor megpróbálja regisztrálni a saját üzemeltetésű integrációs modult, a következő hibaüzenet jelenik meg: "nem sikerült regisztrálni ezt a Integration Runtime csomópontot! Győződjön meg arról, hogy a hitelesítési kulcs érvényes, és az Integration Service Host szolgáltatás fut ezen a gépen. "
- A Integration Runtime Configuration Manager megnyitásakor a **leválasztott** vagy a **Csatlakozás** állapot jelenik meg. Amikor megtekinti a Windows-eseménynaplókat, **Eseménynapló**  >  **alkalmazás-és szolgáltatások naplói**  >  **Microsoft Integration Runtime**, a következőhöz hasonló hibaüzenetek jelennek meg:

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

### <a name="enable-remote-access-from-an-intranet"></a>Távoli hozzáférés engedélyezése intranetről

Ha a PowerShell segítségével titkosítja a hitelesítő adatokat egy hálózati gépről, a saját üzemeltetésű integrációs modul telepítésének helyétől eltérő módon, engedélyezheti a **távelérést az intranetről** lehetőséggel. Ha a PowerShellt a saját üzemeltetésű integrációs modult futtató gépen lévő hitelesítő adatok titkosítására futtatja, akkor nem engedélyezheti a **távelérést az intranetről**.

Engedélyezze **a távelérést az intranetről** , mielőtt újabb csomópontot ad hozzá a magas rendelkezésre álláshoz és méretezhetőséghez.  

Ha a saját üzemeltetésű Integration Runtime 3,3-es vagy újabb verzióját futtatja, alapértelmezés szerint a saját üzemeltetésű integrációs modul telepítője letiltja a **távelérést az intranetről** a saját üzemeltetésű integrációs modult futtató számítógépen.

Ha tűzfalat használ a partner vagy mások számára, a 8060-es portot vagy a felhasználó által konfigurált portot manuálisan is megnyithatja. Ha tűzfallal kapcsolatos probléma van a saját üzemeltetésű integrációs modul beállításakor, a következő paranccsal telepítheti a saját üzemeltetésű integrációs modult a tűzfal konfigurálása nélkül:

```cmd
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Ha úgy dönt, hogy nem nyitja meg a 8060-as portot a saját üzemeltetésű integrációs modulban, használja a hitelesítő adatok beállítása az adattároló hitelesítő adatainak konfigurálására szolgáló mechanizmust. Használhatja például a **New-AzDataFactoryV2LinkedServiceEncryptCredential PowerShell-** parancsmagot.

## <a name="ports-and-firewalls"></a>Portok és tűzfalak

Két tűzfalat kell figyelembe venni:

- A szervezet központi útválasztóján futó *vállalati tűzfal*
- Az a *Windows tűzfal* , amely démonként van konfigurálva a helyi gépen, ahol a saját üzemeltetésű Integration Runtime telepítve van

![A tűzfalak](media/create-self-hosted-integration-runtime/firewall.png)

A vállalati tűzfal szintjén a következő tartományokat és kimenő portokat kell konfigurálnia:

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-internal.md)]

A Windows tűzfal szintjén vagy a számítógép szintjén ezek a kimenő portok általában engedélyezve vannak. Ha nem, akkor a tartományokat és portokat konfigurálhatja egy saját üzemeltetésű integrációs modult futtató gépen.

> [!NOTE]
> Mivel a Azure Relay jelenleg nem támogatja a Service címkét, a **AzureCloud** vagy az **Internet** szolgáltatást a NSG-szabályokban kell használnia a Azure Relay való kommunikációhoz.
> A Azure Data Factory való kommunikációhoz használhatja a NSG-szabály telepítője **DataFactoryManagement** .

A forrás-és a elsüllyedés alapján előfordulhat, hogy további tartományokat és kimenő portokat kell engedélyeznie a vállalati tűzfalon vagy a Windows tűzfalon.

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-external.md)]

Egyes felhőalapú adatbázisok, például a Azure SQL Database és a Azure Data Lake esetében engedélyezni kell a saját üzemeltetésű Integration Runtime-gépek IP-címeit a tűzfal konfigurációjában.

### <a name="get-url-of-azure-relay"></a>Azure Relay URL-címének beolvasása

Az egyik szükséges tartomány és port, amelyet a tűzfal engedélyezési listájában kell elhelyezni, a kommunikáció Azure Relay. A saját üzemeltetésű integrációs modul az interaktív szerzői műveletek, például a kapcsolatok tesztelése, a mappák listájának és a táblázatok listájának lekérése, a séma beolvasása és az előnézeti adatelőnézet Ha nem szeretné engedélyezni a **. servicebus.Windows.net** -t, és szeretné, hogy pontosabb URL-címek legyenek, akkor a saját üzemeltetésű integrációs modul által az ADF-portálon megkövetelt teljes tartományneveket láthatja. Kövesse az alábbi lépéseket:

1. Nyissa meg az ADF-portált, és válassza ki saját üzemeltetésű integrációs modulját.
2. A Szerkesztés lapon válassza a **csomópontok** lehetőséget.
3. Válassza a **szolgáltatás URL-címeinek megjelenítése** lehetőséget az összes teljes tartománynév lekéréséhez.

   ![Azure Relay URL-címek](media/create-self-hosted-integration-runtime/Azure-relay-url.png)

4. Ezeket a teljes tartományneveket a tűzfalszabályok engedélyezési listájában adhatja hozzá.

### <a name="copy-data-from-a-source-to-a-sink"></a>Adatok másolása forrásból a fogadóba

Győződjön meg arról, hogy megfelelően engedélyezte a tűzfalszabályok beállításait a vállalati tűzfalon, a saját üzemeltetésű integrációs modul Windows tűzfalán, valamint magát az adattárat. Ezeknek a szabályoknak a engedélyezése lehetővé teszi, hogy a saját üzemeltetésű integrációs modul sikeresen csatlakozhasson a forráshoz és a fogadóhoz. Engedélyezze a szabályokat a másolási műveletben érintett összes adattárhoz.

Ha például egy helyszíni adattárból egy SQL Database fogadóba vagy egy Azure szinapszis Analytics-fogadóba szeretne másolni, hajtsa végre a következő lépéseket:

1. Engedélyezze a kimenő TCP-kommunikációt az 1433-as porton a Windows tűzfal és a vállalati tűzfal esetében is.
2. Konfigurálja a SQL Database tűzfal beállításait úgy, hogy hozzáadja a saját üzemeltetésű integrációs modul számítógépének IP-címét az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi a 1433-es kimenő portot, a saját üzemeltetésű integrációs modul nem fér hozzá közvetlenül az SQL-adatbázishoz. Ebben az esetben a SQL Database és az Azure szinapszis Analytics használatával [szakaszos másolatot](copy-activity-performance.md) készíthet. Ebben az esetben csak HTTPS (443-es port) szükséges az adatáthelyezéshez.

## <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez

A saját üzemeltetésű integrációs modult úgy is telepítheti, ha letölt egy felügyelt identitás-telepítőcsomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). A Részletes utasításokért tekintse meg a helyszíni [és a felhő közötti adatáthelyezést](tutorial-hybrid-copy-powershell.md) ismertető cikket.

- Konfiguráljon egy energiasémát a gazdagépen a saját üzemeltetésű integrációs modulhoz, hogy a gép ne legyen hibernálva. Ha a gazdaszámítógép hibernált állapotba kerül, a saját üzemeltetésű integrációs modul offline állapotba kerül.
- Rendszeresen biztonsági másolatot készít a saját üzemeltetésű integrációs modulhoz társított hitelesítő adatokról.
- A saját üzemeltetésű IR-telepítési műveletek automatizálásához tekintse meg a [meglévő saját üzemeltetésű IR beállítása a PowerShell](#setting-up-a-self-hosted-integration-runtime)használatával című témakört.

## <a name="next-steps"></a>Következő lépések

Részletes útmutatásért lásd [: oktatóanyag: helyszíni információk másolása a felhőbe](tutorial-hybrid-copy-powershell.md).
