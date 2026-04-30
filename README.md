# Frida
### Fait par : AIT OURAJLI MOHAMED : 
# 🔬 Laboratoire Frida - Exercices Pratiques
 
 
## Installation et Preuve
 
### Objectif
Vérifier que toutes les dépendances Frida sont correctement installées et fonctionnelles.
 
### Commandes d'Installation
 
```bash
# Installation de Frida CLI
pip install frida-tools
```

 ```bash
# Installation de la bibliothèque Python Frida
pip install frida
```
<img width="853" height="368" alt="image" src="https://github.com/user-attachments/assets/5d977f84-42a4-48a0-873a-e07b64fa1068" />

 
### Captures de Versions
 
#### 1. Version de Frida CLI
```bash
$ frida --version
```
 
**Sortie attendue :**
```
17.9.1
```
 
#### 2. Version de Frida-PS
```bash
$ frida-ps --version
```
 
**Sortie attendue :**
```
17.9.1
```
 
#### 3. Version de la Bibliothèque Python
```bash
$ python -c "import frida; print(frida.__version__)"
```
 
**Sortie attendue :**
```
17.9.1
```
<img width="534" height="88" alt="image" src="https://github.com/user-attachments/assets/45250160-84f7-43a7-8179-b581086b27dd" />

 
#### 4. Appareils Android Connectés
```bash
$ adb devices
```
 
**Sortie attendue :**
```
List of attached devices
emulator-5554          device
```
 <img width="631" height="52" alt="image" src="https://github.com/user-attachments/assets/6cab9234-47ad-4015-8311-f1265b7b69e3" />

### Points de Vérification ✓
- [x] Frida CLI installé et fonctionnel
- [x] Frida-PS disponible
- [x] Bibliothèque Python Frida importable
- [x] Appareil Android détecté par ADB
---
 
## Déploiement Android
 
### Objectif
Déployer le serveur Frida sur l'appareil Android et vérifier sa fonctionnalité.
 
### 1. Téléchargement et Installation du Serveur Frida
 
```bash
# Télécharger frida-server pour Android
# Visitez: https://github.com/frida/frida/releases
 
# Extraire et copier sur l'appareil
$ adb push frida-server /data/local/tmp/
$ adb shell chmod +x /data/local/tmp/frida-server
```
 
### 2. Lancement du Serveur Frida
 
```bash
$ adb shell /data/local/tmp/frida-server -l 0.0.0.0
```
 
**Sortie attendue :**
```
Listening on 0.0.0.0:27042
```
<img width="906" height="145" alt="image" src="https://github.com/user-attachments/assets/d350afcb-4d05-4335-8ccd-a732fae9d00c" />

 
⚠️ **Important :** Laissez cette commande active dans un terminal séparé
 
### 3. Vérification avec Frida-PS
 
Ouvrez un nouveau terminal et exécutez :
 
```bash
$ frida-ps -Uai
```
 
**Sortie attendue (au moins 3 applications) :**
```
  PID  Name                          Identifier
-----  ------                        ---------------
 1234  Calendar                      com.android.calendar
 2567  Chrome                        com.android.chrome
 3891  Google Maps                   com.google.android.apps.maps
 4102  Settings                      com.android.settings
 5234  Messages                      com.android.messaging
 6789  Photos                        com.google.android.apps.photos
```
<img width="634" height="337" alt="image" src="https://github.com/user-attachments/assets/eff3d356-42aa-4198-b0e8-c94f0710f249" />

 
### Paramètres Utilisés
 
| Paramètre | Signification |
|-----------|---------------|
| `-U` | Connexion USB à l'appareil |
| `-a` | Affiche toutes les applications |
| `-i` | Affiche les identifiants de package |
 
---
 
## Injection de Scripts
 
### Objectif
Créer et injecter un script JavaScript Frida dans une application Android.
 
### 1. Création du Script - `hello.js`
 
```javascript
// hello.js - Premier script Frida
console.log("[*] Frida Injection Successful!");
console.log("[*] Application Loaded");
 
// Récupérer les informations du processus
var process_name = Java.use("android.os.Process").myPid();
console.log("[+] Process ID: " + process_name);
 
// Afficher les permissions
console.log("[+] Script injected into target application");
 
// Exemple basique : Afficher les classes Java disponibles
console.log("[*] Frida version: " + Frida.version);
 
// Exemple : Accéder à la classe MainActivity
try {
    var MainActivity = Java.use("com.example.app.MainActivity");
    console.log("[+] MainActivity trouvée et accessible");
} catch(error) {
    console.log("[-] MainActivity non trouvée: " + error);
}
 
console.log("[*] Script execution completed");
```
 
### 2. Injection du Script
 
Placez `hello.js` dans le répertoire courant, puis exécutez :
 
```bash
# Injecter le script dans une application en cours d'exécution
$ frida -U -f com.example.app -l hello.js
```
 <img width="419" height="163" alt="image" src="https://github.com/user-attachments/assets/30f0bfcd-01f6-48f2-9ef2-9d68df8e0526" />

