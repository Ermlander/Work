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



############3



import win32com.client as win32

def send_email(subject, body, recipients, attachments=None):
    outlook = win32.Dispatch('Outlook.Application')
    mail = outlook.CreateItem(0)
    mail.Subject = subject
    mail.Body = body
    mail.To = ";".join(recipients)
    
    if attachments:
        for attachment in attachments:
            mail.Attachments.Add(attachment)
    
    mail.Send()

# Ścieżka do pliku .msg
msg_path = 'ścieżka/do/pliku.msg'

# Odbiorcy e-maila
recipients = ['adres1@example.com', 'adres2@example.com']

# Wczytanie pliku .msg jako szablon
outlook = win32.Dispatch('Outlook.Application')
namespace = outlook.GetNamespace("MAPI")
msg = namespace.OpenSharedItem(msg_path)

# Pobranie danych z szablonu
subject = msg.Subject
body = msg.Body

# Wysłanie e-maila z danymi ze szablonu
send_email(subject, body, recipients)





###############


import win32com.client as win32
import os
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.image import MIMEImage

def send_email(subject, body, recipients, attachments=None):
    outlook = win32.Dispatch('Outlook.Application')
    mail = outlook.CreateItem(0)
    mail.Subject = subject
    mail.To = ";".join(recipients)
    
    if attachments:
        for attachment in attachments:
            mail.Attachments.Add(attachment)
    
    mail.HTMLBody = body
    mail.Send()

# Ścieżka do pliku .msg
msg_path = 'ścieżka/do/pliku.msg'

# Odbiorcy e-maila
recipients = ['adres1@example.com', 'adres2@example.com']

# Wczytanie pliku .msg jako szablon
outlook = win32.Dispatch('Outlook.Application')
namespace = outlook.GetNamespace("MAPI")
msg = namespace.OpenSharedItem(msg_path)

# Pobranie tematu i treści ze szablonu
subject = msg.Subject
html_body = msg.HTMLBody

# Pobranie obrazków z HTML i zapisanie ich jako załączniki tymczasowe
image_attachments = []
for cid in msg.HTMLBody:
    attachment = msg.Attachments.Add(os.path.join(os.getcwd(), 'temp_image.png'))
    attachment.PropertyAccessor.SetProperty("http://schemas.microsoft.com/mapi/proptag/0x3712001F", cid)
    image_attachments.append(attachment)

# Wysłanie e-maila z danymi ze szablonu
send_email(subject, html_body, recipients, image_attachments)
