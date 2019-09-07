---
title: Introduzione a renderScript
description: Questa guida introduce renderScript e illustra come usare le API renderScript intrinseche in un'applicazione Novell. Android destinata al livello API 17 o versione successiva.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: fee63906d30f68d397b60a465a87bd96d4106d14
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757427"
---
# <a name="an-introduction-to-renderscript"></a>Introduzione a renderScript

_Questa guida introduce renderScript e illustra come usare le API renderScript intrinseche in un'applicazione Novell. Android destinata al livello API 17 o versione successiva._

## <a name="overview"></a>Panoramica

RenderScript è un Framework di programmazione creato da Google allo scopo di migliorare le prestazioni delle applicazioni Android che richiedono risorse di calcolo estese. Si tratta di un'API ad alte prestazioni di basso livello basata su [C99](https://en.wikipedia.org/wiki/C99). Poiché si tratta di un'API di basso livello che verrà eseguita su CPU, GPU o DSP, renderScript è particolarmente adatto per le app Android che potrebbero dover eseguire una delle operazioni seguenti:

- Grafica
- Elaborazione di immagini
- Crittografia
- Elaborazione dei segnali
- Routine matematiche

RenderScript utilizzerà `clang` e compilerà gli script nel codice byte LLVM, incluso nell'apk. Quando l'app viene eseguita per la prima volta, il codice byte LLVM verrà compilato nel codice del computer per i processori del dispositivo. Questa architettura consente a un'applicazione Android di sfruttare i vantaggi del codice del computer senza che gli sviluppatori debbano scriverlo per ogni processore sul dispositivo stesso.

Sono disponibili due componenti per una routine renderScript:

1. **Runtime renderScript** &ndash; Si tratta delle API native che sono responsabili dell'esecuzione del renderScript. Sono inclusi tutti i Renderscripts scritti per l'applicazione.

2. **Wrapper gestiti dal Framework Android** &ndash; Classi gestite che consentono a un'app Android di controllare e interagire con il runtime e gli script di renderScript. Oltre alle classi fornite dal Framework per il controllo del runtime di renderScript, la gestione dei dispositivi Android esamina il codice sorgente renderScript e genera le classi wrapper gestite per l'uso da parte dell'applicazione Android.

Il diagramma seguente illustra il modo in cui questi componenti sono correlati:

![Diagramma che illustra il modo in cui il Framework Android interagisce con il runtime renderScript](renderscript-images/renderscript-01.png)

Esistono tre concetti importanti per l'uso di Renderscripts in un'applicazione Android:

1. **Un contesto** &ndash; API gestita fornita dal Android SDK che alloca risorse a renderScript e consente all'app Android di passare e ricevere dati da renderScript.

2. **Un _kernel di calcolo_**  Noto anche come kernel _radice_ o kernel, è una routine che esegue le operazioni. &ndash; Il kernel è molto simile a una funzione C; si tratta di una routine eseguibili che verrà eseguita su tutti i dati nella memoria allocata.

3. **Memoria allocata** &ndash; I dati vengono passati a e da un kernel tramite un' _[allocazione](xref:Android.Renderscripts.Allocation)_ . Un kernel può avere un input e/o un'allocazione di output.

