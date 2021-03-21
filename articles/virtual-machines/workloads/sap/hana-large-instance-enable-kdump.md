---
title: Parancsfájl a Kdump engedélyezéséhez SAP HANAban (nagyméretű példányok) | Microsoft Docs
description: Parancsfájl a Kdump engedélyezéséhez SAP HANA (nagyméretű példányok) HLI típusú I, HLI Type II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4b06cbcbef79e243116bddb33adbcf6476fac8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213368"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>SAP HANA on Azure Large Instances Kdump (HLI)

A kdump konfigurálása és engedélyezése egy olyan lépés, amely szükséges a rendszerösszeomlások hibakereséséhez, amelyek nem egyértelműek.
Időnként előfordulhat, hogy a rendszer váratlanul leáll, és nem magyarázható hardver-vagy infrastruktúra-probléma.
Ezekben az esetekben ez lehet operációs rendszer vagy alkalmazási probléma, és a kdump lehetővé teszi a SUSE számára, hogy megtudja, miért összeomlott a rendszer.

## <a name="enable-kdump-service"></a>Kdump szolgáltatás engedélyezése

Ez a dokumentum ismerteti a Kdump szolgáltatás Azure HANA nagyméretű példányon való engedélyezésének részleteit (**i és II** típus)

## <a name="supported-skus"></a>Támogatott SKU-i

|  Hana nagyméretű példány típusa   |  Operációs rendszer szállítója   |  Operációs rendszer csomagjának verziója   |  Termékváltozat |
|-----------------------------|--------------|-----------------------|-------------|
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Típus                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Típus                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Típus                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   II. típus                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   II. típus                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   II. típus                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   II. típus                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   II. típus                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   II. típus                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   II. típus                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   II. típus                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   II. típus                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Előfeltételek

- A Kdump szolgáltatás `/var/crash` címtárat használ a memóriaképek írásához, győződjön meg arról, hogy a partíciónak megfelelő hely van a memóriaképek elhelyezéséhez.

## <a name="setup-details"></a>Telepítés részletei

- A Kdump engedélyezéséhez szükséges parancsfájl [itt](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh) található.
> [!NOTE]
> Ez a szkript a tesztkörnyezet beállításán alapul, és az ügyfélnek várhatóan kapcsolatba kell lépnie az operációs rendszer gyártójával a további hangolás érdekében.
> Külön LUN-t kell kiépíteni az új és a meglévő kiszolgálók számára a memóriaképek és szkriptek mentéséhez, és gondoskodni fog arról, hogy a fájlrendszert a LUN-ban konfigurálja.
> A Microsoft nem felelős a memóriakép elemzéséhez. Az ügyfélnek meg kell nyitnia egy jegyet az operációs rendszer gyártójával, hogy elemezze.

- Futtassa ezt a parancsfájlt a HANA Large-példányon az alábbi parancs használatával

    > [!NOTE]
    > a parancs futtatásához sudo jogosultság szükséges.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Ha a parancs kimenete Kdump sikeresen engedélyezve van, ellenőrizze, hogy a rendszer újraindításával sikeresen alkalmazza-e a módosításokat.

- Ha a parancs kimenete nem tudott végrehajtani bizonyos műveletet, zárja be!!!!,, majd a Kdump szolgáltatás nincs engedélyezve. Tekintse át a [támogatási problémát](#support-issue)ismertető szakaszt.

## <a name="test-kdump"></a>Kdump tesztelése

> [!NOTE]
>  Az alábbi művelet elindítja a kernel összeomlását és a rendszer újraindítását.

- Kernel összeomlásának elindítása

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- A rendszer sikeres újraindítása után keresse `/var/crash` meg a kernel-összeomlási naplók mappáját.

- Ha a `/var/crash` címtára aktuális dátummal rendelkezik, akkor a Kdump engedélyezése sikeresen megtörtént.

## <a name="support-issue"></a>Támogatási probléma

Ha a parancsfájl hibát jelez, vagy a Kdump nincs engedélyezve, a következő részletekkel emelje fel a szolgáltatást a Microsoft támogatási csapatával.

* HLI előfizetés azonosítója

* Kiszolgálónév

* Operációs rendszer szállítója

* Operációs rendszer verziója

* Kernel verziója

## <a name="related-documents"></a>Kapcsolódó dokumentumok
- További tudnivalók a [kdump konfigurálásáról](https://www.suse.com/support/kb/doc/?id=3374462)
