# ROSSI Thomas

# Exercice 1. Variables d’environnement

__1. Dans quels dossiers bash trouve-t-il les commandes tapées par l’utilisateur ?__

Il faut regarder le contenu de la varible $PATH :
```/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin```

__2. Quelle variable d’environnement permet à la commande cd tapée sans argument de vous ramener dans
votre répertoire personnel ?__

C'est la variable $HOME que j'ai trouvé grace à ```set | grep /home/thomas```

**3. Explicitez le rôle des variables LANG, PWD, OLDPWD, SHELL et _.**

LANG contient la langue en l'encodage utilisé

PWD  contient le repertoire courant ou l'utilisateur est actuellement placé

OLDPWD contient l'ancien repertoire courant ou l'utilisateur était placé

SHELL contient l'emplacement du SHELL que l'on utilise donc /bin/bash dans notre cas

$_ contient le dernier argument de la dernière commande tapé

**4. Créez une variable locale MY_VAR (le contenu n’a pas d’importance). Vérifiez que la variable existe.**

MY_VAR=test

Pour vérifier que la variable existe il faut tapé ```set | grep MY_VAR```

Si une ligne est affichée c'est qu'elle existe

**5. Tapez ensuite la commande bash. Que fait-elle ? La variable MY_VAR existe-t-elle ? Expliquez. A la fin
de cette question, tapez la commande exit pour revenir dans votre session initiale.**

Elle ouvre un autre sous bash depuis notre bash

La variable MY_VAR n'est plus accessible dans ce bash car il "ouvre" un autre bash qui n'a donc pas MY_VAR et MY_VAR n'est pas initialisé dans le .bashrc

Après le exit, MY_VAR est donc de nouveau accessible.

**6. Transformez MY_VAR en une variable d’environnement et recommencez la question précédente. Expliquez.**

```
export MY_VAR
MY_VAR=coucou
bash
echo $MY_VAR
coucou
exit
```

Avec MY_VAR qui est maintenant une variable d'environnement après avoir tapé bash, la variable est de nouveau accessible car le nouveau bash a la même environnement que le précédent.

**7. Créer la variable d’environnement NOMS ayant pour contenu vos noms de binômes séparés par un espace.
Afficher la valeur de NOMS pour vérifier que l’affectation est correcte.**

```
export NOMS=Thomas Tom
printenv NOMS
Thomas
```

Ne fonctionne pas car il affiche que mon Prénom, il faut mettre la chaine de caractère entre guillemet pour que la variable contienne toute la chaine.

**8. Ecrivez une commande qui affiche ”Bonjour à vous deux, binôme1 binôme2 !” (où binôme1 et binôme2
sont vos deux noms) en utilisant la variable NOMS.**

```
export NOMS="Thomas Tom"
echo "Bonjour à vous deux, $NOMS"
Bonjour à vous deux, Thomas Tom
```

**9. Quelle différence y a-t-il entre donner une valeur vide à une variable et l’utilisation de la commande
unset ?**

Donner une valeur vide à une variable permet de créé une variable qui peut être utilisé plus tard.

Unset une variable permet de supprimer la variable, elle ne sera donc plus accessible.

**10. Utilisez la commande echo pour écrire exactement la phrase : $HOME = chemin (où chemin est votre
dossier personnel d’après bash)**

```
echo '$HOME = '$HOME
$HOME = /home/thomas
```

**Ajoutez le chemin vers script à votre PATH de manière permanente.**

```PATH=$PATH:~/script```

# Exercice 2. Contrôle de mot de passe

**Écrivez un script testpwd.sh qui demande de saisir un mot de passe et vérifie s’il correspond ou non au
contenu d’une variable PASSWORD dont le contenu est codé en dur dans le script. Le mot de passe saisi par
l’utilisateur ne doit pas s’afficher.**

```
PASSWORD=toor
read -s -p "Entrer votre mot de passe : " read_PASSWORD

if test $read_PASSWORD = $PASSWORD; then
        echo "Bon mot de passe"
else
        echo "Le mot de passe est incorrect"
fi
```

