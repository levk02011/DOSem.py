import requests
import time
import schedule
import smtplib
from email.mime.text import MIMEText

# URL веб-сайту, на який ви хочете заходити
target_url = "https://example.com"

# Кількість запитів, які ви хочете виконати
num_requests = 10

# Інтервал між запитами (в секундах)
request_interval = 10000

# Функція для виконання запитів
def make_requests():
    for i in range(num_requests):
        try:
            response = requests.get(target_url)
            print(f"Запит {i + 1}: Код відповіді: {response.status_code}")
        except requests.RequestException as e:
            print(f"Запит {i + 1}: Помилка: {e}")
        time.sleep(request_interval)

# Розклад для перезапуску кожні 10 000 секунд
schedule.every(10000).seconds.do(make_requests)

# Функція для відправки результатів на пошту
def send_email():
    msg = MIMEText("Програма завершила виконання.")
    msg["Subject"] = "Результати запитів на сайт"
    msg["From"] = "перший@gmail.com"
    msg["To"] = "другий@gmail.com"

    # Встановлення параметрів SMTP-сервера
    smtp_server = "smtp.example.com"
    smtp_port = 587
    smtp_username = "перший@gmail.com"
    smtp_password = "пароль до 1"

    with smtplib.SMTP(smtp_server, smtp_port) as server:
        server.starttls()
        server.login(smtp_username, smtp_password)
        server.sendmail(smtp_username, [msg["To"]], msg.as_string())

# Розклад для відправки результатів на пошту щогодини
schedule.every().hour.do(send_email)

# Запуск розкладу
while True:
    schedule.run_pending()
    time.sleep(1)