Lo spazio dei nomi [Android. Renderscripts](xref:Android.Renderscripts) contiene le classi per l'interazione con il runtime di renderScript. In particolare, la [`Renderscript`](xref:Android.Renderscripts.RenderScript) classe gestirà il ciclo di vita e le risorse del motore renderScript. L'app Android deve inizializzare una o più[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
oggetti. Un'allocazione è un'API gestita responsabile dell'allocazione e dell'accesso alla memoria condivisa tra l'app Android e il runtime renderScript. Viene in genere creata un'allocazione per l'input e, facoltativamente, viene creata un'altra allocazione per conservare l'output del kernel. Il motore di runtime di renderScript e le classi wrapper gestite associate gestiranno l'accesso alla memoria utilizzata dalle allocazioni. non è necessario che uno sviluppatore di app Android esegua operazioni aggiuntive.

Un'allocazione conterrà uno o più [Android. Renderscripts. Elements](xref:Android.Renderscripts.Element).
Gli elementi sono un tipo specializzato che descrive i dati in ogni allocazione.
I tipi di elemento dell'allocazione di output devono corrispondere ai tipi dell'elemento input. Quando si esegue, un renderScript scorre ogni elemento nell'allocazione di input in parallelo e scrive i risultati nell'allocazione di output. Esistono due tipi di elementi:

- **tipo semplice** Concettualmente è uguale a un `float` tipo di dati C o a `char`. &ndash;

- **tipo complesso** Questo tipo è simile a C `struct`. &ndash;

Il motore renderScript eseguirà un controllo del runtime per assicurarsi che gli elementi in ogni allocazione siano compatibili con quelli richiesti dal kernel. Se il tipo di dati degli elementi nell'allocazione non corrisponde al tipo di dati previsto dal kernel, verrà generata un'eccezione.

Tutti i kernel renderScript verranno inclusi in un tipo discendente del[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
. La `Script` classe viene utilizzata per impostare i parametri per un renderScript, impostare l' `Allocations`oggetto appropriato ed eseguire renderScript. Nel Android SDK sono `Script` presenti due sottoclassi:

- **`Android.Renderscripts.ScriptIntrinsic`** Alcune delle attività renderScript più comuni sono aggregate nel Android SDK e sono accessibili da una sottoclasse della classe [ScriptIntrinsic.](xref:Android.Renderscripts.ScriptIntrinsic) &ndash; Non è necessario che uno sviluppatore prenda ulteriori passaggi per usare questi script nell'applicazione, perché sono già disponibili.

- **`ScriptC_XXXXX`** Noti anche come _script utente_, si tratta di script scritti dagli sviluppatori e inclusi nel pacchetto APK. &ndash; In fase di compilazione, la gestione dei dispositivi Android genera classi wrapper gestite che consentiranno di usare gli script nell'app Android.
  Il nome di queste classi generate è il nome del file renderScript, con `ScriptC_`prefisso. La scrittura e l'incorporamento di script utente non è ufficialmente supportata da Novell. Android e non rientra nell'ambito di questa guida.

Di questi due tipi, solo `StringIntrinsic` è supportato da Novell. Android. Questa guida illustra come usare gli script intrinseci in un'applicazione Novell. Android.

## <a name="requirements"></a>Requisiti

Questa guida è destinata alle applicazioni Novell. Android destinate a livello API 17 o superiore. L'uso di _script utente_ non è trattato in questa guida.

La [libreria di supporto Novell. Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports API intrinseche renderScript per le app destinate a versioni precedenti del Android SDK. L'aggiunta di questo pacchetto a un progetto Novell. Android dovrebbe consentire alle app destinate a versioni precedenti del Android SDK di sfruttare gli script intrinseci.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Uso di Renderscripts intrinseci in Novell. Android

Gli script intrinseci sono un ottimo modo per eseguire attività di elaborazione complesse con una quantità minima di codice aggiuntivo. Sono state ottimizzate per offrire prestazioni ottimali in un'ampia sezione di dispositivi.
Non è insolito che uno script intrinseco esegua 10x più velocemente del codice gestito e 2-3x volte dopo un'implementazione di C personalizzata. Molti degli scenari di elaborazione tipici sono trattati dagli script intrinseci. Questo elenco degli script intrinseci descrive gli script correnti in Novell. Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; Converte RGB in RGBA usando una tabella di ricerca 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) Provideshigh performance renderScript API to [Blas.](http://www.netlib.org/blas/) &ndash; I sottoprogrammi BLAS (Basic Linear Algebra Subprograms) sono routine che forniscono blocchi predefiniti standard per l'esecuzione di operazioni di base su vettori e matrici. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; Unisce due allocazioni.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; Applica una sfocatura gaussiana a un'allocazione.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; Applica una matrice di colori a un'allocazione (ad esempio, modifica colori, regolazione tonalità).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; Applica una matrice di colori 3x3 a un'allocazione.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; Applica una matrice di colori 5x5 a un'allocazione.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; Filtro istogramma intrinseco.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; Applica una tabella di ricerca per canale a un buffer.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; Script per l'esecuzione del ridimensionamento di un'allocazione 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; Converte un buffer YUV in RGB.

Per informazioni dettagliate su ognuno degli script intrinseci, vedere la documentazione dell'API.

I passaggi di base per l'uso di renderScript in un'applicazione Android sono descritti di seguito.

**Creare un contesto renderScript** &ndash; Il[`Renderscript`](xref:Android.Renderscripts.RenderScript)
la classe è un wrapper gestito intorno al contesto renderScript, che controllerà l'inizializzazione, la gestione delle risorse e la pulizia. L'oggetto renderScript viene creato usando il `RenderScript.Create` Metodo Factory, che accetta un contesto Android, ad esempio un'attività, come parametro. Nella riga di codice seguente viene illustrato come inizializzare il contesto renderScript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Creazione di allocazioni** A seconda dello script intrinseco, potrebbe essere necessario crearne uno o due `Allocation`. &ndash; Il[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
la classe dispone di diversi metodi factory per semplificare la creazione di un'allocazione per un intrinseco. Ad esempio, il frammento di codice seguente illustra come creare l'allocazione per le bitmap.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Spesso sarà necessario creare un oggetto `Allocation` per contenere i dati di output di uno script. Il frammento di codice seguente mostra come `Allocation.CreateTyped` usare l'helper per creare un'istanza `Allocation` di un secondo che lo stesso tipo dell'originale:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Creare un'istanza del wrapper di script** Ogni classe wrapper di script intrinseco deve avere metodi helper (in genere chiamati `Create`) per creare un'istanza di un oggetto wrapper per lo script. &ndash; Il frammento di codice seguente è un esempio di come creare un' `ScriptIntrinsicBlur` istanza di un oggetto sfocato. Il `Element.U8_4` metodo helper creerà un elemento che descrive un tipo di dati costituito da 4 campi a 8 bit, Unsigned Integer valori, adatti per contenere i dati di un `Bitmap` oggetto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Assegna allocazioni, imposta parametri, & Esegui script** La classe fornisce un`ForEach` metodo per eseguire effettivamente renderScript. `Script` &ndash; Questo metodo esegue l'iterazione `Element` di ogni `Allocation` oggetto nell'oggetto che contiene i dati di input. In alcuni casi, potrebbe essere necessario fornire un oggetto `Allocation` che contenga l'output.
`ForEach`sovrascriverà il contenuto dell'allocazione di output. Per eseguire i frammenti di codice dei passaggi precedenti, in questo esempio viene illustrato come assegnare un'allocazione di input, impostare un parametro e infine eseguire lo script (copiando i risultati nell'allocazione di output):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

È possibile estrarre l' [immagine sfocata con](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) la ricetta renderScript. si tratta di un esempio completo di come usare uno script intrinseco in Novell. Android.

## <a name="summary"></a>Riepilogo

Questa guida ha presentato renderScript e come usarla in un'applicazione Novell. Android. Viene brevemente descritto il renderScript e il funzionamento in un'applicazione Android. Sono stati descritti alcuni componenti chiave di renderScript e la differenza tra gli _script utente_ e gli _script intrinseche_. Infine, in questa guida sono stati illustrati i passaggi per l'uso di uno script intrinseco in un'applicazione Novell. Android.

## <a name="related-links"></a>Collegamenti correlati

- [Spazio dei nomi Android. Renderscripts](xref:Android.Renderscripts)
- [Sfocare un'immagine con renderScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Esercitazione: Introduzione con renderScript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
