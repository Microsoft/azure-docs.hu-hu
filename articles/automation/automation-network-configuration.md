---
title: Azure Automation hálózati konfiguráció részletei
description: Ez a cikk a Azure Automation az állapot Azure Automation-konfiguráció, a hibrid Runbook-feldolgozó, a Update Management és a Change Tracking és a leltár számára szükséges hálózati információk részleteit tartalmazza.
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 0add7eed6abbe6c137d423ee4a7ef5f0f60072e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900208"
---
# <a name="azure-automation-network-configuration-details"></a>Azure Automation hálózati konfiguráció részletei

Ez a lap a [hibrid Runbook](#hybrid-runbook-worker-and-state-configuration)-feldolgozó és az állapot-konfiguráció, valamint a [Update Management és a Change Tracking és a leltár](#update-management-and-change-tracking-and-inventory)számára szükséges hálózatkezelési adatokat tartalmazza.

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hibrid Runbook Worker és State Configuration

A hibrid Runbook-feldolgozó számára a következő portokra és URL-címekre van szükség, valamint az [Automation-állapot konfigurációjának](automation-dsc-overview.md) Azure Automationsal való kommunikációra.

* Port: csak 443 szükséges a kimenő internet-hozzáféréshez
* Globális URL-cím: `*.azure-automation.net`
* US Gov Virginia globális URL-címe: `*.azure-automation.us`
* Ügynök szolgáltatás: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>A hibrid Runbook-feldolgozók hálózati tervezése

Ahhoz, hogy a rendszer vagy a felhasználó hibrid Runbook feldolgozója Azure Automationhoz kapcsolódjon és regisztráljon, hozzáféréssel kell rendelkeznie az ebben a részben ismertetett portszámhoz és URL-címekhez. A munkavégzőnek hozzáféréssel kell rendelkeznie a Log Analytics ügynökhöz a Azure Monitor Log Analytics munkaterülethez való csatlakozáshoz [szükséges portokhoz és URL-címekhez](../azure-monitor/platform/agent-windows.md) .

Ha rendelkezik egy adott régióhoz definiált Automation-fiókkal, akkor a hibrid Runbook-feldolgozó kommunikációt korlátozhatja az adott regionális adatközpontra. Tekintse át a [Azure Automation által használt DNS-rekordokat](how-to/automation-region-dns-records.md) a szükséges DNS-rekordokhoz.

### <a name="configuration-of-private-networks-for-state-configuration"></a>A magánhálózatok konfigurálása az állapot konfigurációjához

Ha a csomópontok egy magánhálózaton találhatók, a fent megadott portot és URL-címeket kötelező megadni. Ezek az erőforrások hálózati kapcsolatot biztosítanak a felügyelt csomópont számára, és lehetővé teszik a DSC számára a Azure Automation való kommunikációt.

Ha olyan DSC-erőforrásokat használ, amelyek a csomópontok között kommunikálnak, például a [waitfor * erőforrásai](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)között, akkor is engedélyeznie kell a csomópontok közötti forgalmat. A hálózati követelmények megismeréséhez tekintse meg az egyes DSC-erőforrások dokumentációját.

A TLS 1,2-hez szükséges ügyfél-követelmények megismeréséhez lásd: [tls 1,2 kényszerítés Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Update Management és Change Tracking és leltár

Az ebben a táblázatban szereplő címek a Update Management és a Change Tracking és a leltár esetében egyaránt szükségesek. A táblázatot követő bekezdés is vonatkozik mindkettőre.

A címekkel folytatott kommunikáció az 443-es **portot** használja.

|Azure Public  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*. ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*. oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

Ha hálózati csoport biztonsági szabályait hozza létre, vagy Azure Firewall konfigurálja az Automation szolgáltatás és a Log Analytics munkaterület forgalmának engedélyezéséhez, használja a **GuestAndHybridManagement** és a **AzureMonitor** [szolgáltatás címkéit](../virtual-network/service-tags-overview.md#available-service-tags) . Ez leegyszerűsíti a hálózati biztonsági szabályok folyamatos felügyeletét. Ha biztonságosan és magántulajdonban szeretne csatlakozni az Automation szolgáltatáshoz az Azure-beli virtuális gépekről, tekintse át az [Azure Private link használata](./how-to/private-link-security.md)című ismertetőt. A helyszíni tűzfal-konfigurációk részét képező aktuális szolgáltatási címke és tartomány információ beszerzéséhez lásd: [letölthető JSON-fájlok](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Következő lépések

* További információ az [Automation Update Management áttekintése](update-management\overview.md).
* További tudnivalók a [hibrid Runbook-feldolgozóról](automation-hybrid-runbook-worker.md).
* A [change Tracking és a leltár](change-tracking\overview.md)megismerése.
* Az [Automation-állapot konfigurációjának](automation-dsc-overview.md)megismerése.
