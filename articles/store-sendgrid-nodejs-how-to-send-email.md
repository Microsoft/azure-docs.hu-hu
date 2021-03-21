---
title: A SendGrid e-mail szolgáltatás használata (Node.js) | Microsoft Docs
description: Ismerje meg, hogyan küldhet e-mailt az Azure SendGrid e-mail szolgáltatásával. A Node.js API használatával írt példák.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015424"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>E-mailek küldése a SendGrid használatával Node.js

Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure SendGrid e-mail szolgáltatásával. A minták a Node.js API használatával íródnak. A tárgyalt forgatókönyvek közé tartozik például az **e-mailek** létrehozása, az **e-mail küldése**, a **mellékletek hozzáadása**, **a szűrők használata és a** **Tulajdonságok frissítése**. További információt a SendGrid és az e-mailek küldéséről a [következő lépések](#next-steps) című szakaszban talál.

## <a name="what-is-the-sendgrid-email-service"></a>Mi a SendGrid E-mail szolgáltatás?

A SendGrid egy [felhőalapú e-mail-szolgáltatás] , amely megbízható [tranzakciós e-mail-kézbesítést], skálázhatóságot és valós idejű elemzéseket biztosít, valamint rugalmas API-kat, amelyek egyszerűvé teszik az egyéni integrációt. Az általános SendGrid-használati forgatókönyvek a következők:

* Nyugták automatikus küldése az ügyfeleknek
* A terjesztési listán szereplő ügyfelek havi e-szórólapok és különleges ajánlatok küldésének felügyelete
* Valós idejű mérőszámok gyűjtése a letiltott e-mailekhez és az ügyfelekre való válaszadáshoz
* Jelentések létrehozása a trendek azonosításához
* Ügyfelekkel kapcsolatos kérdések továbbítása
* Az alkalmazás e-mail-értesítései

További információ: [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Hivatkozás a SendGrid Node.js modulra

A Node.js SendGrid-modulja a következő paranccsal telepíthető a Node Package Manager (NPM) használatával:

```bash
npm install sendgrid
```

A telepítést követően a modult az alkalmazásban a következő kód használatával kérheti le:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

A SendGrid modul exportálja a **SendGrid** és az **e-mail-** függvényeket.
A **SendGrid** a webes API-n keresztül küldi el az e-maileket, míg az **e** -mail-üzenet beágyazva.

## <a name="how-to-create-an-email"></a>Útmutató: E-mail létrehozása

Egy e-mail-üzenet a SendGrid modul használatával történő létrehozásakor először létre kell hozva egy e-mail-üzenetet az e-mail-függvénnyel, majd el kell küldeni azt a SendGrid függvény használatával. Az alábbi példa egy új üzenet létrehozását szemlélteti az e-mail-függvény használatával:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

HTML-üzenetet is megadhat az azt támogató ügyfelek számára a HTML-tulajdonság beállításával. Például:

```javascript
html: This is a sample <b>HTML<b> email message.
```

A szöveg és a HTML tulajdonságok beállítása egyszerű tartalékot biztosít a szöveges tartalomhoz olyan ügyfelek számára, amelyek nem támogatják a HTML-üzeneteket.

Az e-mail-függvény által támogatott összes tulajdonságról további információt a következő témakörben talál: [sendgrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése

Miután létrehozta az e-mail-üzenetet az e-mail-függvénnyel, elküldheti azt a SendGrid által biztosított webes API-val. 

### <a name="web-api"></a>Webes API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Míg a fenti példák egy e-mail-objektum és egy visszahívási függvény átadását mutatják, közvetlenül is meghívhatják a Send függvényt az e-mail-tulajdonságok megadásával. Például:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Útmutató: melléklet hozzáadása
A mellékletek hozzáadhatók üzenetekhez úgy, hogy megadják a **fájlok** tulajdonság fájlnevét és elérési útját (ke) t. Az alábbi példa egy melléklet küldését szemlélteti:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> A **Files** tulajdonság használatakor a fájlnak elérhetőnek kell lennie az [FS. readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile)használatával. Ha a csatolni kívánt fájl az Azure Storage-ban van tárolva, például egy blob-tárolóban, először a fájlt helyi tárolóba vagy egy Azure-meghajtóra kell másolnia, hogy mellékletként lehessen elküldeni a **Files** tulajdonság használatával.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Útmutató: a láblécek és a nyomkövetés engedélyezése szűrők használatával

A SendGrid további e-mail-funkciókat biztosít a szűrők használatával. Ezek olyan beállítások, amelyek hozzáadhatók egy e-mail-üzenethez, amely lehetővé teszi bizonyos funkciók használatát, például a követést, a Google Analyticset, az előfizetés nyomon követését stb. A szűrők teljes listáját a [szűrési beállítások][Filter Settings]című témakörben tekintheti meg.

A szűrők az üzenetekre a **szűrők** tulajdonság használatával alkalmazhatók.
A szűrőket a szűrőre jellemző beállításokat tartalmazó kivonat határozza meg.
Az alábbi példák bemutatják a láblécet, majd a szűrők követése elemre kattintanak:

### <a name="footer"></a>Lábléc

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Kattintson a nyomon követés gombra

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Útmutató: az e-mail-tulajdonságok frissítése

Egyes e-mail-tulajdonságok felülírhatják a **SetProperty** vagy a **addProperty** használatával történő hozzáfűzést. Például további címzetteket adhat hozzá a következő használatával:

```javascript
email.addTo('jeff@contoso.com');
```

vagy állítson be szűrőt a használatával

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

További információ: [sendgrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: további SendGrid-szolgáltatások használata

A SendGrid olyan webes API-kat kínál, amelyek segítségével további SendGrid funkciókat alkalmazhat az Azure-alkalmazásból. További részletekért tekintse meg a [SENDGRID API dokumentációját][SendGrid API documentation].

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, kövesse az alábbi hivatkozásokat további információért.

* SendGrid Node.js modul adattára: [SendGrid-NodeJS][sendgrid-nodejs]
* SendGrid API-dokumentáció: <https://sendgrid.com/docs>
* SendGrid Speciális ajánlat az Azure-ügyfelek számára: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[felhőalapú e-mail szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail kézbesítés]: https://sendgrid.com/transactional-email
