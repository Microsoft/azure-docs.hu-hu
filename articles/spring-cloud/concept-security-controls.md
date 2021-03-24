---
title: Az Azure Spring Cloud Service biztonsági vezérlői
description: Az Azure Spring Cloud Service-be beépített biztonsági vezérlők használata.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 62a0bd19f6b10bbe6561f5587ed85d4d1e5880b3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878164"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Az Azure Spring Cloud Service biztonsági vezérlői

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

A biztonsági vezérlők az Azure Spring Cloud Service-be vannak építve.

A biztonsági ellenőrzés olyan Azure-szolgáltatás minősége vagy funkciója, amely hozzájárul a szolgáltatás a biztonsági rések megelőzésére, észlelésére és reagálására.  Az egyes vezérlők esetében az *Igen* vagy a *nem* értékkel kell jelezni, hogy a szolgáltatás jelenleg van-e érvényben.  Az *N/a-* t olyan vezérlőhöz használjuk, amely nem alkalmazható a szolgáltatásra. 

**Adatvédelmi biztonsági vezérlők**

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|:-------------|:-------|:-------------------------------|:----------------------|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A felhasználó által feltöltött forrás és összetevők, a konfigurációs kiszolgáló beállításai, az Alkalmazásbeállítások és az állandó tárterületen tárolt adatok tárolása az Azure Storage-ban történik, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban.<br><br>A konfigurációs kiszolgáló gyorsítótára, a feltöltött forrásból származó futtatókörnyezeti bináris fájlok, valamint az alkalmazás élettartama alatt az alkalmazás naplófájljai mentve lesznek az Azure Managed Disk szolgáltatásba, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban.<br><br>A felhasználó által feltöltött forrásból létrehozott tároló-lemezképeket a rendszer a Azure Container Registryba menti, ami automatikusan titkosítja a kép tartalmát a nyugalmi állapotban. | [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)<br><br>[Az Azure Managed Disks kiszolgálóoldali titkosítása](../virtual-machines/disk-encryption.md)<br><br>[Tároló képtárolója Azure Container Registry](../container-registry/container-registry-storage.md) |
| Titkosítás átmeneti állapotában | Igen | A felhasználói alkalmazás nyilvános végpontja alapértelmezés szerint HTTPS protokollt használ a bejövő forgalomhoz. |  |
| Titkosított API-hívások | Igen | Az Azure Spring Cloud Service-t konfiguráló felügyeleti hívások a HTTPS-en keresztül Azure Resource Manager hívásokon keresztül történnek. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Hálózati hozzáférés biztonsági vezérlői**

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|:-------------|:-------|:-------------------------------|:----------------------|
| Szolgáltatás címkéje | Igen | A **AzureSpringCloud** szolgáltatás címkével [hálózati biztonsági csoportokon](../virtual-network/network-security-groups-overview.md#security-rules) vagy [Azure Firewallon](../firewall/service-tags.md)adhat meg kimenő hálózati hozzáférés-vezérlést, hogy lehetővé váljon az Azure Spring Cloud-alkalmazások forgalmának engedélyezése.<br><br>*Megjegyzés:* Jelenleg csak a 2020/07/14 után létrehozott új Azure Spring Cloud Service-példány támogatja a **AzureSpringCloud** szolgáltatás címkéjét. | [Szolgáltatáscímkék](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md)