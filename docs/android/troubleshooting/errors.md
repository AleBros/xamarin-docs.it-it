---
title: Matrice di errori Novell. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 037b5a8939ab5866a10e7ecc58a1e2a5bdfb5bfd
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757389"
---
# <a name="xamarinandroid-errors-matrix"></a>Matrice di errori Novell. Android

## <a name="errors-reference"></a>Riferimento agli errori

Questo documento fornisce alcune informazioni sui diversi codici di errore di Novell.

|Category|Descrizione|
|--- |--- |
|XA0xxx|Errori dei Mandroidi|
|XA1xxx|Errori di copia del file/collegamenti simbolici (correlati al progetto)|
|XA2xxx|Errori del linker|
|XA3xxx|Errori AOT|
|XA4xxx|Errori di generazione del codice|
|XA5xxx|Errori GCC e.|
|XA6xxx|errori degli strumenti interni di dei Mandroidi|
|XA7xxx|Riservato|
|XA8xxx|Riservato|
|XA9xxx|Errori di licenza|

## <a name="error-codes"></a>Codici di errore

### <a name="xa0xxx-errors"></a>Errori XA0xxx

|Codice di errore|DESCRIZIONE|
|--- |--- |
|XA0000|Errore imprevisto. compilare un [report sui bug](https://github.com/xamarin/xamarin-android/issues/new).|
|XA0001|'-DEVNAME è stato fornito senza alcuna azione specifica del dispositivo.|
|XA0002|Non è stato possibile analizzare la variabile{0}di ambiente ''.|
|XA0003|Il nome dell'{0}applicazione '. exe ' è in conflitto con un nome di SDK o un assembly del prodotto (con estensione dll).|
|XA0004|La nuova logica refcounting richiede l'abilitazione di SGen.|
|XA0005|La directory di output{0}'' non esiste.|
|XA0006|Non esiste alcuna piattaforma devel in '{0}', usare--Platform = Plat per specificare l'SDK|
|XA0007|L'assembly radice '{0}' non esiste.|
|XA0008|È necessario specificare solo un assembly radice.|
|XA0009|Errore durante il caricamento degli {0}assembly:.|
|XA0010|Non è stato possibile analizzare gli argomenti della {0}riga di comando:.|
|XA0011|{0}è stato creato in base a un runtime{1}più recente () rispetto al supporto MonoTouch.|
|XA0012|Per completare '{0}' sono disponibili dati incompleti.|
|XA0013|Il supporto per la profilatura richiede l'abilitazione di SGen.|
|XA0014|iOS {0} non supporta la compilazione di applicazioni destinate a ARMv6.|
|XA0020|Non è stato possibile determinare il percorso di dei Mandroidi.|
|XA0100|EmbeddedNativeLibrary '{0}' non è valido nel progetto di applicazione Android. Usare invece AndroidNativeLibrary.|

### <a name="xa1xxx-errors"></a>Errori XA1xxx

|Codice di errore|DESCRIZIONE|
|--- |--- |
|XA1001|Impossibile trovare un'applicazione nella directory specificata.|
|XA1002|Impossibile creare i collegamenti simbolici. i file sono stati copiati.|
|XA1003|Non è stato possibile terminare l'{0}applicazione ''. Potrebbe essere necessario terminare l'applicazione manualmente.|
|XA1004|Non è stato possibile ottenere l'elenco delle applicazioni installate.|
|XA1005|Non è stato possibile terminare l'{0}applicazione '' nel dispositivo{1}'' {2}:. Potrebbe essere necessario terminare l'applicazione manualmente.|
|XA1006|Non è stato possibile installare l'{0}applicazione '' nel dispositivo{1}'' {2}:.|
|XA1007|Non è stato possibile avviare l'{0}applicazione '' nel dispositivo{1}'' {2}:. È comunque possibile avviare l'applicazione manualmente toccando.|
|XA1008|Non è stato possibile avviare il {0}simulatore:.|
|XA1009|Non è stato possibile copiare l'{0}assembly ''{1}in ' {2}':.|
|XA1010|Non è stato possibile caricare l'{0}assembly ' {1}':.|
|XA1011|Non è stato possibile aggiungere il file di{0}risorse mancante:''.|
|XA1101|Non è stato possibile avviare l'app.|
|XA1102|Non è stato possibile connettersi all'app (per terminarla) {0}:.|
|XA1103|Non è stato possibile scollegare.|
|XA1104|Non è stato possibile inviare {0}il pacchetto:.|
|XA1105|Tipo di risposta imprevisto.|
|XA1106|Non è stato possibile ottenere l'elenco delle applicazioni nel dispositivo: Timeout della richiesta.|
|XA1107|Non è stato possibile avviare l'applicazione.|
|XA1201|Non è stato possibile caricare il {0}simulatore:.|
|XA1301|La libreria nativa{0}''{1}() è stata ignorata perché non corrisponde alle architetture di compilazione correnti ({2}).|

### <a name="xa2xxx-errors"></a>Errori XA2xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA2001|Non è stato possibile collegare gli assembly.|
|XA2002|Non è possibile risolvere il {0}riferimento:.|
|XA2003|L'opzione{0}'' verrà ignorata perché il collegamento è disabilitato.|
|XA2004|Impossibile trovare il file di definizioni{0}del linker aggiuntivo ''.|
|XA2005|Non è stato{0}possibile analizzare le definizioni di ''.|
|XA2006|Non è stato possibile risolvere{0}il riferimento all'elemento dei{1}metadati '' ({2}definito in '') da' '.|

### <a name="xa3xxx-errors"></a>Errori XA3xxx

Si tratta di errori AOT.

|Codice di errore|Descrizione|
|--- |--- |
|XA3001|Non è stato possibile AOT l'{0}assembly ''.|
|XA3002|Restrizione AOT: Il metodo{0}'' deve essere statico perché è decorato con [MonoPInvokeCallback].|
|XA3003|Conflitto tra le opzioni--debug e--LLVM. Il debug Soft è disabilitato.|

### <a name="xa4xxx-errors"></a>Errori XA4xxx

Si tratta di errori di generazione del codice.

|Codice di errore|Descrizione|
|--- |--- |
|XA4001|Il modello principale non può essere distesa con '{0}'.|
|XA4101|Il registrar non è in grado di compilare{0}una firma per il tipo ''.|
|XA4102|Il Registrar ha trovato un tipo{0}non valido '' nella firma{2}per il metodo ''. In alternativa{1}, usare ''.|
|XA4103|Il Registrar ha trovato un tipo{0}non valido '' nella firma{2}per il metodo '': Il tipo implementa INativeObject, ma non dispone di un costruttore che accetta due argomenti (IntPtr, bool).|
|XA4104|Il registrar non può effettuare il marshalling del{0}valore restituito per il tipo '{1}' nella firma per il metodo ''.|
|XA4105|Il registrar non è in grado di effettuare{0}il marshalling del parametro di{1}tipo '' nella firma per il metodo ''.|
|XA4106|Il registrar non può effettuare il marshalling del{0}valore restituito per la struttura '{1}' nella firma per il metodo ''.|
|XA4107|Il registrar non è in grado di effettuare{0}il marshalling del parametro di{1}tipo '' nella firma per il metodo ''.|
|XA4108|Il registrar non è in grado di ottenere il tipo{0}ObjectiveC per il tipo gestito ''.|
|XA4109|Impossibile compilare il codice del registrar generato. Inviare un [report sui bug](http://bugzilla.xamarin.com).|
|XA4110|Il registrar non è in grado di effettuare il{0}marshalling del parametro out di{1}tipo '' nella firma per il metodo ''.|
|XA4111|Il registrar non è in grado di compilare{0}una firma per il{1}tipo '' nel metodo ''.|
|XA4200|Può generare solo ACW per i tipi ' Claas '.|
|XA4201|Impossibile determinare il nome JNI per il {0}tipo.|
|XA4203|Il nome del tipo specificato deve essere completo.|
|XA4204|Non è possibile risolvere il tipo{0}di interfaccia ''. Probabilmente manca un riferimento a un assembly.|
|XA4205|[ExportField] può essere utilizzato solo con i metodi con parametri 0.|
|XA4206|[Export] non può essere usato in un tipo generico.|
|XA4207|Impossibile utilizzare [ExportField] in un tipo generico.|
|XA4208|[Java. Interop. ExportFieldAttribute] non può essere usato in un metodo che restituisce void.|
|XA4209|Non è stato possibile creare JavaTypeInfo per {0} la classe {1}: a causa di.|
|XA4210|È necessario aggiungere un riferimento a mono. Android. Export. dll quando si usa ExportAttribute o ExportFieldAttribute.|
|XA4211|File AndroidManifest. XML //uses-sdk/@android:targetSdkVersion '{0}' è minore di $ (TargetFrameworkVersion)'{1}'. Uso{1} dell'API per la compilazione di ACW.|

### <a name="xa5xxx-errors"></a>Errori XA5xxx

|Codice di errore|DESCRIZIONE|
|--- |--- |
|XA5101|Manca il{0}compilatore ''. Installare NDK Android.|
|XA5102|La conversione dall'assembly al codice nativo non è riuscita. Inviare un [report sui bug](http://bugzilla.xamarin.com).|
|XA5103|Impossibile compilare il file '{0}'. Inviare un [report sui bug](http://bugzilla.xamarin.com).|
|XA5201|Il collegamento nativo non è riuscito. Verificare i flag utente forniti a GCC:{0}|
|XA5202|Il collegamento nativo non è riuscito. Verificare il log di compilazione.|
|XA5303|Avviso di collegamento nativo: {0}.|
|XA5300|Android SDK non è stato trovato o non è stato installato completamente.|
|XA5301|Manca lo strumento "strip". Installare il componente "strumenti da riga di comando" di Xcode.|
|XA5302|Manca lo strumento ' dsymutil '. Installare il componente "strumenti da riga di comando" di Xcode.|
|XA5203|Non è stato possibile generare i simboli di debug (directory dSYM). Verificare il log di compilazione.|
|XA5204|Non è stato possibile rimuovere il file binario finale. Verificare il log di compilazione.|
|XA5205|Manca lo strumento ' AAPT '. Installare il pacchetto di Android SDK build-Tools.|
|XA5206|[https://login.microsoftonline.com/consumers/]({0}). La directory {1} delle risorse Android non esiste.|
|XA5207|[https://login.microsoftonline.com/consumers/]({0}). Il file {1} della libreria Java non esiste.|
|XA5208|Download non riuscito. Scaricarlo {0} e inserirlo {1} nella directory.|
|XA5209|Decompressione non riuscita. Scaricarlo {0} ed estrarlo {1} nella directory.|
|XA5210|[https://login.microsoftonline.com/common/]({0}). Il file {1} della libreria nativa non esiste.|

### <a name="xa6xxx-errors"></a>Errori XA6xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA6001|La versione in esecuzione di Cecil non supporta la rimozione degli assembly.|
|XA6002|Impossibile rimuovere l'assembly '{0}'.|
|XA6003|Messaggio UnauthorizedAccessException.|

### <a name="xa9xxx-errors"></a>Errori XA9xxx

Questi codici di errore sono gli errori di licenza e di attivazione.

#### <a name="xa9000"></a>XA9000

 **Causa:** Licenza scaduta

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9001"></a>XA9001

 **Causa:** Versione di prova scaduta

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Causa:** AndroidJavaLibrary

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Se un assembly di associazione ha il file con estensione jar incorporato, viene intercettato in fase di pacchetto, non in fase di compilazione.**

 **Causa:** AndroidNativeLibrary

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9003"></a>XA9003

 **Causa:** System.Runtime.Serialization

 **Verificato durante:** Pacchetto

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Causa:** System.ServiceModel.Web

 **Verificato durante:** Pacchetto

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Causa:** Mono.Data.Tds

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **A cui fa riferimento System. Data. dll, che è consentito**

 **Causa:** Mono.Android.Export

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9004"></a>XA9004

 **Motivo:** --profilatura

 **Verificato durante:** Pacchetto

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9005"></a>XA9005

 **Causa:** Limite dimensioni (32 KB).

 **Verificato durante:** Pacchetto

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|-|-|-|

#### <a name="xa9006"></a>XA9006

 **Causa:** Spazio dei nomi System. Data. SqlClient.

 **Verificato durante:** Pacchetto

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9008"></a>XA9008

 **Causa:** Compilazione dalla riga di comando.

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9009"></a>XA9009

 **Causa:** Numero di serie mancante.

 **Verificato durante:** Untestable

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId non valido.

 **Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Equivalente a XA9018.

#### <a name="xa9011"></a>XA9011

 **Causa:** Non è stato possibile aggiornare il file di licenza (in un nuovo formato di file).

 **Verificato durante:** Attivazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Causa:** Nessun Internet

 **Verificato durante:** Attivazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9013"></a>XA9013

 **Causa:** Errore sconosciuto

 **Verificato durante:** Attivazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9014"></a>XA9014

 **Causa:** Codice di attivazione non valido

 **Verificato durante:** Attivazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9017"></a>XA9017

 **Causa:** Il server di attivazione non restituisce una licenza valida.

 **Verificato durante:** Attivazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9018"></a>XA9018

**Causa:** Licenza non valida

**Verificato durante:** Compilazione

|Starter|Indie|Business (versione di valutazione)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|
