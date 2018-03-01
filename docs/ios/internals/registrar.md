---
title: Registrazione di tipo
ms.topic: article
ms.prod: xamarin
ms.assetid: 4C1669A4-C12B-9C49-4A39-9046576D10DC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 32d29456b9880914f728b9e24fc0bfadb2f5efde
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="type-registrar"></a>Registrazione di tipo

Questo documento descrive il sistema di registrazione di tipo usato da xamarin. IOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registrazione di classi gestite e i metodi

Durante l'avvio, verrà registrato xamarin:

  - Le classi con un [[registrare]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) attributo come classi Objective-C.
  - Le classi con un [[Category]](https://developer.xamarin.com/api/type/CRuntime.CategoryAttribute) attributo come categorie Objective-C.
  - Interfaccia con un [[Protocol]](https://developer.xamarin.com/api/type/Foundation.ProtocolAttribute/) attributo come protocolli Objective-C.

in ogni membri casi con un [[esportare]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) attributo vengono esportati in Objective-C. In questo modo le classi gestite essere creati e gestiti i metodi da chiamare da Objective-C ed è il modo in cui sono collegate proprietà e metodi tra il mondo in c# e Objective-C uno.

Un esempio molto semplice è la classe AppDelegate dotato di tutte le applicazioni. Si ricorderà che il metodo Main gestito disponga di una riga come questa:

    UIApplication.Main (args, null, "AppDelegate");

In questo modo il runtime Objective-C per creare il tipo denominato "AppDelegate" come classe delegata dell'applicazione.  Per il runtime Objective-C imparare a creare un'istanza della classe "AppDelegate" scritta in c#, questa classe deve essere registrato.

Xamarin. IOS runtime si occuperà della registrazione, internamente, questa registrazione può essere effettuata interamente in fase di esecuzione (la registrazione dinamica) o può essere eseguita in fase di compilazione (registrazione statico).  L'approccio dinamico prevede l'utilizzo di reflection all'avvio per trovare tutte le classi e metodi per registrare e passare a quelli per il runtime Objective-C.  L'approccio statico controlla se gli assembly utilizzati dall'applicazione in fase di compilazione.  Determina le classi e metodi da registrare con Objective-C e genera una mappa a cui è incorporata in un file binario.  Quindi, all'avvio, registriamo la mappa con il runtime Objective-C.

### <a name="categories"></a>Categories

A partire da xamarin 8.10, sarà possibile creare categorie di Objective-C utilizzando la sintassi di c#.

Questa operazione viene eseguita utilizzando l'attributo [Category], specifica del tipo da estendere come un argomento dell'attributo.
Nell'esempio seguente viene estesa per l'istanza NSString:

    [Category (typeof (NSString))]

Ogni metodo categoria sta utilizzando il meccanismo normale per l'esportazione di metodi in Objective-C utilizzando l'attributo [Esporta]:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Tutti i metodi di estensione gestita devono essere statici, ma è possibile creare metodi di istanza Objective-C utilizzando la sintassi standard per i metodi di estensione in c#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

e il primo argomento al metodo di estensione sarà l'istanza in cui è stato richiamato il metodo.

Esempio completo:

    [Category (typeof (NSString))]
    public static class MyStringCategory
    {
        [Export ("toUpper")]
        static string ToUpper (this NSString self)
        {
            return self.ToString ().ToUpper ();
        }
    }

In questo esempio aggiungerà un metodo di istanza nativo toUpper alla classe NSString, che può essere richiamata da Objective-C.

    [Category (typeof (UIViewController))]
    public static class MyViewControllerCategory
    {
        [Export ("shouldAutoRotate")]
        static bool GlobalRotate ()
        {
            return true;
        }
    }

### <a name="protocols"></a>Protocolli

A partire da xamarin 8.10 interfacce con l'attributo [Protocol] verranno esportate in Objective-C come protocolli.

Esempio:

    [Protocol ("MyProtocol")]
    interface IMyProtocol
    {
        [Export ("method")]
        void Method ();
    }

    class MyClass : IMyProtocol
    {
        void Method ()
        {
        }
    }

Questo verrà esportato in Objective-C come un protocollo (Protocollo1) e una classe (MyClass) che implementa il protocollo.

 **Registrazione dinamica**

viene utilizzato per le compilazioni simulatore, come accelera il ciclo di compilazione/debug.  Questo è il risultato di eliminare i passaggi che genera il mapping di classe e la compilazione di questa tabella di mappa nell'utilità di avvio applicazione ogni volta che si avvia l'applicazione e un'utilità di avvio di uso generale viene invece utilizzato ogni volta.  Computer desktop con una notevole quantità di energia elettrica per il runtime di eseguire l'analisi delle classi rapidamente, pertanto le prestazioni non sono mai un problema.

 **Registrazione statico**

è progettata per le compilazioni di dispositivi come dispositivi mobili sono più lenti rispetto ai computer desktop e runtime di eseguire l'analisi è lenta.  Poiché si basa dispositivo sarà sempre necessario creare un nuovo file binario, il ciclo di compilazione/debug non è interessato dalla creazione del mapping di registrazione.

## <a name="new-registration-system"></a>Nuovo sistema di registrazione

A partire dal 6.2.6 stabile versione e la versione beta 6.3.4 è stato aggiunto il nuovo programma di registrazione statico. Il precedente punto 7.2.1 versione siamo registrar di nuovo il valore predefinito.

Questo nuovo sistema di registrazione offre le nuove funzionalità seguenti:

- La compilazione di rilevamento in fase di errori di programmazione:
    - Due classi, la registrazione con lo stesso nome.
    - Più di un metodo esportato per rispondere allo stesso selettore



- Possibile rimuovere il codice nativo non utilizzato
    - Il nuovo sistema di registrazione verrà aggiunti riferimenti forti al codice usato in librerie statiche, consentendo il linker nativo rimuovere un codice nativo non utilizzato dal file binario risulta.
      Nelle associazioni di esempio di Xamarin, la maggior parte delle applicazioni diventano più piccoli almeno 300 KB.

- Supporto per le sottoclassi generiche di NSObject. Vedere [NSObject Generics](~/ios/internals/api-design/nsobject-generics.md) per ulteriori informazioni. Inoltre, il nuovo sistema di registrazione rileva i costrutti generici non supportati che avrebbe causato precedentemente comportamento casuale in fase di esecuzione.

Ecco alcuni esempi degli errori per il nuovo registar rilevati:

Esportazione di più volte lo stesso selettore nella stessa classe.

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo:")]
    void Foo (NSString str);
    [Export ("foo:")]
    void Foo (string str)
}
```

Esportazione di più di una classe gestita con lo stesso nome Objective-C.

```csharp
[Register ("Class")]
class MyClass : NSObject {}

[Register ("Class")]
class YourClass : NSObject {}
```

Esportazione di metodi generici.

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo")]
    void Foo<T> () {}
}
```



Alcuni aspetti da tenere presenti sul nuovo registro:
- Alcune terze parti librerie devono essere aggiornate per funzionare con il nuovo sistema di registrazione, vedere la sezione [necessarie modifiche seguente](#required_modifications) per altri dettagli.
- È anche un svantaggio a breve termine che Clang deve essere utilizzata se viene utilizzato il framework di account (infatti intestazione accounts.h Apple può essere compilata solo Clang). Aggiungere <code>--compiler:clang</code> agli argomenti mtouch aggiuntive da utilizzare Clang se l'uso Xcode 4.6 o versione precedente (xamarin. IOS verrà automaticamente selezionare Clang in Xcode 5.0 o versione successiva).

    <li>Se Xcode 4.6 (o versioni precedenti) viene utilizzato, GCC / lettera G + + è necessario selezionare se il tipo esportato i nomi contengono caratteri non ascii (in questo modo la versione di Clang fornito con Xcode 4.6 non supporta caratteri non ascii all'interno degli identificatori nel codice Objective-C). Aggiungere <code>--compiler:gcc</code> agli argomenti aggiuntivi mtouch utilizzare GCC.


## <a name="selecting-a-registrar"></a>Selezionando un registrar

È possibile selezionare un altro registrar tramite l'aggiunta di una delle seguenti opzioni per gli argomenti aggiuntivi mtouch iOS del progetto le opzioni di compilazione:

-  `--registrar:static` : il valore predefinito per i dispositivi di compilazioni
-  `--registrar:dynamic` : il valore predefinito per compilazioni simulatore
-  `--registrar:legacystatic` : il valore predefinito per il dispositivo compila finché xamarin 7.2.1
-  `--registrar:legacydynamic` : il valore predefinito per il simulatore compila finché xamarin 7.2.1


## <a name="shortcomings-in-the-old-registration-system"></a>Molti punti deboli nel sistema di registrazione precedente

Il sistema di registrazione precedente presenta i seguenti svantaggi:

-  Si è verificato alcun riferimento statico (nativo) per Objective-C classi e metodi in librerie native di terze parti, questo significa che non chiediamo al linker native di rimuovere il codice nativo di terze parti che non è stata effettivamente utilizzato (in quanto tutti gli elementi verranno rimossi) che. Questo è il motivo per il "-force_load libNative.a" ogni binding di terze parti che doveva eseguire (o l'equivalente ForceLoad = true nell'attributo [LinkWith]).
-  È possibile esportare i due tipi gestiti con lo stesso nome Objective-C, senza alcun avviso. Uno scenario raro è che le due classi AppDelegate (in spazi dei nomi diversi). In fase di esecuzione sarebbe completamente casuali quello che è stato prelevato (infatti che modificata tra le esecuzioni di un'app che non è stato ricompilato anche - effettuate per un'esperienza di debug molto perplessità e frustrante).
-  È possibile esportare i due metodi con la stessa firma Objective-C. Ancora una volta quale deve essere chiamato da Objective-C è casuale, ma questo problema non è comune quanto quello precedente, principalmente perché l'unico modo per sfruttare in realtà questo bug è stata l'override del metodo gestito sfortunato.
-  Il set di metodi che è stato esportato è leggermente diverso tra compilazioni statiche e dinamiche.
-  Non funziona correttamente quando si esportano le classi generiche (quale implementazione generica esatta eseguita in fase di esecuzione sarà casuale, producendo un comportamento indeterminato).


 <a name="required_modifications" />


## <a name="new-registrar-required-changes-to-bindings"></a>Nuovo programma di registrazione: Le modifiche necessarie alle associazioni


Le associazioni esistenti Objective-C potrebbero dover essere aggiornato per funzionare con il nuovo registar.

Di seguito è riportato un elenco di modifiche che devono essere eseguite.

### <a name="protocols-must-have-the-protocol-attribute"></a>Protocolli devono avere l'attributo [Protocol]

Le implementazioni di protocolli è necessario ora l'attributo [Protocol] applicata ad essi.  Se non esegue questa operazione, sarà possibile errore del linker native come questa:

```csharp
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>I selettori devono avere un numero di parametri valido

Tutti i selettori di numero di parametri è necessario indicare correttamente.  In precedenza, questi errori sono stati ignorati e potrebbe causare problemi di runtime.

In breve, il numero di punti e deve corrispondere al numero di parametri.

Ad esempio:

-  Senza parametri: "foo"
-  Un parametro: "foo:'
-  Due parametri: ' foo:parameterName2:'


Di seguito sono gli usi:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Utilizzare il parametro IsVariadic nell'esportazione

Le funzioni Variadic devono detto prima il relativo attributo di esportazione (in questo modo il selettore non è corretto sul numero di argomenti, ad esempio punto 2. precedente viene violato):

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Deve essere collegata ai simboli esistenti

Non è possibile associare le classi che non sono presenti nella libreria nativa.

Se si tenta di associare le classi non esistente, si otterrà un errore del linker nativo.

Ciò si verifica quando un'associazione già esistente per un certo tempo e il codice nativo è stato modificato durante tale intervallo di tempo in modo che una determinata classe nativa è stato rimossa o rinominata, mentre l'associazione non è stato aggiornato.

