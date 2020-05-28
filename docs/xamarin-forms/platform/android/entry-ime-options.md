---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bb77e9fafe39bf76a7d4290dba0bc658cd15094f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140036"
---
# <a name="entry-input-method-editor-options-on-android"></a>Opzioni dell'editor del metodo di input voce in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android imposta le opzioni di Input Method Editor (IME) per la tastiera soft per un [`Entry`](xref:Xamarin.Forms.Entry) . Ciò include l'impostazione del pulsante azione utente nell'angolo inferiore della tastiera morbida e le interazioni con `Entry` . Viene utilizzato in XAML impostando la [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) proprietà associata su un valore dell' [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

Il `Entry.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. [ `Entry.SetImeOptions` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. entry. SetImeOptions ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Voce}, Xamarin.Forms . Il metodo PlatformConfiguration. AndroidSpecific. ImeFlags), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per impostare l'opzione azione del metodo di input per la tastiera soft per [`Entry`](xref:Xamarin.Forms.Entry) , con l' [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione che fornisce i valori seguenti:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default): indica che non è richiesta alcuna chiave di azione specifica e che il controllo sottostante ne produrrà il proprio se possibile. Sarà `Next` o `Done` .
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None): indica che non verrà resa disponibile alcuna chiave di azione.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go): indica che la chiave dell'azione eseguirà un'operazione "go", portando l'utente alla destinazione del testo digitato.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search): indica che la chiave dell'azione esegue un'operazione di ricerca, portando l'utente ai risultati della ricerca del testo digitato.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send): indica che la chiave dell'azione eseguirà un'operazione di invio, reinviando il testo alla relativa destinazione.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next): indica che la chiave dell'azione eseguirà un'operazione "Avanti", portando l'utente al campo successivo che accetterà il testo.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done): indica che la chiave dell'azione eseguirà un'operazione "Done", chiudendo la tastiera soft.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous): indica che la chiave dell'azione eseguirà un'operazione "precedente", portando l'utente al campo precedente che accetterà il testo.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction): maschera per selezionare le opzioni di azione.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning): indica che il controllo ortografico non apprende dall'utente, né suggerisce correzioni in base a quanto digitato in precedenza dall'utente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen): indica che l'interfaccia utente non deve andare a schermo intero.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi): indica che non verrà visualizzata alcuna interfaccia utente per il testo estratto.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction): indica che non verrà visualizzata alcuna interfaccia utente per le azioni personalizzate.

Il risultato è che un [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valore specificato viene applicato alla tastiera morbida per [`Entry`](xref:Xamarin.Forms.Entry) , che imposta le opzioni di input Method Editor:

[![Voce Input Method Editor specifica della piattaforma](entry-ime-options-images/entry-imeoptions.png "Voce Input Method Editor specifica della piattaforma")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "Voce Input Method Editor specifica della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
