---
title: A Windows Azure diagnosztikai bővítmény (WAD) telepítése és konfigurálása
description: Tudnivalók a Windows diagnosztikai bővítmény telepítéséről és konfigurálásáról. Emellett megtudhatja, hogyan lehet leírást adni arról, hogyan tárolja a rendszer az adatokat és az Azure Storage-fiókot.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3ff752b673c49047551c48c4c8693b00d7b5edeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787404"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>A Windows Azure diagnosztikai bővítmény (WAD) telepítése és konfigurálása
[Az Azure diagnostics bővítmény](diagnostics-extension-overview.md) a Azure Monitor ügynöke, amely monitorozási adatokat gyűjt a vendég operációs rendszerről, valamint az Azure-beli virtuális gépek és egyéb számítási erőforrások számítási feladatairól. Ez a cikk részletesen bemutatja a Windows diagnosztikai bővítményének telepítését és konfigurálását, valamint az adatok az Azure Storage-fiókban való tárolásának leírását.

A diagnosztikai bővítmény virtuálisgép-bővítményként van megvalósítva az Azure-ban, így ugyanezeket [a](../../virtual-machines/extensions/overview.md) telepítési lehetőségeket támogatja a Resource Manager, a PowerShell és a parancssori felület használatával. A [virtuálisgép-bővítmények telepítésével](../../virtual-machines/extensions/features-windows.md) és karbantartásével kapcsolatos részletekért lásd: Virtuálisgép-bővítmények és -szolgáltatások Windows rendszeren.

## <a name="overview"></a>Áttekintés
A Windows Azure diagnosztikai bővítményének konfigurálásakor meg kell adnia egy tárfiókot, ahová a rendszer az összes megadott adatot elküldi. Igény szerint hozzáadhat egy vagy *több* adat fogadót, hogy az adatokat különböző helyekre küldje.

- Azure Monitor fogadó – Vendégteljesítmény-adatok küldése a Azure Monitor metrikákba.
- Eseményközpont-fogadó – Vendégteljesítmény- és naplóadatok küldése az Azure Event Hubsba az Azure-n kívülre való továbbításhoz. Ez a fogadó nem konfigurálható a Azure Portal.


## <a name="install-with-azure-portal"></a>Telepítés a Azure Portal
A diagnosztikai bővítményt telepítheti és konfigurálhatja a Azure Portal egy különálló virtuális gépre, amely felületet biztosít a konfigurációval való közvetlen munkavégzés helyett. Ha engedélyezi a diagnosztikai bővítményt, az automatikusan egy alapértelmezett konfigurációt fog használni, amely a leggyakoribb teljesítményszámlálókat és eseményeket használja. Ezt az alapértelmezett konfigurációt az adott követelményeknek megfelelően módosíthatja.

> [!NOTE]
> Az alábbiakban a diagnosztikai bővítmény leggyakoribb beállításait ismertetjuk. Az összes konfigurációs beállítás részleteit lásd: [A Windows diagnosztikai bővítmény sémája.](diagnostics-extension-schema-windows.md)

1. Nyissa meg a virtuális gép menüjét a Azure Portal.

2. Kattintson a **Virtuális gép menü** **Figyelés** szakaszában található Diagnosztikai beállítások elemre.

