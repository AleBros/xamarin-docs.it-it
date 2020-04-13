---
title: Introduzione agli effetti
description: Gli effetti consentono di personalizzare i controlli nativi di ogni piattaforma e in genere vengono usati per apportare piccole modifiche allo stile. Questo articolo offre un'introduzione agli effetti, delinea il limite tra gli effetti e i renderer personalizzati e descrive la classe PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9325c34c645b75f28c7e2070f6bb095780ddb02
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771457"
---
# <a name="introduction-to-effects"></a>Introduzione agli effetti

_Gli effetti consentono di personalizzare i controlli nativi in ogni piattaforma e vengono in genere utilizzati per piccole modifiche di stile. In questo articolo viene fornita un'introduzione agli effetti, viene descritto il limite tra effetti e renderer personalizzati e viene descritta la classe PlatformEffect._

Le [pagine, i layout e i controlli](~/xamarin-forms/user-interface/controls/index.md) Xamarin.Forms presentano un'API comune per descrivere le interfacce utente per dispositivi mobili multipiattaforma. Il rendering di ogni pagina, layout e controllo viene eseguito in modo diverso su ogni piattaforma usando una classe `Renderer`, che a sua volta crea un controllo nativo (corrispondente alla rappresentazione Xamarin.Forms), lo dispone sullo schermo e aggiunge il comportamento specificato nel codice condiviso.

Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Tuttavia, l'implementazione di una classe di renderer personalizzata per eseguire una personalizzazione di controlli semplici è spesso una risposta complessa. Gli effetti semplificano questo processo, consentendo una più semplice personalizzazione dei controlli nativi in ogni piattaforma.

Gli effetti vengono creati in processi specifici per la piattaforma creando sottoclassi del controllo `PlatformEffect` e vengono usati associandoli a un controllo appropriato in una libreria .NET Standard o in un progetto di libreria condivisa per Xamarin.Forms.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Perché usare un effetto su un renderer personalizzato?

Gli effetti semplificano la personalizzazione di un controllo, sono riutilizzabili e possono essere associati a parametri per migliorare ulteriormente il riutilizzo.

Tutto ciò che si può ottenere con un effetto può essere ottenuto anche con un renderer personalizzato. I renderer personalizzati offrono tuttavia maggiore flessibilità e personalizzazione rispetto agli effetti. Le linee guida seguenti elencano le situazioni in cui è possibile scegliere un effetto su un renderer personalizzato:

- Un effetto è consigliato quando la modifica delle proprietà di un controllo specifico della piattaforma consentirà di ottenere il risultato auspicato.
- Un renderer personalizzato è obbligatorio quando è necessario sostituire i metodi di un controllo specifico della piattaforma.
- Un renderer personalizzato è obbligatorio quando è necessario sostituire il controllo specifico della piattaforma che implementa un controllo Xamarin.Forms.

## <a name="subclassing-the-platformeffect-class"></a>Creazione di una sottoclasse della classe PlatformEffect

La tabella seguente elenca lo spazio dei nomi per la classe `PlatformEffect` su ogni piattaforma e i tipi delle relative proprietà:

|Piattaforma|Spazio dei nomi|Contenitore|Controllo|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|Visualizzazione|
|Piattaforma UWP (Universal Windows Platform)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Ogni classe `PlatformEffect` specifica della piattaforma espone le proprietà seguenti:

- `Container`: fa riferimento al controllo specifico della piattaforma in uso per implementare il layout.
- `Control`: fa riferimento al controllo specifico della piattaforma in uso per implementare il controllo Xamarin.Forms.
- `Element`: fa riferimento al controllo Xamarin.Forms sottoposto a rendering.

Gli effetti non contengono informazioni sul tipo del contenitore, controllo o elemento a cui sono associati poiché possono essere collegati a qualsiasi elemento. Quando un effetto è collegato a un elemento che non supporta deve pertanto ridurre le prestazioni normalmente o generare un'eccezione. Le proprietà `Container`, `Control` e `Element` possono tuttavia eseguire il cast del tipo di implementazione. Per altre informazioni su questi tipi, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Ogni classe `PlatformEffect` specifica della piattaforma espone i metodi seguenti, che devono essere sottoposti a override per implementare un effetto:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached)– chiamato quando un effetto è collegato a un Xamarin.Forms controllo. La personalizzazione del controllo avviene in una versione sottoposta a override del metodo, in ogni classe dell'effetto specifico della piattaforma, unitamente alla gestione delle eccezioni nel caso in cui non sia possibile applicare l'effetto al controllo Xamarin.Forms specificato.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached)– chiamato quando un effetto viene staccato da un Xamarin.Forms controllo. La pulizia dell'effetto, ad esempio l'annullamento della registrazione di un gestore eventi, avviene in una versione sottoposta a override del metodo, in ogni classe dell'effetto specifico della piattaforma.

Inoltre, espone `PlatformEffect` il [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) metodo , che può anche essere sottoposto a override. Questo metodo viene chiamato quando una proprietà dell'elemento è stata modificata. La risposta alle modifiche delle proprietà associabili nel controllo Xamarin.Forms avviene in una versione sottoposta a override del metodo, in ogni classe dell'effetto specifico della piattaforma. Deve sempre essere eseguito un controllo della proprietà modificata, dal momento che l'override può essere chiamato più volte.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
