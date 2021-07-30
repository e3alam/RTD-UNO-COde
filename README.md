# RTD-UNO-COde
Code used for RTD Experiment

import pyfirmata as pf
import time
import numpy as np
import matplotlib.pyplot as plt

port='/COM3'
board = pf.Arduino(port)

board.analog[0].mode=pf.INPUT

it = pf.util.Iterator(board)
it.start()

board.analog[0].enable_reporting()

def writeFile(name, listName1, listName2):
    with open(name, 'w') as f:
        
        for x,y in zip(listName1, listName2):
            stringIn="{} {}\n".format(x,y)
            f.write(stringIn)
         
timeList=[]
voltageList=[]

for x in range(140):
    
    pin0=board.analog[0].read()
    if pin0==None:
        pass
    else:
        timeList.append(x)
        voltageList.append(pin0*5)
        
    time.sleep(30)

plt.plot(timeList,voltageList)
plt.xlabel("Time (s)")
plt.ylabel("Voltage (v)")
plt.show()

V = np.array(voltageList)

Rk = 1000
I = (5-V)/Rk

Ru = V/I

T0 = 0
alpha = 0.00392
T = ((Ru/1000) - 1)/alpha + T0

plt.plot(timeList, T, 'b-o')
plt.xlabel("Time (s)")
plt.ylabel("Temperature (C)")

temperatureList = T
writeFile("testfilenew.txt", timeList, voltageList)
