---
title: 'Xamarin.Essentials: Clipboard'
description: Questo documento descrive la classe Clipboard in Xamarin.Essentials, che consente di copiare e incollare testo negli Appunti di sistema tra un'applicazione e l'altra.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
ms.openlocfilehash: 0b5eaf3feb608a352f8f9c97bdddac55c89d4f94
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545174"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Clipboard

La classe **Clipboard** consente di copiare e incollare testo negli Appunti del sistema tra un'applicazione e l'altra.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Uso di Clipboard

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per controllare se attualmente negli **Appunti** è presente testo da incollare:

```csharp
var hasText = Clipboard.HasText;
```

Per impostare il testo negli **Appunti**:

```csharp
await Clipboard.SetTextAsync("Hello World");
```

Per leggere il testo dagli **Appunti**:

```csharp
var text = await Clipboard.GetTextAsync();
```

Ogni volta che uno dei contenuti degli Appunti ha modificato un evento viene attivato:

```csharp
public class ClipboardTest
{
    public ClipboardTest()
    {
        // Register for clipboard changes, be sure to unsubscribe when needed
        Clipboard.ClipboardContentChanged += OnClipboardContentChanged;
    }

    void OnClipboardContentChanged(object sender, EventArgs    e)
    {
        Console.WriteLine($"Last clipboard change at {DateTime.UtcNow:T}";);
    }
}
```

> [!TIP]
> L'accesso agli Appunti deve essere eseguito sul thread dell'interfaccia utente principale. Vedere l'API [MainThread](~/essentials/main-thread.md) per informazioni su come richiamare i metodi sul thread dell'interfaccia utente principale.

## <a name="api"></a>API

- [Codice sorgente di Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentazione dell'API Clipboard](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
