---
title: Az Azure tárolók tartalmazó ajánlat |} Az Azure Marketplace-en
description: Egy tároló-ajánlat az Azure piactér közzétételi folyamatának áttekintése.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pabutler
ms.openlocfilehash: 9617380822f5814d1cbd2bd2b69eac72722fe813
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942754"
---
# <a name="containers"></a>Containers

<table> <tr> <td>Ez a szakasz ismerteti, hogyan tehet közzé egy tárolórendszerképet a <a href="https://azuremarketplace.microsoft.com">Azure Marketplace-en</a>.  
A tároló ajánlat típusát támogatja a Docker-tárolórendszerképekhez helyezhető üzembe, <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> példányok vagy <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> lévő üzemeltetett és a egy <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registrybe </a> tárház. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Az ajánlat összetevők

Ebben a szakaszban egy tároló közzétételi elemeinek ismerteti, és célja, a közzétevő eligazodhat az Azure piactéren. Közzététel a következő fő részeket osztva:

- [Előfeltételek](./cpp-prerequisites.md) -létrehozása vagy egy tároló-ajánlat közzététele előtt technikai és üzleti követelményeit.
- [Az ajánlat létrehozásához](./cpp-create-offer.md) – hozzon létre egy új tárolót ajánlat használatával a Cloud Partner portálra szükséges lépéseket tartalmazza.
- [A technikai eszközök előkészítése](./cpp-create-technical-assets.md) – egy ajánlatot az Azure Marketplace-en, a tároló megoldás technikai eszközök létrehozása.
- [Az ajánlat közzététele](./cpp-publish-offer.md) – hogyan lehet elküldeni az ajánlat az Azure piactéren való közzétételre.

## <a name="container-publishing-process"></a>Tároló közzétételi folyamata

A következő ábra szemlélteti a magas szintű lépéseket egy Virtuálisgép-ajánlat közzététele.
![Az ajánlat közzétételének lépései](./media/containers-offer-process.png)

Egy tároló-ajánlat közzétételéhez a magas szintű lépései a következők:

1. Az ajánlat létrehozásához – az ajánlat részletes információkat tartalmaznak. Ezen információk közé tartozik: az ajánlat leírása, marketing-adategység-specifikációi, anyagokat és támogatási információk.
2. Hozzon létre az üzleti és technikai eszközök – hozzon létre az üzleti eszközök (jogi dokumentumok és marketinganyagokat) és a kapcsolódó megoldás technikai eszközök (az Azure Container Registry a tárolók képek üzemeltetett.
3. A Termékváltozat létrehozása – a termékváltozat(ok) társított az ajánlat létrehozása. Egy egyedi Termékváltozat megadása kötelező az egyes lemezképek szeretné közzétenni.
4. Igazolom, és tegye közzé az ajánlat – az ajánlat és a technikai eszközök befejezése után az ajánlatot küldhet. Az elküldés során először a közzétételi folyamat. Ez a folyamat során a megoldás vizsgálják, érvényesíti, minősített, majd "élesíti" az Azure Marketplace-en.

## <a name="next-steps"></a>További lépések

Mielőtt ezeket a lépéseket, meg kell felelnie a [műszaki és üzleti igényeinek](./cpp-prerequisites.md) egy tárolót a Microsoft Azure piactéren való közzétételéhez.