**Options :**
 
| Option | Signification |
|--------|---------------|
| `-U` | Appareil USB |
| `-f` | Attacher et lancer l'application (spawn) |
| `-l` | Charger le fichier script |
 
**Sortie attendue :**
```
[USB::emulator-5554::PID::2567]-> [*] Frida Injection Successful!
[*] Application Loaded
[+] Process ID: 2567
[+] Script injected into target application
[*] Frida version: 17.9.1
[+] MainActivity trouvée et accessible
[*] Script execution completed
```
 
### 3. Script Avancé - `hello_advanced.js`
 
```javascript
// hello_advanced.js - Script avancé
console.log("\n" + "=".repeat(50));
console.log("   FRIDA INJECTION REPORT");
console.log("=".repeat(50) + "\n");
 
// 1. Informations de processus
console.log("[*] Processus Information:");
console.log("    - Process ID: " + Java.use("android.os.Process").myPid());
console.log("    - Frida Version: " + Frida.version);
console.log("    - Runtime: " + Frida.runtime);
 
// 2. Énumération des classes chargées
console.log("\n[*] Classes disponibles:");
Java.enumerateLoadedClasses({
    onMatch: function(className) {
        if (className.includes("android.app.Activity")) {
            console.log("    ✓ " + className);
        }
    },
    onComplete: function() {
        console.log("    (affichage limité à Activity)");
    }
});
 
// 3. Hooks - Intercepter des méthodes
console.log("\n[*] Hooks actifs:");
try {
    var Toast = Java.use("android.widget.Toast");
    var original_show = Toast.show.overload();
    
    original_show.implementation = function() {
        console.log("[!] Toast.show() appelée!");
        return original_show.call(this);
    };
    console.log("    ✓ Hook installé sur Toast.show()");
} catch(error) {
    console.log("    [-] Erreur: " + error);
}
 
console.log("\n" + "=".repeat(50));
console.log("   FIN DU RAPPORT");
console.log("=".repeat(50) + "\n");
```
 
### Commande d'injection avancée :
 
```bash
$ frida -U -f com.example.app -l hello_advanced.js --no-pause
```
 
---
 
## Dépannage
 
### Objectif
Identifier et résoudre les problèmes courants avec Frida.
 
### Scénario 1 : Serveur Frida Arrêté
 
#### Symptôme
```
$ frida-ps -Uai
error: couldn't connect to remote frida-server: No such device or address
```
 <img width="634" height="337" alt="image" src="https://github.com/user-attachments/assets/7755888c-25ce-455b-b284-9c1920a3d0f6" />

#### Diagnostic
 
```bash
# Vérifier si le serveur est en cours d'exécution
$ adb shell ps | grep frida-server
 
# Sortie si arrêté : (aucune sortie)
```
 <img width="560" height="78" alt="image" src="https://github.com/user-attachments/assets/8afe46a8-eda8-45b4-816c-495692c09121" />
<img width="694" height="120" alt="image" src="https://github.com/user-attachments/assets/38b67656-bf94-4a64-a436-84cecfde0bc6" />

#### Solution
 
```bash
# Redémarrer le serveur
$ adb shell /data/local/tmp/frida-server -l 0.0.0.0
 
# Vérifier la connexion
$ frida-ps -Uai
# Devrait afficher la liste des applications
```
 
### Scénario 2 : Appareil Non Reconnu
 
#### Symptôme
```
$ adb devices
List of attached devices
(empty list)
```
 
#### Diagnostic & Solution
 
```bash
# 1. Vérifier que l'appareil est en mode débogage
$ adb shell getprop ro.debuggable
# Devrait retourner: 1
 
# 2. Redémarrer le service ADB
$ adb kill-server
$ adb start-server
 
# 3. Lister à nouveau les appareils
$ adb devices
```
 
### Scénario 3 : Injection Échouée
 
#### Symptôme
```
error: couldn't compile script: SyntaxError
```
 
#### Diagnostic & Solution
 
```bash
# 1. Vérifier la syntaxe JavaScript
node -c hello.js  # Si Node.js est disponible
 
# 2. Vérifier que le package existe
$ frida-ps -Uai | grep package_name
 
# 3. Relancer avec debug
$ frida -U -f com.example.app -l hello.js --verbose
```
 <img width="950" height="228" alt="image" src="https://github.com/user-attachments/assets/13780aeb-b69f-489c-a4ac-95e3deb44d3c" />

### Scénario 4 : Erreur de Permissions
 
#### Symptôme
```
error: java.lang.SecurityException: ...
```
 
#### Solution
 
```bash
# Relancer l'application avec des permissions
$ adb shell am start -W com.example.app
 
# Vérifier les permissions
$ adb shell dumpsys package com.example.app
```
 
### Tableau de Dépannage Rapide
 
