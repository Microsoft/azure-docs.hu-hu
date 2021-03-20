---
title: Azure CDN gyorsítótárazási viselkedés szabályozása lekérdezési karakterláncokkal – prémium szint
description: Azure CDN lekérdezési karakterlánc gyorsítótárazása szabályozza, hogy a rendszer hogyan gyorsítótárazza a fájlokat, amikor egy webes kérelem lekérdezési karakterláncot tartalmaz. Ez a cikk a Verizon termék Azure CDN Premium kiadásában a lekérdezési karakterláncok gyorsítótárazását ismerteti.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a799309b6e5d00db3b6c206187eec7097c9dc11a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018596"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Azure CDN gyorsítótárazási viselkedés szabályozása lekérdezési karakterláncokkal – prémium szint
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Prémium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN) segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazza a fájlokat egy olyan webes kérelem esetében, amely egy lekérdezési karakterláncot tartalmaz. Lekérdezési karakterláncot tartalmazó webes kérelem esetén a lekérdezési karakterlánc a kérelemnek a kérdőjel (?) utáni részét jelöli. A lekérdezési karakterláncok tartalmazhatnak egy vagy több kulcs-érték párokat, amelyekben a mező nevét és értékét egy egyenlőségjel (=) választja el egymástól. A kulcs-érték párokat egy jel (&) választja el egymástól. Például: http: \/ /www.contoso.com/content.mov?field1=value1&mező = érték2. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár szerepel, a rendelésük nem számít. 

> [!IMPORTANT]
> A standard és a prémium CDN-termékek ugyanazt a lekérdezési karakterlánc-gyorsítótárazási funkciót biztosítják, a felhasználói felület azonban eltérő. Ez a cikk a **Verizon Azure CDN Premium** csomag felületét ismerteti. A Azure CDN standard termékekkel történő lekérdezési karakterláncok gyorsítótárazásával kapcsolatban lásd: [Azure CDN gyorsítótárazási viselkedés szabályozása a lekérdezési karakterláncokkal – standard szint](cdn-query-string.md).
>


Három lekérdezési karakterlánc mód érhető el:

- **standard – gyorsítótár**: alapértelmezett mód. Ebben a módban a CDN-pont – jelenlét (POP) csomópont továbbítja a lekérdezési karakterláncokat a kérelmezőtől az első kérelemben szereplő forrás-kiszolgálónak, és gyorsítótárazza az eszközt. A POP-kiszolgálóról kiszolgált összes további kérelem figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz le nem jár.

    >[!IMPORTANT] 
    > Ha a jogkivonat-hitelesítés engedélyezve van a fiók bármely elérési útjához, a normál gyorsítótárazási mód az egyetlen használható mód. 

- **no-cache**: ebben a módban a lekérdezési karakterláncokat tartalmazó kérelmeket a rendszer nem gyorsítótárazza a CDN pop-csomóponton. A POP-csomópont közvetlenül a forrás-kiszolgálóról kérdezi le az adategységet, és minden kéréssel átadja azt a kérelmezőnek.

- **egyedi gyorsítótár**: ebben a módban minden, egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot is, egyedi objektumként kezeli a saját gyorsítótárával. Például a forráskiszolgáló válasza egy kérelemhez (például. ashx? q = test1) gyorsítótárazva van a POP-csomóponton, és a későbbi gyorsítótárak esetében ugyanezt a lekérdezési karakterlánccal adja vissza. A (például. ashx? q = teszt2) kérelem egy különálló eszközként van gyorsítótárazva, amely a saját élettartama beállítással van ellátva.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc olyan paramétereket tartalmaz, amelyek minden kérelemre (például egy munkamenet-AZONOSÍTÓra vagy egy felhasználónévre) változnak, mert az alacsony gyorsítótár-találati arányt eredményez.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>A prémium szintű CDN-profilok lekérdezési karakterláncának gyorsítótárazási beállításainak módosítása
1. Nyisson meg egy CDN-profilt, majd kattintson a **kezelés** elemre.
   
    ![CDN-profil kezelése gomb](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Vigye a kurzort a **nagyméretű http** -lapra, majd vigye a kurzort a **gyorsítótár beállításai** menü fölé. Kattintson a **lekérdezés-karakterlánc gyorsítótárazása** elemre.
   
    Megjelenik a lekérdezési karakterlánc gyorsítótárazási beállításai.
   
    ![CDN lekérdezési karakterlánc gyorsítótárazási beállításai](./media/cdn-query-string-premium/cdn-query-string.png)
3. Válasszon egy lekérdezési karakterlánc módot, majd kattintson a **frissítés** gombra.

> [!IMPORTANT]
> Mivel időt vesz igénybe, hogy a regisztráció a CDN-en keresztül legyen propagálva, előfordulhat, hogy a gyorsítótár-karakterlánc beállításainak módosításai nem azonnal láthatók. A propagálás általában 10 percen belül befejeződik.
 

