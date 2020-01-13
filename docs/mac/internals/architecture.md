---
title: Architettura di Xamarin.Mac
description: Questa guida esamina Xamarin.Mac e la relativa relazione con Objective-C a un livello basso. Vengono illustrati concetti quali la compilazione, i selettori, i registrar, l'avvio dell'app e il generatore.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 51900adb1dd15675e584671f3b06ad6d7572f47d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017559"
---
# <a name="xamarinmac-architecture"></a>Architettura di Xamarin.Mac

_Questa guida esamina Xamarin.Mac e la relativa relazione con Objective-C a un livello basso. Vengono illustrati concetti quali la compilazione, i selettori, i registrar, l'avvio dell'app e il generatore._

## <a name="overview"></a>Panoramica

Le applicazioni Xamarin.Mac vengono eseguite nell'ambiente di esecuzione mono e usano il compilatore di Novell per compilare il linguaggio intermedio (IL), che è quindi compilato JIT (just-in-Time) in codice nativo in fase di esecuzione. Questa operazione viene eseguita side-by-side con il runtime di Objective-C. Entrambi gli ambienti di runtime vengono eseguiti su un kernel di tipo UNIX, in particolare XNU, ed espongono diverse API al codice utente, consentendo agli sviluppatori di accedere al sistema nativo o gestito sottostante.

Il diagramma seguente mostra una panoramica di base di questa architettura:

