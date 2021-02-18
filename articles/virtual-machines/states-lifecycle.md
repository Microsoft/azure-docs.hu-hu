---
title: Az Azure-beli virtuális gépek életciklusa és állapota
description: Az Azure-beli virtuális gépek életciklusának áttekintése, beleértve azon állapotok leírását, amelyeket a virtuális gép bármikor tartalmazhat.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 7f91d5eebfae990f60b47f2c98f364046bafbf04
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594365"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Virtuális gépek életciklusa és állapota

Az Azure Virtual Machines (VM-EK) különböző állapotokon haladnak át, amelyek beoszthatók *kiépítés* és *energiaellátási* állapotba. Ennek a cikknek a célja, hogy leírja ezeket az állapotokat, és kiemelje, hogy mikor kell kiszámítani az ügyfeleket a példányok használatáért. 

## <a name="power-states"></a>Energiaállapotok

A Power állapot a virtuális gép utolsó ismert állapotát jelöli.

![Virtuális gép energiagazdálkodási állapotának diagramja](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
A következő táblázat ismerteti az egyes példányok állapotát, és azt jelzi, hogy a példány használatban van-e, vagy sem.

:::row:::
   :::column span="":::

   **Állam**
   
   :::column-end:::
   :::column span="":::

   **Leírás**

   :::column-end:::
   :::column span="":::

   **Példány-használat számlázása**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Indítás**

   :::column-end:::
   :::column span="":::

   A virtuális gép elindul.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Nem számlázott**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Futó**

   :::column-end:::
   :::column span="":::

   Virtuális gép normál működésének állapota

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Számlázása**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Leállítás**

   :::column-end:::
   :::column span="":::

   Ez egy átmeneti állapot. Ha elkészült, a rendszer **Leállítottként** jeleníti meg.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Számlázása**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Leállítva**

   :::column-end:::
   :::column span="":::

   A virtuális gép le lett állítva a vendég operációs rendszerből, vagy az erő API-k használatával.

   A hardver továbbra is a virtuális gép számára van lefoglalva, és a gazdagépen marad.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Számlázása***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Felszabadítás**

   :::column-end:::
   :::column span="":::

   Átmeneti állapot. Ha elkészült, a virtuális gép fel lesz **töltve.**

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Nem számlázott***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Felszabadítva**

   :::column-end:::
   :::column span="":::

   A virtuális gép sikeresen leállt, és el lett távolítva a gazdagépről.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Nem számlázott**

   :::column-end:::
:::row-end:::


Bizonyos Azure-erőforrások, például a lemezek és a hálózatkezelés &#42;ért díjat számítunk fel. A példányon lévő szoftverlicenc-licencek nem számítanak fel díjat.

## <a name="provisioning-states"></a>Kiépítési állapotok

A kiépítési állapot a felhasználó által kezdeményezett, vezérlő-sík művelet állapota a virtuális gépen. Ezek az állapotok eltérhetnek a virtuális gép energiagazdálkodási állapotától.

- **Létrehozás** – virtuális gép létrehozása.

- **Update** – frissíti egy meglévő virtuális gép modelljét. Néhány nem Modelles változás a virtuális gépen, például az indítás/újraindítás alatt is a frissítés alatt marad.

- **Delete** – virtuális gép törlése.

- **Felszabadítás** – a virtuális gép leállítása és eltávolítása a gazdagépről. A virtuális gép felszabadítása frissítésnek minősül, ezért a frissítéshez kapcsolódó kiépítési állapotokat fogja megjeleníteni.



Az alábbi átmeneti művelet azt jelzi, hogy a platform elfogadta a felhasználó által kezdeményezett műveletet:

:::row:::
   :::column span="":::

   **Állam**
   
   :::column-end:::
   :::column span="2":::

   **Leírás**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Létrehozás**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Frissítés**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Törlés**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OPERÁCIÓSRENDSZER-kiépítési állapotok**
   
   :::column-end:::
   :::column span="2":::

   **Leírás**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Ha egy virtuális gépet operációs rendszer rendszerképével hoztak létre, és nem speciális képpel, akkor a következő alállapotok figyelhetők meg:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   A virtuális gép fut, és a vendég operációs rendszer telepítése folyamatban van.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Rövid életű állapot. A virtuális gép gyorsan átvált a **sikerre** , kivéve, ha a bővítményeket telepíteni kell. A bővítmények telepítése időt vehet igénybe.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Megjegyzés**: az operációs rendszer üzembe helyezése **sikertelen** állapotra vált, ha az operációs rendszer meghibásodása miatt nem sikerül időben telepíteni az operációs rendszert. Az ügyfeleket az infrastruktúrában az üzembe helyezett virtuális gép számlázása alapján számítjuk fel.

   :::column-end:::

:::row-end:::

A művelet befejezését követően a virtuális gép a következő állapotok egyikére vált:

- **Sikeres** – a felhasználó által kezdeményezett műveletek befejeződtek.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Sikertelen** – sikertelen műveletet jelöl. További információért és lehetséges megoldásokhoz tekintse meg a hibakódokat.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Virtuálisgép-példány nézet

A példány nézet API a virtuális gép futtatásával kapcsolatos információkat biztosít. További információkért tekintse meg a [Virtual Machines-példány nézet](/rest/api/compute/virtualmachines/instanceview) API dokumentációját.

Az Azure Resources Explorer egyszerű KEZELŐFELÜLETet biztosít a virtuális gép futási állapotának megtekintéséhez: [erőforrás-kezelő](https://resources.azure.com/).

A kiépítési állapotok a virtuális gép tulajdonságai és a példány nézetében láthatók. A Power állapotok a virtuális gép példány nézetében érhetők el.

Az előfizetésben lévő összes virtuális gép energiaellátási állapotának lekéréséhez használja [Virtual Machines az összes olyan API-](/rest/api/compute/virtualmachines/listall) t, amelynek **statusOnly** a paraméter értéke *true (igaz*).

## <a name="next-steps"></a>Következő lépések

További információ a virtuális gép figyeléséről: [Virtual Machines in Azure (virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)).