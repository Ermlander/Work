df2["Zawiera_wzorzec"] = df2["Tekst"].apply(lambda x: next((wzorzec for wzorzec in df1["Wzorzec"] if wzorzec in x), None))


df2["Zawiera_wzorzec"] = df2["Tekst"].str.extract(f'({pattern})', flags=re.IGNORECASE, expand=False)

for real_name in df2['realName'].to_list():
  df1.loc[ df1['userName'].str.contains(real_name), 'userName' ] = real_name



pattern = '|'.join(df1["Wzorzec"])
df2["Zawiera_wzorzec"] = df2["Tekst"].str.extract(f'({pattern})', expand=False)


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




    df['result'] = df.apply(lambda row: 'xyz' if row['a'] == row['b'] else ('xcv' if row['c'] == 0 and row['d'] == 1 and row['a'] <= 40 else None), axis=1)
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





###################


import win32com.client as win32
import os
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from bs4 import BeautifulSoup
import base64

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

# Parsowanie treści HTML
soup = BeautifulSoup(html_body, 'lxml')

# Wyszukiwanie obrazków
images = soup.find_all('img')

# Przetwarzanie i zamiana obrazków na dane base64
for img in images:
    image_cid = img['src']
    image_attachment = msg.Attachments.Item(image_cid)
    
    # Zapisywanie obrazka jako załącznik tymczasowy
    image_path = os.path.join(os.getcwd(), 'temp_image.png')
    image_attachment.SaveAsFile(image_path)
    
    # Konwersja obrazka na dane base64
    with open(image_path, 'rb') as f:
        image_data = f.read()
        image_base64 = base64.b64encode(image_data).decode('utf-8')
    
    # Zamiana ścieżki obrazka na dane base64 w treści HTML
    img['src'] = 'data:image/png;base64,' + image_base64
    
# Aktualizacja treści HTML
html_body = str(soup)

# Wysłanie e-maila z danymi ze szablonu
send_email(subject, html_body, recipients)





#################3


import win32com.client as win32
import os
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from bs4 import BeautifulSoup
import base64

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

# Parsowanie treści HTML
soup = BeautifulSoup(html_body, 'lxml')

# Wyszukiwanie obrazków
images = soup.find_all('img')

# Przetwarzanie i zamiana obrazków na dane base64
for img in images:
    image_cid = img['src']
    image_attachment = None
    
    for attachment in msg.Attachments:
        if attachment.FileName == image_cid:
            image_attachment = attachment
            break
    
    if image_attachment:
        # Zapisywanie obrazka jako załącznik tymczasowy
        image_path = os.path.join(os.getcwd(), 'temp_image.png')
        image_attachment.SaveAsFile(image_path)
        
        # Konwersja obrazka na dane base64
        with open(image_path, 'rb') as f:
            image_data = f.read()
            image_base64 = base64.b64encode(image_data).decode('utf-8')
        
        # Zamiana ścieżki obrazka na dane base64 w treści HTML
        img['src'] = 'data:image/png;base64,' + image_base64
    
# Aktualizacja treści HTML
html_body = str(soup)

# Wysłanie e-maila z danymi ze szablonu
send_email(subject, html_body, recipients)





################


import win32com.client as win32
import os
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from bs4 import BeautifulSoup
import base64

def send_email(subject, body, recipients):
    outlook = win32.Dispatch('Outlook.Application')
    mail = outlook.CreateItem(0)
    mail.Subject = subject
    mail.To = ";".join(recipients)
    mail.HTMLBody = body
    mail.Send()

# Ścieżka do pliku .msg
msg_path = 'ścieżka/do/pliku.msg'

# Odbiorcy e-maila
recipients = ['adres1@example.com', 'adres2@example.com']

# Wczytanie pliku .msg jako szablon
outlook = win32.Dispatch('Outlook.Application')
namespace = outlook.GetNamespace("MAPI")
msg_template = namespace.OpenSharedItem(msg_path)

