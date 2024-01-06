# IOT-Project_減肥好幫手
## 1.關於專案
輸入欲測量食物每100公克的熱量以及限制的攝取熱量，若超過設定的量則LED燈亮起，蜂鳴器發出DO、RE、MI提示告警。
## 2.專案緣由
減肥期間，常常會想吃零食但又擔心一不小心吃過多
## 3.專案所需實體材料
* 樹莓派*1
* 重量感測器*1
* LED燈泡*1
* 電阻*1
* 蜂鳴器*1
* 杜邦線*2
* 杜邦線*2
* 麵包板*1
* 筆電*1
* USB線*1
* 插頭*1
## 4.設計和實體照片
## 5.程式設計
import RPi.GPIO as GPIO
import time
import sys
from hx711 import HX711


GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)


buzzer_pin = 18  
led_pin = 4      


EMULATE_HX711 = False

if not EMULATE_HX711:
    import RPi.GPIO as GPIO
else:
    from emulated_hx711 import HX711

def doReMi():
    GPIO.setup(buzzer_pin, GPIO.OUT)
    p = GPIO.PWM(buzzer_pin, 50)
    p.start(50)

    print("Do")
    p.ChangeFrequency(523)
    time.sleep(1)

    print("Re")
    p.ChangeFrequency(587)
    time.sleep(1)
   
    print("Mi")
    p.ChangeFrequency(659)
    time.sleep(1)

    p.stop()

def led():
    GPIO.setup(led_pin, GPIO.OUT)
    GPIO.output(led_pin, GPIO.HIGH)
    time.sleep(1)
    GPIO.output(led_pin, GPIO.LOW)

def cleanAndExit():
    print("Cleaning...")
    GPIO.cleanup()
    print("Bye!")
    sys.exit()


hx = HX711(5, 6)
hx.set_reading_format("MSB", "MSB")
hx.set_reference_unit(410.4)
hx.reset()
hx.tare()
print("Tare done! Add weight now...")

try:
    calories_per_100g = float(input("Enter the calories per 100 grams of the snack: "))
    calorie_limit = float(input("Enter the calorie limit: "))

    while True:
        val = hx.get_weight(5)
        print("Weight: {} grams".format(val))
        
        calories = (val / 100) * calories_per_100g
        print("Calories: {:.2f}".format(calories))
        
        if calories > calorie_limit:
            led()
            doReMi()

        hx.power_down()
        hx.power_up()
        time.sleep(0.1)

except (KeyboardInterrupt, SystemExit):
    cleanAndExit()
    
finally:
    GPIO.cleanup()

## 6.影片呈現連結
## 7.可以改進的地方或其他發想
* 可以連接到手機，利用手機輸入單位熱量以及限制熱量
* 可以預先輸入常見零食熱量，增加鏡頭功能，利用鏡頭辨識零食種類
## 8.參考資料
* https://tutorials-raspberrypi.com/digital-raspberry-pi-scale-weight-sensor-hx711/
* https://officeguide.cc/raspberry-pi-gpio-led-tutorial-examples/
* https://sites.google.com/view/zsgititit/home/%E7%A1%AC%E9%AB%94%E5%AF%A6%E4%BD%9C/raspberry-shu-mei-pai/raspberry-shi-yong-fengbuzzier
* https://pinout.xyz/

