
[TryHackMe | Cyber Security Training](https://tryhackme.com/room/lofi)

[Lo-Fi-THM-Walkthrough-By-Reju-Kole](https://systemweakness.com/lo-fi-thm-walkthrough-by-reju-kole-1310cf7fb83c)

---

### Walkthrough - TryHackMe "Lo-Fi"

### 🛠️ **Objectif : Exploiter une vulnérabilité LFI (Local File Inclusion) pour récupérer des fichiers sensibles.**

---

### 🔍 **Reconnaissance**

- La room présente un site web vulnérable à l'inclusion de fichiers locaux.  
- L'objectif est d'exploiter cette faille pour lire des fichiers critiques.

---

### 🏴 **Exploitation - LFI**

1. **Tester la vulnérabilité**
    - Le site charge des fichiers via un paramètre `page`.
    - Tester un fichier présent sur le serveur :
    
    ```text
    http://target.com/?page=about.html
    ```

2. **Remonter l'arborescence et lire `/etc/passwd`**
    - Si l'application tourne sous Linux, on peut lire la liste des utilisateurs :
    
    ```text
    http://target.com/?page=../../../../../etc/passwd
    ```

3. **Récupération du flag**
    - Essai d’accès à un fichier flag supposé :
    
    ```text
    http://target.com/?page=../../../flag.txt
    ```
    
    - Le flag s'affiche avec cette requête.

---

### 🔥 **Conclusion**

- La vulnérabilité LFI permet d’accéder à des fichiers sensibles du serveur.  
- Elle peut être combinée avec d'autres attaques (ex: RCE via log poisoning).  
- **Contre-mesures :** Valider et filtrer les entrées utilisateur, utiliser des whitelists de fichiers accessibles, et désactiver l'inclusion directe de chemins utilisateur.
