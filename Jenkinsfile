node{
   stage('SCM Checkout'){
     git 'https://github.com/Balachandar36/my-app.git'
   }
    stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
    stage('SonarQube Analysis') {
      def mvnHome =  tool name: 'maven3', type: 'maven'
     withSonarQubeEnv('sonar') { 
    sh "${mvnHome}/bin/mvn sonar:sonar"
	   }
 }
   stage('Build Docker Imager'){
   sh 'docker build -t bala36tsr/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u bala36tsr -p ${dockerPassword}"
    }
   sh 'docker push bala36tsr/myweb:0.0.2'
   }
    stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	 }
    }
     stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 3.8.196.194:8088"
   sh "docker tag bala36tsr/myweb:0.0.2 3.8.196.194:8088/bala:1.0.0"
   sh 'docker push 3.8.196.194:8088/bala:1.0.0'
   }
    stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest bala36tsr/myweb:0.0.2' 
   }
}  
