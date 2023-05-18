import pandas as pd

# Konwertowanie listy tabela na DataFrame
df = pd.DataFrame(tabela, columns=["Nazwa użytkownika", "Miesiąc", "Rok", "Wynik w procentach", "Miesiąc z rzędu poniżej 95%"])

df["Miesiąc z rzędu poniżej 95%"] = df.groupby("Nazwa użytkownika", group_keys=False).apply(
    lambda group: group.groupby((group["Wynik w procentach"] >= 95).cumsum().rename(None))["Wynik w procentach"].apply(
        lambda x: (x < 95).cumsum().mask(x >= 95, 0)
    )
).reset_index(drop=True)

# Wyświetlenie zaktualizowanego DataFrame
print(df)





#####


months_below_95 = df.groupby("Nazwa użytkownika")["Wynik w procentach"].apply(
    lambda x: (x < 95).cumsum().where(x < 95, 0)
)

df["Miesiąc z rzędu poniżej 95%"] = months_below_95.groupby(df["Nazwa użytkownika"]).apply(
    lambda x: x.groupby((x != x.shift()).cumsum()).cumsum()
)


#########


df["Liczba kolejnych miesięcy poniżej 95%"] = df.groupby("Pracownik")["Wynik"].apply(
    lambda x: (x < 0.95).cumsum().mask(x >= 0.95, 0)
)
