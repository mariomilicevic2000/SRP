# SRP Lab 6

wsl - otvaramo linux terminal

id - kako bi saznali identitet korisnika

groups - izlistaje kojim grupama pripadamo

sudo adduser alice4 - dodajemo novog usera kao admin, stavimo password passwd

su - alice4 - logiramo se kao taj user, u ovom slučaju alice

sudo adduser bob4 - još jedan user

whoami

mkdir srp, cd srp - kreiramo novi direktorij u home direktoriju, ulazimo u njega

echo Hello world > security.txt - upis stringa u datoteku

ls -l - ispisuje listu prava korisnika nad ovim fileom

getfacl security.txt - ispisuje listu prava, ljepše formatirano

getfacl . - ispisuje listu prava nad direktorijem

cat security.txt - ispisuje sadržaj filea

chmod u-r security.txt - oduzimamo sebi pravo čitanja filea security.txt, sada svi mogu čitati file osim alice4

chmod u+r security.txt - vraćamo si pravo čitanja

chmod u-x . - oduzimamo si pravo executanja trenutnog direktorija

cat /home/alice4/srp/security.txt - bob može pročitati security.txt

chmod o-r security.txt - oduzimamo grupi others pravo čitanja nad security.txt

sudo usermod -aG alice4 bob4 - dodamo bob4 kao člana grupe alice4 kao administrator

cat /etc/group | grep alice4 - izlistavamo tko pripada grupi alice4

kao bob4 se odlogiramo pa ponovno logiramo da bi updateali sigurnosni token boba

cat /etc/shadow - permission denied

getfacl /etc/shadow - nemamo prava

sudo gpasswd -d bob4 alice4 - mičemo bob4 iz grupe alice4

sudo setfacl -m u:bob4:r /home/alice4/srp/security.txt - dajemo pravo na čitanje datoteke bobu kao useru

sudo groupadd alice_reading_group - kao admin kreiramo grupu za čitanje alice4 fileova

sudo setfacl -m g:alice_reading_group:r /home/alice4/srp/security.txt - dajemo grupi prava za čitanje nad security.txt

getfacl /home/alice4/srp/security.txt - vidimo da je dodana grupa alice_reading_group i da ima prava čitanja nad security.txt

```jsx
import os

print('Real (R), effective (E) and saved (S) UIDs:') 
print(os.getresuid())

with open('/home/alice4/srp/security.txt', 'r') as f:
    print(f.read())
```

python lab6_g4.py

```jsx
Real (R), effective (E) and saved (S) UIDs:
(1000, 1000, 1000)
Traceback (most recent call last):
  File "lab6_g4.py", line 6, in <module>
    with open('/home/alice4/srp/security.txt', 'r') as f:
IOError: [Errno 13] Permission denied: '/home/alice4/srp/security.txt'
```

getfacl lab6_g4.py - svi imaju sva prava

sudo python lab6_g4.py

```jsx
Real (R), effective (E) and saved (S) UIDs:
(0, 0, 0)
Hello world
```

python /mnt/c/Users/A507/lab6_g4.py - pokrećemo ga kao bob

```jsx
Real (R), effective (E) and saved (S) UIDs:
(1006, 1006, 1006)
Hello world
```

whatis passwd

getfacl $(which passwd) - vlasnik je root, jedino on ima pravo write

ps -eo pid,ruid,euid,suid,cmd | grep passwd - efektna prava za passwd