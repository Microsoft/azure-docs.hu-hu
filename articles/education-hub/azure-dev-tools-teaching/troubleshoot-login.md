---
title: Bejelentkezési hiba elhárítása az Azure fejlesztői eszközök tanításához
description: Ez ismerteti azokat a műveleteket, amelyeket a tanulónak el kell végeznie, ha hibaüzenetet kap, amikor bejelentkezik az Azure dev Tools for Teaching szolgáltatásba.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096652"
---
# <a name="troubleshooting-student-login-issues"></a>A tanulói bejelentkezési problémák elhárítása
Az Azure fejlesztői eszközök tanításhoz való hozzáféréséhez a felhasználónak Microsoft-fiókkal (MSA) kell rendelkeznie. A tanulók automatikusan átirányítják a MSA, ha a fiókja még nem MSA, vagy nem kapcsolódik a MSA. Ha a tartománya Active Directoryhoz van társítva, akkor az adott tartomány alá tartozó összes fiók már MSA minősül.

Ha egy tanuló megpróbál bejelentkezni, és a következő hibaüzenetet kapja, használja az alább ismertetett megoldások egyikét.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Bejelentkezési hibaüzenet." border="false":::

Két megoldás létezik: hozzon létre egy új Microsoft-fiók, vagy használjon egy meglévő Microsoft-fiók.

## <a name="create-a-new-microsoft-account"></a>Új Microsoft-fiók létrehozása
### <a name="use-a-university-email-address"></a>Egyetemi e-mail-cím használata
Ha egyetemi e-mail-címmel jelentkezik be (például: `John.Smith@university.edu` ), létre kell hoznia egy Microsoft-fiók az e-mail-címmel. A fiók létrehozása ingyenes, és mindössze néhány percet vesz igénybe. A Microsoft-fiók lehetővé teszi, hogy egyetlen felhasználónévvel és jelszóval automatikusan bejelentkezzen az összes Microsoft-termékbe.

### <a name="use-a-personal-email-address"></a>Személyes e-mail-cím használata
Ha személyes e-mail-címmel jelentkezik be (például: `John.Smith@email.com` ), de van egy egyetemi e-mail-címe, próbálja meg az egyetemi e-mail-címet használni. Ha korábban bejelentkezett az intézmény webáruházba személyes e-mail-címmel, vagy nem rendelkezik egyetemi e-mail-címmel, akkor létre kell hoznia vagy csatolnia kell egy Microsoft-fiók a személyes e-mail-címével.

## <a name="use-an-existing-microsoft-account"></a>Meglévő Microsoft-fiók használata
Ha egy tanuló rendelkezik egy meglévő Microsoft-fiókval (például Xbox), akkor a fiókot egy Azure dev Tools-fiókhoz is csatlakozhatják.

1. Nyissa meg a következőt: https://account.microsoft.com.
1. Jelentkezzen be Microsoft-fiók hitelesítő adataival.
1. Válassza **ki az adatait a** menüszalag felső menüjéből.

1. Kattintson **a kezelés a microsoftba való bejelentkezés módjára**. A rendszer megkéri, hogy ellenőrizze az identitását. A rendszer e-mailben elküld egy biztonsági kódot.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="A bejelentkezés kezelése." border="false":::

1. Adja meg az e-mailben megadott biztonsági kódot.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Adja meg a bejelentkezési kódot." border="false":::!

1. Kattintson az **E-mail hozzáadása** a fiókhoz lehetőségre, és adja meg az egyetemi e-mail-címét.
Amikor legközelebb bejelentkezik, az egyetemi e-mail-címével férhet hozzá az Azure fejlesztői eszközökhöz az oktatási célra.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="A Microsoftba való bejelentkezés módjának kezelése." border="false":::

## <a name="next-steps"></a>Következő lépések
- [Gyakori kérdések](program-faq.md)

- [Támogatási lehetőségek](program-support.md)
