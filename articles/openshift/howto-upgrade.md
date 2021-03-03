---
title: Azure Red Hat OpenShift-fürt frissítése
description: Ismerje meg, hogyan frissíthet egy Azure Red Hat OpenShift-fürtöt, amely a OpenShift 4 rendszert futtatja
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720885"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Azure Red Hat OpenShift-(ARO-) fürt frissítése

Az ARO-fürt életciklusának része a legújabb OpenShift-verzió rendszeres frissítését is magában foglalja. Fontos, hogy alkalmazza a legújabb biztonsági kiadásokat, vagy frissítsen a legújabb funkciók beszerzéséhez. Ez a cikk bemutatja, hogyan frissítheti az összes összetevőt egy OpenShift-fürtön a OpenShift webkonzol használatával.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI újabb verziójának 2.0.65 kell futtatnia. Az aktuális verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető témakört.

Ez a cikk azt feltételezi, hogy egy meglévő Azure Red Hat OpenShift-fürthöz hozzáférése van jogosultságokkal rendelkező felhasználóként `admin` .

## <a name="check-for-available-aro-cluster-upgrades"></a>Az ARO-fürtök elérhető frissítéseinek keresése

A OpenShift webkonzolon válassza az **adminisztrációs**  >  **fürt beállításai** lehetőséget, majd nyissa meg a **részletek** lapot.

Ha a fürt **frissítési állapota** az **elérhető frissítéseket** tükrözi, frissítheti a fürtöt.

## <a name="upgrade-your-aro-cluster"></a>Az ARO-fürt frissítése

Az előző lépésben található webkonzolon állítsa be a **csatornát** a frissíteni kívánt verzióhoz tartozó megfelelő csatornára, például: `stable-4.5` .

Válassza ki a frissíteni kívánt verziót, majd kattintson a **frissítés** elemre. A frissítés állapota a következőre módosul: `Update to <product-version> in progress` . A fürt frissítésének előrehaladását a kezelők és a csomópontok folyamatjelző sávjának megtekintésével tekintheti át.

## <a name="next-steps"></a>Következő lépések
- [Az ARO-fürtök frissítése az OC CLI használatával](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Az elérhető OpenShift-tároló platform-tanácsadókkal és-frissítésekkel kapcsolatos információkat a Customer Portal [hibajavítások című szakaszában](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) találja.
