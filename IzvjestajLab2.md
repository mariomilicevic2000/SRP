# SRP Lab 2

Za ovu vježbu smo koristili programski jezik Python, i njegovu biblioteku cryptography.

Za dani zadatak potrebna je barem verzija 3 Pythona, što možemo provjeriti komandom 

python —version

Pokrećemo virtualno okruženje u Pythonu da ne smetamo u globalnom okruženju komandom python -m venv grupa3srp

Pokrećemo virtualno okruženje nizom komandi:

cd mmilic05, cd grupa3srp, cd Scripts, activate, cd.. 2 puta

Nadalje instaliramo biblioteku cryptography naredbom: `pip install cryptography`

Importamo sustav za simetričnu enkripciju Fernet: **`from** **cryptography.fernet** **import** Fernet`

Za enkripciju i dekripciju koristimo naredbe:

key = Fernet.generate_key()

f = Fernet(key)

plaintext = b"Hello world"

ciphertext = f.encrypt(plaintext)

f.decrypt(ciphertext)

Pojmovi:

plaintext je podatak u normalnom obliku koji želimo enkriptirati

ciphertext je enkriptirani plaintext

algoritam za enkripciju, algoritam za dekripciju

simetrični ključ je ključ po kojem algoritam enkriptira plaintext

Da bi otkrio koji od fileova sa a507 local servera je moj, koristimo ovaj odsječak koda:

```python
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

filename = hash('prezime_ime') + ".encrypted"

if __name__ == "__main__":
    h = hash('milicevic_mario')
    print(h)
```

python brute_force.py - izbacuje ime odgovarajućeg osobnog filea na a507 serveru (4812949d924828a97566cfd4ad134222a97ac5522bf8bac74f25b9d210318ac3)

Save link u file path: C:\Users\A507\mmilic05

```python
 key = int.from_bytes(os.urandom(32), "big") & int('1'*KEY_ENTROPY, 2)
 key_base64 = base64.urlsafe_b64encode(key.to_bytes(32, "big"))
 fernet = Fernet(key_base64)

def brute_force():
	ctr = 0
	while True:
		key_bytes = ctr.to_bytes(32, "big")
		key = base64.urlsafe_b64encode(key_bytes)
		if not (ctr+1) % 1000: //ispisuje svako 1000
			print(f"[*] Keys tested: {ctr+1:,}", end="\r")
		ctr += 1
```

Pseudokod challengea:

C = E_K(P)

for k=1,...,infinity

P = D_k(C)

if test_png(P)

print("KEY FOUND:", k)

break

Napad je tipa brute force budući da imamo samo 22 bita entropije ključa

Testiramo početak svakog dekriptiranog plaintexta da vidimo radi li se o png headeru budući da znamo da je rješenje png formata

Puni kod:

```python
from cryptography.fernet import Fernet
import base64
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

filename = hash('prezime_ime') + ".encrypted"

def test_png(header):
    if header.startswith(b"\211PNG\r\n\032\n"):
        return True

def brute_force():
    # Reading from a file
    filename = "4812949d924828a97566cfd4ad134222a97ac5522bf8bac74f25b9d210318ac3.encrypted"
    with open(filename, "rb") as file:
        ciphertext = file.read()

    ctr = 0
    while True:
            key_bytes = ctr.to_bytes(32, "big")
            key = base64.urlsafe_b64encode(key_bytes)
            if not (ctr+1) % 1000:
                print(f"[*] Keys tested: {ctr+1:,}", end="\r")

            try:
                plaintext = Fernet(key).decrypt(ciphertext)
                header = plaintext[:32]
                if test_png(header):
                    print(f"[+] KEY FOUND: {key}")
                    # Writing to a file
                    with open("BINGO.png", "wb") as file:
                        file.write(plaintext)                    
                    break
            except Exception:
                pass
            ctr += 1

if __name__ == "__main__":
    brute_force()
```