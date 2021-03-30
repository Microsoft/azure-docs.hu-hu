---
title: 'Azure AD Connect Sync: technikai fogalmak | Microsoft Docs'
description: Ismerteti Azure AD Connect Sync technikai fogalmait.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01e53b30a4c27296e30e031ffb771697afa8e1e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87019675"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Az Azure AD Connect szinkronizálása: technikai kulcsfogalmak
Ez a cikk összefoglalja az [architektúra megismerését ismertető](how-to-connect-sync-technical-concepts.md)témakört.

Azure AD Connect a szinkronizálás egy stabil Metadirectory szinkronizációs platformra épít.
A következő fejezetekben a Metadirectory-szinkronizálással kapcsolatos fogalmakat mutatjuk be.
A MIIS (Microsoft Identity Integration Server), az ILM (Identity Lifecycle Manager) és a FIM (Forefront Identity Manager) használatával a Azure Active Directory Sync Services a következő platformot biztosítja az adatforrásokhoz való csatlakozáshoz, az adatok adatforrások közötti szinkronizálásához, valamint az identitások kiépítéséhez és megszüntetéséhez.

![Műszaki fogalmak](./media/how-to-connect-sync-technical-concepts/scenario.png)

A következő szakaszokban további részleteket talál a FIM szinkronizációs szolgáltatás következő aspektusairól:

* Összekötő
* Attribútumfolyam
* Összekötő területe
* Metaverse
* Kiépítés

## <a name="connector"></a>Összekötő
A csatlakoztatott címtárral folytatott kommunikációhoz használt kód-modulokat összekötőknek (korábbi nevén felügyeleti ügynököknek (MAs) nevezik) nevezzük.

Ezek a Azure AD Connect Sync alkalmazást futtató számítógépre vannak telepítve. Az összekötők az ügynök nélküli képességet biztosítják a távoli rendszerprotokollok használatával, ahelyett, hogy a speciális ügynökök üzembe helyezésére kellene támaszkodni. Ez csökkenti a kockázat-és üzembe helyezési időpontokat, különösen a kritikus fontosságú alkalmazások és rendszerek kezelésekor.

A fenti képen az összekötő az összekötő terület szinonimája, de a külső rendszerrel folytatott kommunikációt is magában foglalja.

Az összekötő felelős az összes importálási és exportálási funkcióért a rendszeren, és felszabadítja a fejlesztőket abban, hogy megértse, hogyan lehet natív módon csatlakozni az egyes rendszerekhez, amikor a deklaratív kiépítés használatával testreszabja az adatok átalakítását.

Az Importálások és az exportálások csak az ütemezés szerint történnek, ami lehetővé teszi a rendszeren belüli változások további szigetelését, mivel a módosítások nem továbbítódnak automatikusan a csatlakoztatott adatforrásra. Emellett a fejlesztők létrehozhatnak saját összekötőket is, amelyek gyakorlatilag bármilyen adatforráshoz csatlakozhatnak.

## <a name="attribute-flow"></a>Attribútumfolyam
A metaverse a szomszédos összekötők területeiről származó összes identitás összevont nézete. A fenti ábrán látható, hogy az attribútum folyamata a bejövő és a kimenő folyamathoz tartozó nyílhegyekkel rendelkező sorokban van ábrázolva. Az attribútum folyamata az adatok egyik rendszerről a másikra történő másolása vagy átalakítása, valamint az összes attribútum (bejövő vagy kimenő).

Az attribútum folyamata az összekötő területe és a metaverse bi iránya között történik, ha a szinkronizálási (teljes vagy Delta) műveletek futtatása ütemezve van.

Az attribútum folyamata csak akkor történik meg, ha ezek a szinkronizálások futnak. Az attribútumok folyamatait a szinkronizálási szabályok határozzák meg. Ezek lehetnek bejövőek (a fenti képen ISR) vagy kimenő (OSR a fenti képen).

