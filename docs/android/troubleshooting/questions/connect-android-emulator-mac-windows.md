---
title: "È possibile connettersi a emulatori Android è in esecuzione su un Mac da una macchina virtuale Windows?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 2f0ef027d8a2d40ccf85e35d5a85eba4cd7c7ccc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>È possibile connettersi a emulatori Android è in esecuzione su un Mac da una macchina virtuale Windows?

Per connettersi a un emulatore Android di Google in esecuzione in un computer Mac da una macchina virtuale di Windows, utilizzare la procedura seguente:

1.  Avviare l'emulatore sul Mac.

2.  Terminare il `adb` server nel Mac:

    ```bash
    adb kill-server
    ```

3.  Si noti che l'emulatore è in ascolto sulle porte TCP 2 nell'interfaccia di rete loopback:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    La porta dispari è quello utilizzato per connettersi a `adb`. Vedere anche [http://developer.android.com/tools/devices/emulator.html#emulatornetworking](http://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Opzione 1_: utilizzare [ `nc` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html) per inoltrare i pacchetti TCP in ingresso ricevuti esternamente sulla porta 5555 (o qualsiasi altra porta desiderato) alla porta dispari sull'interfaccia di loopback (**127.0.0.1 5555** in questo esempio), e inoltrare i pacchetti in uscita di nuovo l'altro modo:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0 < backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Purché il `nc` comandi rimangono in esecuzione in una finestra terminale, i pacchetti verranno inoltrati come previsto. È possibile digitare CTRL + C nella finestra del terminale per chiudere il `nc` comandi una volta utilizzando l'emulatore.

    (Opzione 1 è in genere più semplice che opzione 2, soprattutto se **preferenze di sistema > sulla Privacy e sicurezza > Firewall** viene attivato.) 

    _Opzione 2_: utilizzare [ `pfctl` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html) per reindirizzare i pacchetti dalla porta TCP `5555` (o qualsiasi altra porta a cui si desidera) nel [connessione di rete condivisa](http://kb.parallels.com/en/4948) interfaccia per la porta numero dispari di interfaccia di loopback (`127.0.0.1:5555` in questo esempio):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Questo comando imposta la porta di inoltro mediante il `pf packet filter` servizio di sistema. Le interruzioni di riga sono importanti. Assicurarsi di mantenere intatte quando si copia-incolla. È inoltre necessario modificare il nome dell'interfaccia *vmnet8* se si usa Parallels. `vmnet8` nome della particolare *dispositivo NAT* per il *connessione di rete condivisa* modalità di fusione VMWare. È probabile che l'interfaccia di rete appropriata in Parallels [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Connettersi all'emulatore dai computer Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Sostituire "ip indirizzo-di-di-mac" con l'indirizzo IP del Mac, ad esempio come indicato da `ifconfig vmnet8 | grep 'inet '`. Se necessario, sostituire `5555` con la porta di desiderato del passaggio 4\. (Nota: è possibile ottenere l'accesso da riga di comando a `adb` tramite [ **strumenti > Android > Prompt dei comandi di Android Adb** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) in Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Alternativo utilizzando tecnica `ssh`

Se è stata abilitata _account di accesso remoto_ nel Mac, è possibile utilizzare `ssh` porta inoltro per la connessione all'emulatore.

1.  Installare un client SSH in Windows. È possibile installare [Git per Windows](https://git-for-windows.github.io/). Il `ssh` sarà disponibile nel comando di **Git Bash** prompt dei comandi.

2.  Eseguire i passaggi da 1 a 3 sopra per avviare l'emulatore, terminare il `adb` server nel Mac e identificare le porte dell'emulatore.

3.  Eseguire `ssh` in Windows per configurare l'inoltro alla porta bidirezionale tra una porta locale in Windows (`localhost:15555` in questo esempio) e la porta dell'emulatore dispari nell'interfaccia di loopback del Mac (`127.0.0.1:5555` in questo esempio):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Sostituire `mac-username` con il nome utente Mac, come indicato da `whoami`. Sostituire `ip-address-of-the-mac` con l'indirizzo IP del Mac.

4.  Connettersi all'emulatore utilizzando la porta locale in Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: per ottenere l'accesso da riga di comando a `adb` tramite [ **strumenti > Android > Prompt dei comandi di Android Adb** in Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Una certa cautela: se si utilizza la porta `5555` per la porta locale, `adb` considera che l'emulatore viene eseguito in locale in Windows. Ciò non causa problemi in Visual Studio, ma in Visual Studio per Mac provoca il chiuso immediatamente dopo l'avvio dell'app.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Tecnica alternativa utilizzando `adb -H` non è ancora supportata

In teoria, un altro approccio potrebbe consistere nell'utilizzare `adb`della funzionalità incorporate per connettersi a un `adb` server in esecuzione in un computer remoto (ad esempio [http://stackoverflow.com/a/18551325](http://stackoverflow.com/a/18551325)).
Tuttavia, le estensioni dell'IDE di xamarin non forniscono un modo per configurare tale opzione.

## <a name="contact-information"></a>Le informazioni di contatto

Questo documento viene illustrato il comportamento corrente a partire da marzo 2016. La tecnica descritta in questo documento non è parte del gruppo di test stabile per Xamarin, pertanto è stato interrotto in futuro.

Se si nota che la tecnica non funziona più, o se si notano errori nel documento, è possibile aggiungere alla discussione sul thread seguenti forum: [http://forums.xamarin.com/discussion/33702/ Android-Emulator-from-host-Device-Inside-Windows-VM](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Grazie

