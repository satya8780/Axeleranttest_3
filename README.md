# Axeleranttest_3

#5 Write a declarative pipeline YAML file that demonstrates application deployment from a private Git repository.


pipeline{
agent any

environment{
    PATH = "maven path"
}
stages{
   stage("Clone code"){
       steps{
           git credentialsID:'github',url:'https://github.com'
    }
}
   stage("Maven Build"){
       steps{
           sh"mvn clean package"                      # ssh jenkins and tomcat 
           sh "mv target/*.war target /myweb.war"     # configure tomcat setup in jenkins with credentials
      }                                               # rename the .war file
}

     stage("deploy-dev"){
         steps{
        sshagent(['tomcat-new']){
            ssh"""
                scp -o strictHostKeyChecking=no target/myweb.war ec2-user@ipaddress of tomcat server:/opt/tomcat8/webapps/       #ssh jenkins to tomcat and copy war file under webapps
                
                ssh ec2-user@tomcat ip address /opt/tomcat/bin/shutdown.sh
             
                ssh ec2-user@tomcat ip address /opt/tomcat/bin/startup.sh
              
               """
              }
            }
         }
       }
    }
