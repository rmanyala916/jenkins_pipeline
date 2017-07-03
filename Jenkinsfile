stage 'CI'

node {
   checkout scm
   
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/rmanyala916/simple-spring.git'
      // Get the Maven tool.
      // ** NOTE:  This 'Maven' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'Maven'
   }
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      }
   }

   stage('Results') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archive 'target/*.jar'
   }
}

    stage('SonarQube analysis') { 
        node{
            def mvnHome
            mvnHome = tool 'Maven'
            withSonarQubeEnv('Sonar') { 

                if (isUnix()) {
                    sh "'${mvnHome}/bin/mvn' org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar -f pom.xml "+ 
                    " -Dsonar.projectKey=org.sonarqube:java-sonar " +

                    " -Dsonar.projectKey=org.sonarqube:java-sonar " +
                    " -Dsonar.projectName='Java :: Simple Spring Project' " +
                    " -Dsonar.projectVersion=1.0 " +
                    " -Dsonar.language=java " +
                    " -Dsonar.sources=. " +
                    " -Dsonar.tests=. " +
                    " -Dsonar.test.inclusions='**/*Test*/**' " +
                    " -Dsonar.exclusions='**/*Test*/**' "
                } else {
                    bat ('"${mvnHome}\bin\mvn" org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar -f pom.xml ' + '  -Dsonar.projectKey=org.sonarqube:java-sonar -Dsonar.projectName="Java :: Simple Spring Project"')

                }    
            }
        }
    }


   stage name:'Deploy to staging', concurrency:1
   node {
    //sh 'sudo docker run -d -p=3000:80 --network=bundlev2_prodnetwork nginx'
    sh 'sudo docker-compose up -d --build'
   }  
