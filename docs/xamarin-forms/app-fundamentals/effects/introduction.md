---
title: Introduzione a effetti
description: Effetti consentono controlli nativi in ogni piattaforma per la personalizzazione e vengono in genere usati per lo stile piccole modifiche. Questo articolo viene fornita un'introduzione agli effetti, delinea il limite tra gli effetti e renderer personalizzati e descrive la classe PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d3fa958e999a10832d5fa15e4190077955b0e6df
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997380"
---
# <a name="introduction-to-effects"></a>Introduzione a effetti

_Effetti consentono controlli nativi in ogni piattaforma per la personalizzazione e vengono in genere usati per lo stile piccole modifiche. Questo articolo viene fornita un'introduzione agli effetti, delinea il limite tra gli effetti e renderer personalizzati e descrive la classe PlatformEffect._

Xamarin. Forms [controlli, layout e pagine](~/xamarin-forms/user-interface/controls/index.md) presenta un'API comune per descrivere le interfacce utente per dispositivi mobili multipiattaforma. Ogni pagina, layout e il controllo viene eseguito il rendering in modo diverso in ogni piattaforma usando un `Renderer` classe che a sua volta crea un controllo nativo (che corrisponde alla relativa rappresentazione in xamarin. Forms), lo dispone sullo schermo e aggiunge il comportamento specificato nel condiviso codice.

Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Tuttavia, implementazione di una classe di renderer personalizzato per eseguire una personalizzazione di controlli semplici è spesso una risposta approfondita. Effetti semplificano questo processo, consentendo controlli nativi in ciascuna piattaforma più facile da personalizzare.

Gli effetti vengono creati nei progetti specifici della piattaforma creando sottoclassi di `PlatformEffect` controllo e quindi gli effetti vengono usati associandoli a un controllo appropriato in un progetto di libreria condivisa o una libreria .NET Standard di xamarin. Forms.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Perché usare un effetto su un Renderer personalizzato?

Effetti di semplificano la personalizzazione di un controllo, sono riutilizzabili e possono essere parametrizzati per migliorare ulteriormente il riutilizzo.

Tutto ciò che può essere ottenuta con un effetto può essere ottenuta anche con un renderer personalizzato. Tuttavia, renderer personalizzati offrono maggiore flessibilità e personalizzazione rispetto a effetti. Di seguito sono elencate le situazioni in cui è possibile scegliere un effetto su un renderer personalizzato:

- Quando si modificano le proprietà di un controllo specifico della piattaforma verrà ottenere il risultato desiderato, è consigliabile un effetto.
- Un renderer personalizzato è obbligatorio quando è necessario eseguire l'override di un controllo specifico della piattaforma.
- Un renderer personalizzato è obbligatorio quando è necessario sostituire il controllo specifiche della piattaforma che implementa un controllo di xamarin. Forms.

## <a name="subclassing-the-platformeffect-class"></a>Creazione di una sottoclasse della classe PlatformEffect

La tabella seguente elenca lo spazio dei nomi per il `PlatformEffect` classe su ogni piattaforma e i tipi delle relative proprietà:

|Piattaforma|Spazio dei nomi|Contenitore|Control|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|Visualizza|
|Piattaforma UWP (Universal Windows Platform)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Ogni specifica della piattaforma `PlatformEffect` classe espone le proprietà seguenti:

- `Container` -fa riferimento al controllo specifiche della piattaforma in uso per implementare il layout.
- `Control` -fa riferimento al controllo specifiche della piattaforma in uso per implementare il controllo di xamarin. Forms.
- `Element` -fa riferimento al controllo di xamarin. Forms che viene eseguito il rendering.

Gli effetti non è tipo di informazioni sul contenitore, controllo o elemento che a cui sono collegate in quanto essi possono essere collegati a qualsiasi elemento. Pertanto, quando un effetto è collegato a un elemento che non supporta deve adattarsi in modo o generare un'eccezione. Tuttavia, il `Container`, `Control`, e `Element` proprietà possono eseguire il cast del tipo di implementazione. Per ulteriori informazioni su questi tipi vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Ogni specifica della piattaforma `PlatformEffect` classe espone i metodi seguenti, che devono essere sottoposto a override per implementare un effetto:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) : viene chiamato quando un effetto è collegato a un controllo di xamarin. Forms. Una versione override del metodo, in ogni classe effetto platfom specifici, è possibile eseguire una personalizzazione del controllo, con la gestione delle eccezioni nel caso in cui non è possibile applicare l'effetto sul controllo specificato di xamarin. Forms.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) : viene chiamato quando un effetto non è associato a un controllo di xamarin. Forms. Una versione override del metodo, in ogni classe effetto specifico della piattaforma, è possibile eseguire la pulizia effetto, ad esempio annullando la registrazione di un gestore eventi.

Inoltre, il `PlatformEffect` espone il [ `OnElementPropertyChanged` ](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) metodo, che è anche possibile eseguire l'override. Questo metodo viene chiamato quando una proprietà dell'elemento è stato modificato. Una versione override del metodo, in ogni classe effetto specifico della piattaforma, è possibile rispondere alle modifiche di proprietà associabili del controllo di xamarin. Forms. Un controllo per la proprietà viene modificata deve essere sempre eseguito, come la sostituzione può essere chiamata più volte.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
