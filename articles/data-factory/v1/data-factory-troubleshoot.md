---
title: Azure Data Factory problémák elhárítása
description: Ismerje meg, hogyan lehet elhárítani a Azure Data Factory használatával kapcsolatos problémákat.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
robots: noindex
ms.openlocfilehash: ed831e5f07eb29110b858dfb16b73f276926424f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388243"
---
# <a name="troubleshoot-data-factory-issues"></a>Data Factory-hibák elhárítása
> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. 

Ez a cikk a Azure Data Factory használatakor felmerülő problémákkal kapcsolatos hibaelhárítási tippeket tartalmaz. Ez a cikk nem sorolja fel az összes lehetséges problémát a szolgáltatás használata során, de bizonyos problémákkal és általános hibaelhárítási tippekkel foglalkozik.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Hiba: Az előfizetés nincs regisztrálva a Microsoft.DataFactory névtér használatára
Amennyiben ezt a hibaüzenetet kapja, az Azure Data Factory erőforrás-szolgáltató nincs regisztrálva a számítógépén. Tegye a következőket:

1. Indítsa el az Azure PowerShellt.
2. Jelentkezzen be az Azure-fiókjába a következő parancs használatával.

   ```powershell
   Connect-AzAccount
   ```

3. Futtassa a következő parancsot a Azure Data Factory-szolgáltató regisztrálásához.

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
   ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Probléma: jogosulatlan hiba Data Factory parancsmag futtatásakor
Valószínűleg nem a megfelelő Azure-fiókot vagy előfizetést használja az Azure PowerShell futtatásához. Az alábbi parancsmagokkal válassza ki a megfelelő Azure-fiókot és előfizetést az Azure PowerShell használatához.

1. Connect-AzAccount – a megfelelő felhasználói azonosító és jelszó használata
2. Get-AzSubscription – megtekintheti a fiókhoz tartozó összes előfizetést.
3. Select-AzSubscription &lt; előfizetés neve &gt; – válassza ki a megfelelő előfizetést. Használja ugyanazt a szolgáltatást, amellyel létrehoz egy adatgyárat a Azure Portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Probléma: nem sikerült elindítani adatkezelés Gateway Express telepítőjét Azure Portal
Az adatkezelési átjáró expressz telepítéséhez az Internet Explorer vagy egy Microsoft ClickOnce-kompatiblis webböngésző szükséges. Amennyiben az expressz telepítés nem indul el, tegye a következők egyikét:

