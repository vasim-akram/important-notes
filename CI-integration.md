## jenkins setup -
* add key -> ```wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -```
* add repo -> ```sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list```
* ```sudo apt-get update```
* ```sudo apt-get install jenkins```
* start jenkins -> [start-jenkins](http://localhost:8080)