# Pobranie tematu i treści ze szablonu
subject = msg_template.Subject
html_body = msg_template.HTMLBody

# Pobranie załączonych obrazków
attachments = msg_template.Attachments
image_tags = BeautifulSoup(html_body, 'html.parser').find_all('img')

# Zamiana załączonych obrazków na osadzone obrazy w HTML
for image_tag in image_tags:
    attachment_name = image_tag['src']
    attachment = attachments.Item(attachment_name)
    
    # Pobranie danych obrazka
    image_data = attachment.PropertyAccessor.BinaryToString(attachment.PropertyAccessor.GetProperty("http://schemas.microsoft.com/mapi/proptag/0x7FFF001E"))
    image_type = attachment.PropertyAccessor.GetProperty("http://schemas.microsoft.com/mapi/proptag/0x7FFF001F")
    
    # Konwersja danych obrazka do kodu Base64
    encoded_image_data = base64.b64encode(image_data).decode('utf-8')
    
    # Utworzenie osadzonego obrazka w HTML
    image_tag['src'] = f"data:{image_type};base64,{encoded_image_data}"
    image_tag['alt'] = attachment_name

# Wysłanie e-maila
send_email(subject, str(html_body), recipients)

# Zamknięcie szablonu
msg_template.Close(0)




df2["Zawiera_wzorzec"] = df2["Tekst"].str.extract('(' + '|'.join(df1["Wzorzec"]) + ')', expand=False)


444444



ConsecutiveMonths = 
VAR CalendarTable = 
    ADDCOLUMNS (
        CALENDAR ( MIN('NazwaTabeli'[Data]), MAX('NazwaTabeli'[Data]) ),
        "MonthYear", FORMAT ( [Date], "YYYY-MM" )
    )
VAR ConsecutiveMonthsCount = 
    MAXX (
        FILTER (
            CalendarTable,
            NOT (
                ISBLANK (
                    CALCULATE (
                        MAX ( 'NazwaTabeli'[Data] ),
                        FILTER ( 'NazwaTabeli', 'NazwaTabeli'[Data] = EARLIER ( [Date] ) )
                    )
                )
            )
            &&
            NOT (
                ISBLANK (
                    CALCULATE (
                        MAX ( 'NazwaTabeli'[Data] ),
                        FILTER ( 'NazwaTabeli', 'NazwaTabeli'[Data] = EDATE ( EARLIER ( [Date] ), -1 ) )
                    )
                )
            )
        ),
        [MonthYear]
    )
RETURN
    ConsecutiveMonthsCount

W powyższym kodzie, zastąp 'NazwaTabeli' odpowiednią nazwą twojej tabeli.

Opis działania funkcji:

    Tworzymy zmienną CalendarTable, która jest tabelą kalendarza zawierającą wszystkie daty od najwcześniejszej do najpóźniejszej daty w kolumnie "Data".
    Tworzymy zmienną ConsecutiveMonthsCount, która bę

ConsecutiveMonths = 
VAR CalendarTable = 
    ADDCOLUMNS (
        CALENDAR ( MIN('NazwaTabeli'[Data]), MAX('NazwaTabeli'[Data]) ),
        "MonthYear", FORMAT ( [Date], "YYYY-MM" )
    )
VAR ConsecutiveMonthsCount = 
    MAXX (
        FILTER (
            CalendarTable,
            NOT (
                ISBLANK (
                    CALCULATE (
                        MAX ( 'NazwaTabeli'[Data] ),
                        FILTER ( 'NazwaTabeli', 'NazwaTabeli'[Data] = EARLIER ( [Date] ) )
                    )
                )
            )
            &&
            NOT (
                ISBLANK (
                    CALCULATE (
                        MAX ( 'NazwaTabeli'[Data] ),
                        FILTER ( 'NazwaTabeli', 'NazwaTabeli'[Data] = EDATE ( EARLIER ( [Date] ), -1 ) )
                    )
                )
            )
        ),
        [MonthYear]
    )
RETURN
    ConsecutiveMonthsCount

