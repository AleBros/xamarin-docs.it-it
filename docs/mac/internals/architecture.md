---
title: Architettura di xamarin. Mac
description: Questa Guida Esplora xamarin. Mac e la relativa relazione Objective-c di basso livello. Vengono illustrati concetti, ad esempio la compilazione, i selettori, Registrar, avvio dell'app e il generatore.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 04/12/2017
ms.openlocfilehash: 7b9b3b648f6ac66859714052e916407026da10d4
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526936"
---
# <a name="xamarinmac-architecture"></a>Architettura di xamarin. Mac

_Questa Guida Esplora xamarin. Mac e la relativa relazione Objective-c di basso livello. Vengono illustrati concetti, ad esempio la compilazione, i selettori, Registrar, avvio dell'app e il generatore._

## <a name="overview"></a>Panoramica

Le applicazioni xamarin. Mac eseguire all'interno dell'ambiente di esecuzione Mono e consente di compilare fino a Intermediate Language (IL), che viene quindi Just-in-Time (JIT) compilate in codice nativo in fase di esecuzione del compilatore di Xamarin. Ciò comporta l'esecuzione side-by-side con il Runtime di Objective-C. Entrambi gli ambienti di runtime eseguiti su un kernel simili a UNIX, in particolare XNU ed espongono diverse API per il codice utente che consente agli sviluppatori di accedere al sistema nativo o gestito sottostante.

Il diagramma seguente mostra una panoramica di base di questa architettura:

