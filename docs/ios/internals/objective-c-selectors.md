---
title: Selettori Objective-C in Novell. iOS
description: In questo documento viene illustrato come interagire con i selettori Objective C#-C da. Viene descritto come richiamare i selettori e le considerazioni tecniche che devono essere presi in considerazione quando si esegue questa operazione.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/12/2017
ms.openlocfilehash: 17b845345175d80237bcfdb171461f2c763c364e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291844"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selettori Objective-C in Novell. iOS

Il linguaggio Objective-C si basa su *selettori*. Un selettore è un messaggio che può essere inviato a un oggetto o a una *classe*. [Novell. iOS](~/ios/internals/api-design/index.md) esegue il mapping di selettori di istanza ai metodi di istanza e ai selettori di classe per i metodi statici.

Diversamente dalle normali funzioni C (e come C++ le funzioni membro), non è possibile richiamare direttamente un selettore usando [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) , i selettori vengono inviati a una classe Objective-C o a un'istanza usando il cmdlet[`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
funzione.

Per ulteriori informazioni sui messaggi in Objective-C, vedere la guida sull' [utilizzo degli oggetti](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) di Apple.

## <a name="example"></a>Esempio

Si supponga di voler richiamare il cmdlet[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
selettore [`NSString`](https://developer.apple.com/documentation/foundation/nsstring).
La dichiarazione (dalla documentazione di Apple) è:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Questa API presenta le caratteristiche seguenti:

- Il tipo restituito è `CGSize` per il API unificata.
- Il `font` parametro è un [UIFont](xref:UIKit.UIFont) (e un tipo (indirettamente) derivato da [NSObject](xref:Foundation.NSObject)ed è mappato a [System. IntPtr](xref:System.IntPtr).
- Il `width` parametro, a `CGFloat`, viene mappato `nfloat`a.
- Il `lineBreakMode` parametro, a [`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), è già stato associato in Novell. iOS come[`UILineBreakMode`](xref:UIKit.UILineBreakMode)
Enumerazione.

Riunendola, la `objc_msgSend` dichiarazione deve corrispondere a quanto segue:

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

Dichiararlo come segue:

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

Se il valore restituito fosse una struttura di dimensioni inferiori a 8 byte (ad esempio quella precedente `SizeF` utilizzata prima del trasferimento alle API unificate), il codice precedente verrebbe eseguito sul simulatore ma si è arrestato in modo anomalo sul dispositivo. Per chiamare un selettore che restituisce un valore inferiore a 8 bit, dichiarare la `objc_msgSend_stret` funzione:

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

La chiamata di un selettore prevede tre passaggi:

1. Ottiene la destinazione del selettore.
2. Ottiene il nome del selettore.
3. Chiamare `objc_msgSend` con gli argomenti appropriati.

### <a name="selector-targets"></a>Destinazioni del selettore

Una destinazione del selettore è un'istanza dell'oggetto o una classe Objective-C. Se la destinazione è un'istanza di e proviene da un tipo Novell. iOS associato, usare [`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle) la proprietà.

Se la destinazione è una classe, utilizzare [`ObjCRuntime.Class`](xref:ObjCRuntime.Class) per ottenere un riferimento all'istanza della classe, quindi utilizzare la [`Class.Handle`](xref:ObjCRuntime.Class.Handle) proprietà.

### <a name="selector-names"></a>Nomi dei selettori

I nomi dei selettori sono elencati nella documentazione di Apple. Sono inclusi [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc) [`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) , ad esempio [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) , i selettori e. I due punti incorporati e finali fanno parte del nome del selettore e non possono essere omessi.

Una volta ottenuto il nome del selettore, è [`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector) possibile crearne un'istanza.

### <a name="calling-objc_msgsend"></a>Chiamata di objc_msgSend

`objc_msgSend`Invia un messaggio (selettore) a un oggetto. Questa famiglia di funzioni accetta almeno due argomenti obbligatori: la destinazione del selettore, ovvero un'istanza o un handle di classe, il selettore e gli eventuali argomenti necessari per il selettore. Gli argomenti dell' `System.IntPtr`istanza e del selettore devono essere e tutti gli argomenti rimanenti devono corrispondere al tipo previsto dal selettore, ad esempio un `nint` oggetto `NSObject` `System.IntPtr` per un oggetto `int`o per tutti i tipi derivati da. Usare il[`NSObject.Handle`](xref:Foundation.NSObject.Handle)
per ottenere un oggetto `IntPtr` per un'istanza di tipo Objective-C.

Esiste più di una `objc_msgSend` funzione:

- Usare [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) per i selettori che restituiscono uno struct. In ARM sono inclusi tutti i tipi restituiti che non sono un'enumerazione o uno dei tipi`char`incorporati C ( `int`, `short`,, `long`, `float`, `double`). In x86 (il simulatore), questo metodo deve essere usato per tutte le strutture di dimensioni superiori a 8 byte`CGSize` (è di 8 byte e `objc_msgSend_stret` non usa nel simulatore). 
- Usare [`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) per i selettori che restituiscono un valore a virgola mobile solo su x86. Questa funzione non deve essere usata su ARM; utilizzare `objc_msgSend`invece. 
- La funzione [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) principale viene utilizzata per tutti gli altri selettori.

Dopo aver deciso quali `objc_msgSend` funzioni è necessario chiamare (il simulatore e il dispositivo possono richiedere un metodo diverso), è possibile usare un metodo normale [`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute) per dichiarare la funzione per una chiamata successiva.

Un set di `objc_msgSend` dichiarazioni predefinite si trova in `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Chiamate diverse sul simulatore e sul dispositivo

Come descritto in precedenza, Objective-C presenta tre tipi `objc_msgSend` di metodi: uno per le chiamate regolari, uno per le chiamate che restituiscono valori a virgola mobile (solo x86) e uno per le chiamate che restituiscono valori struct. Il secondo include il `_stret` suffisso `ObjCRuntime.Messaging`in.

Se si richiama un metodo che restituirà determinati struct (regole descritte di seguito), è necessario richiamare il metodo con il valore restituito come primo parametro come `out` valore:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

La regola per il momento in cui `_stret_` usare il metodo differisce da x86 e ARM.
Se si desidera che i binding funzionino sia sul simulatore che sul dispositivo, aggiungere il codice seguente:

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

### <a name="using-the-objc_msgsend_stret-method"></a>Utilizzo del metodo objc_msgSend_stret

Quando si compila per ARM, usare il[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
per qualsiasi tipo di valore che non sia un'enumerazione o uno dei tipi di base per un'enumerazione`int`( `byte` `short`,, `long`, `double`, `float`,).

Quando si compila per x86, usare[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
per qualsiasi tipo di valore che non sia un'enumerazione o uno dei tipi di base per un'enumerazione`int`( `byte` `short`,, `long`, `double`, `float`,) e la cui dimensione nativa è maggiore di 8 byte.

### <a name="creating-your-own-signatures"></a>Creazione di firme personalizzate

Il [GIST](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) seguente può essere usato per creare le proprie firme, se necessario.

## <a name="related-links"></a>Collegamenti correlati

- Esempio [di selettori Objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/)
