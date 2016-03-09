# Logstash - Windows install notes
This document explains how to install and run Logstash as a *service* on a Windows server.

I'm using this setup primarily for shipping logs from Windows servers via Logstash/Redis to Debian GNU/Linux boxes.

This setup is tested with the following software versions:

* Logstash 1.5.2
* Windows 2008 R2 / Windows 2012 R2
* Java JRE 8 64bit
* NSSM 2.24 64bit

NSSM is going to be used to run and install Logstash as a Windows service.

## Prerequisites

* [Logstash](http://logstash.net/)
* [Java JRE](http://www.oracle.com/technetwork/Java/Javase/downloads/)
* [NSSM](http://NSSM.cc/)

## Install Java JRE

Using /s will do a silent installation without asking you any questions.  
It should be save. Haven't had any additional browser toolbars installed afterwards ;-)

    jre-windows-x64.exe /s INSTALLDIR=C:\Java\jre

## Install NSSM

Just extract the ZIP file to `C:\NSSM`
 
## Logstash
### Prepare the directory structure

    REM Base install dir
    MD C:\Logstash
    REM Extract Logstash to this directory
    MD C:\Logstash\install
    REM NSSM will save Logstash's stdout/stderr here
    MD C:\Logstash\NSSM
    REM Let's keep Logstash's config outside the install dir for easier updates
    MD C:\Logstash\conf.d

### Install Logstash as a Windows Service

    CD C:\NSSM\win64
    NSSM install logstash C:\Logstash\install\bin\Logstash.bat
    NSSM set logstash AppParameters agent --config C:\Logstash\conf.d
    NSSM set logstash AppDirectory C:\Logstash\install
    NSSM set logstash AppEnvironmentExtra "Java_HOME=C:\Java\jre"
    NSSM set logstash AppStdout C:\Logstash\NSSM\stdout.log
    NSSM set logstash AppStderr C:\Logstash\NSSM\stderr.log
    REM Replace stdout and stderr files
    NSSM set logstash AppStdoutCreationDisposition 2
    NSSM set logstash AppStderrCreationDisposition 2
    REM Disable WM_CLOSE, WM_QUIT in the Shutdown options. Without it, NSSM can't stop Logstash properly
    NSSM set logstash AppStopMethodSkip 6
    REM Let's start Logstash. I assume a correct configuration is already in place
    net start logstash

### Remove Logstash's Windows service

    net stop logstash
    CD C:\NSSM\win64
    NSSM remove logstash

## Troubleshooting
### Have a look at Logstash's stderr/stdout data first

    type C:\Logstash\NSSM\stderr.log
    type C:\Logstash\NSSM\stdout.log

### Is Java (64bit) installed correctly?

    C:\Java\jre\bin\Java -version
    Java version "1.7.0_60"
    Java(TM) SE Runtime Environment (build 1.7.0_60-b19)
    Java HotSpot(TM) 64-Bit Server VM (build 24.60-b09, mixed mode)

### Test reading Security event logs on Windows

    input {
      eventlog {
        type => 'Win32-EventLog'
        logfile => 'Security'
      }
    }
    output {
      stdout {}
    }