from flask import Flask
from flask_cors import CORS
import time
import wiringpi
from gpiozero import LightSensor
from flask import request
import RPi.GPIO as GPIO
GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)
GPIO.setup(21,GPIO.OUT)


prevValue = 0

def servo(start,end,clock):
    print("func called",start,end,clock)
    for pulse in range(start, end, clock):
        wiringpi.pwmWrite(18, pulse)
        time.sleep(delay_period)
    return True

# use 'GPIO naming'
wiringpi.wiringPiSetupGpio()

# set #18 to be a PWM output
wiringpi.pinMode(12, wiringpi.GPIO.PWM_OUTPUT)
ldr = LightSensor(26)

# set the PWM mode to milliseconds stype
wiringpi.pwmSetMode(wiringpi.GPIO.PWM_MODE_MS)

# divide down clock
wiringpi.pwmSetClock(192)
wiringpi.pwmSetRange(2000)


delay_period = 0.01
app = Flask(__name__)
CORS(app)

@app.route("/")
def hello():
    return "Hello World!"

@app.route("/close")
def close():
    global prevValue
    GPIO.output(21,GPIO.HIGH)
    prevValue = 0
    for pulse in range(50, 250, 1):
                wiringpi.pwmWrite(18, pulse)
                time.sleep(delay_period)
    return "close"

@app.route("/open")
def open():
    global prevValue
    GPIO.output(21,GPIO.LOW)

    prevValue = 1000
    for pulse in range(250, 50, -1):
                wiringpi.pwmWrite(18, pulse)
                time.sleep(delay_period)
    return "open"



@app.route('/step', methods=['GET'])
def stepMode():
    global prevValue
    #prevValue = 90
    rotateval = request.args.get('amount')
    direction = request.args.get('direction')
    print(rotateval)
    print(direction)
    if(direction == 'L1'):
        GPIO.output(21,GPIO.HIGH)
        servo(50,200,1)
        servo(250,200,-1)
    if(direction == 'L2'):
        GPIO.output(21,GPIO.HIGH)
        servo(50,150,1)
        servo(250,150,-1)
    if(direction == 'L3'):
        GPIO.output(21,GPIO.LOW)
        servo(50,100,1)
        servo(250,100,-1)
    if(direction == 'L4'):
        GPIO.output(21,GPIO.LOW)
        servo(50,50,1)
        servo(250,50,-1)
    return "open25"



@app.route("/auto")
def autoMode():
    global prevValue
    ldrval = ldr.value*1000
    print(prevValue)
    if(prevValue < ldrval):
        prevValue  = ldrval
        print(ldrval)
        if(ldrval >100 and ldrval <300):
            servo(250,200,-1)
        elif(ldrval >300  and  ldrval <600):
            servo(250,150,-1)
        elif(ldrval >600 and ldrval <800):
            servo(250,100,-1)
        elif(ldrval >800):
            servo(250,50,-1)
            print("still recording")
    else:
        prevValue  = ldrval
        print(ldrval)
        if(ldrval >600):
            servo(50,100,1)
        elif(ldrval >300  and  ldrval <600):
            servo(50,150,1)
        elif(ldrval >100 and ldrval <300):
            servo(50,210,1)
        elif(ldrval <100):
            servo(50,250,1)
            print("still recording")
    return "auto"






if __name__ == "__main__":
    app.run(host='0.0.0.0', port=3000, debug=True, threaded=True)
