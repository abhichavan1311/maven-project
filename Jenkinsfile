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
                agent { label 'master' }
                steps{
                    echo " This is test A"
                    sh "mvn test"
                }
                
            }
            stage('testB')
            {
                agent { label 'master' }
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
        agent { label 'master' }
        steps
        {
            dir("/var/www/html")
            {
                unstash "maven-build"
            }
            sh """
            sudo cd /var/www/html/
            sudo jar -xvf webapp.war
            """
        }
    }
   

    
}

}
