---
title: Opzioni dell'editor del metodo di input voce in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma Android che imposta le opzioni di Input Method Editor per la tastiera soft per una voce.
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 3711e85bd30deb06f351f4539c5ffc7e4236efb6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653594"
---
# <a name="entry-input-method-editor-options-on-android"></a>Opzioni dell'editor del metodo di input voce in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android imposta le opzioni di Input Method Editor (IME) per la tastiera soft per [`Entry`](xref:Xamarin.Forms.Entry)un. Ciò include l'impostazione di pulsante di azione utente nell'angolo in basso della tastiera software e le interazioni con di `Entry`. Vengono utilizzati in XAML, impostando il [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) proprietà associata a un valore del [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

Il `Entry.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [`Entry.SetImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) metodo, nel [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per impostare l'opzione di azione del metodo di input per la tastiera per il [`Entry`](xref:Xamarin.Forms.Entry), con il [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione che fornisce i valori seguenti:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indica che è richiesto alcun codice di azione specifico e che il controllo sottostante genererà autonomamente se possibile. Si tratterà `Next` o `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indica che nessuna chiave azione sarà disponibile.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) : indica che la chiave dell'azione eseguirà un'operazione di "Vai", che richiede all'utente di destinazione del testo digitato.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) : indica che la chiave dell'azione viene eseguita un'operazione di "ricerca", che richiede all'utente i risultati di ricerca per il testo è stato digitato.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) : indica che la chiave dell'azione eseguirà un'operazione "Invia", fornire il testo per il valore di destinazione.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) : indica che la chiave dell'azione eseguirà un'operazione di "next", indirizzare l'utente al campo successivo in grado di accettare testo.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) : indica che la chiave dell'azione eseguirà un'operazione di "completata", chiudere la tastiera.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) : indica che la chiave dell'azione eseguirà un'operazione "precedente", indirizzare l'utente al campo precedente in grado di accettare testo.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – la maschera per selezionare le opzioni di azione.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) : indica che il controllo ortografico sarà informazioni da parte dell'utente, né suggeriscono correzioni basate su ciò che l'utente ha digitato in precedenza.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) : indica che l'interfaccia utente non va a schermo intero.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indica che nessuna interfaccia utente verrà visualizzato per il testo estratto.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indica che non verrà visualizzata alcuna interfaccia utente per le azioni personalizzate.

Il risultato è che un oggetto specificato [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valore viene applicato per la tastiera per il [`Entry`](xref:Xamarin.Forms.Entry), il metodo di input che imposta le opzioni dell'editor:

[![Metodo editor specifici della piattaforma di input voce](entry-ime-options-images/entry-imeoptions.png "movimento di input metodo editor specifici della piattaforma")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "movimento di input specifico della piattaforma editor (metodo)")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
