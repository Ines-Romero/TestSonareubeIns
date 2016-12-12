# sonarqube-installation
SonarQube Installation steps

    $ sudo apt-get update
    $ sudo apt-get upgrade

## MySQL
Install mysql & jdbc-driver

    $ sudo apt-get install mysql-client mysql-server libmysql-java
    $ mysql -u root -p

Add "sonar" database and "sonar" user with "sonar" password

    CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;
    CREATE USER 'sonar' IDENTIFIED BY 'sonar';
    GRANT ALL ON sonar.* TO 'sonar'@'%' IDENTIFIED BY 'sonar';
    GRANT ALL ON sonar.* TO 'sonar'@'localhost' IDENTIFIED BY 'sonar';
    FLUSH PRIVILEGES;

## Java (JDK & JRE)

    $ sudo apt-get install openjdk-8-jdk openjdk-8-jdk-headless openjdk-8-jre openjdk-8-jre-headless openjdk-8-source    openjdk-8-doc openjdk-8-dbg
    $ sudo apt-get install default-jdk default-jdk-headless default-jre default-jre-headless default-jdk-doc

## SonarQube
### Deploying official docker:

    $ sudo apt-get install docker.io
    $ sudo docker pull sonarqube

    $ docker run -d --name sonarqube \
        -p 9000:9000 -p 9092:9092 -p 3306:3306 \
        -e SONARQUBE_JDBC_USERNAME=sonar \
        -e SONARQUBE_JDBC_PASSWORD=sonar \
        -e SONARQUBE_JDBC_URL=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance \
        sonarqube

### Installation from official repository:
/etc/apt/sources.list

    #------------------------------------------------------------------
    deb http://downloads.sourceforge.net/project/sonar-pkg/deb binary/
    #------------------------------------------------------------------

    $ sudo apt-get update
    $ sudo apt-get install sonar

    sonar.jdbc.username=sonar
    sonar.jdbc.password=sonar

    sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance

    sonar.web.host=127.0.0.1
    sonar.web.context=/sonar
    sonar.web.port=9000

    sonar.web.javaOpts=-server

/etc/init.d/sonar

    #-----------------------------------------------------------
    #!/bin/sh
    #
    # rc file for SonarQube
    #
    # chkconfig: 345 96 10
    # description: SonarQube system (www.sonarsource.org)
    #
    ### BEGIN INIT INFO
    # Provides: sonar
    # Required-Start: $network
    # Required-Stop: $network
    # Default-Start: 3 4 5
    # Default-Stop: 0 1 2 6
    # Short-Description: SonarQube system (www.sonarsource.org)
    # Description: SonarQube system (www.sonarsource.org)
    ### END INIT INFO
 
    /usr/bin/sonar $*
    #-----------------------------------------------------------

    $ sudo ln -s $SONAR_HOME/bin/linux-x86-64/sonar.sh /usr/bin/sonar
    $ sudo chmod 755 /etc/init.d/sonar
    $ sudo update-rc.d sonar defaults

## AthenaPDF service
    $ sudo docker pull arachnysdocker/athenapdf-service
    $ sudo docker run -p 8080:8080 --rm --env-file=sonarqube-athena.env arachnysdocker/athenapdf-service &

## Example of usage:
    http://<docker-address>:8080/convert?auth=arachnys-weaver&url=http://blog.arachnys.com/
    $ curl http://dockerhost:8080/convert\?auth\=arachnys-weaver\&url\=http://blog.arachnys.com/ |> out.pdf
