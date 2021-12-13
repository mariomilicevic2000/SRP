# SRP Lab 3 nastavak

# Digitalni potpis korištenjem public-key kriptografije

---

Kao nastavak vježbe 3 radimo digitalne potpise koristeći public-key kriptografiju.

Kao i u prošlim vježbama, prvo aktiviramo virtualno okruženje u pythonu.

Za potrebe vježbe skinuli smo sa a507 local servera potrebne materijale, dvije slike, njihove pripadajuće digitalne potpise, te budući da se radi o public-key kriptografiji, javni ključ.

Kompletan kod vježbe:

```python
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives import hashes
from cryptography.exceptions import InvalidSignature

def load_public_key():
    with open("public.pem", "rb") as f:
        PUBLIC_KEY = serialization.load_pem_public_key(
            f.read(),
            backend=default_backend()
        )
    return PUBLIC_KEY

def verify_signature_rsa(signature, message):
    PUBLIC_KEY = load_public_key()
    try:
        PUBLIC_KEY.verify(
            signature,
            message,
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
    except InvalidSignature:
        return False
    else:
        return True

with open("image_2.sig", "rb") as file:
    signature = file.read()

with open("image_2.png", "rb") as file:
    image = file.read()  

is_authentic = verify_signature_rsa(signature, image)
print(is_authentic)
```

Korišten je RSA kriptosustav.

Prva funkcija "load_public_key" vrši učitavanje i deserijalizaciju (postupak prevođenja podatkovne strukture ili stanja objekta u format koji se kasnije može pohraniti ili prenijeti i rekonstruirati) javnog ključa.

Funkcija "verify_signature_rsa" radi glavnu funkcionalnost programa, a to je da provjerava ispravnost digitalnog potpisa koristeći postojeće mehanizme provjere. Funkcija verify kao argumente prima digitalni potpis, sliku kojoj provjeravamo autentičnost, padding i SHA256 hash funkciju. Ukoliko digitalni potpis nije ispravan, zbog exceptiona InvalidSignature funkcija vraća False, a ako je ispravan vraća se True.

U pokretačkom dijelu programa učitavamo obje slike i pripadajuće potpise, pozivamo funkciju koja vraća bool vrijednost u ovisnosti o ispravnosti potpisa, te ju ispisujemo.