W powyższym kodzie, zastąp 'NazwaTabeli' odpowiednią nazwą twojej tabeli.

Opis działania funkcji:

    Tworzymy zmienną CalendarTable, która jest tabelą kalendarza zawierającą wszystkie daty od najwcześniejszej do najpóźniejszej daty w kolumnie "Data".
    Tworzymy zmienną ConsecutiveMonthsCount, która bę



4444234



ConsecutiveMonthsCount = 
VAR CalendarTable = 
    ADDCOLUMNS (
        CALENDAR ( MIN('NazwaTabeli'[Data]), MAX('NazwaTabeli'[Data]) ),
        "MonthYear", FORMAT ( [Date], "YYYY-MM" )
    )
VAR ConsecutiveMonthsGroup =
    ADDCOLUMNS (
        CalendarTable,
        "PreviousMonth",
        CALCULATE (
            MAX ( 'NazwaTabeli'[Data] ),
            FILTER ( 'NazwaTabeli', 'NazwaTabeli'[Data] < EARLIER ( [Date] ) )
        )
    )
VAR ConsecutiveMonthsCount =
    COUNTROWS (
        FILTER (
            SUMMARIZE (
                ConsecutiveMonthsGroup,
                [MonthYear],
                "PreviousMonth",
                [PreviousMonth]
            ),
            [MonthYear] = [PreviousMonth]
        )
    )
RETURN
    ConsecutiveMonthsCount




    
df['result'] = df.apply(lambda row: 'xyz' if row['a'] == row['b'] else ('xcv' if row['c'] == 0 and row['d'] == 1 and row['a'] <= 40 else None), axis=1)



# Przykładowy DataFrame
data = {'a': [35, 40, 45, 50, 30],
        'b': [35, 42, 45, 50, 25],
        'c': [0, 1, 0, 1, 0],
        'd': [1, 0, 1, 0, 1]}
df = pd.DataFrame(data)

# Tworzymy nową kolumnę 'result' zgodnie z twoimi warunkami
df['result'] = df.apply(lambda row: 'xyz' if row['a'] == row['b'] else 
                          ('xcv' if row['c'] == 0 and row['d'] == 1 and row['a'] <= 40 else
                            ('abc' if row['c'] == 1 and row['d'] == 0 and row['a'] > 40 else 
                             ('pqr' if row['c'] == 0 and row['d'] == 0 and row['b'] > 40 else None))), axis=1)

print(df)

df_sentences['found_words'] = df_sentences['sentences'].apply(lambda x: ', '.join([word for word in df_words['words'] if word in x]))



.replace(['[', ']'], '', regex=True)


######

MonthsBelow :=
VAR PreviousRowResult =
    CALCULATE(
        FIRSTNONBLANK('TableName'[wynik], 1),
        FILTER(
            ALL('TableName'),
            'TableName'[User] = EARLIER('TableName'[User]) &&
            'TableName'[miesiąc] < EARLIER('TableName'[miesiąc])
        )
    )
RETURN
    IF('TableName'[wynik] = "Below" && (ISBLANK(PreviousRowResult) || PreviousRowResult = "Below"), 1, 0)







    #####



    SumBelowWithPrevious :=
VAR PreviousRowResult =
    CALCULATE(
        SUM('TableName'[wynik]),
        FILTER(
            ALL('TableName'),
            'TableName'[User] = EARLIER('TableName'[User]) &&
            'TableName'[miesiąc] < EARLIER('TableName'[miesiąc])
        )
    )
RETURN
    IF('TableName'[wynik] = "Below", 'TableName'[wynik] + IF(ISBLANK(PreviousRowResult), 0, PreviousRowResult), 0)




import pandas as pd

# Dane wejściowe
data = {'Id_main': [123, 123, 123, 456, 456, 789],
        'Id_step': ['L1 1st', 'L1 2nd', 'L1 st', 'L2 1st', 'L2 2nd']}

