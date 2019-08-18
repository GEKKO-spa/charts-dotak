podTemplate(containers: [
  containerTemplate(name: 'helm', image: 'alpine/helm:latest', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'curl', image: 'appropriate/curl:latest', command: 'cat', ttyEnabled: true),
]) {
  node(POD_LABEL){
      withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'AWS_Dotaki_Preprod_Cred']]){
        stage('Helm build'){
            sh '''
            echo '######################## Helm Build #################################'
            git clone https://github.com/loick-gekko/charts-dotak.git
            cd charts-dotak
            git checkout $BRANCH_NAME
            '''
            container('helm'){
                sh '''
                echo '######################## Helm Build Start #################################'
                cd charts-dotak
                cd $BRANCH_NAME
                helm init --client-only
                helm package . 
                echo '######################## Helm Container End ###############################'
                echo '######################## Helm Build End ##################################'
                '''
            }
        }
        stage('Helm publish'){
          sh '''
          echo '######################## Helm Publish Start #################################'
          '''
          container('curl'){
            sh '''
            echo '######################## Curl Container Start #################################'
            cd charts-dotak
            ls -al
            cd $BRANCH_NAME
            ls -al
            CHART=$(ls | grep .tgz)
            curl -L --data-binary "@"$CHART chartmuseum-chartmuseum.chartmuseum.svc.cluster.local:8080/api/charts
            curl chartmuseum-chartmuseum.chartmuseum.svc.cluster.local:8080/api/charts
            echo '######################## Curl Container End #################################'
            echo '######################## Helm Publish End #################################'
            '''
          }
        }
      }
  }
}