3. Ha a diagnosztikai **bővítmény** még nincs engedélyezve, kattintson a Vendégszintű figyelés engedélyezése lehetőségre.

   ![Monitorozás engedélyezése](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. A rendszer létrehoz egy új Azure Storage-fiókot a virtuális géphez névvel, amely a virtuális gép erőforráscsoport-nevén alapul, és kiválasztja a vendégteljesítmény-számlálók és -naplók alapértelmezett készletét.

   ![Diagnosztikai beállítások](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. A **Teljesítményszámlálók lapon** válassza ki a virtuális gépről gyűjteni kívánt vendégmetrikákat. A **speciálisabb kijelöléshez** használja az Egyéni beállítást.

   ![Teljesítményszámlálók](media/diagnostics-extension-windows-install/performance-counters.png)

6. A **Naplók lapon** válassza ki a virtuális gépről gyűjteni kívánt naplókat. A naplókat elküldheti a storage-nak vagy az event hubsnak, de nem Azure Monitor. A [Log Analytics-ügynökkel vendégnaplókat](../agents/log-analytics-agent.md) gyűjthet a Azure Monitor.

   ![Képernyőkép a Naplók lapról, a virtuális géphez kiválasztott különböző naplókkal.](media/diagnostics-extension-windows-install/logs.png)

7. Az **Összeomlási memóriakép lapon** adja meg az összeomlás utáni memóriaképeket gyűjtő folyamatokat. Az adatok a diagnosztikai beállítás tárfiókba lesznek írva, és igény szerint megadhat egy blobtárolót is.

   ![Összeomlási memóriaképek](media/diagnostics-extension-windows-install/crash-dumps.png)

8. A **Fogadók lapon** adja meg, hogy az adatokat az Azure-tárolótól különböző helyekre küldje-e a rendszer. Ha a **Azure Monitor** lehetőséget választja, a rendszer elküldi a vendégteljesítmény-adatokat a Azure Monitor metrikáknak. Az Event Hubs-fogadó nem konfigurálható a Azure Portal.

   ![Képernyőkép a Fogadók lapról, az Engedélyezett diagnosztikai adatok Azure Monitor lehetőséggel.](media/diagnostics-extension-windows-install/sinks.png)
   
   Ha nem engedélyezte a virtuális géphez konfigurált rendszer-hozzárendelt identitást, az alábbi figyelmeztetést láthatja, amikor menti a konfigurációt a Azure Monitor fogadóval. Kattintson a szalagcímre a rendszer által hozzárendelt identitás engedélyezéséhez.
   
   ![Felügyelt entitás](media/diagnostics-extension-windows-install/managed-entity.png)

9. Az **Ügynökben** módosíthatja a tárfiókot, beállíthatja a lemezkvótát, és megadhatja, hogy kell-e diagnosztikai infrastruktúra-naplókat gyűjteni.  

   ![Képernyőkép az Ügynök lapról a tárfiók beállításának lehetőségével.](media/diagnostics-extension-windows-install/agent.png)

10. A **konfiguráció mentéshez** kattintson a Mentés gombra. 

> [!NOTE]
> Bár a diagnosztikai bővítmény konfigurációja formázható JSON vagy XML formátumban, a Azure Portal konfigurációk mindig JSON-ként lesznek tárolva. Ha xml-t használ egy másik konfigurációs módszerrel, majd módosítja a konfigurációt a Azure Portal, a beállítások JSON-re módosulnak.

## <a name="resource-manager-template"></a>Resource Manager-sablon
Lásd: [Monitorozás és](../../virtual-machines/extensions/diagnostics-template.md) diagnosztika használata Windows rendszerű virtuális gépekkel, Azure Resource Manager a diagnosztikai bővítmény sablonokkal való üzembe helyezéséről Azure Resource Manager sablonokat. 

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése
Az Azure CLI használatával üzembe helyezheti a Azure Diagnostics-bővítményt egy meglévő virtuális gépen [az az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) használatával, ahogyan az alábbi példában is. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

A védett beállításokat a konfigurációs séma [PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) eleme határozza meg. Az alábbiakban egy minimális példát mutatunk be egy védett beállításfájlra, amely meghatározza a tárfiókot. A [privát beállítások részletes](diagnostics-extension-schema-windows.md#privateconfig-element) adataiért lásd: Példakonfiguráció.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

A nyilvános beállítások a konfigurációs [séma Nyilvános](diagnostics-extension-schema-windows.md#publicconfig-element) elemében vannak definiálva. Az alábbiakban egy minimális példát mutatunk be egy nyilvános beállításfájlra, amely lehetővé teszi a diagnosztikai infrastruktúra naplóinak, egyetlen teljesítményszámlálónak és egyetlen eseménynaplónak a gyűjtését. A [nyilvános beállítások részletes](diagnostics-extension-schema-windows.md#publicconfig-element) adataiért lásd: Példakonfiguráció.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>A PowerShell telepítése
A PowerShell használatával üzembe helyezheti a Azure Diagnostics-bővítményt egy meglévő virtuális gépen a [Set-AzVMDiagnosticsExtension parancs](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) használatával, ahogyan az alábbi példában is. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

A privát beállításokat a [PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element)elemben, a nyilvános beállításokat pedig a konfigurációs séma [Nyilvános](diagnostics-extension-schema-windows.md#publicconfig-element) elemében definiáljuk. Dönthet úgy is, hogy a tárfiók adatait a Set-AzVMDiagnosticsExtension parancsmag paramétereiként adja meg ahelyett, hogy a privát beállításokba beleveszi őket.

Az alábbiakban egy minimális példa található egy konfigurációs fájlra, amely lehetővé teszi a diagnosztikai infrastruktúra naplóinak, egyetlen teljesítményszámlálónak és egyetlen eseménynaplónak a gyűjtését. A [privát és](diagnostics-extension-schema-windows.md#publicconfig-element) nyilvános beállítások részletes adataiért lásd: Példakonfiguráció. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Lásd [még: Use PowerShell to enable Azure Diagnostics in a virtual machine running Windows](../../virtual-machines/extensions/diagnostics-windows.md)(A PowerShell Azure Diagnostics engedélyezése Windows rendszerű virtuális gépen).

## <a name="data-storage"></a>Adattárolás
Az alábbi táblázat a diagnosztikai bővítményből gyűjtött különböző adattípusokat sorolja fel, és hogy azok táblaként vagy blobként vannak-e tárolva. A táblákban tárolt adatok a nyilvános konfiguráció [StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) beállításától függően blobban is tárolhatók.


| Adatok | Tárolási típus | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tábla | Diagnosztikai monitor és konfigurációváltozások. |
| WADDirectoriesTable | Tábla | A diagnosztikai figyelő által figyelt könyvtárak.  Ide tartoznak az IIS-naplók, az IIS sikertelen kérésnaplói és az egyéni könyvtárak.  A blob naplófájlja a Tároló mezőben van megadva, a blob neve pedig a RelativePath mezőben.  Az AbsolutePath mező a fájl helyét és nevét jelzi, ahogyan az az Azure-beli virtuális gépen létezett. |
| WadLogsTable | Tábla | Kódba írt naplók a nyomkövetési listenerrel. |
| WADPerformanceCountersTable | Tábla | Teljesítményszámlálók. |
| WADWindowsEventLogsTable | Tábla | Windows-eseménynaplók. |
| wad-iis-failedreqlogfiles | Blob | Az IIS sikertelen kérelmek naplóiból tartalmaz információkat. |
| wad-iis-logfiles | Blob | Az IIS-naplókról tartalmaz információkat. |
| "egyéni" | Blob | A diagnosztikai figyelő által figyelt könyvtárak konfigurálásán alapuló egyéni tároló.  A blobtároló neve a WADDirectoriesTable könyvtárban lesz megadva. |

## <a name="tools-to-view-diagnostic-data"></a>Diagnosztikai adatok megtekintésére használt eszközök
Az adatok a tárolóba való átvitele után több eszköz is megtekinthető. Például:

* Server Explorer az Visual Studio-ban – Ha telepítette az Azure Tools for Microsoft Visual Studio-t, a Server Explorer Azure Storage csomópontját használva megtekintheti az Azure Storage-fiókokból származó csak olvasható blob- és táblaadatokat. A helyi Storage Emulator-fiókból és az Azure-hoz létrehozott tárfiókok adatai is megjeleníthetőek. További információ: [Tárolási erőforrások tallózása és kezelése a Server Explorerrel.](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy különálló alkalmazás, amellyel egyszerűen dolgozhat Azure Storage-adatokkal Windows, OSX és Linux rendszeren.
* [Az Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) tartalmazza Azure Diagnostics Managert, amely lehetővé teszi az Azure-ban futó alkalmazások által gyűjtött diagnosztikai adatok megtekintését, letöltését és kezelését.

## <a name="next-steps"></a>Következő lépések
- A [monitorozási adatoknak](diagnostics-extension-stream-event-hubs.md) a virtuális eszközökre való Event Hubs Adatok küldése a Windows Azure diagnosztikai bővítményből a Azure Event Hubs.
