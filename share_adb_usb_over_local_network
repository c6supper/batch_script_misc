@echo off
	:loop
	taskkill /f /im adb.exe
	set /a ADB_PORT=5555 
	set /a BPort=%ADB_PORT%
	
	adb kill-server

	setlocal ENABLEDELAYEDEXPANSION
	set /a Maxport = 65535 - BPort

	for /l %%X in (0,1,!Maxport!) do ( 
	set /a tempo=BPort+X
	echo Current port=!tempo!   
	netstat -o -n -a | findstr :!tempo! 
	if !ERRORLEVEL! equ 0 (@echo "Port available") ELSE (@echo Port : !tempo! is   Available sir ! >> Availables.txt 
	goto eof) 
	set /a BPort=BPort+1 )

	:eof
	echo Available port found 
	echo Finish processing
	set /a ADB_PORT=!tempo!

	@For /f "tokens=4" %%* in (
		'route.exe print ^|findstr "\<0.0.0.0\>"'
	) Do @Set "LOCAL_IP=%%*"
	@echo %LOCAL_IP%

	start /b adb -a nodaemon server start
	sleep 2
	adb forward --remove-all

	adb tcpip %ADB_PORT%
	IF %ERRORLEVEL% EQU 0 (
		echo "adb listen to tcp %ADB_PORT%"
	) ELSE (
		echo "adb tcpip failed with error code: %ERRORLEVEL%"
		goto loop
	)
	
	sleep 2
	
	adb forward tcp:%ADB_PORT% tcp:%ADB_PORT%
	IF %ERRORLEVEL% EQU 0 (
		echo "#######run 'adb connect %LOCAL_IP%:%ADB_PORT%' remotely#######"
	) ELSE (
		echo "adb forward tcp:%ADB_PORT% tcp:%ADB_PORT% failed with error code: %ERRORLEVEL%"
	)

	adb wait-for-usb-disconnect
	adb forward --remove-all
	
	goto loop
