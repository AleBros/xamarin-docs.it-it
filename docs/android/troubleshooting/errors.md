---
title: Matrice di errori di xamarin
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: e9916d8e264c202a914e6fd70e664beea276e93d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774148"
---
# <a name="xamarinandroid-errors-matrix"></a>Matrice di errori di xamarin

## <a name="errors-reference"></a>Riferimento per gli errori

Questo documento fornisce alcune informazioni sui codici di errore diversi da Xamarin.

|Category|Descrizione|
|--- |--- |
|XA0xxx|mandroid errori|
|XA1xxx|Copia del file o collegamenti simbolici (progetto correlato) errori|
|XA2xxx|Errori del linker|
|XA3xxx|Errori AOT|
|XA4xxx|Errori di generazione di codice|
|XA5xxx|GCC e gli errori Toolchain|
|XA6xxx|errori interni degli strumenti mandroid|
|XA7xxx|Riservata|
|XA8xxx|Riservata|
|XA9xxx|Errori di gestione delle licenze|


## <a name="error-codes"></a>Codici di errore

### <a name="xa0xxx-errors"></a>Errori XA0xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA0000|Errore imprevisto: compilare un [rapporto bug](http://bugzilla.xamarin.com).|
|XA0001|'-devname è stato specificato senza alcun intervento specifico del dispositivo.|
|XA0002|Impossibile analizzare la variabile di ambiente '{0}'.|
|XA0003|Applicazione '{0} .exe' conflitti di nome con un nome di assembly (DLL) SDK o prodotto.|
|XA0004|La nuova logica di refcounting richiede sgen deve essere abilitata troppo.|
|XA0005|La directory di output '{0}' non esiste.|
|XA0006|È presente alcuna piattaforma di sviluppo per sistemi operativi in '{0}', utilizzare - platform = PLAT per specificare il SDK|
|XA0007|L'assembly principale '{0}' non esiste.|
|XA0008|È necessario fornire solo un assembly principale.|
|XA0009|Errore durante il caricamento degli assembly: {0}.|
|XA0010|Non è riuscito ad analizzare gli argomenti della riga di comando: {0}.|
|XA0011|{0} è stato compilato con un runtime più recente (\\{1 \\}) rispetto a MonoTouch supportato.|
|XA0012|I dati incompleti viene forniti per completare '{0}'.|
|XA0013|Supporto di profilatura richiede sgen deve essere abilitata troppo.|
|XA0014|iOS {0} non supporta ARMv6 di destinazione di compilazione di applicazioni.|
|XA0020|Impossibile determinare il percorso mandroid.|
|XA0100|EmbeddedNativeLibrary '{0}' non è valido nel progetto di applicazione Android. Utilizzare AndroidNativeLibrary.|

### <a name="xa1xxx-errors"></a>Errori XA1xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA1001|Impossibile trovare un'applicazione nella directory specificata.|
|XA1002|Non è stato possibile creare collegamenti simbolici, sono stati copiati i file.|
|XA1003|Impossibile terminare l'applicazione '{0}'. È possibile terminare l'applicazione manualmente.|
|XA1004|Impossibile ottenere l'elenco delle applicazioni installate.|
|XA1005|Impossibile terminare l'applicazione '{0}' nel dispositivo '\\{1 \\}': {2}. È possibile terminare l'applicazione manualmente.|
|XA1006|Impossibile installare l'applicazione '{0}' nel dispositivo '\\{1 \\}': {2}.|
|XA1007|Non è stato possibile avviare l'applicazione '{0}' nel dispositivo '\\{1 \\}': {2}. È comunque possibile avviare manualmente l'applicazione toccando su di esso.|
|XA1008|Impossibile avviare il simulatore: {0}.|
|XA1009|Impossibile copiare l'assembly '{0}' a '\\{1 \\}': {2}.|
|XA1010|Non è stato possibile caricare l'assembly '{0}': \\{1 \\}.|
|XA1011|Impossibile aggiungere il file di risorsa mancante: '{0}'.|
|XA1101|Non è stato possibile avviare l'app.|
|XA1102|Impossibile connettersi all'app (per terminarlo): {0}.|
|XA1103|Impossibile scollegare.|
|XA1104|Impossibile inviare il pacchetto: {0}.|
|XA1105|Tipo di risposta non prevista.|
|XA1106|Impossibile ottenere l'elenco delle applicazioni nel dispositivo: timeout della richiesta.|
|XA1107|Impossibile avviare l'applicazione.|
|XA1201|Non è stato possibile caricare il simulatore: {0}.|
|XA1301|La libreria nativa di '{0}' (\\{1 \\}) è stata ignorata perché il architecture(s) compilazione corrente ({2}) non corrisponde.|

