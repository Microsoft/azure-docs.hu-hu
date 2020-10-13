---
title: Z-pufferelési hibák csökkentése
description: Ismerteti a z-harci összetevők enyhítésének eljárásait
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e217676f5e1f4dcba24917cb140d9d4d8fcc422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90024366"
---
# <a name="z-fighting-mitigation"></a>Z-pufferelési hibák csökkentése

Ha két felület átfedésben van, nem egyértelmű, hogy az egyiket a másikon kell megjeleníteni. Az eredmény akár képpont is változhat, ami a kamera nézettől függő összetevőkből ered. Ennek következtében a kamera vagy a háló mozgatásakor ezek a minták vibrálnak. Ezt az összetevőt *z-harcok*nevezik. Az AR-és a VR-alkalmazások esetében a probléma intenzívebbé válik, mert a fej által csatlakoztatott eszközök természetesen mindig áthelyezhetők. Ha meg szeretné akadályozni, hogy a megjelenítők ne tudják elhárítani a megoldást, az Azure Remote rendering szolgáltatásban is elérhető.

## <a name="z-fighting-mitigation-modes"></a>Z – a megelőzési módok elleni küzdelem

|Helyzet                        | Eredmény                               |
|---------------------------------|:-------------------------------------|
|Normál z – harcok               |![Nem determinisztikus elsőbbség a vörös és a zöld quadok között](./media/zfighting-0.png)|
|Z – a küzdelem engedélyezése engedélyezve    |![A vörös quad elsőbbséget élvez](./media/zfighting-1.png)|
|Pepita kiemelése engedélyezve|![A piros és zöld négymagos váltógomb a Pepita mintában](./media/zfighting-2.png)|

A következő kód lehetővé teszi a z-harcok enyhítését:

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Actions()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> A Z-Fighting mérséklés egy globális beállítás, amely az összes megjelenített rácsvonalra hatással van.

## <a name="reasons-for-z-fighting"></a>A z-Fighting okai

Z – a harcok főleg két okból történnek:

1. Ha a felületek nagyon távol vannak a kamerától, a mélységi értékeik pontossága és az értékek megkülönböztetni lesznek
1. Ha a hálóban lévő felületek fizikailag átfedésben vannak

Az első probléma mindig megtörténhet, és nehezen lehet megtörténni. Ha ez történik az alkalmazásban, győződjön meg arról, hogy a *közel-sík* távolsága a *távoli sík* felé irányuló távolság a gyakorlatban alacsony. Tegyük fel például, hogy az 0,01-es és a távolsági sík közelében található közel-sík az 1000-es távolságra kerül, de sokkal hamarabb létrehozza ezt a problémát, mint a 0,1 közel-és a 20-as távolsági síkon.

A második probléma a rosszul létrehozott tartalom mutatója. A valós világban két objektum nem lehet ugyanazon a helyen, mint egy időben. Az alkalmazástól függően előfordulhat, hogy a felhasználók tudni szeretnék, hogy az átfedésben lévő felületek léteznek-e, és hol vannak. Például egy olyan épület CAD-jelenete, amely egy valós fejlesztés alapja, nem tartalmazhat fizikailag lehetetlen felületi metszeteket. A vizuális vizsgálat lehetővé tételéhez a kiemelés mód elérhető, amely a lehetséges z-harcok animált Pepita mintaként jeleníti meg.

## <a name="limitations"></a>Korlátozások

Az ajánlott z – küzdelem a legjobb megoldás. Nincs garancia arra, hogy az összes z-harcok megszűnnek. Emellett a rendszer automatikusan az egyik felületet fogja előnyben részesíteni egy másikkal. Így ha olyan felületek vannak, amelyek túl vannak közelebb egymáshoz, előfordulhat, hogy a "rossz" felület véget ér a tetején. Gyakori probléma esete, ha a szöveg és más matricák egy felületre vannak alkalmazva. A z-harcok enyhítése lehetővé teszi, hogy ezek a részletek könnyedén eltűnnek.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

* A z-harcok enyhítésének engedélyezése csekély mértékben nem jelent teljesítménybeli terhelést.
* A z-harcok átfedésének engedélyezése emellett nem triviális teljesítménybeli terhelést is jelent, bár a jelenettől függően változhat.

## <a name="api-documentation"></a>API-dokumentáció

* [C# RemoteManager. ZFightingMitigationSettings tulajdonság](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.zfightingmitigationsettings)
* [C++ RemoteManager:: ZFightingMitigationSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#zfightingmitigationsettings)

## <a name="next-steps"></a>Következő lépések

* [Renderelési módok](../../concepts/rendering-modes.md)
* [Újravetítés késői fázisban](late-stage-reprojection.md)