[![Diagramma che illustra una panoramica di base dell'architettura](architecture-images/mac-arch.png "Diagramma che illustra una panoramica di base dell'architettura")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Codice nativo e gestito

Quando si sviluppa per Novell, vengono spesso usati i termini *nativi* e codice *gestito* . Il codice gestito è codice in cui l'esecuzione viene gestita dal .NET Framework Common Language Runtime o nel caso di Novell: il runtime di mono.

Il codice nativo è codice che verrà eseguito in modalità nativa sulla piattaforma specifica, ad esempio Objective-C o anche codice compilato AOT, su un chip ARM. Questa guida illustra il modo in cui il codice gestito viene compilato in codice nativo e illustra il funzionamento di un'applicazione Xamarin.Mac, per sfruttare al meglio le API Mac di Apple tramite l'uso di binding, pur avendo accesso a. BCL di NET e un linguaggio sofisticato, C#ad esempio.

## <a name="requirements"></a>Requisiti

Per sviluppare un'applicazione macOS con Xamarin.Mac è necessario quanto segue:

- Un Mac che esegue macOS Sierra (10,12) o versione successiva.
- La versione più recente di Xcode (installata dall' [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- La versione più recente di Xamarin.Mac e Visual Studio per Mac

Per eseguire le applicazioni Mac create con Xamarin.Mac sono necessari i requisiti di sistema seguenti:

- Mac che esegue Mac OS X 10,7 o versione successiva.

## <a name="compilation"></a>Compilazione

Quando si compila un'applicazione della piattaforma Novell, il C# compilatore mono F#(o) verrà eseguito e compilerà F# il codice e il C# codice in Microsoft Intermediate Language (MSIL o il). Xamarin.Mac usa quindi un compilatore *just-in-time (JIT)* in fase di esecuzione per compilare il codice nativo, consentendo l'esecuzione sull'architettura corretta, in base alle esigenze.

Questo è contrario a Xamarin.iOS che usa la compilazione AOT. Quando si usa il compilatore AOT, tutti gli assembly e tutti i metodi in essi contenuti vengono compilati in fase di compilazione. Con JIT, la compilazione avviene su richiesta solo per i metodi che vengono eseguiti.

Con le applicazioni Xamarin.Mac, mono viene in genere incorporato nel bundle dell'app e viene definito **mono incorporato**. Quando si usa l'API Xamarin.Mac classica, l'applicazione può invece usare **mono di sistema**, ma questa operazione non è supportata nel API unificata. System mono si riferisce a mono installato nel sistema operativo. All'avvio dell'applicazione, l'app Xamarin.Mac utilizzerà questa.

## <a name="selectors"></a>Selettori

Con Novell sono disponibili due ecosistemi distinti, ovvero .NET e Apple, che è necessario riunire per sembrare più semplici, per garantire che l'obiettivo finale sia un'esperienza utente uniforme. Nella sezione precedente è stato illustrato il modo in cui i due runtime comunicano ed è possibile che sia stato ascoltato il termine "Bindings", che consente di usare le API Mac native in Novell. Le associazioni sono illustrate in dettaglio nella [documentazione sull'associazione di Objective-C](~/mac/platform/binding.md), quindi per ora è possibile esaminare il funzionamento di Xamarin.Mac dietro le quinte.

In primo luogo, è necessario un modo per esporre Objective-C a C#, operazione eseguita tramite selettori. Un selettore è un messaggio inviato a un oggetto o a una classe. Con Objective-C questa operazione viene eseguita tramite le funzioni [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) . Per altre informazioni sull'uso dei selettori, vedere la guida per i selettori di iOS [Objective-C](~/ios/internals/objective-c-selectors.md) . Deve inoltre essere disponibile un modo per esporre il codice gestito a Objective-C, che è più complicato a causa del fatto che Objective-C non conosce nulla sul codice gestito. Per aggirare questo problema, viene usato un [registrar](~/mac/internals/registrar.md). Questa operazione è stata illustrata più dettagliatamente nella sezione successiva.

## <a name="registrar"></a>Registrar

Come indicato in precedenza, il registrar è codice che espone il codice gestito a Objective-C. Questa operazione viene eseguita creando un elenco di tutte le classi gestite che derivano da NSObject:

- Per tutte le classi che non eseguono il wrapping di una classe Objective-C esistente, viene creata una nuova classe Objective-C con membri Objective-C che eseguono il mirroring di tutti i membri gestiti che hanno un attributo `[Export]`.
- Nelle implementazioni per ogni membro Objective-C, il codice viene aggiunto automaticamente per chiamare il membro gestito con mirroring.

Lo pseudo-codice seguente mostra un esempio di come eseguire questa operazione:

**C#(codice gestito):**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective-C (codice nativo):**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

Il codice gestito può contenere gli attributi, `[Register]` e `[Export]`, usati dal registrar per tenere presente che l'oggetto deve essere esposto a Objective-C. L'attributo [Register] viene utilizzato per specificare il nome della classe Objective-C generata nel caso in cui il nome generato predefinito non sia appropriato. Tutte le classi derivate da NSObject vengono registrate automaticamente con Objective-C. L'attributo obbligatorio [Export] contiene una stringa, ovvero il selettore usato nella classe Objective-C generata.

Esistono due tipi di registrar usati in Xamarin.Mac-Dynamic e static:

- Registrar dinamici: si tratta del registrar predefinito per tutte le compilazioni Xamarin.Mac. Il registrar dinamico esegue la registrazione di tutti i tipi nell'assembly in fase di esecuzione. Questa operazione viene eseguita usando le funzioni fornite dall'API di runtime di Objective-C. Il registrar dinamico ha pertanto un avvio più lento, ma un tempo di compilazione più rapido. Le funzioni native, in genere in C, denominate trampolini, vengono usate come implementazioni del metodo quando si usano i registrar dinamici. Variano a seconda delle diverse architetture.
- Registrar statici: il registrar statico genera codice Objective-C durante la compilazione, che viene quindi compilato in una libreria statica e collegato all'eseguibile. Questo consente un avvio più rapido, ma richiede più tempo durante la fase di compilazione.

## <a name="application-launch"></a>Avvio dell'applicazione

La logica di avvio di Xamarin.Mac varia a seconda che venga usato embedded o System mono. Per visualizzare il codice e i passaggi per l'avvio dell'applicazione Xamarin.Mac, fare riferimento al file di [intestazione di avvio](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) nel repository pubblico Novell-macios.

## <a name="generator"></a>Generator

Xamarin.Mac contiene le definizioni per ogni API Mac. È possibile esplorare uno di questi nel [repository GitHub MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Queste definizioni contengono interfacce con attributi, nonché eventuali metodi e proprietà necessari. Il codice seguente, ad esempio, viene usato per definire un NSBox nello [spazio dei nomi AppKit](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Si noti che si tratta di un'interfaccia con diversi metodi e proprietà:

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

    …

    [Export ("borderRect")]
    CGRect BorderRect { get; }

    [Export ("titleRect")]
    CGRect TitleRect { get; }

    [Export ("titleCell")]
    NSObject TitleCell { get; }

    [Export ("sizeToFit")]
    void SizeToFit ();

    [Export ("contentViewMargins")]
    CGSize ContentViewMargins { get; set; }

    [Export ("setFrameFromContentFrame:")]
    void SetFrameFromContentFrame (CGRect contentFrame);

    …

}
```

Il generatore, denominato `bmac` in Xamarin.Mac, accetta questi file di definizione e usa gli strumenti .NET per compilarli in un assembly temporaneo. Tuttavia, questo assembly temporaneo non è utilizzabile per chiamare il codice Objective-C. Il generatore legge quindi l'assembly temporaneo e genera C# il codice che può essere utilizzato in fase di esecuzione. Questo è il motivo per cui, ad esempio, se si aggiunge un attributo casuale al file Definition. cs, questo non verrà visualizzato nel codice output. Il generatore non ne è a conoscenza e pertanto `bmac` non è in grado di cercarlo nell'assembly temporaneo per l'output.

Una volta creato il file Xamarin.Mac. dll, il Packager, `mmp`, raggruppa tutti i componenti.

A un livello elevato, ottiene questo risultato eseguendo le attività seguenti:

- Creare una struttura del bundle dell'app.
- Copiare gli assembly gestiti.
- Se è abilitato il collegamento, eseguire il linker gestito per ottimizzare gli assembly rimuovendo le parti inutilizzate.
- Creare un'applicazione di avvio. il collegamento nel codice dell'utilità di avvio è stato parlato insieme al codice del registrar se in modalità statica.

Questa operazione viene quindi eseguita come parte del processo di compilazione utente che compila il codice utente in un assembly che fa riferimento a Xamarin.Mac. dll ed esegue `mmp` per renderlo un pacchetto

Per informazioni più dettagliate sul linker e su come viene usato, vedere la guida del [linker](~/ios/deploy-test/linker.md) iOS.

## <a name="summary"></a>Riepilogo

Questa guida ha esaminato la compilazione di app Xamarin.Mac ed Esplora Xamarin.Mac e la relativa relazione con Objective-C.
