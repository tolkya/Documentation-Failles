<summary><h1>Documentation-Failles</h1></summary>

<details>
<summary><h1> API Application Programming Interface </h1></summary>
<h2> Qu'est-ce qu'une API ?</h2>
<h2>Application Programming Interface</h2>

- Interface qui permet à deux applications de communiquer
- Pont entre différents systèmes - services
- Échange de données standardisé

![alt text](image.png)

- Réutiliser des services existants
- Gagner du temps et éviter de tout recréer
- Connecter plusieurs systèmes (site web, mobile, base de données…)

Exemple concret :
- Votre app météo → API météo → Données actualisées
- Site e-commerce → API banque → Validation paiement 

Points clés :
- Comme un serveur dans un restaurant : vous commandez, il apporte ce que vous demandez
- Omniprésentes aujourd'hui (réseaux sociaux, e-commerce, etc.)  


<h2> API REST REpresentational State Transfer </h2>

Caractéristiques principales :
- Utilise les méthodes HTTP standards
- URLs descriptives et prévisibles
- Format JSON pour les données
- Architecture simple et intuitive

Méthodes HTTP :
- GET /users/123 → Récupérer un utilisateur
- POST /users → Créer un utilisateur
- PUT /users/123 → Modifier un utilisateur
- DELETE /users/123 → Supprimer un utilisateur

Avantages :
- ✅ Simple à comprendre et implémenter
- ✅ Largement adopté et supporté
- ✅ Mise en cache efficace


<h2> API GraphQL Query Language for API </h2>

Caractéristiques principales :
- Une seule URL pour toutes les opérations
- Requêtes flexibles et précises / personnalisation de la requête
- Le client demande exactement ce dont il a besoin

Exemple de requête :
{
    user(id: 123) {
        name
        email
        posts {
            title
        }
    }
}


Avantages :
- ✅ Évite le sur-fetching et sous-fetching
- ✅ Une seule requête pour des données complexes
- ✅ Auto-documentation via le schéma


<h2> REST vs GraphQL </h2>

    Aspect                    REST                     GraphQL 
----------------------- ---------------------- --------------------
    Simplicité 		          ⭐⭐⭐⭐⭐                ⭐⭐⭐ 	     
    Flexibilité                ⭐⭐⭐                 ⭐⭐⭐⭐⭐    
    Performance                ⭐⭐⭐                 ⭐⭐⭐⭐ 
    Apprentissage              Facile               Plus complexe 
    Écosystème                 Mature               En croissance

Quand utiliser REST :
- APIs simples et CRUD basiques
- Équipe débutante
- Mise en cache importante

Quand utiliser GraphQL :
- Applications complexes avec besoins variés
- Applications mobiles (économie de bande passante)
- Équipe expérimentée
</details>

<details>
<summary><h1> CSRF Cross-Site Request Forgery </h1></summary>
<h2> Qu'est-ce que CSRF ?</h2>

Cross-Site Request Forgery est une vulnérabilité qui force un utilisateur authentifié à exécuter des actions non désirées sur une application web.

<h2> Principe fondamental </h2>
Utilisateur connecté + Requête forgée = Action malveillante

Exemple : 
- Comme un escroc utilisant votre signature pour des virements, CSRF utilise votre session web pour agir à votre insu.

<h2> Mécanisme d'une attaque CSRF </h2>

![alt text](A.png)

<h2> Conditions pour une attaque réussie </h2>

![alt text](B.png)

Pourquoi ça fonctionne ? Les navigateurs incluent automatiquement les cookies dans toutes les requêtes vers un domaine !

<h2>Vecteurs d'attaque CSRF </h2>

- Emails piégés

Objet : "🏍️ Votre moto a gagné !"

De : concours@moto-magazine.com


Cliquez ici pour réclamer vos 500€ d'équipements !

- Réseaux sociaux 

Liens partagés avec descriptions attrayantes

- Attaques AJAX modernes
```bash
// Requête JavaScript malveillante
fetch('https://app.com/api/users/123', {
  method: 'DELETE',
  credentials: 'include' // Cookies inclus !
});
```

- Images malveillantes

```bash
<!-- Image invisible qui exécute l'action -->
<img src="https://app.com/delete-account?confirm=yes" 
     style="width:1px;height:1px;">
```

- Techniques psychologiques
• Urgence : "Offre limitée 5 min !"
• Récompense : Gros gains promis
• Autorité : Faux logos officiels
• Simplicité : "Un simple clic"

<h2> Comment Symfony nous protège </h2>

![alt text](C.png)

- Configuration
```bash
# config/packages/framework.yaml
framework:
    csrf_protection:
        enabled: true
        check_header: true
```

- Template Twig
```bash
{{ form_start(form) }}
    {{ form_widget(form.name) }}
    {{ form_widget(form.email) }}
    
    {# Token CSRF automatique ! #}
    
    <button type="submit">Enregistrer</button>
{{ form_end(form) }}
```

- HTML généré
```bash
<form method="POST">
    <input type="text" name="user[name]">
    <input type="email" name="user[email]">
    
    <!-- ✅ Token CSRF automatique -->
    <input type="hidden" name="_csrf_token" 
           value="Vx8rQ2mL9kPnF7sA3hN6bE1tY4uI0wR5">
    
    <button type="submit">Enregistrer</button>
</form>
```
</details>

