node {

    withMaven(maven:'maven') {

        stage('Checkout') {
            git url: 'https://github.com/piomin/sample-spring-microservices.git', credentialsId: 'github-piomin', branch: 'master'
        }

        stage('Build') {
           
            
            jdk = tool name: 'JAVA_HOME'
  env.JAVA_HOME = "${jdk}"

  echo "jdk installation path is: ${jdk}"

    def mvnHome = tool 'maven'
  // next 2 are equivalents
  sh "${jdk}/bin/java -version"

  // note that simple quote strings are not evaluated by Groovy
  // substitution is done by shell script using environment
  sh '$JAVA_HOME/bin/java -version'
            
            sh 'mvn clean install'
             
            def pom = readMavenPom file:'pom.xml'
            print pom.version
            env.version = pom.version
        }

        stage('Image') {
            dir ('gateway-service') {
                def app = docker.build "localhost:5000/gateway-service:${env.version}"
                app.push()
            }
        }

        stage ('Run') {
            docker.image("localhost:5000/gateway-service:${env.version}").run('-p 3333:3333 -h gateway --name gateway --link discovery --link account --link customer')
        }
     

    }

}
