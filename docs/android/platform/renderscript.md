---
title: Un'introduzione a Renderscript
description: Questa guida presenta Renderscript e spiega come usare la funzione intrinseca Renderscript dell'API in un'applicazione xamarin. Android tale livello destinazioni API 17 o successiva.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5369542552a41100443c5e91ceca9e110c5c7c3c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108730"
---
# <a name="an-introduction-to-renderscript"></a>Un'introduzione a Renderscript

_Questa guida presenta Renderscript e spiega come usare la funzione intrinseca Renderscript dell'API in un'applicazione xamarin. Android tale livello destinazioni API 17 o successiva._

## <a name="overview"></a>Panoramica

Renderscript è un framework di programmazione creato da Google allo scopo di migliorare le prestazioni delle applicazioni Android che richiedono notevoli risorse di calcolo. Si tratta di un basso livello e ad alte prestazioni basata su API [C99](http://en.wikipedia.org/wiki/C99). Poiché si tratta di un basso livello API che verrà eseguito su CPU, GPU o dai DSP, Renderscript è particolarmente adatto per le app Android che potrebbe essere necessario eseguire nessuna delle operazioni seguenti:

* Grafica
* Elaborazione di immagini
* Crittografia
* Elaborazione dei segnali
* Routine matematiche

Renderscript userà `clang` e compilare gli script di codice byte LLVM, che viene incluso in file APK. Quando si esegue l'app per la prima volta, il codice byte LLVM verrà compilato in codice macchina per i processori nel dispositivo. Questa architettura consente a un'applicazione Android di sfruttare i vantaggi del codice macchina senza che gli sviluppatori stessi necessità di scrivere per ogni processore sul dispositivo se stessi.

Esistono due componenti a una routine Renderscript:

1. **Il runtime Renderscript** &ndash; tratta le API native che sono responsabili dell'esecuzione di Renderscript. Ciò include qualsiasi Renderscripts scritti per l'applicazione.

2. **Wrapper gestiti dal Framework di Android** &ndash; gestiti classi che consentono a un'app Android controllare e interagire con il runtime Renderscript e script. Oltre alle classi di framework fornito per il controllo runtime Renderscript, la toolchain Android verrà esaminare il codice sorgente Renderscript e genera classi wrapper gestite da utilizzare per l'applicazione Android.

Il diagramma seguente illustra la relazione tra questi componenti:

![Diagramma che illustra come il Framework Android interagisce con il Renderscript Runtime](renderscript-images/renderscript-01.png)

Esistono tre concetti importanti per l'uso Renderscripts in un'applicazione Android:

1. **Un contesto** &ndash; gestito fornito da Android SDK che alloca le risorse a Renderscript e consente all'app Android passare e ricevere dati dal Renderscript API.

2. **Oggetto _kernel di calcolo_**  &ndash; noto anche come il _kernel radice_ oppure _kernel_, questo una routine che esegue il lavoro. Il kernel è molto simile a una funzione C; è una routine parallelizzabile che verrà eseguita su tutti i dati in memoria allocata.

3. **Memoria allocata** &ndash; dei dati sono passati da e verso un kernel attraverso un  _[allocazione](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Un kernel può avere un solo input e/o un output di allocazione.

Il [Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) dello spazio dei nomi contiene le classi per l'interazione con il runtime Renderscript. In particolare, il [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) classe gestiranno le risorse del motore di Renderscript e ciclo di vita. L'app Android deve inizializzare uno o più [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
Oggetti. Un'allocazione è un'API gestita che è responsabile dell'allocazione e accedere alla memoria che verrà condivisi tra app Android e il runtime Renderscript. In genere, viene creata un'allocazione per l'input e, facoltativamente, un'altra allocazione viene creato per contenere l'output del kernel. Il motore di runtime Renderscript e le classi wrapper gestito associato verranno gestire l'accesso per la memoria utilizzata dalle allocazioni, non è necessario per gli sviluppatori di app Android eseguire operazioni aggiuntive.

Un'allocazione conterrà una o più [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Gli elementi sono un tipo specializzato che descrivono i dati in ogni allocazione.
I tipi di elemento dell'output allocazione deve corrispondere ai tipi di elemento di input. Durante l'esecuzione, un Renderscript sarà l'iterazione di ogni elemento nell'allocazione di input in parallelo e scrivere i risultati nell'output di allocazione. Esistono due tipi di elementi:

- **tipo semplice** &ndash; concettualmente è lo stesso come un tipo di dati C `float` o un `char`.

- **tipo complesso** &ndash; questo tipo è simile a una C `struct`.

Il motore Renderscript eseguirà un controllo di runtime per garantire che gli elementi in ogni allocazione siano compatibili con quelle richieste dal kernel. Se il tipo di dati degli elementi nell'allocazione non corrisponde al tipo di dati che prevede il kernel, verrà generata un'eccezione.

Tutti i kernel Renderscript verranno racchiusi in un tipo che è un discendente del [`Android.Renderscripts.Script`](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)
. Il `Script` classe viene utilizzata per impostare i parametri per un Renderscript, impostare appropriato `Allocations`, ed eseguire il Renderscript. Esistono due `Script` sottoclassi in Android SDK:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Alcune delle attività più comuni Renderscript aggregati in Android SDK e sono accessibili da una sottoclasse di [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) classe. Non è necessario per uno sviluppatore di eseguire eventuali passaggi aggiuntivi per utilizzare questi script nella propria applicazione, perché sono già fornite.

- **`ScriptC_XXXXX`** &ndash; Noto anche come _script utente_, questi sono gli script che vengono scritti dagli sviluppatori e compresso nell'APK. In fase di compilazione la toolchain Android genera classi wrapper gestite che consentono di script da usare nell'app Android.
  Il nome di queste classi generate è il nome del file Renderscript, con preceduto `ScriptC_`. La scrittura e l'inserimento di script dell'utente non è ufficialmente supportato da xamarin. Android e rientra nell'ambito di questa Guida.

Di questi due tipi, solo il `StringIntrinsic` è supportata da xamarin. Android. Questa guida illustra come usare gli script intrinseci in un'applicazione xamarin. Android.

## <a name="requirements"></a>Requisiti

Questa guida è per le applicazioni xamarin. Android tale livello API di destinazione 17 o successiva. L'uso di _script utente_ non trattate in questa Guida.

Il [libreria di supporto di xamarin. Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports l'intrinseche Renderscript dell'API per le app destinate alle versioni precedenti di Android SDK. Aggiunta di questo pacchetto a un progetto xamarin. Android debba consentire alle App destinati a versioni precedenti di Android SDK per sfruttare gli script intrinseci.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Uso di Renderscripts intrinseco in xamarin. Android

Gli script intrinseci sono un ottimo modo per eseguire attività con utilizzo intensivo di calcolo con una quantità minima di codice aggiuntivo. Sono stati ottimizzati per offrire prestazioni ottimali su una sezione trasversale elevata di dispositivi disponibili.
Non è insolito per uno script intrinseco eseguire 10 volte più veloci rispetto al codice gestito e 2 e 3 le ore di x dopo rispetto a un'implementazione personalizzata di C. Molti degli scenari di elaborazione tipica vengono analizzati gli script intrinseci. Questo elenco degli script di intrinseche vengono descritti gli script correnti in xamarin. Android:

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; converte RGB per RGBA usando una tabella di ricerca 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh Renderscript APIs ad alte prestazioni per [BLAS](http://www.netlib.org/blas/). BLAS (Basic Linear Algebra sottoprogrammi) sono routine che forniscono i blocchi di compilazione standard per l'esecuzione di operazioni della matrice e il vettore di base. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; fonde insieme due allocazioni.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; applica una sfocatura gaussiana un'allocazione.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; una matrice di colori viene applicata un'allocazione (vale a dire modificare colori, regolare hue).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; una matrice di 3x3 colori viene applicata un'allocazione.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; una matrice di 5x5 colori viene applicata un'allocazione.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; un filtro intrinseche istogramma.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; si applica a una tabella di ricerca per ogni canale a un buffer.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; Script per eseguire il ridimensionamento di un'allocazione 2D.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; converte un buffer YUV in RGB.

Per informazioni dettagliate su ogni script intrinseci, vedere la documentazione dell'API.

I passaggi di base per l'uso Renderscript in un'applicazione Android sono descritto di seguito.

**Creare un contesto Renderscript** &ndash; il [`Renderscript`](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)
classe è un wrapper gestito per il contesto Renderscript e controllano l'inizializzazione, gestione delle risorse e la pulizia. L'oggetto Renderscript viene creato utilizzando il `RenderScript.Create` un metodo factory, che accetta un contesto Android (ad esempio, un'attività) come parametro. La riga di codice seguente viene illustrato come inizializzare il contesto Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Creare le allocazioni** &ndash; a seconda lo script intrinseco, potrebbe essere necessario creare uno o due `Allocation`s. Il [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
classe ha diversi metodi factory per facilitare la creazione di un'allocazione per un intrinseco. Ad esempio, il frammento di codice seguente viene illustrato come creare allocazione per le immagini bitmap.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Spesso, sarà necessario creare un `Allocation` per contenere i dati di output di uno script. Questo frammento di codice seguente viene illustrato come utilizzare il `Allocation.CreateTyped` helper per creare un'istanza di un secondo `Allocation` che lo stesso tipo dell'originale:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Creare un'istanza del wrapper di Script** &ndash; ognuna delle classi wrapper intrinseco script deve avere i metodi helper (in genere chiamato `Create`) per creare un'istanza di un oggetto wrapper per lo script. Il frammento di codice seguente è un esempio di come creare un'istanza di un `ScriptIntrinsicBlur` blur oggetto. Il `Element.U8_4` metodo di supporto verrà creato un elemento che descrive un tipo di dati che è 4 campi di valori integer senza segno a 8 bit, adatti per contenere i dati di un `Bitmap` oggetto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Assegnare Allocation(s), impostare i parametri, Esegui Script** &ndash; le `Script` classe fornisce un `ForEach` metodo per eseguire effettivamente il Renderscript. Questo metodo scorre ognuno `Element` nella `Allocation` che contiene i dati di input. In alcuni casi, potrebbe essere necessario fornire un `Allocation` che contiene l'output.
`ForEach` sovrascriverà il contenuto dell'output di allocazione. Per il trasporto di con i frammenti di codice nei passaggi precedenti, questo esempio illustra come assegnare un'allocazione di input, impostare un parametro e infine eseguire lo script (copia i risultati nell'output di allocazione):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Si consiglia di consultare il [Blur un'immagine con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) ricetta, è un esempio completo di come usare uno script intrinseco in xamarin. Android.

## <a name="summary"></a>Riepilogo

Questa guida ha presentato Renderscript e come usarlo in un'applicazione xamarin. Android. È stato illustrato brevemente Renderscript What ' s e il funzionamento in un'applicazione Android. Descrive alcuni dei componenti principali di Renderscript e la differenza tra _script utente_ e _intrinseche script_. Infine, questa guida ha illustrato la procedura descritta in tramite uno script intrinseco in un'applicazione xamarin. Android.



## <a name="related-links"></a>Collegamenti correlati

- [Spazio dei nomi Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [L'effetto di un'immagine con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Esercitazione: Introduzione a Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
