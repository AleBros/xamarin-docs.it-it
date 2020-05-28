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
ms.openlocfilehash: a891ec70f6f83984ed463fe914442758bdf57a2e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139308"
---
# <a name="introduction-to-effects"></a>Introduzione agli effetti

_Gli effetti consentono la personalizzazione dei controlli nativi in ogni piattaforma e vengono in genere utilizzati per piccole modifiche dello stile. Questo articolo fornisce un'introduzione agli effetti, delinea il confine tra gli effetti e i renderer personalizzati e descrive la classe PlatformEffect._

Xamarin.Forms[Pagine, layout e controlli](~/xamarin-forms/user-interface/controls/index.md) presentano un'API comune per descrivere le interfacce utente per dispositivi mobili multipiattaforma. Ogni pagina, layout e controllo viene sottoposta a rendering in modo diverso in ogni piattaforma utilizzando una `Renderer` classe che a sua volta crea un controllo nativo (corrispondente alla Xamarin.Forms rappresentazione), lo dispone sullo schermo e aggiunge il comportamento specificato nel codice condiviso.

Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Tuttavia, l'implementazione di una classe di renderer personalizzata per eseguire una personalizzazione di controlli semplici è spesso una risposta complessa. Gli effetti semplificano questo processo, consentendo una più semplice personalizzazione dei controlli nativi in ogni piattaforma.

Gli effetti vengono creati nei progetti specifici della piattaforma mediante la sottoclasse del `PlatformEffect` controllo, quindi gli effetti vengono utilizzati associando tali elementi a un controllo appropriato in una Xamarin.Forms libreria .NET standard o in un progetto di libreria condivisa.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Perché usare un effetto su un renderer personalizzato?

Gli effetti semplificano la personalizzazione di un controllo, sono riutilizzabili e possono essere associati a parametri per migliorare ulteriormente il riutilizzo.

Tutto ciò che si può ottenere con un effetto può essere ottenuto anche con un renderer personalizzato. I renderer personalizzati offrono tuttavia maggiore flessibilità e personalizzazione rispetto agli effetti. Le linee guida seguenti elencano le situazioni in cui è possibile scegliere un effetto su un renderer personalizzato:

- Un effetto è consigliato quando la modifica delle proprietà di un controllo specifico della piattaforma consentirà di ottenere il risultato auspicato.
- Un renderer personalizzato è obbligatorio quando è necessario sostituire i metodi di un controllo specifico della piattaforma.
- È necessario un renderer personalizzato quando è necessario sostituire il controllo specifico della piattaforma che implementa un Xamarin.Forms controllo.

## <a name="subclassing-the-platformeffect-class"></a>Creazione di una sottoclasse della classe PlatformEffect

La tabella seguente elenca lo spazio dei nomi per la classe `PlatformEffect` su ogni piattaforma e i tipi delle relative proprietà:

|Piattaforma|Spazio dei nomi|Contenitore|Controllo|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms. Platform. iOS|UIView|UIView|
|Android|Xamarin.Forms. Platform. Android|ViewGroup|Visualizzazione|
|Piattaforma UWP (Universal Windows Platform)|Xamarin.Forms. Platform. UWP|FrameworkElement|FrameworkElement|

Ogni classe `PlatformEffect` specifica della piattaforma espone le proprietà seguenti:

- `Container`: fa riferimento al controllo specifico della piattaforma in uso per implementare il layout.
- `Control`: fa riferimento al controllo specifico della piattaforma usato per implementare il Xamarin.Forms controllo.
- `Element`: fa riferimento al Xamarin.Forms controllo di cui è in corso il rendering.

Gli effetti non contengono informazioni sul tipo del contenitore, controllo o elemento a cui sono associati poiché possono essere collegati a qualsiasi elemento. Quando un effetto è collegato a un elemento che non supporta deve pertanto ridurre le prestazioni normalmente o generare un'eccezione. Le proprietà `Container`, `Control` e `Element` possono tuttavia eseguire il cast del tipo di implementazione. Per altre informazioni su questi tipi, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Ogni classe `PlatformEffect` specifica della piattaforma espone i metodi seguenti, che devono essere sottoposti a override per implementare un effetto:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached): viene chiamato quando un effetto è associato a un Xamarin.Forms controllo. Una versione sottoposta a override di questo metodo, in ogni classe di effetti specifici della piattaforma, è la posizione in cui eseguire la personalizzazione del controllo, insieme alla gestione delle eccezioni, nel caso in cui l'effetto non possa essere applicato al Xamarin.Forms controllo specificato.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached): viene chiamato quando un effetto viene scollegato da un Xamarin.Forms controllo. La pulizia dell'effetto, ad esempio l'annullamento della registrazione di un gestore eventi, avviene in una versione sottoposta a override del metodo, in ogni classe dell'effetto specifico della piattaforma.

Inoltre, `PlatformEffect` espone il [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) metodo, che può essere sottoposto a override. Questo metodo viene chiamato quando una proprietà dell'elemento è stata modificata. Una versione sottoposta a override di questo metodo, in ogni classe di effetti specifici della piattaforma, è la posizione in cui rispondere alle modifiche delle proprietà associabili sul Xamarin.Forms controllo. Deve sempre essere eseguito un controllo della proprietà modificata, dal momento che l'override può essere chiamato più volte.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
