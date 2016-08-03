# Étape 1 : Récupérer le projet week-6

[Récupérer un projet Women On Rails](https://women-on-rails.github.io/guide/get_project)

# Étape 2 : Lire l'exercice et se lancer

[Retrouvez les slides du cours](http://slides.com/women_on_rails/week-6)

## Détruire une instance en passant par le navigateur

### Créer la route

Pour commencer, nous allons créer une nouvelle route dans le fichier ````config/routes.rb```` pour signifier à la fois quelle action HTTP nous voulons accomplir (ici ````delete````), le controleur de l'objet associé (ici ````curiosities````) et la méthode qui définira l'action à faire quand l'utilisateur cliquera sur le lien (ici ````destroy````).

Rajoutez la ligne suivante dans le fichier ````config/routes.rb```` :

````delete '/curiosity/:id', to: 'curiosities#destroy', as: 'curiosity'````

### Ajouter la méthode correspondante dans le controleur

Maintenant, il s'agit de définir ce qu'il se passe quand l'utilisateur va cliquer sur le lien pour détruire une instance. Dans la méthode ````destroy````, nous allons récupérer l'instance que nous voulons supprimer, grâce aux paramètres de l'url ````/curiosities/25````. Ensuite, nous allons la supprimer dans la base de données grâce à la méthode ````.delete```` et ensuite rediriger l'utilisateur sur la vue de toutes les curiosités.

Rajoutez la méthode ````destroy```` et ce qu'elle fait dans le controleur ````Curiosities```` qui se trouve dans ````app/controllers/curiosities.rb```` :

``` Ruby
def destroy
  @curiosity = Curiosity.find(params[:id]) # Récupère l'id de la curiosité à supprimer
  @curiosity.delete                        # Supprime la curiosité dans la base de données

  redirect_to curiosities_path               # Redirige l'utilisateur vers la vue Index
end
````

> Astuce : 
> Les valeurs contenues dans la variable params viennent du navigateur de l'utilisateur. 
> Il les envoie au serveur lorsqu'une requete est effectuée. Par exemple, si un utilisateur demande:
> http://localhost:3000/curiosities?toto=poulpe

> Alors params[:toto] sera égal à poulpe.

> La variable ````params```` est simplement un tableau de valeurs accessibles grace à des clés. 
> Ici la valeur à laquelle accéder est ````poulpe```` et la clé d'accès est ````:toto````.


### Ajouter le lien pour supprimer les curiosités dans la vue

Il faut maintenant afficher à l'utilisateur qu'il peut supprimer une curiosité. Pour cela, dans la vue, nous allons créer un lien dans la boucle de toutes les curiosités contenant le chemin pour détruire une instance en particulier.

Un lien dynamique se construit de cette façon en rails :

````<%= link_to 'Nom du lien qui sera affiché dans la vue', chemin_vers_le_controleur %>````

Il faut donc connaître le chemin (````path````) qui indiquera la route dans le fichier ````config/routes.rb```` vers la méthode du controleur. Pour trouver ce chemin, vous pouvez entrer ````rake routes```` dans votre terminal. Ce qui vous donne :

``` Console
     Prefix Verb   URI Pattern              Controller#Action
       root GET    /                        curiosities#index
curiosities GET    /curiosities(.:format)   curiosities#index
  curiosity DELETE /curiosity/:id(.:format) curiosities#destroy
````

Vous retrouvez bien le verbe HTTP ````DELETE```` (cf Verb), l'url ````/curiosity/:id```` (cf URI Pattern), la méthode du Controleur ````curiosities#destroy```` (cf Controller#Action). Et tout devant, un prefix ````curiosity```` qui vous donne en fait le chemin à rajouter dans votre vue : ````curiosity_path````. Attention, ici, le controleur a besoin de l'id de la curiosité à supprimer. Il faut donc la passer dans les paramètres. Nous l'indiquons comme ceci : ````curiosity_path(curiosity)````.

> Important : ````curiosity_path(curiosity)```` est une méthode générée par Ruby On Rails directement, en fonction de ce que vous avez défini dans le fichier ````route.rb````. 
> Elle accepte un objet ````curiosity```` ou son identifiant ````curiosity.id````.

Rajoutez le lien dans votre vue :

````<%= link_to 'Supprimer', curiosity_path(curiosity), method: :delete %>````

Dans le cadre d'une suppression, nous rajoutons un élément important au link_to : la précision du verbe HTTP avec method: :delete.

Testez maintenant votre nouvelle action dans le serveur.

## Pour aller plus loin

### Ajout d'une pop-up de confirmation

Nous aimerions ajouter une pop-up de confirmation avec ````data: { confirm: 'Message de confirmation' }```` dans le ````link_to```` de la vue, car c'est une action sur laquelle l'utilisateur ne pourra pas revenir. Nous voulons donc être sûrs de son choix.

Aidez-vous de la documentation du [link_to](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to) et ajoutez cette pop-up !

### Ajout d'une image

Pour rendre notre page plus sympa, nous aimerions avoir une image à la place du texte Supprimer. Amusez-vous à remplacer ce texte par une icône en utilisant les icônes bootstrap, dont vous pouvez trouver la documentation [ici](http://getbootstrap.com/components/) !

### Afficher une curiosité en particulier

- Ajouter une route dans le fichier ````config/routes```` pour ````GET```` l'url ````curiosity/:id```` vers une méthode ````show```` du controleur ````Curiosities````.
- Créer la méthode ````show```` dans le controleur ````Curiosities```` pour retrouver la curiosité ````@curiosity```` qu'on affichera dans la vue, grâce au ````params[:id]````.
- Créer un ````link_to```` vers la curiosité dans ````index.html```` dans la boucle de ````@curiosities```` en vérifiant le chemin via ````rake routes````.
- Créer la vue ````show.html```` dans ````app/views/curiosities```` et afficher le nom de la curiosité grâce à la variable définit dans le controleur ````<%= @curiosity.name %>````
- Pimper la vue : rajouter l'image, la description, des balises html, du css

## Lancer le serveur sur lequel va tourner l'application

En premier lieu, vérifiez que votre application a tous les Gems (plug-ins, bibliothèques... bref des briques de code) qu'elle utilise à disposition : vous pouvez les installer automatiquement en faisant la commande ````bundle install```` dans votre console, à l'intérieur du dossier de votre projet ````week-6````.

Si un problème survient au niveau de la version de ruby, vous devriez avoir besoin d'effectuer la commande ````rbenv install 2.3.1```` dans la console pour installer la version de ruby dont l'application a besoin. 
(Si rbenv ne connait pas cette version, utilisez la commande ````brew update && brew upgrade ruby-build```` avant)
Puis, installez bundler pour cette version avec la commande ````gem install bundle````. Et enfin, faites un ````bundle update````pour mettre a jour vos plug-ins. 

Pour lancer un serveur Ruby On Rails, vous devez faire la commande ````rails server```` (ou ````rails s````) toujours dans votre console. 
Et voila, votre serveur est lancé !

### Visualiser l'application sur le navigateur

Après avoir lancé votre serveur, vous pourrez ouvrir votre navigateur pour y coller l'URL suivante : [http://localhost:3000/](http://localhost:3000/)
Vous devriez visualiser le contenu de la vue que vous avez ouverte précédement. 
Apres avoir fait des modifications sur cette vue, vous n'aurez qu'à recharger la page du navigateur pour voir vos modifications apparaître. (rafraîchir: F5 ou ````CTRL + R```` sous windows, ````CMD + R```` sous mac)

# Étape 3 : Enregistrer les modifications sur le dépôt distant

[Enregistrer vos modifications et les envoyer sur votre répertoire Github](https://women-on-rails.github.io/guide/push_project)

# Pour aller plus loin :
- La documentation de Ruby : http://ruby-doc.org/core-2.3.1/
- La documentation de Ruby On Rails : http://api.rubyonrails.org/
