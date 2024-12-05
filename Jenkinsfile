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

options {
        // Keep only the 2 most recent builds, and discard older ones
        buildDiscarder(logRotator(numToKeepStr: '2', daysToKeepStr: '7', artifactNumToKeepStr: '2'))
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
             junit '**/target/surefire-reports/*.xml'

                 }
            }

    }

    stage('deploy_green')
    {
        when { expression {params.select_environment == 'green'}
        beforeAgent true}

  /*
Pre-Agent Evaluation:

When beforeAgent true is specified, the when condition is evaluated before the agent for the stage is allocated.
This saves system resources because the agent is not unnecessarily allocated if the condition is not met. For example:
If the when condition evaluates to false, the Jenkins agent is not acquired, and the stage is skipped entirely.
This is particularly useful when you have limited agents (e.g., master, slave1) and want to avoid tying up resources for stages that won't run.
Default Behavior Without beforeAgent true:

By default, Jenkins evaluates the when condition after the agent is allocated. If the condition evaluates to false, the agent is released, but resources are briefly tied up unnecessarily.
Without beforeAgent true, this can cause performance issues, especially in environments with high job loads or limited agents.*/
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
