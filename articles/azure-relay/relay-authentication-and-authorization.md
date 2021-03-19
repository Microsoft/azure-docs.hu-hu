---
title: Hitelesítés és engedélyezés Azure Relay | Microsoft Docs
description: Ez a cikk áttekintést nyújt a közös hozzáférésű aláírás (SAS) hitelesítéséről a Azure Relay szolgáltatással.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033377"
---
# <a name="azure-relay-authentication-and-authorization"></a>Hitelesítés és engedélyezés Azure Relay

Az alkalmazások közös hozzáférésű aláírási (SAS-) hitelesítés használatával hitelesíthetők Azure Relay. Az SAS-hitelesítés lehetővé teszi az alkalmazások számára, hogy a továbbítási névtérben konfigurált hozzáférési kulccsal hitelesítsék a Azure Relay szolgáltatást. Ezt a kulcsot használhatja arra, hogy létrehoz egy közös hozzáférési aláírási jogkivonatot, amelyet az ügyfelek a Relay szolgáltatásban való hitelesítéshez használhatnak.

## <a name="shared-access-signature-authentication"></a>Közös hozzáférésű aláírások hitelesítése

Az [sas-hitelesítés](../service-bus-messaging/service-bus-sas.md) lehetővé teszi a felhasználók számára, hogy hozzáférést biztosítson Azure Relay erőforrásokhoz adott jogokkal. Az SAS-hitelesítés magában foglalja egy adott erőforráshoz társított jogokkal rendelkező titkosítási kulcs konfigurációját. Az ügyfelek ezután egy SAS-token bemutatásával férhetnek hozzá ehhez az erőforráshoz, amely az elérni kívánt erőforrás-URI-t és a beállított kulccsal lejáró lejárati értéket tartalmazza.

Az SAS-kulcsok a Relay-névtérben konfigurálhatók. A Service Bus üzenetkezeléstől eltérően a [Relay hibrid kapcsolatok](relay-hybrid-connections-protocol.md) támogatja a jogosulatlan vagy névtelen küldőket. Az entitáshoz való névtelen hozzáférést a létrehozáskor engedélyezheti, ahogy az a portálon látható következő képernyőképen is látható:

![A "hibrid kapcsolat létrehozása" nevű párbeszédpanel "név" szövegmezővel és az "ügyfél-hitelesítés szükséges" címkével ellátott jelölőnégyzettel rendelkezik, amely be van jelölve.][0]

Az SAS használatához konfigurálhat egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objektumot egy továbbító névtéren, amely a következőkből áll:

* A szabályt azonosító *Kulcsnév* .
* A *PrimaryKey* a sas-jogkivonatok aláírására/érvényesítésére használt titkosítási kulcs.
* A *értesítésiközpont* a sas-jogkivonatok aláírására/érvényesítésére használt titkosítási kulcs.
* A figyelési, küldési vagy kezelési jogosultságok gyűjteményét képviselő *jogosultságok* .

A névtér szintjén konfigurált engedélyezési szabályok hozzáférést biztosíthatnak a névtérben lévő összes továbbítási kapcsolathoz a megfelelő kulccsal aláírt tokenekkel rendelkező ügyfelek számára. Akár 12 ilyen engedélyezési szabály is konfigurálható egy továbbító névtérben. Alapértelmezés szerint minden egyes jogosultsággal rendelkező [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) konfigurálva van minden névtérhez, amikor először kiépítik őket.

Egy entitás eléréséhez az ügyfélnek egy adott [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)létrehozott sas-tokent kell használnia. Az SAS-tokent egy olyan erőforrás-karakterlánc HMAC-SHA256 hozza létre, amely a hozzáférést igényelő erőforrás-URI-t, valamint az engedélyezési szabályhoz társított titkosítási kulccsal lejáró lejáratot tartalmaz.

A Azure Relay SAS-hitelesítésének támogatása az Azure .NET SDK 2,0-as és újabb verzióiban érhető el. Az SAS a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)támogatását is támogatja. Minden olyan API-t, amely a paramétert fogadja, az SAS-kapcsolatok karakterláncok támogatását tartalmazza.

## <a name="next-steps"></a>Következő lépések

- A SAS-vel kapcsolatos további részletekért olvassa [el Service Bus hitelesítés közös hozzáférési aláírásokkal](../service-bus-messaging/service-bus-sas.md) című cikkét.
- A Hibrid kapcsolatok képességgel kapcsolatos részletes információkért tekintse meg a [Azure Relay hibrid kapcsolatok protokoll útmutatóját](relay-hybrid-connections-protocol.md) .
- Service Bus üzenetkezelési hitelesítéssel és engedélyezéssel kapcsolatos információkért lásd: [Service Bus hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png