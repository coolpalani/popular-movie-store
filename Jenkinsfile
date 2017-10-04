 node('maven') {
             // define commands
             def ocCmd = "oc --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token` --server=https://openshift.default.svc.cluster.local --certificate-authority=/run/secrets/kubernetes.io/serviceaccount/ca.crt"
             def mvnCmd = "mvn "//-s configuration/cicd-settings.xml"

             stage 'Build'
             git branch: 'demotest1', url: 'https://github.com/wzzrd/popular-movie-store.git'
             def v = version()
             sh "${mvnCmd} clean compile -DskipTests=true"

             stage 'Test and Analysis'
             parallel (
                 'Test': {
                     sh "${mvnCmd} test"
                     // step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
                 },
                 'Static Analysis': {
                     //sh "${mvnCmd} jacoco:report sonar:sonar -Dsonar.host.url=http://sonarqube:9000 -DskipTests=true"
                     println("Did static Sonar analysis")
                 }
             )
  
             stage 'Deploy Locally'
             println("Deploying to the local project")
             sh "${mvnCmd} fabric:push"
   
             stage 'Deploy Azure'
             input message: "Promote to Azure?", ok: "Promote"
             // tag for stage
             // sh "${ocCmd} tag dev/tasks:latest stage/tasks:${v}"
             println("Deploying to the Azure project")
             // sh "${mvnCmd} fabric:push --docker.push.registry=<AZURE REGISTRY>"
          }

          def version() {
            def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
            matcher ? matcher[0][1] : null
          }
