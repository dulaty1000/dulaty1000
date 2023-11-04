import telebot  # telegram bot jasauga kerek
import requests  # internet izdeuge kerek
import json  
### first API class
class one_weather:  
    @staticmethod
    def take_weather(city, open_weather_token):
        res = requests.get(f'https://api.openweathermap.org/data/2.5/weather?q={city}&appid={open_weather_token}&units=metric')
        data = res.json()
        # The "weather" field is a list, so access it as data["weather"][0]["main"]
        return data["weather"][0]["main"]
#### second API class
class two_weather:  # second API class
    @staticmethod
    def get_weather(city, open_weather_token):
        r = requests.get(f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={open_weather_token}&units=metric")
        data = r.json()
        # The "main" field contains temperature information, so access it as data["main"]["temp"]
        return data["main"]["temp"]
        
#### adapter class

class WeatherAdapter:  
    def __init__(self, city, open_weather_token):
        self.city = city
        self.open_weather_token = open_weather_token

    def get_weather_data(self):
        weather_description = one_weather.take_weather(self.city, self.open_weather_token)    # baylanistirdik
        cur_weather = two_weather.get_weather(self.city, self.open_weather_token)
        return weather_description, cur_weather

bot = telebot.TeleBot("6798373596:AAGvSCshpCAcihzz4wzB3XKKAeABJ2pNDNg") # kerekti adrester
API = 'ef2cd6e84e5e196aa3ac41e2e60008f2'
### bot done
@bot.message_handler(commands=['start'])  # bot en birinshi kosu ushin
def start(message):
    bot.send_message(message.chat.id, 'Salem! Aua rayin bilu ushin kala atin jaziniz;)') # surak message

@bot.message_handler(content_types=['text']) # text jauap kabildau 
def get_weather(message):
    city = message.text.strip().lower()  # name city alu 
    weather_adapter = WeatherAdapter(city, API)  
    weather_description, cur_weather = weather_adapter.get_weather_data()

    response = f"Температура: {cur_weather}°C\nПогода: {weather_description}\nБүгінгі күніңіз жақсы өтсін!)"  # output 
    bot.reply_to(message, response)

bot.polling(none_stop=True)
