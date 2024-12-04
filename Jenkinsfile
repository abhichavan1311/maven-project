pipeline
{

agent {
  label 'master'
}

parameters {
    choice choices: ['blue', 'green'], name: 'select_environment'
}

environment{
    NAME = "abhishek"
}
tools {
  maven 'maven1'
}

stages{

    stage('build')
    {
        steps {
            
            sh 'mvn clean package -DskipTests=true'
           
        }

        

    }

    stage('test')
    { 
        parallel {
            stage('testA')
            {
                agent { label 'slave1' }
                steps{
                    echo " This is test A"
                    sh "mvn test"
                }
                
            }
            stage('testB')
            {
                agent { label 'slave1' }
                steps{
                echo "this is test B"
                sh "mvn test"
                }
            }
        }
        post {
        success {
             dir("webapp/target/")
            {
            stash name: "maven-build", includes: "*.war"
                 }
                 }
            }

    }

    stage('deploy_green')
    {
        when { expression {params.select_environment == 'green'}
        beforeAgent true}
        agent { label 'slave1' }
        steps
        {
            dir("/var/www/html")
            {
                unstash "maven-build"
            }
            sh """
            cd /var/www/html/
            jar -xvf webapp.war
            """
        }
    }
   

    
}

}
