import time
from threading import Timer

count = 10

def loop():
    global count
    count -= 1
    if count != 0:
        Timer(1, loop, ()).start()
    print "Number: ", count
    time.sleep(2)
    print "After Number: ", count 

print "Start"
loop()
print "Stop?"
