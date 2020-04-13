---
title: Introduzione a Renderscript
description: Questa guida introduce Renderscript e spiega come usare le API Renderscript intrinseche in un'applicazione Xamarin.Android destinata al livello API 17 o superiore.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019803"
---
# <a name="an-introduction-to-renderscript"></a>Introduzione a Renderscript

_Questa guida introduce Renderscript e spiega come usare le API Renderscript intrinseche in un'applicazione Xamarin.Android destinata al livello API 17 o superiore._

## <a name="overview"></a>Panoramica

Renderscript è un framework di programmazione creato da Google allo scopo di migliorare le prestazioni delle applicazioni Android che richiedono ampie risorse computazionali. Si tratta di un basso livello, API ad alte prestazioni basato su [C99](https://en.wikipedia.org/wiki/C99). Poiché si tratta di un'API di basso livello che verrà eseguita su CPU, GPU o DSP, Renderscript è adatto per le app Android che potrebbero dover eseguire una delle operazioni seguenti:

- Grafica
- Elaborazione delle immagini
- Crittografia
- Elaborazione del segnale
- Routine matematiche

Renderscript utilizzerà `clang` e compilerà gli script in codice byte LLVM che viene fornito in bundle nell'APK. Quando l'app viene eseguita per la prima volta, il codice byte LLVM verrà compilato in codice macchina per i processori nel dispositivo. Questa architettura consente a un'applicazione Android di sfruttare i vantaggi del codice macchina senza che gli sviluppatori stessi debbano scriverlo per ogni processore sul dispositivo stesso.

Esistono due componenti per una routine Renderscript:

1. **Il runtime** &ndash; Renderscript Si tratta delle API native responsabili dell'esecuzione di Renderscript. Sono inclusi tutti i Rendering scritti per l'applicazione.

2. **Wrapper gestiti dalle** &ndash; classi gestite di Android Framework che consentono a un'app Android di controllare e interagire con il runtime Renderscript e gli script. Oltre alle classi fornite dal framework per il controllo del runtime Renderscript, la toolchain Android esaminerà il codice sorgente Renderscript e genererà classi wrapper gestite per l'utilizzo da parte dell'applicazione Android.

Il diagramma seguente illustra la relazione tra questi componenti:

![Diagramma che illustra l'interazione di Android Framework con il runtime di Renderscript](renderscript-images/renderscript-01.png)

Esistono tre concetti importanti per l'utilizzo di Renderscripts in un'applicazione Android:There are three important concepts for using Renderscripts in an Android application:

1. **Un contesto** &ndash; Un'API gestita fornita dall'SDK di Android che alloca le risorse a Renderscript e consente all'app Android di passare e ricevere dati da Renderscript.

2. Un &ndash; kernel di ** _calcolo_ ** Noto anche come _il kernel radice_ o _kernel_, si tratta di una routine che esegue il lavoro. Il kernel è molto simile a una funzione C; è una routine parallelizzabile che verrà eseguita su tutti i dati nella memoria allocata.

3. I dati di &ndash; **memoria allocati** vengono passati da e verso un kernel tramite un'allocazione . _[Allocation](xref:Android.Renderscripts.Allocation)_ Un kernel può avere un input e/o un'allocazione di output.

Lo spazio [dei nomi Android.Renderscripts](xref:Android.Renderscripts) contiene le classi per l'interazione con il runtime Renderscript.The Android.Renderscripts namespace contains the classes for interacting with the Renderscript runtime. In particolare, [`Renderscript`](xref:Android.Renderscripts.RenderScript) la classe gestirà il ciclo di vita e le risorse del motore Renderscript. L'app Android deve inizializzare uno o più[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
. Un'allocazione è un'API gestita responsabile dell'allocazione e dell'accesso alla memoria condivisa tra l'app Android e il runtime Renderscript.An Allocation is a managed API that is responsible for allocation and accessing the memory that is shared between the Android app and the Renderscript runtime. In genere, viene creata un'allocazione per l'input e, facoltativamente, un'altra allocazione viene creata per contenere l'output del kernel. Il motore di runtime Renderscript e le classi wrapper gestite associate gestiranno l'accesso alla memoria contenuta nelle allocazioni, non è necessario che uno sviluppatore di app Android esegua operazioni aggiuntive.

Un'allocazione conterrà uno o più [file Android.Renderscripts.Elements](xref:Android.Renderscripts.Element).
Gli elementi sono un tipo specializzato che descrive i dati in ogni allocazione.
I tipi di elemento dell'allocazione di output devono corrispondere ai tipi dell'elemento di input. Durante l'esecuzione, un Renderscript eseguirà l'iterazione di ogni elemento nell'allocazione di input in parallelo e scriverà i risultati nell'allocazione di output. Esistono due tipi di elementi:

- **simple type** &ndash; Concettualmente si tratta dello stesso `float` tipo `char`di dati C o di un oggetto .

- **tipo** &ndash; complesso Questo tipo `struct`è simile a un c .

Il motore Renderscript eseguirà un controllo di runtime per garantire che gli elementi in ogni allocazione siano compatibili con quanto richiesto dal kernel. Se il tipo di dati degli elementi nell'allocazione non corrisponde al tipo di dati previsto dal kernel, verrà generata un'eccezione.

Tutti i kernel Renderscript verranno incapsulati da un tipo che è un discendente[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
. La `Script` classe viene utilizzata per impostare i `Allocations`parametri per un oggetto Renderscript, impostare l'oggetto appropriato ed eseguire Renderscript. Esistono due `Script` sottoclassi in Android SDK:

- **`Android.Renderscripts.ScriptIntrinsic`**&ndash; Alcune delle attività Renderscript più comuni sono in bundle in Android SDK e sono accessibili da una sottoclasse della classe [ScriptIntrinsic.](xref:Android.Renderscripts.ScriptIntrinsic) Non è necessario che uno sviluppatore esercitina alcuna procedura aggiuntiva per utilizzare questi script nell'applicazione in quanto sono già forniti.

- **`ScriptC_XXXXX`**&ndash; Conosciuti anche come _script utente_, si tratta di script scritti dagli sviluppatori e inclusi nel pacchetto APK. In fase di compilazione, la toolchain Android genererà classi wrapper gestite che consentiranno agli script di essere utilizzati nell'app Android.
  Il nome di queste classi generate è il nome `ScriptC_`del file Renderscript, preceduto da . La scrittura e l'incorporazione di script utente non è ufficialmente supportata da Xamarin.Android e oltre l'ambito di questa guida.

Di questi due tipi, solo il `StringIntrinsic` è supportato da Xamarin.Android. Questa guida verrà illustrato come utilizzare gli script intrinseci in un'applicazione Xamarin.Android.This guide will discuss how to use intrinsic scripts in a Xamarin.Android application.

## <a name="requirements"></a>Requisiti

Questa guida è per le applicazioni Xamarin.Android destinate al livello API 17 o superiore. L'uso degli _script utente_ non è trattato in questa guida.

La libreria di supporto [Xamarin.Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) esegue il backport delle API Renderscript instrinsic per le app destinate alle versioni precedenti dell'SDK di Android. L'aggiunta di questo pacchetto a un progetto Xamarin.Android deve consentire alle app destinate alle versioni precedenti di Android SDK di sfruttare gli script intrinseci.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Utilizzo di rendering intrinseci in Xamarin.AndroidUsing Intrinsic Renderscripts in Xamarin.Android

Gli script intrinseci sono un ottimo modo per eseguire attività di elaborazione intensive con una quantità minima di codice aggiuntivo. Sono stati sintonizzati a mano per offrire prestazioni ottimali su una grande sezione trasversale di dispositivi.
Non è raro che uno script intrinseco esegua 10 volte più velocemente del codice gestito e 2-3 volte dopo rispetto a un'implementazione C personalizzata. Molti degli scenari di elaborazione tipici sono coperti dagli script intrinseci. Questo elenco degli script intrinseci descrive gli script correnti in Xamarin.Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; Converte RGB in RGBA utilizzando una tabella di ricerca 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Fornisce API Renderscript ad alte prestazioni a [BLAS](http://www.netlib.org/blas/). Il BLAS (Basic Linear Algebra Subprograms) sono routine che forniscono blocchi predefiniti standard per l'esecuzione di operazioni di base vettoriali e matriciali. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; unisce due allocazioni.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; Applica una sfocatura gaussiana a un'allocazione.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; Applica una matrice di colori a un'allocazione (ad esempio, modificare i colori, regolare la tonalità).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; Applica una matrice di colori 3x3 a un'allocazione.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; Applica una matrice di colori 5x5 a un'allocazione.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; Un filtro dell'istogramma intrinseco.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; Applica una tabella di ricerca per canale a un buffer.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; Script per eseguire il ridimensionamento di un'allocazione 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; Converte un buffer YUV in RGB.

Consultare la documentazione dell'API per informazioni dettagliate su ciascuno degli script intrinseci.

I passaggi di base per l'utilizzo di Renderscript in un'applicazione Android sono descritti di seguito.

**Creare un contesto** &ndash; Renderscript[`Renderscript`](xref:Android.Renderscripts.RenderScript)
class è un wrapper gestito intorno al contesto Renderscript e controllerà l'inizializzazione, la gestione delle risorse e la pulizia. Il Renderscript oggetto viene `RenderScript.Create` creato utilizzando il metodo factory, che accetta un contesto Android (ad esempio un'attività) come parametro. La seguente riga di codice illustra come inizializzare il contesto Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Creare allocazioni** &ndash; A seconda dello script intrinseco, potrebbe `Allocation`essere necessario creare uno o due s. Le[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
classe dispone di diversi metodi factory per facilitare la creazione di un'istanza di un'allocazione per un intrinseco. Ad esempio, il frammento di codice seguente illustra come creare allocazione per le bitmap.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Spesso, sarà necessario creare `Allocation` un per contenere i dati di output di uno script. Questo frammento di codice `Allocation.CreateTyped` seguente viene `Allocation` illustrato come utilizzare l'helper per creare un secondo che lo stesso tipo dell'originale:This following snippet shows how to use the helper to instantiate a second that the same type as the original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Creare un'istanza del wrapper** &ndash; di script Ogni classe `Create`wrapper di script intrinseca deve disporre di metodi di supporto (in genere chiamati ) per creare un'istanza di un oggetto wrapper per tale script. Il frammento di codice seguente è `ScriptIntrinsicBlur` un esempio di come creare un'istanza di un oggetto blur. Il `Element.U8_4` metodo helper creerà un element che descrive un tipo di dati che è 4 campi di `Bitmap` 8 bit, valori integer senza segno, adatto per contenere i dati di un oggetto:The helper method will create an Element that describes a data type that is 4 fields of 8-bit, unsigned integer values, suitable for holding the data of a object:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Assegna allocazioni, Imposta parametri & Esegui script** &ndash; La `Script` classe fornisce un `ForEach` metodo per eseguire effettivamente renderscript. Questo metodo eseguirà `Element` l'iterazione su ciascuno di `Allocation` essi nei dati di input. In alcuni casi, potrebbe essere `Allocation` necessario fornire un che contiene l'output.
`ForEach`sovrascriverà il contenuto dell'Allocazione dell'output. Per continuare con i frammenti di codice dei passaggi precedenti, in questo esempio viene illustrato come assegnare un'allocazione di input, impostare un parametro e quindi eseguire infine lo script (copiando i risultati nell'allocazione di output):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Si consiglia di controllare la [sfocatura di un'immagine con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) ricetta, è un esempio completo di come utilizzare uno script intrinseco in Xamarin.Android.

## <a name="summary"></a>Riepilogo

Questa guida ha introdotto Renderscript e come usarlo in un'applicazione Xamarin.Android.This guide introduced Renderscript and how to use it in a Xamarin.Android application. Ha brevemente discusso che cosa è Renderscript e come funziona in un'applicazione Android. Sono stati descritti alcuni dei componenti chiave di Renderscript e la differenza tra _script utente_ e _script instrinsici._ Infine, questa guida ha illustrato i passaggi per l'utilizzo di uno script intrinseco in un'applicazione Xamarin.Android.Finally, this guide discussed the steps in using an intrinsic script in a Xamarin.Android application.

## <a name="related-links"></a>Collegamenti correlati

- [Spazio dei nomi Android.Renderscripts](xref:Android.Renderscripts)
- [Sfocare un'immagine con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Esercitazione: Introduzione a RenderscriptTutorial: Getting Started with Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
