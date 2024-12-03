pipeline
{

agent {
  label 'Built-In Node'
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
            script{
                file = load "script.groovy"
                file.hello()
            }
            sh 'mvn clean package -DskipTests=true'
           
        }

        

    }

    stage('test')
    { 
        parallel {
            stage('testA')
            {
                agent { label 'Built-In Node' }
                steps{
                    echo " This is test A"
                    sh "mvn test"
                }
                
            }
            stage('testB')
            {
                agent { label 'Built-In Node' }
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

    stage('deploy_dev')
    {
        when { expression {params.select_environment == 'green'}
        beforeAgent true}
        agent { label 'Built-In Node' }
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

    stage('deploy_prod')
    {
      when { expression {params.select_environment == 'prod'}
        beforeAgent true}
        agent { label 'Built-In Node' }
        steps
        {
             timeout(time:5, unit:'DAYS'){
                input message: 'Deployment approved?'
             }
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
