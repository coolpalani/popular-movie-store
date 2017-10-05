 node('maven') {
             // define commands
             def ocCmd = "oc --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token` --server=https://openshift.default.svc.cluster.local --certificate-authority=/run/secrets/kubernetes.io/serviceaccount/ca.crt"            
             def projectName0 = env.NAMESPACE
             def projectName1 = env.KUBE_NAMESPACE
             def projectName2 = params.NAMESPACE
             def projectName3 = params.KUBE_NAMESPACE

  
             println("Found env:  ${projectName0} ${projectName1} ${projectName2} ${projectName3}")
  
             stage 'Build'
             sh "${ocCmd} start-build -Fw popular-movie-store-s2i -n crossclouddemo"
   
             stage 'Run integration test'
             sleep(1)
  
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