### <a name="xa2xxx-errors"></a>Errori XA2xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA2001|Non è possibile collegare gli assembly.|
|XA2002|Non può risolvere il riferimento: {0}.|
|XA2003|Opzione '{0}' verrà ignorato perché il collegamento è disabilitato.|
|XA2004|Impossibile individuare il file di definizione del linker aggiuntive '{0}'.|
|XA2005|Le definizioni di '{0}' non può essere analizzate.|
|XA2006|Riferimento all'elemento dei metadati '{0}' (definito in '\\{1 \\}') da '{2}' non è possibile risolvere.|

### <a name="xa3xxx-errors"></a>Errori XA3xxx

Si tratta di errori AOT.

|Codice di errore|Descrizione|
|--- |--- |
|XA3001|Impossibile non AOT l'assembly '{0}'.|
|XA3002|Restrizione AOT: metodo '{0}' deve essere statico poiché è decorata con [MonoPInvokeCallback].|
|XA3003|In conflitto: opzioni di debug e - llvm. Debug soft è disabilitato.|


### <a name="xa4xxx-errors"></a>Errori XA4xxx

Si tratta di errori di generazione di codice.

|Codice di errore|Descrizione|
|--- |--- |
|XA4001|Non è possibile expansed per '{0}' del modello principale.|
|XA4101|Il programma di registrazione non è possibile generare una firma per il tipo '{0}'.|
|XA4102|Il programma di registrazione trovato un tipo non valido '{0}' nella firma per il metodo '{2}'. In alternativa, usare '\\{1 \\}'.|
|XA4103|Il programma di registrazione trovato un tipo non valido '{0}' nella firma per il metodo '{2}': il tipo implementa INativeObject, ma non dispone di un costruttore che accetta due (IntPtr, bool) argomenti.|
|XA4104|Il programma di registrazione non è possibile effettuare il marshalling il valore restituito per il tipo '{0}' nella firma per il metodo '\\{1 \\}'.|
|XA4105|Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo '{0}' nella firma per il metodo "\\{1 \\}".|
|XA4106|Il programma di registrazione non è possibile effettuare il marshalling il valore restituito per la struttura '{0}' nella firma per il metodo '\\{1 \\}'.|
|XA4107|Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo '{0}' nella firma per il metodo "\\{1 \\}".|
|XA4108|Il programma di registrazione non è possibile ottenere il tipo di ObjectiveC per un tipo gestito '{0}'.|
|XA4109|Impossibile compilare il codice generato registrar. Inviare un [rapporto bug](http://bugzilla.xamarin.com).|
|XA4110|Il programma di registrazione non è possibile effettuare il marshalling il parametro out di tipo '{0}' nella firma per il metodo '\\{1 \\}'.|
|XA4111|Il programma di registrazione non è possibile generare una firma per il tipo '{0}' nel metodo '\\{1 \\}'.|
|XA4200|Può solo generare del ACW per i tipi 'claas'.|
|XA4201|Impossibile determinare il nome JNI per il tipo {0}.|
|XA4203|Nome del tipo specificato deve essere completo.|
|XA4204|Impossibile risolvere il tipo di interfaccia '{0}'. Probabilmente manca un riferimento a un assembly.|
|XA4205|[ExportField] sono utilizzabili solo in metodi con parametri, 0.|
|XA4206|[Esportazione] non può essere utilizzata su un tipo generico.|
|XA4207|[ExportField] può essere usato in un tipo generico.|
|XA4208|[Java.Interop.ExportFieldAttribute] può essere usato in un metodo che restituisce void.|
|XA4209|Impossibile creare JavaTypeInfo per classe: {0} a causa di \\{1 \\}.|
|XA4210|È necessario aggiungere un riferimento a Mono.Android.Export.dll quando si utilizza ExportAttribute o ExportFieldAttribute.|
|XA4211|AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}' è minore di $(TargetFrameworkVersion) '\\{1 \\}'. Utilizzando API-\  per la compilazione ACW.|


