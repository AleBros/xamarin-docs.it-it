---
title: Matrice di errori di xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: b51e8b3d931ccbb511afe7d06d9be66fa104fb46
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121841"
---
# <a name="xamarinandroid-errors-matrix"></a>Matrice di errori di xamarin. Android

## <a name="errors-reference"></a>Riferimento per gli errori

Questo documento fornisce alcune informazioni sui codici di errore diversi da Xamarin.

|Category|Descrizione|
|--- |--- |
|XA0xxx|mandroid errori|
|XA1xxx|Copia del file o i collegamenti simbolici (progetto correlato) errori|
|XA2xxx|Errori del linker|
|XA3xxx|Errori AOT|
|XA4xxx|Errori di generazione di codice|
|XA5xxx|GCC e gli errori di Toolchain|
|XA6xxx|errori interni degli strumenti di mandroid|
|XA7xxx|Riservata|
|XA8xxx|Riservata|
|XA9xxx|Errori di licenza|


## <a name="error-codes"></a>Codici di errore

### <a name="xa0xxx-errors"></a>Errori XA0xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA0000|Errore imprevisto. specificare una [report sui bug](http://bugzilla.xamarin.com).|
|XA0001|'-devname è stato specificato senza l'intervento specifico del dispositivo.|
|XA0002|Non è stato possibile analizzare la variabile di ambiente '{0}'.|
|XA0003|Nome dell'applicazione '{0}.exe' è in conflitto con un nome di assembly (DLL) SDK o il prodotto.|
|XA0004|Nuova logica di refcounting richiede sgen deve essere abilitata troppo.|
|XA0005|La directory di output '{0}' non esiste.|
|XA0006|Non vi è alcuna piattaforma di sviluppo per sistemi operativi '{0}', usare: piattaforma = PLAT per specificare il SDK|
|XA0007|L'assembly radice '{0}' non esiste.|
|XA0008|È necessario fornire solo un unico assembly radice.|
|XA0009|Errore durante il caricamento degli assembly: {0}.|
|XA0010|Non è riuscito ad analizzare gli argomenti della riga di comando: {0}.|
|XA0011|{0} è stato creato un runtime più recente ({1}) rispetto a quello supportato MonoTouch.|
|XA0012|Vengono forniti dati incompleti per completare '{0}'.|
|XA0013|Il supporto della profilatura richiede sgen deve essere abilitata troppo.|
|XA0014|iOS {0} nepodporuje compilazione di applicazioni destinate a ARMv6.|
|XA0020|Impossibile determinare il percorso di mandroid.|
|XA0100|EmbeddedNativeLibrary '{0}' non è valido nel progetto di applicazione Android. Usare invece AndroidNativeLibrary.|

### <a name="xa1xxx-errors"></a>Errori XA1xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA1001|Impossibile trovare un'applicazione nella directory specificata.|
|XA1002|Non è possibile creare collegamenti simbolici, sono stati copiati i file.|
|XA1003|Non è stato possibile terminare l'applicazione '{0}'. Potrebbe essere necessario terminare manualmente l'applicazione.|
|XA1004|Impossibile ottenere l'elenco delle applicazioni installate.|
|XA1005|Non è stato possibile terminare l'applicazione '{0}'nel dispositivo'{1}': {2}. Potrebbe essere necessario terminare manualmente l'applicazione.|
|XA1006|Non è stato possibile installare l'applicazione '{0}'nel dispositivo'{1}': {2}.|
|XA1007|Non è stato possibile avviare l'applicazione '{0}'nel dispositivo'{1}': {2}. È comunque possibile avviare l'applicazione manualmente toccandola.|
|XA1008|Impossibile avviare il simulatore: {0}.|
|XA1009|Impossibile copiare l'assembly '{0}'a'{1}': {2}.|
|XA1010|Non è riuscito a caricare l'assembly '{0}': {1}.|
|XA1011|Impossibile aggiungere il file di risorsa mancante: '{0}'.|
|XA1101|Non è stato possibile avviare l'app.|
|XA1102|Impossibile connettersi all'app (per terminarlo): {0}.|
|XA1103|Impossibile scollegare.|
|XA1104|Non è riuscito a inviare il pacchetto: {0}.|
|XA1105|Tipo di risposta non prevista.|
|XA1106|Impossibile ottenere l'elenco delle applicazioni nel dispositivo: timeout della richiesta.|
|XA1107|Impossibile avviare l'applicazione.|
|XA1201|Non è riuscito a caricare il simulatore: {0}.|
|XA1301|La libreria nativa di '{0}' ({1}) è stata ignorata perché non corrisponde la architecture(s) compilazione corrente ({2}).|