## <a name="connected-system"></a>Csatlakoztatott rendszerek
A csatlakoztatott rendszer (más néven csatlakoztatott könyvtár) a távoli rendszerre hivatkozik, Azure AD Connect a szinkronizálás kapcsolódott a-hoz és a-ból származó személyazonossági adatok olvasásához és olvasásához.

## <a name="connector-space"></a>Összekötő területe
Az egyes csatlakoztatott adatforrások az objektumok és attribútumok szűrt részhalmaza jelennek meg az összekötő területén.
Ez lehetővé teszi, hogy a szinkronizálási szolgáltatás helyileg működjön anélkül, hogy az objektumok szinkronizálásakor fel kellene vennie a kapcsolatot a távoli rendszerrel, és csak a csak importálásra és exportálásra korlátozza az interakciót.

Ha az adatforrás és az összekötő képes a változások listájának megadására (különbözeti importálás), akkor a működési hatékonyság jelentősen megnő, mivel a legutóbbi lekérdezési ciklusok cseréje óta csak változás történik. Az összekötő területe elszigeteli a csatlakoztatott adatforrást az automatikusan propagált módosításoktól, ha megköveteli, hogy az összekötő ütemezze az importálást és az exportálást. Ez a kiegészítő biztosítás a következő frissítés tesztelésekor, előzetes megtekintésekor vagy megerősítésekor is megerősítheti a biztonságot.

## <a name="metaverse"></a>Metaverse
A metaverse a szomszédos összekötők területeiről származó összes identitás összevont nézete.

Mivel az identitások össze vannak kapcsolva, és a szolgáltató a különböző attribútumok számára van hozzárendelve importálási folyamat-hozzárendeléseken keresztül, a központi metaverse objektum több rendszer információinak összesítését kezdi. Ebből az objektum-attribútumból a leképezések a kimenő rendszerekre vonatkozó adatokat hordozzák.

Az objektumok akkor jönnek létre, amikor egy mérvadó rendszer megtervezi őket a metaverse-ba. Amint az összes kapcsolat el lett távolítva, a metaverse objektum törlődik.

A metaverse objektumait nem lehet közvetlenül szerkeszteni. Az objektumban lévő összes adattal az attribútum folyamatán keresztül kell hozzájárulni. A metaverse állandó összekötőket tart fenn az egyes összekötők területével. Ezekhez az összekötőhöz nem szükséges újraértékelés az egyes szinkronizálási futtatásokhoz. Ez azt jelenti, hogy Azure AD Connect szinkronizálásnak nem kell minden alkalommal megkeresnie a megfelelő távoli objektumot. Ezzel elkerülhető, hogy a költséges ügynökök ne tudják megakadályozni az olyan attribútumok módosításait, amelyek általában felelősek az objektumok korrelációjában.

Az olyan új adatforrások felderítése során, amelyeknek esetlegesen már meglévő objektumokat kell kezelniük, Azure AD Connect szinkronizálás egy JOIN szabály nevű folyamatot használ a lehetséges pályázók kiértékeléséhez, amelyekhez hivatkozást kell létrehozni.
A hivatkozás létrehozása után ez a kiértékelés nem történik meg, és a normál adatforgalom a távoli csatlakoztatott adatforrás és a metaverse között fordulhat elő.

## <a name="provisioning"></a>Kiépítés
Ha egy mérvadó forrás egy új objektumot hoz létre a metaverse-ba, egy új összekötő terület objektum hozható létre egy másik összekötőn, amely egy alsóbb rétegbeli csatlakoztatott adatforrást jelöl.

Ez eleve létrehozza a hivatkozást, és az attribútumok folyamata kétirányú irányban is folytatható.

Ha egy szabály azt határozza meg, hogy új összekötőt kell létrehozni, akkor a rendszer kiépítés néven nevezi el. Mivel azonban ez a művelet csak az összekötő területén történik, a rendszer addig nem viszi át a csatlakoztatott adatforrásba, amíg el nem végzi az exportálást.

## <a name="additional-resources"></a>További források
* [Azure AD Connect Sync: szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
