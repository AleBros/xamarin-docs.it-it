---
title: Architettura Xamarin.Mac
description: Questa guida viene esaminato Xamarin.Mac e la relativa relazione per Objective-C a un livello basso. Vengono illustrati concetti, ad esempio la compilazione, i selettori, Registrar, l'app viene avviata e il generatore.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: d6d7557fed5ea0ca0719dcbddbda316340645320
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-architecture"></a>Architettura Xamarin.Mac

_Questa guida viene esaminato Xamarin.Mac e la relativa relazione per Objective-C a un livello basso. Vengono illustrati concetti, ad esempio la compilazione, i selettori, Registrar, l'app viene avviata e il generatore._

## <a name="overview"></a>Panoramica

Xamarin.Mac applicazioni eseguite all'interno dell'ambiente di esecuzione Mono e usare il compilatore di Xamarin per compilare fino a Intermediate Language (IL), che è quindi Just-in-Time (JIT) compilate in codice nativo in fase di esecuzione. Ciò comporta l'esecuzione side-by-side con il Runtime Objective-C. Entrambi gli ambienti di runtime sono eseguiti su un kernel simili a UNIX, in particolare XNU ed espongono diverse API per il codice utente consentendo agli sviluppatori di accedere al sistema nativo o gestito sottostante.

Il diagramma seguente mostra una panoramica dell'architettura di base:

