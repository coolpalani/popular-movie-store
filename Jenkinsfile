 node('maven') {
             // define commands
             def ocCmd = "oc --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token` --server=https://openshift.default.svc.cluster.local --certificate-authority=/run/secrets/kubernetes.io/serviceaccount/ca.crt"
             def mvnCmd = "mvn "//-s configuration/cicd-settings.xml"

             stage 'Build'
             git branch: 'demotest1', url: 'https://github.com/wzzrd/popular-movie-store.git'
             def v = version()
             sh "${mvnCmd} clean install -DskipTests=true"

             stage 'Test and Analysis'
             parallel (
                 'Test': {
                     sh "${mvnCmd} test"
                     step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
                 },
                 'Static Analysis': {
                     //sh "${mvnCmd} jacoco:report sonar:sonar -Dsonar.host.url=http://sonarqube:9000 -DskipTests=true"
                   sleep(3000)
                 }
             )

             stage 'Push to Nexus'
             sleep(3000) //sh "${mvnCmd} deploy -DskipTests=true"

             stage 'Deploy DEV'
             sleep(3000)
   
             stage 'Deploy STAGE'
             input message: "Promote to STAGE?", ok: "Promote"
             // tag for stage
             // sh "${ocCmd} tag dev/tasks:latest stage/tasks:${v}"
             sleep(3000)
          }

          def version() {
            def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
            matcher ? matcher[0][1] : null
          }
