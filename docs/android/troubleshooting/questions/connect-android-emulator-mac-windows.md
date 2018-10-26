---
title: È possibile connettersi a emulatori Android in esecuzione in un computer Mac da una macchina virtuale Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 6e66bf4edb4269aa0f3b765df4a08b78c128f763
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115640"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>È possibile connettersi a emulatori Android in esecuzione in un computer Mac da una macchina virtuale Windows?

Per connettersi all'emulatore Android in esecuzione in un computer Mac da una macchina virtuale Windows, usare la procedura seguente:

1.  Avviare l'emulatore su Mac.

2.  Terminare il `adb` server nel computer Mac:

    ```bash
    adb kill-server
    ```

3.  Si noti che l'emulatore sia in ascolto su porte TCP 2 nell'interfaccia di rete loopback:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    La porta numero dispari è quello usato per connettersi a `adb`. Vedere anche [ http://developer.android.com/tools/devices/emulator.html#emulatornetworking ](http://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Opzione 1_: uso [`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    per inoltrare i pacchetti TCP in ingresso ricevuto dall'esterno sulla porta 5555 (o qualsiasi altra porta desiderato) alla porta dispari nell'interfaccia di loopback (**127.0.0.1 5555** in questo esempio), e inoltrare i pacchetti in uscita di nuovo l'altro modo:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Purché il `nc` comandi rimangono in esecuzione in una finestra del terminale, i pacchetti verranno inoltrati come previsto. È possibile digitare CTRL + C nella finestra del terminale per chiudere il `nc` comandi dopo aver completato usando l'emulatore.

    (Opzione 1 è in genere più semplice rispetto a Option 2, soprattutto se **preferenze di sistema > sicurezza e Privacy > Firewall** è attivata.) 

    _Opzione 2_: uso [`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    Per reindirizzare i pacchetti TCP dalla porta `5555` (o qualsiasi altra porta desiderato) nella [connessione di rete condivisa](http://kb.parallels.com/en/4948) interfaccia per la porta dispari nell'interfaccia di loopback (`127.0.0.1:5555` in questo esempio):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Questo comando imposta la porta di inoltro usando il `pf packet filter` il servizio di sistema. Le interruzioni di riga sono importanti. Assicurarsi di mantenerli intatte quando si copia-incolla. È necessario anche modificare il nome dell'interfaccia *vmnet8* se si usa Parallels. `vmnet8` nome della speciale *dispositivo NAT* per il *connessione di rete condivisa* modalità in VMWare Fusion. È probabile che l'interfaccia di rete appropriata in Parallels [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Connettersi all'emulatore dal computer Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Sostituire "ip: indirizzo-di--mac" con l'indirizzo IP del Mac, ad esempio come elencate da `ifconfig vmnet8 | grep 'inet '`. Se necessario, sostituire `5555` con la porta di desiderato dal passaggio 4\. (Nota: un modo per ottenere l'accesso da riga di comando a `adb` consiste nell'usare [ **strumenti > Android > Android prompt dei comandi Adb** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) in Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Alternativa usando tecnica `ssh`

Se è stata abilitata _account di accesso remoto_ nel Mac, quindi è possibile usare `ssh` per connettersi all'emulatore di inoltro alla porta.

1.  Installare un client SSH in Windows. È possibile installare [Git per Windows](https://git-for-windows.github.io/). Il `ssh` sarà disponibile nel comando i **Git Bash** prompt dei comandi.

2.  Seguire i passaggi da 1 a 3 sopra per avviare l'emulatore, terminare il `adb` server nel computer Mac e identificare le porte dell'emulatore.

3.  Eseguire `ssh` su Windows per configurare l'inoltro alla porta bidirezionale tra una porta locale su Windows (`localhost:15555` in questo esempio) e la porta dell'emulatore dispari nell'interfaccia di loopback del Mac (`127.0.0.1:5555` in questo esempio):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Sostituire `mac-username` con il nome utente Mac ottenuti tramite `whoami`. Sostituire `ip-address-of-the-mac` con l'indirizzo IP del Mac.

4.  Connettersi all'emulatore tramite la porta locale in Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: un semplice metodo per ottenere l'accesso da riga di comando a `adb` consiste nell'usare [ **strumenti > Android > Android prompt dei comandi Adb** in Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Una certa cautela: se si usa la porta `5555` per la porta locale, `adb` considera che l'emulatore venga eseguito in locale in Windows. Ciò non causa problemi in Visual Studio, ma in Visual Studio per Mac fa sì che l'app uscire immediatamente dopo l'avvio.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Tecnica alternativa usando `adb -H` non è ancora supportata

In teoria, un altro approccio, è possibile usare `adb`della funzionalità predefinita per la connessione a un' `adb` server in esecuzione in un computer remoto (vedere ad esempio [ http://stackoverflow.com/a/18551325 ](http://stackoverflow.com/a/18551325)).
Tuttavia, le estensioni dell'IDE di xamarin. Android non forniscono attualmente un modo per configurare tale opzione.

## <a name="contact-information"></a>Le informazioni di contatto

Questo documento illustra il comportamento corrente a partire da marzo 2016. La tecnica descritta in questo documento non fa parte della suite di test stabile per Xamarin, quindi potrebbe interrompere in futuro.

Se si nota che la tecnica non funziona più, o se si nota che eventuali altri errori nel documento, è possibile aggiungere alla discussione sul thread di forum seguenti: [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Grazie

