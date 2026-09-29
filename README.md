# Préparer son environnement Python pour le workshop MNIST

Ce tuto explique comment faire tourner le notebook `workshop_deep_learning.ipynb` sur **Linux**, **Windows**, **macOS** ou **Google Colab**.

Packages nécessaires : `torch`, `torchvision`, `numpy`, `pillow`, plus `jupyter` et `ipykernel` pour le notebook.

> **Conseil :** à faire **avant** le workshop. L'installation de PyTorch pèse plusieurs centaines de Mo, et le dataset MNIST (~50 Mo) se télécharge à la première exécution.

---

## 0. Prérequis communs

- **Python 3.10, 3.11 ou 3.12** (les versions les plus sûres pour PyTorch).
- Un dossier de travail, par exemple `workshop-mnist/`, dans lequel on met le notebook.

Vérifier la version installée :

```bash
python3 --version      # Linux / macOS
py --version           # Windows
```

---

## 1. Linux (Ubuntu / Debian)

### Installer Python, venv et tkinter

tkinter sert à la fenêtre de dessin à la fin du workshop. Sur Linux, il est souvent absent par défaut.

```bash
sudo apt update
sudo apt install python3 python3-venv python3-pip python3-tk
```

(Fedora : `sudo dnf install python3 python3-tkinter`)

### Créer et activer l'environnement virtuel

```bash
cd workshop-mnist
python3 -m venv .venv
source .venv/bin/activate
```

Le prompt affiche maintenant `(.venv)`. Pour désactiver plus tard : `deactivate`.

### Installer les packages

```bash
pip install --upgrade pip
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
pip install numpy pillow jupyter ipykernel
```

> La version CPU de PyTorch suffit largement pour MNIST et est beaucoup plus légère que la version GPU.

Passer ensuite à la section **5. Enregistrer le kernel Jupyter**.

---

## 2. Windows

### Installer Python

Télécharger Python sur https://www.python.org/downloads/windows/ et, pendant l'installation, **cocher « Add python.exe to PATH »**. tkinter est inclus par défaut.

### Créer et activer l'environnement virtuel

Dans **PowerShell**, dans le dossier du workshop :

```powershell
cd workshop-mnist
py -m venv .venv
.venv\Scripts\Activate.ps1
```

Si PowerShell refuse avec une erreur « l'exécution de scripts est désactivée », lancer une fois :

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

puis relancer la commande d'activation.

Dans l'**invite de commandes (cmd)**, l'activation se fait avec :

```cmd
.venv\Scripts\activate.bat
```

### Installer les packages

```powershell
python -m pip install --upgrade pip
pip install torch torchvision
pip install numpy pillow jupyter ipykernel
```

(Sur Windows, la version par défaut de PyTorch installée par pip est déjà la version CPU.)

Passer ensuite à la section **5. Enregistrer le kernel Jupyter**.

---

## 3. macOS

### Installer Python

Deux options :

- **python.org** (le plus simple) : https://www.python.org/downloads/macos/ — tkinter est inclus.
- **Homebrew** :

  ```bash
  brew install python@3.12 python-tk@3.12
  ```

  Le paquet `python-tk` est **indispensable** pour la fenêtre de dessin avec Homebrew.

### Créer et activer l'environnement virtuel

```bash
cd workshop-mnist
python3 -m venv .venv
source .venv/bin/activate
```

### Installer les packages

```bash
pip install --upgrade pip
pip install torch torchvision
pip install numpy pillow jupyter ipykernel
```

(Fonctionne sur Mac Intel et Apple Silicon M1/M2/M3/M4.)

---

## 4. Alternative : tout faire dans VS Code

Si vous utilisez VS Code plutôt que Jupyter dans le navigateur :