data2 = {'Id_main': [123, 123, 123, 456, 456, 789],
         'L1 1st input': [222, 2223, 333, 2, 13],
         'L1 1st output': [222, 2223, 333, 2, 13],
         'L1 2nd input': [222, 2223, 333, 2, 13],
         'L1 2nd output': [222, 2223, 333, 2, 13]}

# Tworzenie ramki danych
df = pd.DataFrame(data)
df2 = pd.DataFrame(data2)

# Dodanie dwóch pustych kolumn do ramki danych "data"
df['input'] = ''
df['output'] = ''

# Wybór rekordu z data2 na podstawie wartości Id_main i kawałka nazwy Id_step
id_main = 123
id_step_part = 'L1 1st'

# Konstrukcja nazwy kolumny na podstawie Id_step
column_name = f"{id_step_part} input"

# Wybór rekordu na podstawie Id_main i kolumny
selected_record = df2.loc[df2['Id_main'] == id_main]

# Uzupełnienie pustych kolumn
df.loc[df['Id_main'] == id_main, 'input'] = selected_record[column_name].values[0]
df.loc[df['Id_main'] == id_main, 'output'] = selected_record[column_name.replace('input', 'output')].values[0]

print(df)




def fill_data(row):
    id_main = row['Id_main']
    match_no = row['Match No']
    id_step = row['Id_step']
    column_name = f"{id_step.split()[0]} {id_step.split()[1]} input"
    row['Input'] = df2.loc[(df2['Id_main'] == id_main) & (df2['Match No'] == match_no), column_name].values[0]
    row['Output'] = df2.loc[(df2['Id_main'] == id_main) & (df2['Match No'] == match_no), column_name.replace('input', 'output')].values[0]
    return row

# Uzupełnienie danych w ramce danych 'data'
df = df.apply(fill_data, axis=1)


################################



def fill_output(row):
    id_main = row['Id_main']
    match_no = row['Match No']
    id_step = row['Id_step']
    column_name = f"{id_step.split()[0]} {id_step.split()[1]} output"
    # Wybór rekordu na podstawie Id_main i Match No
    selected_record = df2.loc[(df2['Id_main'] == id_main) & (df2['Match No'] == match_no)]
    # Uzupełnienie kolumny 'Output'
    row['Output'] = selected_record[column_name].values[0] if not selected_record.empty else ''
    return row

# Uzupełnienie danych w ramce danych 'data' dla kolumny "Output"
df = df.apply(fill_output, axis=1)


################

column_name = f"{parts[0]} {parts[-2]} {parts[-1]} Output"



def is_vessel(text):
    keywords = [
        'Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker',
        'DWT', 'GRT', 'flag', 'IMO', 'MMSI',
        'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag',
        'Additional Sanctions Information—Subject to Secondary Sanctions',
        'Linked To:'
    ]
    for keyword in keywords:
        if re.search(keyword, text):
            return True
    return False




def extract_keywords(text):
    # Define a regular expression pattern to match words
    pattern = r'\b\w+\b'
    # Find all words in the text using the regular expression pattern
    words = re.findall(pattern, text)
    # Filter out words with length less than 3 characters
    keywords = [word for word in words if len(word) > 2]
    # Convert keywords to lowercase to standardize them
    keywords = [word.lower() for word in keywords]
    # Remove duplicates
    unique_keywords = list(set(keywords))
    return unique_keywords

# Example DataFrame
data = {
    'text_column': [
        "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY).",
        "NYOS (f.k.a. BRAWNY; f.k.a. MARIGOLD; f.k.a. NABI) (T2DS4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079080; MMSI 572443210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY).",
        "SANCHI (f.k.a. GARDENIA; f.k.a. SEAHORSE; f.k.a. SEPID) (T2EF4) Crude Oil Tanker 164,154DWT 85,462GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9356608; MMSI 572455210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY).",
        # Add more texts here...
    ]
}

df = pd.DataFrame(data)

# Apply the extract_keywords function to each row in the DataFrame and save the result in a new column
df['keywords'] = df['text_column'].apply(extract_keywords)

