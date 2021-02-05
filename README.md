# Network Automation - It schedule to backup the configuration of routers every 10mins or hour and day.









def BACKUP():

    TNOW = datetime.datetime.now().replace(microsecond=0)
    TNOW = TNOW.strftime("%d-%b-%Y")

    IP_LIST = open('1_Device.txt')
    for IP in IP_LIST:


        print ('\n  ' + 'ROUTER' + " - "+ IP.strip() + ' \n' )
        RTR = {
        'ip':   IP,
        'username': 'cisco',
        'password': 'cisco',
        'device_type': 'cisco_ios',
        }

        try:
            net_connect = ConnectHandler(**RTR)
        except NetMikoTimeoutException:
            print ('Device not reachable.')
            continue
        except AuthenticationException:
            print ('Authentication Failure.')
            continue
        except SSHException:
            print ('Make sure SSH is enabled in device.')
            continue

        print ('Initiating cofig backup at ' + str(TNOW))
        output1 = net_connect.send_config_set("do sh ip int br")
        print(output1)
        output3 = net_connect.send_config_set("do sh ip route")
        print(output3)
        output2 = net_connect.send_config_set("do sh run")
        print(output2)

        IP = IP.strip()
        SAVE_FILE = open("ROUTER-------" + IP + '-------' + TNOW, 'w')
        SAVE_FILE.write(output1 + output3 + output2)
        SAVE_FILE.close
        print('Finished config backup')

schedule.every(10).minutes.do(BACKUP)
while True:
	schedule.run_pending()
	time.sleep(1)