1. Installer les extensions **Python** et **Jupyter** (Microsoft).
2. Créer le venv et installer les packages comme ci-dessus (terminal intégré : `Ctrl+ù` / `` Ctrl+` ``).
3. Ouvrir `workshop_deep_learning.ipynb`.
4. En haut à droite du notebook, cliquer sur **Select Kernel → Python Environments → `.venv`**.

`ipykernel` doit être installé dans le venv, sinon VS Code proposera de l'installer.

---

## 5. Enregistrer le kernel Jupyter (Linux / Windows / macOS)

L'environnement virtuel doit être **activé** (`(.venv)` visible dans le terminal).

```bash
python -m ipykernel install --user --name workshop-mnist --display-name "Python (workshop MNIST)"
```

Puis lancer Jupyter :

```bash
jupyter notebook
```

ou, si vous préférez l'interface plus moderne :

```bash
jupyter lab
```

Ouvrir `workshop_deep_learning.ipynb`, puis **Kernel → Change Kernel → Python (workshop MNIST)**.

> Si un `import torch` échoue alors que vous l'avez installé, c'est presque toujours que le notebook tourne sur le mauvais kernel. Vérifier le nom du kernel en haut à droite.

Pour supprimer le kernel après le workshop :

```bash
jupyter kernelspec uninstall workshop-mnist
```

---

## 6. Google Colab

Rien à installer : PyTorch, torchvision, numpy et Pillow sont déjà disponibles.

1. Aller sur https://colab.research.google.com
2. **Fichier → Importer un notebook** → choisir `workshop_deep_learning.ipynb`.
3. (Optionnel) **Exécution → Modifier le type d'exécution** : le CPU suffit pour MNIST.
4. Exécuter les cellules dans l'ordre.

⚠️ **Limite importante : la fenêtre de dessin (`draw_and_predict`) ne fonctionne pas sur Colab**, car tkinter a besoin d'un écran local. Sur Colab :

- tout le reste du workshop fonctionne (chargement, entraînement, précision) ;
- dans la dernière cellule, **commenter la ligne** `draw_and_predict(model, device=device)` ;
- si `import tkinter` provoque une erreur dans la première cellule, commenter aussi `import tkinter as tk`.

Le dataset est retéléchargé à chaque nouvelle session Colab (c'est rapide).

---

## 7. Vérifier que tout fonctionne

Coller cette cellule au début du notebook et l'exécuter :

```python
import sys, numpy, torch, torchvision, PIL
print("Python      :", sys.version.split()[0])
print("numpy       :", numpy.__version__)
print("torch       :", torch.__version__)
print("torchvision :", torchvision.__version__)
print("Pillow      :", PIL.__version__)
print("Kernel      :", sys.executable)   # doit pointer vers .venv (sauf sur Colab)

try:
    import tkinter
    print("tkinter     : OK")
except ImportError:
    print("tkinter     : ABSENT (fenêtre de dessin indisponible)")
```

Puis exécuter la cellule `load_mnist()` une première fois pour télécharger le dataset dans `./data`.

---

## 8. Problèmes fréquents

| Problème | Solution |
|---|---|
| `ModuleNotFoundError: No module named 'torch'` | Mauvais kernel : sélectionner « Python (workshop MNIST) » ou le `.venv`. |
| `No module named '_tkinter'` | Linux : `sudo apt install python3-tk` · macOS Homebrew : `brew install python-tk@3.12`. |
| `python` introuvable sur Windows | Réinstaller Python en cochant « Add to PATH », ou utiliser `py`. |
| Activation refusée dans PowerShell | `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`. |
| Pas de version de torch compatible | Version de Python trop récente ou trop ancienne : utiliser Python 3.10 à 3.12. |
| Le téléchargement de MNIST échoue | Vérifier la connexion (proxy d'école/entreprise), puis relancer la cellule. |
| La fenêtre de dessin ne s'ouvre pas sur Colab | Normal, voir la section 6. |

---

## Récapitulatif express

```bash
# Linux / macOS
python3 -m venv .venv
source .venv/bin/activate
pip install torch torchvision numpy pillow jupyter ipykernel
python -m ipykernel install --user --name workshop-mnist --display-name "Python (workshop MNIST)"
jupyter notebook
```

```powershell
# Windows (PowerShell)
py -m venv .venv
.venv\Scripts\Activate.ps1
pip install torch torchvision numpy pillow jupyter ipykernel
python -m ipykernel install --user --name workshop-mnist --display-name "Python (workshop MNIST)"
jupyter notebook
```