print(df)




import re

def categorize_text(text):
    categories = {
        'Entity': ['Corporation', 'Company', 'Organization', 'Firm', 'Enterprise'],
        'Individual': ['Person', 'Individual', 'Human', 'Citizen'],
        'Location/Place': ['Country', 'City', 'Town', 'State', 'Region', 'Location', 'Place'],
        'Vessel': ['Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker', 'DWT', 'GRT', 'flag', 'IMO', 'MMSI', 'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag', 'Additional Sanctions Information—Subject to Secondary Sanctions', 'Linked To:']
    }
    
    # Initialize counts for each category
    category_counts = {category: 0 for category in categories}
    
    # Count the number of keywords in each category found in the text
    for category, keywords in categories.items():
        for keyword in keywords:
            if re.search(keyword, text):
                category_counts[category] += 1
    
    # Determine the category with the most keywords found
    max_category = max(category_counts, key=category_counts.get)
    
    return max_category

# Example text
example_text = "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY)."

# Categorize the example text
result_category = categorize_text(example_text)
print("Result Category:", result_category)



##############################



import re

def categorize_text(text):
    categories = {
        'Entity': {
            'keywords': ['Corporation', 'Company', 'Organization', 'Firm', 'Enterprise'],
            'points': 1
        },
        'Individual': {
            'keywords': ['Person', 'Individual', 'Human', 'Citizen', 'Type - I', 'Type:I'],
            'points': 5
        },
        'Location/Place': {
            'keywords': ['Country', 'City', 'Town', 'State', 'Region', 'Location', 'Place', 'this is location'],
            'points': 1
        },
        'Vessel': {
            'keywords': ['Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker', 'DWT', 'GRT', 'flag', 'IMO', 'MMSI', 'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag', 'Additional Sanctions Information—Subject to Secondary Sanctions', 'Linked To:'],
            'points': 1
        }
    }
    
    # Initialize counts and points for each category
    category_info = {category: {'count': 0, 'points': 0} for category in categories}
    
    # Count the number of keywords and accumulate points in each category found in the text
    for category, info in categories.items():
        for keyword in info['keywords']:
            if re.search(keyword, text):
                category_info[category]['count'] += 1
                category_info[category]['points'] += info['points']
    
    # Filter categories with at least two keywords found
    eligible_categories = {category: info for category, info in category_info.items() if info['count'] >= 2}
    
    # If no category has at least two keywords found, return "Cannot determine"
    if not eligible_categories:
        return "Cannot determine"
    
    # Find the category with the maximum points
    max_category = max(eligible_categories, key=lambda k: eligible_categories[k]['points'])
    
    # Check if there is a draw
    if sum(info['points'] for info in eligible_categories.values()) > eligible_categories[max_category]['points']:
        return "Cannot determine"
    
    return max_category

# Example text
example_text = "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY)."

# Categorize the example text
result_category = categorize_text(example_text)
print("Result Category:", result_category)




#############################################


import re

def categorize_text(text):
    categories = {
        'Entity': {
            'keywords': ['Corporation', 'Company', 'Organization', 'Firm', 'Enterprise'],
            'points': 1
        },
        'Individual': {
            'keywords': ['Person', 'Individual', 'Human', 'Citizen', 'Type - I', 'Type:I'],
            'points': 5
        },
        'Location/Place': {
            'keywords': ['Country', 'City', 'Town', 'State', 'Region', 'Location', 'Place', 'this is location'],
            'points': 1
        },
        'Vessel': {
            'keywords': ['Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker', 'DWT', 'GRT', 'flag', 'IMO', 'MMSI', 'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag', 'Additional Sanctions Information—Subject to Secondary Sanctions', 'Linked To:'],
            'points': 1
        }
    }
    
    # Initialize counts and points for each category
    category_info = {category: {'count': 0, 'points': 0} for category in categories}
    
    # Count the number of keywords and accumulate points in each category found in the text
    for category, info in categories.items():
        for keyword in info['keywords']:
            if re.search(keyword, text):
                category_info[category]['count'] += 1
                category_info[category]['points'] += info['points']
    
    # Filter categories with at least two keywords found
    eligible_categories = {category: info for category, info in category_info.items() if info['count'] >= 2}
    
    # If no category has at least two keywords found, return "Cannot determine"
    if not eligible_categories:
        return "Cannot determine"
    
    # Find the category with the maximum points
    max_category = max(eligible_categories, key=lambda k: eligible_categories[k]['points'])
    
    # Check if there is a draw
    if sum(info['points'] for info in eligible_categories.values()) > eligible_categories[max_category]['points']:
        return "Cannot determine"
    
    return max_category



    ###################################







    import re

