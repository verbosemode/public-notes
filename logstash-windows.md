# Logstash - Windows install notes

This document explains how to install and run Logstash as a *service* on a Windows server.

I'm using this setup primarily for shipping logs from Windows servers via Logstash/Redis to Debian GNU/Linux boxes.

This setup is tested with the following software versions:

* Logstash 1.4.2
* Windows 2008 R2 / Windows 2012 R2
* Java JRE 7 64bit
* NSSM 2.23 64bit

NSSM is going to be used to run and install Logstash as a Windows service.


## Prerequisites

* [Logstash](http://logstash.net/)
* [Java JRE](https://www.java.com/en/download/manual.jsp)
* [NSSM](http://nssm.cc/)

## Install Java JRE

Using /s will do a silent installation without asking you any questions.
It should be save. Haven't had any additional browser toolbars installed afterwards ;-)

	jre-7u60-windows-x64.exe /s INSTALLDIR=c:\java\jre
        

## Install NSSM
	
Just extract the ZIP file to c:\nssm

## Logstash

### Prepare the directory structure

        REM Base install dir
	md c:\logstash
	REM Extract Logstash to this directory
	md c:\logstash\install
	REM NSSM will save Logstash's stdout/stderr here
	md c:\logstash\nssm
	REM Let's keep Logstash's config outside the install dir for easier updates
	md c:\logstash\conf.d

### Install Logstash as a Windows Service

	cd c:\nssm\win64
	nssm install logstash C:\logstash\install\bin\logstash.bat
	nssm set logstash AppParameters agent --config c:\logstash\conf.d
	nssm set logstash AppDirectory C:\logstash\install
	nssm set logstash AppEnvironmentExtra "JAVA_HOME=C:\java\jre"
	nssm set logstash AppStdout c:\logstash\nssm\stdout.log
	nssm set logstash AppStderr c:\logstash\nssm\stderr.log
	REM Replace stdout and stderr files
	nssm set logstash AppStdoutCreationDisposition 2
	nssm set logstash AppStderrCreationDisposition 2
	REM Disable WM_CLOSE, WM_QUIT in the Shutdown options
	nssm set logstash AppStopMethodSkip 6
	REM Let's start Logstash. I assume a correct configuration is already in place
	net start logstash

### Remove Logstash's Windows service

	net stop logstash
	cd c:\nssm\win64
	nssm remove logstash

## Troubleshooting

### Have a look at Logstash's stderr/stdout data first

	type c:\logstash\nssm\stderr.log
	type c:\logstash\nssm\stdout.log

### Is Java (64bit) installed correctly?

	C:\> c:\java\jre\bin\java -version
	java version "1.7.0_60"
	Java(TM) SE Runtime Environment (build 1.7.0_60-b19)
	Java HotSpot(TM) 64-Bit Server VM (build 24.60-b09, mixed mode)

