podTemplate(yaml: """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins/kube-default: true
    app: jenkins
    component: agent
spec:
  containers:
    - name: jnlp
      image: jenkinsci/jnlp-slave:latest
      resources:
        limits:
          cpu: 1
          memory: 2Gi
        requests:
          cpu: 1
          memory: 2Gi
      imagePullPolicy: Always
      env:
      - name: POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
      - name: DOCKER_HOST
        value: tcp://localhost:2375
    - name: docker
      image: docker:latest
      command:
      - cat
      tty: true
      volumeMounts:
      - mountPath: /var/run/docker.sock
        name: docker-sock
    - name: maven
      image: maven:3.6.3-jdk-8
      tty: true
      command:
      - cat
  volumes:
    - name: docker-sock
      hostPath:
         path: /var/run/docker.sock
"""
){ 
	node(POD_LABEL) {
	
			def serverVersion=''
            stage('Checkout'){
                checkout scm
            }
			container('docker'){
			
				stage('Get Versions'){
						serverVersion = sh label: '', returnStdout: true, script: 'cd backend && $(mvn help:evaluate -Dexpression=project.version -q -DforceStdout)'
				}
				stage('Build Docker Image') {
                	    sh 'cd backend && docker build --tag tommyelroy/wirvsirus:server-$serverVersion .'	
				}
			}
    }
}