def categorize_text(text):
    categories = {
        'Entity': {
            'keywords': ['Corporation', 'Company', 'Organization', 'Firm', 'Enterprise'],
            'points': 1
        },
        'Individual': {
            'keywords': ['Person', 'Individual', 'Human', 'Citizen', 'Type - I', 'Type:I'],
            'points': [1, 2, 1, 1, 5, 5]
        },
        'Location/Place': {
            'keywords': ['Country', 'City', 'Town', 'State', 'Region', 'Location', 'Place', 'this is location'],
            'points': 1
        },
        'Vessel': {
            'keywords': ['Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker', 'DWT', 'GRT', 'flag', 'IMO', 'MMSI', 'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag', 'Additional Sanctions Information—Subject to Secondary Sanctions', 'Linked To:'],
            'points': 1
        }
    }
    
    # Initialize counts and points for each category
    category_info = {category: {'count': 0, 'points': 0} for category in categories}
    
    # Count the number of keywords and accumulate points in each category found in the text
    for category, info in categories.items():
        for keyword, points in zip(info['keywords'], info['points']):
            if re.search(keyword, text):
                category_info[category]['count'] += 1
                category_info[category]['points'] += points
    
    # Filter categories with at least two keywords found
    eligible_categories = {category: info for category, info in category_info.items() if info['count'] >= 2}
    
    # If no category has at least two keywords found, return "Cannot determine"
    if not eligible_categories:
        return "Cannot determine"
    
    # Find the category with the maximum points
    max_category = max(eligible_categories, key=lambda k: eligible_categories[k]['points'])
    
    # Check if there is a draw
    if sum(info['points'] for info in eligible_categories.values()) > eligible_categories[max_category]['points']:
        return "Cannot determine"
    
    return max_category

# Example text
example_text = "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY)."

# Categorize the example text
result_category = categorize_text(example_text)
print("Result Category:", result_category)

# Example text
example_text = "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY)."

# Categorize the example text
result_category = categorize_text(example_text)
print("Result Category:", result_category)




##############################


import re

def categorize_text(text):
    categories = {
        'Entity': {
            'keywords': ['Corporation', 'Company', 'Organization', 'Firm', 'Enterprise'],
            'points': 1
        },
        'Individual': {
            'keywords': ['Person', 'Individual', 'Human', 'Citizen', 'Type - I', 'Type:I'],
            'points': [1, 2, 1, 1, 5, 5]
        },
        'Location/Place': {
            'keywords': ['Country', 'City', 'Town', 'State', 'Region', 'Location', 'Place', 'this is location'],
            'points': 1
        },
        'Vessel': {
            'keywords': ['Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker', 'DWT', 'GRT', 'flag', 'IMO', 'MMSI', 'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag', 'Additional Sanctions Information—Subject to Secondary Sanctions', 'Linked To:'],
            'points': 1
        }
    }
    
    # Initialize points for each category
    category_points = {category: 0 for category in categories}
    
    # Accumulate points for each category found in the text
    for category, info in categories.items():
        for keyword, points in zip(info['keywords'], info['points']):
            if re.search(keyword, text):
                category_points[category] += points
    
    # Find the category with the maximum points
    max_category = max(category_points, key=category_points.get)
    
    # Check if there is a draw
    if sum(category_points.values()) > category_points[max_category]:
        return "Cannot determine"
    
    return max_category