# Exercice 3. Expressions rationnelles

```
function is_number()
{
        re='^[+-]?[0-9]+([.][0-9]+)?$'
        if ! [[ $1 =~ $re ]] ; then
                return 1
        else
                return 0
        fi
}

is_number $1
if [ $? = 0 ] ; then
        echo "$1 est un nombre"
else
        echo "$1 n'est pas un nombre"
fi
```

# Exercice 4. Contrôle d’utilisateur

**Écrivez un script qui vérifie l’existence d’un utilisateur dont le nom est donné en paramètre du script. Si le
script est appelé sans nom d’utilisateur, il affiche le message : ”Utilisation : nom_du_script nom_utilisateur”,
où nom_du_script est le nom de votre script récupéré automatiquement (si vous changez le nom de votre
script, le message doit changer automatiquement)**

```
if [ $# = "1" ]; then
        user_exist=$(grep -c ^$1: /etc/passwd)
else
        echo "Utilisation : $0 nom_utilisateur"
        exit
fi

if [ "$user_exist" = "1" ]; then
        echo "L'utilisateur $1 existe"
else
        echo "L'utilisateur $1 n'existe pas"
fi
```

# Exercice 5. Factorielle

**Écrivez un programme qui calcule la factorielle d’un entier naturel passé en paramètre (on supposera que
l’utilisateur saisit toujours un entier naturel).**

```
i=1
fct=1

while [ $i -lt $1 ]
do
        ((i++))
        fct=$(($fct*$i))
done

echo $fct
```

# Exercice 6. Le juste prix

**Écrivez un script qui génère un nombre aléatoire entre 1 et 1000 et demande à l’utilisateur de le deviner.
Le programme écrira ”C’est plus !”, ”C’est moins !” ou ”Gagné !” selon les cas (vous utiliserez $RANDOM).**

```
alea=$((1 + RANDOM % 1000))

while [ true ]
do

        read -p "Entrer un nombre entre 1 et 1000 : " nb

        if [ $nb -lt $alea ]; then
                echo "Le nombre est plus grand !"
        elif [ $nb -gt $alea ]; then
                echo "Le nombre est plus petit !"
        else
                echo "Vous avez trouver le bon nombre, c'est $nb"
                exit
        fi
done
```

# Exercice 7. Statistiques

**1. Écrivez un script qui prend en paramètres trois entiers (entre -100 et +100) et affiche le min, le max
et la moyenne. Vous pouvez réutiliser la fonction de l’exercice 3 pour vous assurer que les paramètres
sont bien des entiers.
2. Généralisez le programme à un nombre quelconque de paramètres (pensez à SHIFT)
3. Modifiez votre programme pour que les notes ne soient plus données en paramètres, mais saisies et
stockées au fur et à mesure dans un tableau.**

## Voici mon script final
```
function is_number()
{
        re='^[+-]?[0-9]+([.][0-9]+)?$'
        if ! [[ $1 =~ $re ]] ; then
                return 1
        else
                return 0
        fi
}
echo -n "Entrer la liste des valeurs séparer par un espace : "
read -a tab
j=0

min=${tab[1]}
max=${tab[1]}
moy=${tab[1]}

for i in ${tab[*]}
do
        is_number $i
        if [ $? = 0 ]; then
                if [ $i -lt -100 ] || [ $i -gt 100 ]; then
                        echo "$1 est un nombre mais est inférieur à -100 ou supérieur à 100"
                else
                        if [ $i -gt $max ]; then
                                max=$i
                        fi

                        if [ $i -lt $min ]; then
                                min=$i
                        fi

                        somme=$(($somme+$i))
                        ((j++))


                fi

        else
                echo "$1 n'est pas un nombre"

        fi

        shift
done

moy=$(($somme/$j))
echo "Le chiffre minimum est $min"
echo "Le chiffre moyen est $moy"
echo "Le chiffre maximun est $max"
echo "Liste des valeurs que vous avez entrer sous forme de tableau : ${tab[*]}"
```
