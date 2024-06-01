# Todo Task Manager

## Structure du projet

.
├── todo
├── todo_create
├── todo_update
├── todo_delete
├── todo_show
├── todo_list
|── todo_search
## Description

`todo` est un script Bash simple pour gérer vos tâches quotidiennes. Chaque tâche a un identifiant unique, un titre, une description, un lieu, une date et une heure d'échéance, et un marqueur de complétion. Le script vous permet de créer, mettre à jour, supprimer, afficher et rechercher des tâches, ainsi que de lister les tâches d'une journée spécifique.

## Prérequis

- Un système d'exploitation basé sur Unix 
- Bash

## Étapes

### 1. Créer le fichier `todo`

1. Créez le fichier `todo` :
   ```bash
   touch todo
2.Rendez le fichier exécutable :
chmod +x todo
3.Ouvrez le fichier todo avec un éditeur de texte :
nano todo
4.Ajoutez le contenu suivant dans le fichier todo :
############
#!/bin/bash

TODO_FILE="$HOME/.todo_tasks"

# Vérifiez et créez le fichier de tâches si nécessaire
if [ ! -f "$TODO_FILE" ]; then
    touch "$TODO_FILE"
fi

# Fonction d'usage
usage() {
    echo "Usage: todo [command]"
    echo "Commands:"
    echo "  create          Create a new task"
    echo "  update <id>     Update an existing task"
    echo "  delete <id>     Delete a task"
    echo "  show <id>       Show details of a task"
    echo "  list <date>     List tasks of a given day"
    echo "  search <title>  Search for a task by title"
}

# Vérifiez si aucun argument n'est fourni
if [ $# -eq 0 ]; then
    # Affiche les tâches d'aujourd'hui
    today=$(date +%Y-%m-%d)
    $0 list "$today"
    exit 0
fi

# Gérer les commandes
case $1 in
    create)
        ./todo_create
        ;;
    update)
        ./todo_update "$2"
        ;;
    delete)
        ./todo_delete "$2"
        ;;
    show)
        ./todo_show "$2"
        ;;
    list)
        ./todo_list "$2"
        ;;
    search)
        ./todo_search "$2"
        ;;
    *)
        usage
        ;;
esac
############
5.Enregistrez et quittez l'éditeur (pour nano, utilisez Ctrl+O, puis Enter, puis Ctrl+X).

### 2. Créer le script todo_create

1.Créez le fichier todo_create :
touch todo_create
2.Rendez le fichier exécutable :
chmod +x todo_create
3.Ouvrez le fichier todo_create avec un éditeur de texte :
nano todo_create
4.Ajoutez le contenu suivant dans le fichier todo_create :
############
#!/bin/bash

TODO_FILE="$HOME/.todo_tasks"

read -p "Title (required): " title
while [ -z "$title" ]; do
    echo "Title is required."
    read -p "Title (required): " title
done

read -p "Description: " description
read -p "Location: " location
read -p "Due Date (required, YYYY-MM-DD): " due_date
while [[ ! "$due_date" =~ ^[0-9]{4}-[0-9]{2}-[0-9]{2}$ ]]; do
    echo "Invalid date format. Use YYYY-MM-DD."
    read -p "Due Date (required, YYYY-MM-DD): " due_date
done

read -p "Due Time (HH:MM): " due_time
completed="false"

id=$(($(wc -l < "$TODO_FILE") + 1))

echo "$id|$title|$description|$location|$due_date|$due_time|$completed" >> "$TODO_FILE"
echo "Task created with ID $id."
#################
5.Enregistrez et quittez l'éditeur (pour nano, utilisez Ctrl+O, puis Enter, puis Ctrl+X).

### 3. Créer le script todo_update
1.Créez le fichier todo_update :
touch todo_update
2.Rendez le fichier exécutable :
chmod +x todo_create
3.Ouvrez le fichier todo_update avec un éditeur de texte :
nano todo_update
4.Ajoutez le contenu suivant dans le fichier todo_update :
#!/bin/bash

TODO_FILE="$HOME/.todo_tasks"

id=$1
if [ -z "$id" ]; then
    echo "Task ID is required." >&2
    exit 1
fi

task=$(grep "^$id|" "$TODO_FILE")
if [ -z "$task" ]; then
    echo "Task not found." >&2
    exit 1
fi

IFS='|' read -r id title description location due_date due_time completed <<< "$task"

read -p "Title [$title]: " new_title
title=${new_title:-$title}

read -p "Description [$description]: " new_description
description=${new_description:-$description}

read -p "Location [$location]: " new_location
location=${new_location:-$location}

read -p "Due Date [$due_date]: " new_due_date
while [[ -n "$new_due_date" && ! "$new_due_date" =~ ^[0-9]{4}-[0-9]{2}-[0-9]{2}$ ]]; do
    echo "Invalid date format. Use YYYY-MM-DD."
    read -p "Due Date [$due_date]: " new_due_date
done
due_date=${new_due_date:-$due_date}

read -p "Due Time [$due_time]: " new_due_time
due_time=${new_due_time:-$due_time}

read -p "Completed (true/false) [$completed]: " new_completed
completed=${new_completed:-$completed}

sed -i.bak "/^$id|/c\\$id|$title|$description|$location|$due_date|$due_time|$completed" "$TODO_FILE"
echo "Task $id updated."

### 4. Créer le script todo_delete
1.Créez le fichier todo_delete :
touch todo_delete
2.Rendez le fichier exécutable :
chmod +x todo_delete
3.Ouvrez le fichier todo_update avec un éditeur de texte :
nano todo_delete
4.Ajoutez le contenu suivant dans le fichier todo_update :
#!/bin/bash

TODO_FILE="$HOME/.todo_tasks"

id=$1
if [ -z "$id" ]; then
    echo "Task ID is required." >&2
    exit 1
fi

if ! grep -q "^$id|" "$TODO_FILE"; then
    echo "Task not found." >&2
    exit 1
fi

sed -i.bak "/^$id|/d" "$TODO_FILE"
echo "Task $id deleted."

### 5. Créer le script todo_show
1.Créez le fichier todo_show :
touch todo_show
2.Rendez le fichier exécutable :
chmod +x todo_show
3.Ouvrez le fichier todo_update avec un éditeur de texte :
nano todo_show
4.Ajoutez le contenu suivant dans le fichier todo_show :
#!/bin/bash

TODO_FILE="$HOME/.todo_tasks"

id=$1
if [ -z "$id" ]; then
    echo "Task ID is required." >&2
    exit 1
fi

task=$(grep "^$id|" "$TODO_FILE")
if [ -z "$task" ]; then
    echo "Task not found." >&2
    exit 1
fi

IFS='|' read -r id title description location due_date due_time completed <<< "$task"

echo "ID: $id"
echo "Title: $title"
echo "Description: $description"
echo "Location: $location"
echo "Due Date: $due_date"
echo "Due Time: $due_time"
echo "Completed: $completed"

### 6. Créer le script todo_list
1.Créez le fichier todo_list :
touch todo_list
2.Rendez le fichier exécutable :
chmod +x todo_list
3.Ouvrez le fichier todo_update avec un éditeur de texte :
nano todo_list
4.Ajoutez le contenu suivant dans le fichier todo_list :
#!/bin/bash

TODO_FILE="$HOME/.todo_tasks"

date=$1
if [ -z "$date" ]; then
    echo "Date (YYYY-MM-DD) is required." >&2
    exit 1
fi

completed_tasks=$(grep "|$date|" "$TODO_FILE" | grep "|true$")
uncompleted_tasks=$(grep "|$date|" "$TODO_FILE" | grep "|false$")

echo "Completed Tasks:"
echo "$completed_tasks" | while IFS='|' read -r id title description location due_date due_time completed; do
    echo "- $title (ID: $id)"
done

echo ""
echo "Uncompleted Tasks:"
echo "$uncompleted_tasks" | while IFS='|' read -r id title description location due_date due_time completed; do
    echo "- $title (ID: $id)"
done

### 7. Créer le script todo_search
1.Créez le fichier todo_search :
touch todo_search
2.Rendez le fichier exécutable :
chmod +x todo_search
3.Ouvrez le fichier todo_update avec un éditeur de texte :
nano todo_search
4.Ajoutez le contenu suivant dans le fichier todo_search :

#!/bin/bash

TODO_FILE="$HOME/.todo_tasks"

title=$1
if [ -z "$title" ]; then
    echo "Title is required." >&2
    exit 1
fi

tasks=$(grep "|$title|" "$TODO_FILE")

if [ -z "$tasks" ]; then
    echo "No tasks found with title containing '$title'."
    exit 0
fi

echo "Tasks matching '$title':"
echo "$tasks" | while IFS='|' read -r id title description location due_date due_time completed; do
    echo "- $title (ID: $id)"
done

## Utilisation
Commandes disponibles

    ##Créer une tâche :
./todo create

// Vous serez invité à entrer les informations de la tâche (titre, description, lieu, date et heure d'échéance).

  ## Mettre à jour une tâche :
./todo update <id>

//Remplacez <id> par l'identifiant de la tâche que vous souhaitez mettre à jour.

## Supprimer une tâche :
./todo delete <id>

//Remplacez <id> par l'identifiant de la tâche que vous souhaitez supprimer.

## Afficher les détails d'une tâche :
./todo show <id>

//Remplacez <id> par l'identifiant de la tâche dont vous souhaitez voir les détails.

## Lister les tâches d'une journée spécifique :
./todo list <date>

//Remplacez <date> par la date souhaitée au format YYYY-MM-DD.

## Rechercher une tâche par titre :
./todo search <title>

//Remplacez <title> par le titre (ou une partie du titre) de la tâche que vous souhaitez rechercher.

## Afficher les tâches du jour courant :
Si vous exécutez le script sans arguments, il affichera les tâches du jour courant.
./todo




