# Example text
example_text = "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY)."

# Categorize the example text
result_category = categorize_text(example_text)
print("Result Category:", result_category)

################


import re

def categorize_text(text):
    categories = {
        'Entity': {
            'keywords': ['Corporation', 'Company', 'Organization', 'Firm', 'Enterprise'],
            'points': 1
        },
        'Individual': {
            'keywords': ['Person', 'Individual', 'Human', 'Citizen', 'Type - I', 'Type:I'],
            'points': [1, 2, 1, 1, 5, 5]
        },
        'Location/Place': {
            'keywords': ['Country', 'City', 'Town', 'State', 'Region', 'Location', 'Place', 'this is location'],
            'points': 1
        },
        'Vessel': {
            'keywords': ['Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker', 'DWT', 'GRT', 'flag', 'IMO', 'MMSI', 'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag', 'Additional Sanctions Information—Subject to Secondary Sanctions', 'Linked To:'],
            'points': 1
        }
    }
    
    # Initialize points for each category
    category_points = {category: 0 for category in categories}
    
    # Accumulate points for each category found in the text
    for category, info in categories.items():
        for keyword, points in zip(info['keywords'], info['points']):
            if re.search(keyword, text):
                category_points[category] += points
    
    # Find the category with the maximum points
    max_category = max(category_points, key=category_points.get)
    
    # Check if there is a draw
    if sum(category_points.values()) > category_points[max_category]:
        return "Cannot determine"
    
    return max_category

    ######################


    import re

def categorize_text(text):
    categories = {
        'Entity': {
            'keywords': ['Corporation', 'Company', 'Organization', 'Firm', 'Enterprise'],
            'points': 1
        },
        'Individual': {
            'keywords': ['Person', 'Individual', 'Human', 'Citizen', 'Type - I', 'Type:I'],
            'points': [1, 2, 1, 1, 5, 5]
        },
        'Location/Place': {
            'keywords': ['Country', 'City', 'Town', 'State', 'Region', 'Location', 'Place', 'this is location'],
            'points': 1
        },
        'Vessel': {
            'keywords': ['Crude Oil Tanker', 'LPG Tanker', 'Shuttle Tanker', 'Chemical/Products Tanker', 'DWT', 'GRT', 'flag', 'IMO', 'MMSI', 'None Identified flag', 'Iran flag', 'Mongolia flag', 'Panama flag', 'Additional Sanctions Information—Subject to Secondary Sanctions', 'Linked To:'],
            'points': 1
        }
    }
    
    # Initialize points for each category
    category_points = {category: 0 for category in categories}
    
    # Accumulate points for each category found in the text
    for category, info in categories.items():
        for keyword, points in zip(info['keywords'], info['points']):
            if re.search(keyword, text):
                category_points[category] += points
    
    # Find the category with the maximum points
    max_category = max(category_points, key=category_points.get)
    
    # Check if there is a draw
    if sum(category_points.values()) > category_points[max_category]:
        return "Cannot determine"
    
    return max_category

# Example text
example_text = "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY)."

# Categorize the example text
result_category = categorize_text(example_text)
print("Result Category:", result_category)

# Example text
example_text = "NAINITAL (f.k.a. MIDSEA; f.k.a. MOTION; f.k.a. NAJM) (T2DR4) Crude Oil Tanker 298,731DWT 156,809GRT None Identified flag; Former Vessel Flag Malta; alt. Former Vessel Flag Tuvalu; alt. Former Vessel Flag Tanzania; Additional Sanctions Information—Subject to Secondary Sanctions; Vessel Registration Identification IMO 9079092; MMSI 572442210 (vessel) [IRAN] (Linked To: NATIONAL IRANIAN TANKER COMPANY)."

# Categorize the example text
result_category = categorize_text(example_text)
print("Result Category:", result_category)

