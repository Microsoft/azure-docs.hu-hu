---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: c0a0b44b3de088539a070f1182248c4e6db7303f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97510277"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása

Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű integrációs modul az a komponens, amely a gépen lévő SQL Server adatait másolja Azure SQL Databasere. 

1. Hozzon létre egy változót az integrációs modul nevéhez. Adjon meg egy egyedi nevet, és jegyezze fel. Az oktatóanyag későbbi részében használni fogja. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Hozzon létre egy saját üzemeltetésű integrációs modult. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Itt látható a minta kimenete:

   ```console
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Futtassa az alábbi parancsot a létrehozott integrációs modul állapotának lekéréséhez. Ellenőrizze, hogy a **State** tulajdonság **NeedRegistration** értékű-e. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Itt látható a minta kimenete:

   ```console  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Futtassa az alábbi parancsot a hitelesítési kulcsok lekéréséhez, hogy a saját üzemeltetésű integrációsmodul-átjárót regisztrálhassa az Azure Data Factory szolgáltatásban a felhőben: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Itt látható a minta kimenete:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Másolja be az egyik kulcsot (idézőjelek nélkül), amelyet a következő lépésekben a gépére telepítendő, saját üzemeltetésű integrációs modul regisztrálásához használt.  

## <a name="install-the-integration-runtime-tool"></a>Az Integration Runtime eszköz telepítése

1. Ha az integrációs modul már telepítve van a számítógépen, akkor a **Programok hozzáadása vagy eltávolítása** funkcióval távolítsa el. 

2. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) a saját üzemeltetésű integrációs modult egy helyi windowsos gépre. Futtassa a telepítést.

3. Az **Üdvözli a Microsoft Integration Runtime telepítővarázsló** oldalon kattintson a **Tovább** gombra.

4. A **Végfelhasználói licencszerződés** oldalon fogadja el a felhasználási feltételeket és a licencszerződést, majd válassza a **Tovább** gombot.

5. A **Célmappa** oldalon válassza a **Tovább** gombot.

6. **A Microsoft Integration Runtime telepítésre kész** oldalon válassza a **Telepítés** elemet.

7. **A Microsoft Integration Runtime telepítővarázslójának befejező lapján** kattintson a **Befejezés** gombra.

8. Az **Integration Runtime (helyi) regisztrálása** lapon illessze be az előző szakaszban mentett kulcsot, és válassza a **Regisztráció** elemet. 

    ![Az integrációs modul regisztrálása](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. Az **új Integration Runtime (helyi) csomópont** lapon válassza a **Befejezés** lehetőséget. 

10. Az integrációs modul sikeres regisztrációja esetén a következő üzenet jelenik meg:

    ![Sikeres regisztráció](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. Az **Integration Runtime (helyi) regisztrálása** lapon válassza a **Konfigurációkezelő indítása** elemet.

15. A csomópont a felhőszolgáltatáshoz való csatlakoztatásakor a következő oldal jelenik meg:

    ![Csomópont csatlakoztatva oldal](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Teszteltje az SQL Server-adatbázissal létesített kapcsolatot.

    ![Diagnosztika lap](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. A **Configuration Manager** ablakban váltson a **Diagnosztika** lapra.

    b. Válassza az **SqlServer** értéket az adatforrás típusaként.

    c. Adja meg a kiszolgáló nevét.

    d. Adja meg az adatbázis nevét.

    e. Válassza ki a hitelesítési módot.

    f. Adja meg a felhasználónevet.

    : Adja meg a felhasználónévhez társított jelszót.

    h. Válassza a **Teszt** elemet annak ellenőrzéséhez, hogy az integrációs modul kapcsolódni tud-e az SQL Serverhez. Sikeres kapcsolódás esetén egy zöld pipa jelenik meg. Sikertelen kapcsolódás esetén egy hibaüzenet jelenik meg. Javítsa ki a hibákat, és ellenőrizze, hogy az integrációs modul kapcsolódik-e az SQL Serverhez.    

    > [!NOTE]
    > Jegyezze fel a hitelesítési típus, a kiszolgáló, az adatbázis, a felhasználó és a jelszó értékeit. Az oktatóanyag részében használni fogja őket.