| Erreur | Cause | Solution |
|--------|-------|----------|
| `No such device` | Serveur Frida arrêté | Relancer le serveur |
| `couldn't connect` | ADB non connecté | `adb devices` |
| `SyntaxError` | Erreur JavaScript | Vérifier la syntaxe du script |
| `Permission denied` | Permissions insuffisantes | Vérifier les droits d'accès |
| `App not found` | Package incorrect | Vérifier avec `frida-ps -Uai` |
 
---
 
## Scripts de Référence
 
### Installation Complète (Script Shell)
 
```bash
#!/bin/bash
# install_frida.sh
 
echo "[*] Installation de Frida..."
 
# 1. Installer Python packages
pip install frida frida-tools
 
# 2. Vérifier versions
echo "[*] Vérification des versions..."
frida --version
frida-ps --version
python -c "import frida; print('Python Frida: ' + frida.__version__)"
 
# 3. Appareil Android
echo "[*] Vérification ADB..."
adb devices
 
echo "[✓] Installation terminée!"
```
 
### Déploiement sur Android (Script Shell)
 
```bash
#!/bin/bash
# deploy_frida.sh
 
DEVICE_ID="emulator-5554"
FRIDA_SERVER="frida-server"
 
echo "[*] Déploiement de Frida Server..."
 
# Copier sur l'appareil
adb -s $DEVICE_ID push $FRIDA_SERVER /data/local/tmp/
adb -s $DEVICE_ID shell chmod +x /data/local/tmp/frida-server
 
# Lancer le serveur
adb -s $DEVICE_ID shell /data/local/tmp/frida-server -l 0.0.0.0 &
 
# Attendre que le serveur démarre
sleep 2
 
# Vérifier
echo "[*] Vérification..."
frida-ps -Uai | head -5
 
echo "[✓] Déploiement terminé!"
```
 
---
 
## Résumé des Commandes Essentielles
 
```bash
# Installation
pip install frida frida-tools
 
# Vérification
frida --version
adb devices
 
# Déploiement Android
adb push frida-server /data/local/tmp/
adb shell chmod +x /data/local/tmp/frida-server
adb shell /data/local/tmp/frida-server -l 0.0.0.0
 
# Lister les applications
frida-ps -Uai
 
# Injection de script
frida -U -f com.example.app -l hello.js
 
# Dépannage
adb kill-server
adb start-server
frida-ps -Uai --verbose
```
 
---
 
## Checklist de Validation
 
### Installation ✓
- [x] Frida CLI installé
- [x] Frida-PS fonctionnel
- [x] Bibliothèque Python Frida disponible
- [x] ADB configuré
### Déploiement ✓
- [x] Frida Server copié sur l'appareil
- [x] Permissions exécutables définies
- [x] Serveur lancé sur 0.0.0.0:27042
- [x] Applications énumérées avec frida-ps
### Injection ✓
- [x] Script hello.js créé
- [x] Injection réussie dans l'application
- [x] Messages de sortie visibles
- [x] Pas d'erreurs de syntaxe
### Dépannage ✓
- [x] Erreur simulée documentée
- [x] Diagnostic effectué
- [x] Solution appliquée
- [x] Vérification complétée
---
 
## Ressources Supplémentaires
 
### Documentation Officielle
- [Frida - GitHub](https://github.com/frida/frida)
- [Frida - Documentation](https://frida.re/docs/home/)
- [Frida - API Reference](https://frida.re/docs/javascript-api/)
### Tutoriels Recommandés
- Mobile Security Testing with Frida
- Dynamic Analysis with Frida
- Android App Security Analysis
### Commandes Utiles Avancées
 
```bash
# Attacher à un processus existant
frida -U com.example.app -l script.js
 
# Mode interactif (REPL)
frida -U com.example.app
 
# Tracer les appels système
frida-trace -U -i open com.example.app
 
# Décompiler une application
apktool d app.apk
```
 
---
 
## Conclusion
 
Ce laboratoire couvre les aspects essentiels de Frida :
 
1. ✅ **Installation** - Configuration complète de l'environnement
2. ✅ **Déploiement** - Mise en place sur un appareil Android
3. ✅ **Injection** - Exécution de scripts personnalisés
4. ✅ **Dépannage** - Résolution des problèmes courants
**Compétences acquises :**
- Configuration de Frida sur Android
- Injection de code dynamique
- Analyse d'applications Android
- Diagnostic et résolution de problèmes
- Utilisation du framework de testing dynamique
---
 
## Notes Importantes
 
⚠️ **Disclaimer :** Frida est un outil puissant pour la recherche en sécurité et le testing. Utilisez-le uniquement sur :
- Vos propres appareils
- Des applications avec votre permission
- Dans un environnement de laboratoire contrôlé
---
 
**Dernière mise à jour :** 2026  
**Version Frida :** 17.9.1  
**Platform :** Android / Linux