<details>
<summary><h1> IDOR Insecure Direct Object Reference </h1></summary>
<h2> Qu'est-ce qu'une faille IDOR ?</h2>

Une faille IDOR permet d’accéder à une ressource via un identifiant sans contrôle d’accès.

Exemple :

https://site.com/profil?id=123

➡ Si je remplace 123 par 124 et que j’accède au profil de quelqu’un d’autre → faille IDOR.

<h2> Comment ça marche ? </h2>

- Les objets (comptes, fichiers, commandes) ont un identifiant unique.
- L’application ne vérifie pas si l’utilisateur a le droit d’y accéder.

Exemple :

https://banque.com/compte?id=4501

➡ Si un client change 4501 en 4502 et accède au compte d’un autre → faille.

<h2> Conséquences </h2>

- Accès à des données personnelles (risque RGPD).
- Modification ou suppression de données sensibles.
- Escalade de privilèges (ex. un simple utilisateur accède à des fonctions admin).

<h2> Prévention </h2>

- Ne jamais faire confiance aux paramètres côté client (URL, cookies, formulaires).
- Vérifier systématiquement les droits d’accès.
- Utiliser des identifiants non prévisibles (UUID, tokens).
- Faire des tests de sécurité (pentests, bug bounty).


<h2> Conclusion & OWASP </h2>

- Les IDOR sont simples à exploiter mais aux conséquences graves.
- Elles font partie du Top 10 OWASP 2021 : catégorie #1 – Broken Access Control.

<h2> Message clé : Toujours vérifier qui demande, et à quoi il a droit. </h2>
</details>

<details>
<summary><h1> MITM Man-in-the-Middle </h1></summary>
<h2> Qu'est-ce qu'une attaque MITM ?</h2>

Une attaque Man-in-the-Middle est une cyberattaque où un attaquant
s'interpose secrètement entre deux parties qui communiquent, interceptant
et potentiellement modifiant leurs échanges sans que les victimes s'en
aperçoivent.

![alt text](../../SIO2/Programmation/MITM/how-man-in-middle-works-min.png)

<h2> Comment fonctionne une attaque MITM ? </h2>

1. Positionnement
- Wi-Fi public non sécurisé
- ARP Spoofing
- DNS Spoofing
- Compromission de routeur

2. Interception
- Capture de paquets réseau
- Décryptage des communications
- Analyse des métadonnées
- Enregistrement des sessions

3. Manipulation
- Modification des données
- Injection de code malveillant
- Redirection frauduleuse
- Usurpation d'identité

<h2> Types d'attaques MITM </h2>

- Evil Twin
Création d'un faux point d'accès Wi-Fi avec un nom identique à un réseau
légitime pour tromper les utilisateurs.

- ARP Spoofing
Falsification des adresses MAC dans les tables ARP pour rediriger le trafic
vers la machine de l'attaquant.

- SSL Stripping
Rétrogradation des connexions HTTPS vers HTTP pour intercepter les
données en clair.

- DNS Spoofing
Falsification des réponses DNS pour rediriger vers des serveurs malveillants
contrôlés par l'attaquant.

<h2> Risques et conséquences </h2>

<h3>Pour les particuliers</h3>

- Vol d'identifiants : mots de passe, comptes bancaires
- Usurpation d'identité : accès aux réseaux sociaux, emails
- Espionnage : lecture des messages privés, historique
- Fraude financière : transactions détournées

<h3>Pour les entreprises</h3>

- Espionnage industriel : vol de secrets commerciaux
- Compromission des systèmes : accès aux serveurs internes
- Violation de données : exposition d'informations clients
- Perte de réputation : impact sur la confiance

<h2> Risques et conséquences </h2>

- Chiffrement
  - HTTPS obligatoire
  - VPN pour tout le trafic
  - Messagerie chiffrée
  - Protocoles sécurisés

- Vigilance réseau
  - Éviter les Wi-Fi publics
  - Vérifier les certificats SSL
  - Utiliser des réseaux de confiance
  - Surveiller les connexions

- Bonnes pratiques
  - Authentification 2FA
  - Mises à jour régulières
  - DNS sécurisés
  - Formation utilisateurs

- Outils de protection
  - Antivirus avec protection réseau
  - Pare-feu personnels
  - extensions HTTPS
  - Détection ARP Spoofing

<h2> Symfony : Protection côté développement </h2>
<h3>Pourquoi Symfony contre les attaques MITM ?</h3>

Symfony, framework PHP robuste, intègre de nombreuses fonctionnalités de sécurité natives qui aident à protéger les applications web contre les attaques Man-in-the-Middle.

- HTTPS forcé
-  Protection CSRF
- Gestion des sessions
- Headers de sécurité

<h3>Fonctionnalités avancées</h3>

- Validation des données
  - Validation côté serveur obligatoire
  - Sanitisation automatique des inputs
  - Protection contre l'injection SQL

- Authentification robuste
  - Système d'authentification intégré
  - Hashage sécurisé des mots de passe
  - Support OAuth2/JWT

<h3>Bonnes pratiques Symfony contre MITM</h3>

- Toujours utiliser HTTPS en production
- Configurer les headers de sécurité appropriés
- Valider toutes les entrées utilisateur
- Utiliser les bundles de sécurité (SecurityBundle)
- Maintenir Symfony à jour pour les correctifs de sécurité
- Configurer un firewall approprié
</details>

