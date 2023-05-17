import pandas as pd

# Konwertowanie listy tabela na DataFrame
df = pd.DataFrame(tabela, columns=["Nazwa użytkownika", "Miesiąc", "Rok", "Wynik w procentach", "Miesiąc z rzędu poniżej 95%"])

months_below_95 = 0
previous_user = None

for index, row in df.iterrows():
    if row["Nazwa użytkownika"] != previous_user:
        months_below_95 = 0  # Resetuj licznik przy zmianie użytkownika
    if row["Wynik w procentach"] >= 95:
        months_below_95 = 0  # Resetuj licznik, jeśli wynik jest 95% lub więcej
    else:
        months_below_95 += 1
    df.at[index, "Miesiąc z rzędu poniżej 95%"] = months_below_95
    previous_user = row["Nazwa użytkownika"]