### <a name="xa2xxx-errors"></a>Errori XA2xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA2001|Non è possibile collegare gli assembly.|
|XA2002|Non può risolvere il riferimento: {0}.|
|XA2003|Opzione '{0}' verrà ignorato perché il collegamento è disabilitato.|
|XA2004|File di definizione del linker extra '{0}' non è stata trovata.|
|XA2005|Le definizioni da '{0}' nelze analyzovat.|
|XA2006|Riferimento all'elemento dei metadati '{0}' (definito in '{1}') da '{2}' non può essere risolto.|

### <a name="xa3xxx-errors"></a>Errori XA3xxx

Si tratta di errori AOT.

|Codice di errore|Descrizione|
|--- |--- |
|XA3001|Potrebbe non AOT di assembly '{0}'.|
|XA3002|Restrizione di AOT: metodo '{0}' deve essere statico poiché è decorata con [MonoPInvokeCallback].|
|XA3003|In conflitto, le opzioni di debug e - llvm. Disabilitazione del debug soft.|


### <a name="xa4xxx-errors"></a>Errori XA4xxx

Si tratta di errori di generazione di codice.

|Codice di errore|Descrizione|
|--- |--- |
|XA4001|Il modello principale non è stato possibile expansed a '{0}'.|
|XA4101|Il programma di registrazione non è possibile generare una firma per il tipo '{0}'.|
|XA4102|Il programma di registrazione trovato un tipo non valido '{0}'nella firma del metodo'{2}'. Usare '{1}' invece.|
|XA4103|Il programma di registrazione trovato un tipo non valido '{0}'nella firma del metodo'{2}': il tipo implementa INativeObject, ma non dispone di un costruttore che accetta due (IntPtr, bool) argomenti.|
|XA4104|Il programma di registrazione non è possibile effettuare il marshalling il valore restituito per il tipo '{0}'nella firma del metodo'{1}'.|
|XA4105|Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo '{0}'nella firma del metodo'{1}'.|
|XA4106|Il programma di registrazione non è possibile effettuare il marshalling il valore restituito per la struttura '{0}'nella firma del metodo'{1}'.|
|XA4107|Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo '{0}'nella firma del metodo'{1}'.|
|XA4108|Il programma di registrazione non è possibile ottenere il tipo ObjectiveC per tipo gestito '{0}'.|
|XA4109|Impossibile compilare il codice di registrar generato. Inviare un [report sui bug](http://bugzilla.xamarin.com).|
|XA4110|Il programma di registrazione non è possibile effettuare il marshalling il parametro out di tipo '{0}'nella firma del metodo'{1}'.|
|XA4111|Il programma di registrazione non è possibile generare una firma per il tipo '{0}'nel metodo'{1}'.|
|XA4200|Può generare solo del ACW per i tipi 'claas'.|
|XA4201|Impossibile determinare il nome JNI per tipo {0}.|
|XA4203|Il nome del tipo specificato deve essere completo.|
|XA4204|Impossibile risolvere il tipo di interfaccia '{0}'. Probabilmente manca un riferimento a un assembly.|
|XA4205|[ExportField] sono utilizzabili solo su metodi con 0 parametri.|
|XA4206|[Esportazione] non può essere utilizzata su un tipo generico.|
|XA4207|[ExportField] non può essere usato su un tipo generico.|
|XA4208|[Java.Interop.ExportFieldAttribute] non può essere usato in un metodo che restituisce void.|
|XA4209|Non è riuscito a creare JavaTypeInfo per classe: {0} a causa dell'errore {1}.|
|XA4210|È necessario aggiungere un riferimento a Mono.Android.Export.dll quando si usa ExportAttribute o ExportFieldAttribute.|
|XA4211|Androidmanifest. XML //uses-sdk/@android:targetSdkVersion »{0}'è minore di $(TargetFrameworkVersion)'{1}'. Utilizzo API -{1} per la compilazione ACW.|


### <a name="xa5xxx-errors"></a>Errori XA5xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA5101|Mancante '{0}' compilatore. Installare Android NDK.|
|XA5102|Conversione da assembly in codice nativo non è riuscita. Inviare un [report sui bug](http://bugzilla.xamarin.com).|
|XA5103|Impossibile compilare il file '{0}'. Inviare un [report sui bug](http://bugzilla.xamarin.com).|
|XA5201|Collegamento nativa non è riuscita. Leggere attentamente contrassegni utente forniti da gcc: {0}|
|XA5202|Collegamento nativa non è riuscita. Esaminare il log di compilazione.|
|XA5303|Collegamento avviso nativo: {0}.|
|XA5300|Android SDK non trovato o non completamente installato.|
|XA5301|Strumento 'striscia' mancante. Installare il componente 'Strumenti da riga di comando' Xcode.|
|XA5302|Strumento 'dsymutil' mancante. Installare il componente 'Strumenti da riga di comando' Xcode.|
|XA5203|Impossibile generare i simboli di debug (directory di dSYM). Esaminare il log di compilazione.|
|XA5204|Non è stato possibile rimuovere il file binario finale. Esaminare il log di compilazione.|
|XA5205|Strumento 'aapt' mancante. Installare il pacchetto di Android SDK Build tools.|
|XA5206|{0}. Directory delle risorse Android {1} non esiste.|
|XA5207|{0}. File della libreria Java {1} non esiste.|
|XA5208|Download non riuscito. È necessario scaricare {0} e inserirlo il {1} directory.|
|XA5209|Durante la decompressione non riuscita. È necessario scaricare {0} ed estrarre i file per il {1} directory.|
|XA5210|{0}. File della libreria nativa {1} non esiste.|

### <a name="xa6xxx-errors"></a>Errori XA6xxx

|Codice di errore|Descrizione|
|--- |--- |
|XA6001|Versione in esecuzione di Cecil non supporta la rimozione dei assembly.|
|XA6002|Non è stato possibile rimuovere assembly '{0}'.|
|XA6003|UnauthorizedAccessException messaggio.|

### <a name="xa9xxx-errors"></a>Errori XA9xxx

Questi codici di errore sono errori di attivazione e gestione delle licenze.


#### <a name="xa9000"></a>XA9000

 **Causa:** licenza scaduta

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|AVVISO|AVVISO|AVVISO|AVVISO|AVVISO|


#### <a name="xa9001"></a>XA9001

 **Causa:** versione di valutazione scaduta

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|AVVISO|AVVISO|AVVISO|AVVISO|AVVISO|



#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|

 **Causa:** AndroidJavaLibrary

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|

 **Se un assembly di Binding è il JAR incorporati, questo viene rilevato in fase di pacchetto, non in fase di compilazione.**

 **Causa:** AndroidNativeLibrary

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|


#### <a name="xa9003"></a>XA9003

 **Causa:** Serialization

 **Controllati durante:** pacchetto

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|

 **Causa:** ServiceModel

 **Controllati durante:** pacchetto

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|

 **Causa:** Mono.Data.Tds

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|

 **Ciò viene fatto riferimento dal System, che è consentito**

 **Causa:** Mono.Android.Export

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|OK|OK|OK|



#### <a name="xa9004"></a>XA9004

 **Causa:** -profilatura

 **Controllati durante:** pacchetto

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|



#### <a name="xa9005"></a>XA9005

 **Causa:** dimensione massima (32 kb).

 **Controllati durante:** pacchetto

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|OK|-|-|-|



#### <a name="xa9006"></a>XA9006

 **Causa:** dello spazio dei nomi SqlClient.

 **Controllati durante:** pacchetto

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|


#### <a name="xa9008"></a>XA9008

 **Causa:** compilazione dalla riga di comando.

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|OK|OK|OK|


#### <a name="xa9009"></a>XA9009

 **Causa:** mancante il numero di serie.

 **Controllati durante:** Untestable

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId non valido.

 **Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|

Equivalente allo XA9018.



#### <a name="xa9011"></a>XA9011

 **Causa:** non è stato possibile aggiornare il file di licenza (al nuovo formato di file).

 **Controllati durante:** attivazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|

#### <a name="xa9012"></a>XA9012

 **Causa:** internet assente

 **Controllati durante:** attivazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9013"></a>XA9013

 **Causa:** errore sconosciuto

 **Controllati durante:** attivazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9014"></a>XA9014

 **Causa:** codice di attivazione non è valido

 **Controllati durante:** attivazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9017"></a>XA9017

 **Causa:** server di attivazione non restituisce una licenza valida.

 **Controllati durante:** attivazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|ERRORE|ERRORE|ERRORE|ERRORE|ERRORE|


#### <a name="xa9018"></a>XA9018

**Causa:** licenza non valida

**Controllati durante:** compilazione

|Starter|Indie|Business(trial)|Ufficio|Enterprise|
|--- |--- |--- |--- |--- |
|-|-|ERRORE|-|-|

