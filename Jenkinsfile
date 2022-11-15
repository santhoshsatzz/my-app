node{
   stage('SCM Checkout 1'){
     git 'https://github.com/santhoshsatzz/my-app.git'
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
   sh 'docker build -t santhoshsatzz/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u santhoshsatzz -p ${dockerPassword}"
    }
   sh 'docker push santhoshsatzz/myweb:0.0.2'
   }
  stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.126.161.102:8083"
   sh "docker tag santhoshsatzz/myweb:0.0.2 13.126.161.102:8083/damo:1.0.0"
   sh 'docker push 13.126.161.102:8083/damo:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest santhoshsatzz/myweb:0.0.2' 
   }
}
}
