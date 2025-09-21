Creare un socket RAW
Non si usa TCP o UDP, ma un raw socket con protocollo ICMP.
Questo ti permette di costruire a mano i pacchetti ICMP.
Richiede permessi di root (su Linux CAP_NET_RAW).

Preparare l’header ICMP
Un pacchetto ICMP Echo Request ha:
Type = 8 (Echo Request)
Code = 0
Checksum = calcolato su tutto l’header + dati
Identifier = un numero scelto dal client (di solito PID del processo)
Sequence Number = contatore dei pacchetti inviati
Poi aggiungi un payload (es: "hello" o riempi con byte per test).

Calcolare il checksum ICMP
È la somma a 16 bit “one’s complement” su tutti i 16-bit words del pacchetto.
Serve perché il destinatario validi l’integrità.

Inviare il pacchetto
Usi sendto() sul raw socket.
Destinatario = IP del server che vuoi pingare (es: 8.8.8.8).
Il kernel si occupa di incapsulare il pacchetto ICMP dentro IP.

Ricevere la risposta
Usi recvfrom() sul raw socket.
Arriverà un pacchetto IP che contiene un ICMP Echo Reply.

Analizzare la risposta
Leggi l’header IP → salta oltre.
Leggi l’header ICMP:
Type = 0 (Echo Reply)
Code = 0
Identifier deve combaciare col tuo.
Sequence Number deve essere quello che hai inviato.

Calcolare il Round Trip Time (RTT)
Salvi il tempo in cui hai inviato il pacchetto.
Quando ricevi la risposta, calcoli now - sent_time.
Mostri a schermo:
Reply from 8.8.8.8: icmp_seq=1 time=34 ms

Ripetere in loop
Incrementi Sequence Number.
Re-invii pacchetto ogni 1 secondo (come fa ping).
Stampi statistiche finali (pacchetti inviati, ricevuti, perdita, RTT medio).
