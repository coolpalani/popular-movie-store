 node('maven') {
             // define commands
             def ocCmd = "oc --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token` --server=https://openshift.default.svc.cluster.local --certificate-authority=/run/secrets/kubernetes.io/serviceaccount/ca.crt"
             
             stage 'Build'
             sh "${ocCmd} start-build popular-movie-store-s2i -n buildpipeline --env=MAVEN_ARGS=compile"
   
             stage 'Run integration test'
             sh "${ocCmd} start-build popular-movie-store-s2i -n buildpipeline --env=MAVEN_ARGS=test"  
             
             stage 'Deploy Locally'
             sh "${ocCmd} start-build popular-movie-store-s2i -n buildpipeline --env=MAVEN_ARGS=fabric8:deploy"  
             
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
