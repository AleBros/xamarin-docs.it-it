---
title: È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 8537e075c4ac1dba8362ec9ce5fc77ee0935503c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523297"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?

Per connettersi alla emulatore Android in esecuzione su un Mac da una macchina virtuale Windows, attenersi alla procedura seguente:

1. Avviare l'emulatore nel Mac.

2. Terminare il `adb` server nel Mac:

    ```bash
    adb kill-server
    ```

3. Si noti che l'emulatore è in ascolto su due porte TCP sull'interfaccia di rete loopback:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    Il numero di porta dispari è quello utilizzato per la connessione a `adb`. Vedere anche [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4. _Opzione 1_: Usare[`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    per inoltrare i pacchetti TCP in ingresso ricevuti esternamente sulla porta 5555 (o qualsiasi altra porta desiderata) alla porta con numero dispari sull'interfaccia di loopback (**127.0.0.1 5555** in questo esempio) e per inoltrare i pacchetti in uscita viceversa.

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Finché i `nc` comandi rimarranno in esecuzione in una finestra del terminale, i pacchetti verranno inoltrati come previsto. È possibile digitare Control-C nella finestra del terminale per uscire dai `nc` comandi una volta terminato l'uso dell'emulatore.

    L'opzione 1 è in genere più semplice dell'opzione 2, soprattutto se le **Preferenze di sistema > sicurezza & Privacy > firewall** è attivata. 

    _Opzione 2_: Usare[`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    per reindirizzare i pacchetti `5555` TCP dalla porta (o qualsiasi altra porta desiderata) nell'interfaccia di [rete condivisa](http://kb.parallels.com/en/4948) alla porta dispari sull'interfaccia di loopback (`127.0.0.1:5555` in questo esempio):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Questo comando imposta la porta di invio usando il `pf packet filter` servizio di sistema. Le interruzioni di riga sono importanti. Assicurarsi di mantenerli intatti quando si incolla la copia. Se si usano Parallels, sarà anche necessario modificare il nome dell'interfaccia da *vmnet8* . `vmnet8`è il nome del *dispositivo NAT* speciale per la modalità di *rete condivisa* in VMware Fusion. L'interfaccia di rete appropriata in Parallels è probabilmente [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Connettersi all'emulatore dal computer Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Sostituire "IP-address-of-the-Mac" con l'indirizzo IP del Mac, ad esempio elencato da `ifconfig vmnet8 | grep 'inet '`. Se necessario, sostituire `5555` con l'altra porta desiderata dal passaggio 4\. Nota: un modo per ottenere l'accesso da riga di comando `adb` a è tramite [**Strumenti > Android > prompt dei comandi ADB di Android**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) in Visual Studio.

### <a name="alternate-technique-using-ssh"></a>Tecnica alternativa con`ssh`

Se è stato abilitato _login remoto_ nel Mac, è possibile usare `ssh` la porta di trasmissione per connettersi all'emulatore.

1. Installare un client SSH in Windows. Un'opzione consiste nell'installare [git per Windows](https://git-for-windows.github.io/). Il `ssh` comando sarà quindi disponibile nel prompt dei comandi di **git bash** .

2. Seguire i passaggi 1-3 precedenti per avviare l'emulatore, terminare `adb` il server nel Mac e identificare le porte dell'emulatore.

3. Eseguire `ssh` su Windows per impostare la porta bidirezionale per l'invio tra una porta locale in Windows (`localhost:15555` in questo esempio) e la porta dell'emulatore dispari sull'interfaccia di loopback del Mac (`127.0.0.1:5555` in questo esempio):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Sostituire `mac-username` con il nome utente Mac come elencato `whoami`da. Sostituire `ip-address-of-the-mac` con l'indirizzo IP del Mac.

4. Connettersi all'emulatore usando la porta locale per Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    Nota: un modo semplice per ottenere l'accesso da riga di comando `adb` a è tramite [ **Strumenti > Android > prompt dei comandi ADB** di Android in Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).

Una piccola attenzione: se si usa la `5555` porta per la porta locale `adb` , si rileverà che l'emulatore è in esecuzione localmente su Windows. Questa operazione non causa problemi in Visual Studio, ma in Visual Studio per Mac causa la chiusura dell'app immediatamente dopo l'avvio.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>La tecnica alternativa `adb -H` che usa non è ancora supportata

In teoria, un altro approccio consiste nell'usare `adb`la funzionalità incorporata di per connettersi a un `adb` server in esecuzione in un computer remoto (vedere ad [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)esempio).
Tuttavia, le estensioni dell'IDE Novell. Android attualmente non forniscono un modo per configurare tale opzione.

## <a name="contact-information"></a>Informazioni di contatto

Questo documento illustra il comportamento attuale a partire da marzo 2016. La tecnica descritta in questo documento non fa parte del gruppo di test stabili per Novell, quindi potrebbe interrompersi in futuro.

Se si nota che la tecnica non funziona più o se si notano altri errori nel documento, è possibile aggiungere la discussione sul seguente thread del forum: [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Grazie

