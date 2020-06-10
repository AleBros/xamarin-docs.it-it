---
title: " Xamarin.Essentials : Clipboard" Description: "questo documento descrive la classe degli Appunti in Xamarin.Essentials , che consente di copiare e incollare il testo negli Appunti di sistema tra le applicazioni".
ms. AssetID: C52AE99A-0FB3-425D-9106-3DA5777FEFA0 Author: jamesmontemagno ms. Author: Jamont ms. Date: 01/06/2020 ms. Custom: video No-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Appunti

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

Ogni volta che il contenuto degli Appunti è stato modificato, viene attivato un evento:

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
