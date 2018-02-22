CRUD
=====================
Qu'est ce que c'est ?
^^^^^^^^^^^^^^^^^^^^^

Il s'agit des 4 opérations fondamentales que l'on utilise lors de la manipulation de données stockées en base:
CREATE, READ, UPDATE, DELETE. (Créer, Lire, Mettre à jour, Supprimer)

Les systèmes de gestion de base de données sont:

* MySQL : libre et gratuit.

* PostgreSQL : libre et gratuit comme MySQL, avec plus de fonctionnalités.

* SQLite: libre et gratuit, et "Serverless" (très léger mais limité en fonctionnalités) ;

* Oracle : utilisé par les très grosses entreprises, non libre;

Il existe des logiciels de manipulation des bases (SQLDeveloper par exemple), mais nous allons faire une interface via laquelle des données seront créées et manipulées.

==>  Nous codons en: SQLite / Node.js

Site sans la base
^^^^^^^^^^^^^^^^^
1. Création d'un dossier racine pour le projet (nous l'appelleront **projet**)

2. Initilisation du dossier racine avec création du fichier package.json
::

        npm init
3. Installation des différents modules
::

        npm install --save express express-validator body-parser method-override express-flash cookie-parser ejs express-session

- **express**: Pour les routes
- **express-validator**: pour faciliter les validations formulaire
- **body-parser**: Pour récupérer les données html du formulaire dans un objet javascript
- **method-override**: Pour pouvoir implémenter nous même certaines méthodes qui pourraient être déjà pré-définies (Put, post)
- **express-flash**: Pour générer des messages (succès, erreur)
- **cookie-parser**: Pour accéder aux cookies
- **ejs**: Pour gérer les vues
- **express-session**: Pour gérer une session

4. Création du fichier principal de l'application *app.js* dans **projet**




Comme vu en cours:

``var express = require('express')`` pour chaque module installé, pour pouvoir l'utiliser

``app.use`` Pour les différentes routes

``app.listen`` Pour le démarrage de l'appli sur le bon port

``app.set('view engine', 'ejs')`` Pour utiliser des vues ejs

5. Structure de l'appli:

::

        config.js
        app.js
        ./routes
        ------index.js
        ------users.js
        ./views
        ------index.ejs
        ------./layouts
        -----------footer.ejs
        -----------header.ejs
        ------./users
        -----------add.ejs [CREATE]
        -----------edit.ejs [UPDATE]
        -----------list.ejs [READ/DELETE]


Ajout de la base de donnée
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Mise en place d'une base de données dans **projet**
::

        npm install --save sqlite3



- Créer un fichier de configuration *config.js* dans **projet**

::

        var config = {
            database: {
                name:        'test.db',         // your database name
                users_table: 'users'
            },
            server: {
                host: '127.0.0.1', //adresse du host local
                port: '3000'       //port où lancer l'appli
            }
        }

        module.exports = config //exportation du fichier sous la forme d'un objet à attributs

- Dans le fichier principal de l'application *app.js* dans **projet**
Ajouter, pour l'utiliser:


::

        var config = require('./config')

- Connexion à la base de données : dans app.js, on l'ouvre et on lui ajoute la table dont on se servira si celle ci n'existe pas
::

        var db = new sqlite3.Database(dbFile);
        //ou :memory: à la place de dbFile pour enregistrer en mémoire plutot que dans un fichier
::

                db.run('CREATE TABLE IF NOT EXISTS ' + config.database.users_table + ' (' +
        '`id` INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,' +
        '`name` TEXT,' +
        '`age` INTEGER,' +
        '`email` TEXT)');



users.js


C'est ici que nos opérations CRUD sont implémentées

Donc en début de fichier on redéfini la base :
::

        const sqlite3 = require('sqlite3').verbose();
        // open the database
        let db = new sqlite3.Database(

On trouve pour chacune :

* La nature de la fonction :
``get`` (récupérer)
``put`` (remplacer)
``post`` (modifier)
``delete`` (supprimer)

* La requête pour la base ``sql``
* Une gestion des erreurs
* Une action à faire en cas de succès (redirection, affichage..) ``res.render``


::

        app.get('/', function(req, res, next) {
            let sql = `SELECT * FROM users ORDER BY id DESC`;

          db.all(sql, [], (err, rows) => {
              if (err) {
                req.flash('error', err)
                res.render('user/list', {
                    title: 'User List',
                    data: ''
                    })
                throw err;
              }else {
                  // render to views/user/list.ejs template file
                  res.render('user/list', {
                      title: 'User List',
                      data: rows
                  })
                }
            });
        })


Design:
=========

Nous avons utilisé Materialize
http://node.romarin.ovh1.ec-m.fr/


Publication sur le serveur
==========================

modifier les attributs du server dans le fichier config.js