### <a name="xa5xxx-errors"></a>Errori XA5xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA5101|Manca un compilatore '{0}'. Installare Android NDK.|
|XA5102|Conversione da assembly in codice nativo non è riuscita. Inviare un [rapporto bug](http://bugzilla.xamarin.com).|
|XA5103|Impossibile compilare il file '{0}'. Inviare un [rapporto bug](http://bugzilla.xamarin.com).|
|XA5201|Collegamento nativo non è riuscita. Verificare i flag di utente per gcc: {0}|
|XA5202|Collegamento nativo non è riuscita. Esaminare il log di compilazione.|
|XA5303|Nativo collegamento avviso: {0}.|
|XA5300|Android SDK non trovato o non è completamente installato.|
|XA5301|Strumento 'striscia' mancante. Installare il componente 'Strumenti da riga di comando' Xcode.|
|XA5302|Strumento 'dsymutil' mancante. Installare il componente 'Strumenti da riga di comando' Xcode.|
|XA5203|Impossibile generare i simboli di debug (dSYM directory). Esaminare il log di compilazione.|
|XA5204|Impossibile rimuovere il file binario finale. Esaminare il log di compilazione.|
|XA5205|Strumento 'aapt' mancante. Installare il pacchetto di strumenti di compilazione di Android SDK.|
|XA5206|{0}. \\{1 \\} directory Android risorsa non esiste.|
|XA5207|{0}. Java \\{1 \\} file di libreria non esiste.|
|XA5208|Download non riuscito. Scaricare {0} e inserirlo nella directory \\{1 \\}.|
|XA5209|Decompressione non riuscita. Scaricare {0} e decomprimerlo nella directory \\{1 \\}.|
|XA5210|{0}. \\{1 \\} file libreria nativa non esiste.|

### <a name="xa6xxx-errors"></a>Errori XA6xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA6001|Versione in esecuzione di Cecil non supporta rimozione di assembly.|
|XA6002|Non può rimuovere assembly '{0}'.|
|XA6003|UnauthorizedAccessException messaggio.|

### <a name="xa9xxx-errors"></a>Errori XA9xxx

Questi codici di errore sono errori di licenze e l'attivazione.


#### <a name="xa9000"></a>XA9000

 **Causa:** licenza scaduta

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|AVVISO|AVVISO|AVVISO|AVVISO|AVVISO|


#### <a name="xa9001"></a>XA9001

 **Causa:** versione di valutazione scaduta

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|AVVISO|AVVISO|AVVISO|AVVISO|AVVISO|



#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|

 **Causa:** AndroidJavaLibrary

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|

 **Se un assembly di associazione ha il JAR incorporati, questo viene intercettato in fase di pacchetto, non in fase di compilazione.**

 **Causa:** AndroidNativeLibrary

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|


#### <a name="xa9003"></a>XA9003

 **Causa:** Serialization

 **Controllati durante:** pacchetto

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|

 **Causa:** System.ServiceModel.Web

 **Controllati durante:** pacchetto

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|

 **Causa:** Mono.Data.Tds

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|

 **Questo fa riferimento a System.Data.dll, che è consentito**

 **Causa:** Mono.Android.Export

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|



#### <a name="xa9004"></a>XA9004

 **Causa:** -profilo

 **Controllati durante:** pacchetto

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|



#### <a name="xa9005"></a>XA9005

 **Causa:** limite dimensione (32 kb).

 **Controllati durante:** pacchetto

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|-|-|-|



#### <a name="xa9006"></a>XA9006

 **Causa:** dello spazio dei nomi SqlClient.

 **Controllati durante:** pacchetto

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|


#### <a name="xa9008"></a>XA9008

 **Causa:** compilazione dalla riga di comando.

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|


#### <a name="xa9009"></a>XA9009

 **Causa:** il numero di serie mancante.

 **Controllati durante:** Untestable

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId non valido.

 **Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|

Equivale a XA9018.



#### <a name="xa9011"></a>XA9011

 **Causa:** non è stato possibile aggiornare il file di licenza (al nuovo formato di file).

 **Controllati durante:** attivazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|

#### <a name="xa9012"></a>XA9012

 **Causa:** internet non disponibile

 **Controllati durante:** attivazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9013"></a>XA9013

 **Causa:** errore sconosciuto

 **Controllati durante:** attivazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9014"></a>XA9014

 **Causa:** codice di attivazione non valido

 **Controllati durante:** attivazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9017"></a>XA9017

 **Causa:** server di attivazione non restituisce una licenza valida.

 **Controllati durante:** attivazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9018"></a>XA9018

**Causa:** licenza non valida

**Controllati durante:** di compilazione

|Starter|Immaginare|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|-|-|ERRORE|-|-|

