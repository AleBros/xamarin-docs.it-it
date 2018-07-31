---
title: Selettori Objective-C in xamarin. IOS
description: Questo documento illustra come interagire con i selettori Objective-C da c#. Viene descritto come richiamare i selettori e considerazioni tecniche che devono essere presi in considerazione quando si esegue questa operazione.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/12/2017
ms.openlocfilehash: 3083770fd2874eca317585b6bf949f3efe56f879
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351730"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selettori Objective-C in xamarin. IOS

Il linguaggio di Objective-C basa *selettori*. Un selettore è un messaggio che può essere inviato a un oggetto o una *classe*. [Xamarin. IOS](~/ios/internals/api-design/index.md) mappe dei selettori a metodi di istanza dell'istanza e classe selettori ai metodi statici.

A differenza delle normali funzioni C e come le funzioni membro C++, non è possibile richiamare direttamente tramite un selettore [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
(*Riservato*: in teoria è possibile utilizzare P/Invoke per le funzioni membro di C++ non virtuale, ma è necessario preoccuparsi per ogni compilatore alterazione dei nomi, ovvero un mondo di deboli meglio ignorato.) Al contrario, i selettori vengono inviati a una classe di Objective-C o istanza utilizzando il [ `objc_msgSend` funzione](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend).

Può risultare [questa guida utile in Objective-C messaggistica](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html) utili.

<a name="Example" />

## <a name="example"></a>Esempio

Si supponga che si vuole richiamare il [-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:) selettore.
La dichiarazione (dalla documentazione di Apple) è:

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  Il tipo restituito è *CGSize* per l'API unificata.
-  Il *font* parametro è un [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (e un tipo derivato (indirettamente) da [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) ) e viene pertanto eseguito il mapping a [IntPtr](xref:System.IntPtr) .
-  Il *larghezza* parametro, un *CGFloat* , viene eseguito il mapping a *nfloat*.
-  Il *lineBreakMode* parametro, un *UILineBreakMode* , è già stato associato in xamarin. IOS come il [enumerazione UILineBreakMode](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/) .


Riunire il tutto e vogliamo una dichiarazione objc_msgSend che corrisponde a:

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

È necessario dichiararla come:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

Una volta dichiarato, è possibile richiamarlo dopo aver ottenuto i parametri appropriati:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode);
```

È una struttura che era minore di 8 byte le dimensioni di stato del valore restituito (come il precedente `SizeF` usato prima di passare alle API unificata) nel codice precedente sarebbe sono state eseguite sul simulatore ma arrestatosi in modo anomalo nel dispositivo. Pertanto, per chiamare un selettore che restituisce un valore inferiore a 8 bit nella dimensione, di conseguenza si *inoltre* necessario dichiarare il `objc_msgSend_stret()` (funzione):

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

Chiamata diventerebbe quindi:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode);
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode);
```


<a name="Invoking_a_Selector" />

## <a name="invoking-a-selector"></a>Richiamo di un selettore

Richiamo di un selettore prevede tre passaggi:

1.  Ottenere la destinazione del selettore.
1.  Ottenere il nome del selettore.
1.  Chiamare objc_msgSend() con gli argomenti appropriati.


<a name="Selector_Targets" />

### <a name="selector-targets"></a>Selettore destinazioni

Una destinazione del selettore è un'istanza dell'oggetto o una classe di Objective-C. Se la destinazione è un'istanza e proviene da un tipo associato che xamarin. IOS, usare il [ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) proprietà.

Se la destinazione è una classe, usare [ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) per ottenere un riferimento all'istanza della classe, quindi utilizzare il [Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) proprietà.


<a name="Selector_Names" />

### <a name="selector-names"></a>Nomi di selettore

I nomi di selettore sono elencati nella documentazione di Apple. Ad esempio, il [metodi di estensione UIKit NSString](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html) includono [sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:) e [sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:). Due punti finali e incorporati sono importanti e fanno parte del nome del selettore.

Dopo aver creato un nome di selettore, è possibile creare un [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) per tale istanza.


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>La chiamata a objc_msgSend()

 `objc_msgSend()` Consente di inviare un messaggio (selettore) a un oggetto. In questa serie di funzioni richiede almeno due argomenti obbligatori: la destinazione del selettore (un'istanza o una classe di gestione), il selettore di se stesso e quindi gli argomenti necessari per il selettore particolare. Gli argomenti di istanza e del selettore devono essere `System.IntPtr`, e tutti i restanti argomenti devono corrispondere al tipo previsto che il selettore, ad esempio, un' `nint` per un' `int`, o un' `System.IntPtr` per tutte le `NSObject`-i tipi derivati. Usare la [NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) proprietà per ottenere un `IntPtr` per un'istanza del tipo di Objective-C.

E non è più un `objc_msgSend()` (funzione).

Uso [ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) per i selettori che restituiscono una struttura.
Per rendere le cose "interessanti", in ARM si inclusi restituiscono tutti i tipi che sono *non* un'enumerazione o uno qualsiasi dei tipi builtin C (char, short, int, long, float, double). X86 (simulatore), questa operazione deve essere usato per tutte le strutture di dimensioni superiori a 8 byte di dimensioni. (CGSize: utilizzato nell'esempio precedente, è esattamente 8 byte e quindi non usano `objc_msgSend_stret()` nel simulatore.)

Uso [ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret) per i selettori che restituiscono mobile dei punti di valore x86 solo. Questa funzione non dovrà essere usato su ARM; Usare invece `objc_msgSend()`.

Principale [objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend) funzione viene utilizzata per tutti i selettori di altri.

Dopo aver deciso quale `objc_msgSend()` funzioni è necessario chiamare (e può essere più di uno, ad esempio, per simulatore e il dispositivo), è possibile usare una normale [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) per dichiarare la funzione per la successiva chiamata al metodo.

Un set di già pronti `objc_msgSend()` dichiarazioni sono disponibili nella [ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/).


<a name="ugly" />

## <a name="the-ugly"></a>The Ugly

Objective-C dispone di tre tipi di `objc_msgSend` metodi: uno per le chiamate normali, uno per le chiamate che restituiscono valori a virgola mobile (solo x86) e uno per le chiamate che restituiscono valori di uno struct. Quest'ultimo include il suffisso `_stret` in `ObjCRuntime.Messaging`.

Se si richiamano un metodo che restituirà determinate strutture (le regole sono descritte di seguito), è necessario richiamare il metodo con il valore restituito come primo parametro come valore out, simile al seguente:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

Le cose confusa qui e poiché la regola per quando è necessario utilizzare _stret_ è diverso su X86 e ARM, se si desidera che le associazioni a lavorarvi sia il simulatore di dispositivo, è necessario aggiungere il codice che si presenta come segue:

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>Usando il objc\_msgSend\_stret (metodo)

La regola di cui si vuole usare il [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) sono simili a questo modulo **ARM**:

-  Qualsiasi tipo che non è un'enumerazione o uno qualsiasi dei tipi di base per un'enumerazione di valore (int, byte, short, long, double, float).


La regola di cui si vuole usare il [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) sono simili a questo modulo **X86**:

-  Qualsiasi tipo che non è un'enumerazione o uno qualsiasi dei tipi di base per un'enumerazione di valore (int, byte, short, long, double, float) e le cui dimensioni native sono maggiori di 8 byte.


### <a name="creating-your-own-signatures"></a>Creare le firme.

Quanto segue [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) può essere utilizzato per creare le firme, se necessario.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio di selettori](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
