def default_test_list = [
//  ["title",       "role", "action",   "scenario", "args"],
    ["install",     "use",  "create",   "deploy",   "--report"],
    ["deploy",      "use",  "test",     "deploy",   "--destroy never --report"],
    // ["undeploy",    "use",  "test",     "undeploy", "--destroy never --report"],
    ["uninstall",   "use",  "cleanup",  "deploy",   "--report"]
]

def scenarios = [
    "ubuntu2404" = default_test_list,
    // "ubuntu2204": [
    //     ["setup", "install"],
    //     ["use", "deploy"],
    //     ["use", "undeploy"],
    //     ["setup", "uninstall"]
    // ],
    // "ubuntu2004": [
    //     ["setup", "install"],
    //     ["use", "deploy"],
    //     ["use", "undeploy"],
    //     ["setup", "uninstall"]
    // ],
    //  "ubuntu1804": [
    //      ["setup", "install"],
    //      ["use", "deploy"],
    //      ["use", "undeploy"],
    //      ["setup", "uninstall"]
    //  ],
    // "centos10": [
    //    ["setup", "install"],
    //    ["use", "deploy"],
    //    ["use", "undeploy"],
    //    ["setup", "uninstall"]
    // ],
    // "centos9": [
    //    ["setup", "install"],
    //    ["use", "deploy"],
    //    ["use", "undeploy"],
    //    ["setup", "uninstall"]
    // ],
    // "centos8": [
    //    ["setup", "install"],
    //    ["use", "deploy"],
    //    ["use", "undeploy"],
    //    ["setup", "uninstall"]
    // ],
    //  "centos7": [
    //     ["setup", "install"],
    //     ["use", "deploy"],
    //     ["use", "undeploy"],
    //     ["setup", "uninstall"]
    //  ],
    // "debian12": [
    //    ["setup", "install"],
    //    ["use", "deploy"],
    //    ["use", "undeploy"],
    //    ["setup", "uninstall"]
    // ],
    // "debian11": [
    //    ["setup", "install"],
    //    ["use", "deploy"],
    //    ["use", "undeploy"],
    //    ["setup", "uninstall"]
    // ],
    //  "debian10": [
    //     ["setup", "install"],
    //     ["use", "deploy"],
    //     ["use", "undeploy"],
    //     ["setup", "uninstall"]
    //  ]
]

parallel_stages = [:]

for (kv in mapToList(scenarios)) {
    def platform = kv[0]
    def testList = kv[1]

    parallel_stages[platform] = {
        docker
            .image("${MOLECULE_DOCKER_IMAGE}")
            .inside("--name ${JOB_NAME}_${platform} -e OS_AUTH_URL=${OS_AUTH_URL} -e OS_USERNAME=${OS_APPLICATION_CREDENTIAL_ID} -e OS_PASSWORD=${OS_APPLICATION_CREDENTIAL_SECRET} -u root") {

            stage("Install dependencies") {
                sh "ansible-galaxy install -f -r roles/requirements.yml"
            }

            try {
                for(int i = 0; i < testList.size()-1; i++) {
                    def title = testList[i][0]
                    def role = testList[i][1]
                    def action = testList[i][2]
                    def scenario = testList[i][3]
                    def args = testList[i][4]

                    stage("${platform} - ${title}") {
                        sh "cd ./roles/${role} && molecule ${action} -s ${scenario}-${platform} ${args}"
                    }
                }
            } finally {
                def title = "destroy"
                def role = testList[0][1]
                def action = "destroy"
                def scenario = testList[0][3]
                def args = "--report"

                stage("${platform} - ${title}") {
                    sh "cd ./roles/${role} && molecule ${action} -s ${scenario}-${platform} ${args}"
                }
            }
        }
    }
}

node {
    checkout scm

    withCredentials([usernamePassword(
        credentialsId: 'openstack-credentials',
        usernameVariable: 'OS_APPLICATION_CREDENTIAL_ID',
        passwordVariable: 'OS_APPLICATION_CREDENTIAL_SECRET'
    )]) {

        parallel(parallel_stages)

    }
}

@NonCPS
List<List<?>> mapToList(Map map) {
  return map.collect { it ->
    [it.key, it.value]
  }
}