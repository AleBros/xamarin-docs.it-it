---
title: Introduzione a Renderscript
description: Questa guida vengono presentate Renderscript e viene illustrato come utilizzare la funzione intrinseca Renderscript dell'API in un'applicazione di xamarin tale livello destinazioni API 17 o superiore.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9e21a51c409c5444f137a63eb2c6fadfef03cbe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="an-introduction-to-renderscript"></a>Introduzione a Renderscript

_Questa guida vengono presentate Renderscript e viene illustrato come utilizzare la funzione intrinseca Renderscript dell'API in un'applicazione di xamarin tale livello destinazioni API 17 o superiore._

## <a name="overview"></a>Panoramica

Renderscript è un framework di programmazione creato da Google allo scopo di migliorare le prestazioni delle applicazioni Android che richiedono notevoli risorse di calcolo. Si tratta di un basso livello, ad alte prestazioni in base alle API [C99](http://en.wikipedia.org/wiki/C99). Poiché si tratta di un basso livello API che verranno eseguiti in DSP, CPU o GPU, Renderscript è adatta per le app Android che potrebbero essere necessario eseguire una delle seguenti:

* Grafica
* Elaborazione di immagini
* Crittografia
* Elaborazione dei segnali
* Routine matematiche

Renderscript utilizzerà `clang` e compilare gli script al codice byte LLVM che viene inserita nel file APK. Quando si esegue l'app per la prima volta, il codice byte LLVM verrà compilato nel codice macchina per i processori nel dispositivo. Questa architettura consente a un'applicazione Android di sfruttare i vantaggi del codice macchina senza che gli sviluppatori stessi necessità di scrivere per ogni processore sul dispositivo se stessi.

Esistono due componenti a una routine Renderscript:

1. **Il runtime Renderscript** &ndash; tratta le API native che sono responsabili per l'esecuzione di Renderscript. Ciò include qualsiasi Renderscripts scritti per l'applicazione.

2. **Wrapper gestiti dal Framework Android** &ndash; gestite le classi che consentono di controllare e interagire con il runtime Renderscript e script di un'app per Android. Le classi di framework fornito per il controllo runtime Renderscript la toolchain Android verrà esaminare il codice sorgente Renderscript e genera classi wrapper gestito da utilizzare per l'applicazione di Android.

Il diagramma seguente illustra la relazione tra questi componenti:

![Diagramma che illustra come il Framework Android interagisce con il Renderscript Runtime](renderscript-images/renderscript-01.png)

Esistono tre concetti importanti per l'utilizzo di Renderscripts in un'applicazione Android:

1. **Un contesto** &ndash; A managed API fornita da Android SDK che consente di allocare risorse Renderscript e consente all'app Android passare e ricevere dati dal Renderscript.

2. **A _kernel di calcolo_**  &ndash; noto anche come il _kernel radice_ o _kernel_, una routine che esegue il lavoro. Il kernel è molto simile a una funzione C. è una routine eseguibili in parallelo che viene eseguita su tutti i dati di memoria allocata.

3. **Memoria allocata** &ndash; dati sono passati da e verso un kernel tramite un  _[allocazione](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Un kernel può avere un solo input e/o un output di allocazione.

Il [Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) dello spazio dei nomi contiene le classi per l'interazione con il runtime Renderscript. In particolare, il [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) classe gestiscono il ciclo di vita e le risorse del motore di Renderscript. L'app Android deve inizializzare uno o più [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/) oggetti. Un'allocazione è un'API gestita che è responsabile dell'allocazione e la memoria è condivisa tra app Android e il runtime Renderscript di accesso. In genere, viene creata una sola allocazione per l'input e, facoltativamente, un altro allocazione viene creata per contenere l'output del kernel. Il motore di runtime Renderscript e le classi wrapper gestito associato verranno gestire l'accesso alla memoria assegnata le allocazioni, non è necessario per uno sviluppatore di app Android eseguire operazioni aggiuntive.

Un'allocazione conterrà uno o più [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Gli elementi sono un tipo specializzato che descrivono i dati di ogni allocazione.
I tipi di elementi dell'output di allocazione deve corrispondere i tipi di elemento di input. Quando si esegue, un Renderscript verrà scorrere ogni elemento nell'allocazione di input in parallelo e scrivere i risultati nell'output di allocazione. Esistono due tipi di elementi:

- **tipo semplice** &ndash; concettualmente è lo stesso come un tipo di dati C `float` o `char`.

- **tipo complesso** &ndash; questo tipo è simile a C `struct`.

Il motore Renderscript eseguirà un controllo di runtime per verificare che gli elementi in ogni allocazione siano compatibili con la procedura necessaria dal kernel. Se il tipo di dati degli elementi nell'allocazione non corrisponde al tipo di dati che prevede il kernel, verrà generata un'eccezione.

Verranno inclusi tutti Renderscript kernel da un tipo che è un discendente del [ `Android.Renderscripts.Script` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/) classe. Il `Script` classe viene utilizzata per impostare i parametri per un Renderscript, appropriata `Allocations`, ed eseguire il Renderscript. Esistono due `Script` sottoclassi di Android SDK:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Alcune delle più comuni attività Renderscript sono inclusi in Android SDK e sono accessibili da una sottoclasse di [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) classe. Non è necessario per uno sviluppatore di eseguire eventuali passaggi aggiuntivi per utilizzare questi script nella propria applicazione, come già disponibili.

- **`ScriptC_XXXXX`** &ndash; Noto anche come _script utente_, si tratta di script che vengono scritti da sviluppatori e sotto forma di file APK. In fase di compilazione, la toolchain Android genererà le classi wrapper gestito per consentono gli script da usare nell'app Android.
  Il nome di queste classi generate è il nome del file Renderscript, preceduto dal prefisso `ScriptC_`. Scrittura e l'inserimento di script dell'utente non è ufficialmente supportata da xamarin e rientra nell'ambito di questa Guida.

Tra questi due tipi, solo il `StringIntrinsic` è supportato da xamarin. Questa Guida verrà illustrato come utilizzare gli script intrinseci in un'applicazione di xamarin.

## <a name="requirements"></a>Requisiti

Questa guida è per le applicazioni di xamarin tale livello API di destinazione 17 o superiore. L'utilizzo di _script utente_ non viene descritta in questa Guida.

Il [libreria di supporto xamarin V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports l'intrinseche Renderscript dell'API per le app destinate a versioni precedenti di Android SDK. Aggiunta di questo pacchetto a un progetto xamarin deve consentire le app che destinate alle versioni precedenti di Android SDK sfruttare gli script intrinseci.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Utilizzo di Renderscripts intrinseco in xamarin

Gli script intrinseci sono un ottimo modo per eseguire attività con utilizzo intensivo di calcolo con una quantità minima di codice aggiuntivo. Sono stati ottimizzati per offrire prestazioni ottimali in una sezione trasversale elevata di dispositivi disponibili.
Non è insolito per uno script per l'esecuzione di 10 volte più veloce rispetto al codice gestito e 2-3 volte la dopo rispetto a un'implementazione personalizzata di C intrinseco. Molti degli scenari di elaborazione tipica vengono trattati dagli script intrinseco. Questo elenco degli script di intrinseche vengono descritti gli script correnti in xamarin:

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; converte RGB per RGBA utilizzando una tabella di ricerca 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh prestazioni Renderscript APIs a [BLAS](http://www.netlib.org/blas/). BLAS (base Algebra lineare sottoprogrammi) sono routine che forniscono i blocchi di compilazione standard per le operazioni di matrice e il vettore di base. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; combina due allocazioni insieme.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; si applica una sfocatura gaussiana a un'allocazione.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; una matrice di colori viene applicata un'allocazione (ad esempio modifica di colori, regolare la tonalità).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; si applica a una matrice di 3x3 colori a un'allocazione.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; riguardano un'allocazione di una matrice di 5x5 colori.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; un filtro di istogramma intrinseco.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; si applica a una tabella di ricerca per ogni canale a un buffer.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; Script per eseguire il ridimensionamento di un'allocazione 2D.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; converte un buffer YUV RGB.

Per informazioni dettagliate su ciascuno degli script di intrinseci, vedere la documentazione dell'API.

I passaggi di base per l'utilizzo di Renderscript in un'applicazione Android sono descritto di seguito.

**Creare un contesto Renderscript** &ndash; il [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) classe è un wrapper gestito per il contesto Renderscript e controllare l'inizializzazione, gestione delle risorse e pulizia. L'oggetto Renderscript viene creato utilizzando il `RenderScript.Create` metodo factory, che accetta un contesto Android (ad esempio un'attività) come parametro. La riga di codice seguente viene illustrato come inizializzare il contesto Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Creare le allocazioni** &ndash; a seconda di script intrinseco, potrebbe essere necessario creare uno o due `Allocation`s. Il [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/) classe ha diversi metodi factory per creare un'istanza di un'allocazione per una funzione intrinseca. Ad esempio, il frammento di codice seguente viene illustrato come creare l'allocazione per le bitmap.

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

**Creare un'istanza del wrapper Script** &ndash; ognuno delle classi wrapper intrinseco script deve contenere metodi di supporto (in genere chiamato `Create`) per creare un'istanza di un oggetto wrapper per lo script. Frammento di codice seguente è riportato un esempio di come creare un'istanza di un `ScriptIntrinsicBlur` sfocatura oggetto. Il `Element.U8_4` metodo di supporto verrà creato un elemento che descrive un tipo di dati è 4 campi di valori interi senza segno a 8 bit, adatti per contenere i dati di un `Bitmap` oggetto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Assegnare Allocation(s), impostare i parametri & Esegui Script** &ndash; il `Script` classe fornisce un `ForEach` metodo per eseguire effettivamente il Renderscript. Questo metodo verrà scorrere ogni `Element` nel `Allocation` che contiene i dati di input. In alcuni casi, potrebbe essere necessario fornire un `Allocation` che contiene l'output.
`ForEach` sovrascriverà il contenuto dell'output di allocazione. Per eseguire con i frammenti di codice nei passaggi precedenti, in questo esempio viene illustrato come assegnare un'allocazione di input, impostare il parametro e infine eseguire lo script (copia i risultati nell'output di allocazione):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Si potrebbe voler estrarre il [sfocatura un'immagine con Renderscript](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/) recipe, è un esempio completo di come utilizzare uno script intrinseco in xamarin.

## <a name="summary"></a>Riepilogo

Questa Guida introdotti Renderscript e come utilizzarla in un'applicazione di xamarin. È stato illustrato brevemente che cos'è Renderscript e sul relativo funzionamento in un'applicazione Android. Descritto alcuni dei componenti principali di Renderscript e la differenza tra _script utente_ e _intrinseche script_. Infine, questa guida descritti i passaggi descritti in uso uno script intrinseco in un'applicazione di xamarin.



## <a name="related-links"></a>Collegamenti correlati

- [Spazio dei nomi Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [L'effetto di un'immagine con Renderscript](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Esercitazione: Introduzione a Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
