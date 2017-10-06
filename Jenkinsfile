 node('maven') {
             // define commands
             def ocCmd = "oc --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token` --server=https://openshift.default.svc.cluster.local --certificate-authority=/run/secrets/kubernetes.io/serviceaccount/ca.crt"            
             def theKey = """-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEArBTFuFd9Z899L9/zCKlhRS+1ha8894bZnplR58QzTQ/vWotQ
brx9izfKzkQjE4RMOAGmC1tZfzghl/qbNYvmVGd+Oz6sD6avTRRU+BBFyKFqqosH
pNLDpcIG4B9gb862Klt0BhOuG536M2VdNtdbLldakMhm/bx2fLS2niLYdHOzWwCD
BiQQqBkMye3+QrSgDThC+0rIt1zuJWFH7aWAwdtPPuf3yVcnVbpm8lWBC9taxXTF
7dPyzs5MAEHnYnvNd7sSjYbWptK7YSrxY5emEwpg1U/w7278hoYAtzBJIpKMQBzW
EgoWLsBCUPBOmnmYZMx0khJKQUfRui5vABnNuwIDAQABAoIBADkZQ2ohjbkOfimO
k+QB4AbHB+Jt/TrdAgTANw9CaQtfCDrxEyVLYhT51E+RCFqHgYrr8R6/lvjBtVPq
kem4nBrRpG8gIpOm6XAYGQGe1Kj39p7UNxsX3Fd6BDIF90fiPUvcwKjritAbIVRA
hCeYWecMkT6NYK89A8vpYtciFNS+rAZTUSY76O2gK5UnTPApcn8LsmwxxUsCaPzV
T5902aMBC8mBkm4znVrlgzIC6Lp7TYiAwlENr4c5RxDcGsfPklaCTkcozf/oReHJ
W8QdpeWGgiS4e2iY6PcQEG/W2qnUYl+w2RLNxn9FdgBy5sJLcvwfbjh9xfIVXmCu
VFo01GECgYEA3GL4unpONH73MMEaYwcBfehO6bWPiepgsx/0o5OaRJW8O4byd6we
0WnN+sI/pHHqn4YHeVoc/vi1n8OH7jx6+kwFon7L7n0W1KTZB5TO0zl1MkOVOcBf
DE6ULQ9xUjx34xJaLMn4j1zCPrVnjN9ZCD7Q1az7bGbZx7v3FGQYQpkCgYEAx+N7
1zhDZi8xnsGF9JFNoAHyEQPyFi1bTbNwT0CuSrN4lYlrP16IOkAt5iF2hugDhRLy
pVea+YX9ASkrspwlSy0pMnKd7vWCSk4L05B4Cscqh2l19sPWKkec+mMfNWzWWw6Y
uCpwfXX1AU0exLsH04OvczJ08g5gkO0jWjnz23MCgYAVk1rmZrLhUMzCaSU8LKNo
wCV5m9zBk8CDyM0adojTzhTvfvad4KvjtIgF1J6GauCdXXG936SXEaI2Ud4s5J7l
givLDL7KSlTB3KGirF7TLvzaPuONE9g6TDcVcj2V33WdNV6+o8dBwcMCgJ/ymqsM
s5meUaELq8YGV9CInoQBgQKBgBb7Wa66Ak3KWUsmdu9SkXk3IAzCxl+0YU4CtoP2
LNPb5J5UyWbkyIbiACT4bfc+z8DKF+nYs7HCrP8oK6UCfOsyywzIzs29nnhKluOB
NbwPgB1Hi8+OXp5H042aPelXlSGtAPQTtRFHsZ6vrjzyQAkal96Hy5hU/NdM3Iaw
KRBvAoGBAI0CHxm8cnmZcckcSRLM6+imD4vH4R4RavhoUKtxZTssYp7rhpkDARF2
yRL7z3WfjZPRxfoiOxeVu2LCjio/PHid+gGMYmlQfakYbgAvD3hJ0LySEv8sE0SJ
LbgkC2XqMH4HJvKUsUYv9jInee8zXjAw48w2o+j7ZQJhhiWHBwbB
-----END RSA PRIVATE KEY-----"""
    
             stage 'Build'
             sh "${ocCmd} start-build -Fw popular-movie-store-s2i" // -n crossclouddemo"
   
             stage 'Run integration test'
             sleep(5)
  
             stage 'Deploy Azure'
             input message: "Promote to Azure?", ok: "Promote"
             // tag for stage
             sh "${ocCmd} tag dev/tasks:latest stage/tasks:azure"
             // write key
             sh "mkdir -p ~/.ssh"
             sh "chmod 700 ~/.ssh"
             sh "echo ${theKey} >> ~/.ssh/id_rsa
             sh "chmod 600 ~/.ssh/id_rsa"
             // get token
             sh "token=$(ssh -q -i StrictHostKeyChecking=no root@ocp.redhat.lab 'cat ~/token')
             sh "echo ${token}"
             // docker login remote
             sh "ssh -q -i StrictHostKeyChecking=no root@ocp.redhat.lab \"docker login -u pusher -p ${token} docker-registry-default.prod.nontoonyt.com:443\"" 
             // docker tag
             sh "ssh -q -i StrictHostKeyChecking=no root@ocp.redhat.lab \"docker tag docker-registry.default.svc:5000/crossclouddemo/popular-movie-store:azure docker-registry-default.prod.nontoonyt.com:443/crossclouddemo/popular-movie-store:azure\"" 
             // docker push remote
             sh "ssh -q -i StrictHostKeyChecking=no root@ocp.redhat.lab \"docker push docker-registry-default.prod.nontoonyt.com:443/crossclouddemo/popular-movie-store:azure\""
          }

          def version() {
            def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
            matcher ? matcher[0][1] : null
          }
