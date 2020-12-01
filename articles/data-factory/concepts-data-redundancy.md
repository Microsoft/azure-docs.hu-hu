---
title: Adatredundancia a Azure Data Factoryban | Microsoft Docs
description: Tudnivalók a meta-adatredundancia mechanizmusairól Azure Data Factory
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 7f95adc264ed91e75eef668b43f674ddeb7d9e89
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350024"
---
# <a name="azure-data-factory-data-redundancy"></a>**Adatredundancia Azure Data Factory**

Azure Data Factory adatok közé tartoznak a metaadatok (a folyamat, az adatkészletek, a társított szolgáltatások, az integrációs modul és az eseményindítók) és a figyelési adatok (folyamat, eseményindító és tevékenység-futtatások). 

Az összes régióban (kivéve Dél-és Délkelet-Ázsiában) a rendszer a [párosított régióban](../best-practices-availability-paired-regions.md#azure-regional-pairs) tárolja és replikálja az adatokat Azure Data Factory, hogy megvédje a metaadatok elvesztését. Regionális adatközpontok meghibásodása esetén a Microsoft kezdeményezheti Azure Data Factory példányának regionális feladatátvételét. A legtöbb esetben nincs szükség beavatkozásra az Ön részéről. A Microsoft által felügyelt feladatátvétel befejeződése után hozzáférhet a Azure Data Factory a feladatátvételi régióban. 

A Dél-Brazíliában és Délkelet-Ázsiában található adattárolási követelmények miatt Azure Data Factory az adat [csak a helyi régióban](../storage/common/storage-redundancy.md#locally-redundant-storage)tárolódik. Délkelet-Ázsiában a rendszer az összes adattárolást Szingapúrban tárolja. Dél-Brazília esetében az összes adattal Brazíliában van tárolva. Ha egy jelentős katasztrófa miatt a régió elvész, a Microsoft nem fogja tudni helyreállítani Azure Data Factory adatait.  

> [!NOTE]
> A Microsoft által felügyelt feladatátvétel nem vonatkozik a saját üzemeltetésű integrációs modulra (a-ra), mivel ez az infrastruktúra általában az ügyfél által felügyelt. Ha a rendszer az Azure-beli virtuális gépen állítja be a (z)-t, akkor a javaslat az Azure [site Recovery](../site-recovery/site-recovery-overview.md) kihasználása az Azure-beli [virtuális gép feladatátvételének](../site-recovery/azure-to-azure-architecture.md) másik régióba való kezelésére.



## <a name="using-source-control-in-azure-data-factory"></a>**A verziókövetés használata a Azure Data Factoryban**

Annak érdekében, hogy nyomon követhesse és naplózza az Azure-beli adat-előállító metaadatainak módosításait, érdemes megfontolnia a Azure Data Factoryhoz tartozó verziókövetés beállítását. Emellett lehetővé teszi a metaadatok JSON-fájljainak elérését a folyamatok, adatkészletek, társított szolgáltatások és triggerek számára. Azure Data Factory lehetővé teszi a különböző git-tárház (Azure DevOps és GitHub) használatát. 

 Megtudhatja, hogyan állíthatja be a [verziókövetés Azure Data Factoryban](./source-control.md). 

> [!NOTE]
> Katasztrófa esetén (a régió elvesztése esetén) az új adatelőállítót manuálisan vagy automatizált módon lehet kiépíteni. Az új adat-előállító létrehozása után visszaállíthatja a folyamatokat, az adatkészleteket és a társított szolgáltatások JSON-t a meglévő git-tárházból. 



## <a name="data-stores"></a>**Adattárolók**

A Azure Data Factory lehetővé teszi az adatáthelyezést a helyszínen és a felhőben található adattárak között. Az adattárakkal folytatott üzletmenet folytonosságának biztosítása érdekében tekintse át az egyes adattárakhoz tartozó üzletmenet-folytonossági javaslatokat. 

 

## <a name="see-also"></a>Lásd még

- [Azure regionális párok](../best-practices-availability-paired-regions.md)
- [Adattárolás az Azure-ban](https://azure.microsoft.com/global-infrastructure/data-residency/)