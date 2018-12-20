API REST Node.js 

$ node -v
$ npm -v
$ git --version
// install IDE visual Code
// et plugin: Node.js Modules Intellisense

cree un dossier 'monProject'
$ cd monProject
$ npm init
    name: mon-project
    version: (1.0.0)
    description: Serveur HTTP pour API REST
    entry point: (index.js) server.js
    test command:
    git repository:
    keywords: server,http,api,rest
    author:Abdelatif Rochdi
    license:
    (yes)
$ code .

// installation de independance pour le programe
$ npm install express --save
$ sudo npm install -g nodemon
// cree un fichier server.js

// Imports
var express     = require('express');
var bodyParser  = require('body-parser');
var apiRouter   = require('./apiRouter').router;

// Instantiate server
var server = express();

// Body Parser configuration
server.use(bodyParser.urlencoded({ extended: true }));
server.use(bodyParser.json());

// Configure routes
server.get('/', function (req, res) {
    res.setHeader('Content-Type', 'text/html');
    res.status(200).send('<h1>Bonjour sur mon super server</h1>');
});

server.use('/api/', apiRouter);

// Launch server
server.listen(8081, function() {
    console.log('Server en écoute :)');
});

// starten notre server
$nodemon server.js

// Création de la base de données avec Sequelize (ORM)
voir le pdf Fichier docs/DatabaseModel.pdf
on a 2 table Message et user (n:1)
ORM = Object Relational Mapping
pour cree nombreus de clase pour cree des Objects pour le bien manipuler (add , creat, delete, update )
on doit installer le sequelize
$ npm install -g sequelize-cli
$ npm install --save sequelize@3.30.4
$ npm install mysql --save
$ sequelize init
// on va trouver qu'on a nouveux fichier (config, migration, models...)
// on doit faire un mon de passe pour securiser la base de donne 
// et apres on doit cree les models (Message et user) les noms doit etre singulier
$ sequelize model:create --attributes "email:string username:string password:string bio:string isAdmin:boolean" --name User
$ sequelize model:create --attributes "idUser:integer title:string content:string attachment:string likes:integer" --name Message
// maintenant on va trouver 2 fichier dans le document migrations et on va un peut modifier les migration

'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Users', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      email: {
        allowNull: false,
        type: Sequelize.STRING
      },
      username: {
        allowNull: false,
        type: Sequelize.STRING
      },
      password: {
        allowNull: false,
        type: Sequelize.STRING
      },
      bio: {
        allowNull: true,
        type: Sequelize.STRING
      },
      isAdmin: {
        allowNull: false,
        type: Sequelize.BOOLEAN
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('Users');
  }
};

'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Messages', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      userId: {
        allowNull: false,
        type: Sequelize.INTEGER,
        references: {
          model: 'Users',
          key: 'id'
        }
      },
      title: {
        allowNull: false,
        type: Sequelize.STRING
      },
      content: {
        allowNull: false,
        type: Sequelize.STRING
      },
      attachment: {
        allowNull: true,
        type: Sequelize.STRING
      },
      likes: {
        allowNull: false,
        type: Sequelize.INTEGER
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('Messages');
  }
};

// dans le document models on va contruir la relation entre les models:
'use strict';
module.exports = (sequelize, DataTypes) => {
  var Message = sequelize.define('Message', {
    title: DataTypes.STRING,
    content: DataTypes.STRING,
    attachment: DataTypes.STRING,
    likes: DataTypes.INTEGER
  }, {
    classMethods: {
      associate: function(models) {
        // associations can be defined here
        
        models.Message.belongsTo(models.User, {
          foreignKey: {
            allowNull: false
          }
        })
      }
    }
  });
  return Message;
};

// 

'use strict';
module.exports = (sequelize, DataTypes) => {
  var User = sequelize.define('User', {
    email: DataTypes.STRING,
    username: DataTypes.STRING,
    password: DataTypes.STRING,
    bio: DataTypes.STRING,
    isAdmin: DataTypes.BOOLEAN
  }, {
    classMethods: {
      associate: function(models) {
        // associations can be defined here
        models.User.hasMany(models.Message);
      }
    }
  });
  return User;
};

// cree les bases de donnes :
mysql> create database database_development_retro;
mysql> create database database_test_retro;
mysql> create database database_production_retro;

// on doit maintenant lancer la migration
$ sequelize db:migrate

//  Système d'authentification par tokens JWT (Json Web Tokens) 
// explication : un token est une sorte de "carte d'identite"
// Structure d'un Token: 
- Une en-tete : algorithme de chiffrement, type de token
- les donnes : payload(donnees + champs reserves) la date expiration, le sujet , les donnes quont souhait integre a notre token (nom d'utilisateur,son identifiant, si admin ou pas)
- la signature : signature du token (si le token est valide ou non)

// on doit maintenant installer de module suplimentaire
$ npm install jsonwebtoken --save
$ npm install bcrypt --save
$ npm install body-parser --save
// Body Parser :il nous permet de recupere les argument et les parametre fournie dans le body d'une requet HTTP
pour forcer le parse on expl:

https://max:muster@www.example.com:8080/index.html?p1=A&p2=B#ressource
\___/   \_/ \____/ \_____________/ \__/\_________/ \_______/ \_______/
  |      |    |           |         |       |          |         |
Schema   | Kennwort      Host      Port    Pfad      Query    Fragment
      Benutzer

bodyParser.urlencoded({extended: true})

// et apre ondoit presis quon veut parsen avec json:
bodyParser.json()

// on cree apre notre router pour API 
// cree un fichier  apiRouter.js
// cree un document routes
// et dans le dernier cree un fichier usersCtrl.js
on va definier deux section
// Imports
var bcript = require('bcrypt);
var jwt = require('jsonwebtoken');
// Routes
module.exports = {
  register: function(req, res) {
      // TODO: To implement
  }
   login: function(req, res) {
      // TODO: To implement
  }
}

// dans le fichier apiRouter.js

//Import
var express = require('express');
var usersCtrl = require('./routes/userCtrl)
//Router
exports.router = (function() {
    var apiRouter = express.Router();

    // Users routes
    apiRouter.route('/users/register/').post(userCtrl.register);
    apiRouter.route('/users/login/').post(userCtrl.login);
    return apiRouter;
})();

// et dans le fichier server.js
var apiRouter = request(./apiRouter).router;

server.use('/api/', apiRouter);

// les waterfalls permettent de rendre le code plus lisible et agreable
// on doit mtn install nouveaus package
$ npm install async --save
// la fonction waterfall aide nous dexecuter on cascade

asyncLib.waterfall([
    function(done) {
        done(null, 'variable1');
    },
    function(variable1, done) {
        done(null);
    }
], function(err) {
    if(!err) {
        return res.status(200).json({ 'msg': 'ok'});
    } else {
        return res.status(404).json({ 'error': 'error'});
    }
})
