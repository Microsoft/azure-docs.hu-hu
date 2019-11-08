---
title: Az Azure IoT Central felhasználói adatkérési funkciói | Microsoft Docs
description: Ez a cikk az ügyféladatok azonosítását, törlését és exportálását ismerteti az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 27cd2e58eabcd96414391cac1a517ea242a65c21
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809813"
---
# <a name="summary-of-customer-data-request-features"></a>A vásárlói adatkérések funkcióinak összefoglalása

Az Azure IoT Central egy teljes körűen felügyelt, eszközök internetes hálózata (IoT) szoftveres megoldás, amely megkönnyíti a IoT-eszközök összekapcsolását, figyelését és felügyeletét nagy méretekben, mélyreható elemzéseket készíthet a IoT-adatokból, és tájékozott műveleteket végezhet.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Ügyféladatok azonosítása

Azure Active Directory Object-ID azonosítókat használ a felhasználók azonosítására és a szerepkörök hozzárendelésére. Az Azure IoT Central portál a szerepkör-hozzárendelések felhasználói e-mail-címeit jeleníti meg, de csak az Azure Active Directory Object-ID tárolja, az e-mail-címet a rendszer dinamikusan kérdezi le a Azure Active Directory. Az Azure IoT Central-rendszergazdák megtekinthetik, exportálják és törölhetik az alkalmazás felhasználóit egy Azure IoT Central-alkalmazás felhasználói adminisztráció szakaszában.

Az alkalmazáson belül az e-mail-címek is konfigurálhatók a riasztások fogadására. Ebben az esetben az e-mail-címeket IoT Central tárolja, és az alkalmazáson belüli fiók felügyelete lapon kell felügyelni.

Az eszközökre vonatkozóan a Microsoft nem tart fenn információt, és nem fér hozzá az eszköz felhasználói korrelációját lehetővé tevő adatokhoz. Az Azure IoT Centralban felügyelt eszközök közül sok nem személyes eszköz, például egy automatából vagy kávéfőzőn. Előfordulhat azonban, hogy egyes eszközök személyes azonosításra alkalmasak, és a saját belátásuk szerint megőrizheti saját eszközeiket vagy leltározási nyomon követési rendszereit, amelyek az eszközöket az egyéni felhasználókhoz köti. Az Azure IoT Central kezeli és tárolja az eszközökhöz kapcsolódó összes olyan adatmennyiséget, amely személyes adatként volt.

A Microsoft nagyvállalati szolgáltatásainak használatakor a Microsoft bizonyos adatokat hoz létre a rendszer által létrehozott naplók néven. Ezek a naplók a szolgáltatáson belüli, valamint az egyes eszközökhöz kapcsolódó diagnosztikai és a felhasználói tevékenységekhez nem kapcsolódó, ténybeli műveleteket alkotnak. Az Azure IoT Central rendszer által generált naplók nem érhetők el és nem exportálhatók az alkalmazás-rendszergazdák számára.

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A felhasználói adattörlési képesség csak a IoT Central adminisztrációs oldalon keresztül érhető el. Az alkalmazás-rendszergazdák kiválaszthatják a törölni kívánt felhasználót, és az alkalmazás jobb felső sarkában található **Törlés** gombra kattintva törölhetik a rekordot. Az alkalmazás-rendszergazdák eltávolíthatják azokat az egyes fiókokat is, amelyek már nincsenek társítva a szóban forgó alkalmazáshoz.

A felhasználó törlése után a rendszer nem küld további riasztásokat. Az e-mail-címüket azonban egyenként el kell távolítani az egyes konfigurált riasztásokból.

További információ: [szabályok és műveletek konfigurálása az eszközhöz](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Ügyféladatok exportálása

Az adatexportálási képesség csak a IoT Central adminisztrációs oldalon keresztül érhető el. Az ügyféladatokat, a hozzárendelt szerepköröket is beleértve, az alkalmazás rendszergazdája kiválaszthatja, másolhatja és beillesztheti.

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

További információ a fiókok felügyeletéről, beleértve a szerepkör-definíciókat: [az alkalmazás felügyelete](howto-administer.md).
