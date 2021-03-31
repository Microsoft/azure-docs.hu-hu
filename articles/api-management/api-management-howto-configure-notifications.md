---
title: Értesítések és e-mail sablonok konfigurálása
titleSuffix: Azure API Management
description: Ismerje meg, hogyan konfigurálhatja az értesítéseket és az e-mail-sablonokat az Azure API Managementban.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690302"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Az értesítések és e-mail sablonok konfigurálása az Azure API Management szolgáltatásban

API Management lehetővé teszi az értesítések konfigurálását adott eseményekhez, valamint a API Management-példányok rendszergazdájával és fejlesztőivel folytatott kommunikációhoz használt e-mail-sablonok konfigurálását. Ez a cikk bemutatja, hogyan konfigurálhatja az értesítéseket az elérhető eseményekhez, és áttekintést nyújt az eseményekhez használt e-mail sablonok konfigurálásáról.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik API Management Service-példánnyal, hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Értesítések konfigurálása

1.  Válassza ki az **API Management** -példányt.
2.  Kattintson az **értesítések** elemre az elérhető értesítések megtekintéséhez.

    ![Közzétevői értesítések][api-management-publisher-notifications]

    Az alábbi események listáját beállíthatja az értesítésekhez.

    -   **Előfizetési kérelmek (jóváhagyást igénylő)** – a megadott e-mail-címzettek és felhasználók e-mail-értesítéseket kapnak a jóváhagyást igénylő API-termékek előfizetési kéréseiről.
    -   **Új előfizetések** – a megadott e-mail-címzettek és felhasználók e-mailben értesítést kapnak az új API-termékek előfizetéséről.
    -   **Application Gallery-kérelmek** – a megadott e-mail-címzettek és felhasználók e-mail-értesítéseket kapnak, amikor új alkalmazások kerülnek be az alkalmazás-katalógusba.
    -   **Titkos másolat** – a megadott e-mail-címzettek és felhasználók e-mailben megkapják a fejlesztők számára küldött e-mailek vak széndioxid-másolatait.
    -   **Új probléma vagy Megjegyzés** – a megadott e-mail-címzettek és felhasználók kapnak e-mail-értesítéseket, amikor új probléma vagy megjegyzés érkezik a fejlesztői portálra.
    -   **Fiók üzenetének bezárása** – a megadott e-mail-címzettek és felhasználók e-mail-értesítéseket kapnak a fiókok bezárásakor.
    -   Az **előfizetési kvóta korlátja** – a következő e-mail-címzettek és felhasználók kapnak e-mail-értesítéseket, amikor a használati kvóta közel lesz a használati kvótához.

        > [!NOTE]
        > Az értesítéseket a kvóta csak az [előfizetési szabályzat alapján](api-management-access-restriction-policies.md#SetUsageQuota) indítja el. [A kulcsokra](api-management-access-restriction-policies.md#SetUsageQuotaByKey) vonatkozó házirend alapján nem hozhatók be értesítések.

    Minden eseménynél megadhatja az e-mail-címzetteket az e-mail-cím szövegmező használatával, vagy kijelölhet felhasználókat a listából.

3.  Az értesítési e-mail címek megadásához írja be őket az e-mail-cím szövegmezőbe. Ha több e-mail-címmel rendelkezik, vesszővel válassza el őket.

    ![Értesítés címzettjei][api-management-email-addresses]

4.  Nyomja meg az **Add** (Hozzáadás) gombot.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Értesítési sablonok konfigurálása

A API Management értesítési sablonokat biztosít a szolgáltatás felügyelete és használata során elküldött e-mail-üzenetekhez. A következő e-mail-sablonokat kell megadnia.

-   Az Application Gallery beküldése jóváhagyva
-   Fejlesztői Búcsús levél
-   A fejlesztői kvóta korlátja értesítés
-   Felhasználó meghívása
-   Új Megjegyzés hozzáadva egy hibához
-   Új probléma érkezett
-   Új előfizetés aktiválva
-   Előfizetés megújított megerősítése
-   Előfizetési kérelem elutasítása
-   Előfizetési kérelem érkezett

Ezek a sablonok igény szerint módosíthatók.

Az API Management példányhoz tartozó e-mail-sablonok megtekintéséhez és konfigurálásához kattintson az **értesítések sablonok** elemre.

![E-mail-sablonok][api-management-email-templates]

Minden e-mail-sablonhoz tartozik egy egyszerű szöveg, a törzs definíciója pedig HTML formátumban. Az egyes elemek tetszés szerint testreszabhatók.

![E-mail-sablon szerkesztője][api-management-email-template]

A **Parameters (paraméterek** ) lista a tulajdonos vagy a törzsbe beszúrt paraméterek listáját tartalmazza, és az e-mail elküldésekor a rendszer lecseréli a kijelölt értéket. Egy paraméter beszúrásához vigye a kurzort oda, ahová a paramétert el szeretné helyezni, majd kattintson a paraméter nevétől balra található nyílra.

> [!NOTE]
> A rendszer nem cseréli le a paramétereket a tényleges értékekkel egy teszt megtekintésekor vagy küldésekor.

Az e-mail sablon módosításainak mentéséhez kattintson a **Mentés** gombra, vagy a módosítások megszakításához kattintson az **Elvetés** gombra.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
