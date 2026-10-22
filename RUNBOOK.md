# Simulation de day trading — argent 100 % fictif (jamais d'ordre réel)

Etat : `sim.json` (portefeuille) et `journal.csv` (trades clôturés). Outils : `skills/trader-*/` (lire le SKILL.md avant d'utiliser le script, Python 3 standard, données Yahoo gratuites). Les scripts écrivent dans `trader/` ; ce dossier est local à la session, seuls `sim.json` et `journal.csv` sont persistés.

## Règles
- Bourse US ouverte lun-ven 15h30-22h00 (Bruxelles). Fermée / férié : ne rien faire.
- Au plus 1 nouveau trade par jour, entrée à partir de 15h45 (après le range d'ouverture).
- Risque par trade = 1 % du capital actuel ; perte max du jour 3 % ; plafonné au cash.
- Taille = (capital x 1 %) / |entrée - stop| ; conversion USD->EUR au taux du jour (le noter).
- Stop obligatoire. R/R >= 1,5. Trade pris seulement si le Time Machine n'est pas un pile-ou-face et pas de résultats de l'entreprise dans la journée.
- Frais 1 EUR/ordre, slippage 0,05 %. Prix réels uniquement, jamais embellis. Données en retard ou absentes : ne pas simuler, le noter.

## A chaque passage
1. Lire sim.json. Vérifier stops/cibles des positions ouvertes sur les barres 5 min réelles depuis l'ouverture ; stop et cible dans la même barre = stop (hypothèse prudente, à noter). Clôturer si touché, calculer R et P&L net, déplacer vers `historique`, ajouter une ligne à journal.csv (papier_ou_reel = papier).
2. Passage de 15h45 : scanner (scan.py), news, entry/exit, timemachine, puis ouvrir au plus 1 trade.
3. A 21h55 environ : clôturer au dernier prix toute position encore ouverte (day trading, pas de nuit).
4. Mettre à jour sim.json (cash, positions), commit et push avec un message court.
5. Passage du soir : résumé de 5 lignes max (capital, trades du jour, R, P&L, variation depuis 5000 EUR). Tous les 10 trades clôturés : bilan win rate, R moyen, expectancy, profit factor, drawdown. Moins de 30 trades = aucune conclusion. Résultat négatif = le dire franchement.
