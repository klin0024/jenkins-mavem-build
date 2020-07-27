def cloud_name='kubernetes'
def pod_label='maven_label'
def maven_image='gcr.io/cloud-builders/mvn'
def buildah_image='buildah/buildah'
def jnlp_image='jenkins/jnlp-slave:3.27-1'

def maven_repo_path='/home/jenkins/agent/maven-repo'
def maven_repo_pvc='maven-repo-pvc'

def docker_config_path='/home/jenkins/agent/CFGNAME'
def docker_config_map='dockerfile'
def dockerfile='Dockerfile'

def git_url='https://github.com/spring-projects/spring-petclinic'
def git_branch='main'
def image_registry='bastion.ks.example.com:5000'
def image_repository='maven/java'
def image_tag='v1'

def docker_runtime='docker'
def from_image='java:8-jdk-alpine'
def from_image_workdir='/usr/app'

podTemplate(
  label: pod_label,
  cloud: cloud_name, 
  containers: [
    containerTemplate(name: 'maven', image: maven_image, ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'buildah', image: buildah_image, ttyEnabled: true, command: 'cat', privileged: true),
    containerTemplate(name: 'jnlp', image: jnlp_image, ttyEnabled: false, args: '${computer.jnlpmac} ${computer.name}')
  ], 
  volumes: [
    persistentVolumeClaim(mountPath: maven_repo_path, claimName: maven_repo_pvc, readOnly: false),
    configMapVolume(mountPath: docker_config_path, configMapName: docker_config_map)
  ]
) {
  try {
    node(pod_label){
      stage('git pull'){
        git  url: git_url, branch: git_branch
      }
      stage('maven build'){
        container('maven'){
          sh "/usr/bin/mvn -Dmaven.repo.local=${maven_repo_path} clean package"
        }
      }
      container('buildah'){
        stage('image build'){
          /*
          sh """cat <<EOF> ${docker_config_path}/${dockerfile}
          FROM ${from_image}
          COPY target/*.jar ${from_image_workdir}/
          WORKDIR ${from_image_workdir}
          CMD [\"sh\", \"-c\", \"java -jar *.jar\"]
          """
          sh "cat ${docker_config_path}/${dockerfile}"
          */
          sh "buildah bud --tls-verify=false --no-cache --format=${docker_runtime} -f ${docker_config_path}/${dockerfile} -t ${image_registry}/${image_repository}:${image_tag} ."
        }
        stage('image push'){  
          sh "buildah push --tls-verify=false ${image_registry}/${image_repository}:${image_tag}"
        }
      }
    }
  } 
  catch (err) {
     echo "Caught: ${err}"
     currentBuild.result = 'FAILURE'
     throw err
  }   

}