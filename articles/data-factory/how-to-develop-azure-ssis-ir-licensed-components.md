---
title: Licencelt összetevők telepítése az Azure-SSIS Integration Runtime-hoz
description: Ismerje meg, hogy az ISV hogyan fejleszthet és telepíthet fizetős vagy licencelt egyéni összetevőket az Azure-SSIS Integration Runtime számára
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd62822e111346ee9a81a5d1bcce55191b19da02
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386288"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Fizetős vagy licencelt egyéni összetevők telepítése az Azure-SSIS Integration Runtime-hoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogy egy ISV hogyan fejleszthet és telepíthet fizetős vagy licencelt egyéni összetevőket az Azure-ban futó SQL Server Integration Services-(SSIS-) csomagok számára az Azure-SSIS Integration Runtime-ban.

## <a name="the-problem"></a>A probléma

Az Azure-SSIS integrációs modul természete számos kihívást jelent, amelyek az egyéni összetevők helyszíni telepítésének szokásos licencelési módszereit nem kielégítően használják. Ennek eredményeképpen a Azure-SSIS IR eltérő megközelítésre van szükség.

-   A Azure-SSIS IR csomópontjai változékonyak, és bármikor lefoglalhatók vagy kiadhatók. Például elindíthatja vagy leállíthatja a csomópontokat a díjak kezeléséhez, illetve a különböző csomópontok méretének méretezésével. Ennek eredményeképpen a külső gyártótól származó licencek egy adott csomóponthoz való kötése a számítógép-specifikus adatok, például a MAC-címek vagy a CPU-AZONOSÍTÓk használatával már nem valósítható meg.

-   A Azure-SSIS IR a vagy a szolgáltatásban is méretezheti, hogy a csomópontok száma bármikor csökkenhet vagy kiterjeszthető legyen.

## <a name="the-solution"></a>A megoldás

Az előző szakaszban ismertetett hagyományos licencelési módszerek korlátai miatt a Azure-SSIS IR új megoldást biztosít. Ez a megoldás a Windows környezeti változóit és SSIS rendszerváltozókat használ a licencek kötéséhez és a külső összetevők érvényesítéséhez. A független szoftvergyártók ezeket a változókat használhatják egy Azure-SSIS IR egyedi és állandó információinak beszerzésére, például a fürt AZONOSÍTÓjának és a fürtcsomópontok számának megadására. Ezzel az információval a független szoftvergyártók az összetevőhöz tartozó licencet a *fürthöz* tartozó Azure-SSIS IRhoz kötik. Ez a kötés olyan azonosítót használ, amely nem változik az ügyfelek indításakor vagy leállításakor, vertikális fel-vagy leskálázással, méretezéssel vagy kicsinyítéssel, vagy a Azure-SSIS IR bármely módon történő újrakonfigurálásával.

Az alábbi ábrán az új változókat használó, harmadik féltől származó összetevőkre jellemző telepítési, aktiválási és licenc-kötési és érvényesítési folyamatok láthatók:

![Licencelt összetevők telepítése](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Utasítások
1. A független szoftvergyártók a licencelt összetevőiket különböző SKU-ban vagy rétegekben (például egyetlen csomópont, legfeljebb 5 csomópont, legfeljebb 10 csomópont stb.) használhatják. Az ISV biztosítja a megfelelő termékkulcsot, amikor az ügyfelek vásárolnak egy terméket. Az ISV olyan Azure Storage BLOB-tárolót is biztosíthat, amely egy ISV telepítési parancsfájlt és egy kapcsolódó fájlt tartalmaz. Az ügyfelek átmásolhatják ezeket a fájlokat a saját tárolóba, és módosíthatják azokat a saját termékkulcs használatával (például a futtatásával `IsvSetup.exe -pid xxxx-xxxx-xxxx` ). Az ügyfelek ezt követően a tároló SAS URI-ja segítségével is kioszthatják vagy konfigurálhatják a Azure-SSIS IRt paraméterként. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

2. A Azure-SSIS IR kiosztása vagy újrakonfigurálása után az ISV-telepítő minden csomóponton fut, hogy lekérdezze a Windows környezeti változóit, `SSIS_CLUSTERID` és `SSIS_CLUSTERNODECOUNT` . Ezután a Azure-SSIS IR elküldi a fürt AZONOSÍTÓját és a licenccel rendelkező termék termékkulcsot az ISV aktiválási kiszolgáló számára egy aktiválási kulcs létrehozásához.

3. Az aktiválási kulcs kézhezvétele után az ISV telepítő helyileg képes tárolni a kulcsot az egyes csomópontokon (például a beállításjegyzékben).

4. Amikor az ügyfelek olyan csomagot futtatnak, amely az ISV licenccel rendelkező összetevőjét használja a Azure-SSIS IR egyik csomópontján, a csomag beolvassa a helyileg tárolt aktiválási kulcsot, és érvényesíti a csomópont fürt-azonosítójával. A csomag a fürtcsomópontok számának megadását is lehetővé teszi az ISV-aktiválási kiszolgáló számára.

    Íme egy példa az aktiválási kulcsot érvényesítő kódra, amely a fürtcsomópontok számát jelenti:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV-partnerek

Megtalálhatja azon ISV-partnerek listáját, akik kiigazították az összetevők és a bővítmények a Azure-SSIS IR a blogbejegyzés végén, az [egyéni beállítás és a harmadik féltől származó BŐVÍTHETŐSÉG SSIS az ADF-ben](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Következő lépések

-   [Az Azure-SSIS Integration Runtime egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [A Azure-SSIS Integration Runtime Enterprise kiadása](how-to-configure-azure-ssis-ir-enterprise-edition.md)
