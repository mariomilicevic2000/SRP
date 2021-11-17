# SRP Lab 3

Kreiramo virualno okruženje kao i u vježbi 2:

- python -venv srp
- cd Scripts
- activate
- cd.. 2 puta

otvaramo prazni vs code file sa komandom "code ."

## Izazov 1

Rješenje izazova:

```python
from cryptography.hazmat.primitives import hashes, hmac

def generate_MAC(key, message):

	if not isinstance(message, bytes):

		message = message.encode()

	h = hmac.HMAC(key, hashes.SHA256())

	h.update(message)

	signature = h.finalize()

		return signature

if __name__ == "__main__":

	key=b"moj privatni kljuc"

	with open("message.txt", "rb") as file:

		content = file.read()

	mac = generate_MAC(key, content)

	print(mac.hex())
```

koristimo polugotovo rješenje - HMAC iz biblioteke cryptography

pozivamo program sa komandom ".\message_integrity.py

>> c1ad736b88a5a1cb6772ccc670fe9e7d3d75e6faaa26a737a934c97f25ba9c32

ispis je MAC (Message Authentication Code) za ovaj ključ

```python
mac = generate_MAC(key, content)

with open("message.sig", "wb") as file:

	file.write(mac)
```

stvaramo "message.sig" file u koji spremamo poruku koju želimo zaštititi

```python
from cryptography.exceptions import InvalidSignature

def verify_MAC(key, mac, message):

if not isinstance(message, bytes):

message = message.encode()

h = hmac.HMAC(key, hashes.SHA256())

h.update(message)

try:

h.verify(mac)

except InvalidSignature:

return False

else:

return True
```

```python
with open("message.sig", "rb") as file:

	mac = file.read()

	is_authentic = verify_MAC(key, mac, content)

		print(is_authentic)
```

>> True

Ovaj izazov je pokazao generiranje MAC-a za određeni ključ i provjeru integriteta poruke (verify_MAC)

Ako bi išta promijenili u poruci, funkcija verify_MAC bi vratila vrijednost "False".

## Izazov 2

Sa A507 local servera smo skinuli 10 parova poruka-MAC pomoću wget.

Za zadane poruke provjeravamo integritet.

```python
from cryptography.hazmat.primitives import hashes, hmac

from cryptography.exceptions import InvalidSignature

import os

def verify_MAC(key, mac, message):

	if not isinstance(message, bytes):

		message = message.encode()

	h = hmac.HMAC(key, hashes.SHA256())

	h.update(message)

	try:

		h.verify(mac)

	except InvalidSignature:

		return False

	else:

		return True

if __name__ == "__main__":

	key="milicevic_mario".encode()

	path = os.path.join("challenge", "milicevic_mario", "mac_challenge")

	print(path)

	for ctr in range(1, 11):

		msg_filename = f"order_{ctr}.txt"

		sig_filename = f"order_{ctr}.sig"

		print(msg_filename)

		print(sig_filename)

		path_msg = os.path.join(path, msg_filename)

		path_sig = os.path.join(path, sig_filename)

		with open(path + "\\" + msg_filename, "rb") as file:

			content_file = file.read()

		with open(path + "\\" + sig_filename, "rb") as file:

			signature = file.read()

		is_authentic = verify_MAC(key, signature, content_file)

		print(f'Message {content_file.decode():>45} {"OK" if is_authentic else "NOK":<6}')
```

Ispis programa je poruka i status integriteta svake od tih 10 poruka.

Za bazu zadatka koristimo prethodnu verify_MAC funkciju, a ostatak smo adaptirali trenutnom zadatku, i većina koda se odnosi na uspostavljanje filepatha svake poruke odnosno MAC-a.