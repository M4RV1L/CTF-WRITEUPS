Partiamo con la fase di enumerazione delle porte  salvando i risultato in file di nome scan
`nmap -p- --open -sS -min-rate 5000 -vvv -n -Pn 10.10.195.47 -oG scan`
![[Screenshot 2025-08-11 alle 10.32.55.png]]


Ecco cosa abbiamo trovato
![[Screenshot 2025-08-11 alle 10.33.32.png]]

Facciamo una scansione dettagliata delle porte salvando il risultato su in file di nome ports
`nmap -sCV 10.10.195.47 -oN ports`
![[Screenshot 2025-08-11 alle 10.34.01.png]]


Ecco cosa ha trovato questo
![[Screenshot 2025-08-11 alle 10.34.23.png]]

Vediamo che la porta 21 permette di loggarsi un maniera anonima senza password, inoltre ci ha trovato un file "note_to_jake.txt" nella porta.
Per la porta 22 per entrare servono le credenziali, quindi per ora la saltiamo.
Sulla porta 80 corre in servizio http quindi un dito web.

Iniziamo con la porta 80. 
Quindi apriamo il browser e incolliamo l'IP della macchina
![[Screenshot 2025-08-11 alle 10.35.00.png]]

Nel sito non sembra esserci niente. 
Vediamo se ha dei sottodomini con gobuster.

`gobuster dir -u http://10.10.195.47 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt`
![[Screenshot 2025-08-11 alle 10.36.02.png]]


Non ci troverà niente. Inutile perdere tempo.
![[Screenshot 2025-08-11 alle 10.36.21.png]]

Torniamo sul sito e analizziamo il codice sorgente ctrl+u
![[Screenshot 2025-08-11 alle 10.36.42.png]]
Abbiamo trovato un commento che fa riferimento alla steganografia.

Quindi facciamo una ispect sulla pagina web per trovare il nome dell'immagine.
![[Screenshot 2025-08-11 alle 10.37.10.png]]

Trovata!!
Usiamo wget per prendere l'immagine.
`wget 10.10.195.47/brooklyn99`
![[Screenshot 2025-08-11 alle 10.39.27.png]]


Usiamo steghide per trovare informazione dentro l'immagine.
`steghide extract -sf brooklyn99
![[Screenshot 2025-08-11 alle 10.39.42.png]]


Niente da fare ci chiede la password 
![[Screenshot 2025-08-11 alle 10.40.02.png]]
Usiamo stegcracker molto utile in queste situazioni
`stegcracker brooklyn99 /home/marcovillano/Downloads/rockyou.txt`
![[Screenshot 2025-08-11 alle 10.40.44.png]]


Password trovata!!
![[Screenshot 2025-08-11 alle 10.41.08.png]]

Ritorniamo a steghide e mettiamo la password 
![[Screenshot 2025-08-11 alle 10.41.23.png]]
Ci ha estratto il file "note.txt", vediamo cosa c'è dentro.
![[Screenshot 2025-08-11 alle 10.41.46.png]]

Un nome user e la sua password. Potrebbero essere utili per la porta 22.
Ma prima entriamo nella porta 21
![[Screenshot 2025-08-11 alle 10.42.22.png]]
Siamo dentro.
Facciamo ls per vedere se ci sono file.
![[Screenshot 2025-08-11 alle 10.42.53.png]]
C'è il file "note_to_jake.txt". Prendiamolo con get.
Vediamo cosa c'è dentro il file
![[Screenshot 2025-08-11 alle 10.44.24.png]]
Ci dice che la password di Jake è debole possiamo provare a usare hydra.

`hydra -l jake -P /home/marcovillano/Downloads/rockyou.txt`
![[Screenshot 2025-08-11 alle 10.44.46.png]]


Trovata 
![[Screenshot 2025-08-11 alle 10.45.08.png]]

Ora entriamo.
Proviamo prima con Jake
`ssh jake@10.10.195.47`
![[Screenshot 2025-08-11 alle 10.46.46.png]]

Perfetto siamo dentro.
![[Screenshot 2025-08-11 alle 10.47.40.png]]

Entriamo dento holt
![[Screenshot 2025-08-11 alle 10.48.13.png]]
Prima flag trovata!!
![[Screenshot 2025-08-11 alle 10.48.13.png]]
Ora cerchiamo la flag root.
Dobbiamo scalare i privilegi.
Facciamo un find con / per partire dalla radice -perm per i permessi -4000, mandiamo stderr 2>/dev/null
`find / -perm -4000 2>/dev/null`
![[Screenshot 2025-08-11 alle 10.48.41.png]]
Ok ha trovato qualcosa
![[Screenshot 2025-08-11 alle 10.48.58.png]]

Ora usiamo un sito molto utile per la scalata dei privilegi GTFOBins.
Su questo sito possiamo fare un controllo sui binari che ci sono usciti.
Facciamo un `sudo -l`
![[Screenshot 2025-08-11 alle 10.49.27.png]]
Ci dice che il binario less c'è qualcosa.
Allora cerchiamolo su GTFOBins.
![[Screenshot 2025-08-11 alle 10.50.04.png]]

Ci dice che ha un sudo
![[Screenshot 2025-08-11 alle 10.50.24.png]]
Ci dice di fare `sudo less /etc/profile` e dargli `!/bin/sh`
![[Screenshot 2025-08-11 alle 10.50.47.png]]
![[Screenshot 2025-08-11 alle 10.51.11.png]]


Facciamolo e saremo root.
![[Screenshot 2025-08-11 alle 10.51.27.png]]
Facciamo un cd /root per trovare la flag di root
![[Screenshot 2025-08-11 alle 10.51.51.png]]