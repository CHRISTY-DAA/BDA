import sqlite3

# Connexion à la base de données
conn = sqlite3.connect("stocks.db")
cursor = conn.cursor()

# Création de la table des produits
cursor.execute('''
CREATE TABLE IF NOT EXISTS produits (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nom TEXT NOT NULL,
    quantite INTEGER NOT NULL,
    prix REAL NOT NULL
)
''')
conn.commit()

# Fonction pour ajouter un produit avec validation des entrées
def ajouter_produit():
    nom = input("Nom du produit : ")
    while True:
        try:
            quantite = int(input("Quantité : "))
            if quantite < 0:
                raise ValueError("La quantité ne peut pas être négative.")
            break
        except ValueError:
            print("Veuillez entrer un nombre entier valide pour la quantité.")
    
    while True:
        try:
            prix = float(input("Prix : "))
            if prix < 0:
                raise ValueError("Le prix ne peut pas être négatif.")
            break
        except ValueError:
            print("Veuillez entrer un nombre valide pour le prix.")
    
    cursor.execute("INSERT INTO produits (nom, quantite, prix) VALUES (?, ?, ?)", (nom, quantite, prix))
    conn.commit()
    print("Produit ajouté avec succès !")

# Fonction pour afficher tous les produits
def afficher_produits():
    cursor.execute("SELECT * FROM produits")
    produits = cursor.fetchall()
    print("\nListe des produits :")
    for produit in produits:
        print(f"ID: {produit[0]}, Nom: {produit[1]}, Quantité: {produit[2]}, Prix: {produit[3]}€")

# Fonction pour mettre à jour un produit
def mettre_a_jour_produit():
    id_produit = int(input("ID du produit à mettre à jour : "))
    quantite = int(input("Nouvelle quantité : "))
    prix = float(input("Nouveau prix : "))
    cursor.execute("UPDATE produits SET quantite = ?, prix = ? WHERE id = ?", (quantite, prix, id_produit))
    conn.commit()
    print("Produit mis à jour avec succès !")

# Fonction pour supprimer un produit
def supprimer_produit():
    id_produit = int(input("ID du produit à supprimer : "))
    cursor.execute("DELETE FROM produits WHERE id = ?", (id_produit,))
    conn.commit()
    print("Produit supprimé avec succès !")

# Menu principal
def menu():
    while True:
        print("\n=== Gestion des Stocks ===")
        print("1. Ajouter un produit")
        print("2. Afficher les produits")
        print("3. Mettre à jour un produit")
        print("4. Supprimer un produit")
        print("5. Quitter")
        choix = input("Choisissez une option : ")

        if choix == "1":
            ajouter_produit()
        elif choix == "2":
            afficher_produits()
        elif choix == "3":
            mettre_a_jour_produit()
        elif choix == "4":
            supprimer_produit()
        elif choix == "5":
            print("Fermeture de l'application...")
            conn.close()
            break
        else:
            print("Option invalide. Veuillez réessayer.")

# Lancer le menu
if __name__ == "__main__":
    menu()
