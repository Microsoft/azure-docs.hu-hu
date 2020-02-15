---
title: Adatvisszajelzés biztosítása a Azure Maps számára | Microsoft Azure térképek
description: Adatvisszajelzés nyújtása Microsoft Azure Maps feedback eszköz használatával.
author: farah-alyasari
ms.author: v-faalya
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c4eb8932dda32ae6773e76dc7102f5afc6bc655
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209834"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Adatvisszajelzés biztosítása Azure Maps

A Azure Maps a 2018. május óta elérhető. Azure Maps a friss térképi adatok, a könnyen használható REST API-k és a nagy teljesítményű SDK-k lehetővé teszik, hogy a nagyvállalati ügyfelek különböző üzleti célú használati eseteket használjanak. A valós világ minden másodpercen át változik, és fontos számunkra, hogy tényszerű digitális képviseletet nyújtson ügyfeleinknek. A létesítmények megnyitását vagy bezárását tervező ügyfeleinknek azonnal frissíteniük kell a térképeket. Így hatékonyan tudják tervezni a szállítást, a karbantartást vagy az ügyfélszolgálatot a megfelelő létesítményekben. Létrehoztuk a Azure Maps adatvisszajelzési webhelyet, amely lehetővé teszi ügyfeleink számára, hogy közvetlen adatvisszajelzéseket nyújtsanak. Az ügyfelek adatvisszajelzése közvetlenül az adatszolgáltatókba és a Térkép-szerkesztőkbe kerül. Gyorsan kiértékelik és beépíthetik a leképezési termékeinkre vonatkozó visszajelzéseket.  

[Azure Maps adatvisszajelzési webhely](https://feedback.azuremaps.com) egyszerű módszert kínál ügyfeleinknek a térképes adatvisszajelzések megadására, különösen az üzleti pontokra és a lakossági címekre. Ez a cikk bemutatja, hogyan biztosíthat különböző visszajelzést a Azure Maps visszajelzési webhelyen.

## <a name="add-a-business-place-or-a-residential-address"></a>Üzleti hely vagy lakcím hozzáadása 

Előfordulhat, hogy visszajelzést szeretne küldeni egy hiányzó hasznos pontról vagy egy lakcímről. Ezt kétféleképpen teheti meg. Nyissa meg az Azure Térkép adatvisszajelzési webhelyét, keresse meg a hiányzó hely koordinátáit, majd kattintson a "hely hozzáadása" elemre.

  ![hiányzó hely keresése](./media/how-to-use-feedback-tool/search-poi.png)

Vagy használhatja a térképet is. Kattintson a helyre a PIN-kód eldobásához a koordinátákon, majd kattintson a "hely hozzáadása" gombra.

  ![PIN-kód hozzáadása](./media/how-to-use-feedback-tool/add-poi.png)

Ha a gombra kattint, a rendszer átirányítja egy űrlapra, hogy megadja a helyhez tartozó adatokat.

  ![hely hozzáadása](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Üzleti vagy lakossági címek javítása 

A visszajelzési webhely lehetővé teszi, hogy megkeresse és keresse meg a munkahelyi helyet vagy a címeket. Ha nem megfelelő, visszajelzést adhat a címnek vagy a PIN-kód helyének kijavításához. Ha visszajelzést szeretne küldeni a címnek a kijavításához, használja a keresősáv-t egy üzleti hely vagy egy lakcím megkereséséhez. A találatok listájában kattintson a kívánt helyre. Kattintson a hely kijavítása gombra.

  ![keresési hely a javításhoz](./media/how-to-use-feedback-tool/fix-place.png)

Ha visszajelzést szeretne küldeni a címe kijavításához, töltse ki a "hely kijavítása" űrlapot, és kattintson a "submit" (küldés) gombra.

  ![űrlap javítása](./media/how-to-use-feedback-tool/fix-form.png)

Ha a hely PIN-kódjának helye nem megfelelő, jelölje be a "hely kijavítása" űrlap jelölőnégyzetét, amely szerint a "PIN-kód helye helytelen". Helyezze át a PIN-kódot a megfelelő helyre, majd kattintson a Submit (küldés) gombra.

  ![PIN-kód helyének áthelyezése](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Megjegyzés hozzáadása 

A tartózkodási hely keresésének megkezdése mellett a visszajelzési eszköz is lehetővé teszi, hogy a helyhez kapcsolódó részletekhez adjon hozzá egy ingyenes szöveges megjegyzést. Megjegyzés hozzáadásához keresse meg a helyet, vagy kattintson a helyére. Kattintson a Megjegyzés hozzáadása elemre, írja be a megjegyzést, majd kattintson a "Küldés" gombra.

  ![Megjegyzés hozzáadása](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Állapot nyomon követése 

A kérés állapotát nyomon követheti is, ha az "állapot nyomon követése" négyzet bejelölésével bejelöli az e-mail-címet a kérelem végrehajtása közben. A rendszer egy nyomkövetési hivatkozást fog kapni az e-mailben, amely naprakész állapotot biztosít a kérelemnek. 

  ![visszajelzés állapota](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Következő lépések

Azure Maps kapcsolatos technikai kérdések közzétételéhez látogasson el ide:

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure Maps visszajelzési fórum](https://feedback.azure.com/forums/909172-azure-maps)
