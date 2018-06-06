---
title: Selettori di Objective-C in xamarin. IOS
description: Questo documento viene illustrato come interagire con i selettori di Objective-C da c#. Viene descritto come richiamare i selettori e considerazioni tecniche che devono essere presi in considerazione quando si esegue questa operazione.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 25276851879ba828361d3236cbf7896cf748588c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787042"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selettori di Objective-C in xamarin. IOS

Si basa la lingua Objective-C *selettori*. Un selettore è un messaggio che può essere inviato a un oggetto o un *classe*. [Xamarin](~/ios/internals/api-design/index.md) i selettori per metodi di istanza dell'istanza, mappe e i selettori per metodi statici di classe.

A differenza delle normale funzioni C e come funzioni membro C++, non è possibile richiamare direttamente un selettore utilizzando [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
(*Riservato*: in teoria è possibile usare P/Invoke per le funzioni di membro non virtuale C++, ma è necessario preoccuparsi al compilatore alterazione dei nomi, ovvero un mondo di deboli meglio ignorato.) Al contrario, i selettori vengono inviati a una classe Objective-C o istanza utilizzando il [ `objc_msgSend` funzione](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend).

È possibile [questa guida utile sulla messaggistica Objective-C](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html) utile.

<a name="Example" />

## <a name="example"></a>Esempio

Si supponga che si desidera richiamare il [-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:) selettore.
La dichiarazione (dalla documentazione di Apple) è:

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  Il tipo restituito è *CGSize* per l'API unificata.
-  Il *carattere* parametro è un [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (e un tipo derivato (indirettamente) da [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) ) e viene pertanto eseguito il mapping a [IntPtr](https://developer.xamarin.com/api/type/System.IntPtr/) .
-  Il *larghezza* parametro, un *CGFloat* , viene eseguito il mapping a *nfloat*.
-  Il *lineBreakMode* parametro, un *UILineBreakMode* , è già stato associato in xamarin. IOS come il [UILineBreakMode enumerazione](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/) .


In conclusione e si vuole che una dichiarazione di objc_msgSend corrispondente:

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

È necessario eseguirne la dichiarazione:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

Una volta dichiarato, è possibile richiamare una volta che i parametri appropriati:

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

Il valore restituito fosse una struttura che è minore di 8 byte (come il precedente `SizeF` utilizzato prima di passare alle API unificata) eseguite nel codice sopra il simulatore ma bloccato nel dispositivo. Pertanto, per chiamare un selettore che restituisce un valore inferiore a 8 bit nella dimensione, di conseguenza è *anche* è necessario dichiarare il `objc_msgSend_stret()` funzione:

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

Il richiamo di un selettore ha tre passaggi:

1.  Ottenere la destinazione del selettore.
1.  Ottenere il nome del selettore.
1.  Chiamare objc_msgSend() con gli argomenti appropriati.


<a name="Selector_Targets" />

### <a name="selector-targets"></a>Selettore destinazioni

Una destinazione selettore è un'istanza dell'oggetto o una classe Objective-C. Se la destinazione è un'istanza e proviene da un tipo di xamarin. IOS associato, utilizzare il [ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) proprietà.

Se la destinazione è una classe, utilizzare [ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) per ottenere un riferimento all'istanza della classe, quindi utilizzare il [Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) proprietà.


<a name="Selector_Names" />

### <a name="selector-names"></a>Nomi di selettore

Vengono elencati i nomi di selettore nella documentazione di Apple. Ad esempio, il [metodi di estensione UIKit NSString](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html) includono [sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:) e [sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:). Due punti finali e incorporati sono importanti e fanno parte del nome del selettore.

Dopo aver creato un nome di selettore, è possibile creare un [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) istanza relativa.


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>La chiamata objc_msgSend()

 `objc_msgSend()` viene utilizzato per inviare un messaggio (selettore) a un oggetto. Questa famiglia di funzioni richiede almeno due argomenti obbligatori: la destinazione di selettore (un'istanza o una classe di gestione), il selettore di se stesso e quindi gli argomenti necessari per il selettore particolare. Gli argomenti di istanza e selettore devono essere `System.IntPtr`, e tutti i restanti argomenti devono corrispondere al tipo previsto il selettore, ad esempio un `nint` per un `int`, o un `System.IntPtr` per tutti i `NSObject`-tipi derivati. Utilizzare il [NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) proprietà per ottenere un `IntPtr` per un'istanza di tipo Objective-C.

E non è più `objc_msgSend()` (funzione).

Utilizzare [ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) per i selettori che restituiscono una struttura.
Per semplificare le operazioni "interessante", su architetture ARM questo include restituiscono tutti i tipi che sono *non* un'enumerazione o uno dei tipi incorporati di C (char, short, int, long, float, double). X86 (simulatore), questa operazione deve essere utilizzato per tutte le strutture di maggiori dimensioni di 8 byte. (CGSize: utilizzato nell'esempio precedente, è identico a 8 byte e pertanto non utilizza `objc_msgSend_stret()` nel simulatore.)

Utilizzare [ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret) i selettori che restituiscono mobile punto valore x86 solo. Questa funzione non dovrà essere utilizzato su ARM; Utilizzare invece `objc_msgSend()`.

Il principale [objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend) funzione viene utilizzata per tutti gli altri selettori.

Dopo aver deciso che `objc_msgSend()` tipicamente è necessario chiamare e potrebbe essere più di uno, ad esempio, per simulatore e del dispositivo, è possibile utilizzare una normale [ `[DllImport]` ](https://developer.xamarin.com/api/type/System.Runtime.InteropServices.DllImportAttribute/) metodo per dichiarare la funzione per la chiamata successiva.

Un set di predefinite `objc_msgSend()` dichiarazioni possono trovarsi in [ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/).


<a name="ugly" />

## <a name="the-ugly"></a>L'inutile

Objective-C dispone di tre tipi di `objc_msgSend` metodi: uno per le chiamate normali, uno per le chiamate che restituiscono valori a virgola mobile (solo x86) e uno per le chiamate che restituiscono valori struct. Quest'ultimo include il suffisso `_stret` in `ObjCRuntime.Messaging`.

Se si sta chiamando un metodo che restituirà determinate strutture (le regole sono descritte di seguito), è necessario richiamare il metodo con il valore restituito come primo parametro come valore out, simile al seguente:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

Operazioni diventano difficoltosa qui e perché la regola per quando è necessario utilizzare _stret_ è diverso in X86 e ARM, se si desidera che i binding per il simulatore sia il dispositivo, è necessario aggiungere codice simile al seguente:

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>Utilizzando il objc\_msgSend\_stret (metodo)

La regola per l'utilizzo di [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) sono simili per **ARM**:

-  Qualsiasi valore di tipo che non è un'enumerazione o uno qualsiasi dei tipi di base di un'enumerazione (int, byte, short, long, double, float).


La regola per l'utilizzo di [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) sono simili per **X86**:

-  Qualsiasi valore di tipo che non è un'enumerazione o uno qualsiasi dei tipi di base per un'enumerazione (int, byte, short, long, double, float) e le cui dimensioni native sono maggiore di 8 byte.


### <a name="creating-your-own-signatures"></a>Creazione di propri firme.

Nell'esempio [concetto](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) può essere utilizzato per creare la propria firme, se necessario.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio di selettori.](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
