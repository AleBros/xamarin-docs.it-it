---
title: Selettori Objective-C in xamarin. IOS
description: Questo documento illustra come interagire con i selettori Objective-C da c#. Viene descritto come richiamare i selettori e considerazioni tecniche che devono essere presi in considerazione quando si esegue questa operazione.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/12/2017
ms.openlocfilehash: cf39d548dc83fae67e8703d42e9387b8f19504e6
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669753"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selettori Objective-C in xamarin. IOS

Il linguaggio di Objective-C basa *selettori*. Un selettore è un messaggio che può essere inviato a un oggetto o una *classe*. [Xamarin. IOS](~/ios/internals/api-design/index.md) mappe dei selettori a metodi di istanza dell'istanza e classe selettori ai metodi statici.

A differenza delle normali funzioni C e come le funzioni membro C++, non è possibile richiamare direttamente tramite un selettore [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) invece i selettori vengono inviati a una classe di Objective-C o istanza utilizzando la [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
funzione.

Per altre informazioni sui messaggi in Objective-C, dare un'occhiata di Apple [utilizzo di oggetti](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) Guida.

## <a name="example"></a>Esempio

Si supponga che si vuole richiamare il [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
nel selettore [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring).
La dichiarazione (dalla documentazione di Apple) è:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Questa API presenta le caratteristiche seguenti:

- Il tipo restituito è `CGSize` per l'API unificata.
- Il `font` parametro è un [UIFont](xref:UIKit.UIFont) (e un tipo derivato (indirettamente) da [NSObject](xref:Foundation.NSObject)e viene eseguito il mapping a [IntPtr](xref:System.IntPtr).
- Il `width` parametro, un `CGFloat`, viene eseguito il mapping a `nfloat`.
- Il `lineBreakMode` parametro, un [ `UILineBreakMode` ](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), è già stato associato in xamarin. IOS come il [`UILineBreakMode`](xref:UIKit.UILineBreakMode)
Enumerazione.

Riassumendo, il `objc_msgSend` dichiarazione deve corrispondere:

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

Dichiararla come indicato di seguito:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Per chiamare questo metodo, usare codice simile al seguente:

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

Il valore restituito rimasto una struttura che era minore di 8 byte di dimensioni (come il precedente `SizeF` usato prima di passare alle API unificata), il codice sopra riportato sarebbe sono state eseguite sul simulatore ma arrestatosi in modo anomalo nel dispositivo. Per chiamare un selettore che restituisce un valore inferiore a 8 bit nella dimensione, dichiarare la `objc_msgSend_stret` funzione:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Per chiamare questo metodo, usare codice simile al seguente:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>Richiamo di un selettore

Richiamo di un selettore prevede tre passaggi:

1. Ottenere la destinazione del selettore.
2. Ottenere il nome del selettore.
3. Chiamare `objc_msgSend` con gli argomenti appropriati.

### <a name="selector-targets"></a>Destinazioni del selettore

Una destinazione del selettore è un'istanza dell'oggetto o una classe di Objective-C. Se la destinazione è un'istanza e proviene da un tipo associato che xamarin. IOS, usare il [ `ObjCRuntime.INativeObject.Handle` ](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) proprietà.

Se la destinazione è una classe, usare [ `ObjCRuntime.Class` ](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) per ottenere un riferimento all'istanza della classe, quindi usare i [ `Class.Handle` ](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) proprietà.

### <a name="selector-names"></a>Nomi di selettore

I nomi di selettore sono elencati nella documentazione di Apple. Ad esempio, [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring?language=objc) include [ `sizeWithFont:` ](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) e [ `sizeWithFont:forWidth:lineBreakMode:` ](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) selettori. Due punti finali e incorporati sono parte del nome del selettore e non possono essere omessi.

Dopo aver creato un nome di selettore, è possibile creare un [ `ObjCRuntime.Selector` ](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) per tale istanza.

### <a name="calling-objcmsgsend"></a>La chiamata a objc_msgSend

`objc_msgSend` Invia un messaggio (selettore) a un oggetto. In questa serie di funzioni richiede almeno due argomenti obbligatori: la destinazione del selettore (un'istanza o una classe di gestione), il selettore di se stesso e gli argomenti necessari per il selettore. Gli argomenti di istanza e del selettore devono essere `System.IntPtr`, e tutti i restanti argomenti devono corrispondere al tipo previsto che il selettore, ad esempio un `nint` per un' `int`, o un' `System.IntPtr` per tutte le `NSObject`-tipi derivati. Usare il [`NSObject.Handle`](xref:Foundation.NSObject.Handle)
proprietà da ottenere un `IntPtr` per un'istanza del tipo di Objective-C.

È presente più di un `objc_msgSend` funzione:

- Uso [ `objc_msgSend_stret` ](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) per i selettori che restituiscono un tipo struct. In ARM, inclusi restituiscono tutti i tipi che non è un'enumerazione o uno qualsiasi dei tipi incorporati C (`char`, `short`, `int`, `long`, `float`, `double`). X86 (simulatore), questo metodo deve essere usato per tutte le strutture di dimensioni superiori a 8 byte di dimensioni (`CGSize` è di 8 byte e non usa `objc_msgSend_stret` nel simulatore). 
- Uso [ `objc_msgSend_fpret` ](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) per valore a virgola selettori che restituiscono una virgola mobile x86 solo. Questa funzione non dovrà essere usato su ARM; Usare invece `objc_msgSend`. 
- Principale [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) funzione viene utilizzata per tutti i selettori di altri.

Dopo aver deciso quale `objc_msgSend` funzioni è necessario chiamare (simulatore e nel dispositivo potrebbero ognuno richiede un metodo diverso), è possibile usare una normale [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) per dichiarare la funzione per la successiva chiamata al metodo.

Un set di già pronti `objc_msgSend` dichiarazioni sono reperibili `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Diverse chiamate nel simulatore e dispositivo

Come descritto in precedenza, Objective-C dispone di tre tipi di `objc_msgSend` metodi: uno per le chiamate normali, uno per le chiamate che restituiscono valori a virgola mobile (solo x86) e uno per le chiamate che restituiscono valori di uno struct. Quest'ultimo include il suffisso `_stret` in `ObjCRuntime.Messaging`.

Se si richiamano un metodo che restituirà determinati tipi di struct (regole descritte di seguito), è necessario richiamare il metodo con il valore restituito come primo parametro come un `out` valore:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

La regola di cui si vuole usare il `_stret_` metodo differisce su x86 e ARM.
Se si desidera che le associazioni a lavorarvi sia il simulatore di dispositivo, aggiungere il codice seguente:

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objcmsgsendstret-method"></a>Usando il metodo objc_msgSend_stret

Quando si compila per ARM, usare il [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
per qualsiasi tipo di valore che non è un'enumerazione o uno qualsiasi dei tipi di base per un'enumerazione (`int`, `byte`, `short`, `long`, `double`, `float`).

Quando si compila per x86, usare [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
per qualsiasi tipo di valore che non è un'enumerazione o uno qualsiasi dei tipi di base per un'enumerazione (`int`, `byte`, `short`, `long`, `double`, `float`) e le cui dimensioni native sono maggiori di 8 byte.

### <a name="creating-your-own-signatures"></a>Creare le firme

Quanto segue [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) può essere utilizzato per creare le firme, se necessario.

## <a name="related-links"></a>Collegamenti correlati

- [Selettori Objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/) esempio
