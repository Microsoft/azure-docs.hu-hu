---
title: Az üzembe helyezés áttekintése – avere vFXT for Azure
description: Ebből az áttekintésből megtudhatja, hogyan helyezhet üzembe egy avere-vFXT az Azure-fürthöz. A kapcsolódó cikkek konkrét üzembe helyezési utasításokkal rendelkeznek.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271226"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT az Azure-ban – üzembe helyezés áttekintése

Ez a cikk áttekintést nyújt azokról a lépésekről, amelyekkel a avere-vFXT Azure-fürthöz való beszerzése folyamatban van.

A vFXT-fürt Azure piactéren való létrehozása előtt és után több feladat szükséges. A Kezdés és a Befejezés közötti folyamat egyértelművé teszi a szükséges erőfeszítés hatókörét.

## <a name="deployment-steps"></a>A központi telepítés lépései

[A System megtervezése](avere-vfxt-deploy-plan.md)után megkezdheti a avere vFXT-fürt létrehozását.

Az Azure Marketplace-en Azure Resource Manager-sablon gyűjti a szükséges információkat, és automatikusan telepíti a teljes fürtöt.

Ha a vFXT-fürt működik, a használat előtt még néhány konfigurációs lépést is végre kell hajtania. Ha létrehozott egy új blob Storage-tárolót, az adatait át kell helyeznie. Ha NAS-tárolót használ, a fürt létrehozása után fel kell vennie azt. Csatlakoznia kell az ügyfelekhez a fürthöz.

Az alábbiakban áttekintheti az összes lépést.

1. Előfeltételek konfigurálása

   A virtuális gép létrehozása előtt létre kell hoznia egy új előfizetést a avere vFXT projekthez, meg kell adnia az előfizetés tulajdonjogát, ellenőriznie kell a kvótákat, és szükség esetén meg kell adnia a növelést, és el kell fogadnia az avere vFXT szoftver használatára Részletes utasításokért olvassa el [a felkészülés a avere vFXT létrehozásához](avere-vfxt-prereqs.md) című témakört.

1. A avere vFXT-fürt létrehozása

   Az Azure Marketplace használatával hozza létre az Azure-fürthöz készült avere-vFXT. A sablon gyűjti a szükséges információkat, és parancsfájlokat hajt végre a végső termék létrehozásához.

   A fürt létrehozása az alábbi lépéseket foglalja magában, amelyeket a piactér sablonja végez:

   * Szükség esetén hozzon létre új hálózati infrastruktúrát és erőforráscsoportot
   * Fürt létrehozása

     A tartományvezérlő egy egyszerű virtuális gép, amely ugyanabban a virtuális hálózatban található, mint a avere vFXT-fürt, és rendelkezik a fürt létrehozásához és kezeléséhez szükséges egyéni szoftverekkel. A vezérlő létrehozza a vFXT-csomópontokat, és megalkotja a fürtöt, és egy parancssori felületet is biztosít a fürt felügyeletéhez az élettartama során.

     Ha az üzembe helyezés során új virtuális hálózatot vagy alhálózatot hoz létre, a vezérlő nyilvános IP-címmel fog rendelkezni. Ez azt jelenti, hogy a vezérlő Jump hostként szolgál, amely a virtuális hálózaton kívülről csatlakozik a fürthöz.

   * A fürtcsomópont virtuális gépek létrehozása

   * A fürt létrehozása az egyes csomópontokból

   * Szükség esetén létrehozhat egy új BLOB-tárolót, és konfigurálhatja háttérbeli tárolóként a fürt számára.

   A fürt létrehozásáról részletes leírást talál a [vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md)című témakörben.

1. A fürt konfigurálása

   Kapcsolódjon a avere vFXT konfigurációs felületéhez (avere Vezérlőpult) a fürt beállításainak testreszabásához. Ha hardveres tárhelyet vagy további blob-tárolókat használ, vegye igénybe a támogatás figyelését, és adja hozzá a tárolási rendszerét.

   * [Hozzáférés a vFXT-fürthöz](avere-vfxt-cluster-gui.md)
   * [Támogatás engedélyezése](avere-vfxt-enable-support.md)
   * [Tároló konfigurálása](avere-vfxt-add-storage.md) (ha szükséges)

1. Ügyfelek csatlakoztatása

   A [avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md) című témakör útmutatását követve megismerheti a terheléselosztást, valamint azt, hogy az ügyfélszámítógépek hogyan csatlakoztassa a fürtöt.

1. Adatbevitel (ha szükséges)

   Mivel a avere vFXT egy skálázható többügyféles gyorsítótár, a legjobb módszer az, hogy az új háttérbeli tárolóba helyezi át az adatátvitelt, és több ügyfelet tartalmazó, többszálas fájlmásolás-stratégiát is biztosít.

   Ha egy új blob-tárolóba vagy más háttérbeli tárolási rendszerbe kell helyeznie a munkakészletet, kövesse az [adatáthelyezés a vFXT-fürtbe](avere-vfxt-data-ingest.md)című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

Folytassa a [felkészülést a avere-vFXT létrehozásához](avere-vfxt-prereqs.md) az előfeltételként szükséges feladatok elvégzéséhez.
