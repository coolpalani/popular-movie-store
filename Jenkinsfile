 node('maven') {
             // define commands
             def ocCmd = "oc --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token` --server=https://openshift.default.svc.cluster.local --certificate-authority=/run/secrets/kubernetes.io/serviceaccount/ca.crt"            
             
             stage 'Build'
             sh "${ocCmd} start-build -Fw popular-movie-store-s2i" // -n crossclouddemo"
   
             stage 'Run integration test'
             sleep(5)
  
             stage 'Deploy Azure'
             input message: "Promote to Azure?", ok: "Promote"
             // tag for stage
             // sh "${ocCmd} tag dev/tasks:latest stage/tasks:${v}"
             println("Deploying to the Azure project")
             // sh "${mvnCmd} fabric:push --docker.push.registry=<AZURE REGISTRY>"
             // docker tag
             // sh "docker tag docker-registry.default.svc:5000/crossclouddemo/popular-movie-store docker-registry-default.prod.nontoonyt.com/crossclouddemo/popular-movie-store:azure" 
             // docker login remote
             // sh "docker login -u pusher -p <PASSWORD> docker-registry-default.prod.nontoonyt.com " 
             // docker push remote
             // sh "docker push docker-registry-default.prod.nontoonyt.com/crossclouddemo/popular-movie-store:azure"
          }

          def version() {
            def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
            matcher ? matcher[0][1] : null
          }
