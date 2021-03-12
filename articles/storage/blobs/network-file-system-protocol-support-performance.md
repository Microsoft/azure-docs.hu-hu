---
title: NFS 3,0 teljesítménybeli megfontolások az Azure Blob Storage-ban (előzetes verzió) | Microsoft Docs
description: Optimalizálja a hálózati fájlrendszer (NFS) 3,0-es tárolási kérelmének teljesítményét a jelen cikkben ismertetett javaslatok alapján.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 3b596d14ea770bfcd7560903a234d2ab77b66201
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614326"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Hálózati fájlrendszer (NFS) 3,0 teljesítménybeli megfontolások az Azure Blob Storage-ban (előzetes verzió)

A blob Storage mostantól támogatja a Network File System (NFS) 3,0 protokollt. Ez a cikk olyan javaslatokat tartalmaz, amelyek segítenek a tárolási kérelmek teljesítményének optimalizálásában. Az NFS 3,0 Azure Blob Storage-támogatásával kapcsolatos további tudnivalókért lásd: a [hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage szolgáltatásban (előzetes verzió)](network-file-system-protocol-support.md).

> [!NOTE]
> Az NFS 3,0 protokoll támogatása az Azure Blob Storage-ban nyilvános előzetes verzióban érhető el. A standard szintű teljesítményű GPV2 a következő régiókban támogatja: Kelet-Ausztrália, Korea középső régiója és az USA déli középső régiója. Az előzetes verzióban az összes nyilvános régióban a prémium szintű teljesítménnyel rendelkező Block blob is támogatott.

## <a name="add-clients-to-increase-throughput"></a>Ügyfelek hozzáadása az átviteli sebesség növeléséhez 

Az Azure Blob Storage lineárisan méretezhető, amíg el nem éri a maximális tárolási fiók kimenő és bejövő korlátját. Ezért az alkalmazásai több ügyfél használatával is magasabb átviteli sebességet érhetnek el.  A tárolási fiókok kimenő és bejövő korlátainak megtekintéséhez lásd: [a szabványos Storage-fiókok méretezhetőségi és teljesítménybeli célpontjai](../common/scalability-targets-standard-account.md).

A következő diagram azt mutatja be, hogyan növekszik a sávszélesség a további ügyfelek hozzáadásakor. Ebben a diagramban az ügyfél egy virtuális gép (VM), és a fiók a standard szintű teljesítményszint használatával működik. 

> [!div class="mx-imgBorder"]
> ![Standard teljesítmény](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

A következő diagram ugyanezt a hatást mutatja be, ha a prémium szintű teljesítményt használó fiókra van alkalmazva.

> [!div class="mx-imgBorder"]
> ![Prémium szintű teljesítmény](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Prémium szintű teljesítményszint használata kis léptékű alkalmazásokhoz

Nem minden alkalmazás képes felskálázásra további ügyfelek hozzáadásával. Ezekhez az alkalmazásokhoz az [Azure Premium Block blob Storage-fiók](storage-blob-create-account-block-blob.md) konzisztens, kis késésű és magas tranzakciós díjszabást biztosít. A prémium blokk blob Storage-fiók kevesebb szálat és ügyfelet képes elérni a maximális sávszélesség eléréséhez. Egyetlen ügyféllel például a prémium szintű blokk blob Storage-fiók elérheti a **2.3 x** sávszélességet, mint a standard teljesítményű általános célú v2 Storage-fiókkal. 

A következő diagram minden sávja a prémium és a standard teljesítményű Storage-fiókok közötti, elért sávszélesség különbségét mutatja. Ahogy az ügyfelek száma nő, ez a különbség csökken.  

> [!div class="mx-imgBorder"]
> ![Relatív teljesítmény](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-date"></a>A gyakori felülírások elkerülésének dátuma

Hosszabb időt vesz igénybe, mint egy új írási művelet végrehajtása. Ennek oka az, hogy egy NFS-felülírási művelet, különösen a részleges helyi fájl szerkesztése, több mögöttes blob-művelet kombinációja: olvasási, módosítási és írási művelet. Ezért az olyan alkalmazások esetében, amelyekben gyakoriak a helyben végzett módosítások, nem alkalmasak az NFS-kompatibilis blob Storage-fiókokhoz. 

## <a name="other-best-practice-recommendations"></a>Egyéb ajánlott eljárások 

- Használjon megfelelő hálózati sávszélességű virtuális gépeket.

- Több csatlakoztatási pontot is használhat a munkaterhelések engedélyezésekor.

- A lehető legtöbb szálat használhatja.

- Nagyméretű blokkos méretek használata.

- Tárolási kérelmek készítése egy olyan ügyfélről, amely ugyanabban a régióban található, mint a Storage-fiók. Ez növelheti a hálózati késést.

## <a name="next-steps"></a>Következő lépések

- Az NFS 3,0 Azure Blob Storage-támogatásával kapcsolatos további tudnivalókért lásd: a [hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage szolgáltatásban (előzetes verzió)](network-file-system-protocol-support.md).

- Első lépésként tekintse meg [a blob Storage csatlakoztatása a hálózati fájlrendszer (NFS) 3,0 protokoll (előzetes verzió) használatával](network-file-system-protocol-support-how-to.md)című témakört.
