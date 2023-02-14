library identifier: "pipeline-library@v1.5",
retriever: modernSCM(
  [
    $class: "GitSCMSource",
    remote: "https://github.com/redhat-cop/pipeline-library.git" 
  ]
)

pipeline
{
  agent  any
  environment {
        COMMIT_ID = sh (script: 'git rev-parse HEAD', returnStdout: true).trim().substring(0,5)
    }
stages{
  stage("build")
  {
    steps{
      script{
        openshift.withCluster() {
          openshift.withProject() {
             echo "Using project: ${openshift.project()}"
             openshift.selector("bc" , "front-git").startBuild("--wait")
          }
        }
      }
    }
  }
  stage("Tag image") {
       steps{
    tagImage([
            sourceImagePath: "dhanya-jenkins",
            sourceImageName: "node-gitclient",
            sourceImageTag : "latest",
            toImagePath: "dhanya-jenkins",
            toImageName    : "node-gitclient",
            toImageTag     : "${COMMIT_ID}"
      ])
      }
  }
//   stage("Trigger Deployment Update Pipeline "){
//         steps{
//           build job:'node-app-update-deployment-pipeline-front' , parameters: [string(name: 'DOCKERTAG',value: env.BUILD_NUMBER)]
//         }
//       }
  stage("build-back")
  {
    steps{
      script{
        openshift.withCluster() {
          openshift.withProject() {
             echo "Using project: ${openshift.project()}"
             openshift.selector("bc" , "back-git").startBuild("--wait")
          }
        }
      }
    }
  }
  
  stage("Tag image back") {
       steps{
    tagImage([
            sourceImagePath: "dhanya-jenkins",
            sourceImageName: "node-gitserver",
            sourceImageTag : "latest",
            toImagePath: "dhanya-jenkins",
            toImageName    : "node-gitserver",
            toImageTag     : "${COMMIT_ID}"
      ])
       }
  }
  
  stage("Trigger Deployment Update Pipeline back"){
        steps{
          build job:'node-app-update' , parameters: [string(name: 'DOCKERTAG',value: env.COMMIT_ID)]
       }
      }
//   stage('deploy') {
//         steps {
//             script {
//                 openshift.withCluster() {
//                     openshift.withProject("$PROJECT_NAME") {
//                         echo "Using project: ${openshift.project()}"
//                          sh 'oc project "$PROJECT_NAME" '
//                          sh 'oc apply -f https://github.com/Dhanya134/node-2-update/blob/main/dev/front.yaml'
//                     }
//                 }
//             }
//         } 
//     }
//   stage("Trigger Deployment deploy pipeline"){
//         steps{
//           build job:'node-app-update-deployment' 
//         }
//       }
 }
 }
