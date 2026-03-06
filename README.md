# Email-Verification-System

Пошаговое руководство по созданию системы регистрации с подтверждением по email.
Такой способ можете использовать в ваших проектах, где требуется подтверждение по email.

## 📝 Пошаговое описание кода

### Шаг 1: Импорт библиотек

```python
import smtplib          # для отправки email
import random           # для генерации случайных кодов
import ssl              # для безопасного SSL-соединения
from email.mime.text import MIMEText           # для создания текста письма
from email.mime.multipart import MIMEMultipart # для создания HTML-письма
import sqlite3          # для работы с базой данных
```

### Шаг 2: Подключение к базе данных

```python
conn = sqlite3.connect("myDBDBDB")
cursor = conn.cursor()
```

### Шаг 3: Ввод email и пароля пользователя

```python
print("введите ваш адрес почты и пароль")
email = input()
```

### Шаг 4: Проверка сложности пароля

```python
while True:
    passwd = input()
    if len(passwd) < 8:
        print("пароль короткий")
    else:
        print('пароль принят')
        break
```

### Шаг 5: Генерация кодов

```python
code = str(random.randint(100000, 999999))
id = str(random.randint(10000, 99999))
```

### Шаг 6: Создание таблицы в базе данных

```python
cursor.execute('''CREATE TABLE IF NOT EXISTS USERS(
ID INTEGER PRIMARY KEY AUTOINCREMENT,
EMAIL TEXT,
PASSWORD TEXT,
CODE TEXT
)
''')
```

### Шаг 7: Проверка существующего пользователя

```python
cursor.execute('''SELECT * FROM USERS WHERE EMAIL = ? AND PASSWORD = ?''', (email, passwd))
naiden = cursor.fetchone()
```


### Шаг 8: Логика входа/регистрации

```python
if naiden:
    print("вы вошли")
else:
    print("введите пароль который мы отправили на вашу почту:")
    send_to_mail()  # Отправка кода на почту
```

### Шаг 9: Создание функции отправки email

```python
def send_to_mail():
    mailru_ = "---" #адрес вашей почты
    mailru_pass = "---" #пароль внешнего приложения
    send_email = email
```

### Шаг 10: Создание HTML-письма

```python
msg = MIMEMultipart('alternative')
msg['From'] = mailru_
msg['To'] = send_email
msg['Subject'] = 'Code'

body = f"""
    <!DOCTYPE html>
    <html>
    <head>
        <style>
            .code {{
                background: #007bff;
                color: white;
                padding: 15px 30px;
                font-size: 24px;
                border-radius: 5px;
            }}
        </style>
    </head>
    <body>
        <div class="code">{code}</div>
    </body>
    </html>
"""
```

### Шаг 11: Прикрепление HTML к письму

```python
msg.attach(MIMEText(body, 'html', 'utf-8'))
```

### Шаг 12: Настройка SSL и отправка

```python
method = "SSL"
port = 465
context = ssl._create_unverified_context()

if method == 'SSL':
    with smtplib.SMTP_SSL('smtp.mail.ru', port, context=context) as server:
        server.login(mailru_, mailru_pass)
        server.send_message(msg)
```

### Шаг 13: Завершение регистрации

```python
password = input()
if password == code:
    print(f'вы зарегистрироавлись, ваш код: #{id}')
    cursor.execute('''INSERT INTO USERS(EMAIL, PASSWORD, CODE) VALUES(?, ?, ?)''', (email, passwd, id))
else:
    print('Неверный код!')
```

### Шаг 14: Сохранение и закрытие БД

```python
conn.commit()
conn.close()
```

### Шаг 15: Запуск программы

```python
if __name__ == '__main__':
    data_base()
```

## 📁 Структура базы данных

```sql
TABLE USERS (
    ID        INTEGER PRIMARY KEY AUTOINCREMENT,  -- автоматически увеличивается
    EMAIL     TEXT,                                -- email пользователя
    PASSWORD  TEXT,                                -- пароль
    CODE      TEXT                                 -- уникальный код
)
```

## ✅ Проверка работы

1. **Первый запуск (новый пользователь)**
   - Введите email
   - Введите пароль (минимум 8 символов)
   - Получите код на почту
   - Введите код → регистрация

2. **Второй запуск (существующий пользователь)**
   - Введите тот же email и пароль
   - Программа сразу напишет "вы вошли"

## ⚡ Важные моменты

- **Пароль от почты** - используйте пароль для внешних приложений, а не основной
- **Безопасность** - в реальном проекте пароли нужно хешировать
- **Кодировка** - везде используем UTF-8 для русского языка

---
