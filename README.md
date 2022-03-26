# Bot2
import telebot
from config import TOKEN, keys
from extensions import ConvertionException, Converter
import traceback


bot = telebot.TeleBot(TOKEN)


@bot.message_handler(commands=['start', 'help'])
def help(message: telebot.types.Message):
    text = 'Чтобы начать работу, введите команду боту в следующем формате: \n Имя валюты, цену которой, хотите узнать'\
           'Имя валюты, в которой надо узнать цену первой валюты' \
           'Количество первой валюты' \
           'Увидеть список доступных валют: /values'
    bot.reply_to(message, text)


@bot.message_handler(commands=['values'])
def values(message: telebot.types.Message):
    text = 'Доступные валюты:'
    for i in keys.keys():
        text = '\n'.join((text, i))
        bot.reply_to(message, text)
        
@bot.message_handler(content_types=['text'])
def convert(message: telebot.types.Message):
    values = message.text.split(' ')
    try:
        if len(values) != 3:
            raise ConvertionException('Неверное количество параметров!')
        
        answer = Converter.get_price(*values)
    except ConvertionException as e:
        bot.reply_to(message, f"Ошибка пользователя!:\n{e}" )
    except Exception as e:
        traceback.print_tb(e.__traceback__)
        bot.reply_to(message, f"Не удалось обработать команду!:\n{e}" )
    else:
        bot.reply_to(message,answer)

bot.polling()

