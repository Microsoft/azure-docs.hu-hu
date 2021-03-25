---
title: 'Az Azure Application konzisztens pillanatkép-eszköz kibocsátási megjegyzései a következőhöz: Azure NetApp Files | Microsoft Docs'
description: Kibocsátási megjegyzéseket nyújt az Azure Application konzisztens pillanatkép-eszközhöz, amelyet a Azure NetApp Files használatával használhat.
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
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111432"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Kibocsátási megjegyzések az Azure Application konzisztens pillanatkép-eszközhöz (előzetes verzió)

Ezen a lapon megtekintheti az új funkciók megadásához és a hibák elhárításához a AzAcSnap végzett főbb módosításokat.

## <a name="march-2021"></a>Március-2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 előzetes verzió (Build: 20210318.30771)

A AzAcSnap v 5.0 előzetes verziója (Build: 20210318.30771) a következő javításokkal és javításokkal lett közzétéve:

- A AZACSNAP-felhasználó a SAP HANA bérlői adatbázisokba való felvételének szükségességét a következő témakörben találja: [kommunikáció engedélyezése a SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana) szakasszal.
- Javítás a manuális QOS-vel konfigurált kötetekkel való [visszaállítás](azacsnap-cmd-ref-restore.md) engedélyezéséhez.
- Mutex-vezérlő hozzáadva a nagyméretű Azure-példányok SSH-kapcsolatainak szabályozásához.
- Javítsa a telepítőt az elérési utak neveinek a szóközökkel és az egyéb kapcsolódó problémákkal való kezeléséhez.
- A más adatbázis-kiszolgálók támogatásának előkészítésekor a "--hanasid" választható paramétert a "--dbsid" értékre változtatta.

Töltse le a telepítő [legújabb kiadását](https://aka.ms/azacsnapdownload) , és tekintse át az [első lépéseket](azacsnap-get-started.md).

## <a name="next-steps"></a>Következő lépések

- [Ismerkedés az Azure Application konzisztens pillanatkép-eszközzel](azacsnap-get-started.md)