[![Diagramma che mostra una panoramica dell'architettura di base](architecture-images/mac-arch.png "diagramma che mostra una panoramica dell'architettura di base")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Codice gestito e nativo

Durante lo sviluppo di Xamarin, le condizioni di *nativo* e *gestiti* codice vengono spesso utilizzati. Codice gestito è codice che l'esecuzione gestita da Common Language Runtime di .NET Framework o in caso di Xamarin: il Runtime Mono.

Codice nativo è codice che verrà eseguito in modo nativo nella piattaforma specifica (ad esempio, Objective-C o persino codice AOT compilati, in un chip ARM). Questa guida viene illustrato come il codice gestito viene compilato in codice nativo e illustra la modalità di funzionamento di applicazioni, Xamarin.Mac basate sull'uso di Mac API Apple tramite l'utilizzo di associazioni, mentre anche l'accesso a. Libreria di classi base della rete e un linguaggio di sofisticate, ad esempio c#.

## <a name="requirements"></a>Requisiti

Per sviluppare un'applicazione macOS con Xamarin.Mac è necessario quanto segue:

- Un macOS in esecuzione Mac Sierra (10.12) o versione successiva.
- La versione più recente di Xcode (installato dal [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- La versione più recente di Xamarin.Mac e Visual Studio per Mac

Per eseguire le applicazioni Mac create con Xamarin.Mac sono necessari i requisiti di sistema seguenti:

- Un Mac che eseguono Mac OS X 10,7 o versione successiva.

## <a name="compilation"></a>Compilazione

Quando si compila tutte le applicazioni di piattaforma Xamarin, il compilatore c# Mono (o F #) verrà eseguito e verrà compilato il codice c# e F # in Microsoft Intermediate Language (MSIL o IL). Xamarin.Mac Usa quindi un *immediatamente nel tempo (JIT)* compilatore in fase di esecuzione per la compilazione di codice nativo, consentendo l'esecuzione nella corretta architettura in base alle esigenze.

Ciò si differenzia xamarin che viene utilizzata la compilazione AOT. Quando si utilizza il compilatore AOT, tutti gli assembly e tutti i metodi all'interno di essi vengono compilati al momento della compilazione. Con JIT, la compilazione viene eseguita su richiesta solo per i metodi che vengono eseguiti.

Con le applicazioni Xamarin.Mac, Mono viene in genere incorporata nel bundle dell'app (e dette **Mono incorporato**). Quando si utilizza l'API Xamarin.Mac classico, l'applicazione è possibile invece usare **Mono sistema**, tuttavia, ciò non è supportata nell'API unificata. Sistema Mono fa riferimento a Mono che è stato installato nel sistema operativo. All'avvio dell'applicazione, l'app Xamarin.Mac verrà utilizzato.

## <a name="selectors"></a>Selettori

Con Xamarin, sono disponibili due ecosistemi separati, .NET e Apple, che è necessario introdurre insieme a sembrare come semplice possibile, in modo da garantire che l'obiettivo finale è un'esperienza utente uniforme. Abbiamo visto nella sezione precedente la comunicazione tra i due runtime, e può anche molto sentito il termine 'binding', che consente le API native Mac da usare in Xamarin. Le associazioni sono illustrate in dettaglio nel [documentazione associazione Objective-C](~/mac/platform/binding.md), pertanto per il momento, esaminiamo il funzionamento Xamarin.Mac dietro le quinte.

In primo luogo, deve esistere un modo per esporre Objective-C per c#, che viene eseguita tramite i selettori. Un selettore è un messaggio che viene inviato a un oggetto o una classe. Con Objective-C questa operazione viene eseguita tramite il [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) funzioni. Per ulteriori informazioni sull'uso dei selettori, fare riferimento a iOS [selettori Objective-C](~/ios/internals/objective-c-selectors.md) Guida. Sono inoltre deve essere un modo per esporre il codice gestito e Objective-C, che è più complicato che Objective-C non conosce assolutamente il codice gestito. Per evitare questo problema, utilizziamo un [registrar](~/mac/internals/registrar.md). Questo è illustrato più dettagliatamente nella sezione successiva.

## <a name="registrar"></a>Registrar

Come indicato in precedenza, il programma di registrazione è codice che espone il codice gestito per Objective-C. A tale scopo, la creazione di un elenco di tutte le classi gestite che deriva da NSObject:

- Per tutte le classi che non esegue il wrapping di una classe Objective-C esistente, crea una nuova classe Objective-C con membri Objective-C mirroring tutti i membri gestiti che hanno un `[Export]` attributo.
- Nelle implementazioni per ogni membro Objective-C, viene aggiunto automaticamente codice per chiamare il membro gestito con mirroring.

Lo pseudocodice seguente viene illustrato un esempio di questa procedura:

**C# (codice gestito):**

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

Il codice gestito può contenere gli attributi, `[Register]` e `[Export]`, che utilizza il programma di registrazione di sapere che l'oggetto deve essere esposto a Objective-C. L'attributo [registrare] viene utilizzato per specificare il nome della classe Objective-C generata nel caso in cui il nome predefinito generato non è adatto. Tutte le classi derivate da NSObject vengono registrate automaticamente in Objective-C. L'attributo [Esporta] obbligatorio contiene una stringa, ovvero il selettore utilizzato nella classe generata Objective-C.

Esistono due tipi di registri usati in Xamarin.Mac: dinamico e statico:

- Registrar dinamica: questo è il programma di registrazione predefinito per tutte le compilazioni Xamarin.Mac. Il programma di registrazione dinamica esegue la registrazione di tutti i tipi nell'assembly in fase di esecuzione. Ciò avviene tramite le funzioni fornite dall'API di runtime Objective-C. Il programma di registrazione dinamica dispone pertanto di un avvio più lento, ma una più rapida la compilazione. Le funzioni native (in genere in C), chiamate trampolines, vengono utilizzate come implementazioni del metodo quando si utilizza il Registrar dinamico. Variano tra le architetture diverse.
- Registrar statico: il programma di registrazione statico genera codice Objective-C durante la compilazione, che viene quindi compilata in una libreria statica e il file eseguibile collegata. In questo modo un avvio più rapido, ma richiede più tempo durante la fase di compilazione.

## <a name="application-launch"></a>Avvio dell'applicazione

Logica di avvio Xamarin.Mac variano a seconda se incorporati o sistema Mono viene utilizzato. Per visualizzare il codice e i passaggi per l'avvio dell'applicazione Xamarin.Mac, consultare il [avvio intestazione](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) file nel repository pubblico macios di xamarin.

## <a name="generator"></a>Generator

Xamarin.Mac contiene le definizioni per tutte le API Mac. È possibile esplorare uno di questi sul [repository github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Queste definizioni di contengono interfacce con gli attributi, nonché qualsiasi proprietà e metodi necessari. Ad esempio, il codice seguente viene utilizzata per definire un NSBox nel [AppKit dello spazio dei nomi](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Si noti che è un'interfaccia con un numero di metodi e proprietà:

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

Il generatore, chiamato `bmac` in Xamarin.Mac, accetta questi file di definizione e utilizza gli strumenti di .NET per compilarli in un assembly temporaneo. Tuttavia, l'assembly temporaneo non è utilizzabile per chiamare codice Objective-C. Il generatore quindi legge l'assembly temporaneo e genera codice c# che può essere utilizzato in fase di esecuzione. Si tratta, ad esempio, se si aggiunge un attributo casuale per il file di definizione. cs, non verrà visualizzati nel codice di output. Il generatore non riconosce e pertanto `bmac` non riconosce da cercare nell'assembly temporaneo per visualizzarli.

Una volta il Xamarin.Mac.dll è stato creato, il packager, `mmp`, sarà possibile creare un bundle tutti i componenti.

In generale, raggiunge questo eseguendo le attività seguenti:

- Creare una struttura di bundle di app.
- Copiare gli assembly gestiti.
- Se il collegamento è abilitato, eseguire il linker gestito per ottimizzare gli assembly rimuovendo le parti non utilizzate.
- Creare un'applicazione di avvio, il collegamento nel codice dell'utilità di avvio parlato insieme il codice registar se in modalità statica.

Si tratta quindi Esegui come parte dell'utente che fanno riferimento Xamarin.Mac.dll ed esegue processo che viene compilato il codice utente in un assembly di compilazione `mmp` per renderlo un pacchetto

Per ulteriori informazioni sul linker e su come utilizzarlo, fare riferimento a iOS [Linker](~/ios/deploy-test/linker.md) Guida.

## <a name="summary"></a>Riepilogo

In questa guida viene esaminato la compilazione di App Xamarin.Mac e consentirne Xamarin.Mac e la relazione per Objective-C.
