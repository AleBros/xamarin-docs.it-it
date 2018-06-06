---
title: Panoramica delle associazioni Objective-C
description: Questo documento viene fornita una panoramica dei diversi modi per creare associazioni c# per il codice Objective-C, tra cui le associazioni della riga di comando, i progetti di associazione e Sharpie obiettivo. Illustra anche la modalità di funzionamento dell'associazione.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.openlocfilehash: f9f981a9024ad9b1f780efbadeeb7e1f1636a8ae
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781744"
---
# <a name="overview-of-objective-c-bindings"></a>Panoramica delle associazioni Objective-C

_Dettagli di come funziona il processo di associazione_

Associazione di una libreria di Objective-C per l'uso con Xamarin richiede tre passaggi:

1. Scrivere un c# "Definizione dell'API" per descrivere come l'API nativa viene esposta in .NET e come viene eseguito il mapping sottostante Objective-C. Questa operazione viene eseguita utilizzando il linguaggio c# standard costruisce come `interface` e associazione vari **attributi** (vedere questo [semplice esempio](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Dopo aver scritto la definizione dell'API"" in c#, compilarlo per generare un assembly "associazione". Questa operazione può essere eseguita su di [ **riga di comando** ](#commandline) o tramite un [ **progetto associazione** ](#bindingproject) in Visual Studio per Mac o Visual Studio.

3. Tale assembly "associazione" viene quindi aggiunto al progetto di applicazione di Xamarin, pertanto è possibile accedere alla funzionalità native utilizzando l'API è definita.
  Il progetto di associazione è completamente separato dai progetti di applicazione.

**Nota:** passaggio 1 può essere automatizzato con l'assistenza di [ **obiettivo Sharpie**](#objectivesharpie). Esamina l'API di Objective-C e genera una proposta c# "Definizione dell'API". È possibile personalizzare i file creati dall'obiettivo Sharpie e utilizzarli in un progetto di associazione (o nella riga di comando) per creare l'associazione assembly. Obiettivo Sharpie non crea associazioni autonomamente, è semplicemente una parte facoltativa del processo di dimensioni maggiore.

È inoltre possibile leggere altre informazioni tecniche di [funzionamento](#howitworks), che consentono di scrivere i binding.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Associazioni di riga di comando

È possibile utilizzare il `btouch-native` per xamarin. IOS (o `bmac-native` se si utilizza Xamarin.Mac) per compilare direttamente le associazioni. Funziona passando le definizioni di API c# creati manualmente (o obiettivo Sharpie) per lo strumento da riga di comando (`btouch-native` per iOS o `bmac-native` per Mac).


La sintassi generale per la chiamata di questi strumenti è:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

Il comando precedente genera il file `cocos2d.dll` nella directory corrente, e contiene la raccolta completamente associata che è possibile utilizzare nel progetto. Questo è lo strumento utilizzato Visual Studio per Mac per creare le associazioni se si utilizza un progetto di associazione (descritto [seguito](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Progetto di associazione

Un progetto di associazione può essere creato in Visual Studio per Mac o Visual Studio (Visual Studio supporta solo le associazioni di iOS) e rende più semplice modificare e compilare le definizioni delle API per l'associazione (o utilizzando la riga di comando).

Seguire questo [Guida introduttiva](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) per informazioni su come creare e utilizzare un progetto di associazione per produrre un'associazione.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Obiettivo Sharpie

Obiettivo Sharpie è separato, un altro strumento riga di comando che consente con le fasi iniziali della creazione di un'associazione. Non viene creata un'associazione da solo, piuttosto che consente di automatizzare la fase iniziale della generazione di una definizione dell'API per la libreria nativa di destinazione.

Lettura di [documenti obiettivo Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) per informazioni su come analizzare librerie native, Framework nativo e CocoaPods in definizioni di API che possono essere integrata nel binding.

<a name="howitworks" />

## <a name="how-binding-works"></a>Funzionamento dell'associazione

È possibile utilizzare il [[registrare]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) attributo [[esportare]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) attributo, e [chiamata di selettore manuale Objective-C](~/ios/internals/objective-c-selectors.md) interagiscono per associare manualmente nuove (in precedenza tipi di Objective-C non associati).

Prima di tutto, trovare un tipo che si desidera associare. Per fini discussione (e semplicità), si sarà associa il [NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) tipo (che è già stato associato [Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); l'implementazione seguente è semplicemente ad esempio a scopo).

In secondo luogo, è necessario creare il tipo c#. È probabile che si desideri posizionarlo in uno spazio dei nomi; Poiché Objective-C non supporta gli spazi dei nomi, è necessario utilizzare il `[Register]` attributo per modificare il nome del tipo che xamarin verranno registrati con il runtime Objective-C. Il tipo c# deve inoltre ereditare da [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

In terzo luogo, consultare la documentazione di Objective-C e creare [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) istanze per ogni selettore che si desidera utilizzare. Posizionati all'interno del corpo della classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quarto, il tipo dovrà fornire costruttori. Si *deve* concatenare la chiamata del costruttore al costruttore della classe base. Il `[Export]` attributi consentono codice Objective-C per chiamare i costruttori con il nome del selettore specificato:

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

Successivamente, fornire i metodi per ognuno dei selettori dichiarati nel passaggio 3. Si utilizzerà `objc_msgSend()` per richiamare il selettore per l'oggetto nativo. Si noti l'uso di [Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr)) per convertire un `IntPtr` in tipizzato in modo adeguato `NSObject` (sub) tipo. Se si desidera che il metodo possa essere chiamata da codice Objective-C, il membro *deve* essere **virtuale**.

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

Conclusioni:

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

