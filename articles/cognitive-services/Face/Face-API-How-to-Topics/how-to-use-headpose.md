---
title: A HeadPose attribútum használata
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a HeadPose attribútumot az arc téglalap automatikus elforgatásához vagy a Head kézmozdulatok észleléséhez egy videó-hírcsatornában.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 02/23/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: cec466fc420e435159e2882d7b39ef6d03eeea7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733482"
---
# <a name="use-the-headpose-attribute"></a>A HeadPose attribútum használata

Ebből az útmutatóból megtudhatja, hogyan használhatja az észlelt arc HeadPose attribútumát bizonyos kulcsfontosságú forgatókönyvek engedélyezéséhez.

## <a name="rotate-the-face-rectangle"></a>Az arc téglalap elforgatása

Az összes észlelt képpel visszaadott arc téglalap jelzi a képen látható arc helyét és méretét. Alapértelmezés szerint a téglalap mindig a képhez van igazítva (az oldala függőleges és vízszintes); Ez nem hatékony a szögben lévő arcok kialakításához. Olyan helyzetekben, amikor programozott módon kívánja kivágni a képeket a képeken, jobb, ha el szeretné forgatni a téglalapot.

A [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) minta alkalmazás a HeadPose attribútumot használja az észlelt arc téglalapok elforgatásához.

### <a name="explore-the-sample-code"></a>A mintakód megismerése

Az HeadPose attribútum használatával programozott módon elforgathatja az arc téglalapot. Ha az arcok észlelése során megadja ezt az attribútumot (lásd: [arcok észlelése](HowtoDetectFacesinImage.md)), később is lekérdezheti. A [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) alkalmazás következő metódusa a **DetectedFace** objektumok listáját jeleníti meg, és visszaadja a **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** Object objektumok listáját. Az **arc** itt egy olyan egyéni osztály, amely az arc-és a frissített téglalap-koordinátákat is tárolja. A rendszer kiszámítja az új értékeket a **felül**, a **bal**, a **szélesség** és a **magasság** értéknél, és egy új mező **FaceAngle** határozza meg az elforgatást.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>A frissített téglalap megjelenítése

Innen a visszaadott **Face** objektumokat használhatja a képernyőn. Az [FaceDetectionPage. XAML](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) következő soraiban látható, hogyan jelennek meg az új négyszög az adatokból:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Fő kézmozdulatok észlelése

A HeadPose-változások valós idejű nyomon követésével észlelhetők a fej-kézmozdulatok, például az bólintott és a fej rázása. Ezt a funkciót egyéni élettartam-detektorként is használhatja.

Az azonnali észlelés feladata annak megállapítása, hogy a tárgy valódi személy-e, nem pedig képvagy videós ábrázolás. A fej-kézmozdulatok detektora az élőság ellenőrzésének egyik módjaként szolgálhat, különösen egy személy képének ábrázolásával szemben.

> [!CAUTION]
> A Head kézmozdulatok valós idejű észleléséhez a Face API magas arányban kell meghívnia (másodpercenként többször). Ha ingyenes szintű (F0) előfizetéssel rendelkezik, ez a megoldás nem lesz lehetséges. Ha fizetős szintű előfizetéssel rendelkezik, győződjön meg róla, hogy kiszámította a gyors API-hívások fő kézmozdulat-észleléshez való feltételeit.

Tekintse meg az [Face HeadPose mintát](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) a githubon a fő kézmozdulatok észlelésére szolgáló példaként.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) alkalmazást a githubon az elforgatott arc téglalapok működéséhez. Vagy tekintse meg a [Face HeadPose minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) alkalmazást, amely valós időben követi nyomon a HeadPose attribútumot a fő mozgások észlelése érdekében.