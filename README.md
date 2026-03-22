#  Write-up : OWASP UnCrackable Mobile Dashboard - Level 2

##  Présentation du Challenge
L'objectif de ce challenge est de contourner la sécurité d'une application Android qui utilise une bibliothèque native pour valider un code secret. Contrairement au niveau 1, la logique de vérification n'est pas en Java mais en C/C++.

---

## Outils Utilisés
* **JADX-GUI** : Analyse du code Java décompilé.
* **Ghidra** : Reverse engineering de la bibliothèque native `.so`.
* **ADB** : Installation de l'APK et gestion de l'émulateur.
* **Android Studio** : Émulateur Pixel 6 pour les tests dynamiques.

---

##  Étape 1 : Analyse Statique (Java)
En ouvrant l'application dans **JADX-GUI**, on identifie la méthode de vérification. Le code récupère la saisie de l'utilisateur via un `EditText` et appelle une méthode native `this.m.a(string)`.


<img width="1225" height="1033" alt="{4785398D-4CE4-4E71-8CDD-7B9DAC9CCBF7}" src="https://github.com/user-attachments/assets/b714a2aa-2f0b-42e9-a93e-86cd60956652" />

*Légende : Identification de l'appel à la méthode de vérification native.*

---

##  Étape 2 : Extraction de la bibliothèque native
Comme la vérification est native, il faut extraire le fichier `libfoo.so` de l'APK.
1. Changement de l'extension `.apk` en `.zip`.
2. Extraction du dossier `lib/` contenant les différentes architectures (x86, x86_64, etc.).

<img width="876" height="416" alt="{934782BD-2D03-45C8-8D23-6DAD2046DD97}" src="https://github.com/user-attachments/assets/3b133d88-8906-4d54-b4ba-e1fce02bc826" />
*Légende : Extraction des ressources natives de l'APK.*

---

## Étape 3 : Reverse Engineering (Ghidra)
L'analyse de `libfoo.so` dans **Ghidra** révèle la fonction `CodeCheck_bar`. On y observe une comparaison de chaînes de caractères via `strncmp`.

### Analyse du Secret
Le pseudo-code montre que la chaîne saisie est comparée à une valeur stockée en mémoire :
* **Code Secret** : `Thanks for all the fish`.
* **Détail technique** : La valeur est initialement copiée via `strncpy` avant la comparaison.

<img width="1920" height="1080" alt="{B11DF465-5E8A-480A-B35F-74B0FE029826}" src="https://github.com/user-attachments/assets/852e17b3-bd4a-49ca-a15a-0fe016f3e498" />
*Légende : Pseudo-code montrant la chaîne secrète et la fonction strncmp.*

---

## Étape 4 : Validation
En saisissant le secret identifié dans l'application, le message "Success!" confirme la réussite du challenge.

<img width="531" height="847" alt="{FF67BF16-CE71-40AD-9538-CC876A1CB18D}" src="https://github.com/user-attachments/assets/46c6c37c-3801-44f8-9107-558c5d092b27" />
*Légende : Validation du secret sur l'émulateur Android.*

---

##  Résolution de problèmes (Troubleshooting)
Lors du labo, l'émulateur a rencontré une erreur `No target device found` ou des services système non réactifs. Ce problème a été résolu en effectuant un **Wipe Data** sur l'appareil virtuel via le **Device Manager**.

![Device Manager](liens_vers_ton_image_device_manager.png)
*Légende : Réinitialisation de l'émulateur via le Device Manager.*
