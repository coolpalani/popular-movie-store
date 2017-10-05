 node('maven') {
             // define commands
             def ocCmd = "oc --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token` --server=https://openshift.default.svc.cluster.local --certificate-authority=/run/secrets/kubernetes.io/serviceaccount/ca.crt"
             def mvnCmd = "mvn "
             def v = version()
             
             stage 'Build'
             git branch: 'demotest1', url: 'https://github.com/wzzrd/popular-movie-store.git'
             sh "${mvnCmd} clean compile -DskipTests=true"   
             
             stage 'Run integration test'
             sh "${mvnCmd} test"   
             
             stage 'Deploy Locally'
             sh "${ocCmd} start-build --from-file=./target/*.*ar -n buildpipeline"  
             
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
