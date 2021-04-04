---
title: Azure Application konzisztens pillanatkép-eszköz tesztelése Azure NetApp Fileshoz | Microsoft Docs
description: A cikk azt ismerteti, hogyan futtathatja az Azure Application konzisztens pillanatkép-eszköz tesztelési parancsát, amelyet a Azure NetApp Files használatával használhat.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632679"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Azure Application konzisztens pillanatkép-eszköz tesztelése (előzetes verzió)

Ez a cikk azt ismerteti, hogyan futtathatja az Azure Application konzisztens pillanatkép-eszköz tesztelési parancsát, amelyet a Azure NetApp Files használatával használhat.

## <a name="introduction"></a>Bevezetés

A konfiguráció tesztelését a parancs használatával végezheti el `azacsnap -c test` .

## <a name="command-options"></a>Parancs beállításai

A `-c test` parancs a következő beállításokkal rendelkezik:

- `--test hana`  a SAP HANA-példányhoz való kapcsolódás tesztelése.

- `--test storage` az alapul szolgáló tárolási felülettel folytatott kommunikációt úgy ellenőrzi, hogy létrehoz egy ideiglenes tárolási pillanatképet az összes konfigurált `data` köteten, majd eltávolítja őket. 

- `--test all` a és a teszteket is végrehajtja egymás `hana` `storage` után.

- `[--configfile <config filename>]` a (z) egy opcionális paraméter, amely lehetővé teszi az egyéni konfigurációs fájlnevek nevét.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Kapcsolat ellenőrzése SAP HANA `azacsnap -c test --test hana`

Ez a parancs ellenőrzi a HANA-kapcsolatot a konfigurációs fájlban található HANA-példányok esetében. A HDBuserstore használatával csatlakozik a SYSTEMDB, és beolvassa a SID-információkat.

Az SSL esetében ez a parancs a következő opcionális argumentumot veheti igénybe:

- `--ssl=` titkosított kapcsolatot kényszerít az adatbázissal, és meghatározza a SAP HANAsal való kommunikációhoz használt titkosítási módszert, `openssl` vagy vagy `commoncrypto` . Ha meg van adva, akkor a parancs két fájlt vár ugyanabban a címtárban, ezeket a fájlokat a megfelelő SID után kell elnevezni. Tekintse meg az [SSL használatát a SAP HANAsal való kommunikációhoz](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>A parancs kimenete `azacsnap -c test --test hana`

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Kapcsolat ellenőrzése a Storage szolgáltatással `azacsnap -c test --test storage`

A `azacsnap` parancs pillanatképet készít az összes konfigurált adatkötetről annak ellenőrzéséhez, hogy a megfelelő hozzáféréssel rendelkezik-e az egyes SAP HANA-példányok köteteihez. A rendszer létrehoz egy ideiglenes pillanatképet, majd eltávolítja az egyes adatmennyiségeket az egyes fájlrendszerekhez tartozó pillanatkép-hozzáférés ellenőrzéséhez.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>A parancs kimenete `azacsnap -c test --test storage`

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Az Azure-beli nagyméretű példány esetében `azacsnap -c test --test storage` a parancs kikövetkezteti a tárolási és a HLI SKU-t.  Ezen információk alapján útmutatást nyújt a rendszerindítási Pillanatképek konfigurálásához (lásd a `Action:` kimenettel kezdődő sort).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Következő lépések

- [Pillanatkép biztonsági mentése az Azure Application konzisztens pillanatkép-eszközzel](azacsnap-cmd-ref-backup.md)
