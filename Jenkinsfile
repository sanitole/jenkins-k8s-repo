template = '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: docker
  name: docker
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: docker
    name: docker
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker
  volumes:
  - name: docker
    hostPath:
      path: /var/run/docker.sock
    '''

podTemplate(cloud: 'kubernetes', label: 'docker', yaml: template) {
    node ("docker") {
        container ("docker") {
    stage ("Checkout SCM"){
       git branch: 'main', url: 'https://github.com/sanitole/jenkins-k8s-repo.git'
    }
withCredentials([usernamePassword(credentialsId: 'docker-creds', passwordVariable: 'DOCKER_PSWD', usernameVariable: 'DOCKER_USER')]) {

    stage ("Docker build") {
        sh "docker build -t ${DOCKER_USER}/apache:3.0 ."
    }
    stage ("Docker Push") {
        sh """
        docker login -u ${DOCKER_USER} -p ${DOCKER_PSWD}
        docker push ${DOCKER_USER}/apache:3.0 
        """
    }
        }
    }
}
}