* Használja az Internet Explorert vagy a Microsoft ClickOnce-kompatibilis webböngészőt.

    Ha a Chrome-ot használja, lépjen a [Chrome webáruházba](https://chrome.google.com/webstore/), keressen a "ClickOnce" kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, és telepítse.

    Tegye ugyanezt a Firefox esetében is (telepítse a bővítményt). Kattintson az eszköztár Menü megnyitása gombjára (három vízszintes csík a jobb felső sarokban). Kattintson a Kiegészítők menüpontra, keressen a „ClickOnce” kulcsszóra, jelöljön ki egy ClickOnce-bővítmény, és telepítse azt.
* A portálon ugyanezen a panelen megjelenik a **manuális telepítési** hivatkozás. Ezt a módszert használja a telepítési fájl letöltésére és manuális futtatására. Miután a telepítés sikeres volt, megjelenik az adatkezelés átjáró konfigurációja párbeszédpanel. Másolja ki a **kulcsot** a Portál képernyőről, és a konfigurációkezelőben ezt használva regisztrálja az átjárót a szolgáltatásban.  

### <a name="problem-fail-to-connect-to-sql-server"></a>Probléma: nem sikerült csatlakozni a SQL Serverhoz
Indítsa el **adatkezelés átjárót Configuration Manager** az átjárót használó számítógépen, és a **Hibaelhárítás** lapon tesztelje a SQL Serverhoz való kapcsolódást az átjáró gépről. A kapcsolat/átjáróval kapcsolatos problémák elhárításához kapcsolódó tippekért lásd: [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .   

### <a name="problem-input-slices-are-in-waiting-state-forever"></a>Probléma: a bemeneti szeletek örökre várakozó állapotban vannak
Különböző okok miatt a szeletek **várakozási** állapotba kerülhetnek. Az egyik gyakori oka, hogy a **külső** tulajdonság értéke nem **true (igaz**). A Azure Data Factory hatókörén kívül létrehozott adatkészleteket **külső** tulajdonsággal kell megjelölni. Ez a tulajdonság azt jelzi, hogy az adatforrások kívül vannak, és az adatelőállítón belüli folyamatok nem rendelkeznek biztonsági mentéssel. Az adatszeletek **Készként** vannak jelölve, amint elérhetőek az adatok a megfelelő tárban.

Tekintse meg a következő példát az **external** tulajdonság használatáról. Megadhatja a **externalData*** beállítást is, ha a külsőt igaz értékre állítja.

A tulajdonságról az [adatkészleteket](data-factory-create-datasets.md) ismertető cikkben talál további információt.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

A hiba megoldásához adja hozzá az **external** tulajdonságot és a választható **externalData** szakaszt a bemeneti tábla JSON-definíciójához, és hozza létre ismét a táblát.

### <a name="problem-hybrid-copy-operation-fails"></a>Probléma: a hibrid másolási művelet meghiúsul
Az [átjáróval kapcsolatos hibák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) a helyszíni adattárakból a adatkezelés átjáró használatával történő másolásával kapcsolatos hibák elhárítása című témakörben található.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Probléma: az igény szerinti HDInsight kiépítés meghiúsul
HDInsightOnDemand típusú társított szolgáltatás használata esetén olyan linkedServiceName nevet kell megadni, amely egy Azure Blob Storage-ra mutat. A Data Factory szolgáltatás ezt a tárolót használja az igény szerinti HDInsight-fürt naplófájljainak és a kapcsolódó fájloknak a tárolásához.  Néha az igény szerinti HDInsight-fürt kiépítése meghiúsul, és a következő hibaüzenet jelenik meg:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Ez a hiba többnyire azt jelzi, hogy a linkedServiceName szolgáltatásban megadott tárfiók nem ugyanazon az adatközpont-helyen található, ahol a HDInsight kiépítése történik. Példa: Ha az adat-előállító az USA nyugati régiójában van, az Azure Storage pedig az USA keleti régiójában található, az USA nyugati régiójában az igény szerinti kiépítés meghiúsul.

Az additionalLinkedServiceNames egy másik JSON-tulajdonság, amelyben további tárfiókok adhatók meg az igény szerinti HDInsight szolgáltatáshoz. A további csatolt Storage-fiókoknak ugyanabban a helyen kell lenniük, mint a HDInsight-fürtnek, vagy a hiba miatt meghiúsulnak.

### <a name="problem-custom-net-activity-fails"></a>Probléma: az egyéni .NET-tevékenység meghiúsul
A részletes lépésekért lásd: [folyamat hibakeresése egyéni tevékenységgel](data-factory-use-custom-activities.md#troubleshoot-failures) .

## <a name="use-azure-portal-to-troubleshoot"></a>A Azure Portal használata a hibák megoldásához
### <a name="using-portal-blades"></a>Portál-pengék használata
Lásd: [folyamat figyelése](data-factory-monitor-manage-pipelines.md) lépések.

### <a name="using-monitor-and-manage-app"></a>A Megfigyelés és kezelés alkalmazás használata
A részletekért lásd: az [adat-előállító folyamatok figyelése és kezelése az alkalmazás figyelésével és kezelésével](data-factory-monitor-manage-app.md) .

## <a name="use-azure-powershell-to-troubleshoot"></a>A Azure PowerShell használata a hibák megoldásához
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Azure PowerShell használata a hibák megoldásához
További részletekért lásd: [Data Factory folyamatok figyelése Azure PowerShell használatával](data-factory-monitor-manage-pipelines.md) .

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: /previous-versions/azure/dn835050(v=azure.100)

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png