[![Diagramma che mostra una panoramica di base dell'architettura](architecture-images/mac-arch.png "diagramma che mostra una panoramica di base dell'architettura")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Codice nativo e gestito

Durante lo sviluppo per Xamarin, i termini *nativi* e *gestito* codice vengono spesso usati. Il codice gestito è codice che include l'esecuzione gestita da Common Language Runtime di .NET Framework o in caso di Xamarin: il Runtime di Mono.

Codice nativo è codice che verrà eseguito in modo nativo nella piattaforma specifica (ad esempio, Objective-C o anche il codice di compilazione AOT, in un chip ARM). Questa guida illustra come il codice gestito viene compilato in codice nativo e spiega come funziona un'applicazione xamarin. Mac, basate sull'uso delle API Mac Apple tramite l'uso di associazioni, mantenendo anche l'accesso a. Libreria di classi base della rete e un linguaggio sofisticato, ad esempio C#.

## <a name="requirements"></a>Requisiti

Per sviluppare un'applicazione macOS con Xamarin.Mac è necessario quanto segue:

- Un Mac che esegue macOS Sierra (10.12) o versione successiva.
- La versione più recente di Xcode (installato dal [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- La versione più recente di xamarin. Mac e Visual Studio per Mac

Per eseguire le applicazioni Mac create con Xamarin.Mac sono necessari i requisiti di sistema seguenti:

- Un Mac che esegue Mac OS X 10.7 o versione successiva.

## <a name="compilation"></a>Compilazione

Quando si esegue la compilazione di qualsiasi applicazione della piattaforma Xamarin, di Mono C# (o F#) del compilatore verrà eseguito e verrà compilato il C# e F# codice in Microsoft Intermediate Language (MSIL o IL). Xamarin. Mac Usa quindi un *Just in Time (JIT)* compilatore in fase di esecuzione per la compilazione codice nativo, consentendo l'esecuzione dell'architettura corretta in base alle esigenze.

In contrasto con xamarin. IOS che usa la compilazione AOT. Quando si usa il compilatore AOT, tutti gli assembly e tutti i metodi all'interno di essi vengono compilati in fase di compilazione. Con JIT, la compilazione viene eseguita su richiesta solo per i metodi che vengono eseguiti.

Con le applicazioni xamarin. Mac, Mono viene in genere incorporata nel bundle dell'app (e dette **Embedded Mono**). Quando si usa l'API classica di xamarin. Mac, l'applicazione può usare invece **Mono di sistema**, tuttavia, ciò non è supportata nell'API unificata. Mono di sistema fa riferimento al progetto Mono è stato installato nel sistema operativo. All'avvio dell'applicazione, l'app xamarin. Mac verrà usato.

## <a name="selectors"></a>Selettori

Con Xamarin, abbiamo due ecosistemi separati, .NET e Apple, che è necessario visualizzare insieme a sembrare come semplice possibile garantire che l'obiettivo finale è un'esperienza utente uniforme. Abbiamo visto nella sezione precedente modalità di comunicazione tra i due runtime, e si è già molto bene del termine 'binding' che consente le API Mac native da usare in Xamarin. Le associazioni sono illustrate in dettaglio nel [documentazione di binding di Objective-C](~/mac/platform/binding.md), pertanto per il momento, vediamo come funziona xamarin. Mac dietro le quinte.

In primo luogo, deve esistere un modo per esporre Objective-C in C#, che viene eseguita tramite i selettori. Un selettore è un messaggio che viene inviato a un oggetto o classe. Con Objective-C questa operazione viene eseguita tramite il [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) funzioni. Per altre informazioni sull'uso dei selettori, fare riferimento a iOS [selettori Objective-C](~/ios/internals/objective-c-selectors.md) Guida. È anche con un metodo per esporre il codice gestito a Objective-C, che è più complicato dovuto al fatto che Objective-C non conosce il codice gestito. Per evitare questo problema, si usa un' [registrar](~/mac/internals/registrar.md). Questo è illustrato più dettagliatamente nella sezione successiva.

## <a name="registrar"></a>Registrar

Come indicato in precedenza, il programma di registrazione è codice che espone il codice gestito per Objective-C. A tale scopo, si crea un elenco di ogni classe gestita che deriva da NSObject:

- Per tutte le classi che non esegue il wrapping di una classe di Objective-C esistente, creata una nuova classe di Objective-C con i membri di Objective-C mirroring tutti i membri gestiti che hanno un `[Export]` attributo.
- Nelle implementazioni per ogni membro di Objective-C, viene aggiunto automaticamente codice per chiamare il membro gestito con mirroring.

Il pseudo-codice seguente viene illustrato un esempio di questa procedura:

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

Il codice gestito può contenere gli attributi `[Register]` e `[Export]`, che usa le registrar per sapere che l'oggetto deve essere esposto per Objective-C. L'attributo [registrare] consente di specificare il nome della classe Objective-C generata nel caso in cui il nome generato predefinito non è adatto. Tutte le classi derivate da NSObject vengono registrate automaticamente in Objective-C. L'attributo [esportazione] necessario contiene una stringa che rappresenta il selettore usato nella classe generata Objective-C.

Esistono due tipi di registri usati in xamarin. Mac: statiche e dinamiche:

- Registrar dinamica – questo è il programma di registrazione predefinito per tutte le compilazioni di xamarin. Mac. Il programma di registrazione dinamica esegue la registrazione di tutti i tipi nell'assembly in fase di esecuzione. Ciò avviene usando funzioni fornite dalle API di runtime di Objective-C. Il programma di registrazione dinamica ha pertanto un avvio più lento, ma un veloce fase di compilazione. Le funzioni native (in genere in C), chiamate trampolines, vengono utilizzate come implementazioni del metodo quando si usano le Registrar dinamico. Variano tra architetture diverse.
- Registrar statico: il programma di registrazione statico genera il codice Objective-C durante la compilazione, che viene quindi compilata in una libreria statica e collegata nel file eseguibile. Questo consente un avvio più veloce, ma richiede più tempo durante la fase di compilazione.

## <a name="application-launch"></a>Avvio dell'applicazione

La logica di avvio di xamarin. Mac variano a seconda se incorporato o sistema Mono viene usato. Per visualizzare il codice e i passaggi per l'avvio dell'applicazione xamarin. Mac, vedere la [lancio intestazione](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) file nel repository pubblico macios di xamarin.

## <a name="generator"></a>Generator

Xamarin. Mac contiene definizioni per tutte le API Mac. È possibile esplorare tramite uno di questi sul [repository github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Queste definizioni contengono si interfaccia con attributi, nonché qualsiasi proprietà e metodi necessari. Ad esempio, il codice seguente consente di definire un NSBox nel [dello spazio dei nomi di AppKit](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Si noti che è un'interfaccia con un numero di metodi e proprietà:

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

Il generatore, chiamato `bmac` in xamarin. Mac, accetta questi file di definizione e Usa gli strumenti di .NET per compilarli in un assembly temporaneo. Tuttavia, questo assembly temporaneo non è facile da usare per chiamare codice Objective-C. Il generatore, quindi legge l'assembly temporaneo e viene generato l'errore C# codice che può essere usato in fase di esecuzione. Questo è il motivo per cui, ad esempio, se si aggiunge un attributo casuale per il file con estensione cs della definizione, non verrà visualizzati nel codice di output. Il generatore non è conoscenza e pertanto `bmac` all'oscuro da cercare nell'assembly temporaneo può restituirlo.

Una volta di xamarin è stato creato, il packager `mmp`, verranno riunire tutti i componenti di elementi.

A livello generale, raggiunge questo eseguendo le attività seguenti:

- Creare una struttura di bundle dell'app.
- Copiare negli assembly gestiti.
- Se il collegamento è abilitato, eseguire il linker gestito per ottimizzare gli assembly rimuovendo le parti non utilizzate.
- Creare un'applicazione di utilità di avvio, il collegamento nel codice dell'utilità di avvio parlato con il codice di registro se in modalità statica.

Si tratta quindi Esegui come parte dell'utente che fanno riferimento a xamarin ed esecuzioni processo che viene compilato il codice utente in un assembly di compilazione. `mmp` per renderlo un pacchetto

Per ulteriori informazioni sulla modalità di utilizzo e il linker, fare riferimento a iOS [Linker](~/ios/deploy-test/linker.md) Guida.

## <a name="summary"></a>Riepilogo

Questa Guida preso in esame la compilazione di App xamarin. Mac e xamarin. Mac esplorati e sua relazione con Objective-C.
