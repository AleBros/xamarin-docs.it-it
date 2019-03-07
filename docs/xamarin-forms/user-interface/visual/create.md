---
title: Creare un renderer oggetti visivi di xamarin. Forms
description: Oggetto visivo xamarin. Forms consente renderer essere applicate selettivamente alle VisualElement oggetti, senza la necessità di controlli di xamarin. Forms sottoclasse.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 3c614bcd0044a0aa4a698c830d2f2af5aba6c65a
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557513"
---
# <a name="xamarinforms-visual"></a>Oggetto visivo xamarin. Forms

Oggetto visivo xamarin. Forms consente renderer essere applicate selettivamente alle `VisualElement` oggetti, senza la necessità di controlli di xamarin. Forms sottoclasse. Tutti i renderer che specificano una `VisualType` come parte di `ExportRendererAttribute` verrà usato per le visualizzazioni di renderer, anziché il renderer predefinito. In fase di selezione del renderer, il `Visual` proprietà della vista vengono ispezionate e inclusi nel processo di selezione del renderer.

> [!IMPORTANT]
> Attualmente il `Visual` non può essere modificato dopo il controllo è stato eseguito il rendering, ma verrà modificato in una versione futura.

Xamarin. Forms include un aspetto visivo di base di progettazione dei materiali. Per altre informazioni, vedere [xamarin. Forms materiale Visual](material-visual.md).

## <a name="create-a-visual"></a>Creare un oggetto visivo

Nella libreria cross-platform, creare un tipo che deriva da `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

## <a name="register-the-visual-type"></a>Registrare il tipo di oggetto visivo

Creare un renderer per la visualizzazione desiderata e registrare il tipo di oggetto visivo come parte delle piattaforme `ExportRenderAttribute`:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ContentPage), typeof(CustomPageRenderer), new[] { typeof(CustomVisual) })]
namespace MyApp.Android
{
    public class CustomPageRenderer : PageRenderer
    {
        ...
    }
}
```

## <a name="consume-the-visual"></a>Utilizzare l'oggetto visivo

Le applicazioni possono acconsentire esplicitamente tramite l'oggetto visivo personalizzato tramite il `Visual` proprietà:

```xaml
<ContentPage ...
             Visual="Custom">
    ...
</ContentPage>
```

Il codice c# equivalente è:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = new CustomVisual();
```

## <a name="register-a-name-for-a-visual"></a>Registrare un nome per un oggetto visivo

Potrebbe esserci conflitti tra le librerie Visual diversi o forse è sufficiente fare riferimento a un oggetto visivo tramite un nome diverso. In questo scenario, è possibile usare un attributo dell'assembly per registrare un nome diverso per un determinato oggetto visivo:

```csharp
[assembly: Visual("MyMaterialName", typeof(VisualMarker.MaterialVisual))]
```

L'oggetto visivo personalizzato può quindi essere utilizzato con il nome registrato:

```xaml
<ContentPage ...
             Visual="MyMaterialName">
    ...
</ContentPage>
````

## <a name="related-links"></a>Collegamenti correlati

- [Oggetto visivo materiali di xamarin. Forms](material-visual.md)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
