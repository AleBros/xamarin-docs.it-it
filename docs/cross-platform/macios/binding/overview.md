---
title: Panoramica dei binding di Objective-C
description: Questo documento viene fornita una panoramica dei diversi modi per creare le associazioni c# per il codice Objective-C, tra cui le associazioni della riga di comando e progetti di binding Sharpie obiettivo. Viene inoltre descritto il funzionamento dell'associazione.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.openlocfilehash: 97d0c5b9f61d4dafe144d2b2f22df6d465cbbccb
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855273"
---
# <a name="overview-of-objective-c-bindings"></a>Panoramica dei binding di Objective-C

_Dettagli di come funziona il processo di associazione_

Associazione di una libreria Objective-C per l'uso con Xamarin richiede tre passaggi:

1. Scrivere un codice c# "Definizione API" per descrivere come l'API nativa viene esposta in .NET e modo in cui viene eseguito il mapping sottostante Objective-C. Questa operazione viene eseguita utilizzando il linguaggio c# standard costrutti, ad esempio `interface` e l'associazione di vari **attributi** (vedere questo [semplice esempio](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Dopo avere scritto "definizione API" nel linguaggio c#, si compila in modo da produrre un assembly "associazione". Questa operazione può essere eseguita sul [ **della riga di comando** ](#commandline) o mediante un [ **progetto di associazione** ](#bindingproject) in Visual Studio per Mac o Visual Studio.

3. Assembly "binding" viene quindi aggiunto al progetto di applicazione di Xamarin, pertanto è possibile accedere alla funzionalità native usando l'API è definito.
  Il progetto di associazione è completamente separato dai progetti di applicazione.

**Nota:** passaggio 1 può essere automatizzato con l'assistenza degli [ **Sharpie obiettivo**](#objectivesharpie). Esamina le API Objective-C e genera una proposta c# "Definizione API". È possibile personalizzare i file creati da Sharpie obiettivo e usarli in un progetto di associazione (o nella riga di comando) per creare l'assembly di binding. Obiettivo Sharpie non crea associazioni di per sé, ma è semplicemente una parte facoltativa del più ampio del processo.

È anche possibile leggere informazioni tecniche più dettagliate dei [funzionamento](#howitworks), che consente di scrivere le associazioni.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Associazioni di riga di comando

È possibile usare la `btouch-native` per xamarin. IOS (o `bmac-native` se si usa xamarin. Mac) per compilare direttamente le associazioni. Può essere utilizzato per passare le definizioni di API c# creati manualmente (o utilizzando Sharpie Objective) per lo strumento da riga di comando (`btouch-native` per iOS o `bmac-native` per Mac).


La sintassi generale per il richiamo di questi strumenti è:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

Il comando precedente genererà il file `cocos2d.dll` nella directory corrente, e conterrà la libreria completamente associata che è possibile usare nel progetto. Questo è lo strumento che Visual Studio per Mac Usa per creare le associazioni se si usa un progetto di associazione (descritto [seguito](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Progetto di associazione

Un progetto di associazione possa essere creato in Visual Studio per Mac o Visual Studio (Visual Studio supporta solo le associazioni di iOS) e rende più semplice modificare e compilare le definizioni delle API per l'associazione (confronto usando la riga di comando).

Seguire questo [Guida introduttiva](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) per informazioni su come creare e usare un progetto di associazione per produrre un'associazione.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Obiettivo Sharpie

Obiettivo Sharpie è separato, un altro strumento riga di comando che consente con le fasi iniziali della creazione di un'associazione. Non viene creata un'associazione di per sé, piuttosto che consente di automatizzare il passaggio iniziale della generazione di una definizione dell'API per la libreria nativa di destinazione.

Leggere il [docs Sharpie obiettivo](~/cross-platform/macios/binding/objective-sharpie/index.md) per informazioni su come analizzare le librerie native, Framework nativi e CocoaPods in definizioni di API che possono essere compilato in associazioni.

<a name="howitworks" />

## <a name="how-binding-works"></a>Funzionamento dell'associazione

È possibile usare la [[registrare]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) attributo [[esportare]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) attributo, e [chiamata selettori Objective-C manuale](~/ios/internals/objective-c-selectors.md) interagiscono per associare manualmente nuove (in precedenza tipi di Objective-C non associati).

Prima di tutto, trovare un tipo che si desidera associare. Per scopi discussione (e alla semplicità), eseguiremo l'associazione di [NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) tipo (che è già stato associato [Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); l'implementazione seguente è sufficiente, ad esempio a scopo).

In secondo luogo, è necessario creare il tipo c#. È probabile che si desideri inserire tale impostazione in uno spazio dei nomi; Poiché Objective-C non supporta gli spazi dei nomi, è necessario usare il `[Register]` attributo da cambiare il nome del tipo che xamarin. IOS verranno registrati con il runtime di Objective-C. Il tipo c# deve anche ereditare [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

In terzo luogo, consultare la documentazione di Objective-C e creare [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) istanze per ogni selettore si vuole usare. Posizionati all'interno del corpo della classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quarto, il tipo dovrà fornire costruttori. Si *necessario* concatenare la chiamata del costruttore del costruttore di classe di base. Il `[Export]` attributi consentono di chiamare i costruttori con il nome del selettore specificata codice Objective-C:

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

Successivamente, fornire i metodi per ognuno dei selettori dichiarato nel passaggio 3. Questi useranno `objc_msgSend()` per richiamare il selettore sull'oggetto nativo. Si noti l'uso del [Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr)) per convertire un' `IntPtr` in tipizzato in modo adeguato `NSObject` (sub) tipo. Se si desidera che il metodo possa essere chiamata dal codice Objective-C, il membro *devono* essere **virtuale**.

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

Riassumendo:

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [Corso di Xamarin University: Creazione di una libreria di binding di Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Compilare una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)