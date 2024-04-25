# calendarbot
Бот-календарь
В современном информационном обществе, где время является одним из самых ценных ресурсов, эффективное планирование и организация дел являются неотъемлемой частью успешной и продуктивной жизни. 
Однако, на фоне быстрого темпа современного образа жизни, часто возникает необходимость в помощи в поддержании порядка и регулярности в повседневных делах. 
В связи с этим, разработка инновационных и удобных инструментов для автоматизации планирования и ведения списка дел является актуальной задачей. 

Описание частей кода:
1.	Импортируем необходимые модули:
telebot - модуль для работы с Telegram API и создания ботов.
datetime - класс для работы с датой и временем.
datetime as dt - импортируем модуль datetime с кратким именем dt.
import telebot
from datetime import datetime
import datetime as dt

2.	Создаем экземпляр бота, используя токен:
Токен нужно взять у телеграм бота BotFather. BotFather - это бот, который поможет быстро зарегистрировать нового бота в Telegram и получить API-токен для привязки к конструктору. API-токен - уникальный идентификатор, который подтверждает, что программа принадлежит вам. С его помощью можно привязать бота к разным конструкторам и управлять им.
bot = telebot.TeleBot('6864390799:AAFppPF292fS1X0Jis2S-q7bUxMHpd5qHdw')

3.	Объявляем несколько обработчиков сообщений с помощью декораторов:
@bot.message_handler(commands=['start']) - обрабатывает команду /start и отвечает приветственным сообщением.
@bot.message_handler(commands=['help']) - обрабатывает команду /help и отвечает сообщением с инструкцией.
@bot.message_handler(commands=['add']) - обрабатывает команду /add и просит пользователя ввести задание.
@bot.message_handler(func=lambda message: True) - обрабатывает все остальные сообщения от пользователя и просит указать дату напоминания:

@bot.message_handler(commands=['start'])
def send_welcome(message):
    bot.reply_to(message, "Привет! Я бот, который поможет тебе записывать список дел.")


@bot.message_handler(commands=['help'])
def get_help(message):
    bot.reply_to(message, 'Для того, чтобы записать событие, пропишите /add')


@bot.message_handler(commands=['add'])
def add_task(message):
    bot.reply_to(message, "Напишите задание (событие), которое нужно добавить в календарь.")


@bot.message_handler(func=lambda message: True)
def get_date(message):
    task = message.text
    try:
        with open('todo_list.txt', 'a') as file:
            file.write(f"[{datetime.now().strftime('%Y-%m-%d %H:%M:%S')}]: {task}\n")
        bot.reply_to(message, f"Теперь введите дату напоминания в формате ггггммддччмм:")
        bot.register_next_step_handler(message, process_date)
    except Exception as e:
        bot.reply_to(message, f"Произошла ошибка: {e}")

4.	Определяем функцию send_welcome, которая отправляет приветственное сообщение:
def get_help(message):
    bot.reply_to(message, 'Для того, чтобы записать событие, пропишите /add')

5.	Определяем функцию get_help, которая отправляет сообщение с инструкцией:
def get_help(message):
    bot.reply_to(message, 'Для того, чтобы записать событие, пропишите /add')

6.	Определяем функцию add_task, которая просит пользователя ввести задание:
def add_task(message):
    bot.reply_to(message, "Напишите задание (событие), которое нужно добавить в календарь.")

7.	Определяем функцию get_date, которая сохраняет задание и просит пользователя указать дату напоминания:
def get_date(message):
    task = message.text
    try:
        with open('todo_list.txt', 'a') as file:
            file.write(f"[{datetime.now().strftime('%Y-%m-%d %H:%M:%S')}]: {task}\n")
        bot.reply_to(message, f"Теперь введите дату напоминания в формате ггггммддччмм:")
        bot.register_next_step_handler(message, process_date)
    except Exception as e:
        bot.reply_to(message, f"Произошла ошибка: {e}")

8.	Определяем функцию process_date, которая обрабатывает указанную дату напоминания и отправляет сообщение с количеством оставшихся дней до напоминания:
def process_date(message):
    try:
        date = dt.datetime.strptime(message.text, '%Y%m%d%H%M')
        current_date = dt.datetime.now()
        if date > current_date:
            days_diff = (date - current_date).days
            bot.send_message(message.chat.id, f"Оставшиеся дни до отправки сообщения: {days_diff}")
        else:
            bot.send_message(message.chat.id, "Указанная дата уже прошла")
    except ValueError:
        bot.send_message(message.chat.id, "Некорректный формат даты. Повторите попытку.")

9.	Определяем функцию handle_message, которая сохраняет задание и отправляет сообщение о его успешном добавлении:
def handle_message(message):
    task = message.text
    try:
        with open('todo_list.txt', 'a') as file:
            file.write(f"[{datetime.now().strftime('%Y-%m-%d %H:%M:%S')}]: {task}\n")
        bot.reply_to(message, f"Задание '{task}' успешно добавлено в список дел.")
    except Exception as e:
        bot.reply_to(message, f"Произошла ошибка: {e}")

10.	Определяем функцию get_todo_list, которая возвращает список дел, напоминания для которых установлены на текущую дату и время:
def get_todo_list():
    current_datetime = datetime.now()
    formatted_current_datetime = current_datetime.strftime('%Y-%m-%d %H:%M')
    todo_list = []
    with open('todo_list.txt', 'r') as file:
        for line in file:
            if formatted_current_datetime in line:
                todo_list.append(line.split(': ')[1].strip())

    return todo_list

11.	Запускаем бота в режиме ожидания новых сообщений от пользователей: 
bot.polling()

Таким образом, функционал бота полностью настроен и готов к работе. Для того чтобы использовать его, надо зайти в Телеграм и в поисковой строке написать ссылку на бота - @Caalendar_Bot.
