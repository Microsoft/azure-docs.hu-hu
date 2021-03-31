---
title: Verziók kezelése – LUIS
titleSuffix: Azure Cognitive Services
description: A verziók segítségével különböző modelleket hozhat létre és tehet közzé. A modell módosítása előtt célszerű a jelenlegi aktív modell klónozását az alkalmazás egy másik verziójára.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b93e9fd4047ee3731ef6fe4652bb92ce5145987d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018820"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Verziók használata az átmeneti vagy éles alkalmazások befolyásolása nélkül

A verziók segítségével különböző modelleket hozhat létre és tehet közzé. A modell módosítása előtt célszerű a jelenlegi aktív modell klónozását az alkalmazás egy másik [verziójára](./luis-concept-app-iteration.md) .

Az aktív verzió az a verzió, amelyet a LUIS Portal **Build** szakasza tartalmaz, amely céljait, entitásait, funkcióit és mintáit tartalmazza. A szerzői API-k használatakor nem kell beállítania az aktív verziót, mert a verzióra vonatkozó REST API hívások tartalmazzák az útvonalon található verziót.

A verziókhoz való munkavégzéshez nyissa meg az alkalmazást a saját **alkalmazások** lapján, majd válassza a **kezelés** lehetőséget a felső sávon, majd válassza a **verziók** elemet a bal oldali navigációs felületen.

A verziók listája megjeleníti a közzétett verziókat, a közzétételük helyét, valamint a jelenleg aktív verziót.

## <a name="clone-a-version"></a>Verzió klónozása

1. Válassza ki a klónozott verziót, majd válassza a **klónozás** lehetőséget az eszköztáron.

2. A **klón verziója** párbeszédpanelen írja be az új verzió nevét, például: "0,2".

   ![Verzió klónozása párbeszédpanel](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > A verzióazonosító csak karakterből, számjegyből vagy "." állhat, és nem lehet hosszabb 10 karakternél.

   A rendszer létrehoz egy új, a megadott névvel ellátott verziót, és beállítja az aktív verziót.

## <a name="set-active-version"></a>Aktív verzió beállítása

Válasszon egy verziót a listából, majd kattintson az **aktiválás** elemre az eszköztáron.

## <a name="import-version"></a>Importálási verzió

Importálhatja `.json` az alkalmazás egy vagy `.lu` több verzióját is.

1. Válassza az **Importálás** lehetőséget az eszköztárból, majd válassza ki a formátumot.

2. Az **új verzió importálása** előugró ablakban adja meg az új tíz karakteres verzió nevét. Csak akkor kell megadnia a verziószámot, ha a fájl verziója már létezik az alkalmazásban.

    ![Szakasz, verziók lap, új verzió importálása](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    A verzió importálása után az új verzió lesz az aktív verzió.

### <a name="import-errors"></a>Importálási hibák

* Tokenizer hibák: Ha az importálás során **Tokenizer hibaüzenetet** kap, egy olyan verziót próbál importálni, amely a jelenleg használt alkalmazástól eltérő [Tokenizer](luis-language-support.md#custom-tokenizer-versions) használ. A probléma megoldásához tekintse meg az [áttelepítés a tokenizer-verziók között](luis-language-support.md#migrating-between-tokenizer-versions)című témakört.

<a name = "export-version"></a>

## <a name="other-actions"></a>Egyéb műveletek

* Ha **törölni** szeretne egy verziót, válasszon ki egy verziót a listából, majd válassza a **Törlés** lehetőséget az eszköztárból. Kattintson az **OK** gombra.
* Egy verzió **átnevezéséhez** válasszon egy verziót a listából, majd válassza az **Átnevezés** lehetőséget az eszköztárból. Adja meg az új nevet, és válassza a **kész** lehetőséget.
* Egy verzió **exportálásához** válasszon ki egy verziót a listából, majd válassza az **alkalmazás exportálása** lehetőséget az eszköztárból. Válassza a JSON vagy a LU lehetőséget, ha biztonsági mentést szeretne exportálni, vagy a forrás vezérlőelemben szeretné menteni a fájlt, majd válassza az **Exportálás tárolóba** lehetőséget az [alkalmazás egy Luis-tárolóban való használatához](luis-container-howto.md)