##Artsiom Kalita
__Addres:__ _Minsk, Odoevskogo, 101a, 236_
 __Cell number:__ _+375 25 547 34 84_
__E-mail:__ _vas.vollkov@gmail.com_
###Career Objective:
_Third-year student of the Minsk College of Electronics is interested to work as JS/Front-end  programer. I am ready to use my skills in programming for the further growth of your company i promise a serious approach and dedication. I am very active, ambitious and well motivated person_
###Key Skills:
- Ability to create simple but effective prorgramms management softwares using HTML/CSS, Python, C++
- I am good in С++ and Python
###Languages:
_Russian (native),  English (fluent)_
###Professional Experience:
Finished courses at the IT Academy in programming languages: C++, HTML/CSS, Python, Unity
### Example of my code (Python):
import telebot
from telebot import types

name_costs = ''
sum = 0
money = 0
bot = telebot.TeleBot("1790801903:AAGE_9ZHFjUaS_Vjn7JBzlzFmEsNHJ0xFSA")


@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
    sti = open('welcome.webp', 'rb')
    bot.send_sticker(message.chat.id, sti)
    bot.send_message(message.from_user.id, "Я бот для учёта расходов.\n"
                                           "Показать расходы за текущий месяц /show\n"
                                           "Добавить расходы /add\n"
                                           "Всего расходов за текущий месяц /all\n"
                                           "удалить элемент /del\n"
                                           "узнать о статье /show_elem\n"
                                           "Начать новый месяц /new_month")


def show():
    mes = ''
    with open("COSTS.txt", "r") as cost:
        for line in cost:
            mes += line
        return mes


def add_sum():
    with open("COSTS.txt", "a") as cost:
        cost.write(name_costs + " " + str(sum) + "\n")


def deli(message):
    h = int(message.text)
    lst = []
    with open("COSTS.txt", "r") as cost:
        for line in cost:
            line = line.split()
            lst.append(line)
    for i in range(len(lst)):
        if i == h - 1:
            del (lst[i])


    with open("COSTS.txt", "w") as cost:
        for line in lst:
            cost.write(line[0] + " " + line[1] + "\n")
    bot.send_message(message.from_user.id, "Элемент удален:\n" + show())

def show_elem(messege):
    h=messege.text
    t=0
    lst=[]
    with open("COSTS.txt", "r") as cost:
        for line in cost:
            line = line.split()
            lst.append(line)
    for i in range(len(lst)):
        for j in range(len(lst[i])):
                if lst[i][j] ==h:
                    bot.send_message(messege.from_user.id,lst[i][j]+" "+lst[i][j+1])
                    t+=1

    if t==0:
        bot.send_message(messege.from_user.id, "Такого элемента нет, добавить элемент?")
        bot.register_next_step_handler(messege,show_elem1)

def show_elem1(messege):
    if messege.text=="да":
        bot.send_message(messege.from_user.id, "Напиши название пункта расхода")
        bot.register_next_step_handler(messege, add_costs)
    else:
        bot.send_message(messege.from_user.id, "Как хотите")








def all_costs():
    global money

    b = money
    print(b)
    m = 0
    with open("COSTS.txt", "r") as cost:
        for line in cost:
            line = line.split()
        for data in line:
            try:
                m = int(data)
            except(Exception):
                continue
            else:
                money += m
        print(money)
        money -= b


def new_month():
    lst = []
    m = 0
    with open("COSTS.txt", "r") as cost:
        for line in cost:
            line = line.split()
            line[1] = str(0)
            lst.append(line)
    with open("COSTS.txt", "w") as cost:
        for line in lst:
            cost.write(line[0] + " " + line[1] + "\n")


@bot.message_handler(func=lambda m: True)
def get_text_messages(message):
    if message.text == '/show':
        bot.send_message(message.from_user.id, 'Твои расходы за текущий месяц\n' + show())
    elif message.text == '/add':
        bot.send_message(message.from_user.id, "Напиши название пункта расхода")
        bot.register_next_step_handler(message, add_costs)
    elif message.text == '/all':
        all_costs()
        bot.send_message(message.from_user.id, 'Cумма расходов за текущий месяц ' + str(money))
    elif message.text == '/del':
        bot.register_next_step_handler(message, deli)
        bot.send_message(message.from_user.id, "Выбири элемент:\n" + show())
    elif message.text=='/show_elem':
        bot.register_next_step_handler(message, show_elem)




    elif message.text == '/new_month':
        keyboard = types.InlineKeyboardMarkup()
        key_yes = types.InlineKeyboardButton(text='Да', callback_data='yes')
        keyboard.add(key_yes)
        key_no = types.InlineKeyboardButton(text='Нет', callback_data='no')
        keyboard.add(key_no)
        bot.send_message(message.from_user.id, text="Новый месяц?", reply_markup=keyboard)
    else:
        bot.send_message(message.from_user.id, "Я тебя не понимаю. Напиши /help или /start.")


def add_costs(message):
    global name_costs

    name_costs = message.text



    bot.send_message(message.from_user.id, "Напиши сумму расходов")
    bot.register_next_step_handler(message, reg_sum)





def reg_sum(message):
    global sum
    sum = int(message.text)
    add_sum()
    bot.send_message(message.from_user.id, "добавлено: " + name_costs + " " + str(sum))


@bot.callback_query_handler(func=lambda call: True)
def callback_worker(call):
    if call.data == "yes":
        new_month()
        bot.send_message(call.message.chat.id, "вот и наступил новый месяц!\n" + show())
    elif call.data == "no":
        bot.send_message(call.message.chat.id, "продолжаем подсчёт в текущем месяце!\n" + show())
        bot.polling()


bot.polling(none_stop=True, interval=0)